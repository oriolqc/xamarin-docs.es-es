---
title: Herramientas y comandos
description: "Información general de las herramientas incluidas con el objetivo Sharpie y los argumentos de línea de comandos para poder utilizarlas."
ms.topic: article
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 0d6e953a6a45b78d470c7ff73e1d6faa7444a683
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="tools--commands"></a>Herramientas y comandos

_Información general de las herramientas incluidas con el objetivo Sharpie y los argumentos de línea de comandos para poder utilizarlas._

<style type="text/css"> azul .terminal {color: rgb(10,96,254);} .terminal verde {color: rgb(12,156,26);} .terminal fucsia {color: rgb(152,12,103);} </style>


Una vez objetivo Sharpie correctamente [instalado](~/cross-platform/macios/binding/objective-sharpie/get-started.md), abra un terminal y familiarizarse con el <em>comandos</em> Sharpie objetivo debe ofrecer:

<pre>$ <b>sharpie -help</b>
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation</pre>

Objetivo Sharpie proporciona las siguientes herramientas:

<table>
  <thead>
    <tr><td>Herramienta</td><td>Descripción</td>
  </thead>
  <tbody>
    <tr><td><b>xcode</b></td><td>Proporciona información sobre la instalación de Xcode actual y las versiones de iOS y Mac SDK que están disponibles. Usaremos esta información más adelante cuando se generan nuestro enlaces.</td></tr>
    <tr><td><b>pod</b></td><td>Busca, configura, instala (en un directorio local) y enlaza Objective-C <a href="https://cocoapods.org">CocoaPod</a> bibliotecas disponibles desde el repositorio principal de especificaciones. Esta herramienta se evalúa como el CocoaPod instalado para deducir automáticamente la entrada correcta para pasar a la <code>bind</code> herramienta siguiente. <em><strong>Novedad en la versión 3.0.</strong></em></td></tr>
    <tr><td><b>bind</b></td><td>Analiza los archivos de encabezado (<code>*.h</code>) en la biblioteca de C de objetivo en el <a href="~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md">inicial <i>ApiDefinition.cs</i> y <i>StructsAndEnums.cs</i> archivos</a>.</td></tr>
    <tr><td><b>update</b></td><td>Comprueba si hay versiones más recientes de Sharpie de objetivo y descarga e inicia el programa de instalación si está disponible.</td></tr>
    <tr><td><b>verify-docs</b></td><td>Muestra información detallada sobre <code>[Verify]</code> atributos.</td></tr>
    <tr><td><b>docs</b></td><td>Navega a este documento en el explorador web predeterminado.</td></tr>
  </tbody>
</table>

Para obtener ayuda sobre una herramienta objetivo Sharpie específica, escriba el nombre de la herramienta y el `-help` opción. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Antes de que se puede iniciar el proceso de enlace, es necesario obtener información sobre nuestro SDK instalados actual, escriba el comando siguiente en el Terminal `sharpie xcode -sdks`. Los resultados pueden diferir dependiendo de qué versiones de Xcode ha instalado. Objetivo Sharpie busca SDK instalado en cualquier `Xcode*.app` en el `/Applications` directorio:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

De lo anterior, podemos ver que tenemos la `iphoneos9.1` SDK instalado en la máquina y tiene `arm64` compatibilidad con la arquitectura. Se va a utilizar este valor para todos los ejemplos de esta sección. Con esta información en su lugar, estamos preparados para analizar un archivos de encabezado de la biblioteca de C de objetivo en la inicial `ApiDefinition.cs` y `StructsAndEnums.cs` para el proyecto de enlace.

