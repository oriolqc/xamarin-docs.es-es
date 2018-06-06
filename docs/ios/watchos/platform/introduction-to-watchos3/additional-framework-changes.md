---
title: Cambios de marcos de trabajo adicionales watchOS 3
description: Este documento describe los diversos cambios de framework introducidos con watchOS 3 y cómo trabajar con ellos en Xamarin. Se tratan los datos de núcleo, movimiento de núcleo, Foundation, HealthKit, HomeKit, PassKit y UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: af44096928c5e543ac99df3faec9f2e9215f666d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791520"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Cambios de marcos de trabajo adicionales watchOS 3

_Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para watchOS 3._

Además de los cambios más importantes en iOS, Apple ha realizado modificaciones y mejoras para varios marcos de trabajo existentes en watchOS 3.


## <a name="core-data"></a>Datos básicos

Se realizaron las siguientes mejoras en el marco de trabajo de datos principal para inspección 3 del SO:

- Raíz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos es compatible con un error y capturar sin serialización simultáneas.
- El [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) clase mantiene un grupo de almacenes de datos de SQLite.
- El [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WLAN la nueva generación de consultas de características que se pueden anclar contextos de objeto administrado (MOC) a versiones específicas de la base de datos para capturar las futuras y transacciones con errores.
- Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración de datos principal.
- Varios métodos de comodidad nuevos se agregaron a `NSManagedObject` lo que sea más fácil realizar búsquedas y crear subclases.

Para obtener más información, vea de Apple [referencia de marco de trabajo de datos principal](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Movimiento de núcleo

Se realizaron las siguientes mejoras en el marco de trabajo de movimiento de núcleo para inspección 3 del SO:

- El nuevo evento de movimiento del dispositivo usa el acelerómetro y giroscopio para proporcionar actualizaciones de movimiento y la orientación. puede registrar la aplicación de la aplicación para este actualizado (con las tarifas de hasta 100Hz).
- El nuevo evento podómetro rápido, habilita las notificaciones en tiempo real cuando el usuario pausa y reanuda la ejecución. Use la [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) para registrar eventos de podómetro de primer plano o en segundo plano.


## <a name="foundation"></a>Foundation

Se realizaron las siguientes mejoras en el marco de trabajo de Foundation para inspección 3 del SO:

- Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar los cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar si hay intersecciones de intervalo.
- Se agregaron nuevas propiedades a la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) clase adquirir información local y los formatos de presentación disponibles.
- Use la nueva [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) clase para convertir entre diferentes unidades de medida (UM) o realizar cálculos en los valores de UOMs diferentes.
- Use la nueva [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) clase para dar formato a medidas localizadas para mostrar al usuario final.
- Use la nueva [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) las clases para representar UOMs específicos.


## <a name="healthkit"></a>HealthKit

Se realizaron las siguientes mejoras en el marco de trabajo HealthKit para inspección 3 del SO:

- Use la nueva [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) clase para especificar el `ActivityType` y `LocationType` de un entrenamiento.
- El nuevo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) y `WheelchairUse` método de la [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) clase se han agregado para trabajar con silla de ruedas relacionadas con datos de estado.
- Se han agregado nuevas claves de metadatos para tipos de tiempo (como `HKWeatherConditionClear` y `HKWeatherConditionCloudy`) y los tipos de entrenamiento (como `HKWorkoutActivityTypeFlexibility` y `HKWorkoutActivityTypeWheelchairRunPace`) se han agregado.


## <a name="homekit"></a>HomeKit

Se realizaron las siguientes mejoras en el marco de trabajo HomeKit para inspección 3 del SO:

- Agrega la capacidad para ver e interactuar con HomeKit conectados IP cámaras.
- Agregar varios nuevos servicios y características.
- Agregar más contexto y la configuración de los accesorios de servicios principales y vínculo.


## <a name="passkit"></a>PassKit

Se realizaron las siguientes mejoras en el marco de trabajo PassKit para inspección 3 del SO:

- Se expande el marco de trabajo para admitir pagos seguros y de la aplicación en el de Apple Watch de mercancías físicas y servicios.
- Ahora están disponibles las siguientes clases: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) y [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

Se realizaron las siguientes mejoras en el marco de trabajo UIKit para inspección 3 del SO:

- Para admitir el tipo dinámico en las etiquetas, usan los nuevos campos de texto y cuadros de texto `PreferredFontForTextStyle` método de la `UIFont` clase.
- El `ColorWithDisplayP3` método se agregó para admitir Color amplia.


## <a name="related-links"></a>Vínculos relacionados

- [Introducción (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [Novedades de watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
