---
title: Uso de SQLite.NET con Android
description: La biblioteca de NuGet de PCL SQLite.NET proporciona un mecanismo de acceso de datos simple para las aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/18/2018
ms.openlocfilehash: 878b0097fc0f62e6b90d948d8a15ab39db4b2f3e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732260"
---
# <a name="using-sqlitenet-with-android"></a>Uso de SQLite.NET con Android

La biblioteca de SQLite.NET Xamarin recomienda es un ORM muy básica que le permite almacenar y recuperar los objetos en la base de datos local de SQLite en un dispositivo Android con facilidad. ORM es el acrónimo asignación relacional de objetos &ndash; una API que permite guardar y recuperar "objetos" de una base de datos sin necesidad de escribir instrucciones SQL.

Para incluir la biblioteca de SQLite.NET en una aplicación de Xamarin, agregue el siguiente paquete de NuGet al proyecto:

- **Nombre del paquete:** sqlite-net-pcl
- **Autor:** Frank A. Krueger
- **Id.:** sqlite-net-pcl
- **Dirección URL:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Paquete de SQLite.NET NuGet](using-sqlite-orm-images/image1a-sml.png "paquete SQLite.NET NuGet")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Hay una serie de distintos paquetes de SQLite disponibles: asegúrese de que elijan la correcta (no puede ser el mejor resultado en la búsqueda).

Una vez que tenga la biblioteca de SQLite.NET está disponible, siga estos tres pasos para utilizar para tener acceso a una base de datos:

1.  **Agregar un elemento using instrucción** &ndash; agregue la siguiente instrucción para los archivos de C# donde se requiere acceso a datos:

    ```csharp
    using SQLite;
    ```

2.  **Crear una base de datos en blanco** &ndash; una referencia de base de datos puede crearse al pasar la ruta de acceso de archivo en el constructor de clase SQLiteConnection. No es necesario comprobar si ya existe el archivo &ndash; se crean automáticamente si es necesario, en caso contrario, se abrirá el archivo de base de datos existente. El `dbPath` variable debe determinarse según las reglas descritas anteriormente en este documento:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3.  **Guardar datos** &ndash; una vez haya creado un objeto SQLiteConnection, comandos de base de datos se ejecutan mediante una llamada a sus métodos, como CreateTable e Insert similar al siguiente:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4.  **Recuperar datos** &ndash; para recuperar un objeto (o una lista de objetos) use la siguiente sintaxis:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Ejemplo de acceso de datos básicos

El *DataAccess_Basic* código de ejemplo de este documento aspecto cuando se ejecuta en Android. El código muestra cómo realizar operaciones sencillas de SQLite.NET y muestra los resultados como texto en la ventana principal de la aplicación.


**Android**

![Ejemplo de Android SQLite.NET](using-sqlite-orm-images/image3.png "ejemplo SQLite.NET Android")

El ejemplo de código siguiente muestra una interacción de la base de datos completa utiliza la biblioteca de SQLite.NET para encapsular el acceso de base de datos subyacente.
Muestra:

1.  Crear el archivo de base de datos

2.  Insertar algunos datos mediante la creación de objetos y, a continuación, guardarlas

3.  Consultar los datos

Debe incluir estos espacios de nombres:

```csharp
using SQLite; // from the github SQLite.cs class
```

La última de ellas requiere que haya agregado código al proyecto. Tenga en cuenta que la tabla de base de datos de SQLite se define mediante la adición de atributos a una clase (el `Stock` clase) en lugar de un comando CREATE TABLE.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

Mediante el `[Table]` atributo sin especificar un parámetro de nombre de la tabla hará que la tabla de base de datos subyacente tener el mismo nombre que la clase (en este caso, "Stock"). El nombre de tabla real es importante si escribir consultas SQL directamente en la base de datos en lugar de usar los métodos de acceso de datos ORM. De forma similar el `[Column("_id")]` atributo es opcional y si está ausente de una columna se agregará a la tabla con el mismo nombre que la propiedad de la clase.

## <a name="sqlite-attributes"></a>Atributos de código

Atributos comunes que se pueden aplicar a las clases para controlar cómo se almacenan en la base de datos subyacente incluyen:

-   **[PrimaryKey]**  &ndash; Este atributo se puede aplicar a una propiedad de entero para obligarlo a ser la clave principal de la tabla subyacente. No se admiten las claves principales compuestas.

-   **[AutoIncrement]**  &ndash; Este atributo hará que el valor de una propiedad de entero como incremento automático para cada nuevo objeto que se insertan en la base de datos

-   **[Column(name)]**  &ndash; Suministra opcional `name` parámetro invalidará el valor predeterminado del nombre de la columna de base de datos subyacente (que es el mismo que la propiedad).

