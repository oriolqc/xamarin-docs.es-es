---
title: Resumen de capítulo 2. Anatomía de una aplicación
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 2. Anatomía de una aplicación'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 208cf28341ceaa43d1c56b4f5086dc98febee6be
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242752"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumen de capítulo 2. Anatomía de una aplicación

En una aplicación de Xamarin.Forms, los objetos que ocupan espacio en la pantalla se conocen como *elementos visuales*, encapsulado por el [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) clase. Elementos visuales se pueden dividir en tres categorías que correspondan a estas clases:

- [Página](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)
- [Diseño](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [Vista](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)

Un `Page` derivado ocupa toda la pantalla, o casi toda la pantalla. A menudo, el elemento secundario de una página es una `Layout` derivado para organizar los elementos visuales secundarios. Los elementos secundarios de la `Layout` puede ser otro `Layout` clases o `View` derivados (a menudo denominado *elementos*), que son objetos familiares como texto, mapas de bits, controles deslizantes, botones, cuadros de lista y así sucesivamente.

En este capítulo se muestra cómo crear una aplicación centrándose en la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), que es el `View` derivado que muestra texto.

## <a name="say-hello"></a>Le presentamos

Con la plataforma Xamarin instalada, puede crear una nueva solución de Xamarin.Forms en Visual Studio o Visual Studio para Mac. El [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) solución utiliza una biblioteca de clases Portable para el código común. Muestra una solución de Xamarin.Forms creada en Visual Studio sin modificaciones. La solución consta de seis proyectos (los últimos dos de los cuales no se crean con las plantillas de solución de Xamarin.Forms actuales):

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una biblioteca de clases portables (PCL) compartidos por los demás proyectos
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un proyecto de aplicación para Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un proyecto de aplicación para iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un proyecto de aplicación para la plataforma Universal de Windows (Windows 10 y Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), un proyecto de aplicación para Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), un proyecto de aplicación para Windows Phone 8.1

Puede realizar cualquiera de estos proyectos de aplicación del proyecto de inicio y, a continuación, compile y ejecute el programa en un dispositivo o el simulador.

En muchos de los programas de Xamarin.Forms, no puede modificar los proyectos de aplicación. A menudo, estas permanecen pequeño código auxiliar para iniciar el programa. La mayor parte de su enfoque será la biblioteca de clases Portable comunes a todas las aplicaciones.

## <a name="inside-the-files"></a>Dentro de los archivos

Los objetos visuales mostrados por el **Hello** programa se definen en el constructor de la [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) clase. `App` deriva de la clase Xamarin.Forms [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/).

El **referencias** sección de la **Hello** proyecto PCL incluye los siguientes ensamblados de Xamarin.Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

El **referencias** secciones de los proyectos de cinco aplicación incluyen ensamblados adicionales que se aplican a las plataformas individuales:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

Cada uno de los proyectos de aplicación contiene una llamada a estático `Forms.Init` método en el `Xamarin.Forms` espacio de nombres. Esto inicializa la biblioteca de Xamarin.Forms. Una versión diferente de `Forms.Init` se define para cada plataforma. Las llamadas a este método se pueden encontrar en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` (clase), `OnLaunched` (método)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App` (clase), `OnLaunched` (método)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App` (clase), `OnLaunched` (método)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

Además, debe crear una instancia de cada plataforma la `App` clase ubicación en la PCL. Esto se produce en una llamada a `LoadApplication` en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

En caso contrario, estos proyectos de aplicación son programas normal "no hacer nada".

## <a name="pcl-or-sap"></a>¿PCL o SAP?

Es posible crear una solución de Xamarin.Forms con el código común en una biblioteca de clases portables (PCL) o un proyecto de Asset compartida (SAP). Para crear una solución SAP, seleccione la opción de Shared en Visual Studio. El [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) solución muestra la plantilla de SAP sin modificaciones.

Las agrupaciones de enfoque PCL todos comunes de código en un proyecto de biblioteca que se hace referencia a los proyectos de aplicación de plataforma. Con el enfoque SAP, el código común eficazmente existe en todos los proyectos de aplicación de plataforma y se comparte entre ellos.

La mayoría de los desarrolladores de Xamarin.Forms prefieren el enfoque PCL. En este libro, la mayoría de las soluciones son PCL. Los usuarios que usen SAP incluyen un **Sap** sufijo del nombre del proyecto.

Para admitir todas las plataformas de Xamarin.Forms, la versión de .NET usa en la PCL debe tenerla en cuenta las siguientes plataformas:

- .NET Framework 4.5
- Windows 8
- Windows Phone 8,1
- Xamarin.Android
- Xamarin.iOS
- Xamarin.IOS (clásico)

Esto se conoce como PC perfil 111.

Con el enfoque SAP, el código en el proyecto compartido puede ejecutar código diferente para distintas plataformas mediante directivas de preprocesador de C# (`#if`, #`elif`, y `#endif`) con estos predefinido identificadores:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`
- Windows 8.1: `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

En una PCL puede determinar qué plataforma se está ejecutando en tiempo de ejecución, como verá más adelante en este capítulo.

## <a name="labels-for-text"></a>Etiquetas de texto

El [ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) solución muestra cómo agregar un nuevo archivo de C# para el **Greetings** proyecto. Este archivo define una clase denominada `GreetingsPage` que se deriva de `ContentPage`. En este libro, la mayoría de los proyectos contiene un solo `ContentPage` derivado cuyo nombre es el nombre del proyecto con el sufijo `Page` anexado.

El `GreetingsPage` constructor crea instancias de un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) vista, que es la vista de Xamarin.Forms que muestra el texto. El [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad se establece en el texto mostrado por el `Label`. Este programa establece la `Label` a la `Content` propiedad de `ContentPage`. El constructor de la `App` , a continuación, crea una instancia de clase `GreetingsPage` y lo establece en su `MainPage` propiedad.

El texto se muestra en la esquina superior izquierda de la página. En iOS, esto significa que se superpone a barra de estado de la página. Existen varias soluciones para este problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solución 1. Incluir relleno en la página

Establecer un [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) propiedad en la página. `Padding` es de tipo [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/), una estructura con cuatro propiedades:

- [`Left`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Left/)
- [`Top`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Top/)
- [`Right`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Right/)
- [`Bottom`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Bottom/)

`Padding` define un área dentro de una página donde se excluye el contenido. Esto permite la `Label` para evitar sobrescribir la barra de estado de iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solución 2. Incluir relleno simplemente para iOS (solo SAP)

Establecer una propiedad 'Relleno' solo en iOS mediante una SAP con una directiva de preprocesador de C#. Esto se muestra en el [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) solución.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solución 3. Incluir relleno simplemente para iOS (PCL o SAP)

En la versión de Xamarin.Forms utilizado el libro, un `Padding` propiedad específica de iOS en una PCL o SAP se puede seleccionar mediante el [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) o [ `Device.OnPlatform<T>` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform%7BT%7D/p/T/T/T/) método estático. Estos métodos están en desuso

El `Device.OnPlatform` métodos se usan para ejecutar código específico de plataforma o seleccionar valores específicos de la plataforma. Internamente, hacen uso de la [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) propiedad de solo lectura estático, que devuelve un miembro de la [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumeración:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) para Windows 8.1, Windows Phone 8.1 y todos los dispositivos UWP.
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), anteriormente utilizado para identificar Windows Phone 8.0 pero está ahora sin usar
- [`Other`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Other/) no se utiliza

El `Device.OnPlatform` métodos, el `Device.OS` propiedad y el `TargetPlatform` enumeración son todos ahora en desuso. En su lugar, use la [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) propiedad y compare el `string` devuelve valor con los siguientes campos estáticos:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), la cadena "iOS"
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), la cadena "Android"
- [`UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), la cadena "UWP", que hace referencia a la plataforma de Windows en tiempo de ejecución
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Windows/), la cadena "Windows" para el tiempo de ejecución de Windows (Windows 8.1 y Windows Phone 8.1)
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), la cadena "WinPhone" para Windows Phone 8.0

