---
title: ¿Cómo se puede resolver un error PathTooLongException?
description: Este artículo explica cómo resolver un PathTooLongException que puede producirse durante la creación de una aplicación.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/20/2018
ms.openlocfilehash: f50ca3e738cb781f9c80e83f58f2e0fa1fa8e113
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>¿Cómo se puede resolver un error PathTooLongException?

## <a name="cause"></a>Motivo

Nombres de ruta de acceso generado en un proyecto Xamarin.Android pueden ser bastante largos.
Por ejemplo, se pudo generar una ruta de acceso similar al siguiente durante una compilación:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

En Windows (donde es la longitud máxima de una ruta de acceso [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** ha sido posible generar durante la compilación del proyecto si una ruta de acceso generado supera la longitud máxima. 

## <a name="fix"></a>Corregir

A partir de Xamarin.Android 8.0, la `UseShortFileNames` puede establecerse la propiedad de MSBuild para evitar este error. Cuando esta propiedad se establece en `True` (el valor predeterminado es `False`), el proceso de compilación usa nombres de ruta de acceso más cortos para reducir la probabilidad de generar un **PathTooLongException**.
Por ejemplo, cuando `UseShortFileNames` se establece en `True`, se acorta la ruta de acceso anterior a la ruta de acceso que es similar al siguiente:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\lp\\1\\jl\\assets**

Para establecer esta propiedad, agregue la siguiente propiedad de MSBuild para el proyecto **.csproj** archivo:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Para obtener más información acerca de cómo establecer las propiedades de compilación, consulte [proceso de compilación](~/android/deploy-test/building-apps/build-process.md).
