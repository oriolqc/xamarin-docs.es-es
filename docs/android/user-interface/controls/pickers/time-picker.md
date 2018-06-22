---
title: Selector de hora
description: Al seleccionar una hora con TimePickerDialog y DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: c4261e3dccaccc4c88afe9c1033fb16b730fea6e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769902"
---
# <a name="time-picker"></a>Selector de hora

Para proporcionar una manera para que el usuario seleccionar una hora, puede usar [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). Normalmente se usan aplicaciones de Android `TimePicker` con [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) para seleccionar un valor de hora &ndash; Esto ayuda a garantizar una interfaz coherente entre dispositivos y aplicaciones. `TimePicker` permite a los usuarios seleccionar la hora del día en modo de AM/PM de 12 horas o de 24 horas.
`TimePickerDialog` es una clase auxiliar que encapsula el `TimePicker` en un cuadro de diálogo.

[![Captura de pantalla de ejemplo del cuadro de diálogo Selector de tiempo en acción](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Información general

Mostrar las aplicaciones modernas de Android el `TimePickerDialog` en un [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Esto hace posible que una aplicación para mostrar el `TimePicker` como un cuadro de diálogo emergente o incrustar en una actividad. Además, la `DialogFragment` administra el ciclo de vida y la presentación del cuadro de diálogo, lo que reduce la cantidad de código que debe implementarse.

Esta guía muestra cómo utilizar el `TimePickerDialog`, ajustados en un `DialogFragment`. La aplicación de ejemplo muestra el `TimePickerDialog` como un cuadro de diálogo modal cuando el usuario hace clic en un botón en una actividad. Cuando el usuario establece el tiempo, se cierra el cuadro de diálogo y un controlador actualiza un `TextView` en la pantalla de actividad con el momento en que se ha seleccionado.

## <a name="requirements"></a>Requisitos

La aplicación de ejemplo de esta guía está dirigida Android 4.1 (nivel de API
16) o superior, pero se puede usar con Android 3.0 (API nivel 11 o superior). Es posible para la compatibilidad con versiones anteriores de Android con la adición de la v4 Android biblioteca de compatibilidad del proyecto y algunos cambios en el código.

## <a name="using-the-timepicker"></a>Usar el TimePicker

En este ejemplo extiende `DialogFragment`; la implementación de subclase de `DialogFragment` (denominado `TimePickerFragment` a continuación) hospeda y muestra un `TimePickerDialog`. Cuando se inició por primera vez la aplicación de ejemplo, muestra un **tiempo de selección** botón arriba un `TextView` que se usará para mostrar la hora seleccionada:

[![Pantalla de la aplicación de ejemplo inicial](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Al hacer clic en el **tiempo de selección** button, inicie la aplicación de ejemplo la `TimePickerDialog` tal como se muestra en esta captura de pantalla:

[![Captura de pantalla del cuadro de diálogo de selector de hora de predeterminado mostrado por la aplicación](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

En el `TimePickerDialog`, seleccione uno y haga clic en el **Aceptar** botón causas el `TimePickerDialog` para invocar el método [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Esta interfaz se implementa mediante el hospedaje `DialogFragment` (`TimePickerFragment`, que se describen a continuación). Al hacer clic en el **cancelar** botón hace que el fragmento y el cuadro de diálogo se cierra.

`DialogFragment` Devuelve el tiempo seleccionado para el hospedaje Actvity en uno de tres maneras:

1. **Invocar un método o establecer una propiedad** &ndash; la actividad puede proporcionar una propiedad o método específicamente para establecer este valor.

2. **Cuando se genera un evento** &ndash; el `DialogFragment` puede definir un evento que se genera cuando `OnTimeSet` se invoca.

3. **Con un `Action`**  &ndash; el `DialogFragment` puede invocar una `Action<DateTime>` para mostrar la hora en la actividad. La actividad proporcionará la `Action<DateTime` al crear una instancia el `DialogFragment`. 

Este ejemplo usará la tercera técnica, que requiere que la fuente de alimentación de actividad una `Action<DateTime>` controlador para la `DialogFragment`.



## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Iniciar un nuevo proyecto Android denominado **TimePickerDemo** (si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener información sobre cómo crear un nuevo proyecto).

Editar **Resources/layout/Main.axml** y reemplazar su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

Se trata de un basic [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) con un [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) que muestra el tiempo y un [botón](https://developer.xamarin.com/api/type/Android.Widget.Button/) que abre el `TimePickerDialog`. Tenga en cuenta que este diseño usa cadenas no modificables y las dimensiones para que la aplicación más sencilla y más fácil de entender &ndash; una aplicación de producción suele usa recursos para estos valores (como puede verse en la [DatePicker](https://github.com/xamarin/recipes/blob/master/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ejemplo de código).

Editar **MainActivity.cs** y reemplazar su contenido con el código siguiente:

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

Al compilar y ejecutar este ejemplo, debería ver una pantalla inicial similar a la captura de pantalla siguiente:

[![Pantalla inicial de la aplicación](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Al hacer clic en el **tiempo de selección** botón no hace nada porque el `DialogFragment` aún no se ha implementado para mostrar el `TimePicker`.
El paso siguiente consiste en crear esta `DialogFragment`.



## <a name="extending-dialogfragment"></a>Extender DialogFragment

Para ampliar `DialogFragment` para su uso con `TimePicker`, es necesario crear una subclase que se deriva de `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Agregue la siguiente clase al **MainActivity.cs**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

Esto `TimePickerFragment` clase se divide en partes más pequeñas y se explica en la sección siguiente.


### <a name="dialogfragment-implementation"></a>Implementación de DialogFragment

`TimePickerFragment` implementa varios métodos: un método de fábrica, un método de creación de instancias de diálogo y el `OnTimeSet` requerido por el método de controlador `TimePickerDialog.IOnTimeSetListener`.

-   `TimePickerFragment` es una subclase de `DialogFragment`. También implementa la `TimePickerDialog.IOnTimeSetListener` interfaz (es decir, que proporciona el necesario `OnTimeSet` (método)):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` se inicializa para fines de registro (*MyTimePickerFragment* puede cambiarse a cualquier cadena que desea usar). El `timeSelectedHandler` se inicializó la acción a un delegado vacío para evitar excepciones de referencia nula:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   El `NewInstance` se denomina método de generador para crear instancias de un nuevo `TimePickerFragment`. Este método toma una `Action<DateTime>` controlador que se invoca cuando el usuario hace clic en el **Aceptar** botón en el `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Una vez el fragmento que se mostrará, Android llama el `DialogFragment` método [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
    Este método crea un nuevo `TimePickerDialog` objeto y lo inicializa con la actividad, el objeto de devolución de llamada (que es la instancia actual de la `TimePickerFragment`) y la hora actual:

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

-   Cuando el usuario cambia el valor de tiempo en el `TimePicker` cuadro de diálogo, el `OnTimeSet` se invoca el método. `OnTimeSet` crea un `DateTime` objeto con la fecha actual y las mezclas en el tiempo (hora y minuto) seleccionadas por el usuario:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Esto `DateTime` objeto se pasa a la `timeSelectedHandler` que está registrado con el `TimePickerFragment` objeto en tiempo de creación. `OnTimeSet` se invoca este controlador para actualizar la visualización del tiempo de la actividad hasta la hora seleccionada (este controlador se implementa en la sección siguiente):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Mostrar el TimePickerFragment

Ahora que el `DialogFragment` ha sido implementado, es el momento de crear una instancia de la `DialogFragment` mediante la `NewInstance` método generador y mostrarlo mediante la invocación de [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Agregue el método siguiente a `MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

Después de `TimeSelectOnClick` crea una instancia de un `TimePickerFragment`, se crea y se pasa un delegado para un método anónimo que actualiza la visualización del tiempo de la actividad con el valor pasado en el momento. Por último, se inicia el `TimePicker` fragmento del cuadro de diálogo (a través de `DialogFragment.Show`) para mostrar el `TimePicker` al usuario.

Al final de la `OnCreate` método, agregue la siguiente línea para conectar el controlador de eventos para el **selección tiempo** botón que inicia el cuadro de diálogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Cuando el **tiempo de selección** se hace clic en el botón, `TimeSelectOnClick` se invocará para mostrar el `TimePicker` fragmento del cuadro de diálogo al usuario.



## <a name="try-it"></a>¡Inténtelo!

Compile y ejecute la aplicación. Al hacer clic en el **tiempo de selección** botón, el `TimePickerDialog` se muestra en el formato de hora predeterminado para la actividad (en este modo de AM/PM case, 12 horas):

[![Se muestra el cuadro de diálogo de tiempo en modo de AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Al hacer clic en **Aceptar** en el `TimePicker` cuadro de diálogo, el controlador actualiza la actividad `TextView` con el tiempo seleccionado y, a continuación, se cierra:

[![A/M: tiempo se muestra en la actividad TextView](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

A continuación, agregue la siguiente línea de código para `OnCreateDialog` inmediatamente después de `is24HourFormat` se declaran e inicializan:

```csharp
is24HourFormat = true;
```

Este cambio fuerza el indicador que se pasan a la `TimePickerDialog` constructor como `true` para que el modo de 24 horas se utiliza en lugar del formato de hora de la actividad de hospedaje. Al compilar y vuelva a ejecutar la aplicación, haga clic en el **tiempo de selección** botón, el `TimePicker` cuadro de diálogo se muestra ahora en formato de 24 horas:

[![Cuadro de diálogo de TimePicker en formato de 24 horas](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Dado que el controlador llama [DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx) para imprimir la hora a la actividad `TextView`, la hora se imprimirá en el formato de AM/PM de 12 horas de forma predeterminada.



## <a name="summary"></a>Resumen

Este artículo explica cómo mostrar un `TimePicker` widget como un cuadro de diálogo modal de menú emergente de una actividad de Android. Proporciona un ejemplo de `DialogFragment` implementación y explica el `IOnTimeSetListener` interfaz. Este ejemplo también muestra cómo el `DialogFragment` puede interactuar con el host de actividad para mostrar la hora seleccionada.


## <a name="related-links"></a>Vínculos relacionados

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
