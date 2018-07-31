---
title: Solución de problemas de los libros de Xamarin en Android
description: Este documento ofrece sugerencias para solucionar problemas para trabajar con libros de Xamarin en Android. Describe la compatibilidad con el emulador, que no se pudo cargar los libros y otros temas.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: b0333e1a40570374ee6218b7a848d2dd1c06b872
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351722"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Solución de problemas de los libros de Xamarin en Android

## <a name="emulator-support"></a>Compatibilidad con el emulador

Para ejecutar un libro de Android, un emulador de Android debe estar disponible para su uso. No se admiten los dispositivos Android físicos.

Se recomienda el emulador de Google con HAXM si su equipo lo admite.
Si debe tener habilitado en el sistema de Hyper-V, vaya con el emulador de Android de Visual Studio en su lugar.

Debe tener un emulador que se ejecuta Android 5.0 o posterior. No se admiten los emuladores ARM. Use `x86` o `x86_64` solo a los dispositivos.

Lea [nuestra documentación sobre cómo configurar los emuladores de Android] [ android-emu] si no está familiarizado con el proceso.

> [!NOTE]
> Libros 1.1 y versiones anteriores se intente (y producirá un error) para usar los emuladores ARM si están disponibles. Para solucionar esto, emulador inicio el x86 que prefiera antes de abrir o crear un libro de Android. Libros siempre preferirá para conectarse a un emulador en ejecución, siempre que sea compatible.

## <a name="workbooks-wont-load"></a>No se pudo cargar los libros

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Ventana del libro para siempre, nunca gira cargas (Windows)

En primer lugar, compruebe que el emulador tiene acceso a la red totalmente operativa mediante la comprobación de cualquier sitio Web en el Explorador de web del emulador.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Emulador de Android de Visual Studio no se puede conectar a internet

Si el emulador no tiene acceso a la red, deberá seguir estos pasos para corregir el conmutador de red de Hyper-V. Si cambia entre las redes Wi-Fi con frecuencia necesitará periódicamente Repita este paso:

0. **Asegúrese de que las operaciones de red críticos están completas, como esto puede desconectarse temporalmente Windows desde internet.**
1. Cierre los emuladores.
2. Abra `Hyper-V Manager`.
3. En `Actions`, abra `Virtual Switch Manager...`.
4. Eliminar todos los conmutadores virtuales.
5. Haga clic en `OK`.
6. Iniciar el emulador de Android de VS. Probablemente se le indicará que volver a crear el conmutador de red virtual.
7. Compruebe que el explorador del emulador de VS Android puede acceder a internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Vínculos relacionados

- [Informar de errores](~/tools/workbooks/install.md#reporting-bugs)
