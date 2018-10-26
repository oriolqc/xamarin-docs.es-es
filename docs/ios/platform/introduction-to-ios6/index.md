---
title: Introducción a iOS 6
description: Este documento incluye vínculos a guías que describen las características introducidas en iOS 6. Las vistas de colección, PassKit, el marco de redes sociales, y los cambios en StoreKit se tratan.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 25926d82e060b91b007da9c2295b328cb049e8df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103873"
---
# <a name="introduction-to-ios-6"></a>Introducción a iOS 6

_iOS 6 incluye una serie de nuevas tecnologías para desarrollar aplicaciones de Xamarin.iOS 6 aporta a C# a los desarrolladores._

[ ![](images/ios6-large.jpg "El logotipo de iOS 6")](images/ios6-large.jpg#lightbox)

Con iOS 6 y 6 de Xamarin.iOS, los desarrolladores ahora tienen una gran cantidad de funcionalidad a su disposición para crear aplicaciones de iOS, las incluidas que iPhone 5 de destino.
Este documento enumeran algunas de las características nuevas más interesantes que están disponibles y vínculos a artículos de cada tema. Además se mencionan en un par de cambios que será importante, ya que los desarrolladores mueven a iOS 6 y la nueva resolución de iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md)

Las vistas de colección permitir que el contenido se muestren con diseños arbitrarios. Le permiten crear fácilmente los diseños de cuadrícula de fábrica, al tiempo que admite también los diseños personalizados. Para obtener más información, vea, el [Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)guía.


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Introduction to PassKit](~/ios/platform/passkit.md) (Introducción a PassKit)

El marco de PassKit permite a las aplicaciones interactúan con pasadas digitales que se administran en la aplicación Libreta. Para obtener más información, vea, el [Introducción a la guía Kit pasar](~/ios/platform/passkit.md).


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Introducción a eventkit de código](~/ios/platform/eventkit.md)

El marco de trabajo eventkit de código proporciona una manera de obtener acceso a los calendarios, los eventos de calendario y los datos de recordatorios que almacena la base de datos de calendario. Acceso a los calendarios y el calendario de eventos ha estado disponible desde iOS 4, pero iOS 6 ahora expone el acceso a datos de recordatorios. Para obtener más información, consulte el [me](~/ios/platform/eventkit.md) [ntroduction a EventKit](~/ios/platform/eventkit.md) guía.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introducción a las redes sociales](~/ios/platform/social-framework.md)

El marco de redes sociales proporciona una API unificada para interactuar con las redes sociales como Facebook y Twitter, así como SinaWeibo para los usuarios en China. Para obtener más información, vea, el [Introducción a las redes sociales](~/ios/platform/social-framework.md) guía.


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[Cambios en StoreKit](changes-to-storekit.md)

Apple ha presentado dos nuevas características en el Kit de Store: comprar y descargar contenido de App Store desde dentro de la aplicación o iTunes y hospeda los archivos de contenido de la aplicación las compras!. Para obtener más información, vea, el [cambia a Store Kit](changes-to-storekit.md) guía.


## <a name="other-changes"></a>Otros cambios


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload y ViewDidUnload en desuso

El `ViewWillUnload` y `ViewDidUnload` métodos de `UIViewController` ya no se llaman en iOS 6. En versiones anteriores de iOS, estos métodos es posible que se usaron las aplicaciones para guardar el estado antes de la descarga de una vista y el código de limpieza, respectivamente.

Por ejemplo, Visual Studio para Mac crearía un método llamado `ReleaseDesignerOutlets`, como se muestra a continuación, lo que, a continuación, se llamaría de `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Sin embargo, en iOS 6, ya no es necesario llamar a `ReleaseDesignerOutlets`.   
   
   
   
Limpieza del código, deben usar las aplicaciones de iOS 6 `DidReceiveMemoryWarning`. Sin embargo, código que llama a `Dispose` deben usarse con moderación y únicamente para la memoria intensiva objetos como se muestra a continuación:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Nuevamente, una llamada a `Dispose` como arriba rara vez debería ser necesario. En general, la mayoría de las aplicaciones deben hacer es quitar controladores de eventos.

En el caso de guardar el estado, las aplicaciones pueden realizar esto en `ViewWillDisappear` y `ViewDidDisappear` en lugar de `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 resolución

dispositivos iPhone 5 tienen una resolución de 640 x 1136. Las aplicaciones que como destino versiones anteriores de iOS aparecerá letterboxed cuando se ejecuta en un iPhone 5 y como se muestra a continuación:

 [![](images/01-letterboxed.png "Las aplicaciones que como destino versiones anteriores de iOS aparecerá letterboxed cuando se ejecuta en un iPhone 5")](images/01-letterboxed.png#lightbox)

Para la aplicación que aparezca la pantalla completa en iPhone 5, basta con agregar una imagen denominada `Default-568h@2x.png` tener una resolución de 640 x 1136. Captura de pantalla siguiente muestra la aplicación en ejecución después de esta imagen se ha incluido:

 [![](images/02-fullscreen.png "Esta captura de pantalla muestra la aplicación en ejecución después de esta imagen se ha incluido")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Creación de subclases de UINavigationBar

En iOS 6 `UINavigationBar` pueden crear subclases. Esto permite un control adicional sobre la apariencia y comportamiento de la `UINavigationBar`. Por ejemplo, las aplicaciones pueden subclase para agregar las subvistas, animar las vistas y modificar los límites de la `UINavigationBar`.

El código siguiente muestra un ejemplo de una subclase `UINavigationBar` que agrega un `UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Para agregar una subclase `UINavigationBar` a un `UINavigationController`, utilice el `UINavigationController` constructor que toma el tipo de la `UINavigationBar` y `UIToolbar`, tal y como se muestra a continuación:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Mediante este `UINavigationBar` subclase da como resultado la vista de imagen que se muestra como se muestra en la captura de pantalla siguiente:

 [![](images/03-navbar.png "Con este resultado de la subclase UINavigationBar en la vista de imagen que se muestra como se muestra en esta captura de pantalla")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientación de la interfaz

Antes de iOS podrían invalidar 6 aplicaciones `ShouldAutorotateToInterfaceOrientation`, devolver true para cualquier orientaciones controlador determinado admite. Por ejemplo, el código siguiente podría usarse para admitir sólo vertical:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

En iOS 6 `ShouldAutorotateToInterfaceOrientation` está en desuso.
En su lugar, las aplicaciones pueden invalidar `GetSupportedInterfaceOrientations` en el controlador de vista raíz, como se muestra a continuación:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

En iPad, el valor predeterminado es todas las cuatro orientaciones si `GetSupportedInterfaceOrientation` no está implementada. En iPhone y iPod Touch, el valor predeterminado es todas las orientaciones excepto `PortraitUpsideDown`.
