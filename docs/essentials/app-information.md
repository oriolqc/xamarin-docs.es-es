---
title: Información de la aplicación de Xamarin.Essentials
description: La clase AppInfo proporciona información acerca de la aplicación.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 32e3eb8fab719540e4c9ffec4e57f5510c10e3f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="xamarinessentials-app-information"></a>Información de la aplicación de Xamarin.Essentials

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

- [Código fuente de AppInfo](https://github.com/xamarin/Essentials/tree/master/Essentials/AppInfo)
- [Documentación de la API de AppInfo](xref:Xamarin.Essentials.AppInfo)
