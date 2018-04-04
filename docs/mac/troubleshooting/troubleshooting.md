---
title: Sugerencias para solucionar problemas de Xamarin.Mac
description: Este documento describen métodos para resolver los problemas que surgen al desarrollar aplicaciones de Xamarin.Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3cec5465f3f30b230cc0f4f1b865209ed327a07c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-troubleshooting-tips"></a>Sugerencias para solucionar problemas de Xamarin.Mac

## <a name="overview"></a>Información general

A veces todos hacen bloqueados mientras se trabaja en un proyecto, en la imposibilidad de obtener una API para que funcione como queremos o intentar solucionar un error. Nuestro objetivo en Xamarin es para que sea correcta para escribir las aplicaciones de escritorio y móviles y le ofrecemos algunos recursos para ayudar a.

Con cualquiera de estos recursos, hay algunos pasos de preparación que puede seguir para ayudarle a resolver el problema rápidamente:

- Determinar la causa del problema como la mejor posible a bloqueos de informe:
 
     - "Mi aplicación se bloquea" es difícil de diagnosticar. "Mi aplicación se bloquea cuando devuelva una matriz vacía para esta llamada" es mucho más fácil trabajar en corregir.

     - "No se puede obtener NSTable trabajar" es menos útil de "Ninguno de los métodos en mi NSTableDelegate parece que se llame en este caso".

- Si es posible proporcionar un programa pequeño ejemplo que muestra el problema. Profundizando a través de páginas de código fuente buscando el problema tiene órdenes de magnitud más tiempo y esfuerzo.

- Saber qué cambios realizados a la aplicación para que se produjera un error que aparezca rápidamente pueden restringir el origen del problema. Teniendo en cuenta si recientemente ha actualizado las versiones de Xamarin.Mac, recortar secciones de la aplicación para encontrar el elemento de la causa del problema, o pruebas anteriores compilaciones para encontrar qué cambio introdujo el problema puede ser muy útil.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Qué hacer cuando se bloquea la aplicación sin resultados

En la mayoría de los casos, el depurador de Visual Studio para Mac detectará las excepciones y se bloquea en la aplicación y le ayudarán a realizar un seguimiento de la causa raíz. Sin embargo, hay algunos casos donde la aplicación de rebote en el área y, a continuación, salir con poca o ninguna salida. Estos pueden incluir:

- Problemas de firma de código.
- Ciertos mono en tiempo de ejecución se bloquea.
- Algunas excepciones de c de objetivo y bloqueos.
- Algunos se bloquea muy pronto la vida del proceso.
- Algunos desbordamientos de pila.
- Aparece en la versión de macOS su **Info.plist** es más reciente de la versión instalada actualmente macOS o no es válido.

Depurar estos programas puede resultar frustrante, como buscar la información necesaria puede ser difícil. Estos son algunos de los enfoques que pueden ayudar a que:

- Asegúrese de que aparece en la versión de macOS el **Info.plist** es la misma que la versión de macOS instalados actualmente en el equipo.
- Compruebe el Visual Studio para la salida de la aplicación de Mac (**vista** -> **rellena** -> **salida de la aplicación**) para seguimientos de pila o de salida en rojo desde Cacao que puede describir la salida.
- Ejecutar la aplicación desde la línea de comandos y examine la salida (en el **Terminal** aplicación) mediante el uso de: 

     `MyApp.app/Contents/MacOS/MyApp` (donde `MyApp` es el nombre de la aplicación)
- Puede aumentar la salida agregando "MONO_LOG_LEVEL" para el comando en la línea de comandos, por ejemplo: 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Puede adjuntar un depurador nativo (`lldb`) para el proceso para ver si proporciona toda la información más (Esto requiere una licencia de pagada). Por ejemplo, haga lo siguiente:

    1. Escriba `lldb MyApp.app/Contents/MacOS/MyApp` en el Terminal.
    2. Escriba `run` en el Terminal.
    3. Escriba `c` en el Terminal.
    4. Salida cuando finalice la depuración.
- Como último recurso, antes de llamar a `NSApplication.Init` en su `Main` método (o en otros lugares según sea necesario), puede escribir texto en un archivo en una ubicación conocida para realizar un seguimiento de en qué paso de inicio está experimentando problemas.

## <a name="known-issues"></a>Problemas conocidos

Las secciones siguientes tratan problemas conocidos y sus soluciones.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>No se puede conectar al depurador en aplicaciones en un espacio aislado

