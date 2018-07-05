---
title: Compilación de XAML en Xamarin.Forms
description: En este artículo se explica cómo XAML se puede compilar directamente en lenguaje intermedio (IL) con el compilador de Xamarin.Forms XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/02/2018
ms.openlocfilehash: 4635857fc850b2985f988f8c20ff854e487f79ed
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403408"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilación de XAML en Xamarin.Forms

_XAML se puede compilar opcionalmente directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC)._

XAMLC ofrece una serie de ventajas:

- Realiza la comprobación en tiempo de compilación de XAML, notificando al usuario de los errores.
- Reduce parte del tiempo de carga y creación de instancias para los elementos XAML.
- Facilita reducir el tamaño de archivo del ensamblado final al dejar de incluir archivos .xaml.

XAMLC está deshabilitado de forma predeterminada para garantizar la compatibilidad con versiones anteriores. Puede habilitarse en el nivel de clase y ensamblado agregando la `XamlCompilation` atributo.

En el ejemplo de código siguiente se muestra cómo habilitar XAMLC en el nivel de ensamblado:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

En este ejemplo, se realizará la comprobación de todo el XAML contenido dentro del ensamblado de tiempo de compilación, con errores XAML que se notifica en tiempo de compilación en lugar de tiempo de ejecución. Por lo tanto, el `assembly` prefijo para el `XamlCompilation` atributo especifica que el atributo se aplica a todo el ensamblado.

En el ejemplo de código siguiente se muestra cómo habilitar XAMLC en el nivel de clase:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

En este ejemplo, la comprobación de que el XAML para el tiempo de compilación la `HomePage` se realizará la clase y los errores se notifican como parte del proceso de compilación.

> [!NOTE]
> El `XamlCompilation` atributo y el `XamlCompilationOptions` enumeración residen en el `Xamarin.Forms.Xaml` espacio de nombres que debe importarse al usarlas.


## <a name="related-links"></a>Vínculos relacionados

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
