---
title: Comandos de & herramientas Sharpie objetivo
description: Este documento proporciona información general de las herramientas incluidas con Sharpie objetivo y los argumentos de línea de comandos para usar con ellos.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 51a0b81204b743824e24cfed83bd73308fa8d506
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977664"
---
# <a name="objective-sharpie-tools--commands"></a>Comandos de & herramientas Sharpie objetivo

_Información general de las herramientas incluidas con el objetivo Sharpie y los argumentos de línea de comandos para poder utilizarlas._

<style type="text/css"> .terminal-blue { color: rgb(10,96,254); } .terminal-green { color: rgb(12,156,26); } .terminal-magenta { color: rgb(152,12,103); } </style>


Una vez objetivo Sharpie correctamente [instalado](~/cross-platform/macios/binding/objective-sharpie/get-started.md), abra una ventana de terminal y familiarizarse con el <em>comandos</em> Sharpie objetivo tiene para ofrecer:

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

|Herramienta|Descripción|
|--- |--- |
|**xcode**|Proporciona información sobre la instalación de Xcode actual y las versiones de iOS y Mac SDK que están disponibles. Vamos a usar esta información más adelante cuando se generan nuestro enlaces.|
|**pod**|Busca, configura, instala (en un directorio local) y enlaza Objective-C [CocoaPod](https://cocoapods.org/) bibliotecas disponibles desde el repositorio principal de especificación. Esta herramienta evalúa la CocoaPod instalado para deducir automáticamente la entrada correcta para pasar a la `bind` herramientas siguientes. Novedad en 3.0|
|**bind**|Analiza los archivos de encabezado (`*.h`) de la biblioteca de Objective-C en inicial [ApiDefinition.cs y StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) archivos.|
|**update**|Comprueba si hay versiones más recientes de Sharpie objetivo y descarga y se inicia el programa de instalación si está disponible.|
|**verify-docs**|Muestra información detallada sobre `[Verify]` atributos.|
|**Documentación**|Navega a este documento en el explorador web predeterminado.|

Para obtener ayuda sobre una herramienta Sharpie objetivo específica, escriba el nombre de la herramienta y el `-help` opción. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Antes de que podemos empezar el proceso de enlace, es necesario obtener información acerca de nuestro SDK instalados actual escribiendo el comando siguiente en el Terminal `sharpie xcode -sdks`. La salida puede diferir dependiendo de qué versiones de Xcode instalada. Objetivo Sharpie busca SDK instalado en cualquier `Xcode*.app` bajo el `/Applications` directorio:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

De lo anterior, podemos ver que tenemos la `iphoneos9.1` SDK instalado en la máquina y tiene `arm64` compatibilidad con la arquitectura. Vamos a usar este valor para todos los ejemplos de esta sección. Con esta información en su lugar, estamos preparados analizar un archivos de encabezado de la biblioteca de Objective-C en inicial `ApiDefinition.cs` y `StructsAndEnums.cs` para el proyecto de enlace.
