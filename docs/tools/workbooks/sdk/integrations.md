---
title: Temas de integración avanzada
description: Este documento describe los temas avanzados relacionados con las integraciones de Xamarin Workbooks. Describe el paquete Xamarin.Workbook.Integrations NuGet y la exposición de API dentro de un libro de Xamarin.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 56ee709b78b8587c2717dc9d25a6357041812d23
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104211"
---
# <a name="advanced-integration-topics"></a>Temas de integración avanzada

Deben hacer referencia a ensamblados de integración del [ `Xamarin.Workbooks.Integrations` NuGet][nuget]. Visite nuestro [documentación de inicio rápido](~/tools/workbooks/sdk/index.md) para obtener más información sobre cómo empezar con el paquete de NuGet.

Integraciones de cliente también se admiten y se inician mediante la colocación de archivos JavaScript o CSS con el mismo nombre que el ensamblado de agente de integración en el mismo directorio. Por ejemplo, si el ensamblado de la integración de agente (que hace referencia a NuGet) se denomina `SampleExternalIntegration.dll`, a continuación, `SampleExternalIntegration.js` y `SampleExternalIntegration.css` se integrarán en el cliente también si existen. Integraciones de cliente son opcionales.

La integración externa propio puede empaquetada como NuGet, proporcionada y puede hacer referencia directamente dentro de la aplicación que hospeda el agente o simplemente coloca junto con un `.workbook` archivo que quiera usarla.

Integraciones externas (agente y cliente) en los paquetes de NuGet se cargará automáticamente cuando el paquete se hace referencia, según la documentación de inicio rápido, mientras que los ensamblados de integración se suministra junto a del libro debe hacer referencia a él como lo:

```csharp
#r "SampleExternalIntegration.dll"
```

Al hacer referencia a una integración de este modo, no se cargará el cliente inmediatamente&mdash;, deberá llamar a código de la integración para que se carga. Se podrá resolver este error en el futuro.

El `Xamarin.Interactive` PCL proporciona integración importantes algunas API. Cada integración debe proporcionar al menos un punto de entrada de integración:

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

En este momento, una vez que se hace referencia al ensamblado de integración, el cliente implícitamente cargará los archivos de integración de JavaScript y CSS.

## <a name="apis"></a>API

Como con cualquier ensamblado al que se hace referencia a él un libro o en vivo inspeccionar la sesión, cualquiera de sus API público son accesible para la sesión. Por lo tanto, es importante contar con una superficie de API razonable y segura para los usuarios a explorar.

El ensamblado de la integración es realmente un puente entre una aplicación o el SDK de interés y la sesión. Puede proporcionar nuevas API que tengan sentido específicamente en el contexto de un libro o en vivo inspeccionar la sesión, o no proporcionar ninguna API público y simplemente realizar tareas "en segundo plano" como objeto de producir [representaciones](~/tools/workbooks/sdk/representations.md).

> [!NOTE]
> Las API que deben ser públicos, pero no se deben mostrar a través de IntelliSense se pueden marcar con el habitual `[EditorBrowsable (EditorBrowsableState.Never)]` atributo.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
