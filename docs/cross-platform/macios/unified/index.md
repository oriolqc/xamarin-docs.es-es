---
title: API unificada
description: "El nuevo estilo API resulta más fácil que nunca para compartir código entre Mac y iOS, así como lo que le permite admitir las aplicaciones de 32 y 64 bits con el mismo binario."
ms.topic: article
ms.prod: xamarin
ms.assetid: 14311617-1BC2-42CC-AF3F-9F97733EE2D0
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 68ea5c700efaaf007718cae3ec8b8f7875385b07
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="unified-api"></a>API unificada

_El nuevo estilo API resulta más fácil que nunca para compartir código entre Mac y iOS, así como lo que le permite admitir las aplicaciones de 32 y 64 bits con el mismo binario._

Para mejorar el uso compartido entre iOS y Mac de código y para permitir a los desarrolladores tienen una sola base de código que funciona en 32 y 64 bits, en una fase temprana 2015 se introdujo una nueva API de productos Xamarin.Mac y Xamarin.iOS llamado a la API unificada.

> [!IMPORTANT]
> **Degradación de perfil clásico:** tal y como se han agregado nuevas plataformas en Xamarin.iOS empezamos a gradualmente dejar de utilizar características del perfil clásico (monotouch.dll). Por ejemplo, la opción de no NRC (que nueva-ref-count) se ha quitado. NRC siempre se ha habilitado para unificado todas las aplicaciones (es decir, no NRC nunca fue una opción) y no tiene problemas conocidos. Las versiones futuras quitará la opción de usar Boehm como el recolector de elementos no utilizados. También era una opción nunca disponible para las aplicaciones unificadas. La eliminación completa de la compatibilidad clásico está programada para el próximo otoño con el lanzamiento de Xamarin.iOS 10.0.

## <a name="overviewoverviewmd"></a>[Información general](overview.md)

Describe el análisis razonado respecto a la API unificada y explica las diferencias de la API clásica en detalle. Hacer referencia a esta página para conocer los cambios realizados en la API unificada.

## <a name="updating-classic-api-based-apps"></a>Actualizar aplicaciones basadas en la API clásicas

Siga las instrucciones pertinentes para su plataforma:

- [Actualización de aplicaciones existentes](updating-apps.md)
- [Actualización de aplicaciones iOS existentes](updating-ios-apps.md)
- [Actualización de aplicaciones Mac existentes](updating-mac-apps.md)
- [Actualización de aplicaciones Xamarin.Forms existentes](updating-xamarin-forms-apps.md)
- [Migración de un enlace a Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Sugerencias para actualizar el código a Unified API](updating-tips.md)

Independientemente de las aplicaciones que va a migrar, visite [estas sugerencias](updating-tips.md) que le ayudarán a actualizar correctamente a la API unificada.

## <a name="library-split"></a>División de biblioteca

Desde este punto, se producirá el API de dos maneras:

-  **API clásica:** limitado a 32 bits (solo) y está expuesto en la `monotouch.dll` y `XamMac.dll` ensamblados.
-  **API unificada:** admiten el desarrollo de bits de 32 y 64 con una única API disponible en la `Xamarin.iOS.dll` y `Xamarin.Mac.dll` ensamblados.

Esto significa que para la empresa a los desarrolladores (no orientados a la tienda de aplicaciones), puede seguir usando las API clásico existentes, tal y como se mantenga mantener ellos indefinidamente, o se pueden actualizar a las nuevas API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Cambios de Namespace

Para reducir la fricción para compartir código entre nuestros productos iOS y Mac, cambiar los espacios de nombres de las API en los productos.

Le estamos quitar el prefijo "MonoTouch" de nuestros productos de iOS y "MonoMac" de nuestro producto Mac en los tipos de datos.

Esto hace más fácil compartir código entre las plataformas iOS y Mac sin tener que recurrir a la compilación condicional y reducirá el ruido en la parte superior de los archivos de código fuente.

