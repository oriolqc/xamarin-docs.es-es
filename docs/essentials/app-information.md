---
title: 'Xamarin.Essentials: Información de la aplicación'
description: En este documento se describe la clase AppInfo de Xamarin.Essentials, que proporciona información sobre la aplicación. Por ejemplo, muestra el nombre de la aplicación y la versión.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 00419fb746609464b49be343938905614c59ab29
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691768"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Información de la aplicación

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **AppInfo** proporciona información sobre la aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Uso de AppInfo

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obtención de información de la aplicación:

La información siguiente se expone a través de la API:

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

## <a name="displaying-application-settings"></a>Representación de la configuración de la aplicación

La clase **AppInfo** también puede mostrar una página de configuración mantenida por el sistema operativo para la aplicación:

```csharp
// Display settings page
AppInfo.OpenSettings();
```

Esta página de configuración permite al usuario cambiar los permisos de la aplicación y realizar otras tareas específicas de la plataforma.

## <a name="api"></a>API

- [Código fuente de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentación de API para AppInfo](xref:Xamarin.Essentials.AppInfo)
