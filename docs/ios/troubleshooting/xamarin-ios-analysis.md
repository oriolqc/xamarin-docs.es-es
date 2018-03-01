---
title: "Reglas de análisis de Xamarin.iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/26/2017
ms.openlocfilehash: 7cf627f369b666bb54d0f512dc1361d2a685a057
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinios-analysis-rules"></a>Reglas de análisis de Xamarin.iOS


## <a name="a-namexia0001xia0001-disabledlinkerrule"></a><a name="XIA0001"/>XIA0001: DisabledLinkerRule

- **Problema:** el vinculador está deshabilitado en el dispositivo para el modo de depuración.
- **Corrección:** debe intentar ejecutar el código con el vinculador para evitar las sorpresas.
Para configurarla, vaya al proyecto > iOS compilación > comportamiento del vinculador.

## <a name="a-namexia0002xia0002-testcloudagentreleaserule"></a><a name="XIA0002"/>XIA0002: TestCloudAgentReleaseRule

- **Problema:** compilaciones de aplicación que inicializar el agente de prueba en la nube se rechazarán Apple cuando envía, ya que usan API privada.
- **Corrección:** agregar o corregir las necesarias #if y define en el código.

## <a name="a-namexia0003xia0003-ipadebugbuildsrule"></a><a name="XIA0003"/>XIA0003: IPADebugBuildsRule

- **Problema:** configuración de depuración que usa claves de firma de desarrollador no debe generar un IPA solo sea necesario para la distribución, que ahora utiliza el Asistente para publicación.
- **Corrección:** deshabilite la generación de IPA en Opciones de proyecto para la configuración de depuración.

## <a name="a-namexia0004xia0004-missing64bitsupportrule"></a><a name="XIA0004"/>XIA0004: Missing64BitSupportRule

- **Problema:** la arquitectura admitida para "de la versión | el dispositivo"no es compatible, falta ARM64 de 64 bits. Se trata de un problema como Apple no acepta aplicaciones de iOS sólo de 32 bits en la tienda.
- **Corrección:** Double, haga clic en el proyecto de iOS, vaya a la compilación > iOS crear y cambiar las arquitecturas admitidas por lo que tiene ARM64.

## <a name="a-namexia0005xia0005-float32rule"></a><a name="XIA0005"/>XIA0005: Float32Rule

- **Problema:** no usa la opción float32 (--aot-options O = = float32) conduce a rendimiento elevado de costo, especialmente en dispositivos móviles, donde es más lento mejorará en gran medida matemática de precisión doble. Tenga en cuenta que .NET utiliza de doble precisión internamente, incluso para float, por lo que si se habilita esta opción afecta a la precisión y, posiblemente, compatibilidad.
- **Corrección:** Double, haga clic en el proyecto de iOS, vaya a la compilación > iOS compilar y desactive el "Realizar todas las operaciones de punto flotante de 32 bits como float de 64 bits".
