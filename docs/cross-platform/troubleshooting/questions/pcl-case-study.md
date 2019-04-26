---
title: Resolver problemas relacionados con System.Diagnostics.Tracing y TPL Dataflow
description: 'Caso práctico de PCL: ¿Cómo se pueden resolver problemas relacionados con System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: d9aa85b946f20addb7d69c559bff68c6b1f75429
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342279"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Caso práctico de PCL: ¿Cómo se pueden resolver problemas relacionados con System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?

> [!IMPORTANT]
> Este ejemplo concreto de `System.Diagnostic.Tracing` ya no genera errores en las últimas versiones de Xamarin de forma predeterminada. Mientras la solución sugerida seguirá funcionando, tenga en cuenta que se han corregido algunos de los errores mencionados en la sección "niveles de errores".
> Además, debe tener en cuenta que .NET Standard ahora es la mejor manera de implementar las API de .NET multiplataforma.

## <a name="summary"></a>Resumen

Xamarin.iOS y Xamarin.Android no implementan el 100% de todos los perfiles PCL que permiten como referencias. Para mayor comodidad práctico en Visual Studio para Mac, Visual Studio y el Administrador de paquetes de NuGet, proyectos de Xamarin permiten el uso de varios perfiles que solo tienen _incompleta_ implementaciones. Por ejemplo, Xamarin.iOS ni Xamarin.Android actualmente incluye una implementación completa de los tipos de PCL "System.Diagnostics.Tracing" espacio de nombres. Esta limitación conduce a tres niveles de errores al intentar usar el valor predeterminado `portable-net45+win8+wpa81` versión del paquete NuGet de flujo de datos TPL de Microsoft.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solución: Cambiar el proyecto de aplicación para hacer referencia a la `portable-net45+win8+wp8+wpa81` versión de la biblioteca TPL Dataflow

(Esto evita los tres niveles de errores y funciona en todas las versiones recientes de Xamarin).

1. Abra el proyecto de aplicación **.csproj** archivo en un editor de texto.

2. Busque la línea que tiene un aspecto similar al siguiente:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Cambio `portable-net45+win8+wpa81` a `portable-net45+win8+wp8+wpa81` (`+wp8` se agrega):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explicación

El `portable-net45+win8+wp8+wpa81` no hace referencia a la versión de la biblioteca **System.Diagnostics.Tracing.dll** _en absoluto_, por lo que evita completamente los tres niveles de problemas.

### <a name="limitations"></a>Limitaciones

- El `portable-net45+win8+wp8+wpa81` versión de la biblioteca no puede incluir el 100% de la funcionalidad de la `portable-net45+win8+wpa81` versión.

- El Administrador de paquetes de NuGet instala el `portable-net45+win8+wpa81` versión del paquete NuGet de PCL de forma predeterminada, por lo que se debe ajustar manualmente la referencia.

## <a name="details-about-the-three-layers-of-errors"></a>Detalles sobre los tres niveles de errores

