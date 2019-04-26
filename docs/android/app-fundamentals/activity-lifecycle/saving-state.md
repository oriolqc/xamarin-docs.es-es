---
title: 'Tutorial: guardado del estado de actividad'
description: Hemos tratado la teoría detrás de guardar el estado en la Guía de ciclo de vida de actividad; Ahora, veamos un ejemplo.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c8f92e55648dff469227cc3bad981ad5f6e6d0ac
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019175"
---
# <a name="walkthrough---saving-the-activity-state"></a>Tutorial: guardado del estado de actividad

_Hemos tratado la teoría detrás de guardar el estado en la Guía de ciclo de vida de actividad; Ahora, veamos un ejemplo._

## <a name="activity-state-walkthrough"></a>Tutorial de estado de actividad

Abramos la **ActivityLifecycle_Start** proyecto (en el [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) ejemplo), compílelo y ejecútelo. Se trata de un proyecto muy simple que tiene dos actividades para mostrar el ciclo de vida de actividad y cómo se llaman a los distintos métodos de ciclo de vida. Al iniciar la aplicación, la pantalla de `MainActivity` se muestra: 

[![Pantalla de actividad A](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Transiciones de estado de visualización

Cada método en este ejemplo se escribe en la ventana de salida de la aplicación de IDE para indicar el estado de la actividad. (Para abrir la ventana de salida en Visual Studio, escriba **CTRL-ALT-O**; para abrir la ventana de salida en Visual Studio para Mac, haga clic en **Vista > paneles > resultado de la aplicación**.)

Cuando la aplicación se inicia por primera vez, la ventana de salida muestra los cambios de estado de *actividad A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Al hacer clic en el **Iniciar actividad B** button, vemos *actividad A* pausa y detención mientras *actividad B* pasa a través de los cambios del estado: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Como resultado, *actividad B* se inicia y se muestra en lugar de *actividad A*: 

[![Pantalla de la actividad B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Al hacer clic en el **Atrás** botón, *actividad B* se destruye y *actividad A* se reanuda: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Adición de contadores haga clic en

A continuación, vamos a cambiar la aplicación para que tengamos un botón que cuenta y muestra el número de veces que se hace clic en. En primer lugar, vamos a agregar un `_counter` variable de instancia a `MainActivity`:

```csharp
int _counter = 0;
```

A continuación, vamos a editar el **Resource/layout/Main.axml** diseño de archivo y agregue un nuevo `clickButton` que muestra el número de veces que el usuario ha hecho clic el botón. Resultante **Main.axml** debe parecerse al siguiente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

Vamos a agregar el código siguiente al final de la [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método `MainActivity` &ndash; este código controla los eventos de click la `clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

Cuando creamos y vuelva a ejecutar la aplicación, aparece un botón nuevo que se incrementa y muestra el valor de `_counter` en cada clic:

[![Agregar recuento táctil](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Pero cuando se gira el dispositivo al modo horizontal, este recuento se pierde:

[![Rotación horizontal establece el recuento a cero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Examinar el resultado de la aplicación, vemos que *actividad A* estaba en pausa, detenido, destruye, vuelve a crear, reiniciar y luego reanuda durante la rotación de la posición vertical como horizontal: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Dado que *actividad A* se destruye y se vuelve a crear otra vez cuando se gira el dispositivo, se pierde su estado de la instancia. A continuación, agregaremos código para guardar y restaurar el estado de la instancia.

### <a name="adding-code-to-preserve-instance-state"></a>Agregar código al estado de la instancia se conservará

Vamos a agregar un método para `MainActivity` para guardar el estado de la instancia. Antes de *actividad A* es destruye, Android llama automáticamente a [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) y pasa un [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que podemos usar para almacenar el estado de la instancia. Vamos a usar para guardar el número de clics como un valor entero:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Cuando *actividad A* se vuelven a crear y reanudar, Android pasa esto `Bundle` en nuestra `OnCreate` método. Vamos a agregar código a `OnCreate` para restaurar el `_counter` valor desde el pasado de `Bundle`. Agregue el código siguiente justo antes de la línea donde `clickbutton` está definida: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Compilación y vuelva a ejecutar la aplicación, a continuación, haga clic en el segundo botón varias veces. Cuando se gira el dispositivo al modo horizontal, se mantiene el recuento!

[![Rotación de la pantalla muestra el número de cuatro conserva](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


Echemos un vistazo a la ventana de salida para ver qué sucedió:
    
```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
``` 

Antes de la [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) se llamó al método, nuestro nuevo `OnSaveInstanceState` se llamó al método para guardar la `_counter` valor en un `Bundle`. Android pasado este `Bundle` nos cuando llama a nuestro `OnCreate` método y se podían utilizado para restaurar la `_counter` valor donde se quedó.


## <a name="summary"></a>Resumen

En este tutorial, hemos usado los conocimientos sobre el ciclo de vida de actividad para conservar los datos de estado. 



## <a name="related-links"></a>Vínculos relacionados

- [ActivityLifecycle (ejemplo)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Actividad de Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
