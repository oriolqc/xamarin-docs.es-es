---
title: 'Tutorial: crear una interfaz de usuario con pestañas con TabHost'
description: En este artículo le guiará por la creación de una interfaz de usuario con pestañas en Xamarin.Android mediante la API de TabHost.
ms.prod: xamarin
ms.assetid: AD6E2173-974E-477C-940F-0CAB5E53326D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: ca9a3f3d31707205cdcd4e0d8e74fa303ccba047
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---creating-a-tabbed-ui-with-tabhost"></a>Tutorial: crear una interfaz de usuario con pestañas con TabHost

_En este artículo le guiará por la creación de una interfaz de usuario con pestañas en Xamarin.Android mediante la API de TabHost._

> [!NOTE]
> `TabHost` es una API antigua que ha quedado en desuso por Google. Los desarrolladores pueden crear aplicaciones con pestañas con el [ActionBar](~/android/user-interface/controls/action-bar.md). La `ActionBar` está disponible en todas las versiones de Android. Que se introdujo en Android 3.0 (API nivel 11) y volver procede para Android 2.2 (API nivel 8) y Android 2.3 (API nivel 10) en el [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que está disponible para Xamarin.Android a través de la [Xamarin Biblioteca de compatibilidad de Android - V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete.

Este artículo le guiará por la creación de una interfaz de usuario con pestañas en Xamarin.Android utilizando el `TabHost` API. Se trata de una API más antiguos que está disponible en todas las versiones de Android. En este ejemplo creará una aplicación con tres pestañas, con la lógica de cada pestaña está encapsulado en una actividad.
Captura de pantalla siguiente es un ejemplo de la aplicación que se va a crear:

![Captura de pantalla de ejemplo de la aplicación con varias pestañas](creating-a-tabbed-ui-images/image02.png)


## <a name="creating-the-application"></a>Crear la aplicación

Descargue y descomprima [TabHostWalkthrough](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/).
Este proyecto actúa como punto de partida para nuestra aplicación y contiene algunas imágenes. Si examina este proyecto, verá que ya hemos creado los recursos con estas características para los iconos de pestaña.

Primero vamos a actualizar el archivo de diseño **Resources/Layout/Main.axml** que va a hospedar las pestañas. Editar **Resources/Layout/Main.axml** e inserte el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
         android:id="@android:id/tabhost"
         android:layout_width="fill_parent"
         android:layout_height="fill_parent">
    <LinearLayout
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:padding="5dp">
        <TabWidget
                android:id="@android:id/tabs"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" />
        <FrameLayout
                android:id="@android:id/tabcontent"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp" />
    </LinearLayout>
</TabHost>
```

Captura de pantalla siguiente muestra el diseño en el Diseñador de Xamarin:

[![Captura de pantalla del diseño TabHost en el Diseñador de Xamarin](creating-a-tabbed-ui-images/image04-sml.png)](creating-a-tabbed-ui-images/image04.png#lightbox)

El TabHost debe tener dos vistas secundarias dentro de él: un `TabWidget` y `FrameLayout`. A la posición del `TabWidget` y `FrameLayout` verticalmente dentro el `TabHost`, un `LinearLayout` se utiliza. El FrameLayout es dónde va el contenido de cada pestaña, que está vacío porque la `TabHost` incrustarán automáticamente cada actividad en tiempo de ejecución. Existen varias reglas que deben tenerse en cuenta cuando se trata de crear un diseño de interfaces de usuario con pestañas:

-   El `TabHost` deben tener el identificador `@android:id/tabhost`.

-   El `TabWidget` deben tener el identificador `@android:id/tabs`.

-   El `FrameLayout` deben tener el identificador `@android:id/tabcontent`.

-   `TabHost` requiere que cualquier actividad que administra heredar de `TabActivity`. Por lo tanto, es importante para crear una subclase `TabActivity` aquí &ndash; una actividad regular no funcionará.

Edite el archivo **MainActivity.cs** para que la clase `MainActivity` subclases `TabActivity` tal como se muestra en el fragmento de código siguiente:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/ic_launcher")]
public class MainActivity : TabActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
    }
}
```

Cree cuatro clases independientes de actividad en el proyecto: `MyScheduleActivity`, `SessionsActivity`, `SpeakersActivity`, y `WhatsOnActivity`. Cada actividad formarán parte de la interfaz de usuario de una pestaña. Ahora estas actividades pasarán a ser un código auxiliar que muestra un `TextView` con un mensaje sencillo. Modificar el código en cada actividad para contener los datos siguientes `OnCreate` implementación:

```csharp
[Activity]
public class MyScheduleActivity : Activity
{
    protected override void OnCreate (Bundle savedInstanceState)
    {
        base.OnCreate (savedInstanceState);
        TextView textview = new TextView (this);
        textview.Text = "This is the My Schedule tab";
        SetContentView (textview);
    }
}
```

Tenga en cuenta que el código anterior no usa un archivo de diseño. Simplemente crea un `TextView` algún texto y lo establece `TextView` como la vista de contenido. Duplicar esto para cada una de las tres actividades restantes.

A continuación se asignará los iconos para cada pestaña. Cada pestaña requiere dos iconos &ndash; uno para el estado seleccionado y otro para el estado no seleccionado. Un ejemplo de estos dos iconos diferentes puede verse en las dos imágenes siguientes (los iconos necesarios para esta aplicación ya se agregaron al proyecto de ejemplo):

![Captura de pantalla de estado seleccionado y los iconos de estado no seleccionado](creating-a-tabbed-ui-images/tab-icons.png)


Se asignará los recursos pueden dibujar con las pestañas de icono definiendo un *Drawableestado-List*. Lista de Estados drawables son un recursos con estas características especiales que se definen en XML y le permiten especificar imágenes diferentes que son específicas de estado de ese elemento. En este ejemplo es una imagen que se utiliza cuando se selecciona una ficha y otro que se utiliza cuando no se selecciona la pestaña. Para ahorrar tiempo, la drawables de lista de estados necesarios se agregaron al proyecto. La siguiente lista muestra los archivos y el XML contiene:

-   `ic_tab_my_schedule.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

-   `ic_tab_sessions.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_sessions_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_sessions_unselected"/>
    </selector>
    ```

-   `ic_tab_speakers.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_speakers_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_speakers_unselected"/>
    </selector>
    ```

-   `ic_tab_whats_on.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

Las pestañas se agregan a la `TabHost` mediante programación, que es una tarea muy repetitiva. Para ello, agregue el método siguiente a la clase `MainActivity`:

```csharp
private void CreateTab(Type activityType, string tag, string label, int drawableId )
{
    var intent = new Intent(this, activityType);
    intent.AddFlags(ActivityFlags.NewTask);

    var spec = TabHost.NewTabSpec(tag);
    var drawableIcon = Resources.GetDrawable(drawableId);
    spec.SetIndicator(label, drawableIcon);
    spec.SetContent(intent);

    TabHost.AddTab(spec);
}
```

Cada pestaña en el `TabHost` se representa mediante una instancia de la de la `TabHost.TabSpec` clase. Esta instancia contiene los metadatos necesarios para representar la ficha, en concreto:

-   **Texto y el icono** &ndash; para mostrarse en el `TabWidget`.

-   **Pestaña contenido** &ndash; Esto puede ser un `Activity` o `View` y se muestra cuando se selecciona la pestaña.

-   **Etiqueta único** &ndash; cada ficha debe tener una etiqueta única asignada a él.

Debemos agregar una `TabHost.TabSpec` instancia para cada pestaña en nuestra aplicación. Permite realizar este paso en el paso siguiente.

Actualizar el método `OnCreate` en `MainActivity` para que sea similar el siguiente código:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateTab(typeof(WhatsOnActivity), "whats_on", "What's On", Resource.Drawable.ic_tab_whats_on);
    CreateTab(typeof(SpeakersActivity), "speakers", "Speakers", Resource.Drawable.ic_tab_speakers);
    CreateTab(typeof(SessionsActivity), "sessions", "Sessions", Resource.Drawable.ic_tab_sessions);
    CreateTab(typeof(MyScheduleActivity), "my_schedule", "My Schedule", Resource.Drawable.ic_tab_my_schedule);
}
```

Ejecute la aplicación. La aplicación debe ser similar a la captura de pantalla se muestra al principio de este tutorial.

Ya está. Hemos creado una aplicación con pestañas que proporciona al usuario una manera fácil de navegar a distintas partes de una aplicación.



## <a name="summary"></a>Resumen

En este capítulo se analizó diseños con pestañas y se le orientará a través de proceso de creación de una aplicación con pestañas. El tutorial muestra cómo utilizar un `TabActivity` para aumentar un diseño de archivos que hospeda un `TabHost` y `TabWidget`. El `TabHost` , a continuación, se rellena con una colección de `TabHost.TabSpec` objetos que utilizaría la `TabHost` en tiempo de ejecución para crear instancias de las actividades que se utilizaría en cada pestaña.



## <a name="related-links"></a>Vínculos relacionados

- [TabHostWalkthrough (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Paquete de compatibilidad con Android biblioteca v7 AppCompat NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
