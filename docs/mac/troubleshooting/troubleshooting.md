---
title: Sugerencias para solucionar problemas de Xamarin.Mac
description: Este documento describe los enfoques para resolver los problemas detectados al desarrollo de aplicaciones de Xamarin.Mac. También se explica cómo obtener soporte técnico.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f498aab5bfaffc08a22f62a318f8f9f73ab0afca
ms.sourcegitcommit: d294c967a18e6d91f3909c052eeff98ede1a21f6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609914"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Sugerencias para solucionar problemas de Xamarin.Mac

## <a name="overview"></a>Información general

A veces todo un bloqueo mientras se trabaja en un proyecto, en la imposibilidad de obtener una API para trabajar como queremos o intentar solucionar un error. Nuestro objetivo en Xamarin es para que pueda tener éxito en la escritura de las aplicaciones de escritorio y móviles, y hemos proporcionado algunos recursos para ayudar a.

Con cualquiera de estos recursos, hay algunos pasos de preparación que puede seguir para ayudarles a solucionar el problema rápidamente:

- Determinar la causa del problema como la mejor posible a los bloqueos de informe:
 
     - "Mi aplicación se bloquea" es difícil de diagnosticar. "Mi aplicación se bloquea cuando devuelva una matriz vacía para esta llamada" es mucho más fácil trabajar en la solución.

     - "No se puede obtener NSTable funcione" es menos útil de "Ninguno de los métodos en mi NSTableDelegate parecer que se llame en este caso".

- Si es posible proporcionar un programa pequeño ejemplo que muestra el problema. Indagar en las páginas de código fuente para buscar el problema tarda órdenes de magnitud más tiempo y esfuerzo.

- Saber qué cambios realizados a la aplicación para producir un error que aparezca pueden reducir rápidamente el origen del problema. Teniendo en cuenta si recientemente ha actualizado las versiones de Xamarin.Mac, recorte de secciones de la aplicación para encontrar el elemento de la causa del problema, o las pruebas anteriores compilaciones para encontrar qué cambio introdujo el problema puede ser muy útil.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Qué hacer cuando se bloquea la aplicación sin resultados

En la mayoría de los casos, el depurador de Visual Studio para Mac detectará las excepciones y se bloquea en la aplicación y le ayudarán a localizar la causa raíz. Sin embargo, hay algunos casos donde la aplicación en el dock de devolución y, a continuación, salir con poca o ninguna salida. Estos pueden incluir:

- Problemas de firma de código.
- Determinados se bloquea en tiempo de ejecución mono.
- Algunas excepciones de Objective-c y bloqueos.
- Algunos se bloquea una etapa muy temprana de la duración de proceso.
- Algunos desbordamientos de pila.
- Aparece en la versión de macOS su **Info.plist** es más reciente de la versión de macOS instalado actualmente o no es válido.

Depuración de estos programas puede ser frustrante, como buscar puede ser difícil la información necesaria. Estos son algunos enfoques que puede ser útil:

- Asegúrese de que aparece en la versión de macOS el **Info.plist** es la misma que la versión de macOS instalado actualmente en el equipo.
- Compruebe el Visual Studio para la salida de la aplicación Mac (**vista** -> **paneles** -> **resultado de la aplicación**) para seguimientos de pila o de salida en rojo desde Cocoa que puede que describan la salida.
- Ejecutar la aplicación desde la línea de comandos y examine la salida (en el **Terminal** app) mediante el uso de: 

     `MyApp.app/Contents/MacOS/MyApp` (donde `MyApp` es el nombre de la aplicación)
- Puede aumentar la salida mediante la adición de "MONO_LOG_LEVEL" al comando en la línea de comandos, por ejemplo: 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Puede adjuntar un depurador nativo (`lldb`) para el proceso para ver si proporciona más información (Esto requiere una licencia de pago). Por ejemplo, realice lo siguiente:

    1. Escriba `lldb MyApp.app/Contents/MacOS/MyApp` en el Terminal.
    2. Escriba `run` en el Terminal.
    3. Escriba `c` en el Terminal.
    4. Salga cuando finalice la depuración.
- Como último recurso, antes de llamar a `NSApplication.Init` en su `Main` método (o en otros lugares, según sea necesario), podría escribir texto en un archivo en una ubicación conocida para realizar un seguimiento en qué paso de inicio está experimentando problemas.

## <a name="known-issues"></a>Problemas conocidos

Las secciones siguientes tratan los problemas conocidos y sus soluciones.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>No se puede conectar al depurador de aplicaciones en espacio aislado

El depurador se conecta a las aplicaciones de Xamarin.Mac a través de TCP, lo que significa que de forma predeterminada cuando se habilita el espacio aislado, no se puede conectar a la aplicación, por lo que si se intenta ejecutar la aplicación sin los permisos adecuados habilitados, se produce un error *"no se puede conectar a el depurador"*. 