1. El **System.Diagnostics.Tracing.dll** ensamblado de fachada es actualmente no están presentes en todas las versiones de Mac de Xamarin.Android (error de no públicos 34888) y de ausencia de todas las versiones de Xamarin.iOS menores que 9.0 (o inferiores a XamarinVS 3.11.1443 en Windows) (se ha corregido en [error 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Este problema hará que uno de los siguientes errores según el destino de implementación y el vinculador de configuración:

    - Xamarin.Android.Common.targets: Error: Excepción al cargar los ensamblados: System.IO.FileNotFoundException: No se pudo cargar el ensamblado ' System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ". ¿Quizás no existe en la Mono para Android perfil?

    - No se pudo cargar el archivo o ensamblado 'System.Diagnostics.Tracing' o uno de sus dependencias. El sistema no puede encontrar el archivo especificado. (System.IO.FileNotFoundException)

    - MTOUCH: error MT3001: Podría no AOT el ensamblado ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH: error MT2002: No se pudo resolver el ensamblado: 'System.Diagnostics.Tracing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

2. Actual [implementación Mono de los tipos en "System.Diagnostics.Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) faltan algunas sobrecargas del método ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Este problema hará que uno de los siguientes errores del vinculador al compilar una aplicación de Xamarin:

    - / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: error: Error al ejecutar la tarea LinkAssemblies: error XA2006: Referencia a metadatos de elemento 'System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])' (definido en ' System.Threading.Tasks.Dataflow, Version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a') desde ' System.Threading.Tasks.Dataflow, versión = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a "no se pudo resolver.

    - MTOUCH: error MT2002: No se pudo resolver la referencia "System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])" desde "System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"

3. Actual [implementación Mono de los tipos en "System.Diagnostics.Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) también es actualmente un _vacía_ "ficticio" implementación ([error 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Cualquier intento de utilizar estos métodos en tiempo de ejecución, por tanto, podría producir resultados inesperados. Para el _determinado_ caso de la biblioteca de flujo de datos TPL de Microsoft, parece que las llamadas a `WriteEvent(System.Int32,System.Object[])` no son esenciales para la mayor parte del comportamiento de la biblioteca, por lo que la corrección de "nivel 2" ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), agregar vacío probablemente serán suficientes para la mayoría de los casos de uso de Microsoft TPL Dataflow implementaciones).

## <a name="questions--answers"></a>Preguntas y respuestas

### <a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>Pude dejar habilitada con la vinculación del <code>portable-net45+win8+wpa81</code> versión de la biblioteca en versiones anteriores de Xamarin.iOS o Xamarin.Android. ¿Cómo funciona?

#### <a name="answer"></a>Respuesta

Es _posible_ para obtener la compilación completado "correctamente" (con vinculación habilitado) en versiones anteriores de Xamarin.iOS o Xamarin.Android en Mac si incluye una referencia a la `System.Diagnostics.Tracing.dll` _delensambladodereferencia_ \[1\] en lugar de _ensamblado de fachada_ \[2], pero Desafortunadamente esto no es una solución "correcta". Los ensamblados de referencia solo están diseñados para utilizarse al crear _bibliotecas portátiles_, código específico de plataforma no igual que las aplicaciones. Intentando _ejecutar_ el código incluido en los ensamblados de referencia (en lugar de simplemente compilación en el mismo) es probable que genere resultados inesperados. La corrección correcta estará para que el equipo de Mono agregar el campo que falta `WriteEvent(System.Int32,System.Object[])` sobrecarga a la [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) tipo ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Ahora es la mejor opción Cambiar a la `portable-net45+win8+wp8+wpa81` versión de la biblioteca TPL Dataflow de Microsoft, como se describe en la sección de solución alternativa anterior.

(Para cualquier persona que puede leer este artículo después de ver una respuesta relacionada más antiguas y más sencillo de StackOverflow (<https://stackoverflow.com/a/23591322/2561894>), tenga en cuenta que la distinción entre los ensamblados de referencia y el ensamblado de fachada era _no_ mencionado no existe.)

**\[1\] ubicaciones "Referencia de ensamblado"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] ubicaciones "Ensamblado de fachada"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>¿Le ayudará a si agregar manualmente una referencia al ensamblado de fachada "System.Diagnostics.Tracing"?

_¿En particular se puede resolver el problema siguiendo estos 2 pasos?_

1. _Copia la `System.Diagnostics.Tracing.dll` ensamblado de fachada en la carpeta del proyecto de aplicación de una de las siguientes ubicaciones:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Agregue una referencia al ensamblado de fachada en el proyecto de aplicación de Xamarin.iOS o Xamarin.Android._

#### <a name="answer"></a>Respuesta

No, esto no le ayudará.

- Para Xamarin.iOS 9.0 o cualquier versión reciente de Xamarin.Android en Windows, esta solución alternativa es estrictamente redundante y podría provocar errores de compilación similar a "ya se ha importado un ensamblado 'System.Diagnostics.Tracing' con la misma identidad.".

- Para Xamarin.iOS 8.10 o inferior, o para Xamarin.Android en Mac, le ayudará esta solución pero _sólo_ para el problema del ensamblado que falta "layer1". Lo hará _no_ solucionar los errores del vinculador "layer2", por lo que no es una solución completa.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>¿Puedo usar el [paquete System.Diagnostics.Tracing NuGet](https://www.nuget.org/packages/System.Diagnostics.Tracing/) para resolver el problema?

#### <a name="answer"></a>Respuesta

No, el paquete de NuGet 3.0 "System.Diagnostics.Tracing" sólo incluye implementaciones específicas de plataforma para "DNXCore50" y "netcore50". Lo explícitamente _omite_ implementaciones para Xamarin.Android ("MonoAndroid") y Xamarin.iOS ("MonoTouch" y "xamarinios"). Esto significa que instalar el paquete tendrá _ningún efecto_ para proyectos de Xamarin.Android y Xamarin.iOS. El paquete de NuGet se da por supuesto que dichas plataformas ofrecen sus _propio_ implementación de los tipos. Esta suposición es "correcta" en el sentido de que tiene Mono _una_ del espacio de nombres, pero como hemos explicado en puntos de implementación \#2 y \#3 de "Detalles sobre los tres niveles de errores", anteriormente, el implementación es están incompleto. Por lo que será la corrección adecuada para que el equipo de Mono resolver [error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) y [error 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario.
