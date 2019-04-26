---
title: Los requisitos e instalación de los libros
description: Este documento describe cómo descargar e instalar Xamarin Workbooks, tratan las plataformas admitidas y requisitos del sistema.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: a1001163d89a9a9cda16a7ee5e644307fcc9875c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218596"
---
# <a name="workbooks-installation-and-requirements"></a>Los requisitos e instalación de los libros

<a name="install" />

## <a name="download-and-install"></a>Descargue e instale

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Compruebe el [requisitos](#requirements) a continuación.
2. Descargue e instale [Workbooks de Xamarin para Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Iniciar [jugar](~/tools/workbooks/workbook.md) con libros o pruebe el [ejemplos](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Compruebe el [requisitos](#requirements) a continuación.
2. Descargue e instale [Xamarin Workbooks para Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Iniciar [jugar](~/tools/workbooks/workbook.md) con libros o pruebe el [ejemplos](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10.11 o posterior
- **Windows** -Windows 7 o superior (con Internet Explorer 11 o posterior y .NET 4.6.1 o posterior)

#### <a name="supported-app-platforms"></a>Plataformas de aplicaciones compatibles

|Plataforma de aplicaciones|Sistemas operativos admitidos|Notas|
|--- |--- |--- |
|Mac|Solo se admite en Mac|
|iOS|Compatible con Mac y Windows|Xamarin.iOS 11.0 y Xcode 9.0 o posterior deben instalarse en el equipo Mac. Ejecución de libros de iOS en Windows requiere un host de compilación de Mac que ejecuta todo lo anterior y el [simulador remoto de iOS](~/tools/ios-simulator/index.md) instalado en Windows.|
|Android|Compatible con Mac y Windows|Debe usar el emulador de Google, Visual Studio o Xamarin para Android, con un dispositivo virtual > = 5.0|
|WPF|Solo se admite en Windows|
|Consola (.NET Framework)|Compatible con Mac y Windows|
|Consola (.NET Core)|Compatible con Mac y Windows|


## <a name="reporting-bugs"></a>Informar de errores

Por favor, [informar de problemas en GitHub][bugs]e incluya toda la información siguiente:

### <a name="log-files"></a>Archivos de registro

Adjunte siempre los archivos de registro de cliente de los libros:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x también incluye la capacidad para seleccionar el archivo de registro en el buscador (macOS) o el explorador (Windows) directamente desde el menú principal:

- **Ayudar a > Mostrar archivo de registro**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Rutas de acceso de registro para los libros 1.3 y versiones anteriores:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Información de versión de plataforma

Es muy útil para conocer detalles sobre el sistema operativo y los productos de Xamarin instalados.

En el menú principal en los libros:

* **Ayudar a > Copiar información de versión**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instrucciones para los libros 1.3 y versiones anteriores:

Visual Studio para Mac

- **Visual Studio > acerca de Visual Studio > Mostrar detalles > Copiar información**
- Pegue en el informe de errores

Programa para la mejora

- **Ayuda > acerca de Visual Studio > Copiar información**
- Háganos saber la versión del sistema operativo y si se están ejecutando Windows de 32 bits o 64 bits.

### <a name="samples"></a>Muestras

Si se puede adjuntar o vincular a la **.workbooks** archivos tiene problemas, que pueden ayudar a resolver los errores más rápidamente.

### <a name="devices"></a>Dispositivos

Si tiene problemas para conectar su iOS o Android libro y ya ha comprobado [nuestra página de solución de problemas](~/tools/workbooks/troubleshooting/index.md), necesitamos saber:

- Nombre del dispositivo que está intentando conectarse a
- Versión del sistema operativo del dispositivo
- Android: Compruebe que está usando un x86 emulador
- Android: ¿Qué plataforma emulador usa? ¿Emulador de Google?
  ¿Emulador de Android de Visual Studio? ¿Xamarin Android Player?
- iOS en Windows: ¿Qué versión del simulador remoto de Xamarin iOS ha instalado (consulte **agregar o quitar programas** en **Panel de Control**)?
- iOS en Windows: Proporcione también información de versión de plataforma para el host de compilación de Mac
- ¿Tiene conectividad de red (comprobar a través del explorador web) en el dispositivo?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependiendo de cómo se hayan adquirido en los libros, es posible que deba llevar a cabo dos procedimientos de desinstalación. Compruebe ambos para desinstalar completamente el software.

#### <a name="visual-studio-installer"></a>Instalador de Visual Studio

Si tiene Visual Studio 2017, abra **instalador de Visual Studio**y busque en **componentes individuales** para **Xamarin Workbooks**. Si está activada, desactívela y, a continuación, haga clic en **modificar** para desinstalar.

#### <a name="system-uninstall"></a>Desinstalación del sistema

Si ha instalado los libros usted mismo con un instalador descargado, necesitará desinstalar a través de la **aplicaciones y características** página de configuración del sistema en Windows 10 o a través de **agregar o quitar programas** en el Control Panel en versiones anteriores de Windows.

> **Inicio > Configuración > sistema > aplicaciones y características**

![](install-images/windows-remove.png "Xamarin Workbooks como se muestra en &quot;aplicaciones &amp; características&quot;")

**Debe seguir el procedimiento para el instalador de Visual Studio para asegurarse de que no vuelva a instalar los libros sin su conocimiento.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partir de [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin Workbooks se puede desinstalar desde el terminal mediante la ejecución:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Los archivos y directorios quitará y pedir confirmación antes de continuar, detallará el programa de desinstalación.

Pase el `-help` argumento para el `uninstall` secuencia de comandos para escenarios más avanzados.

En el caso de versiones anteriores, debe quitar manualmente lo siguiente:

1. Eliminar la aplicación Workbooks de `"/Applications/Xamarin Workbooks.app"`
2. Eliminar la aplicación Inspector de `"Applications/Xamarin Inspector.app"`
2. Eliminar los complementos: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` y `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Eliminar Inspector y los archivos auxiliares aquí: `/Library/Frameworks/Xamarin.Interactive.framework` y `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Degradar

El identificador de paquete para **aplicaciones/Xamarin Workbooks.app** cambió de `com.xamarin.Inspector` a `com.xamarin.Workbooks` en la versión 1.4, como Inspector y Workbooks están ahora totalmente divididas.

Debido a un error en los instaladores anteriores, no es posible cambiar las versiones 1.4 o versiones más recientes mediante el 1.3.2 o instaladores anteriores a.

Para cambiar de 1,4 o posterior a 1.3.2 o anterior:

1. [Desinstalación manual de Workbooks e Inspector](#uninstall-macos)
2. Ejecute el 1.3.2 o una versión anterior `.pkg` instalador
