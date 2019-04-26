---
title: Cambios de marcos adicionales watchOS 3
description: Este documento describe varios cambios de marco de trabajo presentados con watchOS 3 y cómo trabajar con ellos en Xamarin. Se tratan los datos principales, movimiento de Core, Foundation, HealthKit, HomeKit, PassKit y UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: e3eb4e3454aeab08d1333c5dbc3d4808fa4d676c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224518"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Cambios de marcos adicionales watchOS 3

_En este artículo se trata los cambios adicionales y menores o mejoras en los marcos existentes para watchOS 3._

Además de los cambios principales en iOS, Apple ha realizado modificaciones y mejoras para varios marcos de trabajo existentes en watchOS 3.


## <a name="core-data"></a>Datos básicos

Se realizaron las siguientes mejoras en el marco de trabajo de datos principales para watchos 3:

- Raíz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos admite produzca un error simultáneo y capturando sin serialización.
- El [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) clase mantiene un grupo de almacenes de datos de SQLite.
- El [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WAL la nueva generación de consultas de características donde se pueden anclar los contextos de objeto administrado (MOC) para versiones específicas de la base de datos para capturar futuras y transacciones con errores.
- Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración Core Data.
- Se han agregado varios métodos útiles de nuevo a `NSManagedObject` facilitando la tarea realizar búsquedas y crear subclases.

Para obtener más información, consulte Apple [referencia de Framework Core datos](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Movimiento de Core

Se realizaron las siguientes mejoras en el marco de movimiento de núcleo para watchos 3:

- El nuevo evento de movimiento de dispositivo utiliza el acelerómetro y el giroscopio para proporcionar actualizaciones de movimiento y la orientación. Puede registrar la aplicación para esta actualización (a velocidades de hasta 100Hz).
- El nuevo evento podómetro permite rápidas y notificaciones en tiempo real cuando el usuario pausa y reanuda su ejecución. Use la [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) registrarse para eventos podómetro primer o segundo plano.


## <a name="foundation"></a>Foundation

Se realizaron las siguientes mejoras en el marco de Foundation para inspección 3 del SO:

- Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar las intersecciones de intervalo.
- Se han agregado nuevas propiedades a la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) clase adquirir información local y los formatos de visualización disponible.
- Use la nueva [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) clase para convertir entre distintas unidades de medida (UM) o realizar cálculos en los valores de UOMs diferentes.
- Use la nueva [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) clase para dar formato localizadas medidas para mostrar al usuario final.
- Use la nueva [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) las clases para representar UOMs específicos.


## <a name="healthkit"></a>HealthKit

Se realizaron las siguientes mejoras en el marco HealthKit para inspección 3 del SO:

- Use la nueva [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) clase para especificar el `ActivityType` y `LocationType` de un entrenamiento.
- El nuevo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) y `WheelchairUse` método de la [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) se han agregado clases para trabajar con la silla de ruedas relacionados con los datos de estado.
- Se han agregado nuevas claves de metadatos para los tipos de tiempo (como `HKWeatherConditionClear` y `HKWeatherConditionCloudy`) y los tipos de entrenamiento (como `HKWorkoutActivityTypeFlexibility` y `HKWorkoutActivityTypeWheelchairRunPace`) se han agregado.


## <a name="homekit"></a>HomeKit

Se realizaron las siguientes mejoras en el marco de HomeKit para inspección 3 del SO:

- Agrega la capacidad para ver e interactuar con HomeKit conectados IP cámaras.
- Agrega varios nuevos servicios y características.
- Agregar más contexto y la configuración de los accesorios de servicios principales y los servicios de vínculo.


## <a name="passkit"></a>PassKit

Se realizaron las siguientes mejoras en el marco de PassKit para inspección 3 del SO:

- Expande el marco de trabajo para admitir los pagos seguros en la aplicación en el Apple Watch de productos y servicios.
- Ahora están disponibles las siguientes clases: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) y [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

Se realizaron las siguientes mejoras en el marco UIKit para inspección 3 del SO:

- Para admitir el tipo dinámico en las etiquetas, usan los nuevos campos de texto y cuadros de texto `PreferredFontForTextStyle` método de la `UIFont` clase.
- El `ColorWithDisplayP3` método se agregó para admitir Color amplio.


## <a name="related-links"></a>Vínculos relacionados

- [Introducción (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [Novedades de watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
