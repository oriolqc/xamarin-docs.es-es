---
title: Actualización de aplicaciones unificadas de Xamarin.Mac a 64 bits
description: Esta guía describe cómo actualizar las aplicaciones de Xamarin.Mac a 64 bits de destino. También proporciona ejemplos de los tipos de errores que pueden surgir al realizar este cambio.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 9bd70fec5d6d3bbbc4855980e1542bd4e486acaa
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528525"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Actualización de aplicaciones unificadas de Xamarin.Mac a 64 bits

A partir de enero de 2018, Apple requiere que los nuevos [envíos de Mac App Store de 64 bits de destino](https://developer.apple.com/news/?id=06282017a). Junio de 2018 deben actualizar las aplicaciones que ya están disponibles en el Store de la aplicación de Mac para 64 bits de destino.

El **archivo** > **New** plantilla de proyecto de Xamarin.Mac crea aplicaciones de 64 bits de forma predeterminada, por lo que las aplicaciones creadas recientemente ya son compatibles con 64 bits y no requieren ningún cambio.

## <a name="targeting-64-bit"></a>Destinatarios de 64 bits

1. Abra el **opciones de proyecto** ventana de la aplicación de Xamarin.Mac:

   ![El menú contextual del proyecto](mac-64-bit-images/1-contextual_menu-vsmac.png "el menú contextual del proyecto")

2. Seleccione **de compilación de Mac** y establecer **arquitecturas compatibles** a **x86\_64**:

   [![Establecer las arquitecturas admitidas en x86_64](mac-64-bit-images/2-project_options-vsmac.png "estableciendo las arquitecturas admitidas en x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Si la aplicación tiene dependencias externas, como las referencias nativas o proyectos de enlace, actualícelas a 64 bits de destino.

### <a name="errors"></a>Errores

La primera vez que cree o ejecute la aplicación con compatibilidad con 64 bits, pueden producirse errores de vínculo de problemas de clang o en tiempo de ejecución. Estos errores pueden ocurrir si terceros dependencias — por ejemplo, las referencias nativas en la carga manual y marcos de trabajo de todo el sistema, Xamarin.Mac o proyectos enlaces: no se han actualizado a 64 bits.

> [!TIP]
> Convertir el proyecto a 64 bits es un cambio importante y puede revelar indirectamente varios errores de programación. En concreto puede cambiar el tamaño y la alineación de estructuras de datos, lo que afectaría a firmas p/invoke y código nativo vinculado en el proyecto. Considere la posibilidad de revisar cualquier dada de advertencias de compilación y probar exhaustivamente la aplicación posteriormente para detectar posibles problemas.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Error de ejemplo resultante de una dependencia de terceros vinculados dinámicamente destinados a 64 bits:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Este error se podría seguir en tiempo de ejecución `dlopen` devolver `IntPtr.Zero` en lugar de un identificador esperado.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Error de ejemplo resultante de una dependencia de terceros vinculados estáticamente destinados a 64 bits:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Para compilar y ejecutar correctamente, actualice estas dependencias a 64 bits y vuelva a compilar la aplicación.

