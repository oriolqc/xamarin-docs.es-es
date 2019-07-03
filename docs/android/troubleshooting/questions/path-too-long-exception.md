---
title: ¿Cómo se puede resolver un error de PathTooLongException?
description: En este artículo se explica cómo resolver una excepción PathTooLongException que puede producirse durante la compilación de una aplicación.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 443c3cc742ceb919e64a781e18c5a97c342abb44
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522924"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>¿Cómo se puede resolver un error de PathTooLongException?

## <a name="cause"></a>Motivo

Los nombres de ruta de acceso generada en un proyecto de Xamarin.Android pueden ser bastante largos.
Por ejemplo, durante una compilación se pudo generar una ruta de acceso similar al siguiente:

**C:\\algunos\\Directory\\solución\\proyecto\\obj\\depurar\\__library_projects__ \\ Xamarin.Forms.Platform.Android\\library_project_imports\\activos**

En Windows (donde es la longitud máxima de una ruta de acceso [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** podría generarse durante la compilación del proyecto si una ruta de acceso generado supera la longitud máxima. 

## <a name="fix"></a>Solución

El `UseShortFileNames` propiedad de MSBuild está establecida en `True` para evitar este error de forma predeterminada. Cuando esta propiedad se establece en `True`, el proceso de compilación usa nombres de ruta de acceso más cortos para reducir la probabilidad de generar un **PathTooLongException**.
Por ejemplo, cuando `UseShortFileNames` está establecido en `True`, la ruta de acceso anterior se ha reducido a la ruta de acceso que es similar al siguiente:

**C:\\algunos\\Directory\\solución\\proyecto\\obj\\depurar\\lp\\1\\jl\\activos**

Para establecer esta propiedad manualmente, agregue la siguiente propiedad de MSBuild para el proyecto **.csproj** archivo:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si se establece esta marca no se soluciona el **PathTooLongException** error, otro enfoque consiste en especificar un [raíz de salida intermedio común](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) para proyectos de la solución estableciendo `IntermediateOutputPath` en el proyecto **.csproj** archivo. Intente usar una ruta de acceso relativamente corto. Por ejemplo:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Para obtener más información sobre cómo establecer las propiedades de compilación, véase [proceso de compilación](~/android/deploy-test/building-apps/build-process.md).
