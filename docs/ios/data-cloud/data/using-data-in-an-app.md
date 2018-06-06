---
title: Utilizando los datos en una aplicación de iOS
description: Este documento describe la DataAccess_Adv ejemplo, que muestra cómo recopilar proporcionados por el usuario y realizar crear, leer, actualizar y eliminar operaciones de base de datos (CRUD) en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 5c9eab9316539ecf5988c8768bef9ef2cd61513e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784545"
---
# <a name="using-data-in-an-ios-app"></a>Utilizando los datos en una aplicación de iOS

El **DataAccess_Adv** ejemplo muestra una aplicación que permite proporcionados por el usuario y *CRUD* funcionalidad de base de datos (creación, lectura, actualización y eliminación). La aplicación consta de dos pantallas: una lista y un formulario de entrada de datos. Todo el código de acceso de datos es reutilizable en iOS y Android sin ninguna modificación.

Después de agregar algunos datos de las pantallas de aplicación este aspecto en iOS:

 ![](using-data-in-an-app-images/image9.png "lista de ejemplos de iOS")

 ![](using-data-in-an-app-images/image10.png "detalle de ejemplo de iOS")

IOS proyecto se muestra a continuación: el código se muestra en esta sección se encuentra dentro de la **Orm** directorio:

 ![](using-data-in-an-app-images/image13.png "árbol de proyecto de iOS")

El código nativo de la interfaz de usuario para el ViewControllers en iOS está fuera del ámbito de este documento.
Hacer referencia a la [iOS trabajar con tablas y las celdas](~/ios/user-interface/controls/tables/index.md) guía para obtener más información sobre los controles de interfaz de usuario.

## <a name="read"></a>Leer

Hay un par de operaciones de lectura en el ejemplo:

-  Leer la lista
-  Leer registros individuales


Los dos métodos en la `StockDatabase` clase son:

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

Para simplificar el código de aplicación, un único método de guardar es proporcionado por el que realiza una inserción o actualización, dependiendo de si se ha establecido la PrimaryKey. Dado que la `Id` propiedad se marca con un `[PrimaryKey]` atributo no se debe establecer en el código.
Este método lo detectará si el valor se estableció anterior guardado (comprobando la propiedad de clave principal) e insertar o actualizar el objeto según corresponda:

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



Las aplicaciones del mundo real normalmente requerirá cierta validación (por ejemplo, los campos obligatorios, longitudes mínimas u otras reglas de negocios).
Implementan buenas aplicaciones multiplataforma como gran parte de la validación lógica como sea posible en el código compartido, pasando de errores de validación de copia de seguridad en la interfaz de usuario para su presentación según las capacidades de la plataforma.

## <a name="delete"></a>Eliminar

A diferencia de la `Insert` y `Update` métodos, el `Delete<T>` método puede aceptar simplemente el valor de clave principal en lugar de una completa `Stock` objeto.
En este ejemplo un `Stock` objeto se pasa al método pero solo la propiedad de identificador se pasa a la `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Mediante un archivo de base de datos de SQLite rellenado previamente

Algunas aplicaciones se suministran con una base de datos que ya se rellena con datos.
Fácilmente puede hacerlo en la aplicación móvil mediante un archivo de base de datos de SQLite existente con la aplicación de envío y copiarlo en un directorio de escritura antes de tener acceso a ella. Dado que SQLite es un formato de archivo estándar que se usa en muchas plataformas, hay una serie de herramientas disponibles para crear un archivo de base de datos de SQLite:

-  **La extensión de Firefox SQLite Manager** – funciona en Mac y Windows y genera archivos que son compatibles con iOS y Android.
-  **Línea de comandos** : vea [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Al crear un archivo de base de datos de distribución con la aplicación, tenga cuidado con la nomenclatura de tablas y columnas para asegurarse de que coinciden con lo que espera el código, especialmente si usas SQLite.NET que esperarán los nombres para que coincida con sus propiedades y clases de C# (o la atributos personalizados asociados).

Para iOS, incluya el archivo de código en la aplicación y asegúrese de está marcado con **acción de compilación: contenido**. Coloque el código en el `FinishedLaunching` para copiar el archivo en un directorio de escritura *antes de* se llama a los métodos de datos. El código siguiente va a copiar una base de datos existente denominada **data.sqlite**, solo si aún no existe.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Cualquier código de acceso a datos (si ADO.NET o mediante SQLite.NET) que se ejecuta una vez que esto tenga will completado tienen acceso a los datos previamente rellenados.


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recetas de datos](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
