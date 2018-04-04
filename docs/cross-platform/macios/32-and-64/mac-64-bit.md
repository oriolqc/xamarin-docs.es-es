---
title: Actualizar aplicaciones Xamarin.Mac unificado a 64 bits
description: Esta guía describe cómo actualizar sus aplicaciones Xamarin.Mac para 64 bits de destino
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/22/2018
ms.openlocfilehash: e365fe1af47338f41aebe4bc0d81d289466a9b6c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Actualizar aplicaciones Xamarin.Mac unificado a 64 bits

A partir de enero de 2018, Apple requiere que las nuevas [64 bits de destino de envíos a la tienda de aplicaciones Mac](https://developer.apple.com/news/?id=06282017a). Aplicaciones disponibles en la tienda de aplicaciones Mac deben actualizarse a 64 bits de destino por de 2018 de junio.

El **archivo** > **New** Xamarin.Mac plantilla de proyecto crea aplicaciones de 64 bits de forma predeterminada, por lo que las aplicaciones recientemente creadas ya son compatibles con 64 bits y no requieren ningún cambio.

## <a name="targeting-64-bit"></a>Destinatarios de 64 bits

1. Abra la **Project Options** ventana para el está la aplicación Xamarin.Mac:

   ![El menú contextual del proyecto](mac-64-bit-images/1-contextual_menu-vsmac.png "el menú contextual del proyecto")

2. Seleccione **Mac compilar** y establecer **admite arquitecturas** a **x86\_64**:

   [![Establecer las arquitecturas admitidas en x86_64](mac-64-bit-images/2-project_options-vsmac.png "establecer las arquitecturas admitidas en x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Si la aplicación tiene dependencias externas como las referencias nativas o proyectos de enlace, actualizarlos a 64 bits de destino.

### <a name="errors"></a>Errores

La primera vez que compilar o ejecuta la aplicación con la compatibilidad de 64 bits, podría experimentar errores de vínculo de problemas de clang o en tiempo de ejecución. Estos errores pueden producirse si terceros dependencias: por ejemplo, las referencias nativas Xamarin.Mac o proyectos de enlaces o carga manual y marcos de trabajo de todo el sistema, no se han actualizado a 64 bits.

> [!TIP]
> Convertir el proyecto a 64 bits es un cambio importante y puede descubrir indirectamente varios errores de programación. En concreto puede cambiar el tamaño y la alineación de estructuras de datos, lo que afectaría a firmas p/invoke y código nativo vinculado en el proyecto. Considere la posibilidad de revisar las advertencias de compilación dadas y probar la aplicación throughly posteriormente para detectar posibles problemas.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Error de ejemplo procedente de una dependencia de terceros vinculados dinámicamente que tienen como destino 64 bits:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Este error se podría seguir en tiempo de ejecución `dlopen` devolver `IntPtr.Zero` en lugar de un identificador esperado.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Error de ejemplo procedente de una dependencia de terceros vinculados estáticamente que no está diseñado para 64 bits:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Para compilar y ejecutar correctamente, actualice estas dependencias a 64 bits y vuelva a compilar la aplicación.