[![Edición de los derechos](troubleshooting-images/debug01.png "los derechos de edición")](troubleshooting-images/debug01-large.png#lightbox)

El **permitir conexiones de red salientes (cliente)** permiso es necesario para que el depurador, este se habilita la depuración con normalidad. Puesto que no se puede depurar sin él, hemos actualizado la `CompileEntitlements` de destino para `msbuild` para agregar automáticamente ese permiso a los derechos para cualquier aplicación que está aislada para la depuración solo compilaciones. Las compilaciones de versión deben usar los derechos especificados en el archivo de derechos, sin modificar.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: no hay datos disponibles para la codificación 437
 
Al incluir 3rd bibliotecas de terceros en una aplicación de Xamarin.Mac, podría obtener un error en el formulario "System.NotSupportedException: No hay datos disponibles para la codificación 437" al intentar compilar y ejecutar la aplicación. Por ejemplo, bibliotecas, como `Ionic.Zip.ZipFile`, puede producir esta excepción durante la operación.

Esto puede solucionarse, abra las opciones para el proyecto de Xamarin.Mac, que se va a **de compilación de Mac** > **internacionalización** y comprobando la **West** internacionalización:

[![Modificar las opciones de compilación](troubleshooting-images/issue01.png "Modificar las opciones de compilación")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>No se pudo compilar (mm5103)

Este error se produce normalmente cuando una nueva versión de Xcode es la versión y ha instalado la versión nueva, pero no aún ejecutarlo. Antes de intentar compilar con una nueva versión de Xcode, deberá primero ejecute esa versión de al menos una vez.

La primera vez que ejecute una nueva versión de Xcode, instala varias herramientas de línea de comandos que son necesarios para Xamarin.Mac. Además, debe realizar una compilación limpia después de actualizar Xcode o su versión de Xamarin.Mac.

Si no se puede resolver este problema, inicie [archivar un error](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Falta entitlements.plist

La versión más reciente de Visual Studio para Mac ha quitado la sección de los derechos de la **Info.plist** editor y lo ha colocado en independiente **Entitlements.plist** editor (para una mejor compatibilidad multiplataforma con Xamarin.iOS).

Con el nuevo Visual Studio para Mac instalado, cuando se crea un nuevo proyecto de aplicación de Xamarin.Mac, un **Entitlements.plist** archivo se agregará automáticamente al árbol del proyecto:

![Selección de los derechos](troubleshooting-images/entitlements01.png "seleccionando los derechos")

Si hace doble clic en el **Entitlements.plist** se mostrará el archivo, el Editor de derechos:

[![Edición de los derechos](troubleshooting-images/entitlements02.png "los derechos de edición")](troubleshooting-images/entitlements02-large.png#lightbox)

Para proyectos de Xamarin.Mac existentes, deberá crear manualmente el **Entitlements.plist** archivo con el botón secundario en el proyecto en el **panel de solución** y seleccionando **agregar**  >  **Nuevo archivo...** . A continuación, seleccione **Xamarin.Mac** > **lista de propiedades vacía**:

![Agregar una nueva lista de propiedades](troubleshooting-images/entitlements03.png "agregar una nueva lista de propiedades")

Escriba `Entitlements` para el nombre y haga clic en el **New** botón. Si el proyecto anteriormente incluye un archivo de derechos, se le pedirá para agregarlo al proyecto en lugar de crear un nuevo archivo:

[![Comprobar la sobrescritura de un archivo](troubleshooting-images/entitlements04.png "comprobando la sobrescritura de un archivo")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>Soporte de la Comunidad en los foros

La Comunidad de desarrolladores que usan productos de Xamarin es asombroso y muchos visite nuestros [foros de Xamarin.Mac](http://forums.xamarin.com/categories/mac) compartir experiencias y a sus conocimientos. Además, los ingenieros de Xamarin periódicamente visite el foro para ayudar a.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Archivar un error

Sus comentarios son importantes para nosotros. Si encuentra algún problema con Xamarin.Mac:

- Busque en el [repositorio de problemas](https://github.com/xamarin/xamarin-macios/issues). 
- Antes de la migración a GitHub, los problemas de Xamarin se recopilaban en [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Busque allí para ver si están los mismos problemas.
- Si no encuentra un problema, registre uno nuevo en el [repositorio de problemas de GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Los problemas de GitHub son públicos. No es posible ocultar comentarios ni datos adjuntos. 

De la siguiente información, incluya toda la que pueda:                                                                                                                                          

- Un ejemplo sencillo que reproduzca el problema. Siempre que sea posible, esta información es **muy útil**. 
- El seguimiento de la pila completo del bloqueo.
- El código de C# del bloqueo. 
