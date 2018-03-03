---
title: "Caso práctico de PCL: ¿Cómo puedo resolver problemas relacionados a System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: c1d8bab1af8082d74f447cd51422a7eedb7c18c4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Caso práctico de PCL: ¿Cómo puedo resolver problemas relacionados a System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?

## <a name="summary"></a>Resumen

Xamarin.iOS y Xamarin.Android no implementan el 100% de todos los perfiles PCL permiten como referencias. Por comodidad resulta muy práctico en Visual Studio para Mac, Visual Studio y el Administrador de paquetes de NuGet, proyectos de Xamarin permiten el uso de diversos perfiles que solo tienen _incompleta_ implementaciones. Por ejemplo, Xamarin.iOS ni Xamarin.Android actualmente incluye una implementación completa de los tipos de PCL "System.Diagnostics.Tracing" espacio de nombres. Esta limitación conduce a 3 niveles de errores al intentar usar el valor predeterminado `portable-net45+win8+wpa81` versión del paquete NuGet de flujo de datos de Microsoft TPL.


## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solución alternativa: Cambiar el proyecto de aplicación para hacer referencia a la `portable-net45+win8+wp8+wpa81` versión de la biblioteca de flujo de datos TPL

(Esto evita que todas las 3 capas de errores y funciona en todas las versiones recientes de Xamarin).

1. Abra el proyecto de aplicación `.csproj` archivo en un editor de texto.

2. Busque la línea que tiene un aspecto similar al siguiente:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Cambie `portable-net45+win8+wpa81` a `portable-net45+win8+**wp8**+wpa81`:

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explicación

El `portable-net45+win8+wp8+wpa81` versión de la biblioteca no hace referencia a "System.Diagnostics.Tracing.dll" _en absoluto_, por lo que evita completamente todas las 3 capas de problemas.

### <a name="limitations"></a>Limitaciones

- El `portable-net45+win8+wp8+wpa81` versión de la biblioteca no puede incluir el 100% de la funcionalidad de la `portable-net45+win8+wpa81` versión.

- El Administrador de paquetes de NuGet instala el `portable-net45+win8+wpa81` versión del paquete NuGet de PCL de forma predeterminada, por lo que se debe ajustar manualmente la referencia.




## <a name="details-about-the-3-layers-of-errors"></a>Obtener más información acerca de los 3 niveles de errores

