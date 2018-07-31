---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353274"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido** : obtener los datos del sensor tan rápido como sea posible (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Juego** : velocidad adecuada para juegos (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Normal** : velocidad predeterminada adecuada para los cambios de orientación de pantalla.
- **Interfaz de usuario** : velocidad adecuada para la interfaz de usuario general.

Si no se garantiza que el controlador de eventos para ejecutarse en el subproceso de interfaz de usuario y, si el controlador de eventos necesita tener acceso a elementos de interfaz de usuario, use el [ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) método para ejecutar ese código en el subproceso de interfaz de usuario.