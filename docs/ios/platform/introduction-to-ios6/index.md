---
title: Introducción a iOS 6
description: iOS 6 incluye una serie de nuevas tecnologías para el desarrollo de aplicaciones, que Xamarin.iOS 6 ofrece a los desarrolladores de C#.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 8f3be80ffb8156c24c96b03fda8eac3907ca88bd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-ios-6"></a>Introducción a iOS 6

_iOS 6 incluye una serie de nuevas tecnologías para el desarrollo de aplicaciones, que Xamarin.iOS 6 ofrece a los desarrolladores de C#._

[ ![](images/ios6-large.jpg "El logotipo de iOS 6")](images/ios6-large.jpg#lightbox)

Con iOS 6 y 6 de Xamarin.iOS, los desarrolladores ahora tienen una gran cantidad de capacidad a su disposición para crear aplicaciones de iOS, los que incluidos esa iPhone 5 de destino.
Este documento enumeran algunas de las más nuevas y emocionantes características que están disponibles y vínculos a artículos de cada tema. Además, afecta a un par de cambios que será importante, ya que los desarrolladores mueven los iOS 6 y la nueva resolución de iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md)

Vistas de colección permiten el contenido que se mostrará mediante los diseños arbitrarios. Le permiten crear fácilmente los diseños de cuadrícula de fábrica, que la ejecución de los diseños personalizados. Para obtener más información, vea, el [Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)guía.


## <a name="introduction-to-pass-kitiosplatformpasskitmd"></a>[Introducción al Kit de pasar](~/ios/platform/passkit.md)

El marco de trabajo pasa Kit permite a las aplicaciones interactúan con pasadas digitales que se administran en la aplicación de la libreta. Para obtener más información, vea, el [Introducción a la Guía del Kit de pasar](~/ios/platform/passkit.md).


##  <a name="introduction-to-event-kitiosplatformeventkitmd"></a>[Introducción al Kit de eventos](~/ios/platform/eventkit.md)

El marco de trabajo de EventKit proporciona una manera de tener acceso a los calendarios, los eventos de calendario y datos de avisos que almacena la base de datos de calendario. Acceso a los calendarios y el calendario de eventos ha estado disponible desde iOS 4, pero iOS 6 ahora expone el acceso a datos de avisos. Para obtener más información, consulte el [I](~/ios/platform/eventkit.md) [ntroduction a EventKit](~/ios/platform/eventkit.md) guía.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introducción al marco de redes sociales](~/ios/platform/social-framework.md)

El marco de trabajo Social proporciona una API unificada para interactuar con redes sociales incluido Twitter y Facebook, así como SinaWeibo para los usuarios en China. Para obtener más información, vea, el [Introducción al marco sociales](~/ios/platform/social-framework.md) guía.


##  <a name="changes-to-store-kitchanges-to-storekitmd"></a>[Cambios para almacenar Kit](changes-to-storekit.md)

Apple ha introducido dos nuevas características en el Kit de almacén: comprar y descargar iTunes o contenido de la tienda de aplicaciones desde dentro de la aplicación y hospeda los archivos de contenido para las compras de la aplicación!. Para obtener más información, vea, el [cambia al almacén Kit](changes-to-storekit.md) guía.


## <a name="other-changes"></a>Otros cambios


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload y ViewDidUnload en desuso

El `ViewWillUnload` y `ViewDidUnload` métodos de `UIViewController` ya no se llaman en iOS 6. En versiones anteriores de iOS, estos métodos pueden haberse usados por las aplicaciones para guardar el estado que tenía antes de la descarga de una vista y el código de limpieza respectivamente.

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
   
   
   
Para el código de limpieza, deben usar las aplicaciones de iOS 6 `DidReceiveMemoryWarning`. Sin embargo, el código que llama a `Dispose` debe usarse con moderación y solo para uso intensivo objetos de memoria como se muestra a continuación:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Una vez más, al llamar a `Dispose` como arriba raramente debería ser necesario. En general, la mayoría de las aplicaciones deben hacer consiste en quitar controladores de eventos.

En el caso de guardar el estado, las aplicaciones pueden usar en `ViewWillDisappear` y `ViewDidDisappear` en lugar de `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 resolución

dispositivos iPhone 5 tienen una resolución de 640 x 1136. Aplicaciones destinados a versiones anteriores de iOS aparecerán letterboxed cuando se ejecuta en un iPhone 5, tal y como se muestra a continuación:

 [![](images/01-letterboxed.png "Aplicaciones destinados a versiones anteriores de iOS aparecerán letterboxed cuando se ejecuta en un iPhone 5")](images/01-letterboxed.png#lightbox)

Para la aplicación que aparezca la pantalla completa en iPhone 5, basta con agregar una imagen denominada `Default-568h@2x.png` con una resolución de 640 x 1136. Captura de pantalla siguiente muestra la aplicación en ejecución después de esta imagen se ha incluido:

 [![](images/02-fullscreen.png "Esta captura de pantalla muestra la aplicación en ejecución después de esta imagen se ha incluido")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Creación de subclases UINavigationBar

En iOS 6 `UINavigationBar` puede ser una subclase. Esto permite un control adicional sobre la apariencia y funcionamiento de la `UINavigationBar`. Por ejemplo, las aplicaciones pueden crear subclases de para agregar subvistas, animar esas vistas y modificar los límites de la `UINavigationBar`.

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

Para agregar una subclase `UINavigationBar` a una `UINavigationController`, use la `UINavigationController` constructor que toma el tipo de la `UINavigationBar` y `UIToolbar`, tal y como se muestra a continuación:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Mediante este `UINavigationBar` subclase da como resultado en la vista de la imagen que se muestra como se muestra en la siguiente captura de pantalla:

 [![](images/03-navbar.png "Con este resultado de la subclase UINavigationBar en la vista de imagen que se muestra como se muestra en esta captura de pantalla")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientación de la interfaz

Antes de iOS pudieron invalidar 6 aplicaciones `ShouldAutorotateToInterfaceOrientation`, devolver true para cualquier orientaciones controlador determinado admite. Por ejemplo, el código siguiente se utilizaría para admitir solo vertical:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

En iOS 6 `ShouldAutorotateToInterfaceOrientation` está en desuso.
En su lugar, las aplicaciones pueden suplantar `GetSupportedInterfaceOrientations` en el controlador de vista de raíz, tal y como se muestra a continuación:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

En iPad, el valor predeterminado es todas las cuatro orientaciones si `GetSupportedInterfaceOrientation` no está implementada. En el iPhone y iPod Touch, el valor predeterminado es todas las orientaciones excepto `PortraitUpsideDown`.
