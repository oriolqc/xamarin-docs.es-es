---
title: '¿Por qué tiene errores mi aplicación de iOS 9 con: System.Exception: no se pudo serializar el objeto de Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 93666fcb39f0cd717c14eb07e6407801e9f0642e
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350604"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>¿Por qué tiene errores mi aplicación de iOS 9 con: System.Exception: no se pudo serializar el objeto de Objective-C?

Es posible que vea un error de esta forma:

> System.Exception: No se pudo serializar el objeto de Objective-C... No se pudo encontrar una instancia administrada existente para este objeto...

Cambios en la API de iOS 9 requieran el uso de un constructor de devolución de llamada al llamar a código no administrado, como la API subyacente ahora espera. Use la línea siguiente para agregar el constructor de devolución de llamada a la clase: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario. 
