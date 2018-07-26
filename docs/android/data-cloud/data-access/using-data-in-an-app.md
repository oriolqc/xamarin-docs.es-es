---
title: Uso de datos en una aplicación de Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 563c04ef1c8eec00108844894c5f9bdc0e9950e3
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241895"
---
# <a name="using-data-in-an-app"></a>Uso de datos en una aplicación

El **DataAccess_Adv** muestra una aplicación que permite la entrada del usuario y la funcionalidad de base de datos CRUD (creación, lectura, actualización y eliminación). La aplicación consta de dos pantallas: una lista y un formulario de entrada de datos. Todo el código de acceso de datos es volver a usar en iOS y Android sin ninguna modificación.

Después de agregar algunos datos de las pantallas de la aplicación este aspecto en Android:

![Lista de ejemplos de Android](using-data-in-an-app-images/image11.png "lista de ejemplos de Android")

![Detalle de ejemplo de Android](using-data-in-an-app-images/image12.png "detalle de ejemplo de Android")

A continuación se muestra el proyecto de Android &ndash; el código se muestra en esta sección se encuentra dentro de la **Orm** directorio:

![Árbol de proyecto de Android](using-data-in-an-app-images/image14.png "árbol de proyecto de Android")

El código de interfaz de usuario nativo para las actividades en Android está fuera del ámbito de este documento. Hacer referencia a la [Android ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) guía para obtener más información sobre los controles de interfaz de usuario.

## <a name="read"></a>Leer

Hay un par de operaciones de lectura en el ejemplo:

-  Leer la lista
-  Lectura de registros individuales

Los dos métodos en el `StockDatabase` son de clase:

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

Android presenta los datos como un `ListView`.

## <a name="create-and-update"></a>Crear y actualizar

Para simplificar el código de aplicación, un único método save es siempre que realiza una inserción o actualización, dependiendo de si se ha establecido el PrimaryKey. Dado que el `Id` propiedad está marcada con un `[PrimaryKey]` no debe establecer en el código de atributo. Este método lo detectará si el valor ha sido anterior guardado (activando la propiedad de clave principal) e insertar o actualizar el objeto en consecuencia:

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```

Normalmente, las aplicaciones del mundo real requerirá alguna validación (por ejemplo, los campos obligatorios, longitudes mínimas u otras reglas de negocio). Implementan buenas aplicaciones multiplataforma como gran parte de la validación de lógica como sea posible en el código compartido, pasando de errores de validación de copia de seguridad en la interfaz de usuario para su presentación según las capacidades de la plataforma.

## <a name="delete"></a>Eliminar

A diferencia de la `Insert` y `Update` métodos, el `Delete<T>` método puede aceptar simplemente el valor de clave principal en lugar de una completa `Stock` objeto. En este ejemplo un `Stock` objeto se pasa al método, pero solo la propiedad Id se pasa a la `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Uso de un archivo de base de datos SQLite rellenado previamente

Algunas aplicaciones se suministran con una base de datos ya rellenado con datos. Fácilmente puede realizar esto en su aplicación móvil mediante un archivo de base de datos de SQLite existente con la aplicación de envío y copiarlo en un directorio de escritura antes de acceder a él. Dado que SQLite es un formato de archivo estándar que se usa en muchas plataformas, hay varias herramientas disponibles para crear un archivo de base de datos de SQLite:

-   **La extensión de Firefox SQLite Manager** &ndash; funciona en Mac y Windows y genera archivos que son compatibles con iOS y Android.

-   **Línea de comandos** &ndash; vea [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

Al crear un archivo de base de datos de distribución con la aplicación, tenga cuidado con la nomenclatura de tablas y columnas para asegurarse de que coinciden con lo que espera el código, especialmente si usas SQLite.NET que esperará a los nombres para que coincida con sus propiedades y clases de C# (o el atributos personalizados asociados).

Para asegurarse de que algo de código se ejecuta antes de nada en su aplicación Android, puede colocarlo en la primera actividad para cargar o crear un `Application` subclase que se carga antes de cualquier actividad. El código siguiente muestra un `Application` subclase que copia un archivo de base de datos existente **data.sqlite** fuera de la **Resources/Raw/** directory.

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
