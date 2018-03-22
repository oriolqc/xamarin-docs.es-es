---
title: System.Data
ms.topic: article
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 145a0692ed9761944eec4c7ece1f098a584f2d54
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="systemdata"></a>System.Data

Xamarin.iOS 8.10 agrega compatibilidad para [System.Data](https://developer.xamarin.com/api/namespace/System.Data/), incluido el `Mono.Data.Sqlite.dll` proveedor de ADO.NET. La compatibilidad incluye la adición de las siguientes acciones [ensamblados](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`


<a name="Example" />

## <a name="example"></a>Ejemplo

El siguiente programa crea una base de datos en `Documents/mydb.db3`, y si la base de datos previamente no existe, se rellenan con datos de ejemplo. A continuación, se consulta la base de datos, con el resultado escrito en `stderr`.

### <a name="add-references"></a>Agregar referencias

En primer lugar, haga doble clic en el **referencias** nodo y elija **editar referencias...**  , a continuación, seleccione `System.Data` y `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Agregar nuevas referencias")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Código de ejemplo

El código siguiente muestra un ejemplo sencillo de creación de una tabla e insertar filas mediante comandos SQL incrustados:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> Como se mencionó en el ejemplo de código anterior, es aconsejable para incrustar las cadenas de comandos SQL ya que hace que el código sea vulnerable a [inyección de código SQL](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Usar parámetros de comando

El código siguiente muestra cómo utilizar los parámetros del comando para insertar texto escrito por el usuario de forma segura en la base de datos (incluso si el texto contiene caracteres especiales de SQL como único apóstrofo):

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>Funcionalidad que falta

Ambos **System.Data** y **Mono.Data.Sqlite** faltan algunas funciones.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

Funcionalidad de falta de **System.Data.dll** consta de:

-  Cualquier cosa que necesite [System.CodeDom](https://developer.xamarin.com/api/namespace/System.CodeDom/) (p. ej.  [System.Data.TypedDataSetGenerator](https://developer.xamarin.com/api/type/System.Data.TypedDataSetGenerator/) )
-  Compatibilidad de archivos de configuración XML (p. ej.  [System.Data.Common.DbProviderConfigurationHandler](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderConfigurationHandler/) )
-   [System.Data.Common.DbProviderFactories](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderFactories/) (depende de la compatibilidad con archivos de configuración XML)
-   [System.Data.OleDb](https://developer.xamarin.com/api/namespace/System.Data.OleDb/)
-   [System.Data.Odbc](https://developer.xamarin.com/api/namespace/System.Data.Odbc/)
-  El `System.EnterpriseServices.dll` dependencia era *quitado* de `System.Data.dll` , da lugar a la eliminación de la [SqlConnection.EnlistDistributedTransaction(ITransaction)](https://developer.xamarin.com/api/member/System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction/(System.EnterpriseServices.ITransaction)) método.


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Mientras tanto, **Mono.Data.Sqlite.dll** ha sufrido ningún cambio de código de origen, pero en su lugar puede ser host a un número de *en tiempo de ejecución* emite desde `Mono.Data.Sqlite.dll` enlaza SQLite 3.5. iOS 8, mientras tanto, se distribuye con SQLite 3.8.5. Basta con decir, algunas de las cosas han cambiado entre las dos versiones.

La versión anterior de operaciones de E/s se suministran con las siguientes versiones de código:

- **iOS 7** -versión 3.7.13.
- **iOS 6** -versión 3.7.13.
- **iOS 5** -versión 3.7.7.
- **iOS 4** -versión 3.6.22.

Por ejemplo, los problemas más comunes que parecen estar relacionado con la consulta de esquema de base de datos, determinar en tiempo de ejecución que éstas existen en una tabla determinada, como `Mono.Data.Sqlite.SqliteConnection.GetSchema` (reemplazar [DbConnection.GetSchema](https://developer.xamarin.com/api/member/System.Data.Common.DbConnection.GetSchema/)) y `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` () reemplazar [DbDataReader.GetSchemaTable](https://developer.xamarin.com/api/member/System.Data.Common.DbDataReader.GetSchemaTable/)). En resumen, parece que nada mediante [DataTable](https://developer.xamarin.com/api/type/System.Data.DataTable/) es probable que funcione.

<a name="Data_Binding" />

## <a name="data-binding"></a>Enlace de datos

Enlace de datos no se admite en este momento.

