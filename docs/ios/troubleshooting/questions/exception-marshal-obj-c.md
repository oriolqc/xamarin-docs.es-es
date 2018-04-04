---
title: '¿Por qué se realiza la aplicación de iOS 9 por error con: System.Exception: no se pudo serializar el objeto Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f7382ac963249a3f3646a917d8700e3a12873ec9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>¿Por qué se realiza la aplicación de iOS 9 por error con: System.Exception: no se pudo serializar el objeto Objective-C?

Puede ver un error de esta forma:

> System.Exception: No se pudo serializar el objeto Objective-C... No se pudo encontrar una instancia administrada existente para este objeto...

Cambios en la API de iOS 9 requieren que se use un constructor de devolución de llamada al llamar a código no administrado, como la API subyacente ahora lo espere. Para agregar el constructor de devolución de llamada a la clase, use la siguiente línea: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, vea [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo registre un error nuevo si es necesario. 
