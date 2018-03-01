---
title: "Resumen del capítulo 16. Enlace de datos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 954d5d9e270db156f5ef2577706c667e05ab544c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumen del capítulo 16. Enlace de datos

Los programadores a menudo se encuentran unos escribir controladores de eventos que detectan cuando ha cambiado una propiedad de un objeto y usarlo para cambiar el valor de una propiedad en otro objeto. Puede automatizar este proceso con la técnica de *enlace de datos*. Enlaces de datos normalmente se definen en XAML y se convierten en parte de la definición de la interfaz de usuario.

Muy a menudo, estos enlaces de datos conectan los objetos de interfaz de usuario a los datos subyacentes. Se trata de una técnica que se explora más en [ **capítulo 18. MVVM**](chapter18.md). Sin embargo, los enlaces de datos también pueden conectar dos o más elementos de interfaz de usuario. La mayoría de los primeros ejemplos de enlace de datos en este capítulo muestra esta técnica.

## <a name="binding-basics"></a>Conceptos básicos de enlace

Varias propiedades, métodos y clases implicadas en el enlace de datos:

- El [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) clase se deriva de [ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/) y encapsula muchas características de un enlace de datos
- El [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) propiedad está definida por el [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) (clase)
- El [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) también se define el método por el [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) (clase)
- El [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/) clase define tres adicionales `SetBinding` métodos

Las dos clases siguientes admiten extensiones de marcado XAML para los enlaces:

- [`BindingExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) admite la `Binding` extensión de marcado
- [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) admite la `x:Reference` extensión de marcado

Dos interfaces intervienen en el enlace de datos:

- [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) en el `System.ComponentModel` es el espacio de nombres para implementar notificaciones cuando cambia una propiedad
- [`IValueConverter`](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) se utiliza para definir clases pequeñas que convierten los valores de un tipo a otro en enlaces de datos

Un enlace de datos conecta a dos propiedades del mismo objeto o (más comúnmente) dos objetos diferentes. Estas dos propiedades se conocen como el *origen* y *destino*. Por lo general, un cambio en la propiedad de origen hace un cambio que se produzca en la propiedad de destino, pero a veces se invierte la dirección. A pesar de todo:

- el *destino* propiedad debe estar respaldada por un [`BindableProperty`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- el *origen* propiedad generalmente es un miembro de una clase que implementa [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)

Una clase que implementa `INotifyPropertyChanged` se activa un [ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/) evento cuando cambia una propiedad de valor. `BindableObject` implementa `INotifyPropertyChanged` y automáticamente se activa un `PropertyChanged` evento cuando una propiedad respaldada por un `BindableProperty` cambia los valores, pero también puede escribir sus propias clases que implementan `INotifyPropertyChanged` sin tener que derivar de `BindableObject`.

## <a name="code-and-xaml"></a>Código y XAML

El [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) ejemplo muestra cómo establecer un enlace de datos en el código:

- El origen es el `Value` propiedad de un `Slider`
- El destino es el `Opacity` propiedad de un `Label`

Los dos objetos están conectados mediante el establecimiento del `BindingContext` de la `Label` el objeto a la `Slider` objeto. Las dos propiedades están conectadas mediante una llamada a un [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/) método de extensión en el `Label` que hacen referencia a la `OpacityProperty` propiedad enlazable y `Value` propiedad de la `Slider` expresado como un cadena.

Manipular el `Slider` , a continuación, hace que la `Label` a atenuar u ocultar.

El [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) es el mismo programa con el enlace de datos establecido en XAML. El `BindingContext` de la `Label` está establecido en un `x:Reference` referencia de extensión de marcado el `Slider`y el `Opacity` propiedad de la `Label` está establecido en el `Binding` extensión de marcado con su [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propiedad hace referencia a la `Value` propiedad de la `Slider`.

## <a name="source-and-bindingcontext"></a>Origen y BindingContext

El [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) muestra un enfoque alternativo en el código. A `Binding` objeto se crea al establecer la [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/) propiedad a la `Slider` objeto y el [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propiedad a "Value". El [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método `BindableObject` , a continuación, se llama en el `Label` objeto.

El [ `Binding` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/) también podrían haberse utilizado para definir la `Binding` objeto.

El [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) muestra la técnica comparable en XAML. El `Opacity` propiedad de la `Label` está establecido en un `Binding` extensión de marcado con [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) establecido en el `Value` propiedad y [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/) establecido en un Embedded `x:Reference` extensión de marcado.

En resumen, hay dos maneras de hacer referencia al objeto de origen de enlace:

- A través de la `BindingContext` propiedad del destino
- A través de la `Source` propiedad de la `Binding` propio objeto

Si se especifican ambos, el segundo tiene prioridad. La ventaja de la `BindingContext` es que se propaga a través del árbol visual. Se trata de *muy* práctico si varias propiedades de destino están enlazados al mismo objeto de origen.

El [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programa muestra esta técnica con el [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) elemento. Dos `Button` elementos para navegar hacia delante y hacia atrás heredan un `BindingContext` de su grupo primario que hace referencia a la `WebView`. El `IsEnabled` las propiedades de los dos botones, a continuación, tener simple `Binding` las extensiones de marcado que tienen como destino el botón `IsEnabled` propiedades basan en la configuración de la [ `CanGoBack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoBack/) y [ `CanGoForward` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoForward/) propiedades de solo lectura de la `WebView`.

