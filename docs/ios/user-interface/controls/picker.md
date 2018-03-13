---
title: Selector de
description: "Esta guía tratará diseñar y trabajar con selectores en una aplicación de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/02/2017
ms.openlocfilehash: 3418d0b7946e1da68380512d64bdfc8b13d1d784
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="picker"></a>Selector de

El control de selector muestra control 'rueda-like' que contiene una lista desplazable de valores con el valor seleccionado se resalta. Los usuarios gira la rueda para seleccionar la opción que deseen.

Un usuario específico caso para selectores para establecer la fecha o tiempo. Para proporcionar esta Apple ha creado una subclase personalizada de la clase de UIPickerView denominada UIDatePicker.

Este artículo describen implementar y utilizar el [selector](#picker) y [selector de fecha](#datepicker) controles.

<a name="picker"/>

## <a name="picker"></a>Selector de

### <a name="implementing-a-picker"></a>Implementación de un selector

Un selector se implementa creando una nueva [`UIPickerView`](https://developer.xamarin.com/api/type/UIKit.UIPickerView/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="pickers-and-storyboards"></a>Selectores y los guiones gráficos

Si se utiliza el Diseñador de iOS para crear la interfaz de usuario, el selector puede agregarse a su diseño del cuadro de herramientas:

![](picker-images/image1.png)


### <a name="working-with-picker"></a>Trabajar con el selector

Una vez que ha creado un selector, ya sea en código o a través de guiones gráficos, debe asignar un _modelo_ a él por lo que puede pasar e interactuar con los datos;

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    var pickerModel = new PeopleModel(personLabel);

    personPicker.Model = pickerModel;
}
```

El código siguiente muestra un ejemplo de un modelo:

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {   
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

En primer lugar debe pasar en algunos datos para proporcionar diferentes opciones para un usuario seleccione. Cuando sea posible intentar mantener esta lista lo más corta posible, o si es necesario intenta usar más de una 'marcar' (denominado *componentes*):

![Selector con dos componentes](picker-images/image3.png)

Para establecer el número de componentes, invalide el `GetComponentCount` método: 

```csharp
public override nint GetComponentCount(UIPickerView pickerView)
{
    return 2;
}
```

El valor devuelto indica el número de marca que tendrá el selector.

### <a name="customizing-appearance"></a>Personalizar la apariencia de
 
La apariencia de la `UIPickerView` puede personalizarse mediante el uso de la `UIPickerView.UIPickerViewAppearance` de clase o reemplazar por la `UIPickerViewModel.GetView` y `UIPickerViewModel.GetRowHeight` métodos en el `UIPickerViewModel`.


<a name="datepicker"/>

## <a name="date-picker"></a>Selector de fecha

### <a name="implementing-a-date-picker"></a>Implementar un selector de fecha

Un selector de fecha se implementa creando una nueva [ `UIDatePickerView` ](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="date-pickers-and-storyboards"></a>Los selectores de fecha y los guiones gráficos

Si está utilizando el Diseñador de iOS para crear la interfaz de usuario, el **selector de fecha** pueden agregarse a la distribución del cuadro de herramientas. Desde el panel de propiedades, se pueden ajustar las propiedades siguientes:

![](picker-images/image2.png)

* **Modo** : modo de la fecha y hora. Puede tratarse de fecha, hora, fecha y hora o un temporizador de cuenta atrás. 
* **Configuración regional** : la configuración regional del selector de fecha. Elija **predeterminado** establecida en el valor predeterminado del sistema o establecer ninguna configuración regional específica.
* **Intervalo** : muestra el incremento en el que se mostrarán las opciones de reloj.
* **Fecha, fecha de como mínimo, máximo fecha** : establece la fecha inicial que se mostrará el selector y las restricciones para las fechas seleccionables.

### <a name="configuring-the-datepicker"></a>Configurar el selector de fechas

Puede limitar el intervalo de fechas que puede seleccionar un usuario mediante el uso de la `MinimumDate` y `MaximumDate` propiedades. El fragmento de código siguiente muestra un ejemplo de cómo establecer el intervalo entre 60 años hoy y hace:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();

components.Year = -60;

NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);

datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

Como alternativa, también podría utilizar controles de .NET para establecer el intervalo de fechas mínimo y máximo. Por ejemplo:

```csharp
DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
```

También puede establecer el `MinuteInterval` propiedad para establecer el intervalo en el que el selector de mostrará minutos. El siguiente fragmento de código se puede utilizar para establecer el selector de minutos que se establecerá en intervalos de 10.

```csharp
datePickerView.MinuteInterval = 10;
```

Hay cuatro modos que se pueden establecer el selector de fecha mediante el uso de la [ `UIDatePicker.Mode` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.Mode/) propiedad. La lista siguiente muestra un ejemplo de cada una de ellas y cómo implementarla:

#### <a name="time"></a>Tiempo

El modo de tiempo muestra la hora con un selector de hora y minuto y una designación de AM o PM opcional. Se establece con el `UIDatePickerMode.Time` propiedad. Por ejemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

La siguiente imagen ilustra un ejemplo de este modo DatePicker:

![](picker-images/image8.png)



#### <a name="date"></a>Fecha

El modo de fecha muestra la fecha con un mes, día y selector de año. Se establece con el `UIDatePickerMode.Date` propiedad. Por ejemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

La siguiente imagen ilustra un ejemplo de este DatePicker:

![](picker-images/image7.png)

El orden de los selectores depende de la configuración regional de la la `UIDatePicker`. De forma predeterminada se establecerá en el sistema predeterminado. La imagen anterior muestra el diseño de los selectores en el `en_US` configuración regional, pero cambiarlo a un día | Mes | Diseño de año, podría utilizar el siguiente código para establecer la configuración regional:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![](picker-images/image9.png)


#### <a name="date-and-time"></a>Fecha y hora

El modo de fecha y hora muestra una vista de shortend de la fecha, la hora en horas y minutos y un dependings de designación AM o PM opcional en si se utiliza un reloj de 12 o 24 horas. Se establece con el `UIDatePickerMode.DateAndTime` propiedad. Por ejemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

La siguiente imagen ilustra un ejemplo de este DatePicker:

![](picker-images/image6.png)

Al igual que con [fecha](#Date), el orden de los selectores y el uso de 12 o 24 horas dependen de la configuración regional de la la `UIDatePicker`.

#### <a name="countdown-timer"></a>Temporizador de cuenta atrás

El modo de temporizador de cuenta atrás muestra la hora y los valores de los minutos. Se establece con el `UIDatePickerMode.CountDownTimer` propiedad. Por ejemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

La siguiente imagen ilustra un ejemplo de este DatePicker:

![](picker-images/image5.png)

Puede usar el `CountDownDuration` propiedad que se va a capturar el dispayed de valor por el selector de fecha de cuenta atrás. Por ejemplo, para agregar el valor de cuenta atrás a la fecha actual, podría utilizar el siguiente código:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="formatting"></a>Formato 

Los valores de los modos DateAndTime, fecha y hora se pueden capturar mediante el `Date` propiedad en el UIDatePicker (por ejemplo: `datePickerView.Date`), que es de tipo NSDate. Para dar formato a esta fecha en algo más legible, utilice [ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/). Los ejemplos siguientes muestran cómo utilizar algunas de las propiedades disponibles en esta clase.

El `DateFormat` se establece como una cadena para representar cómo se debe mostrar la fecha:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

El `TimeStyle` conjuntos de propiedades un `NSDateFormatterStyle`:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Los campos de `NSDateFormatterStyle` mostrar como sigue:

![](picker-images/timestyle.png)

El `DateStyle` conjuntos de propiedades un `NSDateFormatterStyle`:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Los campos de `NSDateFormatterStyle` mostrar como sigue:

![](picker-images/datestyle.png)

A continuación, puede generar el NSDate con formato en una cadena mediante el código siguiente:

```csharp
dateLabel.Text = dateTimeformat.ToString(datePickerView.Date);
```

## <a name="related-links"></a>Vínculos relacionados

- [PickerControl (ejemplo)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
