---
title: Solución de problemas de iOS 13 y Xamarin
description: Esta sección contiene sugerencias para la funcionalidad de Xamarin iOS 13 relacionados con la solución.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 99fd7e41e1521c6a9254d286bf989281658ccf24
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039792"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Solución de problemas para iOS 13 y Xamarin.iOS

![Características en versión preliminar](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>Actualizar Xcode 11 detiene el simulador de inicio

Después de actualizar a **Xcode 11 beta 1** cada vez que se inicia un simulador de la siguiente excepción y no se inicia el simulador. Esto sucede con todos los simuladores.

### <a name="exception"></a>Excepción

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Solución

Hasta que haya un [corregir](https://github.com/xamarin/xamarin-macios/issues/6216), se pueden seguir los pasos siguientes para volver a instalar el marco de simulador antiguo para permitir a los desarrolladores siguen funcionando:

> [!NOTE]
> Estos pasos se supone que dispone de dos aplicaciones de Xcode:
> - **Xcode11 beta1.app** : la versión beta, que no funciona con simuladores y Visual Studio para Mac.
> - **Xcode102.app** : una versión estable de Xcode 10. Suyo podría denominarse también **Xcode.app**.
>
> Cambiar los ejemplos de línea de comandos siguientes según corresponda para su configuración.

1. Asegúrese de que tiene 11 Xcode seleccionado a través de xcode-select:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Ejecute, si es necesario, el programa de instalación de las herramientas por primera vez.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Quitar el marco de trabajo siguiente:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Volver a la versión anterior de Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Vuelva a ejecutar la primera herramienta de inicio para el _antiguo_ versión de Xcode que acaba de seleccionar

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Después de seguir estos pasos, debería poder volver a trabajar con el simulador de iOS.