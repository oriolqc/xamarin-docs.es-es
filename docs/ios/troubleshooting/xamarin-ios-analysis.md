---
title: Reglas de análisis de Xamarin.iOS
description: Este documento describe un conjunto de reglas de análisis que compruebe la configuración de proyecto de Xamarin.iOS para ayudar a determinar si la configuración más/better-optimized está disponibles.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 8a4990ce7b2bcacbd4b97b214458531b3d94122e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121040"
---
# <a name="xamarinios-analysis-rules"></a>Reglas de análisis de Xamarin.iOS

Análisis de Xamarin.iOS es un conjunto de reglas que comprueban la configuración del proyecto para ayudarle a determinar si hay valores optimizados mejor y más.

Ejecutar las reglas de análisis tan a menudo como sea posible para encontrar posibles mejoras en las primeras fases y ahorrar tiempo de desarrollo.

Para ejecutar las reglas, en Visual Studio para el menú del equipo Mac, seleccione **proyecto > Ejecutar análisis de código**.

> [!NOTE]
> Análisis de Xamarin.iOS se ejecuta solo en la configuración seleccionada actualmente. Se recomienda encarecidamente ejecutar la herramienta de depuración **y** configuración de lanzamiento.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** el enlazador está deshabilitado en el dispositivo para el modo de depuración.
- **Corrección:** debe intentar ejecutar el código con el vinculador para evitar sorpresas.
Para configurarlo, vaya al proyecto > compilación de iOS > comportamiento del enlazador.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** compilaciones de la aplicación que inicializar el agente de prueba en la nube se rechazarán por Apple cuando envía, ya que usan las API privada.
- **Corrección:** agregar o corregir el #if necesario y se define en el código.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** configuración de depuración que usa claves de firma de desarrollador no debe generar un archivo IPA, según sea necesario solo para su distribución, que ahora usa el Asistente para publicación.
- **Corrección:** deshabilitar las compilación IPA en Opciones de proyecto para la configuración de depuración.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** la arquitectura admitida para "release | el dispositivo"no es compatible, falta ARM64 de 64 bits. Se trata de un problema como Apple no acepta aplicaciones de iOS solo de 32 bits en AppStore.
- **Corrección:** doble clic en el proyecto de iOS, vaya a la compilación > compilación de iOS y cambie las arquitecturas admitidas por lo que tiene ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** no usa la opción float32 (--aot-options O = = float32) lleva a la enorme costo de rendimiento, especialmente en dispositivos móviles, donde es más lento de un modo contrastable matemática de precisión doble. Tenga en cuenta que .NET usa doble precisión internamente, incluso para float, por lo que si se habilita esta opción afecta a la precisión y, posiblemente, compatibilidad.
- **Corrección:** doble clic en el proyecto de iOS, vaya a la compilación > compilación de iOS y desactive el "Realizar todas las operaciones de punto flotante de 32 bits como punto flotante de 64 bits".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** se recomienda usar el controlador de HttpClient nativo en lugar del administrado para mejorar el rendimiento, menor tamaño del archivo ejecutable y para admitir fácilmente los estándares más recientes.
- **Corrección:** doble clic en el proyecto de iOS, vaya a la compilación > compilación de iOS y cambie la implementación de HttpClient NSUrlSession (iOS 7 +) o CFNetwork para admitir la versión anterior a iOS 7.
