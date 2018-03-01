---
title: Historial de versiones
ms.topic: article
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 3ab0c968f17f47ea298b89e626995df1f8dc857e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="release-history"></a>Historial de versiones

## <a name="34-october-11-2017"></a>3.4 (11 de octubre de 2017)

[Descargar v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Compatibilidad con Xcode 9: iOS 11, macOS 10.13, tvOS 11 y watchOS 4
* Ahora se deben corregir problemas con SIMD y tgmath
* Se ha quitado completamente telemetría

## <a name="33-august-3-2016"></a>3.3 (3 de agosto de 2016)

[Descargar v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Compatibilidad con Xcode 8 Beta 4, iOS 10, macOS 10.12, tvOS 10 y watchOS 3.
* Actualizado para Clang última generación principal (2016-08-02)
* [Opciones de envío de telemetría se conservan](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` a `sharpie bind`.

## <a name="32-june-14-2016"></a>3.2 (14 de junio de 2016)

[Descargar v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Compatibilidad con Xcode 8 Beta 1, 10 de iOS, Mac OS 10.12, tvOS 10 y watchOS 3.

## <a name="31-may-31-2016"></a>3.1 (31 de mayo de 2016)

[Descargar v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Compatibilidad con CocoaPods 1.0
* Mejora la confiabilidad de enlace CocoaPods creando la primera nativo `.framework` y, a continuación, enlace de datos que
* Copie CocoaPods `.framework` y enlace definición en un `Binding` directorio para facilitar la integración con Xamarin.iOS y Xamarin.Mac proyectos de enlace

## <a name="30-october-5-2015"></a>3.0 (5 de octubre de 2015)

[Descargar v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Compatibilidad con nuevas características del lenguaje Objective-C incluidos genéricos ligeros y la nulabilidad, según se presentó en Xcode 7
* Compatibilidad con iOS y Mac SDK más reciente.
* Proyecto de Xcode creación y análisis de compatibilidad. Ahora puede pasar un proyecto de Xcode completo al objetivo Sharpie y hará todo lo posible para averiguar lo correcto (p. ej. `sharpie bind Project.xcodeproj -sdk ios`).
* [CocoaPods](https://cocoapods.org) admite! Ahora puede configurar, compilar y enlazar CocoaPods directamente desde el objetivo Sharpie (p. ej. `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Al enlazar marcos de trabajo, se habilitarán los módulos de Clang si el marco de trabajo admite, lo que produce el análisis correcto de más de un marco de trabajo, puesto que la estructura de framework se define por su `module.modulemap`.
* Para los proyectos de Xcode que genera un producto de framework, analizar ese producto en lugar de destinos de producto intermedio como destinos de distintas de framework en un proyecto de Xcode todavía pueden tener las ambigüedades que no se pueden resolver automáticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de marzo de 2015)

[Descargar v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Enlace de la expresión de operador binario fijo: el lado izquierdo de la expresión incorrectamente se intercambia con el derecho (por ejemplo, `1 << 0` enlazaba incorrectamente como `0 << 1`). Gracias a Adam Kemp por teniendo en cuenta esto.
* Se corrigió un problema con `NSInteger` y `NSUInteger` que se enlaza como `int` y `uint` en lugar de `nint` y `nuint` en i386; `-DNS_BUILD_32_LIKE_64` ahora se pasa a Clang para realizar análisis `objc/NSObjCRuntime.h` funcionan según lo previsto en i386.
* La arquitectura predeterminada de Mac OS X SDK (por ejemplo, `-sdk macosx10.10`) es ahora x86_64 en lugar de i386, por lo que `-arch` puede omitirse a menos que se desea reemplazar el valor predeterminado.

## <a name="210-march-15-2015"></a>2.1.0 (15 de marzo de 2015)

[Descargar v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc #27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): asegúrese de `using ObjCRuntime;` se genera cuando `ArgumentSemantic` se utiliza.
* [bxc #27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): asegúrese de `using System.Runtime.InteropServices;` se genera cuando `DllImport` se utiliza.
* [bxc #27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Default `DllImport` para cargar símbolos desde `__Internal`.
* [bxc #27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): omitir declaraciones de contenedor Objective-C declaradas por adelantado.
* [bxc #27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): enlazar tipos de protocolo con un único calificación como interfaces concretas (`id<Foo>` como `Foo` en lugar de `Foundation.NSObject<Foo>`).
* [bxc #28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): enlazar `UInt32`, `UInt64`, y `Int64` literales como `Int32` para quitar el `u` o `uL` sufijos cuando los valores de forma segura pueden caber en `Int32`.
* [bxc #28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): corregir la asignación de nombres de enumeración cuando se inicia el nombre nativo original con un `k` prefijo.
* `sizeof` Expresiones de C cuyo argumento de tipo no se asigna a un tipo primitivo de C# se evaluará en Clang y enlazadas como un literal entero para evitar la generación de C# no válido.
* Corrija la sintaxis de C de objetivo para las propiedades cuyo tipo es un bloque (código Objective-C aparece en los comentarios por encima de las declaraciones de enlace).
* Enlazar tipos cariados como su tipo original (`int[]` decaiga a `int*` durante el análisis semántico en Clang, pero enlazarla que el original como escritas `int[]` en su lugar).

Gracias mucho a Dave Dunkin para muchos de los errores corregidos en esta versión de punto de informes.

## <a name="200-march-9-2015"></a>2.0.0: 9 de marzo de 2015

[Descargar v2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Objetivo 2.0 Sharpie es una versión principal que ofrece un controlador mejorado basada en Clang y analizador y un nuevo motor de enlace basado en NRefactory. Estos componentes mejorados proporcionan para _mucho_ mejor enlaces, especialmente alrededor de enlace de tipo. Muchas otras mejoras se realizaron internas Sharpie objetivo que brindará muchas características visibles para el usuario en futuras versiones.

Objetivo 2.0 Sharpie se basa en Clang 3.6.1.

### <a name="type-binding-improvements"></a>Mejoras de tipos de enlace

* Ahora se admiten los bloques de C de objetivo. Esto incluye los bloques anónimo/insertados y bloques con nombre a través de `typedef`. Bloques anónimos se vincularán como `System.Action` o `System.Func` delega, mientras se enlazarán bloques con nombre como un nombre seguro `delegate` tipos.

* Hay una heurística de nomenclatura mejorada para las enumeraciones anónimas inmediatamente precedidos de un `typedef` resolver a un tipo entero builtin como `long` o `int`.

* Punteros de C se vinculan ahora como C# `unsafe` punteros en lugar de `System.IntPtr`. Esto da como resultado mayor claridad en el enlace al que desea activar los parámetros de puntero en `out` o `ref` parámetros. No es posible deducir siempre si un parámetro debe ser `out` o `ref`, por lo que el puntero se conserva en el enlace para permitir la auditoría sea más fácil.

* Una excepción para el enlace de puntero anterior es cuando se encuentra un puntero de rango de 2 a un objeto Objective-C como un parámetro. En estos casos, la convención es predominante y se enlazará el parámetro como `out` (p. ej. `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Comprobar el atributo

A menudo, encontrará que enlaces producidos por objetivo Sharpie ahora se pueden anotar con el `[Verify]` atributo. Estos atributos indican que debe _comprobar_ que objetivo Sharpie ha hecho lo correcto comparando el enlace con la declaración C/Objective-C original (que se proporciona en un comentario sobre la declaración de enlace).

Comprobación es _recomienda_ para todas las declaraciones de enlace, pero es más probable que _requiere_ para declaraciones anotan con el `[Verify]` atributo. Esto es porque en muchas situaciones, no hay suficientes metadatos en el código nativo de origen original para deducir cómo generar más de un enlace. Debe hacer referencia a documentación o los comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Consulte la [comprobar atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) documentación para obtener más detalles.

### <a name="other-notable-improvements"></a>Otras mejoras importantes

* `using` Ahora se generan instrucciones en función de tipos dependientes. Por ejemplo, si un `NSURL` se enlazó, un `using Foundation;` instrucción generará también.

* `struct` y `union` se enlazará ahora declaraciones, usando la `[FieldOffset]` baza para uniones.

* Los valores de enumeración con inicializadores de la expresión constante se enlazarán correctamente; la expresión completa es traducir a C#.

* Ahora se enlazan bloques y los métodos de Variádicas.

* Marcos de trabajo ahora se admiten a través de la `-framework` opción. Consulte la documentación en [enlace marcos de Native](http://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks) para obtener más detalles.

* Código fuente de C objetivo será detecta automáticamente ahora, que debe eliminar la necesidad de pasar `-ObjC` o `-xobjective-c` para Clang manualmente.

* Clang uso de módulo (`@import`) es ahora detecta automáticamente, lo que debe eliminar la necesidad de pasar `-fmodules` para Clang manualmente para las bibliotecas que utiliza la nueva compatibilidad de módulo en Clang.

* La API unificada de Xamarin ahora es el destino de enlace predeterminado; Utilice la `-classic` opción a la API de clásico sólo de 32 bits de destino.

### <a name="notable-bug-fixes"></a>Correcciones de errores importantes

* Corregir `instancetype` enlace cuando se utiliza en una categoría de Objective-c.
* Nombre completo categorías Objective-c.
* Prefijo protocolos Objective-C con `I` (p. ej. `INSCopying` en lugar de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 21 de diciembre de 2014

[Descargar v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correcciones de errores menores.

## <a name="111-december-15-2014"></a>1.1.1: 15 de diciembre de 2014

[Descargar v1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 era la primera versión principal después de 1,5 años de uso interno y desarrollo de Xamarin después de la vista previa inicial de objetivo Sharpie en abril de 2013. Esta versión es la primera que generalmente se considera estable y se puede usar para una amplia variedad de bibliotecas nativas, que incluye Clang back-end.

