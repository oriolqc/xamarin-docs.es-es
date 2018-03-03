---
title: "watchOS 3 solución de problemas"
description: "Este artículo proporciona varias sugerencias de solución de problemas para trabajar con watchOS 3 en aplicaciones de Xamarin Apple Watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 02ec4c3c9827f7cfac48184eb12491faaa0c92c8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 solución de problemas

_Este artículo proporciona varias sugerencias de solución de problemas para trabajar con watchOS 3 en aplicaciones de Xamarin Apple Watch._

Esta página enumera algunos problemas conocidos que pueden producirse al utilizar watchOS 3 con Xamarin y la solución a esos problemas.

## <a name="activities"></a>Actividades

Para la actividad de uso compartido para que funcione correctamente, todos los relojes de Apple emparejada debe estar ejecutando watchOS 3.

Problemas conocidos:

- Responder a una notificación de uso compartido de actividad en ocasiones, se produce un error.
- Responder a una notificación de uso compartido de la actividad con un mensaje puede producir un error.
- Texto contextual por encima de un mensaje de notificación de uso compartido de actividad es incorrecto.


## <a name="apple-pay"></a>Apple Pay

Problemas conocidos:

- Si se especifica una fecha de expiración incorrecto o código CW para un nuevo cuidado de pago en Apple Pay, al alcanzar **siguiente** se bloqueará el proceso en ejecución.
- En la aplicación de Apple Pay compras que requieren un número PIN pueden bloquearse.



## <a name="auto-mac-unlock"></a>Desbloqueo automático Mac

Mediante watchOS 3 beta 2 (o posterior) y macOS beta de Sierra 2 (o posterior), si está habilitada la autenticación en dos fases en la cuenta de usuario iCloud, pueden utilizar su Apple Watch automáticamente desbloquear su equipo Mac.



## <a name="background-refresh"></a>Actualización en segundo plano

Infracción de recursos del sistema se producirá un bloqueo de aplicación watchOS 3 con los siguientes códigos de excepción:

- **0xc51bad01** -la aplicación consume demasiado tiempo de CPU.
- **0xc51bad02** -la aplicación consume demasiado tiempo real.
- **0xc51bad03** -la aplicación no tenía suficiente en tiempo de ejecución para completar la tarea actual.



## <a name="clock"></a>Clock

Complicaciones desde aplicaciones de Apple Watch recién instaladas pueden aparecer como espacio en blanco. Reiniciar el equipo de Apple Watch para corregir este problema.


## <a name="connectivity"></a>Conectividad

Problemas conocidos:

- watchOS no pedirá al usuario permiso de acceso para los datos de usuario protegido en el de Apple Watch. Conceda el acceso en la aplicación de iPhone antes de utilizar los datos en la aplicación de inspección.
- El Apple Watch puede entrar en un estado donde no todas las transmisiones de WatchConnectivity, reinicie el Apple Watch corregir.


## <a name="notifications"></a>Notificaciones

Si un archivo adjunto de medios es demasiado grande, se presentará en iPhone del usuario, pero no sus Apple Watch.


## <a name="nsurlconnection"></a>NSURLConnection

Cualquier `NSURLConnection` las conexiones que utilizan los protocolos TLS anteriores se producirá un error. Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no es compatible con SSLv3 y se recomienda que la aplicación deje de uso de la criptografía de SHA-1 y 3DES tan pronto como sea posible.

A partir de watchOS 3, seguridad de las conexiones SSL/TLS se está aplicando estrictamente por Apple. Aplicaciones y servicios afectados deben actualizar los servidores web para usar las últimas versiones del protocolo TLS.


## <a name="nsurlsession"></a>NSURLSession

A partir de watchOS 3, el `HTTPBodyStream` propiedad de la `NSMutableURLRequest` clase debe estar establecida en un flujo sin abrir desde `NSURLConnection` y `NSURLSession` ahora estrictamente imponer este requisito.


## <a name="privacy"></a>Privacidad

Problemas conocidos:

Cuando se trabaja con `https://` ambas direcciones URL `NSURLSession` y `NSURLConnection` ya no se admiten conjuntos de cifrado RC4 durante el protocolo de enlace TLS. Uno de los siguientes códigos de error se puede generar:

- **-1200 o-98** : para `NSURLErrorSecurityConnectionFailed` y SecureTransport errores.
- **-1200 [3:-9824]** -error de carga de http.
- **-1200**  -  `NSURLConnection` finalizado con errores.

A partir de watchOS 3, seguridad de las conexiones SSL/TLS se está aplicando estrictamente por Apple. Aplicaciones y servicios afectados deben actualizar los servidores web para usar las últimas versiones del protocolo TLS. Vea [NSURLConnection](#NSURLConnection) anteriormente para obtener más información.


## <a name="snapshots"></a>Instantáneas

Aplicaciones de WatchKit que no han adoptado el nuevo `HandelBackgroundTask` API dejará de recibir actualizaciones periódicas en watchOS 3. 


## <a name="watchkit"></a>WatchKit

Plano SpriteKit y SceneKit se pondrá en pausa cuando una aplicación entra en segundo plano en el área de watchOS.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novedades de watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
