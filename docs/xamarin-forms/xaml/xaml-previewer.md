---
title: Controlador de vista previa XAML para Xamarin.Forms
description: En este artículo se explica cómo usar a la vista previa de XAML para ver sus diseños de Xamarin.Forms representan a medida que escribe. El controlador de vista previa de XAML está disponible en Visual Studio 2017, Visual Studio para Mac y Visual Studio 2019 (versión preliminar).
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: 4258eca8e18229420ceb043a010c896137187653
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240388"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Controlador de vista previa XAML para Xamarin.Forms

_Vea los diseños de Xamarin.Forms representan a medida que escribe._

## <a name="requirements"></a>Requisitos

Los proyectos requieren el paquete Xamarin.Forms NuGet más reciente para el controlador de vista previa de XAML para que funcione. Vista previa de aplicaciones para Android requiere [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Puede encontrar más información en el [notas de la versión](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Introducción

La vista previa de XAML de Xamarin.Forms muestra una vista previa de específicos de la plataforma de su archivo XAML en tiempo real, mientras lo edita.

::: zone pivot="windows"

### <a name="visual-studio"></a>Programa para la mejora

El controlador de vista previa de XAML está disponible al expandir el panel de vista de división. Dividir el comportamiento de la vista de forma predeterminada se puede controlar desde el **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. En este cuadro de diálogo, puede seleccionar la vista de documento predeterminada y la orientación dividida.

[![Opciones de controlador de vista previa de Xamarin.Forms en Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "opciones de controlador de vista previa de Xamarin.Forms en Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Al abrir una página XAML, se dividirá en función de la configuración seleccionada en el editor de la **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. Sin embargo, se puede cambiar la división para cada archivo en la ventana del editor.

#### <a name="xaml-preview-controls"></a>Controles de vista previa XAML

La parte superior de la ventana del editor tiene botones para seleccionar qué panel está en uso, con el botón superior cambiar al panel de diseño y el botón Cambiar al panel de origen. El botón central intercambia el orden de panel.

[![Controla el controlador de vista previa de Xamarin.Forms para cambiar entre diseño, el origen y la vista en dos paneles en Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "controla el controlador de vista previa de Xamarin.Forms para cambiar entre diseño, el origen y la vista en dos paneles en Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

La parte inferior de la ventana del editor tiene botones vertical y horizontalmente, dividir los paneles y para expandir o contraer el panel secundario actual.

[![Controles de orientación del panel de vista previa de Xamarin.Forms en Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controles de orientación del panel de vista previa de Xamarin.Forms en Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

El **Preview** botón se muestra en el editor cuando se abre una página XAML. El panel de vista previa se puede mostrar u ocultar presionando el **Preview** situado en la esquina superior derecha de cualquier ventana de documento XAML:

[![Controlador de vista previa de Xamarin.Forms en Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "controlador de vista previa de Xamarin.Forms en Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end
::: zone pivot="win-dev16"

### <a name="visual-studio-2019-preview"></a>Visual Studio 2019 (versión preliminar)

El controlador de vista previa de XAML está disponible al expandir el panel de vista de división. Dividir el comportamiento de la vista de forma predeterminada se puede controlar desde el **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. En este cuadro de diálogo, puede seleccionar la vista de documento predeterminada y la orientación dividida.

[![Opciones de controlador de vista previa de Xamarin.Forms en Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "opciones de controlador de vista previa de Xamarin.Forms en Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Al abrir una página XAML, se dividirá en función de la configuración seleccionada en el editor de la **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. Sin embargo, se puede cambiar la división para cada archivo en la ventana del editor.

#### <a name="xaml-preview-controls"></a>Controles de vista previa XAML

La parte superior de la ventana del editor tiene botones para seleccionar qué panel está en uso, con el botón superior cambiar al panel de diseño y el botón Cambiar al panel de origen. El botón central intercambia el orden de panel.

[![Controla el controlador de vista previa de Xamarin.Forms para cambiar entre diseño, el origen y la vista en dos paneles en Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "controla el controlador de vista previa de Xamarin.Forms para cambiar entre diseño, el origen y la vista en dos paneles en Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

La parte inferior de la ventana del editor tiene botones vertical y horizontalmente, dividir los paneles y para expandir o contraer el panel secundario actual.

[![Controles de orientación del panel de vista previa de Xamarin.Forms en Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controles de orientación del panel de vista previa de Xamarin.Forms en Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>Opciones de vista previa de XAML

Las opciones en la parte superior del panel de vista previa son:

* **Teléfono** : vista previa en una pantalla de tamaño de teléfono
* **Tablet** -versión preliminar en una pantalla de Tablet PC tamaño
* **Android** : mostrar la versión de Android de la pantalla
* **iOS** : mostrar la versión de iOS de la pantalla (*Nota: Si usa Visual Studio en Windows, debe ser [emparejado con un equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar este modo*)
* **Vertical (icono)** – utiliza orientación vertical para la versión preliminar
* **Horizontal (icono)** – utiliza orientación para la versión preliminar de horizontal

::: zone pivot="win-dev16"

## <a name="device-drop-down-menu"></a>Menú desplegable del dispositivo

En Visual Studio 2019 (versión preliminar), puede seleccionar un dispositivo para obtener una vista previa de una lista desplegable de dispositivos Android o iOS. Esta lista desplegable sustituye a las opciones de "Teléfono" y "Tablet PC". Al realizar vistas previas de iOS, la lista desplegable mostrará dispositivos iPhone y iPad. Al realizar vistas previas de Android, la lista desplegable mostrará varios teléfonos y tabletas Android. Ambas listas incluyen los tamaños de pantalla y resoluciones de pantalla.

::: zone-end

## <a name="detect-design-mode"></a>Detectar el modo de diseño

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propiedad puede examinarse para determinar si la aplicación se está ejecutando en el controlador de vista previa. Esto le permite especificar código que solo se ejecutará cuando la aplicación se ejecuta en el controlador de vista previa:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

::: zone pivot="win-dev16"

## <a name="enable-design-time-rendering-for-custom-controls"></a>Habilitar la representación en tiempo de diseño para controles personalizados

Controles personalizados tienen participar en tiempo de representación para aparecer en la vista previa, independientemente de si el control se encuentra en el proyecto o se importa desde una biblioteca de diseño. Esto puede realizarse agregando el [ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute) a la clase del control:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Se puede ver un ejemplo de esto en [clase base de ImageCirclePlugin de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs).

::: zone-end

## <a name="add-design-time-data"></a>Agregar datos en tiempo de diseño

Algunos diseños pueden ser difíciles visualizar sin los datos enlazados a los controles de interfaz de usuario. Para que la versión preliminar más útil, asignar algunos datos estáticos a los controles por codificar un contexto de enlace (ya sea en el código subyacente o mediante XAML).

Hacer referencia a la de James Montemagno [entrada de blog sobre cómo agregar datos en tiempo de diseño](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver cómo enlazar a una clase ViewModel estático en XAML.

## <a name="troubleshooting"></a>Solución de problemas

Compruebe los problemas siguientes y el [foros de Xamarin](https://forums.xamarin.com/categories/xamarin-forms), si tiene problemas.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Controlador de vista previa de XAML no está visible o muestra un error

Compruebe lo siguiente:

* El agente de diseñador debe ser la configuración de la primera vez que obtenga una vista previa de un archivo XAML, aparecerá un indicador de progreso en la vista previa, junto con los mensajes de progreso, hasta que esto está listo.
* Intente cerrar y volver a abrir el archivo XAML.
* Asegúrese de que su `App` clase tiene un constructor sin parámetros.

::: zone pivot="win-dev16"

### <a name="custom-controls-arent-rendering"></a>Controles personalizados no están procesando

Asegúrese de que el proyecto se compiló. Si el controlador de vista previa no puede representar el control sin errores o creador del control no participar en representación en tiempo de diseño, el controlador de vista previa muestra la clase del control base.

::: zone-end
