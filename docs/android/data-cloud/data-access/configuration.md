---
title: Configuración
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/11/2016
ms.openlocfilehash: b3a7858361d25f26807ea328e8bfdd30ca8d483b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241884"
---
# <a name="configuration"></a>Configuración

Usar SQLite en la aplicación de Xamarin.Android que necesita para determinar la ubicación de archivo correcto para el archivo de base de datos.

## <a name="database-file-path"></a>Ruta de acceso de archivo de base de datos

Independientemente de qué método de acceso de datos que use, debe crear un archivo de base de datos antes de que se pueden almacenar datos con SQLite. Dependiendo de la plataforma de destino es la ubicación del archivo será diferente. Para Android puede usar Environment (clase) para construir una ruta válida, como se muestra en el siguiente fragmento de código:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Hay otras cosas a tener en cuenta al decidir dónde se almacenará el archivo de base de datos. Por ejemplo, en Android puede elegir si desea utilizar almacenamiento interno o externo.

Si desea usar una ubicación diferente en cada plataforma en la aplicación de plataforma cruzada puede usar una directiva de compilador tal como se muestra para generar una ruta de acceso diferente para cada plataforma:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Para obtener indicaciones sobre el uso del sistema de archivos en Android, consulte el [examinar archivos](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) receta. Consulte la [Building Cross Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento para obtener más información sobre el uso de directivas de compilador para escribir código específico para cada plataforma.

## <a name="threading"></a>Subprocesos

No se debe usar la misma conexión de base de datos de SQLite en varios subprocesos. Tenga cuidado al abrir, usar y, a continuación, cierre las conexiones que se crea en el mismo subproceso.

Para asegurarse de que el código no está intentando obtener acceso a la base de datos de SQLite desde varios subprocesos al mismo tiempo, realizar un bloqueo manualmente cada vez que se va a obtener acceso a la base de datos, similar al siguiente:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Todos los accesos de base de datos (lecturas, escrituras, actualizaciones, etcetera) deben incluirse con el mismo bloqueo. Debe tener cuidado para evitar una situación de interbloqueo, asegúrese de que el trabajo dentro de la cláusula de bloqueo se mantiene simple y no llamar a otros métodos que también se pueden realizar un bloqueo.


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
