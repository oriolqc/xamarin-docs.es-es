---
title: Control de selector de Xamarin.iOS
description: Este documento describe cómo diseñar y trabajar con controles de selector de una aplicación de Xamarin.iOS. Describe cómo implementar un selector en código y en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 0ef33c2036b1ff2d5a7e2035ca5fa8af58672867
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "34789917"
---
# <a name="picker-control-in-xamarinios"></a>Control de selector de Xamarin.iOS

Un [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) permite elegir un valor de una lista de componentes individuales de una interfaz similar a rueda de desplazamiento.

Los selectores se usan con frecuencia para seleccionar una fecha y hora; Apple proporciona el [`UIDatePicker`](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/)
clase para este propósito.

El artículo describe cómo implementar y utilizar el `UIPickerView` y `UIDatePicker` controles.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implementación de un selector

Implementar un selector de instancias de un nuevo `UIPickerView`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>Selectores y los guiones gráficos

Para crear un selector en la **iOS Designer**, arrastre un **selector vista** desde el **cuadro de herramientas** a la superficie de diseño.

![Arrastre un selector de la vista a la superficie de diseño](picker-images/image1.png "arrastre un selector de la vista a la superficie de diseño")

### <a name="working-with-a-picker-control"></a>Trabajar con un control de selector

Usa un selector de un _modelo_ para interactuar con datos:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

El [ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) clase base implementa dos interfaces [`IUIPickerDataSource`](https://developer.xamarin.com/api/type/UIKit.IUIPickerViewDataSource/)
y [ `IUIPickerViewDelegate` ](https://developer.xamarin.com/api/type/UIKit.IUIPickerViewDelegate/), que declare varios métodos que especifican datos de un selector y cómo administra la interacción:

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

Un selector puede tener varias columnas, o _componentes_. Componentes de dividir un selector en varias secciones, que permite la selección de datos sea más fácil y más específico:

![Selector con dos componentes](picker-images/image3.png "selector con dos componentes")

Para especificar el número de componentes en un selector, use el [`GetComponentCount`](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetComponentCount/p/UIKit.UIPickerView/) 
.

### <a name="customizing-a-pickers-appearance"></a>Personalizar la apariencia de un selector

Para personalizar la apariencia de un selector, utilice el [`UIPickerView.UIPickerViewAppearance`](https://developer.xamarin.com/api/type/UIKit.UIPickerView+UIPickerViewAppearance/)
clase o invalidar el [ `GetView` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetView/p/UIKit.UIPickerView/System.nint/System.nint/UIKit.UIView/) y [ `GetRowHeight` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetRowHeight/p/UIKit.UIPickerView/System.nint/) métodos en el `UIPickerViewModel`.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implementación de un selector de fecha

Implementar un selector de fecha, creando un `UIDatePicker`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>Selectores de fecha y guiones gráficos

Para crear un selector de fecha en la **iOS Designer**, arrastre un **selector de fecha** desde el **cuadro de herramientas** a la superficie de diseño.

![Arrastre un selector de fecha a la superficie de diseño](picker-images/image2.png "arrastre un selector de fecha a la superficie de diseño")

### <a name="date-picker-properties"></a>Propiedades de selector de fecha

#### <a name="minimum-and-maximum-date"></a>Fecha máxima y mínima

[`MinimumDate`](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MinimumDate/) y [ `MaximumDate` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MaximumDate/) limitan el intervalo de fechas disponibles en el selector de fecha. Por ejemplo, el siguiente código restringe a un selector de fecha para los años sesenta conducen al momento presente:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> Es posible convertir explícitamente un `DateTime` a un `NSDate`:
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervalo de minutos

El [ `MinuteInterval` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MinuteInterval/) propiedad establece el intervalo en el que el selector mostrará minutos:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Modo

Selectores de fecha admiten cuatro [modos](https://developer.xamarin.com/api/type/UIKit.UIDatePickerMode/), que se describen a continuación:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` Muestra la hora con un selector de hora y minuto y una designación de A.M. o P.M. opcional:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` Muestra la fecha con un mes, día y el selector de año:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

El orden de los selectores depende de la configuración regional del selector de fecha, que usa la configuración regional del sistema de forma predeterminada. La imagen anterior muestra el diseño de los selectores en el `en_US` configuración regional, pero la siguiente cambia el orden día | Mes | Año:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Día | Mes | Año](picker-images/image9.png "día | Mes | Año")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` muestra una vista reducida de la fecha, el tiempo en horas y minutos y una designación de A.M. o P.M. opcional (dependiendo de si se usa un reloj de 12 o 24 horas):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Igual que con [ `UIDatePickerMode.Date` ](#uidatepickermodedate), el orden de los selectores y el uso de un reloj de 12 o 24 horas dependen de la configuración regional del selector de fecha.

> [!TIP]
> Use la `Date` propiedad para capturar el valor de un selector de fecha en el modo `UIDatePickerMode.Time`, `UIDatePickerMode.Date`, o `UIDatePickerMode.DateAndTime`. Este valor se almacena como un `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` Muestra los valores de hora y minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

El `CountDownDuration` propiedad captura el valor de un selector de fecha en `UIDatePickerMode.CountDownTimer` modo. Por ejemplo, para agregar el valor de la cuenta atrás hasta la fecha actual:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Para dar formato a un `NSDate`, utilice un [ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/).

Para usar un `NSDateFormatter`, llame a su [ `ToString` ](https://developer.xamarin.com/api/member/Foundation.NSDateFormatter.ToString/p/Foundation.NSDate/) método. Por ejemplo:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

El [ `DateFormat` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.DateFormat/) propiedad (una cadena) de un `NSDateFormatter` permite una especificación de formato de fecha personalizable:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

El [ `TimeStyle` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.TimeStyle/) propiedad (una [ `NSDateFormatterStyle` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatterStyle/)) de un `NSDateFormatter` especifica el formato de hora en función de los estilos predeterminados:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Diversos `NSDateFormatterStyle` valores horas muestran como sigue:

- `NSDateFormatterStyle.Full`: 7:46:00 P.M. hora de verano del este
- `NSDateFormatterStyle.Long`: 7:47:00 P. M. EDT
- `NSDateFormatterStyle.Medium`: 7:47:00 P.M.
- `NSDateFormatterSytle.Short`: 7:47 P.M.

##### <a name="datestyle"></a>DateStyle

El [ `DateStyle` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.DateStyle/) propiedad (una `NSDateFormatterStyle`) de un `NSDateFormatter` especifica el formato de fecha en función de los estilos predeterminados:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Diversos `NSDateFormatterStyle` valores muestran las fechas como sigue:

- `NSDateFormatterStyle.Full`: Miércoles, 2 de agosto de 2017 a las 7:48 P.M.
- `NSDateFormatterStyle.Long`: De 2 de agosto de 2017 a las 7:49 PM
- `NSDateFormatterStyle.Medium`: De 2 de agosto de 2017, 7:49 p. M.
- `NSDateFormatterStyle.Short`: 8/2/17, 7:50 PM

> [!NOTE]
> `DateFormat` y `DateStyle` / `TimeStyle` proporcionan diferentes maneras de especificar el formato de fecha y hora. La última establecer propiedades de determinan que el formateador de fecha de la salida.

## <a name="related-links"></a>Vínculos relacionados

- [PickerControl (ejemplo)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
