---
title: 'Xamarin.Essentials: Información de la aplicación'
description: Este documento describe la clase de AppInfo en Xamarin.Essentials, que proporciona información acerca de la aplicación. Por ejemplo, muestra el nombre de la aplicación y la versión.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 25765fbbcbd2475bfcbc72ca5c4feefa8ef19d08
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782110"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Información de la aplicación

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **AppInfo** clase proporciona información acerca de la aplicación.

## <a name="using-appinfo"></a>Uso de AppInfo

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

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

## <a name="api"></a>API

- [Código fuente de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentación de la API de AppInfo](xref:Xamarin.Essentials.AppInfo)
