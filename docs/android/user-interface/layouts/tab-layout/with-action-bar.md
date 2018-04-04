---
title: Diseños con pestañas con el ActionBar
description: Esta guía presenta y explica cómo usar las APIs ActionBar para crear una interfaz de usuario con pestañas en una aplicación Xamarin.Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 5956cd13708f4e7e73926fc01e6142d9cf4a8edb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Diseños con pestañas con el ActionBar

_Esta guía presenta y explica cómo usar las APIs ActionBar para crear una interfaz de usuario con pestañas en una aplicación Xamarin.Android._


## <a name="overview"></a>Información general

La barra de acción es un patrón de interfaz de usuario de Android que se usa para proporcionar una interfaz de usuario coherente para las características clave como pestañas, identidad de la aplicación, los menús y la búsqueda. En 3.0, Android (nivel de API 11), Google introdujo las APIs ActionBar para la plataforma Android. Las APIs ActionBar introducir temas de la interfaz de usuario para proporcionar una apariencia coherente y clases que permiten a las interfaces de usuario con pestañas. Esta guía describe cómo agregar pestañas de la barra de acciones a una aplicación Xamarin.Android. También explica cómo utilizar el v7 Android biblioteca de compatibilidad con pestañas de ActionBar de atrás para aplicaciones de Xamarin.Android destinadas a Android 2.1 para Android 2.3. 

