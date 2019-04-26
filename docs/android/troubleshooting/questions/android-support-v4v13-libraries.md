---
title: Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: a990d933c258812b2b3d3374fb6435af06f729ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227057"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13

## <a name="about-the-android-support-libraries"></a>Acerca de las bibliotecas de compatibilidad con Android

Google ha creado bibliotecas de soporte técnico para que las nuevas características disponibles para las versiones anteriores de Android. En general, las bibliotecas de compatibilidad tienen un número de versión en su nombre, que es el nivel más bajo de API Android son compatibles con (p. ej.: Soporte técnico-v4 sólo puede utilizarse en la API de nivel 4 y versiones posteriores. Obtener más información en este [debate de Stack Overflow](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Dos de las bibliotecas de compatibilidad: `Support-v4` y `Support-v13` no pueden usarse juntos en la misma aplicación, es decir, son mutuamente excluyentes. Esto es porque `Support-v13` realmente contiene todos los tipos y la implementación de `Support-v4`. Si intenta hacer referencia tanto en el mismo proyecto producirá errores de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas con la que hacen referencia a

Puesto que `Support-v4` ha vuelto tan popular, mucha de bibliotecas de terceros 3rd ahora dependen de él. Podría haber elegido a depender v13 de soporte técnico en su lugar, pero es más común que depender de _v4_ porque ofrece a las aplicaciones con estas bibliotecas de terceros 3rd la opción de admitir los niveles de API hasta 4.

Si hace referencia una biblioteca de terceros 3rd Xamarin el `Xamarin.Android.Support.v4.dll` enlazar a `Support-v4`, también debe hacer referencia a cualquier aplicación que usa esta biblioteca `Xamarin.Android.Support.v4.dll`. Esto se convierte en un problema cuando la misma aplicación también quiera usar algunas de las funcionalidades de la `Xamarin.Android.Support.v13.dll` enlazar a `Support-v13`. Si hace referencia a ambos enlaces, se producirán errores de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>Ensamblado de enlace de tipo reenviado v4

Para solucionar este problema, hemos creado un especial `Xamarin.Android.Support.v4.dll` ensamblado que no tiene ninguna implementación, sino simplemente `[assembly: TypeForwardedTo (..)]` atributos que reenvían todos los `Support-v4` tipos a la implementación dentro de la `Xamarin.Android.Support.v13.dll` ensamblado.

Esto significa que un desarrollador puede hacer referencia a esto _tipo reenviado_ en sus aplicaciones que cumplirán la referencia al ensamblado de `Xamarin.Android.Support.v4.dll` por las bibliotecas de terceros 3rd, mientras sigue permitiendo `Xamarin.Android.Support.v13.dll` para usarse en la aplicación.

## <a name="nuget-assistance"></a>Asistencia de NuGet

Mientras que un desarrollador puede agregar manualmente referencias correctas necesaria, se pueden usar NuGet para ayudarle a elegir el ensamblado adecuado (ya sea el valor normal _v4_ enlace o el tipo reenviado _v4_ ensamblado) cuando el paquete de NuGet está instalado.

Por lo tanto, el `Xamarin.Android.Support.v4` paquete NuGet ahora contiene la lógica siguiente:

Si la aplicación está destinado a nivel de API 13 (Monigote 3.2) o superior:

*   `Xamarin.Android.Support.v13` NuGet se agregará automáticamente como una dependencia
*   El _tipo reenviado_ `Xamarin.Android.Support.v4.dll` se hará referencia en el proyecto

Si la aplicación está destinada a algo inferior a la API de nivel de 13, obtendrá el valor normal `Xamarin.Android.Support.v4.dll` enlace hace referenciado en el proyecto.

## <a name="do-i-have-to-use-support-v13"></a>¿Tengo que usar v13 de soporte técnico?

Si su aplicación está destinada a nivel de API 13 o superior y opta por usar el `Xamarin Android Support-v4` paquete NuGet, el `Xamarin Android Support v13` paquete NuGet es una dependencia necesaria.

Creemos que es el aumento de tamaño de la aplicación (los dos archivos .jar se diferencian en 17kb) menor que merece la pena la compatibilidad y menos dolores de cabeza da como resultado.

Si estás tanto sobre el uso de `Support-v4` en una aplicación que tiene como destino la API de nivel de 13 o superior, puede descargar manualmente siempre la `.nupkg`, extráigalo y hacer referencia al ensamblado.
