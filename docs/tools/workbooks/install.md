---
title: "Instalación y los requisitos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: abc9f9402b55a11e313b9938f07f37e5329b55b6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="installation-and-requirements"></a>Instalación y los requisitos

<script> var inspectorOnLoad = función () {var primaryTextBase = "Libros de Xamarin para"; var secondaryTextBase = "o descargar para"; var inspectorDownloadUrlMac = "https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; var inspectorDownloadUrlWin = " https://DL.xamarin.com/Interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  Si (/win/i.test(navigator.platform.toLowerCase())) {aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase;}

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + "Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + "Windows"; };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

<a name="install" />

## <a name="download-and-install"></a>Descargue e instale

<ol>
  <li>Compruebe el <a href="#Requirements"> requisitos</a> a continuación.</li>
  <li>Descargue e instale <a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">Xamarin libros para Mac</a> (<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">o descarga para Windows</a>).
  </li>
  <li>Iniciar <a href="~/tools/workbooks/workbook.md"> jugar</a> con los libros o probar el <a href="https://developer.xamarin.com/workbooks/">ejemplos</a>.
    </li>
</ol>

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10.11 o superior
- **Windows** -Windows 7 o posterior (con Internet Explorer 11 o superior y .NET 4.6.1 o superior)

#### <a name="supported-app-platforms"></a>Plataformas de aplicaciones compatibles

<table>
<thead>
  <tr>
    <th>Plataforma de aplicaciones</th>
    <th>Sistemas operativos admitidos</th>
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
    <td>Compatible con Mac y Windows</td>
    <td>
      <ul>
        <li>Xamarin.iOS 11.0 y Xcode 9.0 o posterior deben instalarse en el equipo Mac.</li>
        <li>Ejecución de libros de iOS en Windows requiere un host de compilación de Mac ejecuta todo lo anterior y el <a href="~/tools/ios-simulator.md">simulador de iOS remoto</a> instalado en Windows.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Android</td>
    <td>Compatible con Mac y Windows</td>
    <td>Debe usar el emulador de Google, Visual Studio o Xamarin para Android, con un dispositivo virtual > = 5.0</td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>Solo se admite en Windows</td>
    <td/>
  </tr>
  <tr>
    <td>Consola (.NET Framework)</td>
    <td>Compatible con Mac y Windows</td>
    <td/>
  </tr>
  <tr>
    <td>Consola (.NET Core)</td>
    <td>Compatible con Mac y Windows</td>
    <td/>
  </tr>
</tbody>
</table>

## <a name="reporting-bugs"></a>Informes errores

Por favor, [informar de problemas en GitHub][bugs]e incluir toda la información siguiente:

### <a name="log-files"></a>Archivos de registro

Adjunte siempre los archivos de registro de cliente de los libros:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x también incluye la capacidad de seleccionar el archivo de registro en Finder (Mac OS) o en el explorador (Windows) directamente desde el menú principal:

- **Archivo de registro de mostrar Ayuda →**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Rutas de acceso de registro para los libros 1.3 y versiones anteriores:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Información de versión de plataforma

Es muy útil para conocer detalles sobre el sistema operativo y Xamarin producto instalado.

En el menú principal en los libros:

* **Ayudar a la información de versión de copia →**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instrucciones para los libros 1.3 y versiones anteriores:

Visual Studio para Mac

- **Visual Studio → sobre Visual Studio → mostrar detalles → copiar información**
- Pegue en el informe de errores

Programa para la mejora

- **Ayudar a → sobre información de copia de Visual Studio →**
- Háganos saber la versión del sistema operativo y si se ejecuta Windows de 32 bits o 64 bits.

### <a name="samples"></a>Muestras

Si solo se puede adjuntar o vincular a la `.workbooks` archivo están teniendo problemas con, que puede ayudar a resolver el error más rápidamente.

### <a name="devices"></a>Dispositivos

Si tiene problemas para conectarse a sus libro Android o iOS y ya ha activado [nuestra página de solución de problemas](~/tools/workbooks/troubleshooting/index.md), necesitamos saber:

- Nombre del dispositivo que está intentando conectarse a
- Versión del sistema operativo del dispositivo
- Android: Compruebe que está usando un x86 emulador
- Android: ¿En qué plataforma emulador se está usando? ¿Emulador de Google?
  ¿Emulador de Android de Visual Studio? ¿Xamarin Android Player?
- iOS en Windows: ¿qué versión del simulador de iOS remoto Xamarin ¿tiene instalado (consulte `Add/Remove Programs` en `Control Panel`)?
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

> **Iniciar → configuración → características y aplicaciones del sistema →**

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

Identificador de la agrupación `/Applications/Xamarin Workbooks.app` cambió de `com.xamarin.Inspector` a `com.xamarin.Workbooks` en la versión 1.4 para facilitar una división futuras de los instaladores de libros de Xamarin & Inspector.

Debido a un error en los instaladores anteriores, no es posible cambiar versiones 1.4 o posterior mediante el 1.3.2 o instaladores anteriores.

Para cambiar de anterior o posterior a 1.3.2 o 1,4:

1. [Desinstalar los libros & Inspector manualmente](#macOS)
2. Ejecute el 1.3.2 o anterior `.pkg` instalador