---
title: Seguimiento de las versiones Xamarin.Essentials
description: La clase VersionTracking le permite comprobar la versión de las aplicaciones y los números de compilación además de ver información adicional, como si fuera la primera vez la aplicación que se inicia alguna vez o para la versión actual, obtienen la información de compilación anterior y mucho más.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ec9d62589ddfb270d5c8a5321b3bc733fc597e4b
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-version-tracking"></a>Seguimiento de las versiones Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **VersionTracking** clase le permite comprobar la versión de las aplicaciones y los números de compilación además de ver información adicional, como si fuera la primera vez la aplicación que se inicia alguna vez o para la versión actual, obtienen el anterior información de compilación y mucho más.

## <a name="using-version-tracking"></a>Uso del seguimiento de la versión

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La primera vez que use la **VersionTracking** clase se iniciará la versión actual del seguimiento. Debe llamar a `Track` temprano solo en su aplicación cada vez que se carga para asegurarse de que se realiza el seguimiento de la información de versión actual:

```csharp
VersionTracking.Track();
```

Después de la inicial `Track` se denomina se puede leer la información de versión:

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

Toda la información de versión se almacena utilizando la [preferencias](preferences.md) API en Xamarin.Essentials y se almacena con un nombre de archivo de **.xamarinessentials [YOUR APP-paquete ID]**.

Desinstalar la aplicación hará que la _LocalSettings_y todas las versiones de seguimiento de la información que va a quitar.

## <a name="api"></a>API

- [Código de origen de seguimiento de versión](https://github.com/xamarin/Essentials/tree/master/Essentials/VersionTracking)
- [Documentación de API de seguimiento de versión](xref:Xamarin.Essentials.VersionTracking)
