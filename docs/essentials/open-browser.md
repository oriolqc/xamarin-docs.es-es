---
title: 'Xamarin.Essentials: Open Browser'
description: La clase Browser de Xamarin.Essentials permite que una aplicación abra un vínculo web en el explorador optimizado preferido del sistema o en el explorador externo.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 68bf7f51e9331c1296f26ad7083cf17ab8e480a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102288"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Browser

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Browser** permite que una aplicación abra un vínculo web en el explorador optimizado preferido del sistema o en el explorador externo.

## <a name="using-browser"></a>Uso de Browser

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Browser funciona mediante una llamada al método `OpenAsync` con `Uri` y `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

El modo de inicio determina cómo se inicia el explorador:

## <a name="system-preferred"></a>Preferencia del sistema

Se intentará usar las [pestañas personalizadas de Chrome](https://developer.chrome.com/multidevice/android/customtabs) para cargar el URI y mantener el reconocimiento de la navegación.

## <a name="external"></a>Externo

Se usará `Intent` para solicitar que se abra el URI a través del explorador normal del sistema.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Preferencia del sistema

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) se usa para cargar el URI y mantener el reconocimiento de la navegación.

## <a name="external"></a>Externo

El `OpenUrl` estándar de la aplicación principal se usa para iniciar el explorador predeterminado fuera de la aplicación.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

El explorador predeterminado del usuario se usará siempre, independientemente de `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Código fuente de Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentación de API para Browser](xref:Xamarin.Essentials.Browser)
