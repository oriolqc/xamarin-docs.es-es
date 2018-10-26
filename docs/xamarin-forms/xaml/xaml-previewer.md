---
title: Controlador de vista previa XAML para Xamarin.Forms
description: En este artículo se explica cómo usar a la vista previa de XAML para ver sus diseños de Xamarin.Forms representan a medida que escribe. El controlador de vista previa de XAML está disponible en Visual Studio 2017 y Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/31/2018
ms.openlocfilehash: def7a7a3bdd9e165252c5ad1928b89ec654e5d74
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108093"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Controlador de vista previa XAML para Xamarin.Forms

_Vea los diseños de Xamarin.Forms representan a medida que escribe._

## <a name="requirements"></a>Requisitos

Los proyectos requieren el paquete Xamarin.Forms NuGet más reciente para el controlador de vista previa de XAML para que funcione. Vista previa de aplicaciones para Android requiere [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Puede encontrar más información en el [notas de la versión](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Introducción

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

El controlador de vista previa de XAML está activada de forma predeterminada y puede controlarse desde el **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. En este cuadro de diálogo puede seleccionar la vista de documento predeterminada y la orientación dividida.

[![Vista previa del control ListView en Visual Studio](xaml-previewer-images/xamlp-options-vs.png "opciones de controlador de vista previa de formularios de Visual Studio")](xaml-previewer-images/xamlp-options-vs.png#lightbox "opciones de controlador de vista previa de formularios de Visual Studio")

Al abrir una página XAML que se dividirá el editor basado en la configuración seleccionada en el **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. Sin embargo, estas preferencias pueden cambiarse en la ventana del editor.

## <a name="xaml-preview-controls"></a>Controles de vista previa XAML

La parte superior de la ventana del editor tiene botones para seleccionar qué panel está en uso, con el botón superior cambiar al panel de diseño y el botón Cambiar al panel de origen. El botón central intercambia el orden de panel.

[![Vista previa del control ListView en Visual Studio](xaml-previewer-images/xamlp-controls-vs.png "controla el panel de vista previa de Forms en Visual Studio")](xaml-previewer-images/xamlp-controls-vs.png#lightbox "controla el panel de vista previa de Forms en Visual Studio")

La parte inferior de la ventana del editor tiene botones vertical y horizontalmente, dividir los paneles y para expandir o contraer el panel secundario actual.

[![Vista previa del control ListView en Visual Studio](xaml-previewer-images/xamlp-controls2-vs.png "controla el panel de vista previa de Forms en Visual Studio")](xaml-previewer-images/xamlp-controls2-vs.png#lightbox "controla el panel de vista previa de Forms en Visual Studio")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

El **Preview** botón se muestra en el editor cuando se abre una página XAML. El panel de vista previa se puede mostrar u ocultar presionando el **Preview** situado en la esquina superior derecha de cualquier ventana de documento XAML:

[![Versión preliminar del control ListView en Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "controlador de vista previa de Forms en Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox "controlador de vista previa de Forms en Visual Studio para Mac")

-----

## <a name="xaml-preview-options"></a>Opciones de vista previa XAML

Las opciones en la parte superior del panel de vista previa son:

* **Teléfono** – se representan en una pantalla de tamaño de teléfono
* **Tablet** – se representan en una pantalla de Tablet PC tamaño (Observe que no hay controles de zoom en la esquina inferior derecha del panel)
* **Android** : mostrar la versión de Android de la pantalla
* **iOS** : mostrar la versión de iOS de la pantalla
* Portait (icono) – utiliza orientación vertical para la versión preliminar
* Horizontal (icono) – utiliza orientación para la versión preliminar de horizontal

## <a name="adding-design-time-data"></a>Agregar datos de tiempo de diseño

Algunos diseños pueden ser difíciles visualizar sin los datos enlazados a los controles de interfaz de usuario. Para que la versión preliminar más útil, asignar algunos datos estáticos a los controles por codificar un contexto de enlace (ya sea en el código subyacente o mediante XAML).

Hacer referencia a la de James Montemagno [entrada de blog sobre cómo agregar datos en tiempo de diseño](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver cómo enlazar a una clase ViewModel estático en XAML.

## <a name="detecting-design-mode"></a>Detectar el modo de diseño

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propiedad puede examinarse para determinar si la aplicación se está ejecutando en el controlador de vista previa. Esto le permite especificar código que solo se ejecutará cuando la aplicación se ejecuta en el controlador de vista previa:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>Solución de problemas

Compruebe los problemas siguientes y el [foros de Xamarin](https://forums.xamarin.com/categories/xamarin-forms), si tiene problemas.

### <a name="xaml-preview-isnt-showing"></a>Vista previa de XAML no está visible

Compruebe lo siguiente:

* Proyecto debe compilarse (compilado) antes de intentar obtener una vista previa de los archivos XAML.
* El agente de diseñador debe ser la configuración de la primera vez que obtenga una vista previa de un archivo XAML, aparecerá un indicador de progreso en la vista previa, junto con los mensajes de progreso, hasta que esto está listo.
* Intente cerrar y volver a abrir el archivo XAML.
* Asegúrese de que su `App` clase tiene un constructor sin parámetros.

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML no válido: El proyecto Android debe generarse antes de que se puede crear la vista previa

El controlador de vista previa de XAML requiere que se compila el proyecto antes de procesar una página.
Si aparece el siguiente error en la parte superior del panel de vista previa, volver a compilar la aplicación y vuelva a intentarlo.

![Mensaje de error: proyecto debe compilarse primero](xaml-previewer-images/error-not-built-sml.png "mensaje de Error: volver a generar el proyecto")
