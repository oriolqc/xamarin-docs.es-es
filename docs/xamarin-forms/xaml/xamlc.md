---
title: Compilación de XAML en Xamarin.Forms
description: En este artículo se explica cómo XAML se puede compilar directamente en lenguaje intermedio (IL) con el compilador de Xamarin.Forms XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/22/2018
ms.openlocfilehash: de1ac47a56bf8d75eefb2ed6c7237f2f63f56ecc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105953"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilación de XAML en Xamarin.Forms

_XAML se puede compilar opcionalmente directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC)._

Compilación de XAML ofrece una serie de ventajas:

- Realiza la comprobación en tiempo de compilación de XAML, notificando al usuario de los errores.
- Reduce parte del tiempo de carga y creación de instancias para los elementos XAML.
- Facilita reducir el tamaño de archivo del ensamblado final al dejar de incluir archivos .xaml.

Compilación de XAML está deshabilitado de forma predeterminada para garantizar la compatibilidad con versiones anteriores. Puede habilitarse en el nivel de clase y ensamblado agregando la [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo.

En el ejemplo de código siguiente se muestra cómo habilitar la compilación de XAML en el nivel de ensamblado:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

En este ejemplo, se realizará la comprobación de todo el XAML contenido dentro del ensamblado de tiempo de compilación, con errores XAML que se notifica en tiempo de compilación en lugar de tiempo de ejecución. Por lo tanto, el `assembly` prefijo para el [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo especifica que el atributo se aplica a todo el ensamblado.

> [!NOTE]
> El [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo y el [ `XamlCompilationOptions` ](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) enumeración residen en el `Xamarin.Forms.Xaml` espacio de nombres que debe importarse al usarlas.

En el ejemplo de código siguiente se muestra cómo habilitar la compilación de XAML en el nivel de clase:

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
> Enlaces compilados se pueden habilitar para mejorar el rendimiento de enlace de datos en las aplicaciones de Xamarin.Forms. Para obtener más información, consulte [compilado enlaces](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Vínculos relacionados

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
