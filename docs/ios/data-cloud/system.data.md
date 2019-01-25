---
title: System.Data en Xamarin.iOS
description: Este documento describe cómo usar System.Data y Mono.Data.Sqlite.dll para tener acceso a datos de SQLite en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 014de47660f2c0ac8295495e417b3d5def135470
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879321"
---
# <a name="systemdata-in-xamarinios"></a>System.Data en Xamarin.iOS

Xamarin.iOS 8.10 agrega compatibilidad para [System.Data](xref:System.Data), incluido el `Mono.Data.Sqlite.dll` proveedor ADO.NET. La compatibilidad incluye la adición de las siguientes acciones [ensamblados](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Ejemplo

El siguiente programa crea una base de datos en `Documents/mydb.db3`, y si la base de datos previamente no existe, se rellenan con datos de ejemplo. A continuación, se consulta la base de datos, con la salida escrita en `stderr`.

### <a name="add-references"></a>Agregar referencias

En primer lugar, haga doble clic en el **referencias** nodo y elija **editar referencias...**  , a continuación, seleccione `System.Data` y `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Adición de nuevas referencias")](system.data-images/edit-references.png#lightbox)

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
> Como se mencionó en el ejemplo de código anterior, es aconsejable incrustar cadenas en los comandos SQL, ya que hace que el código sea vulnerable a [inyección de código SQL](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Uso de parámetros de comando

El código siguiente muestra cómo usar parámetros de comando para insertar texto escrito por el usuario de forma segura en la base de datos (incluso si el texto contiene caracteres especiales de SQL como único apóstrofo):

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

La funcionalidad que falta en **System.Data.dll** consta de:

-  Todo lo que requiere [System.CodeDom](xref:System.CodeDom) (p. ej.  [System.Data.TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
-  Compatibilidad con el archivo de configuración XML (p. ej.  [System.Data.Common.DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
-   [System.Data.Common.DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (depende de la compatibilidad con archivos de configuración XML)
-   [System.Data.OleDb](xref:System.Data.OleDb)
-   [System.Data.Odbc](xref:System.Data.Odbc)
-  El `System.EnterpriseServices.dll` dependencia era *quitado* desde `System.Data.dll` , lo que provocará la eliminación de la [SqlConnection.EnlistDistributedTransaction(ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) método.


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Mientras tanto, **Mono.Data.Sqlite.dll** no sufrido cambios de código fuente, pero en su lugar puede ser host a un número de *en tiempo de ejecución* emite desde `Mono.Data.Sqlite.dll` enlaza SQLite 3.5. iOS 8, mientras tanto, se distribuye con SQLite 3.8.5. Basta con decir que, algunas de las cosas han cambiado entre las dos versiones.

Versión anterior de iOS se suministran con las siguientes versiones de SQLite:

- **iOS 7** -versión 3.7.13.
- **iOS 6** -versión 3.7.13.
- **iOS 5** -versión 3.7.7.
- **iOS 4** -versión 3.6.22.

Por ejemplo, los problemas más comunes que parecen deberse a la consulta de esquema de base de datos, determinar en tiempo de ejecución que columnas hay en una tabla determinada, como `Mono.Data.Sqlite.SqliteConnection.GetSchema` (reemplazar [DbConnection.GetSchema](xref:System.Data.Common.DbConnection.GetSchema) y `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` (reemplazando [DbDataReader.GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). En resumen, parece que cualquier cosa con [DataTable](xref:System.Data.DataTable) es probable que funcione.

<a name="Data_Binding" />

## <a name="data-binding"></a>Enlace de datos

No se admite el enlace de datos en este momento.