-   **[Table(name)]**  &ndash; Marca la clase como la posibilidad de que se almacenará en una tabla de código subyacente. Especificar el parámetro name opcional invalidará el valor predeterminado del nombre de la tabla de base de datos subyacente (que es el mismo que el nombre de clase).

-   **[MaxLength(value)]**  &ndash; Restringir la longitud de una propiedad de texto, cuando se intenta realizar una inserción de una base de datos. Utilizar código debe comprobarlo antes de insertar el objeto como este atributo es sólo 'activan' cuando la inserción de una base de datos o se intenta realizar la operación de actualización.

-   **[Omitir]**  &ndash; SQLite.NET hace para pasar por alto esta propiedad.
    Esto es especialmente útil para las propiedades que tienen un tipo que no se pueden almacenar en la base de datos o propiedades que las colecciones de modelo que no se puede resolver automáticamente ser SQLite.

-   **[Unique]**  &ndash; Garantiza que los valores de la columna de base de datos subyacente son únicos.


La mayoría de estos atributos son opcionales, SQLite usará los valores predeterminados para los nombres de tabla y columna. Siempre debe especificar una clave principal de tipo entero para que las consultas de selección y eliminación se pueden realizar eficazmente en los datos.

## <a name="more-complex-queries"></a>Consultas más complejas

Los siguientes métodos de `SQLiteConnection` puede utilizarse para realizar otras operaciones de datos:

-   **Insertar** &ndash; agrega un nuevo objeto a la base de datos.

-   **Obtener&lt;T&gt;**  &ndash; intenta recuperar un objeto mediante la clave principal.

-   **Tabla&lt;T&gt;**  &ndash; devuelve todos los objetos de la tabla.

-   **Eliminar** &ndash; elimina un objeto con la clave principal.

-   **Consulta&lt;T&gt;**  &ndash; realizar una consulta SQL que devuelve un número de filas (como objetos).

-   **Ejecutar** &ndash; utilizar este método (y no `Query`) cuando no espera insertar filas desde el código SQL (por ejemplo, las instrucciones INSERT, UPDATE y DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Obtiene un objeto de la clave principal

SQLite.Net proporciona el método Get para recuperar un objeto único basado en su clave principal.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selección de un objeto usando Linq

Los métodos que devuelven colecciones admiten `IEnumerable<T>` para que pueda usar Linq para consultar u ordena el contenido de una tabla. El código siguiente muestra un ejemplo de cómo utilizar Linq para filtrar todas las entradas que comienzan por la letra "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Al seleccionar un objeto mediante SQL

A veces, incluso aunque SQLite.Net puede proporcionar acceso basado en objetos a los datos, deberá hacer una consulta más compleja que Linq permite (o es posible que tenga un rendimiento más rápido). Puede utilizar comandos SQL con el método de consulta, como se muestra aquí:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Al escribir instrucciones SQL directamente cree una dependencia en los nombres de tablas y columnas de la base de datos que se han generado desde las clases y sus atributos. Si cambia los nombres en el código debe olvidar actualizar todas las instrucciones SQL escritas manualmente.

### <a name="deleting-an-object"></a>Eliminar un objeto

La clave principal se usa para eliminar la fila, como se muestra aquí:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Puede comprobar el `rowcount` para confirmar el número de filas afectado (eliminado en este caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Uso de SQLite.NET con varios subprocesos

SQLite es compatible con tres modos diferentes de subprocesamiento: *subproceso único*, *multiproceso*, y *serie*. Si desea tener acceso a la base de datos desde varios subprocesos sin restricciones, puede configurar SQLite para usar el **serie** el modo de subprocesamiento. Es importante establecer este modo al principio de la aplicación (por ejemplo, al principio de la `OnCreate` método).

Para cambiar el modo de subprocesamiento, llame a `SqliteConnection.SetConfig`. Por ejemplo, esta línea de código configura SQLite para **serie** modo: 

```csharp
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

La versión de Android de SQLite tiene una limitación que requiere algunos pasos más. Si la llamada a `SqliteConnection.SetConfig` produce una excepción de SQLite como `library used incorrectly`, a continuación, debe usar la siguiente solución alternativa:

1.  Vínculo a nativo **libsqlite.so** biblioteca para que la `sqlite3_shutdown` y `sqlite3_initialize` API se ponen a disposición a la aplicación:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```


2.  Al principio de la `OnCreate` /método siguiente, agregue este código en código de cierre, configurarlo para **serie** modo y reinicializar SQLite:

    ```csharp
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Esta solución también funciona para el `Mono.Data.Sqlite` biblioteca. Para obtener más información acerca de SQLite y subprocesamiento múltiple, consulte [SQLite y varios subprocesos](https://www.sqlite.org/threadsafe.html). 

## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
