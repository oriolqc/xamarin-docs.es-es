---
title: Escribir aplicaciones con capacidad de respuesta
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: a1642c4cbb790cf09d2a31e629408afc61d5b7ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121781"
---
# <a name="writing-responsive-applications"></a>Escribir aplicaciones con capacidad de respuesta

Una de las claves para mantener una interfaz gráfica de usuario con capacidad de respuesta es realizar tareas de larga ejecución en un subproceso en segundo plano por lo que no bloquea la GUI. Supongamos que queremos calcular un valor para mostrar al usuario, pero ese valor tarda 5 segundos para calcular:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Esto funcionará, pero la aplicación se "bloquea" durante 5 segundos mientras se calcula el valor. Durante este tiempo, la aplicación no responderá a ninguna interacción del usuario. Para solucionar este problema, queremos hacer nuestros cálculos en un subproceso en segundo plano:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Ahora se calcula el valor en un subproceso en segundo plano para que nuestra interfaz gráfica de usuario permanece con capacidad de respuesta durante el cálculo. Sin embargo, cuando se realiza el cálculo, nuestra aplicación se bloquea, dejando esto en el registro:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Esto es porque se debe actualizar la interfaz gráfica de usuario desde el subproceso de interfaz gráfica de usuario. Nuestro código actualiza la GUI desde el subproceso de ThreadPool, provocando la aplicación se bloquee. Tenemos que calcular el valor en el subproceso en segundo plano, pero a continuación, realice la actualización en el subproceso de interfaz gráfica de usuario, que se controla con [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

Este código funciona según lo previsto. Esta interfaz gráfica de usuario con capacidad de respuesta se mantiene y se actualiza correctamente una vez que el cálculo es comple.

Tenga en cuenta que esta técnica no se utiliza simplemente para calcular un valor costoso. Se puede usar para cualquier tarea de ejecución prolongada que se puede realizar en segundo plano, como una llamada al servicio web o descargar datos de internet.
