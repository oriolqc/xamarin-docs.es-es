---
title: Resumen de capítulo 15. La interfaz interactiva
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c5b2bc00c4337969322193966f26ce0e151f426e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumen de capítulo 15. La interfaz interactiva

En este capítulo se explora ocho `View` derivados que permiten la interacción con el usuario.

## <a name="view-overview"></a>Ver información general

Xamarin.Forms contiene 20 clases se pueden crear instancias que se derivan de `View` pero no `Layout`. Seis de ellos se han tratado en capítulos anteriores:

- `Label`: [ **Capítulo 2. Anatomía de una aplicación**](chapter02.md)
- `BoxView`: [ **Capítulo 3. Desplazamiento de la pila**](chapter03.md)
- `Button`: [ **Capítulo 6. Clics de botón**](chapter06.md)
- `Image`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **14 del capítulo. AbsoluteLayout**](chapter14.md)

Las vistas de ocho en este capítulo eficazmente permiten al usuario interactuar con los tipos de datos básicos de. NET:

|Tipo de datos|Vistas|
|--- |--- |
|`Double`|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/), [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|
|`Boolean`|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|
|`String`|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/), [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|
|`DateTime`|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/), [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|

Estas vistas se pueden considerar como representaciones visuales de interactivas de los tipos de datos subyacente. Este concepto se explora más en el próximo capítulo, [ **capítulo 16. Enlace de datos**](chapter16.md).

Las vistas de seis restantes se tratan en los capítulos siguientes:

- `WebView`: [ **Capítulo 16. enlace de datos**](chapter16.md)
- `Picker`: [ **Capítulo 19. Vistas de colección**](chapter19.md)
- `ListView`: [ **Capítulo 19. Vistas de colección**](chapter19.md)
- `TableView`: [ **Capítulo 19. Vistas de colección**](chapter19.md)
- `Map`: [ **Capítulo 28. Ubicación y mapas**](chapter28.md)
- `OpenGLView`: No se tratan en este libro (y no se admite para las plataformas de Windows)

## <a name="slider-and-stepper"></a>Control deslizante y paso a paso desencadene

Ambos [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) y [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) permitir al usuario elegir un valor numérico de un intervalo. El `Slider` es un intervalo continuo mientras el `Stepper` implica valores discretos.

### <a name="slider-basics"></a>Conceptos básicos del control deslizante

El [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) es un valor que representa un intervalo de valores entre un mínimo de la izquierda hasta un máximo de la derecha de la barra horizontal. Define tres propiedades públicas:

- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) de tipo `double`, valor predeterminado de 0
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) de tipo `double`, valor predeterminado de 0
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) de tipo `double`, valor predeterminado de 1

Las propiedades enlazables que realizan estas propiedades asegurarse de que son coherentes:

- Para todas las propiedades de tres, el [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) especificado para la propiedad enlazable garantiza que el método `Value` entre `Minimum` y `Maximum`.
- El [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/) método `MinimumProperty` devuelve `false` si `Minimum` se establece en un valor mayor o igual que `Maximum`y similares para `MaximumProperty`. Devolver `false` desde el `validateValue` método causas un `ArgumentException` que se genere.

`Slider` se activa la [ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/) eventos con un [ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/) argumento cuando el `Value` cambios de propiedades, mediante programación o cuando el usuario manipula el `Slider`.

El [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) ejemplo muestra el uso simple de la `Slider`.

### <a name="common-pitfalls"></a>Dificultades comunes

En el código y en XAML, el `Minimum` y `Maximum` propiedades se establecen en el orden que especifique. No olvide inicializar estas propiedades para que `Maximum` siempre es mayor que `Minimum`. De lo contrario, se producirá una excepción.

Inicializar el `Slider` propiedades pueden provocar la `Value` propiedad que se va a cambiar y el `ValueChanged` eventos que se deben activar. Debe asegurarse de que el `Slider` controlador de eventos no tener acceso a vistas que aún no ha sido creadas durante la inicialización de la página.

El `ValueChanged` evento no se desencadena durante `Slider` inicialización a menos que la `Value` cambios de propiedad. Puede llamar a la `ValueChanged` controlador directamente desde el código.

### <a name="slider-color-selection"></a>Selección de color del control deslizante

