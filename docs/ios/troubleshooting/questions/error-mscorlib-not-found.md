---
title: 'Error en tiempo de ejecución: ensamblado mscorlib.dll no se encontró o no se pudo cargar'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 1470396e782fd2343de72e9bea042f45cd07e555
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Error en tiempo de ejecución: ensamblado mscorlib.dll no se encontró o no se pudo cargar

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Este problema se produce cuando el *oculto* `.monotouch-32` y `.monotouch-64` faltan carpetas en el `.xcarchive` para firmar / creación de IPA, desencadena el error en tiempo de ejecución.

