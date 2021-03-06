# ﻿创建 .Net Core 项目

## 添加 Nuget 包：
    - Microsoft.EntityFrameworkCore.Design
    - Microsoft.EntityFrameworkCore.Sqlite
    - Microsoft.EntityFrameworkCore.Tools.DotNet
    - Microsoft.EntityFrameworkCore.Tools.DotNet 提示不兼容的解决方案：
   
## 打开 {项目}.csproj 文件
  ```Xml
	<ItemGroup>
		<!-- 手动加入一行 -->
		<PackageReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.1.0-preview1-final" />
	</ItemGroup>
  ```

>	重新加载项目

## 创建数据库交互类和模型实体类

# 应用
```C#
CoreSQLiteDBContext DBContext = new CoreSQLiteDBContext();
DBContext.SaveChanges();
```


## ! 解决延迟加载导致的空对象问题：
> 方法1 >. 
	首先 using Microsoft.EntityFrameworkCore;
	查询时在目标数据表后使用 .Include("扩展数据表") 包含扩展数据表即可;
	ex :
  ```C#
    foreach (Publisher publisher in BlogDBContext.Publishers.Include("Blogs"))
        Console.WriteLine(publisher.Name + " " + publisher.Blogs.Count);
  ```
> 方法2 >.
	首先安装 Nuget : Microsoft.EntityFrameworkCore.Proxies
  ```C#
	DBContext =>
    protected override void OnConfiguring(DbContextOptionsBuilder optionbuilder)
    {
        //启用 懒惰加载代理 解决因为懒加载导致的空对象问题
        optionbuilder.UseLazyLoadingProxies().UseSqlite(@"Data Source=CoreSQLiteDB.db");
        //optionbuilder.UseSqlite(@"Data Source=CoreSQLiteDB.db");
    }
  ```