-  **API clásica:** usar espacios de nombres `MonoTouch.` o `MonoMac.` prefijo.
-  **API unificada:** ningún prefijo de espacio de nombres

## <a name="api-changes"></a>Cambios en la API

Quita de la API unificada métodos desusados y no hay algunos casos donde hubiera errores tipográficos en los nombres de API al que se enlazaron a los espacios de nombres MonoTouch y MonoMac original en la API clásica. Estas instancias se han corregido en las nuevas API unificada y deben actualizarse en el componente, aplicaciones de iOS y Mac. Esta es una lista de las más comunes que pueden surgir de:

|Nombre del método de API clásico|Nombre del método de API unificada|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Para obtener una lista completa de cambios cuando se cambia desde el clásico a la API unificada, visite nuestro [clásico (monotouch.dll) frente a las diferencias de API unificada (Xamarin.iOS.dll)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) documentación.

## <a name="updating-to-unified"></a>Actualizar a unificada

Varias API/dividir/obsoleta en **clásico** no están disponibles en la **unificado** API. Puede ser más fácil de corregir la `CS0616` advertencias antes de iniciar la (manuales o automatizadas) actualización puesto que tendrá el `[Obsolete]` atributo mensaje (parte de la advertencia) que le guiarán a la API de la derecha.

Tenga en cuenta que estamos publicando un [ *diferencias* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) de vs clásico unificado cambios en la API que pueden utilizarse antes o después de las actualizaciones del proyecto. Todavía se corrige el obsoletes llamadas sesión clásico suele representar un ahorro de tiempo (menos búsquedas de documentación).

