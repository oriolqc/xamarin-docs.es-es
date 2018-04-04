---
title: Requisitos y la instalación de los libros
description: Cómo descargar, instalar y utilizar los libros de Xamarin.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 68cac91a9b430d2abd138c0bb8bd334b65986329
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="workbooks-installation-and-requirements"></a>Requisitos y la instalación de los libros

<a name="install" />

## <a name="download-and-install"></a>Descargue e instale

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Compruebe el [requisitos](#requirements) a continuación.
2. Descargue e instale [libros de Xamarin para Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Iniciar [jugar](~/tools/workbooks/workbook.md) con los libros o probar el [ejemplos](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Compruebe el [requisitos](#Requirements) a continuación.
2. Descargue e instale [Xamarin libros para Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Iniciar [jugar](~/tools/workbooks/workbook.md) con los libros o probar el [ejemplos](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10.11 o superior
- **Windows** -Windows 7 o posterior (con Internet Explorer 11 o superior y .NET 4.6.1 o superior)

#### <a name="supported-app-platforms"></a>Plataformas de aplicaciones compatibles

|Plataforma de aplicaciones|Sistemas operativos admitidos|Notas|
|--- |--- |--- |
|Mac (unificado)|Solo se admite en Mac|
|iOS (unificado)|Compatible con Mac y Windows|Xamarin.iOS 11.0 y Xcode 9.0 o posterior deben instalarse en el equipo Mac. Ejecución de libros de iOS en Windows requiere un host de compilación de Mac ejecuta todo lo anterior y el [simulador de iOS remoto](~/tools/ios-simulator.md) instalado en Windows.|
|Android|Compatible con Mac y Windows|Debe usar el emulador de Google, Visual Studio o Xamarin para Android, con un dispositivo virtual > = 5.0|
|WPF|Solo se admite en Windows|
|Consola (.NET Framework)|Compatible con Mac y Windows|
|Consola (.NET Core)|Compatible con Mac y Windows|


## <a name="reporting-bugs"></a>Informes errores

Por favor, [informar de problemas en GitHub][bugs]e incluir toda la información siguiente:

### <a name="log-files"></a>Archivos de registro

Adjunte siempre los archivos de registro de cliente de los libros:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x también incluye la capacidad de seleccionar el archivo de registro en Finder (Mac OS) o en el explorador (Windows) directamente desde el menú principal:

- **Ayuda > revelar el archivo de registro**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Rutas de acceso de registro para los libros 1.3 y versiones anteriores:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Información de versión de plataforma

Es muy útil para conocer detalles sobre el sistema operativo y Xamarin producto instalado.

En el menú principal en los libros:

* **Ayuda > Copiar información de versión**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instrucciones para los libros 1.3 y versiones anteriores:

Visual Studio para Mac

- **Visual Studio > sobre Visual Studio > Mostrar detalles > Copiar información**
- Pegue en el informe de errores

Programa para la mejora

- **Ayuda > acerca de Visual Studio > Copiar información**
- Háganos saber la versión del sistema operativo y si se ejecuta Windows de 32 bits o 64 bits.

### <a name="samples"></a>Muestras

Si solo se puede adjuntar o vincular a la **.workbooks** archivo están teniendo problemas con, que puede ayudar a resolver el error más rápidamente.

### <a name="devices"></a>Dispositivos

Si tiene problemas para conectarse a sus libro Android o iOS y ya ha activado [nuestra página de solución de problemas](~/tools/workbooks/troubleshooting/index.md), necesitamos saber:

- Nombre del dispositivo que está intentando conectarse a
- Versión del sistema operativo del dispositivo
- Android: Compruebe que está usando un x86 emulador
- Android: ¿En qué plataforma emulador se está usando? ¿Emulador de Google?
  ¿Emulador de Android de Visual Studio? ¿Xamarin Android Player?
- iOS en Windows: ¿qué versión del simulador de iOS remoto Xamarin ¿tiene instalado (consulte **agregar o quitar programas** en **el Panel de Control**)?
- iOS en Windows: también proporcione información de versión de la plataforma para el host de compilación de Mac
- ¿El dispositivo tiene conectividad de red (comprobar a través del explorador web)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependiendo de cómo se hayan adquirido libros & Inspector, es podrán que deba realizar dos procedimientos de desinstalación. Compruebe si ambos en desinstalar completamente el software.

#### <a name="visual-studio-installer"></a>Instalador de Visual Studio

Si tiene Visual Studio de 2017, abra **instalador de Visual Studio**y busque en **componentes individuales** para **Xamarin libros**. Si está activada, desactívela y, a continuación, haga clic en **modificar** para desinstalar.

#### <a name="system-uninstall"></a>Desinstalación de sistema

Si ha instalado los libros & Inspector usted mismo con un instalador descargado, deberá desinstalar a través de la **aplicaciones y características** página de configuración del sistema en Windows 10 o a través de **agregar o quitar programas**en el Panel de Control en versiones anteriores de Windows.

> **Inicio > Configuración > sistema > aplicaciones y características**

![](install-images/windows-remove.png "Los libros de Xamarin y del Inspector, como se muestra en &quot;aplicaciones &amp; características&quot;")

**Todavía debe seguir el procedimiento para el instalador de Visual Studio hacer que los libros seguros & Inspector no obtener reinstalan sin su conocimiento.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partir de [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), libros de Xamarin & Inspector se pueden desinstalar desde un terminal ejecutando:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

El programa de desinstalación detallará los archivos y directorios quitará y pedir confirmación antes de continuar.

Pasar el `-help` argumento pasado a la `uninstall` script para escenarios más avanzados.

En el caso de versiones anteriores, debe quitar manualmente lo siguiente:

1. Eliminar la aplicación Workbooks de `"/Applications/Xamarin Workbooks.app"`
2. Eliminar la aplicación Inspector de `"Applications/Xamarin Inspector.app"`
2. Eliminar los complementos: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` y `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Eliminar Inspector y los archivos auxiliares aquí: `/Library/Frameworks/Xamarin.Interactive.framework` y `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Degradar

Identificador de la agrupación **aplicaciones/Xamarin Workbooks.app** cambió de `com.xamarin.Inspector` a `com.xamarin.Workbooks` en la versión 1.4 para facilitar una división futuras de los instaladores de libros de Xamarin & Inspector.

Debido a un error en los instaladores anteriores, no es posible cambiar versiones 1.4 o posterior mediante el 1.3.2 o instaladores anteriores.

Para cambiar de anterior o posterior a 1.3.2 o 1,4:

1. [Desinstalar los libros & Inspector manualmente](#macOS)
2. Ejecute el 1.3.2 o anterior `.pkg` instalador