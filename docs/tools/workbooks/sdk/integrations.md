---
title: Temas de integración avanzada
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 8ab0bb71d4c79895eca94899c3f277b466fe0eb1
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="external-integrations"></a>Integraciones externos

Deben hacer referencia a ensamblados de integración del [ `Xamarin.Workbooks.Integrations` NuGet][nuget]. Visite nuestro [inicio rápido de documentación](~/tools/workbooks/sdk/index.md) para obtener más información acerca de cómo empezar con el paquete de NuGet.

Integraciones de cliente también se admiten y se inician mediante la colocación de archivos JavaScript o CSS con el mismo nombre que el ensamblado de la integración de agente en el mismo directorio. Por ejemplo, si el ensamblado de la integración de agente (que hace referencia el NuGet) se denomina `SampleExternalIntegration.dll`, a continuación, `SampleExternalIntegration.js` y `SampleExternalIntegration.css` se integrarán en el cliente también si existen. Integraciones de cliente son opcionales.

La integración externa propio puede empaquetar como un NuGet, proporciona y al que hace referencia directamente dentro de la aplicación que hospeda el agente o simplemente colocados unos junto a un `.workbook` archivo que desea usarla.

Integraciones externos (agente y cliente) en los paquetes de NuGet se cargará automáticamente cuando el paquete se hace referencia, según la documentación de inicio rápido, mientras que los ensamblados de integración publicó junto con el libro tendrá que hacer referencia a él como lo:

```csharp
#r "SampleExternalIntegration.dll"
```

Cuando se hace referencia a una integración de esta manera, no se cargará el cliente inmediatamente&mdash;debe llamar a código de la integración para que se carga. Se podrá resolver este error en el futuro.

El `Xamarin.Interactive` PCL proporciona integración importante algunas API. Cada integración debe proporcionar al menos un punto de entrada de integración:

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

Como con cualquier ensamblado al que hace referencia un libro o en vivo inspeccionar sesión, cualquiera de sus API público son accesible para la sesión. Por lo tanto, es importante contar con una superficie de API significativo y segura para los usuarios los exploren.

El ensamblado de integración es efectivamente un puente entre una aplicación o el SDK de interés y la sesión. Puede proporcionar nuevas API que tengan sentido específicamente en el contexto de un libro o en vivo inspeccionar la sesión, o no proporcionar ninguna API público y solo hay que realizar tareas "en segundo plano" como objeto de producir [representaciones](~/tools/workbooks/sdk/representations.md).

> [!NOTE]
> Las API que deben ser pública, pero no deben aparecer a través de IntelliSense se pueden marcar con el habitual `[EditorBrowsable (EditorBrowsableState.Never)]` atributo.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
