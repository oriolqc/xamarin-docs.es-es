---
title: "Instalación de Xamarin en Visual Studio para Windows"
ms.topic: article
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 09/29/2017
ms.openlocfilehash: b68e03251b83192bdc5836af6ea54446ddaad24a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="installing-xamarin-in-visual-studio-on-windows"></a>Instalación de Xamarin en Visual Studio para Windows

Gracias a que ahora Xamarin se incluye con todas las ediciones de Visual Studio sin ningún costo adicional y no requiere ninguna licencia independiente, puede usar el instalador de Visual Studio para descargar e instalar las herramientas de Xamarin.

-   [Requisitos](#requirements)
-   [Instalación](#installation)
-   [Agregar Xamarin a Visual Studio 2017](#vs2017)
-   [Agregar Xamarin a Visual Studio 2015](#vs2015)
-   [Comprobación de la instalación](#verifying)
-   [Pasos siguientes](#nextsteps)


<a name="requirements" />

# <a name="requirements"></a>Requisitos

Para instalar las herramientas de Visual Studio para Xamarin, se necesita lo siguiente:

1. Windows 7 o una versión posterior.

2. Visual Studio 2015 o 2017 (Community, Professional o Enterprise).

3. Xamarin para Visual Studio.

Tenga en cuenta que Xamarin no se puede usar con las ediciones Express de Visual Studio debido a la falta de compatibilidad con los complementos.

Para obtener más información sobre los requisitos previos para instalar y usar Xamarin, consulte [Requisitos del sistema](~/cross-platform/get-started/requirements.md).


<a name="installation" />

# <a name="installation"></a>Instalación

Xamarin se puede instalar como parte de una nueva instalación de Visual Studio.
Para ello, siga estos pasos:

1. Descargue Visual Studio Community, Visual Studio Professional o Visual Studio Enterprise desde la página de [Visual Studio](https://www.visualstudio.com/vs/) (en la parte inferior se encuentran los vínculos de descarga).

2. Haga doble clic en el paquete descargado para iniciar la instalación.

3. Seleccione la carga de trabajo **Desarrollo para dispositivos móviles con .NET** en la pantalla de instalación: 

    [![Selección de Desarrollo para dispositivos móviles con .NET en la pantalla Cargas de trabajo](windows-images/01-mobile-dev-workload-sml.png)](windows-images/01-mobile-dev-workload.png)

4. Con la opción **Desarrollo para dispositivos móviles con .NET** seleccionada, eche un vistazo en el panel **Resumen** de la derecha. Aquí puede anular la selección de opciones de desarrollo para dispositivos móviles que no quiera instalar. De forma predeterminada se instalan todas las opciones que aparecen en la siguiente captura de pantalla (**Xamarin Workbooks**, **Xamarin Profiler**, **Xamarin Remoted Simulator**, **Android NDK**, **Android SDK**, **Java SE Development Kit**, **Emulador de Android de Google**, **Compatibilidad con el lenguaje F#** e **Intel Hardware Accelerated Execution Manager (HAXM)**):

    ![Panel Resumen en el que aparecen las opciones de Xamarin que se van a instalar](windows-images/02-summary.png)

5. Cuando esté listo para comenzar la instalación de Visual Studio, haga clic en el botón **Instalar** situado en la esquina inferior derecha:

    ![Ubicación del botón de instalación](windows-images/03-click-install.png)

   En función de la edición de Visual Studio que vaya a instalar, el proceso de instalación puede tardar bastante tiempo. Puede usar las barras de progreso para supervisar la instalación:

    ![Captura de pantalla de ejemplo de las barras de progreso durante la instalación](windows-images/04-progress-bars.png)

6. Cuando la instalación de Visual Studio haya finalizado, haga clic en el botón **Iniciar** para iniciar Visual Studio:

    ![Ubicación del botón Inicio](windows-images/05-launch.png)


<a name="vs2017" />

## <a name="adding-xamarin-to-visual-studio-2017"></a>Agregar Xamarin a Visual Studio 2017

Si Visual Studio 2017 ya está instalado, puede agregar Xamarin volviendo a ejecutar el instalador de Visual Studio para modificar las cargas de trabajo (consulte [Modificar Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) para obtener más información). Después, siga los pasos indicados anteriormente para instalar Xamarin.

Para obtener más información sobre la descarga e instalación de Visual Studio 2017, consulte [Instalación de Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).


<a name="vs2015" />

## <a name="adding-xamarin-to-visual-studio-2015"></a>Agregar Xamarin a Visual Studio 2015

Para agregar Xamarin.Android a una instalación existente de Visual Studio 2015, siga estos pasos:

1. Haga clic con el botón derecho en el botón **Inicio** de Windows y seleccione **Programas y características**.

2. Haga clic con el botón derecho en **Microsoft Visual Studio** y haga clic en **Cambiar**.

3. Cuando aparezca el cuadro de diálogo del instalador de Visual Studio, haga clic en el botón **Modificar**.

4. En la pestaña **Características**, desplácese hacia abajo hasta **Desarrollo para móviles multiplataforma**. Haga clic en la casilla de verificación situada junto a **C#/.NET (Xamarin)**:

    ![Agregar C#/.NET Xamarin a Visual Studio 2015](windows-images/06-add-xamarin.png)

5. Haga clic en el botón **ACTUALIZAR** para agregar Xamarin a Visual Studio.


<a name="verifying" />

## <a name="verifying-installation"></a>Comprobación de la instalación

En Visual Studio 2017 puede comprobar que Xamarin está instalado haciendo clic en el menú **Ayuda**. Si está instalado, debería ver el elemento de menú **Xamarin**, como se muestra en esta captura de pantalla:

![Elemento de menú Xamarin mostrado en el menú Ayuda](windows-images/12-xamarin-menu-item.png)

Si usa una versión anterior de Visual Studio, haga clic en **Ayuda > Acerca de Microsoft Visual Studio** y desplácese por la lista de productos instalados para ver si Xamarin está instalado:

![Pantalla de productos instalados de Visual Studio](windows-images/13-xamarin-is-installed.png)

Para obtener más información sobre cómo encontrar la información de la versión, consulte [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md) (¿Dónde puedo encontrar los registros y la información de la versión?).

<a name="nextsteps" />

# <a name="next-steps"></a>Pasos siguientes

La instalación de Visual Studio Tools para Xamarin permite empezar a escribir código para sus aplicaciones, pero requiere un paso más para configurarlo y poder compilar e implementar las aplicaciones en el simulador, el emulador y el dispositivo. Consulte las siguientes guías para completar la instalación y empezar a compilar aplicaciones multiplataforma.

## <a name="ios"></a>iOS

Para obtener información detallada, consulte la guía de [instalación de Xamarin.iOS en Windows](~/ios/get-started/installation/windows/index.md). 

1. [Instalación de las herramientas de Xamarin.iOS en Mac](~/ios/get-started/installation/windows/index.md#installation)
2. [Configuración del Mac](~/ios/get-started/installation/windows/index.md#configuration)
3. [Configuración del desarrollador de iOS](~/ios/get-started/installation/windows/index.md#developersetup) (para ejecutar la aplicación en el dispositivo).
4. [Conexión de Visual Studio con el host de compilación del Mac](~/ios/get-started/installation/windows/index.md#connectingtomac)
5. [Simulador remoto de iOS](~/tools/ios-simulator.md)
6. [Introducción a Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

## <a name="android"></a>Android

Para obtener información detallada, consulte la guía de [instalación de Xamarin.Android en Windows](~/android/get-started/installation/windows.md).

1. [Configuración de Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Uso del administrador de Android SDK de Xamarin](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Emulador de Android SDK](~/android/get-started/installation/android-emulator/index.md)
4. [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md)
