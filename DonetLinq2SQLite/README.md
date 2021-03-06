# 创建 .NetFramework 项目

## 添加 Nuget 包：
>-	EntityFramework (强大的 ORM 框架)
>-	EntityFramework.zh-Hans (EntityFramework 中文支持)
>-	System.Data.SQLite (SQLite 组件)
>-	SQLite.CodeFirst (EF 框架暂不支持 SQLite 数据库的 CodeFirst 模式，所以需要此额外的包)
>-	System.Data.SQLite.Core (由 System.Data.SQLite 依赖安装)
>-	System.Data.SQLite.EF6 (由 System.Data.SQLite 依赖安装)
>-	System.Data.SQLite.Linq (由 System.Data.SQLite 依赖安装)

## 配置 App.config
```Xml
  <entityFramework>
    <providers>
      <provider invariantName="System.Data.SQLite" 
		type="System.Data.SQLite.EF6.SQLiteProviderServices, System.Data.SQLite.EF6" />
    </providers>
  </entityFramework>
```

## 增加配置
  ```Xml
  <connectionStrings>
    <!-- name 属性表示此数据库连接字符串配置的名称，会在创建DbContext()时用到-->
	<!-- connectionString 字段表示此数据库连接配置信息，包括服务器和数据库路径/名称、用户名、密码等-->
    <add name="DotNetSQLiteDB" connectionString="DATA SOURCE=SQLiteDB.db" 
	 providerName="System.Data.SQLite" />
  </connectionStrings>
```

## 创建实体类
>- /Models/Bus.cs
>- ——————————————————
```C#
using System.Collections.Generic;
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace DonetLinq2SQLite.Models
{
    public class Bus
    {
        /* 使用 [Key] 声明此字段为主键 */
        [Key]
        [Required]
        [DisplayName("车辆ID")]
        public int ID { get; set; }

        /* 使用 DataType 声明数据类型为 Text */
        [Required]
        [DisplayName("车辆名称"),DataType(DataType.Text)]
        public string Name { get; set; }

        /* 使用 virtual 关键字修饰，否则集合将为空 */
        [Required]
        [DisplayName("乘客")]
        public virtual List<Person> Persons { get; set; }
    }
}
```
>- ——————————————————
>- /Models/Person.cs
>- ——————————————————
```C#
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace DonetLinq2SQLite.Models
{
    public class Person
    {
        /* 使用 [Key] 声明此字段为主键 */
        [Key]
        [Required]
        [DisplayName("乘客ID")]
        public int ID { get; set; }

        /* 使用 DataType 声明数据类型为 Text */
        [Required]
        [DisplayName("乘客姓名"),DataType(DataType.Text)]
        public string Name { get; set; }

        /* 使用 DataType 声明数据类型为 PhoneNumber */
        [Required]
        [DisplayName("手机号码"), DataType(DataType.PhoneNumber)]
        public string Telephone { get; set; }

        /* 使用 DataType 声明数据类型为 Password */
        [Required]
        [DisplayName("密码"), DataType(DataType.Password)]
        public string Password { get; set; }
    }
}
```

## 创建数据库交互类
>- /Models/SQLiteDBContext.cs
```C#
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;

namespace DonetLinq2SQLite.Models
{
	//需继承自 DbContext
    public class SQLiteDBContext : DbContext
    {
        /// <summary>
        /// 车辆表
        /// </summary>
        public DbSet<Bus> BusSet { get; set; }

        /// <summary>
        /// 乘客表
        /// </summary>
        public DbSet<Person> PersonSet { get; set; }

        /// <summary>
        /// 使用 App.config 记录的配置连接数据库
        /// </summary>
        public SQLiteDBContext() : base("DotNetSQLiteDB") {}

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            //必须的代码
            modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
            modelBuilder.Configurations.AddFromAssembly(typeof(SQLiteDBContext).Assembly);

            //初始化数据种子，用于 CodeFirst 模式自动创建或修改数据库
            Database.SetInitializer(new SampleData(modelBuilder));
        }
    }
}
```
>- ——————————————————