El [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programa contiene tres `Slider` elementos que le permiten seleccionar un color de forma interactiva mediante la especificación de sus valores RGB:

[![Captura de pantalla triple de controles deslizantes de R G B](images/ch15fg03-small.png "controles deslizantes RGB")](images/ch15fg03-large.png#lightbox "controles deslizantes RGB")

El [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) ejemplo usa dos `Slider` elementos que se va a mover dos `Label` elementos a través de un `AbsoluteLayout` y atenuación uno en el otro.

### <a name="the-stepper-difference"></a>La diferencia de paso a paso desencadene

El [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) define las mismas propiedades y eventos como `Slider` pero la `Maximum` propiedad se inicializa en 100 y `Stepper` define una cuarta propiedad:

- [`Increment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) de tipo `double`, inicializado en 1

Visualmente, el `Stepper` consta de dos botones con la etiqueta **&ndash;** y **+**. Al presionar **&ndash;** disminuye `Value` por `Increment` a un mínimo de `Minimum`. Al presionar **+** aumenta `Value` por `Increment` hasta un máximo de `Maximum`.

Esto se demuestra la [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) ejemplo.

## <a name="switch-and-checkbox"></a>Casilla de verificación y conmutador

El [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) permite al usuario especificar un valor booleano.

### <a name="switch-basics"></a>Conceptos básicos de conmutador

Visualmente, el `Switch` consta de un control de alternancia que se pueden activar y encenderlo. La clase define una propiedad:

- [`IsToggled`](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) de tipo `bool`

`Switch` define un evento:

- [`Toggled`](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) acompañado de un [ `ToggledEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToggledEventArgs/) objeto, que se desencadena cuando el `IsToggled` cambios de propiedad.

El [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programa muestra el `Switch`.

### <a name="a-traditional-checkbox"></a>Una casilla de verificación tradicional

Algunos desarrolladores de software podría ser preferible más tradicional `CheckBox` a la `Switch`. El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un `CheckBox` clase que deriva de `ContentView`. `CheckBox` se implementa mediante el [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) y [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) archivos. `CheckBox` define tres propiedades (`Text`, `FontSize`, y `IsChecked`) y un `CheckedChanged` eventos.

El [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) ejemplo se muestra cómo hacerlo `CheckBox`.

## <a name="typing-text"></a>Escribir texto

Xamarin.Forms define tres vistas que permiten al usuario escribir y editar texto:

- [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para una sola línea de texto
- [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) para varias líneas de texto
- [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) para una sola línea de texto para realizar búsquedas.

`Entry` y `Editor` derivan de [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/), que deriva de `View`. `SearchBar` se deriva directamente de `View`.

### <a name="keyboard-and-focus"></a>Teclado y el foco

En los teléfonos y tabletas sin teclados físicos, la `Entry`, `Editor`, y `SearchBar` un teclado virtual para emergente hacer que todos los elementos. La presencia de este teclado en la pantalla está relacionado con el foco de entrada. Una vista debe tener su [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) y [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) propiedades establecidas en `true` para obtener el foco de entrada.

Dos métodos, una propiedad de solo lectura y dos eventos relacionados con el foco de entrada. Estos se definen mediante `VisualElement`:

- El [ `Focus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Focus()/) método intenta establecer el foco de entrada en un elemento y devuelve `true` si se realiza correctamente
- El [ `Unfocus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Unfocus()/) método quita el foco de entrada de un elemento
- El [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) propiedad de solo lectura indica si el elemento tiene el foco de entrada
- El [ `Focused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Focused/) evento indica cuando un elemento recibe el foco de entrada
- El [ `Unfocused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Unfocused/) evento indica cuando un elemento pierde el foco de entrada

### <a name="choosing-the-keyboard"></a>Elegir el teclado

El [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) clase desde la que `Entry` y `Editor` derivar define sólo una propiedad:

- [`Keyboard`](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) de tipo [`Keyboard`](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)

Indica el tipo de teclado que se muestra. Algunos teclados están optimizados para URI o números.

El `Keyboard` clase permite definir un teclado con una variable static [ `Keyboard.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Keyboard.Create/p/Xamarin.Forms.KeyboardFlags/) método con un argumento de tipo [ `KeyboardFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardFlags/), una enumeración con los marcadores de bits siguientes:

- `None` Establezca en 0
- [`CapitalizeSentence`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.CapitalizeSentence/) establecido en 1
- [`Spellcheck`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Spellcheck/) establecido en 2
- [`Suggestions`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Suggestions/) establecido en 4
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.All/) establecido en \xFFFFFFFF

Cuando se utiliza la propiedad multilínea [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) cuando se espera un párrafo o más de texto, una llamada a `Keyboard.Create` es un enfoque adecuado para seleccionar un teclado. Para la línea [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), las siguientes propiedades estáticas de solo lectura de `Keyboard` son útiles:

- [`Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Default/)
- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Text/)
- [`Chat`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Chat/)
- [`Url`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Url/)
- [`Email`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Email/)
- [`Telephone`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Telephone/)
- [`Numeric`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Numeric/) para los números positivos con o sin un separador decimal.

El [ `KeyboardTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardTypeConverter/) permite especificar estas propiedades en XAML, como se muestra en el [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programa.

### <a name="entry-properties-and-events"></a>Eventos y propiedades de entrada

La línea [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) define las siguientes propiedades:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) de tipo `string`, el texto que aparece en el `Entry`
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) de tipo `Color`
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontFamily/) de tipo `string`
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontSize/) de tipo `double`
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontAttributes/) de tipo `FontAttributes`
- [`IsPassword`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.IsPassword/) de tipo `bool`, lo que hace que caracteres que se va a enmascarar
- [`Placeholder`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) de tipo `string`, coloreado tenue texto que aparece en la `Entry` antes de que se escribe nada
- [`PlaceholderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.PlaceholderColor/) de tipo `Color`

El `Entry` también define dos eventos:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) con un [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) objeto, que se desencadena cada vez que la `Text` cambios de propiedad
- [`Completed`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/), que se desencadena cuando el usuario finaliza y se descarta el teclado. El usuario indica la finalización de una manera específica de la plataforma

El [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) muestra estos dos eventos.

### <a name="the-editor-difference"></a>La diferencia del Editor

La propiedad multilínea [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) define el mismo `Text` y `Font` propiedades como `Entry` , pero no las demás propiedades. `Editor` También define las mismas dos propiedades que `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) es un programa de tomar notas de forma libre que se guarda y restaura el contenido de la `Editor`.

