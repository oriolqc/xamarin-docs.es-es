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
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416388"
---
# <a name="xamarinios-analysis-rules"></a>Reglas de análisis de Xamarin.iOS

Análisis de Xamarin.iOS es un conjunto de reglas que comprueban la configuración del proyecto para ayudarle a determinar si hay valores optimizados mejor y más.

Ejecutar las reglas de análisis tan a menudo como sea posible para encontrar posibles mejoras en las primeras fases y ahorrar tiempo de desarrollo.

Para ejecutar las reglas, en Visual Studio para el menú del equipo Mac, seleccione **proyecto > Ejecutar análisis de código**.

> [!NOTE]
> Análisis de Xamarin.iOS se ejecuta solo en la configuración seleccionada actualmente. Se recomienda encarecidamente ejecutar la herramienta de depuración **y** configuración de lanzamiento.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** El enlazador está deshabilitado en el dispositivo para el modo de depuración.
- **Fix:** Debe intentar ejecutar el código con el vinculador para evitar sorpresas.
Para configurarlo, vaya al proyecto > compilación de iOS > comportamiento del enlazador.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** Se rechazarán las compilaciones de la aplicación que inicializar al agente de prueba en la nube por Apple cuando envía, que utilizan las API privada.
- **Fix:** Agregar o corregir el #if necesario y se define en el código.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** Configuración de depuración que usa claves de firma de desarrollador no debe generar un archivo IPA según sea necesario solo para su distribución, que ahora usa al Asistente para publicación.
- **Fix:** Deshabilite la generación de IPA en Opciones de proyecto para la configuración de depuración.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** La arquitectura admitida para "release | el dispositivo"no es compatible, falta ARM64 de 64 bits. Se trata de un problema como Apple no acepta aplicaciones de iOS solo de 32 bits en AppStore.
- **Fix:** Haga doble clic en el proyecto de iOS, vaya a la compilación > compilación de iOS y cambie las arquitecturas admitidas por lo que tiene ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** No se usa la opción float32 (--aot-options O = = float32) lleva a la enorme costo de rendimiento, especialmente en dispositivos móviles, donde es más lento de un modo contrastable matemática de precisión doble. Tenga en cuenta que .NET usa doble precisión internamente, incluso para float, por lo que si se habilita esta opción afecta a la precisión y, posiblemente, compatibilidad.
- **Fix:** Haga doble clic en el proyecto de iOS, vaya a la compilación > compilación de iOS y desactive el "Realizar todas las operaciones de punto flotante de 32 bits como punto flotante de 64 bits".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** Se recomienda usar el controlador de HttpClient nativo en lugar del administrado para mejorar el rendimiento, menor tamaño del archivo ejecutable y para admitir fácilmente los estándares más recientes.
- **Fix:** Haga doble clic en el proyecto de iOS, vaya a la compilación > compilación de iOS y cambie la implementación de HttpClient NSUrlSession (iOS 7 +) o CFNetwork para admitir la versión anterior a iOS 7.
