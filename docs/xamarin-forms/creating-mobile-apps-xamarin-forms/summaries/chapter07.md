---
title: Resumen del capítulo 7. XAML frente a código
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 7. XAML frente a código'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052574"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumen del capítulo 7. XAML frente a código

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Xamarin.Forms es compatible con un lenguaje de marcado basado en XML denominado lenguaje de marcado de aplicaciones Extensible o XAML (pronunciado "zammel"). XAML proporciona una alternativa a C# en la definición del diseño de la interfaz de usuario de una aplicación de Xamarin.Forms y, en definir enlaces entre los elementos de interfaz de usuario y los datos subyacentes.

## <a name="properties-and-attributes"></a>Las propiedades y atributos

Estructuras y clases de Xamarin.Forms se convierten en elementos XML en XAML y propiedades de estas clases y estructuras se convierten en atributos XML. Para crear instancias en XAML, una clase por lo general debe tener un constructor público sin parámetros. Las propiedades establecidas en XAML deben tener public `set` descriptores de acceso.

Para las propiedades de los tipos de datos básicos (`string`, `double`, `bool`, etc.), el analizador XAML utiliza la norma `TryParse` métodos para convertir valores de atributo para estos tipos. El analizador XAML puede controlar fácilmente los tipos de enumeración y pueden combinar miembros de la enumeración si el tipo de enumeración se marca con el `Flags` atributo.

Ayuda para el analizador XAML, pueden incluir tipos más complejos (o propiedades de esos tipos) un [ `TypeConverterAttribute` ](xref:Xamarin.Forms.TypeConverterAttribute) que identifica una clase que derive de [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) que admita la conversión desde valores de cadena para esos tipos. Por ejemplo, el [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) convierte color nombres y las cadenas, como "#rrggbb", en `Color` valores.

## <a name="property-element-syntax"></a>Sintaxis de elemento de propiedad

En XAML, las clases y los objetos creados a partir de ellos se expresan como elementos XML. Estos se conocen como *elementos de objeto*. Mayoría de las propiedades de estos objetos se expresa como atributos XML. Se denominan *atributos de la propiedad*.

A veces, una propiedad debe establecerse en un objeto que no se puede expresar como una cadena simple. En tal caso, XAML admite una etiqueta denominada una *property (elemento)* que consta del nombre de clase y el nombre de propiedad separados por un punto. Un elemento de objeto, a continuación, puede aparecer dentro de un par de etiquetas de elemento de propiedad.

## <a name="adding-a-xaml-page-to-your-project"></a>Agregar una página XAML al proyecto

Biblioteca de clases Portable Xamarin.Forms puede contener una página XAML cuando se crea por primera vez, o puede agregar una página XAML a un proyecto existente. En el cuadro de diálogo para agregar un nuevo elemento, elija el elemento que hace referencia a una página XAML, o `ContentPage` y XAML. (No un `ContentView`.)

> [!NOTE]
> Opciones de Visual Studio han cambiado desde que se escribió este capítulo.

Se crean dos archivos: un archivo XAML con el XAML de extensión de nombre de archivo y un archivo de C# con la extensión. xaml.cs. El archivo de C# a menudo se conoce como el *código* del archivo XAML. El archivo de código subyacente es una definición de clase parcial que se deriva de `ContentPage`. En tiempo de compilación, se analiza el XAML y se genera otra definición de clase parcial para la misma clase. Esta clase generada incluye un método denominado `InitializeComponent` que se llama desde el constructor del archivo de código subyacente.

En tiempo de ejecución al finalizar la `InitializeComponent` llamar, todos los elementos del archivo XAML se han crea una instancia e inicializado como si se hubieran creado en código C#.

El elemento raíz en el archivo XAML es `ContentPage`. La etiqueta raíz contiene al menos dos declaraciones de espacios de nombres XML, uno para los elementos de Xamarin.Forms y la otra define un `x` prefijo para los elementos y atributos intrínsecos a todas las implementaciones de XAML. La etiqueta raíz también contiene un `x:Class` atributo que indica el espacio de nombres y el nombre de la clase que deriva de `ContentPage`. Esto coincide con el nombre de clase y espacio de nombres en el archivo de código subyacente.

La combinación de XAML y código se demuestra el [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) ejemplo.

## <a name="the-xaml-compiler"></a>El compilador XAML

Xamarin.Forms tiene un compilador XAML, pero su uso es opcional, según el uso de un [ `XamlCompilationAttribute` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Si no se compila el XAML, se analiza el XAML en tiempo de compilación y se incrusta el archivo XAML en la PCL, donde también se analiza en tiempo de ejecución. Si se compila el XAML, el proceso de compilación convierte el XAML en un formato binario y el procesamiento en tiempo de ejecución es más eficaz.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidad de plataforma en el archivo XAML

En XAML, el [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) clase puede usarse para seleccionar el marcado depende de la plataforma. Esto es una clase genérica y se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. El [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) clase es similar pero que se utilizan con mucha menos frecuencia.

El uso de `OnPlatform` ha cambiado desde que se publicó el libro. Se utilizó originalmente junto con propiedades denominadas `iOS`, `Android`, y `WinPhone`. Ahora se usa con el elemento secundario [ `On` ](xref:Xamarin.Forms.On) objetos. Establecer el [ `Platform` ](xref:Xamarin.Forms.On.Platform) propiedad en una cadena coherente con el público `const` campos de la [ `Device` ](xref:Xamarin.Forms.Device) clase. Establecer el [ `Value` ](xref:Xamarin.Forms.On.Value) propiedad en un valor coherente con la `x:TypeArguments` atributo de la `OnPlatform` etiqueta.

`OnPlatform` se muestra en el [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) ejemplo, se denomina así porque contiene bloques de XAML casi idéntica. La existencia de este marcado repetitivas sugiere que las técnicas de deben estar disponibles para reducirlo.

## <a name="the-content-property-attributes"></a>Los atributos de propiedad de contenido

Algunos elementos de propiedad que se producen con mucha frecuencia, como el `<ContentPage.Content>` la etiqueta en el elemento raíz de un `ContentPage`, o el `<StackLayout.Children>` etiqueta que incluye los elementos secundarios de `StackLayout`.

Todas las clases se pueden identificar una propiedad con un [ `ContentPropertyAttribute` ](xref:Xamarin.Forms.ContentPropertyAttribute) en la clase. Para esta propiedad, las etiquetas de elemento de propiedad no son necesarias. `ContentPage` define su propiedad de contenido como `Content`, y `Layout<T>` (la clase de la cual `StackLayout` deriva) define su propiedad de contenido como `Children`. Estas etiquetas de elemento de propiedad no son necesarias.

El elemento de propiedad de `Label` es `Text`.

## <a name="formatted-text"></a>Texto con formato

El [ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) ejemplo contiene varios ejemplos de configuración de la `Text` y `FormattedText` propiedades de `Label`. En XAML, `Span` objetos aparecen como elementos secundarios de la `FormattedString` objeto.

 Cuando se establece una cadena de varias líneas en el `Text` propiedad, caracteres de fin de línea se convierten en caracteres de espacio, pero se conservan los caracteres de fin de línea cuando una cadena de varias líneas aparece como contenido de la `Label` o `Label.Text` etiquetas:

 [![Captura de pantalla triple de uso compartido de variaciones de texto](images/ch07fg03-small.png "variaciones de texto con formato")](images/ch07fg03-large.png#lightbox "variaciones con el formato de texto")

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 7 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Ejemplos de capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Capítulo 7 F# ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Conceptos básicos de XAML)
