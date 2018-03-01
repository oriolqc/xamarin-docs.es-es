---
title: "Instalación y los requisitos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: a587935e35882ed1dc68817fbbe1ae3e91200f29
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="installation-and-requirements"></a>Instalación y los requisitos

<script> var inspectorOnLoad = función () {var primaryTextBase = "Libros Xamarin & Inspector para"; var secondaryTextBase = "o descargar para"; var inspectorDownloadUrlMac = "https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; var inspectorDownloadUrlWin = "https://dl.xamarin.com/interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  Si (/win/i.test(navigator.platform.toLowerCase())) {aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase;}

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + "Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + "Windows"; };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

## <a name="download-and-installation"></a>Descarga e instalación

<ol>
  <li>Descargue e instale <a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">Xamarin libros & Inspector para Mac</a> (<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">o descarga para Windows</a>).
  </li>
  <li><a href="~/tools/inspector/inspect.md"> Inspeccionar su propia aplicación.</a>
    </li>
</ol>

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10.11 o superior
- **Windows** -Windows 7 o posterior (con Internet Explorer 11 o superior y .NET 4.6.1 o superior)

### <a name="supported-ides"></a>IDE compatible

- Xamarin Studio 6.2 o superior
- Visual Studio para Mac Preview 4 o superior
- Visual Studio 2015 con Xamarin 4.3. x o superior
- Visual Studio de 2017 con cargas de trabajo de Xamarin

Inspección de la aplicación activa está disponible para los clientes de empresa.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas de aplicaciones compatibles

<table>
<thead>
  <tr>
    <th>Plataforma de aplicaciones</th>
    <th>Compatibilidad con IDE</th>
    <th>Notas</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Mac (unificado)</td>
    <td>Solo se admite en Mac</td>
    <td/>
  </tr>
  <tr>
    <td>iOS (unificado)</td>
    <td>Compatible con extra y Visual Studio</td>
    <td>Inspeccionar aplicaciones de iOS de Windows requiere la misma versión de Inspector también esté instalado en el host de compilación de Mac.</td>
  </tr>
  <tr>
    <td>Android</td>
    <td>Compatible con extra y Visual Studio</td>
    <td>
      <ul>
        <li>Debe tener como destino Android > = 4.0.3</li>
        <li>Debe haber habilitado fastdev</li>
        <li>Debe usar los emuladores de Google, Visual Studio o Xamarin Android. Los emuladores de 7 Android no se permita la inspección en este momento.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>Solo se admite en Visual Studio en Windows</td>
    <td/>
  </tr>
</tbody>
</table>

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Informes errores

Se deberían notificar errores directamente a través de Visual Studio:

- **Ayudar a → enviar comentarios → notificar un problema**

Incluya toda la información siguiente:

### <a name="platform-version-information"></a>Información de versión de plataforma

Esta información es vital.

Visual Studio para Mac

- **Visual Studio → sobre Visual Studio → mostrar detalles → copiar información**
- Pegue en el informe de errores

Xamarin Studio

- **Xamarin Studio → sobre Xamarin Studio → mostrar detalles → copiar información**
- Pegue en el informe de errores

Programa para la mejora

- **Ayuda > acerca de Visual Studio > Copiar información**
- Háganos saber la versión del sistema operativo y si se ejecuta Windows de 32 bits o 64 bits.

### <a name="log-files"></a>Archivos de registro

Adjunte siempre IDE y Inspector de archivos de registro de cliente.

Cliente de inspector

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x también incluye la capacidad de seleccionar el archivo de registro en Finder (Mac OS) o en el explorador (Windows) directamente desde el menú principal:

- **Archivo de registro de mostrar Ayuda →**

Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

Programa para la mejora

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- El contenido de Visual Studio `Output` panel también se pueden proporcionar información útil.

### <a name="project-settings"></a>Configuración del proyecto

Si puede adjuntar el `.csproj` para el proyecto que desea inspeccionar, sería muy útil. Esto es más fácil que preguntando opciones de configuración individuales.

También confirme que está en la configuración de depuración.

### <a name="selected-devices"></a>Dispositivos seleccionados

Para iOS y Android, es vital que sabemos cuál es el dispositivo que está depurando en cuando desea inspeccionar. Necesitamos saber:

- Nombre del dispositivo tal como se muestra en el IDE
- Versión del sistema operativo del dispositivo
- Android: Compruebe que está usando un x86 emulador
- Android: ¿En qué plataforma emulador se está usando? ¿Emulador de Google? ¿Emulador de Android de Visual Studio? ¿Xamarin Android Player?
- ¿La aplicación que se está depurando correctamente aparecen y funcionando en el dispositivo?
- ¿El dispositivo tiene conectividad de red (comprobar a través del explorador web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependiendo de cómo se hayan adquirido libros & Inspector, es podrán que deba realizar dos procedimientos de desinstalación. Compruebe si ambos en desinstalar completamente el software.

#### <a name="visual-studio-installer"></a>Instalador de Visual Studio

Si tiene Visual Studio de 2017, abra a "Instalador de Visual Studio" y busque "Xamarin libros" en "Componentes individuales". Si está activada, desactívela y, a continuación, haga clic en "Modificar" para desinstalar.

#### <a name="system-uninstall"></a>Desinstalación de sistema

Si ha instalado los libros & Inspector usted mismo con un instalador descargado, deberá desinstalar a través de la **aplicaciones y características** página de configuración del sistema en Windows 10 o a través de **agregar o quitar programas**en el Panel de Control en versiones anteriores de Windows.

> **Iniciar → configuración → características y aplicaciones del sistema →**

![](install-images/windows-remove.png "Libros de Xamarin y del Inspector, como se muestra en el "Aplicaciones y características"")

**Todavía debe seguir el procedimiento para el instalador de Visual Studio hacer que los libros seguros & Inspector no obtener reinstalan sin su conocimiento.**

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

