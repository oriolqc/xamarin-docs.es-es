---
ms.topic: include
ms.openlocfilehash: 5c11c94956f8d56c66c50a9a480177c5b77c2643
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947442"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido** : obtener los datos del sensor tan rápido como sea posible (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Juego** : velocidad adecuada para juegos (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Normal** : velocidad predeterminada adecuada para los cambios de orientación de pantalla.
- **Interfaz de usuario** : velocidad adecuada para la interfaz de usuario general.

Si no se garantiza que el controlador de eventos para ejecutarse en el subproceso de interfaz de usuario y, si el controlador de eventos necesita tener acceso a elementos de interfaz de usuario, use el [ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) método para ejecutar ese código en el subproceso de interfaz de usuario.