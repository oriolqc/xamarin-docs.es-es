---
title: "Más inteligente Xamarin para Android admite v4 / paquetes de NuGet v13"
ms.topic: article
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: ab5ec19498b3c61e988adc959e1751b96f60210d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Más inteligente Xamarin para Android admite v4 / paquetes de NuGet v13

## <a name="about-the-android-support-libraries"></a>Acerca de las bibliotecas de compatibilidad con Android

Google ha creado las bibliotecas de soporte técnico para realizar nuevas características disponibles en versiones anteriores de Android. En general, compatibilidad con bibliotecas tienen un número de versión en su nombre, que es el nivel más bajo de API de Android que son compatibles con (p. ej.: compatibilidad con-v4 solo pueden usarse en la API de nivel 4 y versiones posteriores. Para obtener más información en este [discusión de desbordamiento de la pila](http://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Dos de las bibliotecas de soporte técnico: `Support-v4` y `Support-v13` no pueden usarse juntas en la misma aplicación, es decir, son mutuamente excluyentes. Esto es porque `Support-v13` realmente contiene todos los tipos y la implementación de `Support-v4`. Si intenta hacer referencia tanto en el mismo proyecto se producirán errores de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas con el de referencia

Puesto que `Support-v4` ha hecho tan popular, muchas bibliotecas de fabricantes 3rd ahora dependen de él. Podría haber elegido dependen v13 de soporte técnico en su lugar, pero es más común que depender _v4_ porque proporciona a las aplicaciones de uso de estas bibliotecas parte 3ª la opción de admitir niveles de API hasta 4.

Si hace referencia a una biblioteca de fabricante 3rd Xamarin el `Xamarin.Android.Support.v4.dll` enlazar a `Support-v4`, también debe hacer referencia a cualquier aplicación que usa esta biblioteca `Xamarin.Android.Support.v4.dll`. Esto es un problema cuando la misma aplicación también desea utilizar algunas de las funciones de la `Xamarin.Android.Support.v13.dll` enlazar a `Support-v13`. Si hace referencia a ambos enlaces, se producirán errores de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>Ensamblado de enlace de tipo reenviado v4

Para solucionar este problema, hemos creado una clase especial `Xamarin.Android.Support.v4.dll` ensamblado que no tiene ninguna implementación, sino simplemente `[assembly: TypeForwardedTo (..)]` atributos que reenvían todos los `Support-v4` tipos a la implementación en la `Xamarin.Android.Support.v13.dll` ensamblado.

Esto significa que un desarrollador puede hacer referencia a él _reenvían_ ensamblado en su aplicación que cumplirá la referencia a `Xamarin.Android.Support.v4.dll` por cualquier biblioteca parte 3ª, mientras sigue permitiendo `Xamarin.Android.Support.v13.dll` para su uso en la aplicación.

## <a name="nuget-assistance"></a>Asistencia de NuGet

Mientras que un desarrollador puede agregar manualmente las referencias correctas necesarias, se pueden usar NuGet para ayudar a elegir el ensamblado adecuado (ya sea la normal _v4_ enlace o tipo reenviado _v4_ ensamblado) cuando el paquete de NuGet está instalado.

Por lo tanto, el `Xamarin.Android.Support.v4` paquete de NuGet ahora contiene la lógica siguiente:

Si la aplicación está destinada a 13 de nivel de API (Monigote 3.2) o superior:

*   `Xamarin.Android.Support.v13` NuGet se agrega automáticamente como una dependencia
*   El _reenvían_ `Xamarin.Android.Support.v4.dll` se hará referencia en el proyecto

Si la aplicación tiene como destino cualquier cosa inferior a 13 de nivel de API, obtendrá la normal `Xamarin.Android.Support.v4.dll` enlace al que hace referencia en el proyecto.

## <a name="do-i-have-to-use-support-v13"></a>¿Tengo que usar v13 de soporte técnico?

Si la aplicación tiene como destino el nivel de API 13 o superior y si decide usar el `Xamarin Android Support-v4` paquete de NuGet, la `Xamarin Android Support v13` paquete NuGet es una dependencia necesaria.

Creemos que es el aumento de tamaño de la aplicación (los dos archivos .jar diferencian KB 17) muy pequeño merece la pena la compatibilidad y dolores de cabeza menos da como resultado.

Si estás firme con sobre el uso de `Support-v4` en una aplicación que tiene como destino 13 de nivel de API o versiones posteriores, puede descargar manualmente siempre la `.nupkg`, extráigalo y hacer referencia al ensamblado.
