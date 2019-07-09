---
title: Uso de datos en una aplicación de iOS
description: Este documento describe la DataAccess_Adv ejemplo, que muestra cómo recopilar entradas del usuario y realizar crear, leer, actualizar y eliminar (CRUD) operaciones de base de datos en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: a47ab26777c594658810b014025477486bbe5cc2
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650147"
---
# <a name="using-data-in-an-ios-app"></a>Uso de datos en una aplicación de iOS

El **DataAccess_Adv** muestra una aplicación que permite la entrada del usuario y *CRUD* funcionalidad de base de datos (creación, lectura, actualización y eliminación). La aplicación consta de dos pantallas: una lista y un formulario de entrada de datos. Todo el código de acceso de datos es volver a usar en iOS y Android sin ninguna modificación.

Después de agregar algunos datos de las pantallas de la aplicación este aspecto en iOS:

 ![](using-data-in-an-app-images/image9.png "lista de ejemplos de iOS")

 ![](using-data-in-an-app-images/image10.png "detalle de ejemplo de iOS")

Proyecto de iOS se muestra a continuación, el código se muestra en esta sección se encuentra dentro de la **Orm** directorio:

 ![](using-data-in-an-app-images/image13.png "árbol de proyecto de iOS")

El código de interfaz de usuario nativo de la ViewControllers en iOS está fuera del ámbito de este documento.
Hacer referencia a la [iOS trabajar con tablas y celdas](~/ios/user-interface/controls/tables/index.md) guía para obtener más información sobre los controles de interfaz de usuario.

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

iOS representa los datos de forma diferente, como un `UITableView`.

## <a name="create-and-update"></a>Crear y actualizar

Para simplificar el código de aplicación, un único método save es siempre que realiza una inserción o actualización, dependiendo de si se ha establecido el PrimaryKey. Dado que el `Id` propiedad está marcada con un `[PrimaryKey]` no debe establecer en el código de atributo.
Este método lo detectará si el valor ha sido anterior guardado (activando la propiedad de clave principal) e insertar o actualizar el objeto en consecuencia:

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



Normalmente, las aplicaciones del mundo real requerirá alguna validación (por ejemplo, los campos obligatorios, longitudes mínimas u otras reglas de negocio).
Implementan buenas aplicaciones multiplataforma como gran parte de la validación de lógica como sea posible en el código compartido, pasando de errores de validación de copia de seguridad en la interfaz de usuario para su presentación según las capacidades de la plataforma.

## <a name="delete"></a>Eliminar

A diferencia de la `Insert` y `Update` métodos, el `Delete<T>` método puede aceptar simplemente el valor de clave principal en lugar de una completa `Stock` objeto.
En este ejemplo un `Stock` objeto se pasa al método, pero solo la propiedad Id se pasa a la `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Uso de un archivo de base de datos SQLite rellenado previamente

Algunas aplicaciones se suministran con una base de datos ya rellenado con datos.
Fácilmente puede realizar esto en su aplicación móvil mediante un archivo de base de datos de SQLite existente con la aplicación de envío y copiarlo en un directorio de escritura antes de acceder a él. Dado que SQLite es un formato de archivo estándar que se usa en muchas plataformas, hay varias herramientas disponibles para crear un archivo de base de datos de SQLite:

-  **La extensión de Firefox SQLite Manager** – funciona en Mac y Windows y genera archivos que son compatibles con iOS y Android.
-  **Línea de comandos** – vea [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Al crear un archivo de base de datos de distribución con la aplicación, tenga cuidado con la nomenclatura de tablas y columnas para asegurarse de que coinciden con lo que espera el código, especialmente si usas SQLite.NET que esperará a los nombres para que coincida con sus propiedades y clases de C# (o el atributos personalizados asociados).

Para iOS, incluya el archivo de sqlite en la aplicación y asegúrese de está marcada con **acción de compilación: Contenido**. Coloque el código en el `FinishedLaunching` para copiar el archivo en un directorio de escritura *antes* llamar a cualquier método de datos. El siguiente código va a copiar una base de datos denominada **data.sqlite**, sólo si aún no existe.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Cualquier código de acceso a datos (si ADO.NET o uso de SQLite.NET) que se ejecuta después de que esto tiene completado tienen acceso a los datos previamente rellenados.


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recetas de datos](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
