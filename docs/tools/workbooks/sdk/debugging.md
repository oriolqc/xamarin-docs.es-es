---
title: Depuración de aplicaciones integradas
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: f36ce595a739667a91d557c4cd896146c5614f51
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="debugging-integrations"></a>Depuración de aplicaciones integradas

## <a name="debugging-agent-side-integrations"></a>Depuración de aplicaciones integradas de lado del agente

Depuración integraciones de lado del agente se realiza mejor mediante los métodos de registro proporcionados por el `Log` clase `Xamarin.Interactive.Logging`. Consulte la [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) para los métodos llamar a.

En Mac OS, aparecen mensajes de registro en el menú de Visor de registro (**Ventana > Visor de registros**) y en el registro de cliente. En Windows, los mensajes sólo aparecen en el registro de cliente, porque no hay ningún visor de registro no existe.

El registro de cliente está en las siguientes ubicaciones en macOS y Windows:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

Hay que tener en cuenta es que al cargar aplicaciones integradas a través de la habitual `#r` mecanismo durante el desarrollo, el ensamblado de integración se recogerán como un _dependencia_ del libro y se empaquetan con él si es una ruta de acceso absoluta no usado. Esto puede causar cambios aparezcan en no propagarse, tal y como si volver a generar la integración hiciera nada.

## <a name="debugging-client-side-integrations"></a>Integraciones de depuración de cliente

Tal y como se escriben en JavaScript y se cargan en la superficie del explorador web integraciones de lado cliente (consulte la [arquitectura](~/tools/workbooks/sdk/architecture.md) documentación), la mejor manera de depurar es con WebKit developer tools en Mac, o mediante el selector de F12 en Windows .

Ambos conjuntos de herramientas le permiten ver código fuente de JavaScript o TypeScript, establecer puntos de interrupción, ver la salida de la consola e inspeccionar y modificar el DOM.

### <a name="mac"></a>Mac

Para habilitar las herramientas de desarrollo para los libros de Xamarin en Mac, ejecute el siguiente comando de su terminal:

```shell
defaults write com.xamarin.Inspector WebKitDeveloperExtras -bool true
```

y, a continuación, reinicie los libros de Xamarin. Una vez hecho esto, debería ver **elemento inspeccionar** aparecen en el menú contextual y un nuevo **Developer** panel estará disponible en las preferencias de los libros. Esta opción le permite elegir si desea que las herramientas de desarrollo abiertas en el inicio:

[![Panel para desarrolladores](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Esta preferencia es de solo reinicio así, deberá reiniciar el cliente de los libros en orden para que surta efecto en los libros de nuevo. Activar las herramientas de desarrollo mediante el menú contextual o las preferencias de mostrará la interfaz de usuario de Safari conocidas:

[![Herramientas de desarrollo de Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Para obtener información sobre el uso de las herramientas de desarrollador de Safari, consulte el [documentación del inspector de WebKit][webkit-docs].

### <a name="windows"></a>Windows

En Windows, el equipo de Internet Explorer proporciona una herramienta conocida como "F12 selector" que es un depurador remoto para las instancias de Internet Explorer incrustados. Puede encontrar la herramienta en la siguiente ubicación:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Selector de F12 de ejecución y se debería ver la instancia incrustada que alimenta la superficie de cliente de los libros en la lista. Elija la base de datos y la familiar F12 aparecerán las herramientas de depuración desde Internet Explorer, asociada al cliente:

[![Herramientas de F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