El depurador se conecta a aplicaciones de Xamarin.Mac a través de TCP, lo que significa que de forma predeterminada cuando se habilita el espacio aislado, no se puede conectar a la aplicación, por lo que si se intenta ejecutar la aplicación sin los permisos adecuados habilitados, obtendrá un error *"no se puede conectar a el depurador"*. 

[![Editar los derechos](troubleshooting-images/debug01.png "los derechos de edición")](troubleshooting-images/debug01-large.png#lightbox)

El **permitir conexiones de red salientes (cliente)** permiso es necesario para que el depurador, éste se habilita, normalmente la depuración. Puesto que no se pueden depurar sin él, hemos actualizado el `CompileEntitlements` de destino para `msbuild` para agregar automáticamente ese permiso a los derechos para cualquier aplicación que se encuentre en un espacio aislado para depuración solo compila. Versiones de lanzamiento deben usar los derechos especificados en el archivo de derechos, sin modificar.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: no hay datos disponibles para la codificación 437
 
Cuando se incluyen 3rd bibliotecas de entidad en la aplicación Xamarin.Mac, podría obtener un error con el formato "System.NotSupportedException: No hay datos disponibles para la codificación 437" al intentar compilar y ejecutar la aplicación. Por ejemplo, bibliotecas, como `Ionic.Zip.ZipFile`, puede producir esta excepción durante la operación.

Esto se puede resolver, abra las opciones para el proyecto Xamarin.Mac, vaya a **Mac compilar** > **internacionalización** y la comprobación de la **oeste** internacionalización:

[![Modificar las opciones de compilación](troubleshooting-images/issue01.png "Modificar las opciones de compilación")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>No se pudo compilar (mm5103)

Este error ocurre normalmente cuando una nueva versión de Xcode es una versión y ha instalado la nueva versión, pero no aún ha ejecutado. Antes de intentar compilar con una nueva versión de Xcode, necesita que se ejecute primero esa versión de al menos una vez.

La primera vez que ejecute una versión nueva de Xcode, instalan varias herramientas de línea de comandos requeridos por Xamarin.Mac. Además, debe realizar una compilación limpia después de actualizar la versión de Xamarin.Mac o Xcode.

Si no se puede resolver este problema, inicie [registre un error](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Falta entitlements.plist

La versión más reciente de Visual Studio para Mac quitó la sección de derechos de la **Info.plist** editor y se coloca en independiente **Entitlements.plist** editor (para mejorar la compatibilidad multiplataforma con Xamarin.iOS).

Con el nuevo de Visual Studio para Mac instalado, cuando se crea un nuevo proyecto de aplicación de Xamarin.Mac, un **Entitlements.plist** archivo se agregará automáticamente al árbol de proyecto:

![Selección de derechos](troubleshooting-images/entitlements01.png "seleccionando derechos")

Si hace doble clic en el **Entitlements.plist** se mostrará el archivo, el Editor de derechos:

[![Editar los derechos](troubleshooting-images/entitlements02.png "los derechos de edición")](troubleshooting-images/entitlements02-large.png#lightbox)

Para los proyectos de Xamarin.Mac existentes, debe crear manualmente el **Entitlements.plist** archivo con el botón secundario en el proyecto en el **solución Pad** y seleccionando **agregar**  >  **Nuevo archivo...** . A continuación, seleccione **Xamarin.Mac** > **lista de propiedades vacía**:

![Agregar una nueva lista de propiedades](troubleshooting-images/entitlements03.png "agregar una nueva lista de propiedades")

Escriba `Entitlements` para el nombre y haga clic en el **New** botón. Si anteriormente, el proyecto incluye un archivo de derechos, se le pedirá que lo agregue al proyecto en lugar de crear un nuevo archivo:

[![Comprobar la sobrescritura de un archivo](troubleshooting-images/entitlements04.png "comprobar la sobrescritura de un archivo")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="contacting-support-business-or-enterprise-licenses"></a>Ponerse en contacto con soporte técnico (licencias business o enterprise)

Si tiene un negocio o una licencia enterprise, usted es elegible solicitar ayuda directamente de los ingenieros de Xamarin a través de incidencias de soporte técnico. Vea [xamarin.com/support](http://xamarin.com/support) para obtener más información.

## <a name="community-support-on-the-forums"></a>Compatibilidad con la Comunidad en los foros

La Comunidad de desarrolladores usan productos de Xamarin es sorprendente y muchas visitan nuestro [Xamarin.Mac foros](http://forums.xamarin.com/categories/mac) compartir experiencias y a sus conocimientos. Además, los ingenieros de Xamarin periódicamente visitan el foro para ayudar a.

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
