---
title: 'Xamarin.Essentials: solución de problemas'
description: Este documento describe cómo solucionar los problemas encontrados al desarrollar con la biblioteca Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 8cb18ab029d2fd161c60fda7e130f319b8f0c3ab
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947379"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: solución de problemas

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Error: Para Xamarin.Android.Support.Compat se detectó un conflicto de versión

Puede producirse el siguiente error al actualizar paquetes de NuGet (o agrega un nuevo paquete) con un proyecto de Xamarin.Forms que usa Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

El problema es que no coinciden las dependencias de los dos paquetes de NuGet. Esto se puede resolver agregando manualmente una versión específica de la dependencia (en este caso **Xamarin.Android.Support.Compat**) que puede admitir tanto.

Para ello, agregue el paquete NuGet que es el origen del conflicto manualmente y usar el **versión** lista para seleccionar una versión específica. Actualmente la versión 27.0.2.1 del Xamarin.Android.Support.Compat & Xamarin.Android.Support.Core.Util NuGet resolverá este error.

Consulte [esta entrada de blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) para obtener más información y ver un vídeo sobre cómo resolver el problema.

Si surge algún problema o encontrar un error envíe el informe en el [repositorio de Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
