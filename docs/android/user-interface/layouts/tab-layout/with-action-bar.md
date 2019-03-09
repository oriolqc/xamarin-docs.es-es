---
title: Diseños con pestañas con ActionBar
description: Esta guía presenta y explica cómo usar las APIs ActionBar para crear una interfaz de usuario con pestañas en una aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 6ce8099aa4230a11a12f4fe8aeffe850f9ef2ce9
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671005"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Diseños con pestañas con ActionBar

_Esta guía presenta y explica cómo usar las APIs ActionBar para crear una interfaz de usuario con pestañas en una aplicación de Xamarin.Android._


## <a name="overview"></a>Información general

La barra de acciones es un patrón de interfaz de usuario de Android que se usa para proporcionar una interfaz de usuario coherente para las características clave, como pestañas, identidad de aplicación, los menús y búsqueda. En Android 3.0 (API nivel 11), Google introdujo las APIs ActionBar a la plataforma Android. Las APIs ActionBar presentar los temas de la interfaz de usuario para proporcionar una apariencia coherente y clases que permiten a las interfaces de usuario con pestañas. Esta guía describe cómo agregar pestañas de la barra de acciones a una aplicación de Xamarin.Android. También se explica cómo usar el v7 Android Support Library a restituir ActionBar fichas para aplicaciones de Xamarin.Android destinadas a Android 2.1 para Android 2.3. 

