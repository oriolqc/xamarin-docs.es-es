---
title: Uso compartido de información general del código
description: 'Este documento compara los distintos métodos para compartir código entre proyectos multiplataforma: proyectos compartidos, bibliotecas de clases portables y .NET Standard, incluyendo las ventajas y desventajas de cada uno.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: conceptdev
ms.author: crdun
ms.date: 08/06/2018
ms.openlocfilehash: 98b5786ae4f071b4d8e8f854561db97aee037fdc
ms.sourcegitcommit: aa7b0182d117e2af66ffaa4fa29b8c214ceecae1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520290"
---
# <a name="sharing-code-overview"></a>Uso compartido de información general del código

_Este documento compara los distintos métodos para compartir código entre proyectos multiplataforma: .NET Standard, proyectos compartidos y las bibliotecas de clases portables, incluyendo las ventajas y desventajas de cada uno._

Hay tres métodos para compartir código entre aplicaciones multiplataforma:

- [**Bibliotecas de .NET standard** ](#Net_Standard) – proyectos de .NET Standard puede implementar el código compartido entre varias plataformas y puede tener acceso a un gran número de las API de .NET (según la versión). .NET standard 1.0-1.6 implementar progresivamente más grandes conjuntos de API, mientras que .NET Standard 2.0 proporciona la mejor cobertura de la BCL de .NET (incluidas las API de .NET disponible en las aplicaciones de Xamarin).
- [**Proyectos compartidos** ](#Shared_Projects) : usar el tipo de proyecto de activos compartidos para organizar el código fuente y usar `#if` directivas de compilador según sea necesario para administrar los requisitos específicos de la plataforma.
- [**Bibliotecas de clases portables** ](#Portable_Class_Libraries) (en desuso): bibliotecas de clases portables (PCL) puede tener como destino varias plataformas con una superficie de API común y usar Interfaces para proporcionar funcionalidad específica de la plataforma. Las PCL están en desuso en las versiones más recientes de Visual Studio &ndash; usar .NET Standard en su lugar.

El objetivo de una estrategia de uso compartido de código es compatible con la arquitectura que se muestra en este diagrama, donde un código base único puede ser utilizado por varias plataformas.

 ![Arquitectura de aplicación de código compartida](code-sharing-images/conceptualarchitecture.png "comparten la arquitectura de aplicaciones de código")

Este artículo comparan los métodos disponibles para ayudarle a elegir el tipo de proyecto adecuada para sus aplicaciones.

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Bibliotecas estándar de .NET

[.NET standard](~/cross-platform/app-fundamentals/net-standard.md) bibliotecas proporcionan un conjunto bien definido de las bibliotecas de clases base que puede hacer referencia a diferentes tipos de proyectos, incluidos los proyectos multiplataforma, como Xamarin.Android y Xamarin.iOS. .NET standard 2.0 se recomienda para lograr la máxima compatibilidad con el código existente de .NET Framework.

![Diagrama de .NET standard](code-sharing-images/netstandard.png "diagrama .NET Standard")

### <a name="benefits"></a>Ventajas

- Le permite compartir código entre varios proyectos.
- Las operaciones de refactorización actualización siempre todas las referencias.
- Una mayor área expuesta de la biblioteca de clases de Base (BCL) de .NET está disponible a los perfiles de PCL. En concreto, .NET Standard 2.0 tiene casi la misma superficie de API de .NET Framework y se recomienda para nuevas aplicaciones y trasladar las PCL existentes.

### <a name="disadvantages"></a>Desventajas

- No se puede usar directivas de compilador como `#if __IOS__`.

### <a name="remarks"></a>Comentarios

.NET standard es [similar a PCL](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries), pero con un modelo más sencillo para la compatibilidad con la plataforma y un mayor número de clases a partir de la BCL.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Proyectos compartidos

[Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) contienen los archivos de código y los recursos que se incluyen en cualquier proyecto que hace referencia a ellos. Proyectos de recurso compartido no producen una salida compilada por sí solos.

Esta captura de pantalla muestra un archivo de solución que contiene tres proyectos de aplicación (para Android, iOS y Windows), con un **Shared** proyecto que contiene common archivos de código de fuente de C#:

![Compartir soluciones project](code-sharing-images/sharedsolution.png "compartido soluciones project")

En el diagrama siguiente, donde cada proyecto incluye todos los archivos de código fuente compartido, se muestra la arquitectura conceptual:

![Diagrama del proyecto compartido](code-sharing-images/sharedassetproject.png "diagrama del proyecto compartido")

### <a name="example"></a>Ejemplo

Una aplicación multiplataforma que admite iOS, Android y Windows requeriría un proyecto de aplicación para cada plataforma. El código común se encuentra en el proyecto compartido.

Una solución de ejemplo contendría las carpetas y los proyectos (los nombres de proyecto elegidas para expresividad, los proyectos no tienen que seguir estas directrices de nomenclatura) siguientes:

- **Shared** : el proyecto compartido que contiene el código común a todos los proyectos.
- **AppAndroid** : proyecto de aplicación de Xamarin.Android.
- **AppiOS** : proyecto de aplicación de Xamarin.iOS.
- **AppWindows** : proyecto de aplicación de Windows.

De esta forma los proyectos de aplicación de tres comparten el mismo código fuente (los archivos de C# compartido). Las modificaciones en el código compartido se compartirá entre los tres proyectos.

### <a name="benefits"></a>Ventajas

- Le permite compartir código entre varios proyectos.
- Código compartido se puede bifurcar en función de la plataforma mediante las directivas de compilador (p ej. uso de `#if __ANDROID__` , como se describe en el [Building Cross Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento).
- Proyectos de aplicación pueden incluir referencias específicas de la plataforma que puede usar el código compartido (como el uso de `Community.CsharpSqlite.WP7` en el ejemplo Tasky para Windows Phone).

### <a name="disadvantages"></a>Desventajas

- Refactorizaciones que afectan al código dentro de las directivas de compilador 'inactive' no actualizarán el código dentro de esas directivas.
- A diferencia de la mayoría de tipos de proyecto, un proyecto compartido no tiene ningún ensamblado de 'salida'. Durante la compilación, los archivos se tratan como parte del proyecto de referencia y compilados en dicho ensamblado. Si desea compartir el código como un ensamblado, a continuación, .NET Standard o bibliotecas de clases portables son una solución mejor.

<a name="Shared_Remarks" />

### <a name="remarks"></a>Comentarios

Una buena solución para los desarrolladores de aplicaciones para escribir código que está pensado solo para uso compartido en su aplicación (y no distribuirlo a otros desarrolladores).

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

> [!TIP]
> Se recomiendan las bibliotecas .NET standard 2.0 en lugar de las bibliotecas de clases portables.

Las bibliotecas de clases Portable [explica con detalle aquí](~/cross-platform/app-fundamentals/pcl.md).

![Diagrama de la biblioteca de clases Portable](code-sharing-images/portableclasslibrary.png "diagrama de la biblioteca de clases Portable")

### <a name="benefits"></a>Ventajas

- Le permite compartir código entre varios proyectos.
- Las operaciones de refactorización actualización siempre todas las referencias.

### <a name="disadvantages"></a>Desventajas

- En desuso en las versiones más recientes de Visual Studio, se recomiendan en su lugar, bibliotecas de .NET Standard. Consulte esta [explicación de las diferencias](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries) entre PCL y .NET Standard.
- No se puede usar directivas de compilador.
- Solo un subconjunto de .NET framework está disponible para su uso, determinado por el perfil seleccionado (consulte la [Introducción a la PCL](~/cross-platform/app-fundamentals/pcl.md) para obtener más información).

### <a name="remarks"></a>Comentarios

Plantilla de PCL se considera en desuso en las versiones más recientes de Visual Studio.

## <a name="summary"></a>Resumen

El código de uso compartido de estrategia que elija está determinado por las plataformas de que destino. Elija un método que funciona mejor para su proyecto.

.NET standard es la mejor opción para la creación de bibliotecas de código que se pueden compartir (especialmente publicación en NuGet). Proyectos compartidos funcionan bien para los desarrolladores de aplicaciones que piensa usar una gran cantidad de funcionalidad específica de plataforma en sus aplicaciones multiplataforma.

Mientras que los proyectos PCL siguen siendo compatibles en Visual Studio, .NET Standard se recomienda para nuevos proyectos.

## <a name="related-links"></a>Vínculos relacionados

- [Building Cross Platform Applications (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)
- [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Caso práctico: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Ejemplo tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Ejemplo tasky con PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
