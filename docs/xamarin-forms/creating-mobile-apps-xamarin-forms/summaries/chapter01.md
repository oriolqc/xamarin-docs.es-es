---
title: Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 534c36a16acdc10ffb6f6b6703296a672875286e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?

Uno de los trabajos más desagradables en la programación es migrar un código base a partir de una plataforma a otra, especialmente si esa plataforma implica un lenguaje de programación diferente. Hay un tentación de recurrir al trasladar el código para refactorizarlo así, pero si ambas plataformas se deben mantener en paralelo, a continuación, las diferencias entre las bases de dos código dificultan el mantenimiento futuro más.

## <a name="cross-platform-mobile-development"></a>Desarrollo móvil multiplataforma

Este problema es común al elegir como destino plataformas móviles. Actualmente, existen dos principales plataformas móviles, la familia de Apple de iPhone e iPad que ejecutan el sistema operativo iOS y el sistema operativo Android que se ejecuta en una variedad de teléfonos y tabletas. Otra plataforma significativo es Universal plataforma Windows (UWP de Microsoft), lo que permite un único programa como destino Windows 10 y Windows 10 Mobile.

Un proveedor de software que desea que estos tres plataformas de destino debe tratar con los paradigmas de interfaz de usuario diferente, tres entornos de desarrollo diferentes, tres interfaces de programación diferentes y&mdash;quizás más cómodamente&mdash; tres de los distintos lenguajes de programación: Objective-C para el iPhone y iPad, Java para Android y C# para Windows.

## <a name="the-c-and-net-solution"></a>La solución de C# y .NET

Aunque Objective-C, Java y C# se derivan desde el lenguaje de programación de C, han evolucionado por las rutas de acceso muy diferentes. C# es la más reciente de estos lenguajes y ha sido en proceso de maduración de maneras muy útil. Además, C# está estrechamente relacionado con una infraestructura de programación completa denominada. NET, que proporciona compatibilidad para matemáticas, depuración, reflexión, colecciones, globalización, E/S de archivos, redes, seguridad, subprocesamiento, servicios web, control de datos y XML y JSON de lectura y escritura.

Actualmente, Xamarin ofrece herramientas que tenga como destino el Mac, iOS y Android API con C# y .NET native. Estas herramientas se denominan Xamarin.Mac, Xamarin.iOS y Xamarin.Android, que se conocen colectivamente como la plataforma de Xamarin. Se trata de bibliotecas y los enlaces que expresan las API nativas de estas plataformas con lenguajes. NET.

A los desarrolladores pueden usar la plataforma Xamarin para escribir aplicaciones en C# orientados a Mac, iOS o Android. Pero cuando se destinan a más de una plataforma, que resulta muy adecuado para compartir una parte del código entre las plataformas de destino. Esto implica separar el programa en el código depende de la plataforma (por lo general de la interfaz de usuario) y código independiente de la plataforma, que requiere normalmente solo base de .NET framework. Este código independiente de la plataforma o puede residir en una biblioteca de clases portables (PCL) o un proyecto compartido, llamar a menudo a un proyecto de recurso compartido o SAP.

## <a name="introducing-xamarinforms"></a>Introducción a Xamarin.Forms

Al elegir como destino varias plataformas móviles, Xamarin.Forms permite compartir aún más el código. Un único programa escrito para Xamarin.Forms puede tener como destino cinco plataformas distintas:

- iOS para programas que se ejecutan en el iPhone, iPad y iPod touch
- Android para programas que se ejecutan en teléfonos y tabletas Android
- la plataforma Universal de Windows al destino Windows 10 y Windows 10 Mobile
- la API de Windows Runtime de Windows 8.1
- el tiempo de ejecución de Windows API de Windows Phone 8.1

Las plantillas de solución de Xamarin.Forms actuales no incluyen plantillas de proyectos para las plataformas de Windows 8.1 y Windows Phone 8.1.

La mayor parte de un programa de Xamarin.Forms existe en una PCL o SAP. Cada una de las plataformas consta de un código auxiliar de pequeña aplicación que llama en la PCL. Las APIs Xamarin.Forms se asignan a los controles nativos en cada plataforma, para que cada plataforma mantiene su apariencia y funcionamiento característico:

[![Captura de pantalla triple de objetos visuales de la plataforma de uso compartido](images/ch01fg03-small.png "Xamarin.Forms controles en cada plataforma")](images/ch01fg03-large.png#lightbox "Xamarin.Forms controles en cada plataforma")

Las capturas de pantalla de izquierda a derecha muestran un iPhone, un teléfono Android y un teléfono de Windows 10 Mobile. En cada pantalla, la página contiene un Xamarin.Forms [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para mostrar texto, un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) para iniciar acciones, un [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) para Si se elige un valor activado/desactivado y un [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) para especificar un valor dentro de un intervalo continuo. Los cuatro de esas vistas son elementos secundarios de un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) en un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/).

También está conectado a la página es una barra de herramientas de Xamarin.Forms que consta de varios [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) objetos. Éstas aparecen como iconos en la parte superior de iOS y Android pantallas y en la parte inferior de la pantalla de Windows 10 Mobile.

Xamarin.Forms también admite XAML, el lenguaje de marcado de aplicaciones Extensible desarrollado en Microsoft para varias plataformas de aplicación. Todos los objetos visuales del programa mostrado anteriormente se definen en XAML, como se muestra en el [ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) ejemplo.

Un programa de Xamarin.Forms puede determinar qué plataforma se está ejecutando en y ejecutar otro código en consecuencia. Más eficaz, los programadores pueden escribir código personalizado para las diversas plataformas y ejecutar ese código desde un programa de Xamarin.Forms de forma independiente de la plataforma. Los desarrolladores también pueden crear controles adicionales mediante la escritura de representadores para cada plataforma.

Aunque Xamarin.Forms es una buena solución para aplicaciones de línea de negocio, o para la creación de prototipos, o realizar una demostración de prueba de concepto rápida, es menos ideal para las aplicaciones que requieran interacción táctil compleja o gráficos vectoriales.

## <a name="your-development-environment"></a>El entorno de desarrollo

El entorno de desarrollo depende de qué plataformas de que destino y lo que los equipos desean utilizar.

Si desea destino iOS, necesitará un Mac con Xcode y la plataforma Xamarin instalado. Compatibilidad con Android también requiere la instalación de Java y los SDK necesarios. Después, puede dirigir iOS y Android mediante Visual Studio para Mac.

Instalar Visual Studio en el equipo le permite marcar todas las plataformas de Windows, iOS y Android. Sin embargo, todavía iOS desde Visual Studio de destino requiere un equipo Mac con Xcode y la plataforma Xamarin instalado.

Puede probar programas en cualquier un dispositivo real conectado mediante USB en el equipo o en un simulador.

## <a name="installation"></a>Instalación

Antes de crear y compilar una aplicación de Xamarin.Forms, debe intentar crear y compilar por separado en una aplicación de iOS, una aplicación de Android y una aplicación de UWP, dependiendo de las plataformas que desea para el entorno de desarrollo y de destino.

Los sitios web de Xamarin y Microsoft contienen información sobre cómo hacerlo:

- [Introducción a iOS](~/ios/get-started/index.md)
- [Introducción a Android](~/android/get-started/index.md)
- [Centro de desarrollo de Windows](http://dev.windows.com)

Una vez puede crear y ejecutar proyectos de esas plataformas individuales, debería presentar ningún problema crear y ejecutar una aplicación de Xamarin.Forms.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Capítulo 1 (ejemplo)](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
