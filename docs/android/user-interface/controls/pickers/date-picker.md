---
title: Selector de fecha
description: Seleccionar fechas del calendario mediante DatePickerDialog y DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: ef9abbd60fc83622631b916c50f4993c1c848b00
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510255"
---
# <a name="android-date-picker"></a>Selector de fecha de Android

## <a name="overview"></a>Información general

Hay ocasiones en las que un usuario debe introducir datos en una aplicación Android. Para ayudar con esto, el marco de trabajo de [`DatePicker`](xref:Android.Widget.DatePicker) Android proporciona el [`DatePickerDialog`](xref:Android.App.DatePickerDialog) widget y el. `DatePicker` Permite a los usuarios seleccionar el año, el mes y el día en una interfaz coherente entre dispositivos y aplicaciones. Es una clase auxiliar que encapsula el `DatePicker` en un cuadro de diálogo. `DatePickerDialog`

Las aplicaciones modernas `DatePickerDialog` [`DialogFragment`](xref:Android.App.DialogFragment)de Android deben mostrar en. Esto permitirá que una aplicación muestre el DatePicker como un cuadro de diálogo emergente o incrustado en una actividad. Además, `DialogFragment` administrará el ciclo de vida y la presentación del cuadro de diálogo, lo que reduce la cantidad de código que se debe implementar.

En esta guía se muestra cómo usar `DatePickerDialog`, encapsulado en un. `DialogFragment` La aplicación de ejemplo mostrará el `DatePickerDialog` como un cuadro de diálogo modal cuando el usuario haga clic en un botón de una actividad. Cuando el usuario establece la fecha, `TextView` se actualizará con la fecha seleccionada.

[![Captura de pantalla del botón seleccionar fecha seguido del cuadro de diálogo Selector de fecha](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisitos

La aplicación de ejemplo de esta guía tiene como destino Android 4,1 (nivel de API).
16) o superior, pero es aplicable a Android 3,0 (nivel de API 11 o superior). Es posible admitir versiones anteriores de Android con la incorporación de la biblioteca de compatibilidad de Android V4 al proyecto y algunos cambios de código.

## <a name="using-the-datepicker"></a>Usar DatePicker

Este ejemplo se extenderá `DialogFragment`. La subclase hospedará y mostrará un `DatePickerDialog`:

![Primer plano cuadro de diálogo de selector de fecha](date-picker-images/image-02.png)

Cuando el usuario selecciona una fecha y hace clic en el botón **Aceptar** , `DatePickerDialog` el llamará al [`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*)método.
El hospedaje `DialogFragment`implementa esta interfaz. Si el usuario hace clic en el botón **Cancelar** , el fragmento y el cuadro de diálogo se descartarán por sí mismos.

Hay varias maneras `DialogFragment` en las que puede devolver la fecha seleccionada a la actividad de hospedaje:

1. **Invocar un método o establecer una propiedad** &ndash; La actividad puede proporcionar una propiedad o un método específicamente para establecer este valor.

2. **Generar un evento** Puede definir un evento que se generará cuando `OnDateSet` se invoque. &ndash; `DialogFragment`

3. **Use un `Action`**  &ndash; puedeinvocar`Action<DateTime>` un para mostrar la fecha en la actividad. `DialogFragment` La actividad proporcionará el `Action<DateTime` al crear instancias de `DialogFragment`. En este ejemplo se utiliza la tercera técnica y se requiere que la actividad proporcione `Action<DateTime>` un a. `DialogFragment`

### <a name="extending-dialogfragment"></a>Extender DialogFragment

El primer paso para mostrar un `DatePickerDialog` es a la subclase `DialogFragment` y hacer que implemente la `IOnDateSetListener` interfaz:

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

Se `NewInstance` invoca el método para crear una instancia de un `DatePickerFragment`nuevo. Este método toma un `Action<DateTime>` que se invocará cuando el usuario haga clic en el botón **Aceptar** en el `DatePickerDialog`.

Cuando se va a mostrar el fragmento, Android llamará al método `OnCreateDialog`. Este método creará un nuevo `DatePickerDialog` objeto e inicializarlo con la fecha actual y el objeto `DatePickerFragment`de devolución de llamada (que es la instancia actual del).

> [!NOTE]
> Tenga en cuenta que el valor del mes `IOnDateSetListener.OnDateSet` en el que se invoca está en el intervalo de 0 a 11, y no de 1 a 12. El día del mes estará en el intervalo de 1 a 31 (según el mes seleccionado).

### <a name="showing-the-datepickerfragment"></a>Mostrar el DatePickerFragment

Ahora que `DialogFragment` se ha implementado, en esta sección se examinará cómo usar el fragmento en una actividad. En la aplicación de ejemplo que acompaña a esta guía, la actividad creará una `DialogFragment` instancia del `NewInstance` mediante el Factory Method y, a `DialogFragment.Show`continuación, lo mostrará invocación. Como parte de la creación de instancias `DialogFragment`de, la actividad pasa `Action<DateTime>`un, que mostrará la fecha en una `TextView` que se hospeda en la actividad:

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```

## <a name="summary"></a>Resumen

En este ejemplo se describe cómo mostrar `DatePicker` un widget como un cuadro de diálogo modal emergente como parte de una actividad de Android. Proporcionó una implementación de DialogFragment de ejemplo y `IOnDateSetListener` explicó la interfaz. En este ejemplo también se muestra cómo DialogFragment puede interactuar con la actividad del host para mostrar la fecha seleccionada.

## <a name="related-links"></a>Vínculos relacionados

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [Seleccionar una fecha](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
