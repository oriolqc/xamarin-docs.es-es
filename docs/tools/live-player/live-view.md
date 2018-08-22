---
redirect_url: /xamarin/tools/live-player/
title: XAML Live vista previa
description: Este documento describe cómo usar Xamarin Live Player para páginas de XAML de vista previa de live, realizar cambios en el XAML y ver los cambios aparezcan al instante en el dispositivo.
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
author: topgenorth
ms.author: toopge
ms.date: 08/08/2018
ms.openlocfilehash: 200d19aa0a13d0557e52cb90021190978838ed39
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251044"
---
# <a name="xaml-live-previewing"></a>XAML Live vista previa

Una de las ventajas de Xamarin Live Player es la capacidad de páginas de XAML de vista previa de live, realizar cambios en el código en Visual Studio y ver los cambios al instante en el dispositivo. La vista previa dinámica se puede realizar en el dispositivo Android o en un simulador o emulador. Esta guía muestra cómo usar la característica de vista previa activa para ver las pantallas XAML individuales.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Un equipo que ejecute Windows 7 o posterior.
2. Visual Studio 2017 versión 15.4 o posterior con el **desarrollo móvil con .NET** carga de trabajo instalada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Un equipo Mac con OS X 10.11, macOS 10.12 o superior.
2. Visual Studio para Mac 7.2 o posterior. Se recomienda la versión más reciente.

-----

<a name="deploydevice" />

## <a name="deploying-to-device"></a>Implementación en un dispositivo

Para poder usar Xamarin Live Player con el dispositivo Android, deberá descargar la aplicación de Xamarin Live Player y emparéjelo a Visual Studio como se describe en el [instalar](~/tools/live-player/install.md) guía. Una vez que se ha emparejado correctamente el dispositivo para Visual Studio, puede empezar a la vista previa dinámica de la página XAML. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra la página XAML que desea que la vista previa activa en el editor de Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Establezca la configuración de dispositivo en **depurar** y seleccione el dispositivo Live Player en la lista:

    ![](live-view-images/vs-image2.png)

3. Para ejecutar esta página XAML como una vista en vivo en el dispositivo, seleccione **Herramientas > Xamarin Live Player > Live ejecutar vista actual** desde la barra de menús:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la página XAML que desea que la vista previa activa en Visual Studio para el editor de Mac:

    ![](live-view-images/image1.png)

2. Establezca la configuración de dispositivo en **depurar** y seleccione el dispositivo Live Player en la lista:

    ![](live-view-images/image2.png)

3. Para ejecutar esta página XAML como una vista en vivo en el dispositivo, seleccione **ejecutar > Live ejecutar vista actual** desde la barra de menús:

    ![](live-view-images/image3.png)

-----

## <a name="deploying-to-android-emulator"></a>Implementación en el emulador de Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra la página XAML que desea que la vista previa activa en el editor de Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Establezca la configuración de dispositivo en **depurar** para Android y seleccione el dispositivo Live Player en la lista:

    ![](live-view-images/vs-image4.png)

3. Para ejecutar esta página XAML como una vista en vivo en el emulador de Android, seleccione **Herramientas > Xamarin Live Player > Live ejecutar vista actual** desde la barra de menús:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra la página XAML que desea que la vista previa activa en Visual Studio para el editor de Mac:

    ![](live-view-images/image7.png)

2. Establezca la configuración de dispositivo en **depurar** para Android y seleccione el dispositivo Live Player en la lista:

    ![](live-view-images/image6.png)

3. Para ejecutar esta página XAML como una vista en vivo en el dispositivo, seleccione Ejecutar > Live ejecutar vista actual de la barra de menús:

    ![](live-view-images/image3.png)

-----

## <a name="related-links"></a>Vínculos relacionados

- [Información general de Xamarin Live Player](https://xamarin.com/live)
- [Entradas del blog](https://blog.xamarin.com/live-player/)
- [Ejemplos de Xamarin Live Player](~/tools/live-player/samples.md)