## <a name="the-binding-mode"></a>El modo de enlace

Establecer el [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/) propiedad de `Binding` a un miembro de la [ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/) enumeración:

- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) por lo que los cambios en la propiedad de origen afectan al objetivo
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) por lo que los cambios en la propiedad de destino afecta al origen
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) para que los cambios en el origen y destino influencias negativas entre sí
- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) Para usar el [ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/) especificado cuando el destino `BindableProperty` se creó. Si no se especifica ninguno, el valor predeterminado es `OneWay` para propiedades enlazables normales, y `OneWayToSource` para propiedades enlazables de solo lectura.

Las propiedades que suelen ser los destinos de enlaces de datos en escenarios MVVM suele tener un `DefaultBindingMode` de `TwoWay`. Estos son:

- `Value` propiedad de `Slider` y `Stepper`
- `IsToggled` propiedad de `Switch`
- `Text` propiedad de `Entry`, `Editor`, y `SearchBar`
- `Date` propiedad de `DatePicker`
- `Time` propiedad de `TimePicker`

El [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) ejemplo muestra los modos de cuatro enlace con un enlace de datos donde el destino es el `FontSize` propiedad de un `Label` y el origen es el `Value` propiedad de un `Slider`. Esto permite que cada `Slider` para controlar el tamaño de fuente de los correspondientes `Label`. Pero la `Slider` elementos no se inicializan porque el `DefaultBindingMode` de la `FontSize` propiedad es `OneWay`.

El [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) ejemplo establece los enlaces en el `Value` propiedad de la `Slider` que hacen referencia a la `FontSize` propiedad de cada `Label`. Esto parece ser con versiones anteriores, pero funciona mejor en inicializar el `Slider` elementos porque el `Value` propiedad de la `Slider` tiene un `DefaultBindingMode` de `TwoWay`.

[![Captura de pantalla triple de invertir enlace](images/ch16fg06-small.png "invertir enlace")](images/ch16fg06-large.png "invertir enlace")

Esto es análogo a cómo se definen los enlaces en MVVM y usará este tipo de enlace con frecuencia.

## <a name="string-formatting"></a>Formato de cadena

Cuando la propiedad de destino es del tipo `string`, puede usar el [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/) propiedad definida por `BindingBase` para convertir el origen en un `string`. Establecer el `StringFormat` propiedad a un formato de cadena que le gustaría usar con el método estático de .NET [ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/) formato para mostrar el objeto. Al utilizar esta cadena de formato dentro de una extensión de marcado, debe encerrarlo entre comillas simples para no confundir con las llaves para una extensión de marcado incrustado.

El [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) ejemplo muestra cómo utilizar `StringFormat` en XAML.

El [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) ejemplo muestra cómo mostrar el tamaño de la página con enlaces a la `Width` y `Height` propiedades de la `ContentPage`.

## <a name="why-is-it-called-path"></a>¿Por qué se lo llama "Path"?

El [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propiedad de `Binding` se denomina así porque puede ser una serie de propiedades e indizadores separados por puntos. El [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) ejemplo muestra varios ejemplos.

## <a name="binding-value-converters"></a>Convertidores de valores de enlace

Cuando las propiedades de origen y de destino de un enlace son tipos diferentes, puede convertir entre los tipos utilizando un convertidor de enlace. Se trata de una clase que implementa el [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) de interfaz y contiene dos métodos: [ `Convert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.Convert/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/) para convertir el origen al destino, y [ `ConvertBack` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.ConvertBack/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/) para convertir el destino en el origen.

El [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca es un ejemplo para convertir un `int` a una `bool`. Se muestra en el [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) ejemplo, que solo permite el `Button` si se ha escrito al menos un carácter en un `Entry`.

El [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) clase convierte un `bool` a una `string` y define dos propiedades para especificar el texto que se debe devolver `false` y `true` valores.
El [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) es similar. El [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) ejemplo muestra cómo utilizar estos dos convertidores para mostrar diferentes textos en diferentes colores en función de un `Switch` configuración.

La interfaz genérica [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) puede reemplazar la `BoolToStringConverter` y `BoolToColorConverter` y actuar como un generalizado `bool`-a-convertidor de objetos de cualquier tipo.

## <a name="bindings-and-custom-views"></a>Enlaces y las vistas personalizadas

Puede simplificar controles personalizados mediante los enlaces de datos. El [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) archivo de código define `Text`, `TextColor`, `FontSize`, `FontAttributes`, y `IsChecked` propiedades, pero no tiene ninguna lógica en absoluto para los objetos visuales del control.
En su lugar el [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) archivo contiene todo el marcado para objetos visuales del control a través de enlaces de datos en el `Label` elementos en función de las propiedades definidas en el archivo de código subyacente.

El [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) ejemplo muestra el `NewCheckBox` control personalizado.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 16 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Ejemplos de capítulo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
