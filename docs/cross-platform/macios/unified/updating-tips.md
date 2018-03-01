---
title: "Sugerencias para actualizar el código a la API unificada"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: dd44dda0fd52ba1271003b3da9f8d4c756c0625b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Sugerencias para actualizar el código a la API unificada

Con la herramienta de migración automatizadas en Visual Studio para Mac convertirá iOS y hacer referencia a la API unificada (Xamarin.iOS.dll o Xamarin.Mac.dll) y realiza muchos de los cambios de código necesarios para los proyectos de Mac (vea la [versión Xamarin Studio notas de la sección 'Clásica a la herramienta de migración de código de API unificada'](http://developer.xamarin.com/releases/studio/xamarin.studio_5.7/xamarin.studio_5.7/) para obtener detalles específicos).

# <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>No se pudo encontrar NSInvalidArgumentException guión gráfico Error

Hay un [error](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) en la versión actual de Visual Studio para Mac que se puede producir después de usar la herramienta de migración automatizada para convertir el proyecto en las API unificada. Tras la actualización, si recibe un mensaje de error en el formulario:

```csharp
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...

```

Puede hacer lo siguiente para resolver este problema, busque el archivo de destino de compilación siguiente:

```csharp
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

En este archivo que necesite buscar la siguiente declaración de destino:

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

Guarde el archivo, reinicie Visual Studio para Mac y realice una limpieza & volver a generar el proyecto. Una corrección para este problema debe liberarse mediante Xamarin en breve.


# <a name="useful-tips"></a>Sugerencias útiles

Después de usar la herramienta de migración, todavía puede obtener algunos errores de compilador que requieren intervención manual.
Algunas cosas que deban corregirse manualmente incluyen:

* Comparar `enum`s pueden requerir un `(int)` cast.


* `NSDictionary.IntValue` ahora devuelve un `nint`, hay un `Int32Value` que se puede utilizar en su lugar.


* `nfloat` y `nint` no se pueden marcar tipos `const`;   `static readonly nint` es una alternativa razonable.


* Lo que solía ser directamente en el `MonoTouch.` ahora están generalmente en el espacio de nombres la `ObjCRuntime.` espacio de nombres (p. ej.: `MonoTouch.Constants.Version` ahora es `ObjCRuntime.Constants.Version`).


* Puede interrumpir el código que serializa objetos al intentar serializar `nint` y `nfloat` tipos. Asegúrese de comprobar que el código de serialización funciona como se esperaba después de la migración.


* A veces código dentro de los errores de la herramienta automatizada `#if #else` directivas de compilación condicional. En este caso debe realizar las correcciones de forma manual (vea los siguientes errores comunes).


* Métodos manualmente exportados mediante `[Export]` no se puede corregir automáticamente por la herramienta de migración, por ejemplo, en este snippert de código que se debe actualizar manualmente el tipo de valor devuelto para `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```


 * La API unificada no proporciona una conversión implícita entre NSDate y DateTime de .NET porque no es una conversión sin pérdida de datos. Para evitar errores relacionados con `DateTimeKind.Unspecified` convertir .NET `DateTime` locales o UTC antes de la conversión a `NSDate`.


 * Métodos de categoría Objective-C ahora se generan como métodos de extensión en la API unificada. Por ejemplo, código que anteriormente usó `UIView.DrawString` ahora haría referencia `NSString.DrawString` en la API unificada.


 * Código que utiliza las clases de AVFoundation con `VideoSettings` debe cambiar para que usen el `WeakVideoSettings` propiedad. Esto requiere un `Dictionary`, que está disponible como una propiedad en las clases de configuración, por ejemplo:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```


 * El NSObject `.ctor(IntPtr)` constructor se ha cambiado de pública a protegido ([para evitar el uso incorrecto](~/cross-platform/macios/unified/index.md#NSObject_ctor)).


 * `NSAction` ha sido [reemplazado](~/cross-platform/macios/unified/index.md#NSAction) con el starndard .NET `Action`. Algunos delegados simple (parámetro único) también se han reemplazado por `Action<T>`.


Por último, hacer referencia a la [diferencias de API unificada de clásico v](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) para buscar cambios en las API en el código. Buscar [esta página](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) le ayudará a encontrar las API clásico y lo que ha se ha actualizado a.


**Nota:** el `MonoTouch.Dialog` espacio de nombres permanece igual después de la migración. Si el código usa **MonoTouch.Dialog** debe seguir utilizar ese espacio de nombres: hacer *no* cambiar `MonoTouch.Dialog` a `Dialog`!


# <a name="common-compiler-errors"></a>Errores comunes del compilador

Otros ejemplos de errores comunes se enumeran a continuación, junto con la solución:

**Error CS0012: El tipo 'MonoTouch.UIKit.UIView' se define en un ensamblado que no se hace referencia.**

Corrección: Esto normalmente significa que el proyecto hace referencia a un componente o un paquete de NuGet que no se ha compilado con la API unificada. Debe eliminar y volver a agregar todos los componentes y NuGet paquetes. Si esto no soluciona el error, la biblioteca externa puede todavía no admite la API unificada.


**MT0034 de error: No se puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS - 'Xamarin.iOS.dll' hace referencia explícitamente, mientras que 'monotouch.dll' hace referencia a ' Xamarin.Mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Corrección: Elimine el componente que está causando este error y vuelva a agregar al proyecto.


**Error CS0234: El nombre de tipo o espacio de nombres 'Base' no existe en el espacio de nombres 'MonoTouch'. ¿Falta una referencia de ensamblado?**

Corrección: La herramienta de migración automática en Visual Studio para Mac *debe* actualizar todo `MonoTouch.Foundation` hace referencia a `Foundation`; sin embargo, en algunos casos deberá actualizarse manualmente. Pueden aparecer errores similares para los otros espacios de nombres contenidas previamente en `MonoTouch`, como `UIKit`.



**Error CS0266: No se puede convertir implícitamente el tipo 'double' en 'System.float'**

Corrección: cambiar el tipo y se convierte en `nfloat`. Este error puede producirse también para los demás tipos con equivalentes de 64 bits (como `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```


**Error CS0266: No se puede convertir implícitamente el tipo 'CoreGraphics.CGRect' a 'System.Drawing.RectangleF'. Existe una conversión explícita (¿falta una conversión?)**

Corrección: Cambiar instancias `RectangleF` a `CGRect`, `SizeF` a `CGSize`, y `PointF` a `CGPoint`. El espacio de nombres `using System.Drawing;` debe reemplazarse por `using CoreGraphics;` (si aún no está presente).



**Error CS1502: sobrecargados de la mejor coincidencia de método para ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' tiene algunos argumentos no válidos**

Corrección: Cambiar el tipo de matriz a `nfloat[]` y explícitamente convertidos `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```


**Error CS0115: 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' está marcado como una invalidación pero ningún método adecuado que se encontró que reemplazar**

Corrección: Cambiar los tipos de parámetro y valor devueltos a `nint`. Esto suele ocurrir en los reemplazos de método como las de `UITableViewSource`, incluido `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etcetera.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```


**Error CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**


Corrección: Cuando el tipo de valor devuelto se cambia a `nint`, convertir el valor devuelto a `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```



**Error CS1061: Tipo 'CoreGraphics.CGPath' no contiene una definición para 'AddElipseInRect'**

Corrección: Comprobar la ortografía en `AddEllipseInRect`. Otros cambios de nombre incluyen:

* Cambiar 'Color.Black' a `NSColor.Black`.
* Cambiar MapKit 'AddAnnotation' a `AddAnnotations`.
* Cambiar AVFoundation 'DataUsingEncoding' a `Encode`.
* Cambiar AVFoundation 'AVMetadataObject.TypeQRCode' a `AVMetadataObjectType.QRCode`.
* Cambiar AVFoundation 'VideoSettings' a `WeakVideoSettings`.
* Cambiar PopViewControllerAnimated a `PopViewController`.
* Cambiar CoreGraphics 'CGBitmapContext.SetRGBFillColor' a `SetFillColor`.

**Error CS0546: no se puede reemplazar porque 'MapKit.MKAnnotation.Coordinate' no tiene un descriptor de acceso set reemplazable (error CS0546)**

Al crear una anotación personalizada mediante la creación de subclases MKAnnotation el campo Coordinate no tiene ningún establecedor, sólo un captador.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Agregar un campo para realizar un seguimiento de la coordenada
* devolver este campo en el captador de la propiedad de coordenadas
* Invalide el método SetCoordinate y establezca el campo
* Llamar a SetCoordinate en el constructor con el parámetro pasado en coordenadas

Debe ser similar al siguiente:

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

- [Actualizar aplicaciones](~/cross-platform/macios/unified/updating-apps.md)
- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Actualizar aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Actualizar aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Actualizar enlaces](~/cross-platform/macios/unified/update-binding.md)
- [Trabajar con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
