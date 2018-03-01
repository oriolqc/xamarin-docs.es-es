---
title: "Barra de navegación"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: 396ed31cba336976342a8dfb26f31eeda20cf494
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="navigation-bar"></a>Barra de navegación

Android 4 introdujo una nueva característica de interfaz de usuario de sistema denominada una *barra de navegación*, lo que proporciona controles de navegación en los dispositivos que no incluyen botones de hardware para **inicio**, **atrás** , y **menú**.
Captura de pantalla siguiente muestra la barra de navegación de un dispositivo de Nexus primos:

 [ ![Ejemplo de una barra de navegación de Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png)

Existen varios nuevos indicadores que controlan la visibilidad de la barra de navegación y sus controles, así como la visibilidad de la barra de sistema que se introdujo en Android 3. Las marcas se definen en la `Android.View.View` clase y se enumeran a continuación:

-   `SystemUiFlagVisible` &ndash; Se hace visible la barra de navegación. 
-   `SystemUiFlagLowProfile` &ndash; DIMS los controles en la barra de navegación. 
-   `SystemUiFlagHideNavigation` &ndash; Oculta la barra de navegación. 


Estas marcas se pueden aplicar a cualquier vista de la jerarquía de vista estableciendo la `SystemUiVisibility` propiedad. Si varias vistas tienen esta propiedad establecida, el sistema las combina con una operación OR y las aplica siempre y cuando la ventana en la que se establecen las marcas conserva el foco. Cuando se quita una vista, también se quitarán todas las marcas que estableció.

En el ejemplo siguiente se muestra una aplicación sencilla que haga clic en cualquiera de los botones de cambiar el `SystemUiVisibility`:

 [ ![Capturas de pantalla que muestra Visible, el perfil de baja y SystemUiVisibility ocultos](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png)

El código para cambiar la `SystemUiVisibility` establece la propiedad en un `TextView` de cada botón, haga clic en controlador de eventos tal y como se muestra a continuación:

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

Además, un `SystemUiVisibility` cambiar genera un `SystemUiVisibilityChange` eventos. Igual que el valor de la `SystemUiVisibility` propiedad, un controlador para el `SystemUiVisibilityChange` eventos se pueden registrar para cualquier vista de la jerarquía. Por ejemplo, el código siguiente utiliza el `TextView` instancia registrarse para el evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Vínculos relacionados

- [SystemUIVisibilityDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
