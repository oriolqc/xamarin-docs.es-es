---
title: ¿Puedo usar una versión anterior de Xcode o Xamarin.iOS
description: Esta guía describe los problemas relacionados con versiones anteriores de Xamarin.iOS o Xcode (de la versión estable actual).
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 7cbc14e0a912fe9c55ff672796e839a8dcdfd9b5
ms.sourcegitcommit: 864f47c4f79fa588b65ff7f721367311ff2e8f8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2019
ms.locfileid: "64347117"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>¿Puedo usar una versión anterior de Xcode o Xamarin.iOS?

Documentación de Xamarin se da por supuesto el uso de la última Xamarin.iOS y Xcode, que se recomienda. Sin embargo, algunos clientes prefieren usar Xcode o Xamarin.iOS el anterior y desea obtener más información sobre las consecuencias.

Notas de la versión contienen la siguiente advertencia:

> [!WARNING]
> **Con una versión anterior de Xcode**
>
> Con una versión anterior de Xcode (de la mencionada en la fórmula anterior [requisitos](https://docs.microsoft.com/xamarin/ios/release-notes/12/12.8#requirements)) a menudo es posible, pero algunas características no estén disponibles. También algunas limitaciones pueden requerir soluciones alternativas, p. ej.:
>
> - El registrador estático requiere que los archivos de encabezados de Xcode para crear aplicaciones, dando lugar a [ `MT0091` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT0091) o [ `MT4109` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT4109) errores si faltan las API. En la mayoría de los casos habilita al enlazador administrado le ayudará a (mediante la eliminación de la API).
> - Las compilaciones de Bitcode (para tvOS y watchOS) pueden producir un error de envío a la aplicación de Store, a menos que se usa una cadena de herramientas de Xcode 9.0 +.

## <a name="further-information"></a>Para obtener más información

Microsoft recomienda encarecidamente utilizando el Xcode más reciente y la versión más reciente de Xamarin.iOS al desarrollar y enviar aplicaciones. Apple requiere el uso más reciente de Xcode al enviar las aplicaciones.

Tenga en cuenta que uso más reciente de Xcode no impedirá que la aplicación como destino versiones anteriores de iOS. Se basa en las versiones de iOS que admite su **Info.plist** entrada y las API de la aplicación usa.

Es posible instalar varias versiones de Xcode side-by-side, con nombres diferentes, como **Xcode101.app** y **Xcode102.app**. Si usa varias versiones, asegúrese de establecer activo de Xcode en [Visual Studio para Mac configuración](~/ios/troubleshooting/questions/ios-sdk.md) y con el [ `xcode-select` ](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [herramienta de línea de comandos](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_).

Sin embargo, raras circunstancias pueden requerir el uso de los componentes anteriores. Esta documentación describe los desafíos generales pueden enfrentar cuando con versiones anteriores a la más reciente.

Cada versión de Apple es único, sin embargo, y puede encontrarse otros riesgos que no se documentan aquí.

A veces son triviales para resolver esto siempre que sea posible ceñirse a las configuraciones admitidas de Xcode más reciente y más reciente Xamarin.iOS estos desafíos.

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>Uso de una antigua Xamarin.iOS con un antiguo Xcode

No se está actualizando Xamarin.iOS y Xcode es posible, al menos durante algún tiempo. El límite es que, en algún momento, Apple requerirá una versión mínima de Xcode para enviar sus aplicaciones. En este momento debe actualizar todos los componentes (macOS, Xcode y Xamarin.iOS) en las versiones más recientes (o la nueva versión mínima de Xcode requerido por Apple y la versión de Xamarin.iOS coincidente).

Es por lo general más fácil actualizar gradualmente y manténgase al día los pequeños cambios. Para proyectos grandes donde las actualizaciones pueden ser más difícil mantenerlo, mantenerse con el conocido conjunto de trabajo puede ser un buen compromiso.

## <a name="use-of-new-xamarinios-with-older-xcode"></a>Uso de Xamarin.iOS nuevo con Xcode anterior

En general, Xamarin.iOS es compatible con versiones anteriores de Xcode siempre que sea posible. Algunos de los desafíos potenciales incluyen:

- Xamarin.iOS más recientes pueden admitir algunas de las características y las API no están presentes en Xcode seleccionada. 
- El **registrador estático** requiere que los archivos de encabezados de Xcode para crear aplicaciones, dando lugar a [ `MT0091` ](~/ios/troubleshooting/mtouch-errors.md#MT0091) o [ `MT4109` ](~/ios/troubleshooting/mtouch-errors.md#MT4109) errores si faltan las API.
  - Habilita el enlazador administrado ayudará en la mayoría de los casos (mediante la eliminación de los enlaces para la nueva API administrados) si no se utiliza.
- Las compilaciones de Bitcode (para tvOS y watchOS) pueden producir un error de envío a la aplicación de Store, a menos que se usa una cadena de herramientas de Xcode 9.0 +.

## <a name="use-of-new-xcode-with-older-xamarinios"></a>Uso de Xcode nuevo con Xamarin.iOS anterior

Este caso de uso es mucho más difícil, ya que Xamarin.iOS no puede predecir los requisitos cambiantes de Xcode nuevo. Las actualizaciones de macOS también pueden ocasionar problemas y sin revisiones de compatibilidad podrían verse afectadas muchas partes de Xamarin.iOS. 

Hay una serie de posibles áreas donde pueden surgir problemas incluidos:

- Incompatibilidades con `mlaunch`:
  - Soporte técnico del simulador podría no funcionar correctamente (o en absoluto)
  - Compatibilidad con dispositivos podría no funcionar correctamente (o en absoluto)
- Compatibilidad con desconocido `mtouch` 
  - No hay compatibilidad con nuevos marcos de trabajo
  - No se admiten los derechos nueva
  - No se admite para las herramientas nuevas o actualizadas
    - Esto puede afectar también de firma de código

### <a name="new-appstore-submission-rules"></a>Nuevas reglas de envío de AppStore

Apple reserva el derecho a las actualizaciones de las reglas de los envíos de AppStore en cualquier momento. Estos cambios de regla sólo a veces se anuncian de antemano. Algunos de estos cambios requieren cambios para admitir, lo que requerirían un componente de Xamarin.iOS actualizado de herramientas.

Además de los cambios de regla, Apple agrega validaciones adicionales a las aplicaciones enviadas a menudo o refuerza los existentes. Algunos de estos requieren cambios en nuestras herramientas (por ejemplo, un nuevos en la lista negra símbolos). Muchas de ellas se encuentran en primer lugar por los clientes enviar, porque no hay ningún anuncio (o lista) de las reglas.

## <a name="summary"></a>Resumen

Siempre que sea posible, reproduzca seguros por las siguiente instrucciones y a desarrollar y Apple enviar con Xcode más reciente publicada de la aplicación de Store.

A su vez, utilice el Xamarin.iOS más reciente publicado. Esto realizará el seguimiento de las correcciones más recientes que pueden afectar a las aplicaciones enviadas y cumplir con los cambios más recientes de la regla.

Donde esto no es viable, considere el uso de una versión de Xcode y Xamarin.iOS anterior coincidente. Esto puede funcionar durante un tiempo, pero en algún momento Apple insistirán en las herramientas más recientes, así que Piénselo según corresponda.
