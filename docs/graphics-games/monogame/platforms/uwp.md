---
title: Crear un proyecto MonoGame UWP
description: MonoGame puede utilizarse para crear juegos y aplicaciones para plataforma Universal de Windows, como destino que varios dispositivos con un código base y un conjunto de contenido.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 12a07be4adb32e8d9461a00e5fdea52d9b930848
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117855"
---
# <a name="creating-a-monogame-uwp-project"></a>Crear un proyecto MonoGame UWP

_MonoGame puede utilizarse para crear juegos y aplicaciones para plataforma Universal de Windows, como destino que varios dispositivos con un código base y un conjunto de contenido._

Este tutorial describe la creación de un proyecto MonoGame Universal Windows Platform (UWP) y carga de contenido. Pueden ejecutar las aplicaciones para UWP en todos los dispositivos de Windows 10, incluidos equipos de escritorio, tabletas, teléfonos Windows y Xbox One.

En este tutorial crea un proyecto vacío que muestra un *azul aciano* en segundo plano (el color de fondo tradicional de aplicaciones XNA).

## <a name="requirements"></a>Requisitos

Desarrollo de aplicaciones MonoGame UWP requiere:

- Sistema operativo Windows 10
- Cualquier versión de Visual Studio 2015
- Herramientas de desarrollo de Windows 10
- Dispositivo de configuración al modo de programador
- [MonoGame 3.5 para Visual Studio](http://www.monogame.net/2016/03/17/monogame-3-5/) o posterior

Para obtener más información, consulte este [página sobre la configuración para el desarrollo de Windows 10 UWP](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Juegos de Xbox One se pueden desarrollar en Xbox One hardware de venta directa. Se necesita software adicional en el equipo de desarrollo y Xbox One. Para obtener información sobre cómo configurar una Xbox One para desarrollo de juegos, vea esta página en [configurando una Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Creación de una plantilla vacía

Una vez que se han instalado todos los recursos necesarios y se ha habilitado el modo de programador en la máquina de Windows 10, podemos crear un nuevo proyecto MonoGame mediante Visual Studio siguiendo estos pasos:

1. Seleccione **archivo** > **nueva** > **proyecto...**
1. Seleccione el **instalado** > **plantillas** > **Visual C#**   >  **MonoGame** categoría: 

    ![](uwp-images/image1.png "Categoría de MonoGame")

1. Seleccione el **MonoGame Windows 10 Universal proyecto** opción: 

    ![](uwp-images/image2.png "Seleccione la opción de proyecto Universal de MonoGame Windows 10")

1. Escriba un nombre para el nuevo proyecto y haga clic en **Aceptar**.
Si Visual Studio muestra los errores después de hacer clic en Aceptar, compruebe que están instaladas las herramientas de Windows 10 y que el dispositivo está en modo de programador.

Una vez que Visual Studio termine de crear la plantilla, podemos ejecutar para ver el proyecto vacío que se ejecuta:

![](uwp-images/image3.png "Una vez que Visual Studio termine de crear la plantilla, ejecútela para ver el proyecto vacío que está ejecutando")

Los números en las esquinas proporcionan información de diagnóstico. Esta información se puede quitar mediante la eliminación del código en `App.xaml.cs` en el `DEBUG` bloque en el `OnLaunched` método:


```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>Que se ejecutan en Xbox One

Los proyectos de UWP pueden implementar en cualquier dispositivo Windows 10 desde el mismo proyecto. Después de configurar la máquina de desarrollo de Windows 10 y Xbox One, se pueden implementar aplicaciones para UWP cambiando el destino a equipo remoto y escribir dirección IP de la Xbox One:

![](uwp-images/remote.png "Se pueden implementar aplicaciones para UWP cambiando el destino a equipo remoto y escribir la dirección IP de los de Xbox")

En Xbox One, el borde blanco representa el área de no seguro para televisores. Para obtener más información, consulte el [sección área segura](#Safe_Area_on_Xbox_One).

![](uwp-images/safearea.png "En Xbox One, el borde blanco representa la zona no segura para televisores")

### <a name="safe-area-on-xbox-one"></a>Área segura en Xbox One

Desarrollo de juegos para consolas requiere considerar el área segura, que es un área en el centro de la pantalla que debe contener todos los objetos visuales críticos (por ejemplo, la interfaz de usuario o HUD). El área situada fuera de la zona segura no se garantiza que estén visibles en todos los televisores, por lo que los objetos visuales que se colocan en esta área puede total o parcialmente visibles en algunas pantallas.

La plantilla de MonoGame para Xbox One tiene en cuenta la zona segura y lo representa como un borde blanco. Esta área también se refleja en tiempo de ejecución en el juego `Window.ClientBounds` propiedad tal como se muestra en esta imagen de la ventana Inspección en Visual Studio. Tenga en cuenta que el alto de los límites del cliente es 1016, a pesar de la resolución de pantalla de 1920 x 1080:

![](uwp-images/clientbounds.png "Tenga en cuenta que el alto de los límites del cliente es 1016, a pesar de la resolución de pantalla de 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Hacer referencia a contenido en los proyectos de UWP

Puede hacer referencia a contenido en los proyectos de MonoGame directamente desde el archivo o a través del [canalización de contenido de MonoGame](~/graphics-games/cocossharp/content-pipeline/index.md). Proyectos pequeños de juegos pueden beneficiarse de la simplicidad de la carga del archivo. Proyectos más grandes se beneficiarán del uso de la canalización de contenido para optimizar el contenido para reducir el tamaño y tiempos de carga. A diferencia de XNA en la consola Xbox 360, el `System.IO.File` está disponible en las aplicaciones para UWP de Xbox una clase.

Para obtener más información sobre la carga de contenido mediante la canalización de contenido, consulte el [Guía de la canalización de contenido](~/graphics-games/cocossharp/content-pipeline/index.md). 

### <a name="loading-content-from-file"></a>Cargando el contenido del archivo

A diferencia de iOS y Android, los proyectos UWP pueden hacer referencia a archivos en relación con el archivo ejecutable. Juegos sencillos en pueden usar este contenido de carga técnica sin necesidad de modificar y compilar el proyecto de canalización de contenido.

Para cargar un `Texture2D` del archivo:

1. Agregue un archivo .png para la carpeta de contenido en el proyecto UWP. Agregar contenido a la carpeta de contenido es una convención en XNA y MonoGame.
1. Haga doble clic en el PNG recién agregada y seleccione Propiedades.
1. Cambiar el **Copy to Output Directory** a **copiar si es posterior**.
1. Agregue el código siguiente al método Initialize de tu juego para cargar un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Para obtener más información sobre el uso de un `Texture2D`, consulte el [Introducción a MonoGame guía](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Resumen

Esta guía explica cómo crear un nuevo proyecto UWP y las consideraciones específicas de UWP al cargar archivos. Los desarrolladores interesados en la creación de juegos UWP completas pueden leer más sobre MonoGame en el [Introducción a MonoGame guía](~/graphics-games/monogame/introduction/index.md).
