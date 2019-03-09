---
title: Portable Visual Basic.NET
description: Esta guía explica cómo se puede usar Visual Basic para escribir los proyectos de biblioteca de clases portables (PCL) que pueden utilizarse en soluciones dirigidas a Xamarin.iOS y Xamarin.Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
---

# <a name="portable-visual-basicnet"></a>Portable Visual Basic.NET

Xamarin proyectos de iOS y Android no admiten de forma nativa Visual Basic; Sin embargo, los desarrolladores pueden usar bibliotecas de clases portables para migrar código existente de Visual Basic para iOS y Android, o para escribir una parte importante de su lógica de aplicación en Visual Basic. Las aplicaciones de Xamarin.Forms pueden crearse completamente en Visual Basic (excepto los representadores personalizados, servicios de dependencia y código subyacente XAML).

## <a name="requirements"></a>Requisitos

Se agregó compatibilidad con bibliotecas de clases portable en Xamarin.Android 4.10.1 Xamarin.iOS 7.0.4 y Xamarin Studio 4.2, lo que significa que los proyectos de Xamarin creados con esas herramientas pueden incorporar los ensamblados de PCL de Visual Basic.

Para crear y compilar bibliotecas de clases portables de Visual Basic debe usar Visual Studio en Windows (Visual Studio 2012 o posterior).

> [!NOTE]
> Bibliotecas de Visual Basic solo se pueden crear y compilaron con Visual Studio. Xamarin.iOS y Xamarin.Android no admite el lenguaje Visual Basic.
>
> Si trabaja exclusivamente en Visual Studio puede hacer referencia el proyecto de Visual Basic desde los proyectos de Xamarin.iOS y Xamarin.Android.
>
> Si los proyectos de Android y iOS también se deben cargar en Visual Studio para Mac debe hacer referencia el ensamblado de salida de la PCL de Visual Basic.


## <a name="creating-a-visual-basicnet-pcl"></a>Creación de una PCL Basic.NET Visual

Esta sección se explica cómo crear una biblioteca de clases Portable de Visual Basic con Visual Studio.
A continuación, se puede hacer referencia a la biblioteca en otros proyectos, incluidas las aplicaciones de Xamarin.iOS, Xamarin.Android y Xamarin.Forms.

### <a name="creating-a-pcl"></a>Creación de una PCL

Al agregar una PCL de Visual Basic en Visual Studio debe elegir un perfil que describe qué plataformas debe ser compatible con la biblioteca. Los perfiles se explican en la introducción a documento PCL.

Los pasos para crear una PCL y elija su perfil son:

1.  En el **nuevo proyecto** pantalla, seleccione el **Visual Basic > biblioteca de clases (Portable)** opción:

    [![](images/image1-sml.png "Crear nueva biblioteca Portable de Visual Basic")](images/image1.png#lightbox)

1.  Visual Studio le pedirá inmediatamente con los siguientes **Agregar biblioteca de clases Portable** cuadro de diálogo para que se puede configurar el perfil. Marque las plataformas que necesita para admitir y presione **Aceptar**.

    [![](images/image2-sml.png "Seleccione el perfil de PCL eligiendo las plataformas")](images/image2.png#lightbox)

1.  El proyecto de PCL de Visual Basic aparecerá como se muestra en el **el Explorador de soluciones** similar al siguiente:

    [![](images/image3-sml.png "Proyecto vacío de PCL de Visual Studio")](images/image3.png#lightbox)


La PCL ahora está lista para el código de Visual Basic que se va a agregar. Proyectos PCL pueden hacer referencia a otros proyectos (proyectos de aplicación, los proyectos de biblioteca e incluso otros proyectos PCL).

### <a name="editing-the-pcl-profile"></a>Editar el perfil PCL

Se pueden ver y cambiar con el botón secundario en el proyecto y elija el perfil de PCL (que controla qué plataformas es compatible con la PCL) **Propiedades > biblioteca > cambiar...** . En esta captura de pantalla se muestra el cuadro de diálogo resultante:

 [![](images/image4-sml.png "Editar perfil de PCL en las propiedades del proyecto")](images/image4.png#lightbox)

Si se cambia el perfil después de código ya se agregó a la PCL, es posible que la biblioteca ya no se compilará si el código hace referencia a las características que no forman parte del perfil recién seleccionado.


## <a name="summary"></a>Resumen

Este artículo se demuestra cómo utilizar código de Visual Basic en aplicaciones de Xamarin con Visual Studio y bibliotecas de clases portables. Aunque Xamarin no es compatible directamente con Visual Basic, la compilación de Visual Basic en una PCL permite que el código escrito con Visual Basic que se incluirán en las aplicaciones iOS y Android.

Las páginas siguientes describen cómo usar PCL de Visual Basic.NET en modo nativo o las aplicaciones de Xamarin.Forms:

- [Creación de aplicaciones de Xamarin.iOS y Xamarin.Android nativas que usan VB](native-apps.md)
- [Creación de aplicaciones de Xamarin.Forms con VB](xamarin-forms.md)


## <a name="related-links"></a>Vínculos relacionados

- [TaskyPortableVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Desarrollo multiplataforma con .NET Framework (Microsoft)](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
