---
title: Introducción a la API unificada
description: API de unificado de Xamarin permite compartir código entre aplicaciones de 32 y 64 bits de Mac y iOS y compatibilidad con el mismo código binario.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: d54d31019f04dc28b9b85a13a0a93f85abc0be75
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782000"
---
# <a name="unified-api-overview"></a>Introducción a la API unificada

API de unificado de Xamarin permite compartir código entre aplicaciones de 32 y 64 bits de Mac y iOS y compatibilidad con el mismo código binario. La API unificada se utiliza de forma predeterminada en los nuevos proyectos de Xamarin.iOS y Xamarin.Mac.

> [!IMPORTANT]
> La API clásica de Xamarin, que va precedida de la API unificada, está en desuso. 
> - La última versión de Xamarin.iOS para admitir la API clásica (monotouch.dll) era Xamarin.iOS 9.10.
> - Xamarin.Mac sigue admitiendo la API clásico, pero ya no se actualizará. Puesto que está en desuso, los desarrolladores deben mover sus aplicaciones a la API unificada.

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

-  **API clásica:** limitado a 32 bits (solamente) y expuesta en los ensamblados `monotouch.dll` y `XamMac.dll` .
-  **API unificada:** admiten el desarrollo de bits de 32 y 64 con una única API disponible en los ensamblados `Xamarin.iOS.dll` y `Xamarin.Mac.dll`.

Esto significa que para la empresa a los desarrolladores (no orientados a la App Store), puede seguir usando las clásicas APIs  existentes, tal y como las estaremos manteniendo siempre, o también pueden actualizar a las nuevas API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Cambios de Namespace

Para reducir la fricción para compartir código entre nuestros productos iOS y Mac, estamos cambiando los espacios de nombres de las API en los productos.

Le estamos quitar el prefijo "MonoTouch" de nuestros productos de iOS y "MonoMac" de nuestro producto Mac en los tipos de datos.

Esto hace más fácil compartir código entre las plataformas iOS y Mac sin tener que recurrir a la compilación condicional y esto también reducirá el ruido en la parte superior de los archivos de código fuente.

-  **API clásica:** usar espacios de nombres `MonoTouch.` o `MonoMac.` prefijo.
-  **API unificada:** No usa ningún prefijo de espacio de nombres

## <a name="runtime-defaults"></a>Valores predeterminados en tiempo de ejecución

La API unificada usa de manera predeterminada el recolector **SGen**  de elementos no utilizados y la [Nuevo sistema de Conteo de referencias](~/ios/internals/newrefcount.md) de seguimiento de la propiedad del objeto. Esta misma función ha sido trasladada a Xamarin.Mac.

