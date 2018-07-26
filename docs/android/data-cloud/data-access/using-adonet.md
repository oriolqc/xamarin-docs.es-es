---
title: Uso de ADO.NET con Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 9e0c1be2e37355242db2fb70857d90127c3b5259
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242217"
---
# <a name="using-adonet-with-android"></a>Uso de ADO.NET con Android

Xamarin tiene compatibilidad integrada para la base de datos SQLite que está disponible en Android y se puede exponer mediante la conocida sintaxis similar a ADO.NET. Con estas API, deberá escribir instrucciones SQL que se procesan como SQLite, `CREATE TABLE`, `INSERT` y `SELECT` instrucciones.

## <a name="assembly-references"></a>Referencias de ensamblado

Usar SQLite a través de ADO.NET, debe agregar el acceso `System.Data` y `Mono.Data.Sqlite` hace referencia al proyecto Android, como se muestra aquí:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin) 

![Referencias de Android en Visual Studio](using-adonet-images/image7.png "hace referencia Android en Visual Studio") 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac) 

![Referencias de Android en Visual Studio para Mac](using-adonet-images/image5.png "Android hace referencia en Visual Studio para Mac") 

-----


Haga clic en **referencias > Editar referencias...**  , a continuación, haga clic para seleccionar los ensamblados necesarios.

## <a name="about-monodatasqlite"></a>Acerca de Mono.Data.Sqlite

Usaremos el `Mono.Data.Sqlite.SqliteConnection` clase para crear un archivo de base de datos vacía y, a continuación, para crear una instancia `SqliteCommand` objetos que podemos usar para ejecutar instrucciones SQL en la base de datos.

**Creación de una base de datos en blanco** &ndash; llamar a la `CreateFile` método con válido (ie. grabable) ruta de acceso de archivo. Debe comprobar si el archivo ya existe antes de llamar a este método, en caso contrario, se creará una nueva base de datos (en blanco) a través de la parte superior de la vieja y se perderán los datos en el archivo anterior.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` El `dbPath` variable debe determinarse según las reglas descritas anteriormente en este documento.

**Creación de una conexión de base de datos** &ndash; una vez creado el archivo de base de datos de SQLite puede crear un objeto de conexión para tener acceso a los datos. La conexión se construye con una cadena de conexión que tiene la forma de `Data Source=file_path`, tal y como se muestra aquí:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Como se mencionó anteriormente, una conexión nunca debe volver a usar entre diferentes subprocesos. En caso de duda, crear la conexión según sea necesario y cerrar cuando haya terminado; pero tenga en cuenta de hacer más a menudo que requiere demasiado.

**Crear y ejecutar un comando de base de datos** &ndash; una vez que tenemos una conexión podemos ejecutar comandos SQL arbitrarios en él. El código siguiente muestra un `CREATE TABLE` instrucción que se está ejecutando.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

Cuando se ejecuta SQL directamente en la base de datos debe tomar las precauciones normales no deben realizar las solicitudes no válidas, por ejemplo, si se intenta crear una tabla que ya existe. Realizar un seguimiento de la estructura de la base de datos para que no provocan una `SqliteException` como **ya existe la tabla de errores de SQLite [elementos]**.

## <a name="basic-data-access"></a>Acceso a datos básicos

El *DataAccess_Basic* código de ejemplo de este documento este aspecto cuando se ejecuta en Android:

![Ejemplo de ADO.NET Android](using-adonet-images/image8.png "ejemplo ADO.NET Android")

El código siguiente muestra cómo realizar operaciones sencillas de SQLite y muestra los resultados como texto en la ventana principal de la aplicación.

Necesita incluir estos espacios de nombres:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

Ejemplo de código siguiente muestra una interacción de la base de datos completa:

1.  Crear el archivo de base de datos
2.  Insertar algunos datos.
3.  Consultar los datos

Estas operaciones generalmente aparecen en varios lugares en todo el código, por ejemplo puede crear el archivo de base de datos y tablas cuando se inicia por primera vez la aplicación y realizar lecturas y escrituras en pantallas individuales en la aplicación. En el ejemplo siguiente se han agrupado en un único método para este ejemplo:

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}

```

## <a name="more-complex-queries"></a>Consultas más complejas

Como SQLite permite comandos arbitrarios de SQL se ejecute en los datos, puede realizar cualquier `CREATE`, `INSERT`, `UPDATE`, `DELETE`, o `SELECT` las instrucciones que se desee. Puede leer acerca de los comandos SQL compatibles con SQLite en el sitio Web de Sqlite. Las instrucciones SQL se ejecutan mediante uno de los tres métodos en un `SqliteCommand` objeto:

-   **ExecuteNonQuery** &ndash; utiliza normalmente para la inserción de datos o la creación de tabla. El valor devuelto para algunas operaciones es el número de filas afectadas, en caso contrario, es -1.

-   **ExecuteReader** &ndash; usa cuando una colección de filas se debe devolver como un `SqlDataReader`.

-   **ExecuteScalar** &ndash; recupera un único valor (por ejemplo, un agregado).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`, `UPDATE`, y `DELETE` instrucciones devolverá el número de filas afectadas. Todas las demás instrucciones SQL devuelven -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

El método siguiente se muestra un `WHERE` cláusula en la `SELECT` instrucción.
Dado que el código es diseñar una instrucción SQL completa debe tener cuidado para escapar caracteres reservados como las comillas (') en torno a las cadenas.

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

El método `ExecuteReader` devuelve un objeto `SqliteDataReader`. Además el `Read` método que se muestra en el ejemplo, incluyen otras propiedades útiles:

-   **RowsAffected** &ndash; recuento de las filas afectadas por la consulta.

-   **HasRows** &ndash; si se devolvieron filas.


### <a name="executescalar"></a>EXECUTESCALAR

Utilice este formulario `SELECT` las instrucciones que devuelven un único valor (por ejemplo, un agregado).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

El `ExecuteScalar` es de tipo de valor devuelto del método `object` &ndash; debería convertir el resultado en función de la consulta de base de datos. El resultado podría ser un entero comprendido entre un `COUNT` consulta o una cadena de una sola columna `SELECT` consulta. Tenga en cuenta que esto es diferente a otros `Execute` los métodos que devuelven un objeto de lector o un recuento del número de filas afectadas.



## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
