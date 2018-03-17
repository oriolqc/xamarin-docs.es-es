---
title: Uso de DatePicker
description: Una vista de Xamarin.Forms que permite al usuario seleccionar una fecha
ms.topic: article
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/12/2018
ms.openlocfilehash: 5c214fe4124b900ea63399b97084d1ce0e181d4a
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="using-datepicker"></a>Uso de DatePicker

_Una vista de Xamarin.Forms que permite al usuario seleccionar una fecha_

El Xamarin.Forms [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) invoca al control de selector de fecha de la plataforma y permite al usuario seleccionar una fecha. `DatePicker` define cinco propiedades:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) de tipo [ `DateTime` ](https://developer.xamarin.com/api/type/System.DateTime/), cuyo valor predeterminado es el primer día del año 1900.
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) de tipo `DateTime`, que el valor predeterminado es el último día del año 2100.
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) de tipo `DateTime`, la fecha seleccionada, cuyo valor predeterminado es el valor [ `DateTime.Today` ](https://developer.xamarin.com/api/property/System.DateTime.Today/).
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) de tipo `string`, [estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET dar formato a cadena, cuyo valor predeterminado es "D", patrón de fecha largo.
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.TextColor/) de tipo [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/), el color utilizado para mostrar la fecha seleccionada, cuyo valor predeterminado es [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/).

El `DatePicker` se activa un [ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/) eventos cuando el usuario selecciona una fecha.

> [!WARNING]
> Al establecer `MinimumDate` y `MaximumDate`, asegúrese de que `MinimumDate` siempre es menor o igual que `MaximumDate`. En caso contrario, `DatePicker` , se producirá una excepción.

Internamente, la `DatePicker` garantiza que `Date` entre `MinimumDate` y `MaximumDate`, ambos inclusive. Si `MinimumDate` o `MaximumDate` se establece para que `Date` no está entre ellos, `DatePicker` ajustará el valor de `Date`.

Todas las cinco propiedades están respaldadas por [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) objetos, lo que significa que pueden aplicar los estilos y las propiedades pueden ser destinos de enlaces de datos. El `Date` propiedad tiene un modo de enlace predeterminado de [ `BindingMode.TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/), lo que significa que puede ser un destino de un enlace de datos en una aplicación que utiliza el [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitectura.

## <a name="initializing-the-datetime-properties"></a>Inicializar las propiedades de fecha y hora

En el código, se puede inicializar el `MinimumDate`, `MaximumDate`, y `Date` propiedades con valores de tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Cuando un `DateTime` se expresa en XAML, el analizador XAML usa el `DateTime.Parse` método con un `CultureInfo.InvariantCulture` argumento para convertir la cadena a un `DateTime` valor. Se deben especificar las fechas en un formato preciso: meses de dos dígitos, dos dígitos días y años de cuatro dígitos, separados por barras diagonales:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Si el `BindingContext` propiedad de `DatePicker` está establecida en una instancia de un modelo de vista que contiene las propiedades de tipo `DateTime` denominado `MinDate`, `MaxDate`, y `SelectedDate` (por ejemplo), puede crear instancias de la `DatePicker` similar a éste :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

En este ejemplo, las tres propiedades se inicializan en las propiedades correspondientes en el modelo de vista. Dado que la `Date` propiedad tiene un modo de enlace de `TwoWay`, cualquier nueva fecha que el usuario selecciona se reflejan automáticamente en el modelo de vista.

Si el `DatePicker` no contiene un enlace en su `Date` propiedad, una aplicación debe asociar un controlador para el `DateSelected` evento sea informado cuando el usuario selecciona un nuevo valor de fecha.

## <a name="datepicker-and-layout"></a>DatePicker y diseño

Es posible usar una opción de diseño horizontal no restringida como `Center`, `Start`, o `End` con `DatePicker`:

```xaml
<DatePicker ··· 
            HorizontalOptions="Center" 
            ··· />
```

Sin embargo, esto no se recomienda. Dependiendo del valor de la `Format` propiedad, selecciona las fechas pueden requerir el ancho de pantalla diferente. Por ejemplo, hace que la cadena de formato "D" `DateTime` mostrar las fechas en un formato largo y "Miércoles, 12 de septiembre de 2018" requiere un ancho de pantalla mayor que "Viernes, 4 de mayo de 2018". Según la plataforma, esta diferencia podría hacer que el `DateTime` vista para cambiar el ancho de diseño, o para la visualización se trunque.

> [!TIP]
> Es mejor usar el valor predeterminado `HorizontalOptions` de `Fill` con `DatePicker`y no para usar un ancho de `Auto` al poner `DatePicker` en un `Grid` celda.

## <a name="datepicker-in-an-application"></a>DatePicker en una aplicación

El [ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) ejemplo incluye dos `DatePicker` vistas en su página. Se pueden usar para seleccionar dos fechas, y el sistema calcula el número de días entre esas fechas. El programa no cambia la configuración de la `MinimumDate` y `MaximumDate` propiedades, por lo que las dos fechas deben estar entre 1900 y 2100.

Este es el archivo XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Cada `DatePicker` se le asigna un `Format` propiedad de "D" para un formato de fecha larga. Observe también que la `endDatePicker` objeto tiene un enlace que tenga como destino su `MinimumDate` propiedad. El origen de enlace está seleccionado `Date` propiedad de la `startDatePicker` objeto. Esto garantiza que la fecha de finalización es siempre posterior o igual que la fecha de inicio. Además de los dos `DatePicker` objetos, un `Switch` tiene la etiqueta "Incluyen dos días en total". 

Los dos `DatePicker` vistas tienen controladores asociados a la `DateSelected` eventos y el `Switch` ha adjuntado un controlador a su `Toggled` eventos. Estos controladores de eventos están en el archivo de código subyacente y desencadenan un nuevo cálculo de los días entre las dos fechas:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

Cuando el ejemplo se ejecuta primero, ambos `DatePicker` vistas se inicializan con fecha de hoy. Captura de pantalla siguiente muestra el programa que se ejecuta en la plataforma Universal de Windows, iOS y Android:

[![Días entre las fechas de inicio de](datepicker-images/DaysBetweenDatesStart.png "días entre las fechas de inicio de")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "días entre las fechas de inicio")

Pulse en cualquiera de los `DatePicker` muestra, se invoca el selector de fecha de la plataforma. Las tres plataformas implementan este selector de fecha de maneras muy diferentes, pero cada enfoque es familiar para los usuarios de esa plataforma:

[![Seleccionar días entre las fechas](datepicker-images/DaysBetweenDatesSelect.png "días entre fechas seleccione")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "seleccionar días entre las fechas")

Una vez seleccionadas las dos fechas, la aplicación muestra el número de días entre esas fechas:

[![Días entre las fechas de resultados](datepicker-images/DaysBetweenDatesResult.png "días entre las fechas de resultados")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "días entre las fechas de resultados")

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DaysBetweenDates](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [DatePicker API](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)