Esto soluciona número de problemas que los desarrolladores enfrentan el sistema antiguo y también facilita la [administración de memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Tenga en cuenta que es posible habilitar el nuevo recuento de referencias incluso para la API clásico, pero el valor predeterminado es conservador y no requiere a los usuarios realizar los cambios. Con la API unificada, se tuvo la oportunidad de cambiar el valor predeterminado y proporcionar a los programadores todas las mejoras al mismo tiempo que refactorizar y volver a probar su código.

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

Para obtener una lista completa de cambios cuando se cambia desde el clásico a la API unificada, visite nuestra documentación [Diferencias en API Clasica(monotouch.dll) VS API unificada(Xamarin.iOS.dll)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/).

## <a name="updating-to-unified"></a>Actualizar a unificada

Varias API/dividir/obsoleta en **clásico** no están disponibles en la API **unificada**. Puede ser más fácil de corregir la advertencias `CS0616` antes de iniciar la actualización(manuales o automatizadas), puesto que tendrá el atributo mensaje `[Obsolete]` (parte de la advertencia) que le guiarán a la API de la derecha.

Tenga en cuenta que estamos publicando un [ *diferencias* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) de vs clásico unificado cambios en la API que pueden utilizarse antes o después de las actualizaciones del proyecto. Todavía se corrige el obsoletes llamadas sesión clásico suele representar un ahorro de tiempo (menos búsquedas de documentación).

Siga estas instrucciones para [actualizar aplicaciones existentes de iOS](~/cross-platform/macios/unified/updating-ios-apps.md), o [aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md) a la API unificada.
Revise el resto de esta página, y [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener más información sobre cómo migrar el código.

### <a name="nuget"></a>NuGet

Los paquetes de NuGet que admitía Xamarin.iOS a través de la API clásica publicar sus ensamblados con el **Monotouch10** moniker de la plataforma.

La API unificada presenta un nuevo identificador de plataforma para los paquetes compatibles - **Xamarin.iOS10**. Paquetes de NuGet existentes deberá actualizarse para agregar compatibilidad para esta plataforma, mediante la construcción frente a la API unificada.

> [!IMPORTANT]
> Si hay un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, versión = 0.0.000, Referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificado, suele ser debido a que un componente o un paquete de NuGet en el proyecto que no se ha actualizado a la API unificada. Debe quitar el componente/NuGet existente, actualice a una versión que admite las API unificada y realice una compilación limpia.

### <a name="the-road-to-64-bits"></a>Viaje a 64 Bits

Para obtener información sobre la compatibilidad con 32 y 64 bits aplicaciones y obtener información sobre marcos, consulte el [Consideraciones de la plataforma para 32 y 64 bits](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nuevos tipos de datos

En el núcleo de la diferencia, Mac y la API de iOS usan un tipo de datos específicos de la arquitectura que siempre es de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo,  Objective-c. asigna el tipo de dato `NSInteger` para `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestra API unificada, sustituiremos los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`.  Se puede considerar "n" como "nativos", para escribir el tipo entero nativo de la plataforma.

Estamos incorporando `nint`, `nuint` y `nfloat` también proporciona los tipos de datos creado a partir de ellos cuando sea necesario.

Para obtener más información sobre estos cambios de tipo de datos, vea la documentación de [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

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

Debido a que los indizadores de C# esperan un tipo de `int`, tendrá que convertir explícitamente `nint` valores `int` para tener acceso a los elementos de una colección o matriz. Por ejemplo:

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

Dentro del API clásico de Xamarin.iOS  (monotouch.dll) el atributo `[Obsolete]` se utiliza de dos maneras diferentes:

-  **En desuso API de iOS:** trata cuando quiera dejar de usar una API porque se sustituida por la más reciente de sugerencias de Apple. La API clásica es ningún problema y a menudo es necesario (si se admite la versión anterior de iOS).
 Estas API (y el `[Obsolete]` atributo) se incluyen en los nuevos ensamblados de Xamarin.iOS.
-  **API incorrecto** algunas API tenía errores tipográficos en sus nombres.

Para los ensamblados originales (monotouch.dll y XamMac.dll) se mantiene el código antiguo disponible para compatibilidad pero se han quitado de los ensamblados de API unificada (Xamarin.iOS.dll y Xamarin.Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject subclases .ctor(IntPtr)

Cada `NSObject` subclase tiene un constructor que acepta un `IntPtr`. Se trata cómo le podemos crear una instancia de una nueva instancia administrada de un identificador nativo de ObjC.

En el API clásico `public` era una  constructor. Sin embargo, era muy fácil hacer un mal uso de esta característica en el código de usuario, por ejemplo, crear varios administrados instancias para una única instancia de ObjC *o* crear una instancia administrada que podría no tienen el estado esperado administrado (para que las subclases).

Para evitar esa clase de problemas los constructores `IntPtr`  ahora son `protected` en la API **unificada** , se usa únicamente para crear subclases de una. Esto garantizará que la corregir/seguridad de API se utiliza para crear la instancia administrada de identificadores, es decir

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

**Clásico**

    public virtual string ReuseIdentifier { get; }

**Unificado**

    public virtual NSString ReuseIdentifier { get; }

Por lo general se prefiere .NET `System.String` tipo. Sin embargo, a pesar de las instrucciones de Apple, algunas API nativa se van a comparar punteros constantes (no la propia cadena) y solo pueden funcionar cuando se exponen las constantes como `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos de Objective-c.

El MonoTouch original no tenía compatibilidad total con los protocolos ObjC y algunos no óptimo, API se agregaron para admitir el escenario más común. Esta limitación no existe ya, pero, por compatibilidad con versiones anteriores, se mantienen varias API alrededor dentro de `monotouch.dll` y `XamMac.dll`.

Estas limitaciones se han quitado y limpiar en las API unificada. La mayoría de cambios tendrá un aspecto similar al siguiente:

**Clásico**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unificado**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

El `I` prefijo significa **unificada** exponer una interfaz, en lugar de un tipo específico para el protocolo ObjC. Esto facilitará la casos donde no desea subclase el tipo específico que proporciona Xamarin.iOS.

También se permiten algunas API para ser más precisos y fácil de usar, p. ej.:

**Clásico**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unificado**

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

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](updating-ios-apps.md)
- [Actualizar aplicaciones de Mac](updating-mac-apps.md)
- [Actualizar aplicaciones de Xamarin.Forms](updating-xamarin-forms-apps.md)
- [Actualizar enlaces](update-binding.md)
- [Actualización de sugerencias](updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
