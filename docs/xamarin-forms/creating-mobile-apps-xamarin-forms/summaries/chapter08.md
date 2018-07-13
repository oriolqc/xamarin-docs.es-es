---
title: Resumen del capítulo 8. Código y XAML en armonía
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 8. Código y XAML en armonía'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b08355db6cc90381b16f51ce7bf23be8e8bd4e14
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994538"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumen del capítulo 8. Código y XAML en armonía

Este capítulo analiza XAML mayor profundidad, y especialmente cómo código y XAML interactúan.

## <a name="passing-arguments"></a>Pasar argumentos

Por lo general, una clase que crea una instancia en XAML debe tener un constructor público sin parámetros; el objeto resultante se inicializa mediante la configuración de la propiedad. Sin embargo, hay otras dos formas que se pueden crear instancias de objetos e inicializados.

Aunque son técnicas de propósito generales, se usan principalmente en relación con los modelos de vista de MVVM.

### <a name="constructors-with-arguments"></a>Constructores con argumentos

El [ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) ejemplo muestra cómo usar el `x:Arguments` etiquetas para especificar los argumentos de constructor. Estos argumentos deben estar delimitados por etiquetas de elemento que indica el tipo del argumento. Para los tipos de datos de .NET básicos, están disponibles las siguientes etiquetas:

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

El [ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) ejemplo muestra cómo usar el `x:FactoryMethod` elemento para especificar un método de generador que se invoca para crear un objeto. Este tipo de método de generador debe ser public y static y debe crear un objeto del tipo en el que está definido. (Por ejemplo el [ `Color.FromRgb` ](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) método califica porque es estático y público y devuelve un valor de tipo `Color`.) Se especifican los argumentos para el método de fábrica en `x:Arguments` etiquetas.

## <a name="the-xname-attribute"></a>El atributo x: Name

El `x:Name` atributo permite que un objeto que se crea una instancia en XAML que se asignará un nombre. Las reglas para estos nombres son los mismos que para los nombres de variables de C#. Después de la devolución de la `InitializeComponent` llame en el constructor, el archivo de código subyacente puede hacer referencia a estos nombres para tener acceso al elemento correspondiente de XAML. Los nombres se convierten realmente por el analizador XAML en campos privados de la clase parcial generada.

El [ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) ejemplo muestra el uso de `x:Name` para permitir que el archivo de código subyacente mantener dos `Label` elementos definidos en XAML que se actualiza con la fecha y hora actuales.

No se puede usar el mismo nombre para varios elementos en la misma página. Esto es un problema concreto, si usa `OnPlatform` crear en paralelo los objetos con nombre para cada plataforma. El [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) muestra una mejor manera de hacer algo como esto.

## <a name="custom-xaml-based-views"></a>Vistas personalizadas basadas en XAML

Hay varias maneras de evitar la repetición de marcado en XAML. Una técnica común consiste en crear una nueva clase basada en XAML que se deriva de [ `ContentView` ](xref:Xamarin.Forms.ContentView). Esta técnica se muestra en el [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) ejemplo. El `ColorView` clase se deriva de `ContentView` para mostrar un color determinado y su nombre, mientras que el `ColorViewListPage` clase se deriva de `ContentPage` como de costumbre y explícitamente crea 17 instancias de `ColorView`.

Obtener acceso a la `ColorView` clase en XAML requiere otra declaración de espacio de nombres XML, normalmente denominado `local` para las clases en el mismo ensamblado.

## <a name="events-and-handlers"></a>Eventos y controladores

Los eventos se pueden asignar a los controladores de eventos en XAML, pero el propio controlador de eventos debe implementarse en el archivo de código subyacente. El [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) muestra cómo crear una interfaz de usuario de teclado en XAML y cómo implementar el `Clicked` controladores en el archivo de código subyacente.

## <a name="tap-gestures"></a>Puntee en gestos

Cualquier `View` objeto puede obtener la entrada táctil y generar eventos de esa entrada. El `View` clase define un [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) propiedad de colección que puede contener uno o más instancias de clases que derivan de [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer).

El [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) genera [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventos. El [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) programa muestra cómo asociar `TapGestureRecognizer` objetos a cuatro `BoxView` elementos para crear un juego imitación:

[![Captura de pantalla de triple de derivación de monkey](images/ch08fg07-small.png "imitación juego")](images/ch08fg07-large.png#lightbox "imitación juego")

Pero la **MonkeyTap** programa necesita realmente el sonido. (Consulte [el próximo capítulo](chapter09.md).)



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 8 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Ejemplos de capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Ejemplo de capítulo 8 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