### <a name="the-searchbar"></a>El SearchBar

El [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) no se deriva de `InputView`, por lo que no tiene un `Keyboard` propiedad. Pero tiene todos los `Text`, `Font`, y `Placeholder` propiedades que `Entry` define. Además, `SearchBar` define tres propiedades adicionales:

- [`CancelButtonColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.CancelButtonColor/) de tipo `Color`
- [`SearchCommand`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) de tipo [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) para su uso con enlaces de datos y MVVM
- [`SearchCommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) de tipo `Object`, para su uso con `SearchCommand`

El específico de la plataforma Cancelar botón borra el texto. El `SearchBar` también tiene un botón de búsqueda específicos de la plataforma. Al presionar cualquiera de estos botones genera uno de los dos eventos que `SearchBar` define:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.TextChanged/) acompañado de un [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) objeto
- [`SearchButtonPressed`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)

El [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) ejemplo muestra el `SearchBar`.

## <a name="date-and-time-selection"></a>Selección de fecha y hora

El [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) y [ `TimePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) vistas implementan controles específicos de la plataforma que permiten al usuario especificar una fecha u hora.

### <a name="the-datepicker"></a>El selector de fechas

[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) define cuatro propiedades:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) de tipo `DateTime`, inicializado al 1 de enero de 1900
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) de tipo `DateTime`, inicializado a 31 de diciembre de 2100
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) de tipo `DateTime`, inicializado a `DateTime.Today`
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) de tipo `string`, .NET inicializado a "d", el patrón de fecha corta, de cadena de formato resultante en una presentación de la fecha como "7/20/1969" la zona horaria del Pacífico.

Puede establecer el `DateTime` dar formato a propiedades en XAML expresar las propiedades como elementos de propiedad y usando la referencia cultural invariable fecha corta ("7/20/1969").   

El [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) ejemplo calcula el número de días entre dos fechas seleccionadas por el usuario.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>El TimePicker (o es una TimeSpanPicker?)

[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) define dos propiedades y ningún evento:

- [`Time`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) es de tipo `TimeSpan` en lugar de `DateTime`, que indica el tiempo transcurrido desde medianoche
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Format/) de tipo `string`, .NET dar el formato de cadena que se inicializa en "t", el patrón de hora corta, que resulta en una presentación de tiempo como "1:45 PM" la zona horaria del Pacífico.

El [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programa muestra cómo utilizar el `TimePicker` para especificar una hora para un temporizador. El programa sólo funciona si mantiene en primer plano.

**SetTimer** también muestra cómo utilizar el [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) método `Page` para mostrar un cuadro de alerta.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 15 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Ejemplos de capítulo 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Entrada](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
