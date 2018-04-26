---
title: Visual Basic.NET portátil
description: Esta guía explica cómo se puede utilizar Visual Basic para escribir los proyectos de biblioteca de clases portables (PCL) que se pueden usar en las soluciones destinadas a Xamarin.iOS y Xamarin.Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f37ff8a2d07681ba8e4ec3ed6ad7e5bbd85e6502
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="portable-visual-basicnet"></a>Visual Basic.NET portátil

Xamarin iOS y Android proyectos no admiten de forma nativa Visual Basic; Sin embargo, los programadores pueden usar bibliotecas de clases Portable para migrar código existente de Visual Basic para iOS y Android, o para escribir una parte significativa de la lógica de aplicación en Visual Basic. Aplicaciones de Xamarin.Forms pueden crearse completamente en Visual Basic (excepto los representadores personalizados y servicios de dependencia, código subyacente XAML).

## <a name="requirements"></a>Requisitos

Se agregó compatibilidad con bibliotecas de clases portables en Xamarin.Android 4.10.1, Xamarin.iOS 7.0.4 y Xamarin Studio 4.2, lo que significa que cualquier Xamarin los proyectos creados con estas herramientas pueden incorporar ensamblados PCL de Visual Basic.

Para crear y compilar bibliotecas de clases Portable de Visual Basic debe usar Visual Studio en Windows (Visual Studio 2012 o posterior).

> [!NOTE]
> Bibliotecas de Visual Basic solo se pueden crear y compilan con Visual Studio. Xamarin.iOS y Xamarin.Android son compatibles con el lenguaje Visual Basic.
>
> Si funciona únicamente en Visual Studio puede hacer referencia el proyecto de Visual Basic desde proyectos Xamarin.iOS y Xamarin.Android.
>
> Si los proyectos de Android y iOS también se pueden cargar en Visual Studio para Mac debe hacer referencia el ensamblado de salida en la PCL de Visual Basic.


## <a name="creating-a-visual-basicnet-pcl"></a>Crear una PCL Basic.NET Visual

Esta sección le guía por el proceso crear una biblioteca de clases Portable de Visual Basic con Visual Studio.
A continuación, puede hacer referencia a la biblioteca en otros proyectos, incluidas las aplicaciones Xamarin.iOS y Xamarin.Android, Xamarin.Forms.

### <a name="creating-a-pcl"></a>Crear una PCL

Al agregar una PCL de Visual Basic en Visual Studio debe elegir un perfil que describe qué plataformas debe ser compatible con la biblioteca. Los perfiles se explican en la introducción a documento PCL.

Los pasos para crear una PCL y elija su perfil son:

1.  En el **nuevo proyecto** pantalla, seleccione la **Visual Basic > biblioteca de clases (Portable)** opción:

    [![](images/image1-sml.png "Crear nueva biblioteca Portable de Visual Basic")](images/image1.png#lightbox)

1.  Visual Studio le pedirá inmediatamente con el siguiente **Agregar biblioteca de clases Portable** cuadro de diálogo para que se puede configurar el perfil. Las plataformas que necesita para admitir y presione de graduación **Aceptar**.

    [![](images/image2-sml.png "Seleccione el perfil de PCL eligiendo plataformas")](images/image2.png#lightbox)

1.  El proyecto de Visual Basic PCL aparecerá como se muestra en el **el Explorador de soluciones** similar a la siguiente:

    [![](images/image3-sml.png "Proyecto vacío de servidor PCL de Visual Studio")](images/image3.png#lightbox)


El PCL ahora está preparado para código de Visual Basic va a agregar. Proyectos PCL pueden hacer referencia a otros proyectos (proyectos de aplicaciones, proyectos de biblioteca e incluso otros proyectos PCL).

### <a name="editing-the-pcl-profile"></a>Edite el perfil PCL

Se pueden ver y cambiar, haga doble clic en el proyecto y elija el perfil de PCL (que controla qué plataformas es compatible con la PCL) **Propiedades > biblioteca > cambio...** . El cuadro de diálogo resultante se muestra en esta captura de pantalla:

 [![](images/image4-sml.png "Editar perfil de PCL en Propiedades del proyecto")](images/image4.png#lightbox)

Si se cambia después de que ya se ha añadido código en la PCL, es posible que la biblioteca ya no se compilará si el código hace referencia a características que no forman parte del perfil recién seleccionado.


## <a name="summary"></a>Resumen

Este artículo se demuestra cómo utilizar código de Visual Basic en aplicaciones de Xamarin con Visual Studio y bibliotecas de clases Portable. Aunque Xamarin no es compatible directamente con Visual Basic, la compilación de Visual Basic en una PCL permite al código escrito con Visual Basic para incluirse en aplicaciones iOS y Android.

Las páginas siguientes describen cómo usar PCLs de Visual Basic.NET en modo nativo o aplicaciones de Xamarin.Forms:

- [Compilar aplicaciones nativas de Xamarin.iOS y Xamarin.Android que usan VB](native-apps.md)
- [Creación de aplicaciones de Xamarin.Forms con VB](xamarin-forms.md)


## <a name="related-links"></a>Vínculos relacionados

- [TaskyPortableVB (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [El desarrollo multiplataforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)