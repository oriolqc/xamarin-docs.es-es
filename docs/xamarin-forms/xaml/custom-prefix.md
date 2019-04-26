---
title: XAML Namespace recomienda prefijos en Xamarin.Forms
description: La clase XmlnsPrefixAttribute puede usarse por los autores de controles para especificar un prefijo recomendado para asociar a un espacio de nombres XAML para el uso XAML.
ms.prod: xamarin
ms.assetid: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
ms.openlocfilehash: 33f18b3f9c9ddb6ab31ca92e2f192ffad783ec0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075108"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>XAML Namespace recomienda prefijos en Xamarin.Forms

La `XmlnsPrefixAttribute` clase puede ser usada por los autores de controles para especificar un prefijo recomendado para asociar a un espacio de nombres XAML para el uso XAML. El prefijo es útil cuando se admiten la serialización del árbol de objetos para XAML, o al interactuar con un entorno de diseño que tiene características de edición de XAML. Por ejemplo:

- Editores de texto XAML podrían utilizar el `XmlnsPrefixAttribute` como una sugerencia para un espacio de nombres XAML inicial `xmlns` asignación.
- Los entornos de diseño XAML podrían utilizar el `XmlnsPrefixAttribute` se agreguen asignaciones para el XAML al arrastrar objetos fuera de un cuadro de herramientas y a una superficie de diseño visual.

Recomienda que los prefijos de espacio de nombres deben definirse en el nivel de ensamblado con el `XmlnsPrefixAttribute` constructor, que toma dos argumentos: una cadena que especifica el identificador de un espacio de nombres XAML y una cadena que especifica un prefijo recomendado:

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

Los prefijos deben utilizar las cadenas cortas, porque el prefijo se aplica normalmente a todos los elementos serializados que proceden del espacio de nombres XAML. Por lo tanto, la longitud de cadena del prefijo puede tener un efecto notable en el tamaño de la salida XAML serializada.

> [!NOTE]
> Más de un `XmlnsPrefixAttribute` pueden aplicarse a un ensamblado. Por ejemplo, si tiene un ensamblado que define los tipos de más de un espacio de nombres XAML, podría definir los valores de prefijo diferente para cada espacio de nombres XAML.

## <a name="related-links"></a>Vínculos relacionados

- [Esquemas de espacio de nombres personalizado XAML](custom-namespace-schemas.md)
- [Espacios de nombres XAML en Xamarin.Forms](namespaces.md)