>-  创建数据库初始化种子
>- ——————————————————
```C#
using System.Collections.Generic;
using System.Data.Entity;
using SQLite.CodeFirst;

namespace DonetLinq2SQLite.Models
{
    /* 需要继承 SQLite.CodeFirst 内的 SqliteInitializerBase:IDatabaseInitializer
     * SqliteDropCreateDatabaseAlways : 总是使用初始化数据种子的数据库
     * SqliteDropCreateDatabaseWhenModelChanges : 仅当数据库模型变化时自动更新数据库
     */

    class SampleData : SqliteDropCreateDatabaseAlways<SQLiteDBContext>
    {
        public SampleData(DbModelBuilder modelBuilder)
            : base(modelBuilder) {}

        //覆写此方法，用于初始化数据种子
        protected override void Seed(SQLiteDBContext context)
        {
            //添加数据
            context.BusSet.Add(
                new Bus()
                {
                    Name = "Bus_0",
                    Persons = new List<Person>()
                    {
                        new Person(){
                            Name="Person_0",
                            Password="password_0",
                            Telephone="+86-12345678900"
                        },
                        new Person(){
                            Name="Person_1",
                            Password="password_1",
                            Telephone="+86-12345678901"
                        },
                        new Person(){
                            Name="Person_2",
                            Password="password_2",
                            Telephone="+86-12345678902"
                        },
                    }
                }
            );
            context.BusSet.Add(
                new Bus()
                {
                    Name = "Bus_1",
                    Persons = new List<Person>()
                    {
                        new Person(){
                            Name="Person_3",
                            Password="password_3",
                            Telephone="+86-12345678903"
                        },
                        new Person(){
                            Name="Person_4",
                            Password="password_4",
                            Telephone="+86-12345678904"
                        },
                    }
                }
            );
            context.BusSet.Add(
                new Bus()
                {
                    Name = "Bus_2",
                    Persons = new List<Person>()
                    {
                        new Person(){
                            Name="Person_5",
                            Password="password_5",
                            Telephone="+86-12345678905"
                        },
                    }
                }
            );
            //保存更改
            context.SaveChanges();

            //交还父类
            base.Seed(context);
        }
    }
}
```
> ——————————————————

## 应用：
### 增删改查：
```C#
using DonetLinq2SQLite.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace DonetLinq2SQLite
{
    class Program
    {
        static void Main(string[] args)
        {
            SQLiteDBContext DBContext = new SQLiteDBContext();

            Console.WriteLine("有两个乘客的 BUS : " + 
                DBContext.BusSet.FirstOrDefault(b => b.Persons.Count == 2).Name);

            Console.WriteLine("——————\n 输出车辆信息：");
            foreach (Bus b in DBContext.BusSet.ToArray())
            {
                Console.WriteLine(string.Format(
                    "Bus ID : {0}\nName : {1}\nPersons : {2}\n***************",
                    b?.ID,
                    b?.Name,
                    (b?.Persons?.Count.ToString()) ?? "获取失败"
                ));
            }

            Console.WriteLine("——————\n 输出乘客信息：");
            foreach (Person p in DBContext.PersonSet.ToArray())
            {
                Console.WriteLine(string.Format(
                    "Person ID : {0}\nName : {1}\nTelephone:{2}\n***************",
                    p.ID,
                    p.Name,
                    p.Telephone
                ));
            }

            Console.WriteLine("——————\n 上车：");
            //直接向 bus 增加 person
            Bus bus = DBContext.BusSet.First(b => b.ID == 2);
            bus.Persons.Add(new Person() 
	    { 
	    	Name = "新上车乘客", 
	    	Password = "p@ssword", 
		Telephone = "+86-12345678910" 
	    });
            //先储存person，再加入bus
            Person person = new Person() 
	    { 
	    	Name = "另一个新上车乘客", 
		Password = "Ap@ssword", 
		Telephone = "+86-12345678911" 
	    };
            DBContext.PersonSet.Add(person);
            bus.Persons.Add(person);
            //储存更改
            DBContext.SaveChanges();

            Console.WriteLine("——————\n 输出车上乘客信息：");
            foreach (Person p in DBContext.BusSet.First(b => b.ID == 2).Persons)
            {
                Console.WriteLine(string.Format(
                    "Person ID : {0}\nName : {1}\nTelephone:{2}\n***************",
                    p.ID,
                    p.Name,
                    p.Telephone
                ));
            }
            
            Console.WriteLine("——————\n 下车：");
            bus.Persons.RemoveAt(0);
            bus.Persons.RemoveAt(2);
            bus.Persons.Last().Name = "最后一个上车乘客";
            DBContext.SaveChanges();

            Console.WriteLine("——————\n 输出车上乘客信息：");
            foreach (Person p in DBContext.BusSet.First(b => b.ID == 2).Persons)
            {
                Console.WriteLine(string.Format(
                    "Person ID : {0}\nName : {1}\nTelephone:{2}\n***************",
                    p.ID,
                    p.Name,
                    p.Telephone
                ));
            }

            Console.Read();
        }
    }
}
```