1. El `System.Diagnostics.Tracing.dll` ensamblado fachada está actualmente presentes en todas las versiones de Mac de Xamarin.Android (error de no públicos 34888) y no están presentes en todas las versiones de Xamarin.iOS menores que 9.0 (o inferiores a XamarinVS 3.11.1443 en Windows) (corregidos en [error 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Este problema hará que uno de los siguientes errores según el destino de implementación y el vinculador de configuración:

    - > Xamarin.Android.Common.targets: Error: excepción al cargar los ensamblados: System.IO.FileNotFoundException: no se pudo cargar el ensamblado ' System.Diagnostics.Tracing, versión = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ". ¿Quizás no existe en el Mono para Android perfil?

    - > No se pudo cargar el archivo o ensamblado 'System.Diagnostics.Tracing' o uno de sus dependencias. El sistema no puede encontrar el archivo especificado. (System.IO.FileNotFoundException)

    - > MTOUCH: error MT3001: podría no AOT el ensamblado ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - > MTOUCH: error MT2002: no se pudo resolver el ensamblado: ' System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a "



2. Actual [implementación Mono de los tipos de "System.Diagnostics.Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) falta algunas sobrecargas de método ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Este problema hará que uno de los siguientes errores del vinculador al compilar una aplicación de Xamarin:

    - > / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: error: Error al ejecutar la tarea LinkAssemblies: error XA2006: referencia al elemento de metadatos ' System.Void System.Diagnostics.Tracing.EventSource:: WriteEvent(System.Int32,System.Object[])' (definido en ' System.Threading.Tasks.Dataflow, Version = 4.5.24.0, referencia cultural = neutral, PublicKeyToken = b03f5f7f11d50a3a ") de ' System.Threading.Tasks.Dataflow, Version = 4.5.24.0, referencia cultural = neutral, PublicKeyToken = b03f5f7f11d50a3a "no se pudo resolver.

    - > MTOUCH: error MT2002: no se pudo resolver la referencia "System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])" desde "System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"


3. Actual [implementación Mono de los tipos de "System.Diagnostics.Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) también es actualmente un _vacía_ "ficticio" implementación ([error 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Cualquier intento de usar estos métodos en tiempo de ejecución, por tanto, podría producir resultados inesperados. Para el _determinado_ caso de la biblioteca de flujo de datos de Microsoft TPL, parece que las llamadas a `WriteEvent(System.Int32,System.Object[])` no son esenciales para la mayor parte del comportamiento de la biblioteca, por lo que la corrección de "nivel 2" ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), agregar vacía implementaciones) le likey sea suficiente para la mayoría de los casos de uso de flujo de datos de Microsoft TPL.




## <a name="questions--answers"></a>Preguntas y respuestas


###<a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>"Pude dejar habilitada con la vinculación el <code>portable-net45+win8+wpa81</code> versión de la biblioteca en versiones anteriores de Xamarin.iOS o en Xamarin.Android. ¿Cómo que funcionó?"

#### <a name="answer"></a>Respuesta

Es _posibles_ para obtener la compilación completada "correctamente" (con la vinculación habilitado) en versiones anteriores de Xamarin.iOS o en Xamarin.Android en Mac si incluye una referencia a la `System.Diagnostics.Tracing.dll` _hacen referencia a ensamblado_ \[1\] en lugar de la _ensamblado fachada_ \[2], pero lamentablemente no es una solución "correcta". Ensamblados de referencia solo están previstos para usarlas para generar _bibliotecas portables_, código no específica de la plataforma como las aplicaciones. Intentando _ejecutar_ el código incluido en los ensamblados de referencia (en lugar de simplemente compilación en el mismo) es probable que den resultados inesperados. Será la solución adecuada para que el equipo de Mono agregar el carácter que falta `WriteEvent(System.Int32,System.Object[])` sobrecarga a la [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) tipo ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). De ahora es la mejor opción Cambiar a la `portable-net45+win8+wp8+wpa81` versión de la biblioteca de flujo de datos de Microsoft TPL tal como se describe en la sección de solución anterior.

(Para cualquier persona que puede leer este artículo después de ver una respuesta más antiguo y más breve relacionada de StackOverflow (<http://stackoverflow.com/a/23591322/2561894>), tenga en cuenta que la diferencia entre los ensamblados de referencia y ensamblados de fachada era _no_ mencionado no existe.)


**\[1\] ubicaciones "Referencia de ensamblado"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] ubicaciones "Ensamblado fachada"**

Windows: <code>C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\<strong>Facades</strong>\System.Diagnostics.Tracing.dll</code>

Mac (Mono): <code>/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/<strong>Facades</strong>/System.Diagnostics.Tracing.dll</code>


###<a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>"Lo ayudará a si se agregar manualmente una referencia al ensamblado de fachada"System.Diagnostics.Tracing"?"

¿Puedo en particular se puede resolver el problema siguiendo estos 2 pasos?

1. Copia la `System.Diagnostics.Tracing.dll` ensamblado de fachada en la carpeta de proyecto de aplicación de una de las siguientes ubicaciones:

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. Agregue una referencia al ensamblado de fachada en el proyecto de aplicación Xamarin.iOS o Xamarin.Android.

#### <a name="answer"></a>Respuesta

No, esto no le ayudará.

- Para Xamarin.iOS 9.0 o cualquier versión reciente de Xamarin.Android en Windows, esta solución alternativa es estrictamente redundante y puede provocar errores de compilación similar a "ya se importó un ensamblado 'System.Diagnostics.Tracing' con la misma identidad.".

- Para Xamarin.iOS 8.10 o hacia abajo o Xamarin.Android en Mac, esta solución le ayudará a pero _sólo_ para el problema del ensamblado que falta "capa 1". Hará lo siguiente _no_ resolver los errores del vinculador "layer2", por lo que no es una solución completa.


###"¿Puedo usar la <a href="https://www.nuget.org/packages/System.Diagnostics.Tracing/">paquete System.Diagnostics.Tracing NuGet</a> para resolver el problema?"

#### <a name="answer"></a>Respuesta

No, el paquete de NuGet 3.0 "System.Diagnostics.Tracing" solo incluye implementaciones específicas de la plataforma para "DNXCore50" y "netcore50". Lo explícitamente _omite_ implementaciones de Xamarin.Android ("MonoAndroid") y Xamarin.iOS ("MonoTouch" y "xamarinios"). Esto significa que instalar el paquete tendrá _ningún efecto_ para los proyectos Xamarin.Android y Xamarin.iOS. El paquete de NuGet se da por supuesto que ambos de esas plataformas proporcionan sus _propio_ implementación de los tipos. Esta suposición es "correcta" en el sentido de que tienen Mono _una_ implementación del espacio de nombres, pero como hemos explicado en puntos \#2 y \#3 de "Detalles sobre los 3 niveles de errores", anteriormente, la implementación está actualmente incompleta. Por lo que será la solución adecuada para que el equipo de Mono resolver [error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) y [error 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).


## <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, vea [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo registre un error nuevo si es necesario.
