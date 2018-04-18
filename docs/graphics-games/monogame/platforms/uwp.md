---
title: Crear un proyecto de UWP MonoGame
description: MonoGame puede utilizarse para crear juegos y aplicaciones de plataforma Universal de Windows, como destino que varios dispositivos con un código base y un conjunto de contenido.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: ee4ee83c07cf01d1324b5f127d4f77ced0df2afe
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="creating-a-monogame-uwp-project"></a>Crear un proyecto de UWP MonoGame

_MonoGame puede utilizarse para crear juegos y aplicaciones de plataforma Universal de Windows, como destino que varios dispositivos con un código base y un conjunto de contenido._

Este tutorial explica la creación del proyecto de plataforma Universal de Windows (UWP) de MonoGame y carga de contenido. Aplicaciones UWP se pueden ejecutar en todos los dispositivos de Windows 10, incluidos los equipos de escritorio, tabletas, Windows Phone y Xbox One.

Este tutorial crea un proyecto vacío que muestra un *azul aciano* fondo (el color de fondo tradicional de aplicaciones XNA).

## <a name="requirements"></a>Requisitos

Desarrollo de aplicaciones de MonoGame UWP se necesita:

- Sistema operativo Windows 10
- Cualquier versión de Visual Studio 2015
- Herramientas de desarrollo de Windows 10
- Dispositivo de configuración al modo de programador
- [3.5 MonoGame para Visual Studio](http://www.monogame.net/2016/03/17/monogame-3-5/) o posterior

Para obtener más información, vea este [página sobre la configuración para el desarrollo de Windows 10 UWP](https://msdn.microsoft.com/en-us/windows/uwp/get-started/get-set-up).

Juegos de Xbox One se pueden desarrollar en hardware de Xbox One comercial. Se requiere software adicional en el equipo de desarrollo y Xbox One. Para obtener información acerca de cómo configurar una Xbox One para desarrollo de juegos, vea esta página en [configurando una Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Crear una plantilla vacía

Una vez que se han instalado todos los recursos necesarios y se ha habilitado el modo de programador en el equipo de Windows 10, podemos crear un nuevo proyecto de MonoGame con Visual Studio, siga estos pasos:

1. Seleccione **archivo** > **nueva** > **proyecto...**
1. Seleccione el **instalado** > **plantillas** > **Visual C#** > **MonoGame** categoría: 

    ![](uwp-images/image1.png "Categoría de MonoGame")

1. Seleccione el **proyecto Universal de MonoGame Windows 10** opción: 

    ![](uwp-images/image2.png "Seleccione la opción de proyecto Universal de MonoGame Windows 10")

1. Escriba un nombre para el nuevo proyecto y haga clic en **Aceptar**.
Si Visual Studio muestra los errores después de hacer clic en Aceptar, compruebe que se instalan herramientas de Windows 10 y que el dispositivo está en modo de programador.

Una vez que Visual Studio finaliza la creación de la plantilla, poder ejecutarlo para ver el proyecto vacío que se ejecuta:

![](uwp-images/image3.png "Una vez que Visual Studio finaliza la creación de la plantilla, ejecútelo para ver el proyecto vacío de ejecución")

Los números en las esquinas proporcionan información de diagnóstico. Esta información se puede quitar eliminando el código en `App.xaml.cs` en el `DEBUG` bloquear el `OnLaunched` método:


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

## <a name="running-on-xbox-one"></a>Se ejecuta en uno de Xbox

Proyectos de UWP pueden implementar en cualquier dispositivo de Windows 10 desde el mismo proyecto. Después de configurar el equipo de desarrollo de Windows 10 y Xbox One, se pueden implementar aplicaciones UWP cambiando el destino a equipo remoto y escribir dirección IP de la Xbox One:

![](uwp-images/remote.png "Aplicaciones UWP se pueden implementar cambiando el destino a equipo remoto y escribir la dirección IP de los de Xbox")

En Xbox One, el borde blanco representa el área de no seguro para televisores. Para obtener más información, consulte el [sección área segura](#Safe_Area_on_Xbox_One).

![](uwp-images/safearea.png "En Xbox One, el borde blanco representa el área de no es seguro para televisores")

### <a name="safe-area-on-xbox-one"></a>Área segura en Xbox uno

Desarrollo de juegos para consolas requiere teniendo en cuenta la zona segura, que es un área en el centro de la pantalla que debe contener todos los objetos visuales críticos (por ejemplo, la interfaz de usuario o HUD). El área fuera del área seguro no se garantiza que estén visibles en todos los televisores, por lo que pueden ser parcial o totalmente visibles en algunas pantallas objetos visuales que se coloca en esta área.

La plantilla de MonoGame de Xbox One tiene en cuenta la zona segura y lo representa como un borde blanco. Esta área también se refleja en tiempo de ejecución en el juego `Window.ClientBounds` propiedad tal y como se muestra en esta imagen de la ventana Inspección en Visual Studio. Tenga en cuenta que el alto de los límites del cliente es 1016, a pesar de la resolución de pantalla de 1920 x 1080:

![](uwp-images/clientbounds.png "Tenga en cuenta que el alto de los límites del cliente es 1016, a pesar de la resolución de pantalla de 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Hacer referencia a contenido en los proyectos UWP

Puede hacer referencia a contenido en los proyectos de MonoGame directamente desde el archivo o a través del [MonoGame contenido canalización](~/graphics-games/cocossharp/content-pipeline/index.md). Proyectos de juegos pequeños pueden beneficiarse de la simplicidad de la carga de archivo. Proyectos grandes se beneficiarán del uso de la canalización de contenido para optimizar el contenido para reducir el tamaño y tiempos de carga. A diferencia de XNA en la consola Xbox 360, la `System.IO.File` clase está disponible en las aplicaciones UWP una Xbox.

Para obtener más información sobre la carga de contenido mediante la canalización de contenido, consulte la [Guía de la canalización de contenido](~/graphics-games/cocossharp/content-pipeline/index.md). 

### <a name="loading-content-from-file"></a>Cargando el contenido del archivo

A diferencia de iOS y Android, los proyectos UWP pueden hacer referencia a archivos en relación con el archivo ejecutable. Juegos sencillos pueden utilizar este contenido de la carga de técnica sin necesidad de modificar y compilar el proyecto de canalización de contenido.

Para cargar una `Texture2D` de archivo:

1. Agregar un archivo .png a la carpeta de contenido en el proyecto de UWP. Agregar contenido a la carpeta de contenido es una convención de XNA y MonoGame.
1. Haga doble clic en el PNG recién agregado y seleccione Propiedades.
1. Cambiar el **copiar en el directorio de salida** a **copiar si es posterior**.
1. Agregue el código siguiente al método de inicialización de su juego para cargar un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Para obtener más información sobre el uso de un `Texture2D`, consulte el [Introducción a la Guía de MonoGame](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Resumen

Esta guía explica cómo crear un nuevo proyecto UWP y las consideraciones específicas de UWP al cargar archivos. A los desarrolladores interesados en la creación de juegos UWP completas pueden leer más sobre MonoGame en el [Introducción a la Guía de MonoGame](~/graphics-games/monogame/introduction/index.md).