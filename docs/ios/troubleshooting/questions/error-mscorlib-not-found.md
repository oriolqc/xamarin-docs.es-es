---
title: 'Error en tiempo de ejecución: The assembly mscorlib.dll no se encontró o no se pudo cargar'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 84bb6815c19bcacb4a9d1bddc44d340d51199c32
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105797"
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Error en tiempo de ejecución: The assembly mscorlib.dll no se encontró o no se pudo cargar

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Este problema se produce cuando el *oculto* `.monotouch-32` y `.monotouch-64` faltan carpetas en el `.xcarchive` para firmar / creación IPA, desencadena el error en tiempo de ejecución.

