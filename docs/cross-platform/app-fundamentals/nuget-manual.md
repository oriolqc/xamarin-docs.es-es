---
title: Creación manual de paquetes de NuGet para Xamarin
description: Este documento contiene sugerencias para ayudarle a crear paquetes de NuGet que tienen como destino la plataforma Xamarin. Se describen los perfiles de Xamarin de paquete de NuGet, PCL NuGets con dependencias de plataforma y se vincula a varios ejemplos de código abierto.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 2adfe70e1d37c7f6e6fc825de1d86513de4a985c
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34845972"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Creación manual de paquetes de NuGet para Xamarin

_Esta página contiene algunas sugerencias para ayudarle a crear paquetes de NuGet que tienen como destino la plataforma Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (y Visual Studio para Mac) incluyen la capacidad de _automáticamente_ generar paquetes de NuGet de PCL, estándar de .NET o proyectos compartidos. Hacer referencia a la [bibliotecas multiplataforma para uso compartido de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) guía para obtener más detalles.

## <a name="nuget-package-xamarin-profiles"></a>Perfiles de Xamarin de paquete de NuGet

El sitio Web de NuGet [compatibilidad con varias versiones de .NET Framework y perfiles](https://docs.nuget.org/create/enforced-package-conventions) describe cómo admitir diferentes marcos de Microsoft y perfiles, pero no incluye los nombres de framework de destino utilizados por Xamarin.

Hoy en día son los principales marcos de destino de Xamarin en uso:

* **MonoAndroid** -Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS [API unificada](~/cross-platform/macios/unified/index.md) (es compatible con 64 bits)
* **Xamarin.Mac** -perfil móvil del Xamarin.Mac, que es equivalente a la superficie Xamarin.iOS y Xamarin.Android API.

También hay un destino en el programa iOS anterior [API clásico](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -API clásica de iOS

A **.nuspec** archivo destinados a todos estos sería algo parecido:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

Los pasos anteriores, se pasa por alto cualquier biblioteca de clases portable.

La mayoría **.nuspec** archivos especifican el número de versión de .NET framework de destino, pero es opcional si el ensamblado funciona con todas las versiones de ese marco de trabajo de destino. Por tanto, si el destino era **lib\MonoAndroid** esto significaría funciona con cualquier versión de Xamarin.Android.

Puede especificar la versión con un conjunto de números sin un punto decimal o bien, puede especificar con decimales. Sin punto decimal NuGet simplemente tomar cada número y convertirlo en una versión insertando un '.' entre cada dígito.

En el MonoAndroid10"anterior" significa "Android 1.0". Esto significa que solo el proyecto [.NET framework de destino](~/android/app-fundamentals/android-api-levels.md) debe ser MonoAndroid versión 1.0 o posterior. La versión se especifica en el `<TargetFrameworkVersion>` elemento en el archivo de proyecto.

Para aclarar:

- **MonoAndroid403** coincide con Android 4.0.3 y versiones más recientes (es decir nivel de API 15)
- **Xamarin.iOS10** coincide con Xamarin.iOS 1.0 y versiones más recientes
- **Xamarin.iOS1.0** también coincide con Xamarin.iOS 1.0 y versiones más recientes

## <a name="pcl-nugets-with-platform-dependencies"></a>PCL NuGets con las dependencias de plataforma

PCL perfiles están limitados en qué API pueden tener acceso de .NET framework, y por supuesto, no pueden tener acceso a código específico de la plataforma. Estos vínculos 3rd terceros explican distintos enfoques para crear paquetes de NuGet que usan PCL y las API nativas para proporcionar compatibilidad para otras plataformas y Xamarin:

- [Cómo hacer su trabajo de las bibliotecas de clases portables](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [El truco PCL gancho](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Crear una PCL NuGet que funciona con Xamarin.iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Esta externo [lista de perfiles de PCL con su nombre de destino de NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) también es una referencia útil.

## <a name="examples"></a>Ejemplos

Algunos ejemplos de código abierto que se pueden consultar para:

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) : escribir una aplicación que use System.Net.Http pero quitar esta biblioteca en y se desplazará considerablemente más rápido (vista [origen](https://github.com/paulcbetts/ModernHttpClient)).
- [**Lámina** ](https://www.nuget.org/packages/Splat/) : una biblioteca para facilitar su multiplataforma que se debe (vista [origen](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -una biblioteca multiplataforma para representar gráficos vectoriales en .NET (vista [origen](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Vínculos relacionados

- [3000 Nugetizer automatizada de creación de Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [Actualizando NuGets de 64 bits de iOS](http://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [Incluir un NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
