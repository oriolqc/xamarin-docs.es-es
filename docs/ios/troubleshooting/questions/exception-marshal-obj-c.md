---
title: '¿Por qué se produce un error System.Exception: Failed to marshal the Objective-C object en mi aplicación para iOS 9?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 3dbb4d9132d5d94e4533704730e95002b5aec0be
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832269"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>¿Por qué se produce un error System.Exception: Failed to marshal the Objective-C object en mi aplicación para iOS 9?

Es posible que vea un error de esta forma:

> System.Exception: No se pudo serializar el objeto de Objective-C... No se pudo encontrar una instancia administrada existente para este objeto...

Cambios en la API de iOS 9 requieran el uso de un constructor de devolución de llamada al llamar a código no administrado, como la API subyacente ahora espera. Use la línea siguiente para agregar el constructor de devolución de llamada a la clase: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario. 
