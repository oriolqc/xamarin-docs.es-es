---
title: Reglas de análisis de Xamarin.iOS
description: Este documento describe un conjunto de reglas de análisis que compruebe la configuración de proyecto Xamarin.iOS para ayudar a determinar si la configuración más/better-optimized está disponibles.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 25d2936f70981ed239626193ba6e4993f1378108
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788903"
---
# <a name="xamarinios-analysis-rules"></a>Reglas de análisis de Xamarin.iOS

Análisis de Xamarin.iOS es un conjunto de reglas que comprueban la configuración del proyecto para ayudarle a determinar si la configuración optimizada mejor y más está disponible.

Ejecutar las reglas de análisis con tanta frecuencia como sea posible para encontrar posibles mejoras desde el principio y ahorra tiempo de desarrollo.

Para ejecutar las reglas, en Visual Studio para el menú de Mac, seleccione **proyecto > Ejecutar análisis de código**.

> [!NOTE]
> Xamarin.iOS sólo se ejecuta el análisis de la configuración seleccionada actualmente. Recomendamos encarecidamente ejecutar la herramienta de depuración **y** configuración de lanzamiento.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** el vinculador está deshabilitado en el dispositivo para el modo de depuración.
- **Corrección:** debe intentar ejecutar el código con el vinculador para evitar las sorpresas.
Para configurarla, vaya al proyecto > iOS compilación > comportamiento del vinculador.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** compilaciones de aplicación que inicializar el agente de prueba en la nube se rechazarán Apple cuando envía, ya que usan API privada.
- **Corrección:** agregar o corregir las necesarias #if y define en el código.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** configuración de depuración que usa claves de firma de desarrollador no debe generar un IPA solo sea necesario para la distribución, que ahora utiliza el Asistente para publicación.
- **Corrección:** deshabilite la generación de IPA en Opciones de proyecto para la configuración de depuración.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** la arquitectura admitida para "de la versión | el dispositivo"no es compatible, falta ARM64 de 64 bits. Se trata de un problema como Apple no acepta aplicaciones de iOS sólo de 32 bits en la tienda.
- **Corrección:** Double, haga clic en el proyecto de iOS, vaya a la compilación > iOS crear y cambiar las arquitecturas admitidas por lo que tiene ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** no usa la opción float32 (--aot-options O = = float32) conduce a rendimiento elevado de costo, especialmente en dispositivos móviles, donde es más lento mejorará en gran medida matemática de precisión doble. Tenga en cuenta que .NET utiliza de doble precisión internamente, incluso para float, por lo que si se habilita esta opción afecta a la precisión y, posiblemente, compatibilidad.
- **Corrección:** Double, haga clic en el proyecto de iOS, vaya a la compilación > iOS compilar y desactive el "Realizar todas las operaciones de punto flotante de 32 bits como float de 64 bits".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** se recomienda usar el controlador HttpClient nativo en lugar del administrado para mejorar el rendimiento, menor tamaño del archivo ejecutable y para admitir fácilmente los estándares más recientes.
- **Corrección:** Double, haga clic en el proyecto de iOS, vaya a la compilación > iOS de compilación y cambie la implementación HttpClient NSUrlSession (iOS 7 +) o CFNetwork para admitir la versión anterior a iOS 7.
