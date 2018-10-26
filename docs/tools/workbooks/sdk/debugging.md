---
title: Depuración de integraciones
description: Este documento describe cómo depurar las integraciones de Xamarin Workbooks, lado del agente y del lado cliente en Windows y Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 86d9c6af93e7f59eb0e819730e46324688df7566
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106005"
---
# <a name="debugging-integrations"></a>Depuración de integraciones

## <a name="debugging-agent-side-integrations"></a>Depuración de integraciones de lado del agente

Depuración de integraciones de lado del agente se realiza mejor mediante los métodos de registro proporcionados por el `Log` clase `Xamarin.Interactive.Logging`. Consulte la [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) para los métodos llamar a.

En macOS, aparecen mensajes de registro en el menú de Visor de registro (**Ventana > Visor de registros**) y en el registro de cliente. En Windows, los mensajes sólo aparecen en el registro de cliente, ya que no hay ningún visor de registro no existe.

El registro de cliente está en las siguientes ubicaciones en macOS y Windows:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Es algo que debe tener en cuenta que al cargar las integraciones a través de la habitual `#r` mecanismo durante el desarrollo, el ensamblado de la integración se recogerán como un _dependencia_ del libro y se empaquetan con él si es una ruta de acceso absoluta no se utiliza. Esto puede causar cambios aparezcan en no propagarse, como si volver a generar la integración de había hecho nada más.

## <a name="debugging-client-side-integrations"></a>Depuración de integraciones de lado cliente

Como las integraciones de lado cliente se escriben en JavaScript y cargan en la superficie del explorador web (consulte la [arquitectura](~/tools/workbooks/sdk/architecture.md) documentación), la mejor manera de depurarlas es con las herramientas de desarrollador de WebKit en Mac, o mediante el selector de F12 en Windows .

Ambos conjuntos de herramientas le permiten ver código fuente de JavaScript/TypeScript, establecer puntos de interrupción, ver la salida de la consola e inspeccionar y modificar el DOM.

### <a name="mac"></a>Mac

Para habilitar las herramientas de desarrollo de Xamarin Workbooks en Mac, ejecute el siguiente comando en el terminal:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

y, a continuación, reinicie Xamarin Workbooks. Una vez hecho esto, debería ver **inspeccionar elemento** aparecen en el menú contextual y un nuevo **Developer** panel estará disponible en las preferencias de los libros. Esta opción le permite elegir si desea que las herramientas de desarrollo abiertas en el inicio:

[![Panel para desarrolladores](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Esta preferencia es de sólo reiniciar así, deberá reiniciar el cliente de los libros en orden para que surta efecto en los libros nuevo. Activar las herramientas de desarrollo mediante el menú contextual o las preferencias mostrará la familiar interfaz de usuario de Safari:

[![Herramientas de desarrollo de Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Para obtener información sobre el uso de las herramientas de desarrollo de Safari, consulte el [documentación del inspector de WebKit][webkit-docs].

### <a name="windows"></a>Windows

En Windows, el equipo de IE proporciona una herramienta conocida como "Selector de F12" es decir, un depurador remoto para las instancias de Internet Explorer incrustados. Puede encontrar la herramienta en la siguiente ubicación:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Selector de F12 de ejecución y debería ver la instancia de embedded que se utiliza en la superficie de cliente de los libros en la lista. Elija la base de datos y presionar F12 familiar aparecerán en el Explorador de Internet de las herramientas de depuración, asociado al cliente:

[![Herramientas de F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
