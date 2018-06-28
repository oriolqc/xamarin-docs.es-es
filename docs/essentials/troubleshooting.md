---
title: 'Xamarin.Essentials: solución de problemas'
description: Este documento describe cómo solucionar los problemas que surgen al desarrollar con la biblioteca Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 3dba315aec2475cb334110ba7555f773f4165aa1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066739"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: solución de problemas

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Error: Para Xamarin.Android.Support.Compat se detectó un conflicto de versión

Puede producirse el siguiente error al actualizar paquetes de NuGet (o agrega un nuevo paquete) con un proyecto de Xamarin.Forms que usa Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.7.0.17-preview -> Xamarin.Android.Support.CustomTabs 27.0.2 -> Xamarin.Android.Support.Compat (= 27.0.2) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

El problema es que no coincidan las dependencias de los dos NuGets. Esto se puede resolver agregando manualmente una versión específica de la dependencia (en este caso **Xamarin.Android.Support.Compat**) que puede ser compatible con.

Para ello, agregue NuGet que constituye el origen del conflicto manualmente y utilice la **versión** lista para seleccionar una versión específica. Actualmente la versión 27.0.2 de Xamarin.Android.Support.Compat NuGet resolverá este error.

Hacer referencia a [esta entrada de blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) para obtener más información y ver un vídeo sobre cómo resolver el problema.

Si experimenta algún problema o encontrar un error, debes informar en la [repositorio de Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
