# Crear un proyecto de .Net Core



## Agregue un paquete Nuget:

-    Microsoft.EntityFrameworkCore.Design
-    Microsoft.EntityFrameworkCore.Sqlite
-    Microsoft.EntityFrameworkCore.Tools.DotNet

 :: Microsoft.EnterpriseFrameworkCore.Tools.DotNet consejos de soluciones incompatibles:

 > Abra el archivo .csproj
 > - ——————
```Xml
    <ItemGroup>
    <!-- agregar una línea manualmente -->
   <PackageReference Include-"Microsoft.EntityFrameworkCore.Tools.DotNet" Versión"2.1.0-preview1-final" />
    </ItemGroup>
```
>	- ——————
> Recarga del proyecto

# Crear clases de interacción de base de datos y clases de entidad de modelo

Aplicación
```C#
CoreSQLiteDBContext DBContext = new CoreSQLiteDBContext();

DBContext.SaveChanges();
```

! Dirección de objetos vacíos causados por la carga diferida:

# Método 1 . . . 

 No usar Microsoft.EntityFrameworkCore;

 Utilice el archivo . Incluir ("Hoja de datos ampliada") contiene tablas de datos extendidas;

	ex :
```c#
 foreach (editor de editores en BlogDBContext.Publishers.Include("Blogs"))

 Console.WriteLine(publisher. Nombre + " " + editor. Blogs.Count);
```

# Método 2 . . .

> Primera instalación Nuget : Microsoft.EnterpriseFrameworkCore.Proxies
```C#
 DBContext >

 invalidación protegida void OnConfiguring(DbContextOptionsBuilder optionbuilder)

    {

 Habilite Lazy Load Agent para resolver problemas de objetos vacíos causados por la carga diferida

 constructor de opciones. UseLazyLoadingProxies(). UseSqlite(-"Origen de datos-CoreSQLiteDB.db");

 constructor de opciones. UseSqlite(-"Origen de datos-CoreSQLiteDB.db");

    }
```
