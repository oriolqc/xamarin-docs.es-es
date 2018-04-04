---
title: Solución de problemas de los libros de Xamarin en Android
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 03c775f6f14263878f9f4a4633ba72931b233b17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Solución de problemas de los libros de Xamarin en Android

## <a name="emulator-support"></a>Compatibilidad con el emulador

Para ejecutar un libro de Android, un emulador de Android debe estar disponible para su uso. No se admiten los dispositivos Android físicos.

Se recomienda el emulador de Google junto con HAXM si su equipo lo admite.
Si es necesario disponer Hyper-V habilitado en el sistema, vaya con el emulador de Android de Visual Studio en su lugar.

Debe tener un emulador que ejecuta Android 5.0 o posterior. No se admiten los emuladores ARM. Use `x86` o `x86_64` solo a los dispositivos.

Lea [nuestra documentación sobre la configuración de emuladores de Android] [ android-emu] si no está familiarizado con el proceso.

> [!NOTE]
> Libros 1.1 y versiones anteriores se intente (y producirá un error) para usar los emuladores ARM si están disponibles. Para solucionar esto, emulador inicio el x86 de su elección antes de abrir o crear un libro de Android. Los libros preferirán siempre para conectarse a un emulador en ejecución, siempre que sea compatible.

## <a name="workbooks-wont-load"></a>No se pudo cargar los libros

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Ventana de libro para siempre, nunca gira cargas (Windows)

En primer lugar, compruebe que el emulador tenga acceso a la red totalmente operativa mediante la comprobación de cualquier sitio Web en el Explorador de web el emulador de Windows.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Emulador de Android de Visual Studio no se puede conectar a internet

Si el emulador no tiene acceso a la red, debe seguir estos pasos para corregir el conmutador de red de Hyper-V. Si cambia entre redes Wi-Fi con frecuencia debe repetir periódicamente:

0. **Asegúrese de que las operaciones de red críticos están completos, como Windows Esto puede desconectarse temporalmente de internet.**
1. Cierre emuladores.
2. Abra `Hyper-V Manager`.
3. En `Actions`, abra `Virtual Switch Manager...`.
4. Eliminar todos los conmutadores virtuales.
5. Haga clic en `OK`.
6. Inicie el emulador de Android de VS. Probablemente se le pedirá que vuelva a crear el conmutador de red virtual.
7. Compruebe que el explorador del emulador Android VS puede tener acceso a internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Vínculos relacionados

- [Informes errores](~/tools/workbooks/install.md#reporting-bugs)