El [ `Device.Idiom` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.Idiom/) está relacionada con la propiedad estática de solo lectura. Esto devuelve un miembro de la [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/), que tiene estos miembros:

- [`Desktop`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Desktop/)
- [`Tablet`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Tablet/)
- [`Phone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Phone/)
- [`Unsupported`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Unsupported/) no se utiliza

Para iOS y Android, el límite entre `Tablet` y `Phone` es un ancho vertical de 600 unidades. Para la plataforma Windows, `Desktop` indica una aplicación UWP que se ejecuta en Windows 10, `Tablet` es un programa de Windows 8.1, y `Phone` indica una aplicación UWP que se ejecuta en Windows 10 o una aplicación de Windows Phone 8.1.

## <a name="solution-3a-set-margin-on-the-label"></a>Solución 3a. Margen de conjunto en la etiqueta

El [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propiedad se introdujo demasiado tarde para que se incluyan en el libro, pero también es de tipo `Thickness` y establézcalo en la `Label` para definir un área fuera de la vista que se incluye en el cálculo de la diseño de la vista.

El `Padding` propiedad solo está disponible en [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) y [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivados. El `Margin` propiedad está disponible en todos los [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) derivados.

## <a name="solution-4-center-the-label-within-the-page"></a>Solución 4. Centro de la etiqueta en la página

Puede Centrar el `Label` dentro de la `Page` (o colocarla en uno de los ocho otros lugares) estableciendo el [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades de la `Label` en un valor de tipo [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/). El `LayoutOptions` estructura define dos propiedades:

- Un [ `Alignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) propiedad de tipo [ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/), una enumeración con cuatro miembros: [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/), lo que significa que izquierdo o superior en función de la orientación, [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/), [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/), lo que significa derecha o inferior dependiendo de la orientación y [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/).

- Un [ `Expands` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) propiedad de tipo `bool`.

Normalmente estas propiedades no se usan directamente. En su lugar, combinaciones de estas dos propiedades proceden de ocho propiedades estáticas de sólo lectura de tipo `LayoutOptions`:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`HorizontalOptions` y `VerticalOptions` son las propiedades más importantes en el diseño de Xamarin.Forms y se tratan con más detalle en [ **Chapter 4. Desplazamiento de la pila**](chapter04.md).

Éste es el resultado con el `HorizontalOptions` y `VerticalOptions` propiedades de `Label` establecido en `LayoutOptions.Center`:

[![Triple captura de pantalla del programa de greetings](images/ch02fg05-small.png "horizontalmente y verticalmente centrado etiquetar")](images/ch02fg05-large.png#lightbox "horizontalmente y verticalmente etiquetar centrado")

## <a name="solution-5-center-the-text-within-the-label"></a>Solución de 5. Centrar el texto dentro de la etiqueta

También puede Centrar el texto (o colocarlo en ocho otras ubicaciones en la página) estableciendo el [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) y [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) propiedades de `Label` a un miembro de la [ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/) enumeración:

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/), significado izquierda o superior (en función de orientación)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.End/), lo que significa que a la derecha o inferior (en función de orientación)

Estas dos propiedades se definen solo `Label`, mientras que la `HorizontalAlignment` y `VerticalAlignment` propiedades se definen mediante `View` y hereda todas `View` derivados. Los resultados visuales pueden parecer similares, pero son muy diferentes, como se muestra en el siguiente capítulo.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Ejemplos de capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Ejemplos de capítulo 2 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/index.md)
