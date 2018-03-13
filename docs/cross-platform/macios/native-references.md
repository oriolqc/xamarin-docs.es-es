---
title: Referencias nativas
description: Las referencias nativas ofrece la capacidad de insertar un marco nativo en un proyecto de Xamarin.iOS o Xamarin.Mac o enlace.
ms.topic: article
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5a33993bdef16191b66127dcc68c57661636c0f8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="native-references"></a>Referencias nativas

_Las referencias nativas ofrece la capacidad de insertar un marco nativo en un proyecto de Xamarin.iOS o Xamarin.Mac o enlace._


Desde iOS 8.0 ha sido posible crear un marco de trabajo incrustado para compartir código entre las extensiones de aplicación y la aplicación principal en Xcode. Mediante la característica de referencia nativa será posible usar estos marcos incrustados (creados con Xcode) en Xamarin.iOS.
 
> [!IMPORTANT]
> **Nota:** no será posible crear marcos incrustados desde cualquier tipo de proyectos de Xamarin.iOS o Xamarin.Mac, las referencias nativas sólo permitir el consumo de los marcos (Objective-C) nativo existentes.




<a name="Terminology" />

## <a name="terminology"></a>Terminología

En iOS 8 (y versiones posteriores), **marcos incrustados** puede ser tanto incrustan marcos de trabajo vinculados estáticamente y vinculadas dinámicamente. Para distribuirlos correctamente, debe convertirlos en marcos "fat" que incluye todos sus _sectores_ para cada arquitectura de dispositivo que desee admitir en su aplicación.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Puertos estáticos frente a Marcos de trabajo dinámicos

**Marcos de trabajo estáticas** se vinculan en tiempo de compilación donde **marcos dinámica** se vinculan en tiempo de ejecución y, en consecuencia, se puede modificar sin volver a vincular. Si ha utilizado cualquier marco de entidad 3rd antes de iOS 8, utilizara un **Framework estática** que se compilan en la aplicación. Vea de Apple [programación de biblioteca dinámica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) documentación para obtener más detalles.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Vs incrustados. Marcos de trabajo del sistema

**Incrusta marcos** se incluyen en el paquete de aplicaciones y sólo son accesibles para la aplicación específica a través de su espacio aislado. **Marcos de trabajo del sistema** se almacenan en el nivel de sistema operativo y están disponibles para todas las aplicaciones en el dispositivo. Actualmente, solo Apple tiene la capacidad para crear marcos de trabajo de nivel de sistema operativo.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Vs finos. Marcos FAT

**Fino marcos** contienen únicamente el código compilado para una arquitectura de sistema específico donde **marcos Fat** contiene código para varias arquitecturas. Cada código de base de específicos de la arquitectura que compila en un marco de trabajo se conoce como un _segmento_. Por lo tanto, por ejemplo, si ha surgido un marco de trabajo que se compiló para dos iOS arquitecturas de simulador (i386 y X86_64), contener dos segmentos.

Si ha intentado distribuir este marco de trabajo de ejemplo con la aplicación, se podría ejecutar correctamente en el simulador, pero producirá un error en el dispositivo, ya que el marco de trabajo no contiene los segmentos de código específico para un dispositivo iOS. Para asegurarse de que un marco de trabajo funcione en todas las instancias, también necesitaría incluir intervalos específicos del dispositivo como arm64, armv7 y armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Trabajar con marcos incrustados

Hay dos pasos que deben realizarse para trabajar con marcos incrustados en una aplicación Xamarin.iOS o Xamarin.Mac: creación de un marco Fat e incrustar el marco de trabajo.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Creación de un marco Fat

Como se mencionó anteriormente, para poder consumir un marco de trabajo incrustado en la aplicación, debe ser un marco Fat que incluye todas las arquitecturas de sistema segmentos para los dispositivos que se ejecutará la aplicación.

Cuando el marco de trabajo y la aplicación consume están en el mismo proyecto de Xcode, esto no es un problema como Xcode compilará el marco de trabajo y la aplicación con la misma configuración de compilación. Puesto que las aplicaciones Xamarin no pueden crear marcos incrustados, no se puede usar esta técnica.

Para resolver este problema, el `lipo` herramienta de línea de comandos puede utilizarse para combinar dos o más marcos de trabajo en una versión de Framework Fat que contiene todos los intervalos necesarios. Para obtener más información sobre cómo trabajar con el `lipo` de comandos, consulte nuestro [vincular bibliotecas nativas](~/ios/platform/native-interop.md) documentación.

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Incrustar un marco de trabajo

El paso de seguimiento son necesarios para incrustar un marco de trabajo en un proyecto de Xamarin.iOS o Xamarin.Mac con las referencias nativas:

1. Crear un nuevo o abra un proyecto existente de Xamarin.iOS, Xamarin.Mac o enlace.
2. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **Agregar referencia nativa**: 

    [![](native-references-images/ref01.png "En el Explorador de soluciones, haga doble clic en el nombre del proyecto y seleccione Agregar referencia nativa")](native-references-images/ref01.png#lightbox)
3. Desde el **abiertos** cuadro de diálogo, seleccione el nombre del marco nativo que desea insertar y haga clic en el **abiertos** botón: 

    [![](native-references-images/ref02.png "Seleccione el nombre del marco nativo para incrustar y haga clic en el botón Abrir")](native-references-images/ref02.png#lightbox)
4. El marco de trabajo se agregará al árbol del proyecto: 

    [![](native-references-images/ref03.png "El marco de trabajo se agregará al árbol de proyectos")](native-references-images/ref03.png#lightbox)

Cuando se compila el proyecto, el marco nativo se incrustará en el paquete de la aplicación.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Extensiones de aplicaciones y marcos incrustados

Internamente Xamarin.iOS puede sacar partido de esta característica para vincular con el tiempo de ejecución Mono como un marco de trabajo (cuando el destino de implementación es > = iOS 8.0), lo que reduce el tamaño de la aplicación significativamente para las aplicaciones con las extensiones (ya que el tiempo de ejecución Mono se incluirá una sola vez para la aplicación toda agrupación, en lugar de una vez para la aplicación de contenedor y una vez para cada extensión).

Las extensiones se vinculan con el tiempo de ejecución Mono como marco de trabajo, dado que todas las extensiones requieren iOS 8.0.

Aplicaciones que no tienen extensiones y aplicaciones que iOS de destino 

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de la incorporación de un marco nativo en una aplicación Xamarin.iOS o Xamarin.Mac.