Tenga en cuenta que `Toolbar` es un componente de la barra de acción más reciente y más generalizada que debe usar en lugar de `ActionBar` (`Toolbar` se ha diseñado para reemplazar `ActionBar`). Para obtener más información, consulte [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisitos

Cualquier aplicación Xamarin.Android que tiene como destino el nivel de API 11 (Android 3.0) o posterior tiene acceso a las APIs de ActionBar como parte de las API nativas de Android. 

Algunas de las APIs ActionBar se han trasladado volver a nivel de API 7 (Android 2.1) y están disponibles a través de la [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que estará disponible para las aplicaciones de Xamarin.Android a través de la [biblioteca de compatibilidad de Android en Xamarin - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete.



## <a name="introducing-tabs-in-the-actionbar"></a>Introducción a las pestañas en el ActionBar

La barra de acciones intenta mostrar todas sus pestañas simultáneamente y realizar todas las pestañas de igual tamaño basándose en el ancho de la etiqueta de ficha más amplia. Esto se muestra en la captura de pantalla siguiente: 

![Captura de pantalla de ejemplo de ActionBar con todas las fichas de igual tamaño que se muestra](with-action-bar-images/image1.png)

Cuando el ActionBar no puede mostrar todas las fichas, configurará las pestañas en una vista puede desplazar horizontalmente. El usuario puede deslizar rápidamente izquierda o derecha para ver las demás pestañas. Esta captura de pantalla de Google Play muestra un ejemplo de esto: 

![Captura de pantalla de ejemplo de pestañas en una vista puede desplazar horizontalmente](with-action-bar-images/image2.png)

Cada pestaña en la barra de acciones se debe asociar con un [ *fragmento*](~/android/platform/fragments/index.md). Cuando el usuario selecciona una ficha, la aplicación mostrará el fragmento que está asociado a la ficha. El ActionBar no es responsable de mostrar el fragmento correspondiente al usuario. En su lugar, el ActionBar le notificará una aplicación acerca de los cambios de estado en una pestaña a través de una clase que implementa la interfaz ActionBar.ITabListener. Esta interfaz proporciona tres métodos de devolución de llamada que va a invocar Android cuando cambia el estado de la pestaña: 

-  **OnTabSelected** -llama a este método cuando el usuario selecciona la pestaña. Se debería mostrar el fragmento.

-  **OnTabReselected** -llama a este método cuando la pestaña ya está seleccionada, pero se vuelve a estar activada por el usuario. Esta devolución de llamada se utiliza normalmente para la actualización o actualizar el fragmento mostrado.

-  **OnTabUnselected** -llama a este método cuando el usuario selecciona otra ficha. Esta devolución de llamada se usa para guardar el estado en el fragmento mostrado antes, este desaparece.

Xamarin.Android ajusta el `ActionBar.ITabListener` con eventos en el `ActionBar.Tab` clase. Las aplicaciones pueden asignar a controladores de eventos a uno o varios de estos eventos. Hay tres eventos (una para cada método en `ActionBar.ITabListener`) que se producirá una pestaña de la barra de acción: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Agregar pestañas a la ActionBar

El ActionBar es nativo para Android 3.0 (API nivel 11) y versiones posteriores y está disponible para cualquier aplicación Xamarin.Android destinado a esta API como mínimo. 

Los siguientes pasos muestran cómo agregar pestañas ActionBar a una actividad Android: 

1. En el `OnCreate` método de una actividad &ndash; *antes de inicializar los widgets de interfaz de usuario* &ndash; una aplicación debe establecer el `NavigationMode` en el `ActionBar` a `ActionBar.NavigationModeTabs` tal como se muestra en este código fragmento de código:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Crear una nueva pestaña con `ActionBar.NewTab()`.

3. Asignar controladores de eventos o proporcionar un personalizado `ActionBar.ITabListener` implementación que responderá a los eventos que se producen cuando el usuario interactúa con las pestañas de ActionBar.

4. Agregar la pestaña que se creó en el paso anterior para el `ActionBar`.


El código siguiente es un ejemplo del uso de estos pasos para agregar pestañas a una aplicación que utiliza los controladores de eventos para responder a los cambios de estado: 

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
                       }
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       }
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>Controladores de eventos y ActionBar.ITabListener

Las aplicaciones deben utilizar controladores de eventos y `ActionBar.ITabListener` para diferentes escenarios. Controladores de eventos ofrecen una cierta cantidad de comodidad sintáctica; evitarle tener que crear una clase e implementar `ActionBar.ITabListener`. Esta conveniencia vienen con un costo &ndash; Xamarin.Android realiza esta transformación para usted, crear una clase e implementar `ActionBar.ITabListener` para usted. Esto es correcto cuando una aplicación tiene un número limitado de pestañas. 

Cuando se trabaja con muchas pestañas, o compartir la funcionalidad común entre las fichas de ActionBar, puede resultar más eficaces en cuanto a memoria y rendimiento para crear una clase personalizada que implementa `ActionBar.ITabListener`y compartir una sola instancia de la clase. Esto reducirá el número de GREF que está usando una aplicación Xamarin.Android. 



### <a name="backwards-compatibility-for-older-devices"></a>Con las versiones anteriores compatibilidad para dispositivos antiguos

El [biblioteca de compatibilidad de Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back puertos ActionBar pestañas para Android 2.1 (API nivel 7). Las pestañas son accesibles en una aplicación Xamarin.Android una vez que este componente se ha agregado al proyecto.

Para usar el ActionBar, una actividad debe subclase `ActionBarActivity` y utilizar el tema de AppCompat tal como se muestra en el fragmento de código siguiente:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Una actividad puede obtener una referencia a su ActionBar desde el `ActionBarActivity.SupportingActionBar` propiedad. El fragmento de código siguiente muestra un ejemplo de cómo configurar la ActionBar en una actividad:

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

En esta guía se explicó cómo crear una interfaz de usuario con pestañas en un Xamarin.Android mediante el ActionBar. Hemos aprendido cómo agregar pestañas a la ActionBar y cómo una actividad puede interactuar con eventos de pestaña a través de la `ActionBar.ITabListener` interfaz. También hemos visto cómo backports de paquete de biblioteca de compatibilidad de Android v7 AppCompat el ActionBar pestañas en versiones anteriores de Android. 


## <a name="related-links"></a>Vínculos relacionados

- [ActionBarTabs (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Fragmentos](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Patrón de la barra de acciones](http://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Paquete de soporte técnico de Xamarin.Android biblioteca v7 AppCompat NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
