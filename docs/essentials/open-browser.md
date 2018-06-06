---
title: Abra Xamarin.Essentials explorador
description: La clase de explorador en Xamarin.Essentials permite a una aplicación abrir un vínculo web en el explorador preferida de sistema optimizado o un explorador externo.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 563d3899cffb80c0215d90e8e4392046c4635256
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783140"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: explorador

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **explorador** clase permite a una aplicación abrir un vínculo web en el explorador preferida de sistema optimizado o un explorador externo.

## <a name="using-browser"></a>Usar el explorador

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad del explorador funciona mediante una llamada a la `OpenAsync` método con el `Uri` y `BrowserLaunchType`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchType.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

El tipo de inicio determina cómo se inicia el explorador:

## <a name="system-preferred"></a>Sistema preferido

[Chrome personalizado pestañas](https://developer.chrome.com/multidevice/android/customtabs) will intentado usar el Uri de carga y mantener un conocimiento de navegación.

## <a name="external"></a>Externo

Un `Intent` se usará para solicitar el Uri se abre a través del explorador normal de sistemas.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Sistema preferido

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) se usa para cargar el Uri y mantener un conocimiento de navegación.

## <a name="external"></a>Externo

El estándar `OpenUrl` en la aplicación principal se utiliza para iniciar el explorador predeterminado fuera de la aplicación.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Explorador predeterminado del usuario siempre se iniciará sin tener en cuenta el `BrowserLaunchType`.

--------------

## <a name="api"></a>API

- [Código fuente de explorador](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentación del explorador de API](xref:Xamarin.Essentials.Browser)
