---
title: Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 58d3b3ae067913a85c3ada5f5b35e64511523ff8
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207913"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Uno de los trabajos más desagradables en la programación es migrar un código base a partir de una plataforma a otra, especialmente si esa plataforma implica un lenguaje de programación diferente. Hay un tentación al trasladar el código para refactorizarlo también, pero si ambas plataformas deben mantenerse en paralelo, a continuación, las diferencias entre las bases de código de dos dificultan el mantenimiento futuro más.

## <a name="cross-platform-mobile-development"></a>Desarrollo móvil multiplataforma

Este problema es común cuando tenga como destino plataformas móviles. Actualmente, existen dos principales plataformas móviles, la familia de Apple de iPhone e iPad que ejecutan el sistema operativo iOS y el sistema operativo Android que se ejecuta en una variedad de teléfonos y tabletas. Otra significativa plataforma es Universal Windows Platform (UWP de Microsoft), lo que permite un único programa de destino tanto Windows 10.

Un proveedor de software que desea que estas plataformas de destino debe ocuparse de paradigmas de interfaz de usuario diferente, tres entornos de desarrollo diferentes, tres interfaces de programación diferentes y&mdash;quizás más cómodamente&mdash;tres lenguajes de programación diferentes: Objective-C para el iPhone y iPad, Java para Android, y C# para Windows.

## <a name="the-c-and-net-solution"></a>La solución de C# y .NET

Aunque Objective-C, Java y C# se derivan desde el lenguaje de programación de C, han evolucionado mediante rutas de acceso muy diferentes. C# es la más reciente de estos lenguajes y ha sido en proceso de maduración de maneras muy útil. Además, C# está estrechamente relacionado con una completa infraestructura de programación llamada. NET, que proporciona compatibilidad para matemáticas, depuración, reflexión, colecciones, globalización, E/S de archivos, redes, seguridad, subprocesos, servicios web, control de datos y XML y JSON de lectura y escritura.

Actualmente, Xamarin ofrece herramientas para tener como destino el Mac, iOS y Android API con C# y .NET native. Estas herramientas se conocen como Xamarin.Mac, Xamarin.iOS y Xamarin.Android, que se conocen colectivamente como la plataforma Xamarin. Estos son los enlaces que expresan las API nativas de estas plataformas con expresiones de .NET y bibliotecas.

Los desarrolladores pueden usar la plataforma de Xamarin para escribir aplicaciones C# en ese destino Mac, iOS o Android. Pero cuando más de una plataforma de destino, tiene mucho sentido compartir parte del código entre las plataformas de destino. Esto implica separar el programa en código dependiente de la plataforma (que generalmente implica la interfaz de usuario) y código independiente de la plataforma, lo que generalmente requiere sólo la base de .NET framework. Este código independiente de la plataforma puede residir en una biblioteca de clases Portable (PCL) o un proyecto compartido, a menudo denominado un proyecto de recurso compartido o SAP.

> [!NOTE]
> Bibliotecas de clases portables se han reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares.

## <a name="introducing-xamarinforms"></a>Introducción a Xamarin.Forms

Cuando tenga como destino varias plataformas móviles, Xamarin.Forms permite compartir el código aún más. Un único programa escrito para Xamarin.Forms puede tener como destino estas plataformas:

- iOS para los programas que se ejecutan en el iPhone, iPad y iPod touch
- Android para programas que se ejecutan en teléfonos y tabletas Android
- la plataforma Universal de Windows para Windows 10 de destino

> [!NOTE]
> Xamarin.Forms ya no es compatible con Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, pero las aplicaciones de Xamarin.Forms se ejecutan en el escritorio de Windows 10. También es compatible con la versión preliminar del [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md), y [Tizen](~/xamarin-forms/platform/other/tizen.md) plataformas.

La mayor parte de un programa de Xamarin.Forms existe en una biblioteca o SAP. Cada una de las plataformas consta de un código auxiliar de pequeña aplicación que llama a este código compartido.

Las APIs Xamarin.Forms se asignan a los controles nativos en cada plataforma, para que cada plataforma mantiene su aspecto característico:

[![Captura de pantalla triple de uso compartido de los objetos visuales de plataforma](images/ch01fg03-small.png "controles de Xamarin.Forms en cada plataforma")](images/ch01fg03-large.png#lightbox "controles de Xamarin.Forms en cada plataforma")

Las capturas de pantalla de izquierda a derecha muestran un iPhone y un teléfono Android:

En cada pantalla, la página contiene un objeto Xamarin.Forms [ `Label` ](xref:Xamarin.Forms.Label) para mostrar texto, un [ `Button` ](xref:Xamarin.Forms.Button) para iniciar acciones, un [ `Switch` ](xref:Xamarin.Forms.Switch) para elegir un valor activado/desactivado y un [ `Slider` ](xref:Xamarin.Forms.Slider) para especificar un valor dentro de un intervalo continuo. Cuatro de esas vistas es elementos secundarios de un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) en un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage).

También se adjunta a la página es una barra de herramientas de Xamarin.Forms que consta de varios [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) objetos. Estos son visibles como iconos en la parte superior de las pantallas de Android y iOS y en la parte inferior de la pantalla de Windows 10 Mobile.

Xamarin.Forms también admite XAML, el lenguaje de marcado de aplicaciones Extensible desarrolladas en Microsoft para varias plataformas de aplicación. Todos los objetos visuales del programa anterior se definen en XAML, como se muestra en el [ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) ejemplo.

Puede determinar en qué plataforma se está ejecutando en un programa de Xamarin.Forms y se ejecutar código diferente según corresponda. Más eficaz, los desarrolladores pueden escribir código personalizado para las diferentes plataformas y ejecutar ese código desde un programa de Xamarin.Forms de forma independiente de la plataforma. Los desarrolladores pueden crear también controles adicionales mediante la escritura de representadores para cada plataforma.

Mientras que Xamarin.Forms es una buena solución para aplicaciones de línea de negocio, o para crear prototipos, o realizar una demostración rápida de prueba de concepto, es menos ideal para aplicaciones que requieren interacción táctil compleja o gráficos vectoriales.

## <a name="your-development-environment"></a>El entorno de desarrollo

El entorno de desarrollo depende de qué plataformas de que destino y lo que los equipos desee utilizar.

Si desea destinados a iOS, necesitará un equipo Mac con Xcode y la plataforma Xamarin instalado. Compatibilidad con Android también requiere la instalación de Java y los SDK necesarios. Puede, a continuación, tener como destino iOS y Android mediante Visual Studio para Mac.

Instalación de Visual Studio en el equipo le permite tener como destino iOS, Android y todas las plataformas de Windows. Sin embargo, aún como destino iOS desde Visual Studio requiere un equipo Mac con Xcode y la plataforma Xamarin instalado.

Puede probar programas en cualquier un dispositivo real conectado mediante USB en el equipo o en un simulador.

## <a name="installation"></a>Instalación

Antes de crear y compilar una aplicación de Xamarin.Forms, debe intentar crear y compilar por separado en una aplicación de iOS, una aplicación de Android y una aplicación de UWP, dependiendo de las plataformas de destino y el entorno de desarrollo.

Los sitios web de Xamarin y Microsoft contienen información sobre cómo hacerlo:

- [Introducción a iOS](~/ios/get-started/index.md)
- [Introducción a Android](~/android/get-started/index.md)
- [Centro de desarrollo de Windows](http://dev.windows.com)

Una vez que puede crear y ejecutar proyectos para estas plataformas individuales, debería tener problemas para crear y ejecutar una aplicación de Xamarin.Forms.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 1, texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Capítulo 1 (ejemplo)](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
