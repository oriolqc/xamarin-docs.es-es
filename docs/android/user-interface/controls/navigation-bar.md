---
title: Barra de navegación
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: ce80fab39c814204631c5cc408c3f0ee99a329e6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110562"
---
# <a name="navigation-bar"></a>Barra de navegación

Android 4 introdujo una nueva característica de interfaz de usuario de sistema denominada una *barra de navegación*, que proporciona controles de navegación en los dispositivos que no incluyen los botones de hardware para **inicio**, **atrás** , y **menú**.
Captura de pantalla siguiente muestra la barra de navegación de un dispositivo Nexus primos:

 [![Ejemplo de una barra de navegación de Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Existen varias nuevas marcas que controlan la visibilidad de la barra de navegación y sus controles, así como la visibilidad de la barra de sistema que se introdujo en Android 3. Las marcas se definen en el `Android.View.View` clase y se enumeran a continuación:

-   `SystemUiFlagVisible` &ndash; Se hace visible la barra de navegación. 
-   `SystemUiFlagLowProfile` &ndash; DIMS controles en la barra de navegación. 
-   `SystemUiFlagHideNavigation` &ndash; Oculta la barra de navegación. 


Estas marcas se pueden aplicar a cualquier vista de la jerarquía de vistas estableciendo el `SystemUiVisibility` propiedad. Si varias vistas tienen esta propiedad establecida, el sistema las combina con una operación OR y los aplica siempre y cuando la ventana en la que se establecen las marcas conserva el foco. Cuando se quita una vista, también se quitarán los indicadores que ha establecido.

El ejemplo siguiente muestra una aplicación sencilla al hacer clic en cualquiera de los botones que cambia el `SystemUiVisibility`:

 [![Capturas de pantalla que muestra Visible, el perfil de baja y SystemUiVisibility oculta](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

El código para cambiar el `SystemUiVisibility` establece la propiedad en un `TextView` en cada botón, haga clic en controlador de eventos como se muestra a continuación:

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

Además, un `SystemUiVisibility` cambiar genera un `SystemUiVisibilityChange` eventos. Igual que la configuración de la `SystemUiVisibility` propiedad, un controlador para el `SystemUiVisibilityChange` eventos se pueden registrar para cualquier vista de la jerarquía. Por ejemplo, el código siguiente usa el `TextView` instancia para registrarse en el evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Vínculos relacionados

- [SystemUIVisibilityDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Introducción a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android de 4.0](http://developer.android.com/sdk/android-4.0.html)
