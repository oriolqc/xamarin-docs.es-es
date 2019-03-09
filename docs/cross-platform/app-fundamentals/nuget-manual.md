---
title: Crear manualmente los paquetes de NuGet para Xamarin
description: Este documento contiene sugerencias para ayudar a crear paquetes de NuGet que tienen como destino la plataforma Xamarin. Se describen los perfiles de Xamarin de paquete de NuGet, paquetes de NuGet de PCL con las dependencias de plataforma y se vincula a varios ejemplos de código abierto.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 4f4ca327479a7f4eb4a7dc7feafdd71291c1b7fe
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671694"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Crear manualmente los paquetes de NuGet para Xamarin

_Esta página contiene algunas sugerencias para ayudar a crear paquetes de NuGet que tienen como destino la plataforma Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (y Visual Studio para Mac) incluyen la capacidad de _automáticamente_ generar paquetes de NuGet de PCL, .NET Standard o proyectos compartidos. Hacer referencia a la [bibliotecas multiplataforma para compartir código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) guía para obtener más detalles.

## <a name="nuget-package-xamarin-profiles"></a>Perfiles de Xamarin de paquete de NuGet

El sitio Web de NuGet [que admiten varias versiones de .NET Framework y perfiles](https://docs.nuget.org/create/enforced-package-conventions) describe cómo admitir diferentes marcos de trabajo de Microsoft y perfiles, pero no incluye .NET framework de destino utilizado por Xamarin.

Los marcos de destino principales de Xamarin en uso hoy en día son:

* **MonoAndroid** - Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS [Unified API](~/cross-platform/macios/unified/index.md) (es compatible con 64 bits)
* **Xamarin.Mac** -perfil móvil de Xamarin.Mac, que es equivalente a la superficie de API de Xamarin.Android y Xamarin.iOS.

También hay un destino para iOS anterior [API clásica](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -API clásica de iOS

Un **.nuspec** archivo destinados a todos estos sería algo así:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

Lo anterior, omite cualquier biblioteca de clases portable.

La mayoría **.nuspec** archivos especifican el número de versión de .NET framework de destino, pero es opcional si el ensamblado funciona con todas las versiones de ese marco de destino. Por tanto, si su objetivo era **lib\MonoAndroid** esto significaría funciona con cualquier versión de Xamarin.Android.

Puede especificar la versión con un conjunto de números sin un punto decimal o puede especificarlo mediante puntos decimales. Sin separador decimal NuGet simplemente tomar cada número y convertirlo en una versión mediante la inserción de un '.' entre cada dígito.

En el MonoAndroid10"anterior" significa "Android 1.0". Esto significa simplemente que el proyecto [.NET framework de destino](~/android/app-fundamentals/android-api-levels.md) debe ser MonoAndroid versión 1.0 o superior. La versión se especifica en el `<TargetFrameworkVersion>` elemento en el archivo de proyecto.

Para aclarar:

- **MonoAndroid403** coincide con Android 4.0.3 y versiones más recientes (es decir nivel de API 15)
- **Xamarin.iOS10** coincide con Xamarin.iOS 1.0 y versiones más recientes
- **Xamarin.iOS1.0** también coincide con Xamarin.iOS 1.0 y versiones más recientes

## <a name="pcl-nugets-with-platform-dependencies"></a>Paquetes de NuGet PCL con las dependencias de plataforma

Los perfiles de PCL están limitados en lo que puede tener acceso a las API de .NET framework, y ciertamente no puedan acceder a código específico de plataforma. Estos vínculos 3rd-party describen diferentes enfoques para crear paquetes de NuGet que usan PCL y las API nativas para proporcionar compatibilidad para Xamarin y otras plataformas:

- [Cómo hacer que el trabajo de las bibliotecas de clases Portable para usted](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [El truco PCL gancho](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Crear una PCL NuGet que funciona con Xamarin.iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Esta externo [lista de perfiles de PCL con su nombre de destino de NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) también es una referencia útil.

## <a name="examples"></a>Ejemplos

Algunos ejemplos de código abierto que se pueden consultar:

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) : escribir la aplicación mediante System.Net.Http, pero quite esta biblioteca y pasará considerablemente más rápido (vista [origen](https://github.com/paulcbetts/ModernHttpClient)).
- [**Asterisco** ](https://www.nuget.org/packages/Splat/) : una biblioteca para que las cosas y multiplataforma que se debe (vista [origen](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -una biblioteca multiplataforma para representar gráficos vectoriales en .NET (vista [origen](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Vínculos relacionados

- [Nugetizer 3000 automatizada de creación de Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [Actualización de paquetes NuGet para iOS de 64 bits](https://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [Incluir NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
