---
title: Introducción a la API unificada
description: Unified API de Xamarin permite compartir código entre aplicaciones de 32 y 64 bits de Mac y iOS y compatibilidad con el mismo código binario.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1d159d280bd3b8855c32e3e437dfdefcbe0463cb
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235030"
---
# <a name="unified-api-overview"></a>Introducción a la API unificada

Unified API de Xamarin permite compartir código entre aplicaciones de 32 y 64 bits de Mac y iOS y compatibilidad con el mismo código binario. Se usa la API unificada de forma predeterminada en los nuevos proyectos de Xamarin.iOS y Xamarin.Mac.

> [!IMPORTANT]
> La API clásica de Xamarin, que precede a la API unificada, está desusada. 
> - La última versión de Xamarin.iOS para admitir la API clásica (monotouch.dll) era 9.10 de Xamarin.iOS.
> - Xamarin.Mac siga siendo compatible con la API clásica, pero ya no se actualizará. Puesto que está en desuso, los desarrolladores deben mover sus aplicaciones a Unified API.

## <a name="updating-classic-api-based-apps"></a>Actualizar aplicaciones clásicas basadas en API

Siga las instrucciones pertinentes para su plataforma:

- [Actualización de aplicaciones existentes](updating-apps.md)
- [Actualización de aplicaciones iOS existentes](updating-ios-apps.md)
- [Actualización de aplicaciones Mac existentes](updating-mac-apps.md)
- [Actualización de aplicaciones Xamarin.Forms existentes](updating-xamarin-forms-apps.md)
- [Migración de un enlace a Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Sugerencias para actualizar el código a Unified API](updating-tips.md)

Independientemente de las aplicaciones que va a migrar, consulte [estas sugerencias](updating-tips.md) que le ayudarán a actualizar correctamente a la API unificada.

## <a name="library-split"></a>División de biblioteca

Desde este punto, se producirá nuestras API de dos maneras:

-  **API clásica:** limitado a 32 bits (solamente) y expuesta en los ensamblados `monotouch.dll` y `XamMac.dll` .
-  **API unificada:** admiten el desarrollo de bits de 32 y 64 con una única API disponible en los ensamblados `Xamarin.iOS.dll` y `Xamarin.Mac.dll`.

Esto significa que para la empresa a los desarrolladores (no orientados a la App Store), puede seguir usando las clásicas APIs  existentes, tal y como las estaremos manteniendo siempre, o también pueden actualizar a las nuevas API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Cambios de Namespace

Para reducir la fricción para compartir código entre nuestros productos iOS y Mac, estamos cambiando los espacios de nombres de las API en los productos.

Nos estamos quitando el prefijo "MonoTouch" de nuestros productos de iOS y "MonoMac" de nuestro producto Mac en los tipos de datos.

Esto hace más fácil compartir código entre las plataformas iOS y Mac sin tener que recurrir a la compilación condicional y esto también reducirá el ruido en la parte superior de los archivos de código fuente.

-  **API clásica:** usar espacios de nombres `MonoTouch.` o `MonoMac.` prefijo.
-  **API unificada:** No usa ningún prefijo de espacio de nombres

## <a name="runtime-defaults"></a>Valores predeterminados de tiempo de ejecución

La API unificada usa de manera predeterminada el recolector **SGen**  de elementos no utilizados y la [Nuevo sistema de Conteo de referencias](~/ios/internals/newrefcount.md) de seguimiento de la propiedad del objeto. Esta misma característica se ha trasladado a Xamarin.Mac.

