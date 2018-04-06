---
title: Desinstalación de Xamarin
description: Desinstalación de los productos de Xamarin de un equipo
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: 98c10aae9e281600201570ea1fc8bb023286164d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="uninstalling-xamarin"></a>Desinstalación de Xamarin

> [!IMPORTANT]
> En este artículo se explica cómo desinstalar Xamarin Studio u otros productos de Xamarin de un equipo Mac o Windows. Para obtener información sobre cómo desinstalar Visual Studio para Mac, consulte el la guía para [Desinstalar](https://docs.microsoft.com/visualstudio/mac/uninstall) en docs.microsoft.com

Hay una serie de productos de Xamarin que permiten el desarrollo de aplicaciones entre plataformas, incluidas las aplicaciones independientes como Xamarin Studio y las extensiones a otras aplicaciones como la compatibilidad con Xamarin en Visual Studio.

En esta guía se explica cómo quitar la funcionalidad de Xamarin en Mac OS o desde Visual Studio en Windows:

1.  [Desinstalación de Xamarin Studio](#uninstallxamarinstudio)
  1.  [Desinstalación de Mono](#uninstallmono)
  1.  [Desinstalación de Xamarin.Android](#uninstallandroid)
  1.  [Desinstalación de Xamarin.iOS](#uninstallios)
  1.  [Desinstalación de Xamarin.Mac](#uninstallmac)
  2.  [Desinstalación de Inspector y Workbooks](#uninstallworkbooks)
1.  [Desinstalación de Xamarin de Windows](#uninstallwindows)
  1.  [Desinstalación de Xamarin desde Visual Studio 2015 y anterior](#uninstallvs2015)
  1.  [Desinstalación de Xamarin desde Visual Studio 2017](#uninstallvs2017)
1.  [Desinstalación de Visual Studio para Mac](#uninstallvsmac)

Si es necesario volver a instalar Xamarin con el Instalador universal, siempre se recomienda que se reinicie primero el equipo.

## <a name="uninstalling-xamarin-on-mac"></a>Desinstalación de Xamarin en un equipo Mac

Esta guía puede usarse para desinstalar cada producto de manera individual dirigiéndose a la sección correspondiente. El conjunto de aplicaciones completo de Xamarin puede desinstalarse siguiendo esta guía de principio a fin.

Para obtener ayuda sobre el uso del [script de desinstalación](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh), vaya a [Usar el script de desinstalación](#uninstallscript) en la parte inferior de esta guía.

<a name="uninstallxamarinstudio" />

### <a name="uninstall-xamarin-studio"></a>Desinstalación de Xamarin Studio

El primer paso a la hora de desinstalar Xamarin Studio de un equipo Mac es buscar **Xamarin Studio.app** en el directorio **/Applications** y arrastrarlo a la **Papelera**. De manera alternativa, haga clic con el botón derecho y seleccione **Trasladar a la Papelera** como se muestra a continuación:

 [![](uninstalling-xamarin-images/image1.png "También puede hacer clic con el botón derecho y seleccionar Trasladar a la Papelera como se muestra aquí")](uninstalling-xamarin-images/image1.png#lightbox)

Al eliminar este lote de aplicaciones se quitará Xamarin Studio, en cambio, todavía existen otros archivos relacionados con Xamarin en un sistema de archivos.

Para quitar todos los seguimientos de Xamarin Studio, deben ejecutarse los siguientes comandos en Terminal:

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Desinstalación del SDK de Mono (MDK)

Mono es una implementación de código abierto de Microsoft .NET Framework que usan todos los productos de Xamarin: Xamarin.iOS, Xamarin.Android y Xamarin.Mac para permitir el desarrollo de estas plataformas en C#.

> [!IMPORTANT]
> Existen otras aplicaciones fuera de Xamarin que también usan Mono, como Unity. Asegúrese de que no hay otras dependencias en Mono antes de desinstalarlo.

Para quitar Mono Framework de un equipo, ejecute los siguientes comandos en Terminal:

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Desinstalación de Xamarin.Android

Existen varios elementos necesarios para la instalación y el uso de Xamarin.Android, como Android SDK y el SDK de Java. Puede encontrar más información disponible sobre estos componentes necesarios en la guía [Instalación manual](https://docs.microsoft.com/visualstudio/mac/installation/).

Use los siguientes comandos para quitar Xamarin.Android:

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Desinstalación de Android SDK y del SDK de Java

Android SDK es necesario para la implementación de aplicaciones de Android. Para quitar completamente todas las partes de Android SDK, busque el archivo en **~/Library/Developer/Xamarin/** y muévalo a la **Papelera**, como se muestra a continuación:

 [![](uninstalling-xamarin-images/image2.png "Para quitar completamente todos los elementos de Android SDK, busque el archivo y muévalo a la Papelera, como se muestra aquí")](uninstalling-xamarin-images/image2.png#lightbox)

El SDK de Java (JDK) no necesita desinstalarse, ya que se ha empaquetado previamente como parte de Mac OS X.

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Desinstalación de Xamarin.iOS

Xamarin.iOS permite el desarrollo de aplicaciones de iOS con C# o F# con Xamarin Studio en un equipo Mac.
El host de compilación de Xamarin también se ha instalado automáticamente con versiones anteriores de Xamarin.iOS para permitir el desarrollo iOS en Visual Studio. Para desinstalar ambos de un equipo, siga los pasos siguientes:

Use los siguientes comandos en Terminal para quitar todos los archivos de Xamarin.iOS desde un sistema de archivos:

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
```

#### <a name="uninstall-the-mac-build-host"></a>Desinstalación del host de compilación de Mac

Nota: Esto puede que ya se haya quitado si ya ha actualizado a Xamarin 4. Ejecute el siguiente comando en Terminal para quitar la aplicación del host de compilación:

```bash
sudo rm -rf "/Applications/Xamarin.iOS Build Host.app"
```

El trabajo `launchd` o el proceso del host de compilación puede que siga ejecutándose o escuchándose en determinados puertos.
Puede comprobar su estado ejecutando `launchctl list | grep com.xamarin.mtvs.buildserver` en Terminal.

```bash
sudo launchctl unload /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
sudo rm -f /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Desinstalación de Xamarin.Mac

Una vez que Xamarin Studio se haya desinstalado correctamente, Xamarin.Mac puede quitarse de su equipo con los siguientes dos comandos para erradicar el producto y la licencia de su equipo Mac respectivamente:

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks-and-inspector"></a>Desinstalación de Inspector y Workbooks

En el caso de la versión 1.2.2 y posteriores, los siguientes comandos Bash quitarán Xamarin Inspector y Workbooks de Xamarin:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Para versiones anteriores, consulte la guía para desinstalar [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

### <a name="uninstall-the-xamarin-installer"></a>Desinstalación del instalador de Xamarin

Use los siguientes comandos para quitar todos los seguimientos del instalador universal de Xamarin:

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Preferences/Xamarin/
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Usar el script de desinstalación

El [script de desinstalación](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh) quitará Xamarin de un equipo. Para usar el script de desinstalación:

1.  Descargue el script de desinstalación y anote la ubicación de descarga. De manera predeterminada, esta es el directorio **/Downloads**.
1.  Abra **Terminal** y cambie el directorio de trabajo al lugar donde se haya descargado el script:

        $ cd /location/of/file

1. Haga que el script sea ejecutable y, después, ejecútelo con **sudo**:

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

1. Por último, elimine el script de desinstalación.

En este momento, Xamarin debe desinstalarse de su equipo.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Desinstalación de Xamarin en un equipo Windows

<a name="uninstallvs2015" />

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 y anterior

Xamarin puede desinstalarse de un equipo Windows mediante el **Panel de control**. Vaya a **Programas y características** o **Programas > Desinstalar un programa** como se muestra a continuación:

 [![](uninstalling-xamarin-images/image3.png "Vaya a Programas y características o a Programas > Desinstalar un programa, como se muestra aquí")](uninstalling-xamarin-images/image3.png#lightbox) [ ![](uninstalling-xamarin-images/image3.png "Vaya a Programas y características o a Programas > Desinstalar un programa, como se muestra aquí")](uninstalling-xamarin-images/image3.png#lightbox)

Para desinstalar Xamarin Studio, busque **Xamarin Studio 5.x.x** en la lista de programas y haga clic en el botón **Desinstalar**. Para quitar la extensión de Xamarin para Visual Studio, y los SDK, busque **Xamarin** en la lista de Programas y haga clic en **Desinstalar**. Estos se muestran en la siguiente captura de pantalla:

 [![](uninstalling-xamarin-images/image4a.png "Ilustración pertinente en esta captura de pantalla")](uninstalling-xamarin-images/image4a.png#lightbox)

Estos programas también pueden quitarse completamente para todos los componentes de Xamarin:

-  SDK de Android


  [![](uninstalling-xamarin-images/image5.png "Estos programas también pueden quitarse completamente para todos los componentes de Xamarin")](uninstalling-xamarin-images/image5.png#lightbox)
-  GTK#


  [![](uninstalling-xamarin-images/image6.png "Estos programas también pueden quitarse completamente para todos los componentes de Xamarin")](uninstalling-xamarin-images/image6.png#lightbox)
-  Instalador de Xamarin Universal


 [![](uninstalling-xamarin-images/image7.png "Estos programas también pueden quitarse completamente para todos los componentes de Xamarin")](uninstalling-xamarin-images/image7.png#lightbox)
-  SDK de Java (tenga cuidado al quitarlo, ya que pueden encontrarse otras dependencias en él)


 [![](uninstalling-xamarin-images/image8.png "Tenga cuidado al quitar el SDK de Java, ya que puede contener otras dependencias")](uninstalling-xamarin-images/image8.png#lightbox)

Para desinstalar completamente Visual Studio, siga las [instrucciones de Microsoft](https://msdn.microsoft.com/library/mt720585.aspx).


<a name="uninstallvs2017" />

## <a name="visual-studio-2017"></a>Visual Studio 2017

Xamarin se puede desinstalar desde Visual Studio 2017 mediante la aplicación del instalador:

1. Use el **menú Inicio** para abrir el **instalador de Visual Studio**.

  [![](uninstalling-xamarin-images/vs2017-01-sml.png "Inicio del Instalador de Visual Studio")](uninstalling-xamarin-images/vs2017-01.png#lightbox)

1. Pulse el botón **Modificar** para la instancia que quiera cambiar.

  [![](uninstalling-xamarin-images/vs2017-02-sml.png "Pulsación en el botón Modificar")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

1. En la pestaña **Cargas de trabajo**, anule la selección de la opción **Desarrollo móvil con .NET** (en la sección **Dispositivos móviles y juegos**).

  [![](uninstalling-xamarin-images/vs2017-03-sml.png "Desactivación de la carga de trabajo Desarrollo móvil")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

1. Haga clic en el botón **Modificar** en la parte inferior derecha de la ventana.
1. El instalador quitará los componentes cuya selección se haya anulado (Visual Studio 2017 debe cerrarse antes de que el instalador pueda realizar cambios).

  [![](uninstalling-xamarin-images/vs2017-04-sml.png "Pulsación en el botón Modificar")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Se quiere desinstalar componentes individuales de Xamarin (por ejemplo, el generador de perfiles o los libros), puede cambiar a la pestaña **Componentes individuales** en el paso 3 y desactivar componentes específicos:

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Desinstalación de componentes individuales")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Para desinstalar completamente Visual Studio 2017, elija **Desinstalar** en el menú de tres barras que hay junto al botón **Iniciar**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Desinstalación completa de Visual Studio")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> Si tiene dos o más instancias de Visual Studio instaladas en paralelo (SxS), como una versión de lanzamiento y una versión preliminar, es posible que al desinstalar una instancia se quiten algunas funciones de Xamarin de las otras instancias de Visual Studio, incluidas:
>
> - Generador de perfiles de Xamarin
> - Libros/Inspector de Xamarin
> - Simulador iOS remoto de Xamarin
> - SDK de Apple Bonjour
>
> Bajo ciertas condiciones, la desinstalación de una de las instancias SxS puede ocasionar la eliminación incorrecta de estas características. Esto puede degradar el rendimiento de la plataforma Xamarin en las instancias de Visual Studio que permanecen en el sistema después de desinstalar la instancia SxS.
>
>Esto se puede resolver mediante la ejecución de la opción **Reparación** en el instalador de Visual Studio, que volverá a instalar los componentes que faltan.

<a name="uninstallvsmac" />

## <a name="uninstalling-visual-studio-for-mac"></a>Desinstalación de Visual Studio para Mac

Para desinstalar Visual Studio para Mac, pero seguir usando Xamarin Studio, busque **Visual Studio.app** en el directorio **/Applications** y arrástrelo a la Papelera. De manera alternativa, haga clic con el botón derecho y seleccione **Trasladar a la Papelera** como se muestra a continuación:

 [![](uninstalling-xamarin-images/image9.png "Clic con el botón derecho en el icono de Visual Studio y selección de Trasladar a la Papelera")](uninstalling-xamarin-images/image9.png#lightbox)

Para desinstalar Xamarin completamente de su equipo, elimine primero Visual Studio para Mac y, después, siga los pasos que se muestran en la sección [Desinstalación de Xamarin Studio](#uninstallxamarinstudio).

## <a name="summary"></a>Resumen

En este artículo hemos analizado la desinstalación completa de Xamarin de un equipo Mac mediante el uso de comandos de la Terminal, así como la desinstalación de Xamarin de un equipo Windows con la opción **Programas y características** (para Visual Studio 2015 y anterior), y el uso del **Instalador de Visual Studio** para Visual Studio 2017.


## <a name="related-links"></a>Vínculos relacionados

- [Script de desinstalación (ejemplo)](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)
