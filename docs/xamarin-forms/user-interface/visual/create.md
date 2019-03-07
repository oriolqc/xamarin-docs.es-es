---
title: Crear a un representador Xamarin.Forms Visual
description: Xamarin.Forms Visual permite que los representadores de forma selectiva se aplique a objetos VisualElement, sin necesidad de controles de Xamarin.Forms subclase.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 3c614bcd0044a0aa4a698c830d2f2af5aba6c65a
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557517"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms Visual

Xamarin.Forms Visual permite que los representadores de forma selectiva se aplique a `VisualElement` objetos, sin necesidad de controles de Xamarin.Forms subclase. Los representadores que especifican un `VisualType` como parte de la `ExportRendererAttribute` se usará para las vistas de representador, en lugar de los representadores de forma predeterminada. En tiempo de selección del representador, el `Visual` propiedad de la vista es inspeccionar y se incluye en el proceso de selección del representador.

> [!IMPORTANT]
> Actualmente la `Visual` no se puede cambiar después de que el control se ha representado, pero esto cambiará en futuras versiones.

Xamarin.Forms incluye una apariencia visual según el diseño de materiales. Para obtener más información, consulte [Xamarin.Forms Material Visual](material-visual.md).

## <a name="create-a-visual"></a>Crear un objeto Visual

En la biblioteca multiplataforma, creación de un tipo que derive de `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

## <a name="register-the-visual-type"></a>Registrar el tipo de objeto Visual

Crear un representador para la vista necesaria y registrar el tipo de objeto Visual como parte de las plataformas `ExportRenderAttribute`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ContentPage), typeof(CustomPageRenderer), new[] { typeof(CustomVisual) })]
namespace MyApp.Android
{
    public class CustomPageRenderer : PageRenderer
    {
        ...
    }
}
```

## <a name="consume-the-visual"></a>Usar el objeto Visual

Las aplicaciones pueden participar en el uso del objeto Visual personalizado a través de la `Visual` propiedad:

```xaml
<ContentPage ...
             Visual="Custom">
    ...
</ContentPage>
```

El código de C# equivalente es:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = new CustomVisual();
```

## <a name="register-a-name-for-a-visual"></a>Registrar un nombre de un objeto Visual

Pueden producirse conflictos entre diferentes bibliotecas Visual o quizás tan solo quiere hacer referencia a un objeto Visual mediante un nombre diferente. En este escenario, puede usar un atributo de ensamblado para registrar un nombre diferente para un objeto Visual determinado:

```csharp
[assembly: Visual("MyMaterialName", typeof(VisualMarker.MaterialVisual))]
```

A continuación, se puede utilizar el objeto Visual personalizado en su nombre registrado:

```xaml
<ContentPage ...
             Visual="MyMaterialName">
    ...
</ContentPage>
````

## <a name="related-links"></a>Vínculos relacionados

- [Material de Xamarin.Forms Visual](material-visual.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
