---
title: Sugerencias para actualizar el código a Unified API
description: Este documento describen errores comunes y algunas sugerencias útiles al actualizar una aplicación para usar la API de unificado de Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: a5083e1d31377caece1b8fb4faf33b6e3ff88202
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61211825"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Sugerencias para actualizar el código a Unified API

Al actualizar las soluciones de Xamarin anteriores a Unified API, es posible que se encontraron los siguientes errores.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>No se pudo encontrar NSInvalidArgumentException guión gráfico Error

Hay un [error](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) en la versión actual de Visual Studio para Mac que se puede producir después de usar la herramienta de migración automatizada para convertir el proyecto a las API unificada. Tras la actualización, si recibe un mensaje de error en el formulario:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Puede hacer lo siguiente para resolver este problema, busque el archivo de destino de compilación siguiente:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

En este archivo deberá buscar la siguiente declaración de destino:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Y agregue el `DependsOnTargets="_CollectBundleResources"` atributo. Así:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Guarde el archivo, reinicie Visual Studio para Mac y realizar una limpieza y volver a generar el proyecto. Una corrección para este problema debe liberarse mediante Xamarin en breve.

## <a name="useful-tips"></a>Sugerencias útiles

Después de usar la herramienta de migración, puede obtener errores de compilación que requieren intervención manual.
Algunas cosas que es posible que deba corregirse manualmente incluyen:

* Comparar `enum`s podría requerir un `(int)` cast.

* `NSDictionary.IntValue` ahora devuelve un `nint`, hay un `Int32Value` que se puede usar en su lugar.

* `nfloat` y `nint` no se puede marcar tipos `const`;   `static readonly nint` es una alternativa razonable.

* Lo que solía ser directamente en el `MonoTouch.` ahora están generalmente en el espacio de nombres el `ObjCRuntime.` espacio de nombres (p. ej.: `MonoTouch.Constants.Version` ahora es `ObjCRuntime.Constants.Version`).

* Puede interrumpir el código que serializa los objetos cuando se intenta serializar `nint` y `nfloat` tipos. No olvide consultar que el código de serialización funciona según lo previsto después de la migración.

* A veces la herramienta automatizada que omite el código dentro de `#if #else` directivas de compilador condicionales. En este caso deberá realizar las correcciones de forma manual (consulte los siguientes errores comunes).

* Los métodos exportados manualmente mediante `[Export]` no se puede corregir automáticamente mediante la herramienta de migración, por ejemplo, en este snippert de código que se debe actualizar manualmente el tipo de valor devuelto a `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * Unified API no proporciona una conversión implícita entre NSDate y DateTime de .NET porque no es una conversión sin pérdida de datos. Para evitar errores relacionados con `DateTimeKind.Unspecified` convertir .NET `DateTime` en local o UTC antes de convertir a `NSDate`.

 * Ahora se generan métodos de la categoría de Objective-C como métodos de extensión en la API unificada. Por ejemplo, código que anteriormente usó `UIView.DrawString` ahora haría referencia `NSString.DrawString` en Unified API.

 * Código que utiliza clases AVFoundation con `VideoSettings` debe cambiar para que usen el `WeakVideoSettings` propiedad. Esto requiere un `Dictionary`, que está disponible como una propiedad en las clases de configuración, por ejemplo:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * El NSObject `.ctor(IntPtr)` constructor ha cambiado de pública a protegido ([para evitar el uso inadecuado](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

 * `NSAction` ha sido [reemplazado](~/cross-platform/macios/unified/overview.md#NSAction) con el .NET starndard `Action`. También se han reemplazado algunos delegados simple (parámetro único) con `Action<T>`.

Por último, hacer referencia a la [diferencias clásico v Unified API](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) para buscar cambios en las API en el código. Buscar [esta página](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) le ayudará a encontrar las API clásicas y lo que ha se ha actualizado a.

**Nota:** el `MonoTouch.Dialog` espacio de nombres permanece igual después de la migración. Si el código usa **MonoTouch.Dialog** debe continuar usar ese espacio de nombres: *no* cambiar `MonoTouch.Dialog` a `Dialog`!

## <a name="common-compiler-errors"></a>Errores comunes del compilador

Otros ejemplos de errores comunes se enumeran a continuación, junto con la solución:

**Error CS0012: El tipo 'MonoTouch.UIKit.UIView' está definido en un ensamblado que no se hace referencia.**

Corrección: Esto suele significar que el proyecto hace referencia a un componente o paquete de NuGet que no se ha compilado con la API unificada. Debe eliminar y volver a agregar todos los componentes y NuGet paquetes. Si esto no soluciona el error, la biblioteca externa no admite aún la API unificada.

**Error MT0034: No pueden incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS - 'Xamarin.iOS.dll' se hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' Xamarin.Mobile, versión = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Corrección: Elimine el componente que está causando este error y vuelva a agregar al proyecto.

**Error CS0234: El nombre de tipo o espacio de nombres 'Base' no existe en el espacio de nombres 'MonoTouch'. ¿Falta una referencia de ensamblado?**

Corrección: La herramienta de migración automatizada en Visual Studio para Mac *debe* actualizar todo `MonoTouch.Foundation` las referencias a `Foundation`, sin embargo, en algunos casos, estos deberá actualizarse manualmente. Pueden aparecer errores similares para otros espacios de nombres contenidas previamente en `MonoTouch`, tales como `UIKit`.

**Error CS0266: No se puede convertir implícitamente el tipo 'double' en 'System.float'**

Corrección: cambiar el tipo y convertir a `nfloat`. Este error puede producirse también para los demás tipos por sus equivalentes de 64 bits (como `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Error CS0266: No se puede convertir implícitamente el tipo 'CoreGraphics.CGRect' a 'System.Drawing.RectangleF'. Existe una conversión explícita (¿falta una conversión?)**

