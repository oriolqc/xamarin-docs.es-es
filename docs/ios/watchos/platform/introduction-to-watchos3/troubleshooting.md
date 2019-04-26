---
title: watchOS 3 solución de problemas
description: Este documento proporciona varias sugerencias para solucionar problemas útiles al trabajar con watchOS 3 en Xamarin. Sugerencias se refieren a las actividades, Apple Pay, actualización en segundo plano, NSURLConnection, privacidad y mucho más.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6d2aaf12bd6c45f6268cf87a77d2ee03a9d7a888
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224037"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 solución de problemas

_En este artículo proporciona varias sugerencias de solución de problemas para trabajar con watchOS 3 en las aplicaciones de Xamarin de Apple Watch._

Esta página enumera algunos problemas conocidos que pueden producirse al usar watchOS 3 con Xamarin y la solución para esos problemas.

## <a name="activities"></a>Actividades

Para la actividad de uso compartido para que funcione correctamente, todas las inspecciones emparejados de Apple debe estar ejecutando watchOS 3.

Problemas conocidos:

- Responder a una notificación de uso compartido de la actividad a veces se produce un error.
- Responder a una notificación de uso compartido de actividad con un mensaje puede producir un error.
- Texto contextual sobre un mensaje de notificación de uso compartido de actividad es incorrecto.

## <a name="apple-pay"></a>Apple Pay

Problemas conocidos:

- Si se especifica una fecha de expiración incorrectos o código CW de una nueva atención de pago en Apple Pay, al alcanzar **siguiente** se bloqueará el proceso en ejecución.
- Se pueden bloquear las compras de la aplicación Apple Pay que requieren un número PIN.

## <a name="auto-mac-unlock"></a>Desbloqueo automático Mac

Con watchOS 3 beta 2 (o superior) y beta de macOS Sierra 2 (o posterior), si está habilitada la autenticación en dos fases en la cuenta de iCloud del usuario, puede usar su Apple Watch automática desbloquear su equipo Mac.

## <a name="background-refresh"></a>Actualización en segundo plano

Infringir los recursos del sistema se producirá un bloqueo de aplicación para watchOS 3 con los siguientes códigos de excepción:

- **0xc51bad01** -la aplicación consume demasiado tiempo de CPU.
- **0xc51bad02** -la aplicación consume demasiado tiempo.
- **0xc51bad03** -la aplicación no tenía suficientes en tiempo de ejecución para completar la tarea actual.

## <a name="clock"></a>Clock

Las complicaciones de aplicaciones de Apple Watch recién instaladas pueden aparecer en blanco. Reiniciar el equipo de Apple Watch para corregir este problema.

## <a name="connectivity"></a>Conectividad

Problemas conocidos:

- watchOS no le pedirá al usuario permiso de acceso para los datos de usuario protegido en el Apple Watch. Conceder acceso en la aplicación de iPhone antes de utilizar datos de la aplicación del reloj.
- El Apple Watch puede entrar en un estado donde todas las transmisiones de WatchConnectivity producirá un error, reinicie el Apple Watch para corregir.

## <a name="notifications"></a>Notificaciones

Si un archivo adjunto de medios es demasiado grande, se presentarán en iPhone del usuario, pero no su Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

Cualquier `NSURLConnection` conexiones mediante los protocolos TLS anteriores se producirá un error. Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación dejen de usar SHA-1 y 3DES criptografía tan pronto como sea posible.

A partir de watchOS 3, seguridad de las conexiones SSL/TLS se está aplicando estrictamente por Apple. Las aplicaciones y servicios afectados deben actualizar los servidores web para usar las últimas versiones del protocolo TLS.

## <a name="nsurlsession"></a>NSURLSession

A partir de watchOS 3, el `HTTPBodyStream` propiedad de la `NSMutableURLRequest` clase debe establecerse en un flujo sin abrir desde `NSURLConnection` y `NSURLSession` ahora estrictamente aplicar este requisito.

## <a name="privacy"></a>Privacidad

Problemas conocidos:

Cuando se trabaja con `https://` ambas direcciones URL `NSURLSession` y `NSURLConnection` ya no se admiten conjuntos de cifrado RC4 durante el protocolo de enlace TLS. Uno de los siguientes códigos de error se puede generar:

- **-1200 o-98** : como `NSURLErrorSecurityConnectionFailed` y SecureTransport errores.
- **[3:-9824]-1200** -error de carga de http.
- **-1200**  -  `NSURLConnection` finalizado con errores.

A partir de watchOS 3, seguridad de las conexiones SSL/TLS se está aplicando estrictamente por Apple. Las aplicaciones y servicios afectados deben actualizar los servidores web para usar las últimas versiones del protocolo TLS. Consulte [NSURLConnection](#nsurlconnection) anteriormente para obtener más información.

## <a name="snapshots"></a>Instantáneas

Las aplicaciones de WatchKit que no hayan adoptado la nueva `HandelBackgroundTask` API dejarán de recibir actualizaciones periódicas en watchOS 3. 

## <a name="watchkit"></a>WatchKit

Segundo plano SpriteKit y SceneKit se pondrá en pausa cuando una aplicación entra en segundo plano en el Dock de watchOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novedades de watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
