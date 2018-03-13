---
title: "XAML dinámico vista previa"
description: "Probar los cambios de código de aplicación en tiempo real en el dispositivo iOS o Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 12/21/2017
ms.openlocfilehash: f5c1649d625f2094fb710066f79a4ae89a696266
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="xaml-live-previewing"></a>XAML dinámico vista previa

Una de las ventajas de Xamarin Player en vivo es la capacidad de live páginas XAML de vista previa, realizar cambios en el código de Visual Studio y ver los cambios al instante aparecen en el dispositivo. La vista previa dinámica puede estar en el dispositivo iOS o Android, o en un simulador o emulador. Esta guía muestra cómo utilizar la característica de vista previa dinámica para ver las pantallas XAML individuales.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Un equipo que ejecuta Windows 7 o superior.
2. Visual Studio 2017 versión 15,4 o posterior con el **desarrollo móvil con .NET** instalada de la carga de trabajo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Un equipo Mac con OS X 10.11 macOS 10.12 o mayor.
2. Visual Studio para Mac 7.2 o posterior. Se recomienda la versión más reciente.

-----



<a name="deploydevice" />

## <a name="deploying-to-device"></a>Implementación en un dispositivo

Para poder usar el Reproductor de Xamarin en directo con el dispositivo iOS o Android, debe descargar la aplicación de Xamarin Player en vivo y emparejar Visual Studio como se describe en el [instalar](~/tools/live-player/install.md) guía. Una vez que se ha emparejado correctamente su dispositivo para Visual Studio, puede empezar la previsualización de la página XAML. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra la página XAML que desea que la vista previa activa en el editor de Visual Studio de 2017:

    ![](live-view-images/vs-image1.png)

2. Establezca la configuración de dispositivo en **depurar | iPhone** para iOS o **depurar** para Android y seleccione el dispositivo Reproductor en vivo de la lista:

    ![](live-view-images/vs-image2.png)

3. Para ejecutar esta página XAML como una vista activa en el dispositivo, seleccione **Herramientas > Xamarin Player Live > Live vista actual ejecute** desde la barra de menús:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra la página XAML que se va a vista previa activa en Visual Studio en el editor de Mac:

    ![](live-view-images/image1.png)

2. Establezca la configuración de dispositivo en **depurar | iPhone** para iOS o **depurar** para Android y seleccione el dispositivo Reproductor en vivo de la lista:

    ![](live-view-images/image2.png)

3. Para ejecutar esta página XAML como una vista activa en el dispositivo, seleccione **ejecutar > Live vista actual ejecute** desde la barra de menús:

    ![](live-view-images/image3.png)

-----








## <a name="deploying-to-android-emulator"></a>Implementación en el emulador de Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra la página XAML que desea que la vista previa activa en el editor de Visual Studio de 2017:

    ![](live-view-images/vs-image1.png)

2. Establezca la configuración de dispositivo en **depurar** para Android y seleccione el dispositivo Reproductor en vivo de la lista:

    ![](live-view-images/vs-image4.png)

3. Para ejecutar esta página XAML como una vista en vivo en el emulador de Android, seleccione **Herramientas > Xamarin Player Live > Live vista actual ejecute** desde la barra de menús:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra la página XAML que se va a vista previa activa en Visual Studio en el editor de Mac:

    ![](live-view-images/image7.png)

2. Establezca la configuración de dispositivo en **depurar** para Android y seleccione el dispositivo Reproductor en vivo de la lista:

    ![](live-view-images/image6.png)

3. Para ejecutar esta página XAML como una vista activa en el dispositivo, seleccione Ejecutar > Live ejecutar vista actual de la barra de menús:

    ![](live-view-images/image3.png)

-----





## <a name="deploying-to-ios-simulator"></a>Implementación en el simulador de iOS

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Actualmente no hay ninguna compatibilidad para usar en vivo XAML obtener una vista previa en el simulador de iOS remoto en Windows. En su lugar, debe [implementar en un dispositivo](#deploydevice).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra la página XAML que se va a vista previa activa en Visual Studio en el editor de Mac:

    ![](live-view-images/image1.png)

2. Establezca la configuración de dispositivo en **depurar | iPhoneSimulator** para iOS y seleccione un simulador de iOS en la lista:

    ![](live-view-images/image2.png)

3. Seleccione **ejecutar > Live vista actual ejecutar** en la barra de menús para iniciar el simulador y mostrar la página XAML:

    ![](live-view-images/image4.png)

4. Una vez que se inicia el simulador, puede comenzar a editar el código XAML y obtener una vista previa aparece en vivo:

    ![](live-view-images/image5.png)  

-----








## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin Player en vivo](https://xamarin.com/live)
- [entrada de blog](https://blog.xamarin.com/live-player/)
- [Ejemplos de Xamarin Player en vivo](~/tools/livehttps://developer.xamarin.com/samples.md)
