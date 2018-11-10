---
title: 'Xamarin.Essentials: Seguimiento de versiones'
description: La clase VersionTracking de Xamarin.Essentials permite comprobar los números de versión y compilación de las aplicaciones además de ver información adicional, por ejemplo si es la primera vez que se ha iniciado la aplicación o para la versión actual, obtener información de la compilación anterior y mucho más.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1eae0bf7c21dd7efa7655633896bdb2897f9d782
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674865"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Seguimiento de versiones

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **VersionTracking** permite comprobar los números de versión y compilación de las aplicaciones además de ver información adicional, por ejemplo si es la primera vez que se ha iniciado la aplicación o para la versión actual, obtener información de la compilación anterior y mucho más.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>Uso de Seguimiento de versiones

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La primera vez que use la clase **VersionTracking**, se iniciará el seguimiento de la versión actual. Solo se debe llamar a `Track` al principio de la aplicación cada vez que se cargue para asegurarse de que se realiza el seguimiento de la información de versión actual:

```csharp
VersionTracking.Track();
```

Después de la llamada inicial a `Track`, se puede leer la información de la versión:

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

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

Toda la información de la versión se almacena mediante la API [Preferences](preferences.md) de Xamarin.Essentials y se almacena con un nombre de archivo de **[ID-DEL-PAQUETE-DE-LA-APLICACIÓN].xamarinessentials.versiontracking**, y sigue la misma persistencia de datos que se describe en la documentación de [Preferences](preferences.md#persistence).

## <a name="api"></a>API

- [Código fuente de VersionTracking](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [Documentación de API para VersionTracking](xref:Xamarin.Essentials.VersionTracking)
