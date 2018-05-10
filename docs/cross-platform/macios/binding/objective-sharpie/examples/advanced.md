---
title: Avanzadas (manual) ejemplo del mundo Real
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 82bca525433e5c8fea3a29250afb83962f2e64fc
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="advanced-manual-real-world-example"></a>Avanzadas (manual) ejemplo del mundo Real


**Este ejemplo se utiliza la [biblioteca POP desde Facebook](https://github.com/facebook/pop).**


Esta sección ofrece un enfoque más avanzado para enlace, que se usará de Apple `xcodebuild` herramienta para compilar primero el proyecto POP y, a continuación, deducir manualmente la entrada Sharpie de objetivo. Esto cubre básicamente lo que está haciendo objetivo Sharpie en segundo plano en la sección anterior.

```csharp
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Dado que la biblioteca POP tiene un proyecto de Xcode (`pop.xcodeproj`), podemos utilizar `xcodebuild` para generar POP. Este proceso a su vez puede generar archivos de encabezado que Sharpie objetivo que necesite analizar. Este es el motivo por el que compilar antes de enlace es importante. Cuando se crea a través de `xcodebuild` Asegúrese de que pasa el mismo identificador SDK y arquitectura que desea pasar al objetivo Sharpie (y recuerde, objetivo Sharpie 3.0 normalmente puede hacer por usted):

```csharp
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

Habrá una gran cantidad de salida de la información de compilación en la consola como parte de `xcodebuild`. Como se muestra anteriormente, podemos ver que se ha ejecutado un destino de "CpHeader" en el que se copiaron los archivos de encabezado en un directorio de salida de compilación. Esto suele ocurrir y facilita el enlace: como parte de la compilación de la biblioteca nativa, archivos de encabezado a menudo se copian en una ubicación compatible "público" que puede realizar análisis más fácil para el enlace. En este caso, sabemos que los archivos de encabezado del POP se encuentran en el `build/Headers` directory.

Ahora estamos listos enlazar POP. Sabemos que desea compilar SDK `iphoneos8.1` con el `arm64` arquitectura, y que nos preocupamos sobre los archivos de encabezado están en `build/Headers` bajo la desprotección de git POP. Si tomamos el `build/Headers` directorio, veremos un número de archivos de encabezado:

```csharp
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Si miramos `POP.h`, podemos ver es el archivo de encabezado de nivel superior de la biblioteca principal que `#import`s otros archivos. Por este motivo, sólo es necesario pasar `POP.h` al objetivo Sharpie, y clang llevará a cabo el resto en segundo plano:

```csharp
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

Observará que se pasa un `-scope build/Headers` argumento pasado a Sharpie de objetivo. Dado que las bibliotecas de C y C objetivo deben `#import` o `#include` otros archivos de encabezado que se muestran los detalles de implementación de la biblioteca y no las API que se va a enlazar, el `-scope` argumento indica Sharpie objetivo para pasar por alto cualquier API que no está definido en un archivo en algún lugar dentro de la `-scope` directory.

Encontrará el `-scope` argumento suele ser opcional para las bibliotecas implementadas correctamente, sin embargo no hay peligro en proporcionar explícitamente de él.

Además, se especifica `-c -Ibuild/headers`. En primer lugar, el `-c` argumento indica objetivo Sharpie detener interpretar los argumentos de línea de comandos y pasar los argumentos subsiguientes _directamente en el compilador de clang_. Por lo tanto, `-Ibuild/Headers` incluye un argumento de compilador de clang que indique clang para buscar en `build/Headers`, que es donde están los encabezados POP. Sin este argumento clang no sabría dónde encontrar los archivos que `POP.h` es `#import`realizando una operación. _Casi todos los "problemas" con el objetivo Sharpie se reducen a pensar en lo que se debe pasar para clang_.

### <a name="completing-the-binding"></a>Completar el enlace

Ahora ha generado el objetivo Sharpie `Binding/ApiDefinitions.cs` y `Binding/StructsAndEnums.cs` archivos.

Se trata básica primer intento del objetivo Sharpie para el enlace y, en algunos casos puede ser todo lo que necesita. Como se mencionó anteriormente, sin embargo, el desarrollador normalmente deberá modificar manualmente los archivos generados al finalizar objetivo Sharpie a [corrija los problemas](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) que podría no ser automáticamente controlada por la herramienta.

Una vez que las actualizaciones están completas, estos dos archivos ahora pueden agregarse a un proyecto de enlace en Visual Studio para Mac o se pasa directamente a la `btouch` o `bmac` herramientas para generar el enlace final.

Para obtener una descripción detallada del proceso de enlace, vea nuestra [instrucciones de tutorial completo](~/ios/platform/binding-objective-c/walkthrough.md).