Corrección: Cambie las instancias a `RectangleF` a `CGRect`, `SizeF` a `CGSize`, y `PointF` a `CGPoint`. El espacio de nombres `using System.Drawing;` debe reemplazarse por `using CoreGraphics;` (si aún no está presente).

**Error CS1502: Sobrecarga de la mejor coincidencia de método para ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' tiene algunos argumentos no válidos**

Corrección: Cambiar el tipo de matriz a `nfloat[]` y convierta explícitamente `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Error CS0115: 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' está marcado como un reemplazo, pero ningún método adecuado que se encontró para invalidar**

Corrección: Cambiar los tipos de valor y los parámetros devueltos a `nint`. Esto suele ocurrir en los reemplazos de método como las de `UITableViewSource`, incluidos `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etcetera.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Error CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**

Corrección: Cuando se cambia el tipo de valor devuelto a `nint`, convertir el valor devuelto a `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Error CS1061: El tipo 'CoreGraphics.CGPath' no contiene una definición para 'AddElipseInRect'**

Corrección: Corrija la ortografía a `AddEllipseInRect`. Otros cambios de nombre incluyen:

* Cambie 'Color.Black' a `NSColor.Black`.
* Cambie 'AddAnnotation' MapKit a `AddAnnotations`.
* Cambie 'DataUsingEncoding' AVFoundation a `Encode`.
* Cambie 'AVMetadataObject.TypeQRCode' AVFoundation a `AVMetadataObjectType.QRCode`.
* Cambie 'VideoSettings' AVFoundation a `WeakVideoSettings`.
* Cambiar PopViewControllerAnimated a `PopViewController`.
* Cambie 'CGBitmapContext.SetRGBFillColor' CoreGraphics a `SetFillColor`.

**Error CS0546: no se puede invalidar porque 'MapKit.MKAnnotation.Coordinate' no tiene un descriptor de acceso set invalidable (CS0546)**

Al crear una anotación personalizada mediante la creación de subclases de MKAnnotation el campo de coordenadas no tiene ningún establecedor, solo un captador.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Agregar un campo para realizar un seguimiento de la coordenada
* devolver este campo en el captador de la propiedad de coordenadas
* Invalide el método SetCoordinate y establezca el campo
* Llamar a SetCoordinate en su constructor con el parámetro pasado coordenado

Debería tener un aspecto similar al siguiente:

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Actualización de aplicaciones](~/cross-platform/macios/unified/updating-apps.md)
- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Actualización de aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Actualización de aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Actualizando enlaces](~/cross-platform/macios/unified/update-binding.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
