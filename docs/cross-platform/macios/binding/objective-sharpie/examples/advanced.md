---
title: Avanzado (manual) ejemplo del mundo Real
description: Este documento describe cómo usar la salida de xcodebuild como entrada para Sharpie objetivo, que proporciona una visión de lo que hace Sharpie objetivo debajo del capó.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c4f7f1e9702fb2ee0f5525343a52e3aacd85d68c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200290"
---
# <a name="advanced-manual-real-world-example"></a>Avanzado (manual) ejemplo del mundo Real

**Este ejemplo se usa el [biblioteca POP desde Facebook](https://github.com/facebook/pop).**

Esta sección trata de un enfoque más avanzado para enlace, que se usará de Apple `xcodebuild` herramienta para compilar primero el proyecto POP y después deducen manualmente la entrada Sharpie objetivo. Esto abarca básicamente lo que hace Sharpie objetivo en segundo plano en la sección anterior.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Dado que la biblioteca de POP tiene un proyecto de Xcode (`pop.xcodeproj`), podemos utilizar `xcodebuild` para crear el punto de presencia. Este proceso a su vez puede generar archivos de encabezado que se deben analizar Sharpie objetivo. Se trata de por qué crear antes de enlace es importante. Cuando se crea a través de `xcodebuild` asegurarse de que pasa el mismo identificador SDK y arquitectura que se va a pasar al objetivo Sharpie (y recuerde, objetivo Sharpie 3.0 normalmente puede hacer por usted):

```
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

Habrá mucha información de salida de compilación en la consola como parte de `xcodebuild`. Tal como se muestra anteriormente, podemos ver que se ha ejecutado un destino de "CpHeader" en la que se copiaron los archivos de encabezado en un directorio de salida de compilación. Esto suele ocurrir y facilita el enlace: como parte de la compilación de la biblioteca nativa, a menudo se copian los archivos de encabezado en una ubicación consumibles "público" que puede hacer que sea más fácil analizar para el enlace. En este caso, sabemos que son archivos de encabezado del punto de presencia en la `build/Headers` directory.

Ahora estamos preparados enlazar el punto de presencia. Sabemos que deseamos crear para SDK `iphoneos8.1` con el `arm64` arquitectura, y que los archivos de encabezado que nos interesan están en `build/Headers` en la extracción de git POP. Si observamos el `build/Headers` directorio, veremos un número de archivos de encabezado:

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Si miramos `POP.h`, podemos ver es el archivo de encabezado de nivel superior de la biblioteca principal que `#import`s otros archivos. Por este motivo, sólo necesitamos pasar `POP.h` a Sharpie objetivo, y clang encargará del resto en segundo plano:

```
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

Observará que se pasa un `-scope build/Headers` argumento Sharpie objetivo. Dado que las bibliotecas de C y Objective-C deben `#import` o `#include` otros archivos de encabezado son detalles de implementación de la biblioteca y no las API que desea enlazar, el `-scope` argumento dice Sharpie objetivo para pasar por alto cualquier API que no está definido en un archivo en algún lugar dentro de la `-scope` directory.

Encontrará el `-scope` argumento a menudo es opcional para las bibliotecas de implementada correctamente, pero no es ningún daño en suministra de forma explícita.

Además, hemos especificado `-c -Ibuild/headers`. En primer lugar, el `-c` argumento dice Sharpie objetivo para dejar de interpretar los argumentos de línea de comandos y pase los argumentos subsiguientes _directamente al compilador clang_. Por lo tanto, `-Ibuild/Headers` es incluye un argumento de compilador de clang que indique a clang para buscar en `build/Headers`, que es donde residen los encabezados POP. Sin este argumento, clang no sabría dónde ubicar los archivos que `POP.h` es `#import`ing. _Casi todos los "problemas" con el objetivo Sharpie reducen a averiguar lo que debe pasar para clang_.

### <a name="completing-the-binding"></a>Completar el enlace

Ahora ha generado el objetivo Sharpie `Binding/ApiDefinitions.cs` y `Binding/StructsAndEnums.cs` archivos.

Estas son las primera pasada básica del objetivo Sharpie del enlace y, en algunos casos puede ser todo lo que necesita. Como se indicó anteriormente, sin embargo, el desarrollador normalmente tendrá que modificar manualmente los archivos generados al finalizar objetivo Sharpie a [corregir cualquier problema](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) que podría no ser automáticamente controlada por la herramienta.

Una vez que finalizan las actualizaciones, estos dos archivos ahora se pueden agregar a un proyecto de enlace en Visual Studio para Mac o se pasa directamente a la `btouch` o `bmac` herramientas para producir el enlace final.

Para obtener una descripción detallada del proceso de enlace, consulte nuestra [instrucciones de tutorial completo](~/ios/platform/binding-objective-c/walkthrough.md).

## <a name="related-links"></a>Vínculos relacionados

- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)