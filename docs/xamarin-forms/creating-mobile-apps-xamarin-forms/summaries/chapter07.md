---
title: "Resumen del capítulo 7. XAML frente a código"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1263328a748ac0bacd368da361aeaff57c4cfa20
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumen del capítulo 7. XAML frente a código

Xamarin.Forms es compatible con un lenguaje de marcado basado en XML denominado Extensible Application Markup Language o XAML (pronunciado "zammel"). XAML proporciona una alternativa a C# para definir el diseño de la interfaz de usuario de una aplicación de Xamarin.Forms y en la definición de enlaces entre los elementos de interfaz de usuario y los datos subyacentes.

## <a name="properties-and-attributes"></a>Propiedades y atributos

Estructuras y clases de Xamarin.Forms se convierten en elementos XML en XAML y propiedades de estas clases y estructuras se convierten en atributos XML. Para crear una instancia en XAML, una clase por lo general debe tener un constructor público sin parámetros. Las propiedades establecidas en XAML deben tener pública `set` descriptores de acceso.

Para las propiedades de los tipos de datos básicos (`string`, `double`, `bool`, etc.), el analizador de XAML usa el estándar `TryParse` métodos para convertir valores de atributo para estos tipos. El analizador de XAML puede controlar fácilmente los tipos de enumeración y pueden combinar los miembros de enumeración si el tipo de enumeración se marca con el `Flags` atributo.

Como ayuda para el analizador de XAML, pueden incluir tipos más complejos (o propiedades de los tipos de) un [ `TypeConverterAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/) que identifica una clase que deriva de [ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/) que admita la conversión desde valores de cadena para esos tipos. Por ejemplo, el [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/) nombres y cadenas, como "#rrggbb" de color se convierte en `Color` valores.

## <a name="property-element-syntax"></a>Sintaxis de elemento de propiedad

En XAML, las clases y los objetos creados a partir de ellos se expresan como elementos XML. Se conocen como *elementos de objeto*. Mayoría de las propiedades de estos objetos se expresa como atributos XML. Se denominan *atributos de la propiedad*.

A veces, una propiedad debe establecerse en un objeto que no se puede expresar como una cadena simple. En tal caso, XAML, es compatible con una etiqueta denominada una *elemento property* que consta del nombre de clase y el nombre de propiedad separados por puntos. Un elemento de objeto, a continuación, puede aparecer dentro de un par de etiquetas de elemento de propiedad.

## <a name="adding-a-xaml-page-to-your-project"></a>Agregar una página XAML a un proyecto

Biblioteca de clases Portable Xamarin.Forms puede contener una página XAML cuando se crea por primera vez, o puede agregar una página XAML a un proyecto existente. En el cuadro de diálogo para agregar un nuevo elemento, elija el elemento que hace referencia a una página XAML, o `ContentPage` y XAML. (No un `ContentView`.)

Se crean dos archivos: un archivo XAML con el XAML de la extensión de nombre de archivo y un archivo de C# con la extensión. Window1.Xaml.cs. El archivo de C# a menudo se conoce como el *código subyacente* del archivo XAML. El archivo de código subyacente es una definición de clase parcial que se deriva de `ContentPage`. En tiempo de compilación, se analiza el código XAML y se genera otra definición de clase parcial para la misma clase. Esta clase generada incluye un método denominado `InitializeComponent` que se llama desde el constructor del archivo de código subyacente.

En tiempo de ejecución, al final de la `InitializeComponent` llamar, todos los elementos del archivo XAML se han crea una instancia y se inicializa como si se hubieran creado en código C#.

El elemento raíz en el archivo XAML es `ContentPage`. La etiqueta raíz contiene al menos dos declaraciones de espacio de nombres XML, uno para los elementos de Xamarin.Forms y la otra define un `x` prefijo para los elementos y atributos intrínsecos a todas las implementaciones de XAML. La etiqueta raíz también contiene un `x:Class` atributo que indica el espacio de nombres y el nombre de la clase que deriva de `ContentPage`. Esto coincide con el nombre de clase y espacio en el archivo de código subyacente.

La combinación de XAML y el código se muestra en el [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) ejemplo.

## <a name="the-xaml-compiler"></a>El compilador XAML

Xamarin.Forms tiene un compilador XAML, pero su uso es opcional en el uso de un [ `XamlCompilationAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/). Si no se compila el código XAML, se analiza el código XAML en tiempo de compilación y el archivo XAML se incrusta en la PCL, donde también se analiza en tiempo de ejecución. Si se compila el código XAML, el proceso de compilación convierte el código XAML en un formato binario y el procesamiento en tiempo de ejecución es más eficaz.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidad de plataforma en el archivo XAML

En XAML, el [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) clase puede usarse para seleccionar marcado depende de la plataforma. Se trata de una clase genérica y se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. El [ `OnIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnIdiom%3CT%3E/) clase es similar pero que se utilizan con mucha menos frecuencia.

El uso de `OnPlatform` ha cambiado desde que se publicó el libro. Se usó originalmente junto con las propiedades denominadas `iOS`, `Android`, y `WinPhone`. Ahora se utiliza con secundarios [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) objetos. Establecer el [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) propiedad a una cadena coherente con el público `const` campos de la [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) clase. Establecer el [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Value/) propiedad en un valor coherente con la `x:TypeArguments` atributo de la `OnPlatform` etiqueta.

`OnPlatform` se muestra en el [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) ejemplo, que se denomina así porque contiene bloques de XAML casi idéntico. La existencia de este marcado redundante sugiere que estén disponibles para reducirlo técnicas.

## <a name="the-content-property-attributes"></a>Los atributos de propiedad de contenido

Algunos elementos de propiedad se producen con mucha frecuencia, como el `<ContentPage.Content>` etiqueta en el elemento raíz de un `ContentPage`, o la `<StackLayout.Children>` etiqueta que incluye los elementos secundarios de `StackLayout`.

Todas las clases se pueden identificar una propiedad con un [ `ContentPropertyAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPropertyAttribute/) en la clase. Para esta propiedad, las etiquetas de elemento de propiedad no son necesarias. `ContentPage` define la propiedad content como `Content`, y `Layout<T>` (la clase de la cual `StackLayout` deriva) define la propiedad content como `Children`. Estas etiquetas de elemento de propiedad no están necesarias.

El elemento de propiedad de `Label` es `Text`.

## <a name="formatted-text"></a>Texto con formato

El [ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) ejemplo contiene varios ejemplos de configuración de la `Text` y `FormattedText` propiedades de `Label`. En XAML, `Span` objetos aparecen como elementos secundarios de la `FormattedString` objeto.

 Cuando se establece una cadena de varias líneas en el `Text` propiedad, caracteres de fin de línea se convierten en caracteres de espacio, pero se conservan los caracteres de fin de línea cuando una cadena de varias líneas aparece como contenido de la `Label` o `Label.Text` etiquetas:

 [![Captura de pantalla triple de uso compartido de variaciones de texto](images/ch07fg03-small.png "variaciones de texto con formato")](images/ch07fg03-large.png "variaciones de texto con formato")



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 7 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Ejemplos de capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Ejemplo de capítulo 7 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
