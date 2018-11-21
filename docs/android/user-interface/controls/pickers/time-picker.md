---
title: Selector de hora
description: Seleccionar una hora mediante TimePickerDialog y DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: faf2c35b49b0b02b9f3b16e19494d2e447361d84
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171656"
---
# <a name="time-picker"></a>Selector de hora

Para proporcionar una forma para el usuario seleccionar una hora, puede usar [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). Normalmente usan aplicaciones Android `TimePicker` con [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) para seleccionar un valor de hora &ndash; Esto ayuda a garantizar una interfaz coherente entre dispositivos y aplicaciones. `TimePicker` permite a los usuarios seleccionar la hora del día en modo de A.M./P.M. de 12 horas o 24 horas.
`TimePickerDialog` es una clase auxiliar que encapsula el `TimePicker` en un cuadro de diálogo.

[![Captura de pantalla de ejemplo del cuadro de diálogo Selector de tiempo en acción](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Información general

Mostrar las aplicaciones modernas Android el `TimePickerDialog` en un [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Esto hace posible para una aplicación para mostrar el `TimePicker` como un cuadro de diálogo emergente o insertarlo en una actividad. Además, el `DialogFragment` administra el ciclo de vida y la presentación del cuadro de diálogo, lo que reduce la cantidad de código que se debe implementar.

Esta guía muestra cómo usar el `TimePickerDialog`, ajustados en un `DialogFragment`. La aplicación de ejemplo muestra el `TimePickerDialog` como un cuadro de diálogo modal cuando el usuario hace clic en un botón en una actividad. Cuando la hora se establece por el usuario, se cierra el cuadro de diálogo y un controlador actualiza un `TextView` en la pantalla de actividad con el tiempo que se ha seleccionado.

## <a name="requirements"></a>Requisitos

La aplicación de ejemplo en esta guía tiene como destino Android 4.1 (nivel de API
16) o superior, pero se puede usar con Android 3.0 (API nivel 11 o superior). Es posible compatibilidad con versiones anteriores de Android con la adición de la v4 Android Support Library al proyecto y algunos cambios de código.

## <a name="using-the-timepicker"></a>Utilizando el objeto TimePicker

Este ejemplo amplía `DialogFragment`; la implementación de subclase de `DialogFragment` (llamado `TimePickerFragment` debajo) hospeda y muestra un `TimePickerDialog`. Cuando la aplicación de ejemplo primero se inicia, muestra un **PICK tiempo** botón arriba un `TextView` que se usará para mostrar la hora seleccionada:

[![Pantalla de la aplicación de ejemplo inicial](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Al hacer clic en el **PICK tiempo** button, inicie la aplicación de ejemplo la `TimePickerDialog` tal como se muestra en esta captura de pantalla:

[![Captura de pantalla del cuadro de diálogo de selector de hora predeterminado mostrado por la aplicación](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

En el `TimePickerDialog`, seleccione uno y haga clic en el **Aceptar** botón hace que el `TimePickerDialog` para invocar el método [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Esta interfaz se implementa mediante el hospedaje `DialogFragment` (`TimePickerFragment`, que se describen a continuación). Al hacer clic en el **cancelar** botón hace que el fragmento y se puede descartar el cuadro de diálogo.

`DialogFragment` Devuelve la hora seleccionada para la actividad de hospedaje de uno de tres maneras:

1. **Invocar un método o establecer una propiedad** &ndash; la actividad puede proporcionar una propiedad o método específicamente para establecer este valor.

2. **Generar un evento** &ndash; el `DialogFragment` puede definir un evento que será se genera cuando `OnTimeSet` se invoca.

3. **Mediante un `Action`**  &ndash; el `DialogFragment` puede invocar un `Action<DateTime>` para mostrar la hora de la actividad. La actividad proporcionará la `Action<DateTime` al crear una instancia el `DialogFragment`. 

Este ejemplo usará la tercera técnica, que requiere que la fuente de alimentación de actividad una `Action<DateTime>` controlador para el `DialogFragment`.



## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Iniciar un nuevo proyecto de Android denominado **TimePickerDemo** (si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener información sobre cómo crear un nuevo proyecto).

Editar **Resources/layout/Main.axml** y reemplace su contenido con el siguiente código XML:

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

Se trata de un basic [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) con un [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) que muestra la hora y un [botón](https://developer.xamarin.com/api/type/Android.Widget.Button/) que abre el `TimePickerDialog`. Tenga en cuenta que este diseño usa cadenas codificadas y las dimensiones para que la aplicación más sencillo y más fácil de entender &ndash; una aplicación de producción suele usa recursos para estos valores (como puede verse en la [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ejemplo de código).

Editar **MainActivity.cs** y reemplace su contenido con el código siguiente:

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

Al compilar y ejecutar este ejemplo, debería ver una pantalla inicial similar a la siguiente captura de pantalla:

[![Pantalla inicial de la aplicación](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Al hacer clic en el **PICK tiempo** botón no hace nada porque el `DialogFragment` aún no se ha implementado para mostrar el `TimePicker`.
El siguiente paso es crear este `DialogFragment`.



## <a name="extending-dialogfragment"></a>Extender DialogFragment

Para ampliar `DialogFragment` para su uso con `TimePicker`, es necesario crear una subclase derivada de `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Agregue la siguiente clase al **MainActivity.cs**:

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

-   `TimePickerFragment` es una subclase de `DialogFragment`. También implementa la `TimePickerDialog.IOnTimeSetListener` interfaz (es decir, proporciona los `OnTimeSet` método):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` se inicializa para fines de registro (*MyTimePickerFragment* puede cambiarse en cualquier cadena que desea usar). El `timeSelectedHandler` acción se inicializa en un delegado vacío para evitar excepciones de referencia nula:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   El `NewInstance` se denomina método de generador para crear una nueva instancia `TimePickerFragment`. Este método toma un `Action<DateTime>` controlador que se invoca cuando el usuario hace clic en el **Aceptar** situado en la `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Cuando el fragmento que se va a mostrarse, se llama Android el `DialogFragment` método [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
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

-   Cuando el usuario cambia el valor de tiempo en el `TimePicker` cuadro de diálogo, el `OnTimeSet` se invoca el método. `OnTimeSet` crea un `DateTime` objeto con la fecha actual y las combinaciones en el tiempo (hora y minuto) seleccionadas por el usuario:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Esto `DateTime` objeto se pasa a la `timeSelectedHandler` que está registrado con el `TimePickerFragment` objeto en tiempo de creación. `OnTimeSet` se invoca este controlador para actualizar la visualización del tiempo de la actividad a la hora seleccionada (este controlador se implementa en la sección siguiente):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Mostrar el TimePickerFragment

Ahora que la `DialogFragment` ha sido implementa, es el momento de crear una instancia de la `DialogFragment` utilizando el `NewInstance` método de fábrica y mostrarla mediante la invocación [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Agregue el siguiente método a `MainActivity`:

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

Después de `TimeSelectOnClick` crea una instancia de un `TimePickerFragment`, crea y pasa un delegado para un método anónimo que actualiza la visualización del tiempo de la actividad con el valor de tiempo pasado. Por último, inicia el `TimePicker` fragmento del cuadro de diálogo (a través de `DialogFragment.Show`) para mostrar el `TimePicker` al usuario.

Al final de la `OnCreate` método, agregue la línea siguiente para asociar el controlador de eventos el **PICK tiempo** botón que inicia el cuadro de diálogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Cuando el **PICK tiempo** se hace clic en el botón, `TimeSelectOnClick` se invocará para mostrar el `TimePicker` fragmento del cuadro de diálogo al usuario.



## <a name="try-it"></a>¡Inténtelo!

Compile y ejecute la aplicación. Al hacer clic en el **PICK tiempo** botón, el `TimePickerDialog` se muestra en el formato de hora predeterminado para la actividad (en este modo de A.M./P.M. caso, 12 horas):

[![Aparece el cuadro de diálogo de tiempo en modo de A.M./P.M.](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Al hacer clic en **Aceptar** en el `TimePicker` cuadro de diálogo, el controlador actualiza la actividad `TextView` con el tiempo seleccionado y, a continuación, se cierra:

[![Tiempo A/M se muestra en la vista de texto de la actividad](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

A continuación, agregue la siguiente línea de código para `OnCreateDialog` inmediatamente después de `is24HourFormat` se declara e inicializa:

```csharp
is24HourFormat = true;
```

Este cambio fuerza el indicador pasando a la `TimePickerDialog` constructor sea `true` por lo que el modo de 24 horas se usa en lugar del formato de hora de la actividad de hospedaje. Al compilar y vuelva a ejecutar la aplicación, haga clic en el **PICK tiempo** botón, el `TimePicker` cuadro de diálogo se muestra ahora en formato de 24 horas:

[![Cuadro de diálogo de TimePicker en formato de 24 horas](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Dado que el controlador llama a [DateTime.ToShortTimeString](xref:System.DateTime.ToShortDateString*) para imprimir la hora a la actividad `TextView`, todavía se imprime el tiempo en el formato de A.M./P.M. de 12 horas de forma predeterminada.



## <a name="summary"></a>Resumen

En este artículo se explica cómo mostrar un `TimePicker` widget como un cuadro de diálogo modal de menú emergente de una actividad de Android. Proporciona un ejemplo `DialogFragment` implementación y explica el `IOnTimeSetListener` interfaz. En este ejemplo también muestra cómo el `DialogFragment` puede interactuar con el host de actividad para mostrar la hora seleccionada.


## <a name="related-links"></a>Vínculos relacionados

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
