---
title: ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9d9bef6d1a0817abc12b5a9bd266b1e1e7d38348
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949639"
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>Información general

Cuando se usa `TabActivity`, el código para crear los iconos de ficha no tiene ningún efecto cuando se ejecuta en el marco de trabajo de Android 4.0. Aunque funcionalmente también funciona como lo hacía en versiones de Android anteriores a 2.3, la `TabActivity` propia clase ha quedado obsoleto en 4.0. Se ha introducido una nueva forma de crear una interfaz con fichas que utiliza la barra de acciones, lo que hablaremos a continuación.


## <a name="action-bar-tabs"></a>Pestañas de la barra de acciones

La barra de acciones incluye compatibilidad para agregar interfaces con pestañas en Android 4.0.
Captura de pantalla siguiente muestra un ejemplo de dicha interfaz.

[![Captura de pantalla de la aplicación que se ejecuta en un emulador; se muestran dos pestañas](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Para crear pestañas en la barra de acciones, primero es necesario establecer su `NavigationMode` propiedad para admitir las pestañas. En Android 4 un `ActionBar` propiedad está disponible en la clase de actividad, podemos usar para establecer el `NavigationMode` similar al siguiente:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Una vez hecho esto, podemos crear una pestaña mediante una llamada a la `NewTab` método en la barra de acciones. Con esta instancia de la ficha, podemos llamar a la `SetText` y `SetIcon` métodos para establecer la pestaña texto de etiqueta y el icono; estas llamadas se realizan en el orden en el código se muestra a continuación:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Antes de que podemos agregar la pestaña sin embargo, se debe controlar la `TabSelected` eventos. En este controlador, podemos crear el contenido de la ficha. Las fichas de la barra de acciones están diseñadas para trabajar con *fragmentos*, que son clases que representan una parte de la interfaz de usuario en una actividad. En este ejemplo, la vista del fragmento contiene un único `TextView`, que se infla en nuestra `Fragment` subclase similar al siguiente:

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);
       
        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

El argumento de evento que se pasa en el `TabSelected` evento es de tipo `TabEventArgs`, que incluye un `FragmentTransaction` propiedad que podemos usar para agregar el fragmento tal como se muestra a continuación:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Por último, podemos agregar la pestaña a la barra de acciones mediante una llamada a la `AddTab` método tal como se muestra en este código:

```csharp
this.ActionBar.AddTab (tab);
```

Para obtener un ejemplo completo, vea el *HelloTabsICS* proyecto en el código de ejemplo para este documento.


## <a name="shareactionprovider"></a>ShareActionProvider

La `ShareActionProvider` clase permite una acción de uso compartida que tenga lugar de una barra de acciones. Se encarga de crear una vista de acción con una lista de aplicaciones que puede controlar un intento para compartir y mantiene un historial de las aplicaciones usadas anteriormente para facilitar el acceso a ellos más adelante desde la barra de acciones. Esto permite que las aplicaciones compartir datos a través de una experiencia de usuario que sea coherente en Android.


### <a name="image-sharing-example"></a>Ejemplo de uso compartido de imagen

Por ejemplo, a continuación es una captura de pantalla de una barra de acción con un elemento de menú para compartir una imagen (procedente del [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) ejemplo). Cuando el usuario puntea el elemento de menú en la barra de acciones, el ShareActionProvider carga la aplicación para controlar una intención que está asociada el `ShareActionProvider`. En este ejemplo, la aplicación de mensajería ya se utilizó anteriormente, por lo que se presenta en la barra de acciones.

[![Captura de pantalla del icono de la aplicación en la barra de acciones de mensajería](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Cuando el usuario hace clic en el elemento de la barra de acciones, se inicia la aplicación de mensajería que contiene la imagen compartida, como se muestra a continuación:

[![Captura de pantalla de la aplicación de mensajería Mostrar imagen monkey](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Especifica la acción de la clase de proveedor

Para usar el `ShareActionProvider`, establezca el `android:actionProviderClass` atributo en un elemento de menú en el XML para el menú de la barra de acción como sigue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>El menú que infla

Para aumentar el menú, se muestra cómo reemplazar `OnCreateOptionsMenu` en la subclase de actividad. Una vez que tenemos una referencia al menú, podemos obtener el `ShareActionProvider` desde el `ActionProvider` propiedad del elemento de menú y, a continuación, use el método SetShareIntent para establecer el `ShareActionProvider`de intención, tal como se muestra a continuación:

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       
           
    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>Creación de la intención

El `ShareActionProvider` usará la intención, pasa a la `SetShareIntent` método en el código anterior, para iniciar la actividad apropiada. En este caso, creamos una intención para enviar una imagen mediante el código siguiente:

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

La imagen en el ejemplo de código anterior se incluye como un recurso con la aplicación y copia en una ubicación públicamente accesible cuando se crea la actividad, por lo que estarán disponibles para otras aplicaciones, como la aplicación de mensajería. El código de ejemplo que acompaña este artículo contiene el código fuente completo de este ejemplo, que muestra su uso.



## <a name="related-links"></a>Vínculos relacionados

- [Hola pestañas ICS (ejemplo)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [Demostración de ShareActionProvider (ejemplo)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Introducción a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android de 4.0](https://developer.android.com/sdk/android-4.0.html)
