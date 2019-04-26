---
title: Controlador de vista previa XAML para Xamarin.Forms
description: En este artículo se explica cómo usar a la vista previa de XAML para ver sus diseños de Xamarin.Forms representan a medida que escribe. El controlador de vista previa de XAML está disponible en Visual Studio de 2019 y 2019 de Visual Studio para Mac.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: db243a9c8dcb25f51bc7926a7aa239531e9c24f6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61210824"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Controlador de vista previa XAML para Xamarin.Forms

_Consulte los diseños de Xamarin.Forms representan a medida que escribe_

## <a name="overview"></a>Información general

El controlador de vista previa de XAML muestra el aspecto de la página XAML de Xamarin.Forms en iOS y Android. Al realizar cambios en el XAML, verá una vista previa inmediatamente junto con el código. El controlador de vista previa de XAML está disponible en Visual Studio y Visual Studio para Mac.

## <a name="getting-started"></a>Introducción

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

Puede abrir a la vista previa de XAML, haga clic en las flechas en el panel de vista de división. Si desea cambiar el valor predeterminado divide el comportamiento de la vista, use el **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. En este cuadro de diálogo, puede seleccionar la vista de documento predeterminada y la orientación dividida.

[![Opciones de controlador de vista previa de Xamarin.Forms en Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "opciones de controlador de vista previa de Xamarin.Forms en Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Al abrir un archivo XAML, se abrirá el editor en tamaño completo o en siguiente para el controlador de vista previa, en función de la configuración seleccionada en el **Herramientas > Opciones > Xamarin > controlador de vista previa de formularios** cuadro de diálogo. Sin embargo, se puede cambiar la división para cada archivo en la ventana del editor.

#### <a name="xaml-preview-controls"></a>Controles de vista previa XAML

Elija si desea ver el código, el controlador de vista previa de XAML, o ambos mediante la selección de estos botones en la división ver panel. El botón central intercambia qué lado de la vista previa y el código están en:

[![Controla el controlador de vista previa de Xamarin.Forms para cambiar entre diseño, el origen y la vista en dos paneles en Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "controla el controlador de vista previa de Xamarin.Forms para cambiar entre diseño, el origen y la vista en dos paneles en Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

Puede cambiar si la pantalla se divide verticalmente u horizontalmente, o contraer un panel completamente:

[![Controles de orientación del panel de vista previa de Xamarin.Forms en Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controles de orientación del panel de vista previa de Xamarin.Forms en Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

El **Preview** botón se muestra en el editor cuando se abre una página XAML. Mostrar u ocultar la vista previa presionando el **Preview** situado en la esquina superior derecha de cualquier ventana de documento XAML:

[![Controlador de vista previa de Xamarin.Forms en Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "controlador de vista previa de Xamarin.Forms en Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>Opciones de vista previa de XAML

Las opciones en la parte superior del panel de vista previa son:

* **Android** : mostrar la versión de Android de la pantalla
* **iOS** : mostrar la versión de iOS de la pantalla (*Nota: Si usa Visual Studio en Windows, debe ser [emparejado con un equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar este modo*)
* **Dispositivo** -lista desplegable de dispositivos Android o iOS, incluido el tamaño de pantalla y resolución
* **Vertical (icono)** – utiliza orientación vertical para la versión preliminar
* **Horizontal (icono)** – utiliza orientación para la versión preliminar de horizontal

## <a name="detect-design-mode"></a>Detectar el modo de diseño

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propiedad indica si la aplicación se está ejecutando en el controlador de vista previa. Con esta opción, puede especificar el código que solo se ejecutará cuando la aplicación es o no se está ejecutando en el controlador de vista previa:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

Esta propiedad es útil si inicializar una biblioteca en el constructor de la página que no se puede ejecutar en tiempo de diseño.

## <a name="troubleshooting"></a>Solución de problemas

Compruebe los siguientes problemas y la [foros de Xamarin](https://forums.xamarin.com/categories/xamarin-forms), si el controlador de vista previa no funciona.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Controlador de vista previa de XAML no está visible o muestra un error

* Puede tardar algún tiempo para el controlador de vista previa en iniciarse, verá "Inicializando Render" hasta que esté listo.
* Intente cerrar y volver a abrir el archivo XAML.
* Asegúrese de que su `App` clase tiene un constructor sin parámetros.
* Comprobar la versión de Xamarin.Forms: debe ser al menos 3.6 de Xamarin.Forms. Puede actualizar a la última versión de Xamarin.Forms a través de NuGet.
* Comprobación de la instalación de JDK - vista previa de Android requiere al menos [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Pruebe cualquier ajuste inicializa las clases en la página C# código subyacente en `if (!DesignMode.IsDesignModeEnabled)`.

### <a name="custom-controls-arent-rendering"></a>Controles personalizados no están procesando

Intente compilar el proyecto. El controlador de vista previa muestra la clase base del control si se produce un error presentar el control, o si el creador del control elegido horizontal de representación en tiempo de diseño. Para obtener más información, consulte [representar controles personalizados en la vista previa de XAML](render-custom-controls.md).