Esto soluciona número de problemas que los desarrolladores enfrentan el sistema antiguo y también facilita la [administración de memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Tenga en cuenta que es posible habilitar de nuevo Refcount incluso para la API clásica, pero el valor predeterminado es conservador y no requieren que los usuarios realizar cambios. Con la API unificada, hemos aprovechado la oportunidad de cambiar el valor predeterminado y brindar a los desarrolladores todas las mejoras al mismo tiempo que refactorizar y volver a probar su código.

## <a name="api-changes"></a>Cambios en la API

Unified API quita métodos en desuso y no hay unas pocas instancias donde había errores tipográficos en los nombres de API al que se enlazaron a los espacios de nombres MonoTouch y MonoMac original en la API clásica. Estas instancias se han corregido en las nuevas API unificada y deben actualizarse en el componente, aplicaciones de iOS y Mac. Esta es una lista de las más comunes que pueden surgir:

|Nombre del método de API clásica|Nombre del método de API unificada|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Para obtener una lista completa de cambios cuando se cambia desde el clásico a la API unificada, visite nuestra documentación [Diferencias en API Clasica(monotouch.dll) VS API unificada(Xamarin.iOS.dll)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/).

## <a name="updating-to-unified"></a>Actualizar a unificada

Varias API/dividir/obsoleta en **clásico** no están disponibles en la API **unificada**. Puede ser más fácil de corregir la advertencias `CS0616` antes de iniciar la actualización(manuales o automatizadas), puesto que tendrá el atributo mensaje `[Obsolete]` (parte de la advertencia) que le guiarán a la API de la derecha.

Tenga en cuenta que estamos publicando un [ *diff* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) de classic vs unificada cambios en la API que pueden usarse antes o después de las actualizaciones del proyecto. Corregir todavía el obsoletes llamadas sesión clásico suelen representar un ahorro de tiempo (menos las búsquedas de documentación).

Siga estas instrucciones para [actualizar aplicaciones existentes de iOS](~/cross-platform/macios/unified/updating-ios-apps.md), o [aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md) a Unified API.
Revise el resto de esta página, y [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener más información sobre cómo migrar el código.

### <a name="nuget"></a>NuGet

Los paquetes de NuGet que admitían Xamarin.iOS a través de la API clásica publicar sus ensamblados con el **Monotouch10** moniker de plataforma.

La API unificada presenta un nuevo identificador de plataforma para los paquetes compatibles - **Xamarin.iOS10**. Paquetes NuGet existentes deberá actualizarse para agregar compatibilidad para esta plataforma, basándose en la API unificada.

> [!IMPORTANT]
> Si tiene un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' se hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, versión = 0.0.000, Referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificada, suele ser debido a que un componente o el paquete NuGet en el proyecto que no se ha actualizado a Unified API. Deberá quitar el componente o NuGet existente, actualice a una versión que admite las API unificada y realizar una compilación limpia.

### <a name="the-road-to-64-bits"></a>El camino a 64 Bits

Para obtener información sobre la compatibilidad con 32 y 64 bits aplicaciones y obtener información sobre marcos, consulte el [Consideraciones de la plataforma para 32 y 64 bits](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nuevos tipos de datos

En el núcleo de la diferencia, Mac y la API de iOS usan un tipo de datos específicos de la arquitectura que siempre es de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo,  Objective-c. asigna el tipo de dato `NSInteger` para `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestra API unificada, sustituiremos los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`.  Se puede considerar de las "n" significado "nativo", por lo que escriba el entero nativo de la plataforma.

Estamos introduciendo `nint`, `nuint` y `nfloat` así proporcionar tipos de datos creado a partir de ellos cuando sea necesario.

Para obtener más información sobre estos cambios de tipo de datos, vea la documentación de [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Cómo detectar la arquitectura de aplicaciones de iOS

Puede haber situaciones donde la aplicación necesita saber si se está ejecutando en una de 32 bits o un sistema de iOS de 64 bits. El código siguiente puede usarse para comprobar la arquitectura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrices y System.Collections.Generic

Debido a que los indizadores de C# esperan un tipo de `int`, tendrá que convertir explícitamente `nint` valores `int` para tener acceso a los elementos de una colección o matriz. Por ejemplo:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Este es el comportamiento esperado, porque la conversión desde `int` a `nint` no es se realiza con pérdida de datos en 64 bits, una conversión implícita.

### <a name="converting-datetime-to-nsdate"></a>Convertir tipos DateTime a NSDate

Al usar las API unificada, la conversión implícita de `DateTime` a `NSDate` valores ya no se realiza. Estos valores se deben convertir explícitamente de un tipo a otro. Los siguientes métodos de extensión pueden utilizarse para automatizar este proceso:

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

### <a name="deprecated-apis-and-typos"></a>Errores tipográficos y API en desuso

Dentro del API clásico de Xamarin.iOS  (monotouch.dll) el atributo `[Obsolete]` se utiliza de dos maneras diferentes:

-  **API de iOS en desuso:** esto es cuando quiera dejar de usar una API porque se sustituye por una versión más reciente de sugerencias de Apple. La API clásica es ningún problema y a menudo es necesario (si se admite la versión anterior de iOS).
 API de este tipo (y el `[Obsolete]` atributo) se incluyen en los nuevos ensamblados de Xamarin.iOS.
-  **API incorrecto** algunas API tuviera errores tipográficos en sus nombres.

Para los ensamblados originales (monotouch.dll y XamMac.dll) se mantiene el código antiguo disponible para compatibilidad pero se han quitado de los ensamblados de Unified API (Xamarin.iOS.dll y Xamarin.Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>.Ctor(IntPtr) subclases de NSObject

Cada `NSObject` subclase tiene un constructor que acepta un `IntPtr`. Se trata cómo nos podemos crear una instancia de una nueva instancia administrada desde un identificador de ObjC nativo.

En el API clásico `public` era una  constructor. Sin embargo, resultó muy fácil hacer un mal uso de esta característica en el código de usuario, por ejemplo, crear varios administra instancias para una única instancia de ObjC *o* crear una instancia administrada que podría no tienen el estado administrado esperado (para que las subclases).

Para evitar esa clase de problemas los constructores `IntPtr`  ahora son `protected` en la API **unificada** , se usa únicamente para crear subclases de una. Esto garantizará que la corregir y seguros API se usa para crear la instancia administrada de identificadores, es decir

    var label = Runtime.GetNSObject<UILabel> (handle);

Esta API devolverá una instancia administrada existente (si ya existe) o va a crear uno nuevo (cuando sea necesario). Ya está disponible en la API clásica y unificada.

Tenga en cuenta que el `.ctor(NSObjectFlag)` también es ahora `protected` pero rara vez se ha utilizado esta fuera de la creación de subclases.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction reemplazado por acción

Con las API unificada, `NSAction` se ha quitado en favor de .NET standard `Action`. Esto supone una mejora importante porque `Action` es un tipo .NET común, mientras que `NSAction` era específico de Xamarin.iOS. Ambos hacen exactamente lo mismo, pero eran tipos distintos e incompatibles y dieron lugar a más código deba escribirse para lograr el mismo resultado.

Por ejemplo, si la aplicación existente de Xamarin incluye el código siguiente:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
Ahora se puede reemplazar con una expresión lambda sencilla:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Anteriormente que sería un error del compilador porque un `Action` no puede asignarse a `NSAction`, pero puesto que `UITapGestureRecognizer` ahora toma un `Action` en lugar de un `NSAction` es válido en las API unificada.

### <a name="custom-delegates-replaced-with-actiont"></a>Delegados personalizados que se reemplazan por acción<T>

En **unificada** algunas sencillas (por ejemplo, un parámetro) delegados de .net se reemplazaron por `Action<T>`. P. ej.,

    public delegate void NSNotificationHandler (NSNotification notification);

Ahora puede usarse como un `Action<NSNotification>`. Este código promover reutilizar y reducir la duplicación de código dentro de Xamarin.iOS y sus propias aplicaciones.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Tarea<bool> reemplazado por la tarea < un valor booleano, NSError >>

En **clásico** Hubo algunas API asincrónicas devolver `Task<bool>`. Sin embargo, algunos de ellos que se van a usar cuando una `NSError` formaba parte de la firma, es decir, el `bool` ya estaba `true` y tenía que detectar una excepción para obtener el `NSError`.

Puesto que algunos errores son muy comunes y el valor devuelto no era útil este patrón se cambió en **unificada** para devolver un `Task<Tuple<Boolean,NSError>>`. Esto le permite comprobar el éxito y cualquier error que podría haber ocurrido durante la llamada asincrónica.

### <a name="nsstring-vs-string"></a>Cadena de vs NSString

En algunos casos se debían cambiarse de algunas constantes `string` a `NSString`, p. ej. `UITableViewCell`

**clásico**

    public virtual string ReuseIdentifier { get; }

**Unificada**

    public virtual NSString ReuseIdentifier { get; }

En general es preferible .NET `System.String` tipo. Sin embargo, a pesar de las directrices de Apple, algunas API nativa son comparar punteros constantes (no la propia cadena) y solo puede funcionar cuando se exponen las constantes como `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos de Objective-c.

El MonoTouch original no tiene compatibilidad total con protocolos ObjC y algunas, que no sea óptimo, API se agregaron para admitir el escenario más común. Esta limitación no existe ya, pero, por compatibilidad con versiones anteriores, se mantienen varias API en torno a dentro de `monotouch.dll` y `XamMac.dll`.

Estas limitaciones se han quitado y limpiado en las API unificada. La mayoría de cambios tendrá este aspecto:

**clásico**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unificada**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

El `I` prefijo significa **unificada** exponer una interfaz, en lugar de un tipo específico para el protocolo ObjC. Esto facilitará a los casos donde no desea subclase del tipo específico que proporciona Xamarin.iOS.

También permite alguna API sea más precisa y fácil de usar, por ejemplo:

**clásico**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unificada**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

Estas API son ahora más fáciles para nosotros, sin hacer referencia a la documentación y la finalización de código IDE le proporcionará más útiles sugerencias basadas en la interfaz de protocolo.

#### <a name="nscoding-protocol"></a>Protocolo NSCoding

El enlace original incluye un .ctor(NSCoder) para cada tipo - aun cuando no admitía la `NSCoding` protocolo.  Una sola `Encode(NSCoder)` método estaba presente en el `NSObject` para codificar el objeto.
Sin embargo, este método solo funcionaría si la instancia se ajustaban a NSCoding protocolo.

En la API unificada hemos corregido esto.  Los nuevos ensamblados solo tendrán el `.ctor(NSCoder)` si el tipo se ajusta a `NSCoding`. También estos tipos tienen ahora un `Encode(NSCoder)` método que se ajusta a la `INSCoding` interfaz.

Impacto bajo: En la mayoría de los casos este cambio no afectará a las aplicaciones como los constructores antiguos, quitados, no se puede usar.

## <a name="further-tips"></a>Conocer más sugerencias

Cambios adicionales a tener en cuenta que se muestran en el [sugerencias para actualizar las aplicaciones a Unified API](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Código de ejemplo

A partir del 31 de julio, hemos publicado los puertos de los ejemplos de iOS para esta nueva API en el `magic-types` bifurcarse en [monotouch-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Para Mac, estamos comprobando ejemplos tanto en el [ejemplos de mac](https://github.com/xamarin/mac-samples) repositorio (se muestran las nuevas API en Mavericks o Yosemite), así como ejemplos de 32 o 64 bits en la rama de magia tipos [ejemplos de mac](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](updating-ios-apps.md)
- [Actualización de aplicaciones de Mac](updating-mac-apps.md)
- [Actualización de aplicaciones de Xamarin.Forms](updating-xamarin-forms-apps.md)
- [Actualizando enlaces](update-binding.md)
- [Actualización de sugerencias](updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
