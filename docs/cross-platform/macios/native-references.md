---
title: IOS, Mac y los proyectos de enlaces de referencias nativo
description: Las referencias nativas le ofrece la capacidad de insertar un marco nativo en un Xamarin.iOS, Xamarin.Mac o proyecto de enlace.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3a497d0bb4674014b8063cb1fbc91eec6e7ae5ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61201564"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Referencias nativas en iOS, Mac y proyectos de enlaces

_Las referencias nativas le ofrece la capacidad de insertar un marco nativo en un proyecto de Xamarin.iOS o Xamarin.Mac o enlace._

Desde iOS 8.0 ha sido posible crear un marco de trabajo incrustado para compartir código entre las extensiones de aplicación y la aplicación principal en Xcode. Mediante la característica de referencia nativa será posible consumir estas plataformas admitidas (creadas con Xcode) en Xamarin.iOS.
 
> [!IMPORTANT]
> No será posible crear marcos incrustados desde cualquier tipo de proyectos de Xamarin.iOS o Xamarin.Mac, las referencias nativas solo permite el consumo de los marcos nativos (Objective-C) existentes.

<a name="Terminology" />

## <a name="terminology"></a>Terminología

En iOS 8 (y versiones posteriores), **marcos incrustado** puede ser tanto incrustados marcos de trabajo vinculados estáticamente y vinculadas dinámicamente. Para distribuirlos correctamente, debe realizar en marcos de trabajo de "fat" que incluye todos sus _segmentos_ para cada arquitectura de dispositivo que desea admitir con la aplicación.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Puertos estáticos frente a. Marcos de trabajo dinámicos

**Marcos estáticos** se vinculan en tiempo de compilación donde **marcos dinámicos** están vinculados en tiempo de ejecución y, por tanto, se puede modificar sin volver a vincular. Si ha utilizado cualquier marco 3rd-party anteriores a iOS 8, usaba un **Framework estático** que se compiló en la aplicación. Consulte Apple [programación de biblioteca dinámica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) documentación para obtener más detalles.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Vs incrustados. Marcos de trabajo del sistema

**Embedded marcos** se incluyen en el lote de aplicaciones y solo son accesibles para la aplicación específica a través de su espacio aislado. **Marcos de trabajo del sistema** se almacenan en el nivel de sistema operativo y están disponibles para todas las aplicaciones en el dispositivo. Actualmente, solo Apple tiene la capacidad para crear marcos de trabajo de nivel de sistema operativo.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Vs finos. Marcos FAT

**Fino marcos** contienen sólo el código compilado para una arquitectura de sistema específica donde **marcos Fat** contienen código para varias arquitecturas. Cada base de código específicos de la arquitectura compilado en un marco de trabajo se conoce como un _segmento_. Por lo tanto, por ejemplo, si tuviéramos un marco que se compiló para las dos arquitecturas de simulador (i386 y X86_64) de iOS, lo contendría dos segmentos.

Si intenta distribuir este marco de trabajo de ejemplo con la aplicación, se podría ejecutar correctamente en el simulador, pero producirá un error en el dispositivo, ya que el marco de trabajo no contiene los segmentos de código específico para un dispositivo iOS. Para asegurarse de que funcione en todas las instancias de un marco de trabajo, también necesitaría incluir segmentos específicos del dispositivo como arm64, armv7 y armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Trabajar con plataformas admitidas

Hay dos pasos que deben completarse para trabajar con plataformas admitidas en una aplicación Xamarin.iOS o Xamarin.Mac: Creación de un marco Fat e incrustar el marco de trabajo.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Creación de un marco Fat

Como se indicó anteriormente, para poder consumir un marco de trabajo incrustado en la aplicación, debe ser un marco Fat que incluye todas las arquitecturas del sistema los segmentos para los dispositivos que se ejecutará la aplicación.

Cuando el marco de trabajo y la aplicación que están en el mismo proyecto de Xcode, esto no es un problema ya que Xcode compilará el marco de trabajo y la aplicación con la misma configuración de compilación. Puesto que las aplicaciones de Xamarin no pueden crear marcos incrustados, no se puede usar esta técnica.

Para resolver este problema, el `lipo` herramienta de línea de comandos puede usarse para combinar dos o más marcos de trabajo en un solo marco Fat que contiene todos los segmentos de necesarios. Para obtener más información sobre cómo trabajar con el `lipo` de comandos, consulte nuestra [vincular bibliotecas nativas](~/ios/platform/native-interop.md) documentación.

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Incrustación de un marco de trabajo

El paso siguiente son necesarios para insertar un marco de trabajo en un proyecto de Xamarin.iOS o Xamarin.Mac con las referencias nativas:

1. Cree un nuevo o abra un proyecto de Xamarin.iOS, Xamarin.Mac o enlace existente.
2. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **Agregar referencia nativa**: 

    [![](native-references-images/ref01.png "En el Explorador de soluciones, haga doble clic en el nombre del proyecto y seleccione Agregar referencia nativa")](native-references-images/ref01.png#lightbox)
3. Desde el **abierto** cuadro de diálogo, seleccione el nombre del marco nativo que desea insertar y haga clic en el **abierto** botón: 

    [![](native-references-images/ref02.png "Seleccione el nombre del marco nativo para insertar y haga clic en el botón Abrir")](native-references-images/ref02.png#lightbox)
4. El marco de trabajo se agregará al árbol del proyecto: 

    [![](native-references-images/ref03.png "El marco de trabajo se agregará al árbol de proyectos")](native-references-images/ref03.png#lightbox)

Cuando se compila el proyecto, el marco nativo se incrustará en el lote de la aplicación.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Extensiones de aplicaciones y plataformas admitidas

Internamente Xamarin.iOS puede sacar partido de esta característica para vincular con el tiempo de ejecución Mono como un marco de trabajo (cuando el destino de implementación es > = iOS 8.0), lo que reduce significativamente para las aplicaciones con extensiones de tamaño de la aplicación (ya que el tiempo de ejecución Mono se incluirá una sola vez para Todo lote de aplicaciones, en lugar de una vez para la aplicación de contenedor y una vez para cada extensión).

Las extensiones se vincularán con el tiempo de ejecución Mono como un marco de trabajo, porque todas las extensiones requieren iOS 8.0.

Aplicaciones que no tienen las extensiones y aplicaciones destinados a iOS 

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de la incorporación de un marco nativo en una aplicación Xamarin.iOS o Xamarin.Mac.

