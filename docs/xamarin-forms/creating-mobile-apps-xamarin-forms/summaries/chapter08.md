---
title: "Resumen del capítulo 8. Código y XAML en armonía"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 452a7835bcb54501edffe7a2467544c6677616ba
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumen del capítulo 8. Código y XAML en armonía

En este capítulo se explora XAML más profundidad, y especialmente cómo código y XAML interactúan.

## <a name="passing-arguments"></a>Pasar argumentos

Por lo general, una clase que se crea una instancia en XAML debe tener un constructor sin parámetros público; el objeto resultante se inicializa a través de la configuración de las propiedades. Sin embargo, hay otras dos formas de que los objetos se pueden crear instancias e inicializados.

Aunque se trata de técnicas de uso generales, se usan principalmente en relación con MVVM ver modelos.

### <a name="constructors-with-arguments"></a>Constructores con argumentos

El [ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) ejemplo muestra cómo utilizar la `x:Arguments` etiqueta para especificar los argumentos de constructor. Estos argumentos deben estar delimitados por etiquetas de elemento que indica el tipo del argumento. Para los tipos de datos básicos. NET, están disponibles las siguientes etiquetas:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>¿Se puede llamar a métodos de XAML?

El [ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) ejemplo muestra cómo utilizar el `x:FactoryMethod` elemento para especificar un método de generador que se invoca para crear un objeto. Este tipo de método de generador debe ser público y estático, y debe crear un objeto del tipo en el que está definido. (Por ejemplo la [ `Color.FromRgb` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/)) método califica porque es public y static y devuelve un valor de tipo `Color`.) Se especifican los argumentos para el método de fábrica en `x:Arguments` etiquetas.

## <a name="the-xname-attribute"></a>El atributo x: Name

El `x:Name` atributo permite que un objeto crea una instancia en XAML que se debe proporcionar un nombre. Las reglas para estos nombres son los mismos que los nombres de variables de C#. Después de la devolución de la `InitializeComponent` de la llamada en el constructor, el archivo de código subyacente puede hacer referencia a estos nombres para tener acceso al elemento correspondiente de XAML. Los nombres se convierten realmente el analizador de XAML en campos privados en la clase parcial generada.

El [ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) ejemplo muestra el uso de `x:Name` para permitir que el archivo de código subyacente mantener dos `Label` elementos definidos en XAML que se actualiza con la fecha y hora actuales.

No se puede usar el mismo nombre para varios elementos en la misma página. Esto es un problema concreto, si utiliza `OnPlatform` crear paralelo objetos con nombre para cada plataforma. El [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) ejemplo muestra una mejor manera de hacer algo similar.

## <a name="custom-xaml-based-views"></a>Vistas personalizadas basadas en XAML

Hay varias maneras de evitar la repetición del marcado en XAML. Una técnica común consiste en crear una nueva clase basada en XAML que se deriva de [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/). Esta técnica se muestra en el [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) ejemplo. El `ColorView` clase se deriva de `ContentView` para mostrar un color determinado y su nombre, mientras que la `ColorViewListPage` clase se deriva de `ContentPage` como de costumbre y explícitamente crea 17 instancias de `ColorView`.

Obtener acceso a la `ColorView` clase en XAML requiere otra declaración de espacio de nombres XML, normalmente denominado `local` para las clases en el mismo ensamblado.

## <a name="events-and-handlers"></a>Eventos y controladores

Eventos se pueden asignar a los controladores de eventos en XAML, pero se debe implementar su propio controlador de eventos en el archivo de código subyacente. El [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) muestra cómo crear una interfaz de usuario de teclado en XAML y cómo implementar el `Clicked` controladores en el archivo de código subyacente.

## <a name="tap-gestures"></a>Pulse movimientos

Cualquier `View` objeto puede obtener la entrada táctil y generar eventos de esa entrada. El `View` clase define un [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) propiedad de colección que puede contener una o más instancias de clases que derivan de [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/).

El [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) genera [ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/) eventos. El [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) programa muestra cómo asociar `TapGestureRecognizer` objetos a cuatro `BoxView` elementos que se va a crear un juego imitación:

[![Captura de pantalla de triple de derivación de mono](images/ch08fg07-small.png "imitación juego")](images/ch08fg07-large.png#lightbox "imitación juego")

Pero la **MonkeyTap** programa necesita realmente el sonido. (Consulte [el próximo capítulo](chapter09.md).)



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 8 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Ejemplos de capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Ejemplo de capítulo 8 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