Tenga en cuenta que `Toolbar` es un componente de barra de acción más reciente y más generales que debe usar en lugar de `ActionBar` (`Toolbar` fue diseñado para reemplazar `ActionBar`). Para obtener más información, consulte [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisitos

Cualquier aplicación de Xamarin.Android que tiene como destino el nivel de API 11 (Android 3.0) o superior tiene acceso a las APIs de ActionBar como parte de las API nativas de Android. 

Algunas de las APIs ActionBar se han trasladado volver al nivel de API 7 (Android 2.1) y están disponibles a través de la [V7 AppCompat biblioteca](https://developer.android.com/tools/support-library/features.html#v7-appcompat), que está disponible para aplicaciones de Xamarin.Android a través de la [biblioteca de compatibilidad de Android de Xamarin - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete.



## <a name="introducing-tabs-in-the-actionbar"></a>Introducción a las pestañas en la barra de acciones

La barra de acciones intenta mostrar todas sus pestañas simultáneamente y todas las fichas Igualar tamaño según el ancho de la etiqueta de ficha más amplio. Esto se muestra en la captura de pantalla siguiente: 

![Captura de pantalla de ejemplo de la barra de acciones con todas las fichas de igual tamaño que se muestra](with-action-bar-images/image1.png)

Cuando la barra de acciones no puede mostrar todas las fichas, configurará las fichas en una vista puede desplazar horizontalmente. El usuario puede pasar el dedo izquierda o derecha para ver las demás pestañas. Esta captura de pantalla de Google Play muestra un ejemplo de esto: 

![Captura de pantalla de ejemplo de pestañas en una vista puede desplazar horizontalmente](with-action-bar-images/image2.png)

Cada pestaña en la barra de acciones se debe asociar con un [ *fragmento*](~/android/platform/fragments/index.md). Cuando el usuario selecciona una ficha, la aplicación mostrará el fragmento que está asociado a la pestaña. La barra de acciones no es responsable de mostrar el fragmento correspondiente al usuario. En su lugar, la barra de acciones le notificará una aplicación acerca de los cambios de estado en una pestaña a través de una clase que implementa la interfaz ActionBar.ITabListener. Esta interfaz proporciona tres métodos de devolución de llamada que se invocará Android cuando cambia el estado de la pestaña: 

-  **OnTabSelected** : este método se llama cuando el usuario selecciona la pestaña. Debe mostrar el fragmento.

-  **OnTabReselected** : este método se llama cuando la pestaña ya está seleccionada, pero se vuelve a estar activada por el usuario. Esta devolución de llamada se utiliza normalmente para la actualización o actualizar el fragmento de muestra.

-  **OnTabUnselected** : este método se llama cuando el usuario selecciona otra pestaña. Esta devolución de llamada se usa para guardar el estado en el fragmento mostrado antes de desaparecer.

Xamarin.Android ajusta el `ActionBar.ITabListener` con eventos en el `ActionBar.Tab` clase. Las aplicaciones pueden asignar a controladores de eventos a uno o varios de estos eventos. Hay tres eventos (una para cada método en `ActionBar.ITabListener`) que se producirá una pestaña de la barra de acción: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Agregar pestañas a la barra de acciones

La barra de acciones es nativo para Android 3.0 (API nivel 11) y versiones posteriores y está disponible para cualquier aplicación de Xamarin.Android que tenga como destino de esta API, como mínimo. 

Los siguientes pasos muestran cómo agregar fichas de la barra de acciones a una actividad de Android: 

1. En el `OnCreate` método de una actividad &ndash; *antes de inicializar los widgets de interfaz de usuario* &ndash; una aplicación debe establecer el `NavigationMode` en el `ActionBar` a `ActionBar.NavigationModeTabs` tal como se muestra en este código fragmento de código:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Crear una nueva pestaña con `ActionBar.NewTab()`.

3. Asignar controladores de eventos o proporcionar una personalizada `ActionBar.ITabListener` implementación que responderá a los eventos que se producen cuando el usuario interactúa con las pestañas de la barra de acciones.

4. La pestaña que se creó en el paso anterior para agregar el `ActionBar`.


El código siguiente es un ejemplo del uso de estos pasos para agregar pestañas a una aplicación que usa controladores de eventos para responder a los cambios de estado: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>Frente a los controladores de eventos ActionBar.ITabListener

Las aplicaciones deben usar controladores de eventos y `ActionBar.ITabListener` para diferentes escenarios. Controladores de eventos ofrecen una cierta comodidad sintáctica; ahorrará tener que crear una clase e implementar `ActionBar.ITabListener`. Esta conveniencia vienen con cierto costo &ndash; Xamarin.Android realiza esta transformación para usted, crear una clase e implementar `ActionBar.ITabListener` para usted. Esto está bien cuando una aplicación tiene un número limitado de pestañas. 

Cuando se trabaja con muchas pestañas, o compartir la funcionalidad común entre las fichas de la barra de acciones, puede ser más eficiente en cuanto a memoria y rendimiento para crear una clase personalizada que implementa `ActionBar.ITabListener`y compartir una sola instancia de la clase. Esto reducirá el número de GREF que esté usando una aplicación de Xamarin.Android. 



### <a name="backwards-compatibility-for-older-devices"></a>Con las versiones anteriores compatibilidad para dispositivos antiguos

El [biblioteca de compatibilidad de Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back puertos pestañas de la barra de acciones para Android 2.1 (API de nivel 7). Las pestañas son accesibles en una aplicación Xamarin.Android una vez que este componente se ha agregado al proyecto.

Para usar la barra de acciones, una actividad debe subclase `ActionBarActivity` y utilizar el tema de AppCompat tal como se muestra en el siguiente fragmento de código:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Una actividad puede obtener una referencia a su barra de acciones desde el `ActionBarActivity.SupportingActionBar` propiedad. El fragmento de código siguiente muestra un ejemplo de la configuración de la barra de acciones en una actividad:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>Resumen

En esta guía se explica cómo crear una interfaz de usuario con pestañas en Xamarin.Android mediante la barra de acciones. Analizamos cómo agregar pestañas a la barra de acciones y cómo una actividad puede interactuar con eventos de pestaña a través de la `ActionBar.ITabListener` interfaz. También hemos visto cómo la biblioteca de compatibilidad de Android v7 AppCompat paquete backports ActionBar las pestañas en versiones anteriores de Android. 


## <a name="related-links"></a>Vínculos relacionados

- [ActionBarTabs (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Fragmentos](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Patrón de la barra de acciones](https://developer.android.com/design/patterns/actionbar.html)
- [AppCompat de Android v7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Paquete de NuGet de soporte técnico de Xamarin.Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
