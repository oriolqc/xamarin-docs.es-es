---
title: Historial de versiones Sharpie objetivo
description: Este documento describe el historial de versiones Sharpie objetivo, la herramienta usada para automatizar la creación de C# enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 03e4a5ac8906d2593cbdf3c15f6b2d1f4a2c6d19
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199654"
---
# <a name="objective-sharpie-release-history"></a>Historial de versiones Sharpie objetivo

## <a name="34-october-11-2017"></a>3.4 (11 de octubre de 2017)

[Descargar v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Compatibilidad con Xcode 9: iOS 11, macOS 10.13, 11, tvOS y watchOS 4
* Ahora se deben corregir los problemas con SIMD y tgmath
* Se quitó completamente telemetría

## <a name="33-august-3-2016"></a>3.3 (3 de agosto de 2016)

[Descargar v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Compatibilidad con Xcode 8 Beta 4, 10 de iOS, macOS 10.12, tvOS 10 y watchOS 3.
* Actualizado para Clang de última generación principal (2016-08-02)
* [Conservar las opciones de envío de telemetría](https://twitter.com/Symbiatch/status/760373403878559744) desde `sharpie pod bind` a `sharpie bind`.

## <a name="32-june-14-2016"></a>3.2 (14 de junio de 2016)

[Descargar v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Compatibilidad con Xcode 8 Beta 1, 10 de iOS, macOS 10.12, tvOS 10 y watchOS 3.

## <a name="31-may-31-2016"></a>3.1 (31 de mayo de 2016)

[Descargar v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Compatibilidad con CocoaPods 1.0
* Mejor confiabilidad de enlace de CocoaPods mediante la primera compilación nativa `.framework` y, a continuación, enlace
* Copiar CocoaPods `.framework` y enlace definición en un `Binding` directory para facilitar la integración con proyectos de enlace de Xamarin.iOS y Xamarin.Mac

## <a name="30-october-5-2015"></a>3.0 (5 de octubre de 2015)

[Descargar v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Compatibilidad con nuevas características de lenguaje de Objective-C incluidos genéricos ligeros y la nulabilidad, como se mencionó en Xcode 7
* Compatibilidad con iOS y Mac SDK más reciente.
* Proyecto de Xcode creación y análisis de soporte técnico. Ahora puede pasar un proyecto de Xcode completo para Sharpie objetivo y hará todo lo posible para averiguar lo adecuado (por ejemplo, `sharpie bind Project.xcodeproj -sdk ios`).
* [CocoaPods](https://cocoapods.org) admiten! Ahora puede configurar, crear y enlazar CocoaPods directamente desde Sharpie objetivo (p. ej. `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Al enlazar marcos, los módulos de Clang se habilitará si el marco de trabajo admite, lo que resulta en el análisis correcto de más de un marco de trabajo, puesto que la estructura de framework se define por su `module.modulemap`.
* Para los proyectos de Xcode que crear un producto de framework, analizar ese producto en lugar de los destinos de producto intermedio como destinos que no es de marco en un proyecto de Xcode todavía pueden tener las ambigüedades que no se puede resolver automáticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de marzo de 2015)

[Descargar v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Enlace de la expresión de operador binario fijo: el lado izquierdo de la expresión incorrectamente se ha intercambiado por la derecha (por ejemplo, `1 << 0` enlazó incorrectamente como `0 << 1`). Gracias a Adam Kemp para advertir esto.
* Se ha corregido un problema con `NSInteger` y `NSUInteger` enlazaba como `int` y `uint` en lugar de `nint` y `nuint` en i386; `-DNS_BUILD_32_LIKE_64` ahora se pasa a Clang para realizar análisis `objc/NSObjCRuntime.h` funcionan según lo previsto en i386.
* La arquitectura predeterminada para el SDK de Mac OS X (por ejemplo, `-sdk macosx10.10`) es ahora x86_64 en lugar de i386, por lo que `-arch` puede omitirse, a menos que se desea reemplazar el valor predeterminado.

## <a name="210-march-15-2015"></a>2.1.0 (15 de marzo de 2015)

[Descargar v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxC#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Asegúrese de `using ObjCRuntime;` se genera cuando `ArgumentSemantic` se utiliza.
* [bxC#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Asegúrese de `using System.Runtime.InteropServices;` se genera cuando `DllImport` se utiliza.
* [bxC#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Default `DllImport` para cargar símbolos desde `__Internal`.
* [bxC#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Omitir declaraciones de contenedor de Objective-C declarado por el avance.
* [bxC#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Enlazar tipos de protocolo con un único calificación como interfaces concretas (`id<Foo>` como `Foo` en lugar de `Foundation.NSObject<Foo>`).
* [bxC#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): Enlazar `UInt32`, `UInt64`, y `Int64` literales como `Int32` para quitar el `u` o `uL` sufijos cuando los valores de forma segura pueden adaptarse a `Int32`.
* [bxC#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Corrija la asignación de nombres de enumeración cuando se inicia el original nombre nativo con un `k` prefijo.
* `sizeof` Expresiones de C cuyo tipo de argumento no se asigna a un C# tipo primitivo se evaluará en Clang y enlazado como un literal entero para evitar que se generen válido C#.
* Corrija la sintaxis de Objective-C para las propiedades cuyo tipo es un bloque (código de Objective-C aparece en los comentarios sobre las declaraciones de enlaces).
* Enlazar tipos cariados como su tipo original (`int[]` se reduce a `int*` durante el análisis semántico en Clang, pero enlazarlo que el original como escritas `int[]` en su lugar).

Muchas gracias a Dave Dunkin para muchos de los errores corregidos en esta versión de punto reporting!

## <a name="200-march-9-2015"></a>2.0.0: 9 de marzo de 2015

[Descargar v2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Objetivo 2.0 Sharpie es una versión principal que incluye un controlador basado en Clang mejorado y analizador y un nuevo motor de enlace basado en NRefactory. Estos componentes mejorados se proporcionan para _mucho_ mejor los enlaces, especialmente en relación con el enlace de tipo. Muchas otras mejoras se realizaron internas Sharpie objetivo que brindará muchas características visibles para el usuario en futuras versiones.

Objetivo 2.0 Sharpie se basa en Clang 3.6.1.

### <a name="type-binding-improvements"></a>Mejoras de tipos de enlace

* Bloques de Objective-C ahora se admiten. Esto incluye bloques anónima/en línea y bloques con nombre a través de `typedef`. Bloques anónimos se vincularán como `System.Action` o `System.Func` delegados, mientras que los bloques con nombre se enlazará un nombre seguro como `delegate` tipos.

* Hay una heurística de nomenclatura mejorada para las enumeraciones anónimas que se vaya precedidas inmediatamente por un `typedef` resolver a un tipo entero builtin como `long` o `int`.

* Punteros de C ahora se enlazan como C# `unsafe` punteros en lugar de `System.IntPtr`. Esto da como resultado mayor claridad en el enlace para cuando es posible que desee activar en los parámetros de puntero `out` o `ref` parámetros. No es posible deducir siempre si un parámetro debe ser `out` o `ref`, por lo que el puntero se mantiene en el enlace para permitir la auditoría sea más fácil.

* Una excepción para el enlace anterior del puntero es cuando se encuentra un puntero de rango de 2 a un objeto de Objective-C como un parámetro. En estos casos, la convención es predominante y se enlazará el parámetro como `out` (por ejemplo, `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Comprobar el atributo

A menudo encontrará enlaces producidos por objetivo Sharpie ahora se anotará con la `[Verify]` atributo. Estos atributos indican que debe _comprobar_ que objetivo Sharpie ha hecho lo correcto comparando el enlace con la declaración de C, Objective-C original (que se proporcionará en un comentario sobre la declaración de enlace).

La comprobación es _recomienda_ para todas las declaraciones enlaces, pero es más probable _requiere_ para declaraciones anotan con el `[Verify]` atributo. Esto es porque en muchas situaciones, no hay suficientes metadatos en el código nativo de origen original para deducir cómo generar mejor un enlace. Es posible que deba hacer referencia a documentación o los comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Consulte la [comprobar atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) documentación para obtener más detalles.

### <a name="other-notable-improvements"></a>Otras mejoras importantes

* `using` Ahora se generan instrucciones basadas en tipos enlazados. Por ejemplo, si un `NSURL` enlazó, un `using Foundation;` instrucción generará también.

* `struct` y `union` declaraciones ahora estará enlazadas, utilizando el `[FieldOffset]` baza para uniones.

* Valores de enumeración con los inicializadores de la expresión constante se enlazarán correctamente; la expresión completa se traduce a C#.

* Ahora se enlazan los bloques y los métodos de Variádicas.

* Marcos de trabajo ahora se admiten a través de la `-framework` opción. Consulte la documentación sobre [plataformas nativas de enlace](https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks) para obtener más detalles.

* Código fuente Objective-C será detecta automáticamente ahora, lo que debe eliminar la necesidad de pasar `-ObjC` o `-xobjective-c` para Clang manualmente.

* Uso de módulos de clang (`@import`) es ahora detecta automáticamente, lo que debe eliminar la necesidad de pasar `-fmodules` para Clang manualmente para las bibliotecas que usan la nueva compatibilidad de módulo en Clang.

* La API de unificado de Xamarin ahora es el destino de enlace predeterminada; Utilice el `-classic` opción a la API clásica sólo de 32 bits de destino.

### <a name="notable-bug-fixes"></a>Correcciones de errores importantes

* Corregir `instancetype` enlace cuando se utiliza en una categoría de Objective-c.
* Categorías de Objective-C de nombre completo
* Prefijo de protocolos de Objective-C con `I` (por ejemplo, `INSCopying` en lugar de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 21 de diciembre de 2014

[Descargar v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correcciones de errores menores.

## <a name="111-december-15-2014"></a>1.1.1: 15 de diciembre de 2014

[Descargar v1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 fue la primera versión principal después de 1,5 años de uso interno y desarrollo de Xamarin después de la versión preliminar inicial de objetivo Sharpie en abril de 2013. Esta versión es la primera que generalmente se considera estable y se puede usar para una amplia variedad de bibliotecas nativas, que incluye un nuevo back-end de Clang.