Siga estas instrucciones para [actualizar aplicaciones existentes de iOS](~/cross-platform/macios/unified/updating-ios-apps.md), o [aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md) a la API unificada.
Revise el resto de esta página, y [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener más información sobre cómo migrar el código.

### <a name="nuget"></a>NuGet

Los paquetes de NuGet que admitía Xamarin.iOS a través de la API clásica publicar sus ensamblados con el **Monotouch10** moniker de la plataforma.

La API unificada presenta un nuevo identificador de plataforma para los paquetes compatibles - **Xamarin.iOS10**. Paquetes de NuGet existentes deberá actualizarse para agregar compatibilidad para esta plataforma, mediante la construcción frente a la API unificada.

> [!IMPORTANT]
> **Nota:** si hay un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, Versión = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificado, suele ser debido a que un componente o un paquete de NuGet en el proyecto que no se ha actualizado a la API unificada. Debe quitar el componente/NuGet existente, actualice a una versión que admite las API unificada y realice una compilación limpia.

### <a name="the-road-to-64-bits"></a>Viaje a 64 Bits

Para obtener información sobre la compatibilidad con 32 y 64 bits aplicaciones y obtener información sobre marcos, consulte el [32 y 64 bits consideraciones de la plataforma](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nuevos tipos de datos

En el núcleo de la diferencia, Mac y API de iOS usan un tipo de datos específicos de la arquitectura que siempre es de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo, asigna Objective-c. el `NSInteger` tipo de datos que `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestra API unificada, sustituiremos los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`.  Se puede considerar "n" como "nativos", para escribir el tipo entero nativo de la plataforma.

Estamos incorporando `nint`, `nuint` y `nfloat` también proporciona los tipos de datos creado a partir de ellos cuando sea necesario.

Para obtener más información sobre estos cambios de tipo de datos, vea el [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Cómo detectar la arquitectura de las aplicaciones iOS

Puede haber situaciones donde la aplicación necesite saber si se está ejecutando en un de 32 bits o un sistema de iOS de 64 bits. El siguiente código se puede utilizar para comprobar la arquitectura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrices y System.Collections.Generic

Porque los indizadores de C# esperan un tipo de `int`, tendrá que convertir explícitamente `nint` valores `int` para tener acceso a los elementos de una colección o matriz. Por ejemplo:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Este es el comportamiento esperado, porque la conversión desde `int` a `nint` no es se realiza con pérdida de 64 bits, una conversión implícita.

### <a name="converting-datetime-to-nsdate"></a>Convertir tipos DateTime a NSDate

Al usar las API unificada, la conversión implícita de `DateTime` a `NSDate` valores ya no se realiza. Estos valores deberá convertir explícitamente de un tipo a otro. Los siguientes métodos de extensión se pueden utilizar para automatizar este proceso:

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>Errores tipográficos y las API en desuso

Interior Xamarin.iOS clásico API (monotouch.dll) el `[Obsolete]` atributo se utiliza de dos maneras diferentes:

-  **En desuso API de iOS:** trata cuando quiera dejar de usar una API porque se sustituida por la más reciente de sugerencias de Apple. La API clásica es ningún problema y a menudo es necesario (si se admite la versión anterior de iOS).
 Estas API (y el `[Obsolete]` atributo) se incluyen en los nuevos ensamblados de Xamarin.iOS.
-  **API incorrecto** algunas API tenía errores tipográficos en sus nombres.

Para los ensamblados originales (monotouch.dll y XamMac.dll) se mantiene el código antiguo disponible para compatibilidad pero se han quitado de los ensamblados de API unificada (Xamarin.iOS.dll y Xamarin.Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject subclases .ctor(IntPtr)

Cada `NSObject` subclase tiene un constructor que acepta un `IntPtr`. Se trata cómo le podemos crear una instancia de una nueva instancia administrada de un identificador nativo de ObjC.

En clásico esta era una `public` constructor. Sin embargo, era muy fácil hacer un mal uso de esta característica en el código de usuario, por ejemplo, crear varios administrados instancias para una única instancia de ObjC *o* crear una instancia administrada que podría no tienen el estado esperado administrado (para que las subclases).

Para evitar esa clase de problemas de la `IntPtr` constructores son ahora `protected` en **unificada** API, se usa únicamente para crear subclases de una. Esto garantizará que la corregir/seguridad de API se utiliza para crear la instancia administrada de identificadores, es decir

    var label = Runtime.GetNSObject<UILabel> (handle);

Esta API devolverá una instancia administrada existente (si ya existe) o va a crear uno nuevo (cuando sea necesario). Ya está disponible en la API clásica y unificada.

Tenga en cuenta que la `.ctor(NSObjectFlag)` también es ahora `protected` pero rara vez se ha utilizado esta fuera subclases.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction reemplazado por acción

Con las API unificada, `NSAction` se ha quitado en favor de .NET estándar `Action`. Esto supone una mejora importante porque `Action` es un tipo común. NET, mientras que `NSAction` era Xamarin.iOS específico. Hacen exactamente lo mismo, pero eran tipos distintos e incompatibles y dan como resultado más código tiene que escribirse para lograr el mismo resultado.

Por ejemplo, si la aplicación Xamarin existente incluye el código siguiente:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
Ahora puede reemplazarse con una expresión lambda sencilla:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Anteriormente que sería un error del compilador porque un `Action` no puede asignarse a `NSAction`, pero como `UITapGestureRecognizer` ahora toma un `Action` en lugar de un `NSAction` es válida en las API unificada.

### <a name="custom-delegates-replaced-with-actiont"></a>Reemplazado por acción de delegados personalizados<T>

En **unificada** algunas sencillas (por ejemplo, un parámetro) los delegados de .net se reemplazaron por `Action<T>`. P. ej.,

    public delegate void NSNotificationHandler (NSNotification notification);

Ahora puede usarse como un `Action<NSNotification>`. Este código de promover reutilizar y reducir la duplicación de código dentro de Xamarin.iOS y sus propias aplicaciones.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Tarea<bool> sustituida por tarea < un valor booleano, NSError >>

En **clásico** había algunas API async devolver `Task<bool>`. No obstante, algunos de ellos donde van a usar cuando un `NSError` formaba parte de la firma, es decir, el `bool` ya estaba `true` y había que detectar una excepción al obtener la `NSError`.

Dado que algunos errores son muy comunes y el valor devuelto no resultaban útil este patrón se cambió en **unificada** para devolver un `Task<Tuple<Boolean,NSError>>`. Esto le permite comprobar el éxito y cualquier error que se han producido durante la llamada asincrónica.

### <a name="nsstring-vs-string"></a>Cadena de vs NSString

En algunos casos tenían algunas constantes se cambió de `string` a `NSString`, p. ej. `UITableViewCell`

**Classic**

    public virtual string ReuseIdentifier { get; }

**Unified**

    public virtual NSString ReuseIdentifier { get; }

Por lo general se prefiere .NET `System.String` tipo. Sin embargo, a pesar de las instrucciones de Apple, algunas API nativa se van a comparar punteros constantes (no la propia cadena) y solo pueden funcionar cuando se exponen las constantes como `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos de Objective-c.

El MonoTouch original no tenía compatibilidad total con los protocolos ObjC y algunos no óptimo, API se agregaron para admitir el escenario más común. Esta limitación no existe ya, pero, por compatibilidad con versiones anteriores, se mantienen varias API alrededor dentro de `monotouch.dll` y `XamMac.dll`.

Estas limitaciones se han quitado y limpiar en las API unificada. La mayoría de cambios tendrá un aspecto similar al siguiente:

**Classic**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unified**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

El `I` prefijo significa **unificada** exponer una interfaz, en lugar de un tipo específico para el protocolo ObjC. Esto facilitará la casos donde no desea subclase el tipo específico que proporciona Xamarin.iOS.

También se permiten algunas API para ser más precisos y fácil de usar, p. ej.:

**Classic**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unified**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

Ahora son más fáciles de nosotros, sin referencia a documentación de API como esta, y la finalización de código IDE le proporcionará más útiles sugerencias basadas en la interfaz de protocolo.

#### <a name="nscoding-protocol"></a>Protocolo NSCoding

El enlace original incluye un .ctor(NSCoder) para cada tipo - aun cuando no admitía la `NSCoding` protocolo.  Una sola `Encode(NSCoder)` método estaba presente en el `NSObject` para codificar el objeto.
Pero este método solo funcionará si la instancia se ajustaban a NSCoding protocolo.

En la API unificada hemos corregido esto.  Los nuevos ensamblados solo tendrán el `.ctor(NSCoder)` si el tipo se ajusta a `NSCoding`. También estos tipos tienen ahora un `Encode(NSCoder)` método que se ajusta a la `INSCoding` interfaz.

Bajo impacto: En la mayoría de los casos este cambio no afectará a las aplicaciones como los constructores antiguos, eliminados, no se pudieron usar.

## <a name="further-tips"></a>Otras sugerencias

Cambios adicionales para tener en cuenta que se enumeran en la [sugerencias para actualizar aplicaciones a la API unificada](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Código de ejemplo

A partir del 31 de julio, nos hemos publicado puertos de los ejemplos de iOS a esta nueva API en el `magic-types` crear una bifurcación en [monotouch-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Para Mac, estamos comprobando ejemplos tanto en el [mac-samples](https://github.com/xamarin/mac-samples) repositorio (mostrando nuevas API en Mavericks o Yosemite), así como ejemplos de 32 o 64 bits de la bifurcación de magia tipos [mac-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).
-->

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](updating-ios-apps.md)
- [Actualizar aplicaciones de Mac](updating-mac-apps.md)
- [Actualizar aplicaciones de Xamarin.Forms](updating-xamarin-forms-apps.md)
- [Actualizar enlaces](update-binding.md)
- [Actualización de sugerencias](updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
