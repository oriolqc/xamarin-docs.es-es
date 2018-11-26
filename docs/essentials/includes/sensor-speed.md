---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 676c5a6795ab4896ccd1b288424bf2040b1208aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2018
ms.locfileid: "52294830"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido**: obtener los datos del sensor tan rápido como sea posible (no se garantiza la devolución en el subproceso de interfaz de usuario).
- **Juego**: velocidad adecuada para juegos (no se garantiza la devolución en el subproceso de interfaz de usuario).
- **Normal**: velocidad predeterminada adecuada para los cambios de orientación de pantalla.
- **Interfaz de usuario**: velocidad adecuada para la interfaz de usuario general.

Si no se garantiza la ejecución del controlador de eventos en el subproceso de interfaz de usuario y si el controlador de eventos necesita tener acceso a elementos de la interfaz de usuario, use el método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para ejecutar ese código en el subproceso de interfaz de usuario.