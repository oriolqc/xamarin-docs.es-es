---
title: ActionBar
ms.topic: article
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 64a5ac7e0c448205da66f9790a506ca34a944140
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>Información general

Cuando se usa `TabActivity`, el código para crear los iconos de ficha no tiene ningún efecto cuando se ejecuta en el marco de trabajo de Android 4.0. Aunque funcionalmente funciona igual que en las versiones de Android anteriores 2.3, la `TabActivity` propia clase está en desuso en 4.0. Se ha incorporado una nueva manera de crear una interfaz con pestañas que usa la barra de acción, que se explica a continuación.


## <a name="action-bar-tabs"></a>Pestañas de la barra de acciones

La barra de acciones incluye compatibilidad para agregar interfaces con pestañas en Android 4.0.
Captura de pantalla siguiente muestra un ejemplo de dicha interfaz.

[![Captura de pantalla de aplicación que se ejecuta en un emulador; se muestran dos pestañas](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Para crear pestañas en la barra de acciones, primero es necesario establecer su `NavigationMode` propiedad para admitir las pestañas. En Android 4, un `ActionBar` propiedad está disponible en la clase de actividad, que podemos usar para establecer el `NavigationMode` similar a la siguiente:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Una vez hecho esto, podemos crear una pestaña mediante una llamada a la `NewTab` método en la barra de acciones. Con esta instancia de la pestaña, podemos llamar a la `SetText` y `SetIcon` métodos para establecer el texto de la etiqueta y el icono; la pestaña estas llamadas se realizan en el orden en el código se muestra a continuación:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Antes de que podemos agregar la pestaña sin embargo, es necesario controlar la `TabSelected` eventos. En este controlador, podemos crear el contenido de la ficha. Las fichas de la barra de acciones están diseñadas para trabajar con *fragmentos*, que son clases que representan una parte de la interfaz de usuario en una actividad. En este ejemplo, la vista del fragmento contiene un único `TextView`, que se aumentar en nuestro `Fragment` subclase similar al siguiente:

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

El argumento de evento que se pasa en el `TabSelected` evento es de tipo `TabEventArgs`, que incluye un `FragmentTransaction` propiedad que podemos usar para agregar el fragmento tal y como se muestra a continuación:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Por último, podemos agregar la pestaña en la barra de acciones mediante una llamada a la `AddTab` método tal como se muestra en este código:

```csharp
this.ActionBar.AddTab (tab);
```

Para obtener un ejemplo completo, vea el *HelloTabsICS* proyecto en el código de ejemplo de este documento.


## <a name="shareactionprovider"></a>ShareActionProvider

La `ShareActionProvider` clase habilita una acción de uso compartida que se realicen en una barra de acción. Se encarga de crear una vista de acción con una lista de las aplicaciones que puede controlar un intento de uso compartido y mantiene un historial de las aplicaciones usadas anteriormente para facilitar el acceso a ellos más adelante en la barra de acción. Esto permite a las aplicaciones compartir datos a través de una experiencia de usuario coherente a lo largo de Android.


### <a name="image-sharing-example"></a>Ejemplo de uso compartido de imagen

Por ejemplo, a continuación se muestra una captura de pantalla de una barra de acción con un elemento de menú para compartir una imagen (procedente del [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) ejemplo). Cuando el usuario puntea el elemento de menú en la barra de acción, el ShareActionProvider carga la aplicación para controlar una intención de que está asociado el `ShareActionProvider`. En este ejemplo, la aplicación de mensajería se ha utilizado previamente, por lo que se presentan en la barra de acción.

[![Captura de pantalla de mensajería de icono de la aplicación en la barra de acciones](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Cuando el usuario hace clic en el elemento de la barra de acciones, se inicia la aplicación de mensajería que contiene la imagen compartida, tal y como se muestra a continuación:

[![Captura de pantalla de aplicación de mensajería Mostrar imagen mono](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Especifica la acción de clase de proveedor

Para usar el `ShareActionProvider`, establezca el `android:actionProviderClass` del atributo en un elemento de menú en el archivo XML para el menú de la barra de acción como se indica a continuación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>El menú lo que infla

Para aumentar el menú, reemplazamos `OnCreateOptionsMenu` en la subclase de actividad. Una vez que tenemos una referencia al menú, podemos obtener la `ShareActionProvider` desde el `ActionProvider` propiedad del elemento de menú y, a continuación, use el método SetShareIntent para establecer el `ShareActionProvider`del intento, tal y como se muestra a continuación:

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


### <a name="creating-the-intent"></a>Crear la intención

El `ShareActionProvider` usará la intención, pasada a la `SetShareIntent` método en el código anterior, para iniciar la actividad apropiada. En este caso, creamos un intento para enviar una imagen mediante el código siguiente:

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

La imagen en el ejemplo de código anterior se incluye como un recurso con la aplicación y se copian en una ubicación públicamente accesible cuando se crea la actividad, por lo que será accesible a otras aplicaciones, como la aplicación de mensajería. El código de ejemplo que se incluye en este artículo contiene el código fuente completo de este ejemplo, que muestra su uso.



## <a name="related-links"></a>Vínculos relacionados

- [Hola pestañas ICS (ejemplo)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [Demostración de ShareActionProvider (ejemplo)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
