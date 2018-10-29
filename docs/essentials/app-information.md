---
title: 'Xamarin.Essentials: Información de la aplicación'
description: Este documento describe la clase AppInfo en Xamarin.Essentials, que proporciona información acerca de la aplicación. Por ejemplo, muestra el nombre de la aplicación y la versión.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831512"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Información de la aplicación

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **AppInfo** proporciona información acerca de la aplicación.

## <a name="using-appinfo"></a>Uso de AppInfo

Agregue referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obtención de información de la aplicación:

La siguiente información se expone a través de la API:

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>Mostrar la configuración de aplicación

La clase **AppInfo** también puede mostrar la pagina de configuraciones de la aplicacion del sistema operativo:

```csharp
// Display settings page
AppInfo.OpenSettings();
```

Esta página de configuración permite al usuario cambiar los permisos de aplicación y realizar otras tareas específicas de la plataforma.

## <a name="api"></a>API

- [Código fuente de la clase AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentación de la API AppInfo](xref:Xamarin.Essentials.AppInfo)
