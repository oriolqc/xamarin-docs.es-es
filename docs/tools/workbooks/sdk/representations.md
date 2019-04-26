---
title: Representaciones en Xamarin Workbooks
description: Este documento describe la canalización de representación de Xamarin Workbooks, lo que permite el procesamiento de resultados completos para cualquier código que devuelve un valor.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: d9aafbe13e06875b6577a4d2308e419932fd1589
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382118"
---
# <a name="representations-in-xamarin-workbooks"></a>Representaciones en Xamarin Workbooks

## <a name="representations"></a>Representaciones

Dentro de una sesión de libro o el inspector, el código que se ejecuta y produce un resultado (por ejemplo, un método que devuelve un valor o el resultado de una expresión) se procesa a través de la canalización de representación en el agente. Todos los objetos, a excepción de los tipos primitivos como enteros, se reflejarán para generar gráficos de miembro interactivo y pasará a través de un proceso para proporcionar representaciones alternativas que el cliente puede representar más enriquecido. Objetos de cualquier tamaño y profundidad de forma segura se admiten (incluidos los ciclos y enumerables infinito) debido a la comunicación remota y reflexión diferida e interactiva.

Xamarin Workbooks proporciona algunos tipos comunes a todos los agentes y los clientes que permiten la representación enriquecidas de resultados. [`Color`][xir-color] es un ejemplo de este tipo, donde ejemplo en iOS, el agente es responsable de convertir `CGColor` o `UIColor` objetos en un `Xamarin.Interactive.Representations.Color` objeto.

Además de representaciones comunes, el SDK de integración proporciona API para serializar las representaciones personalizadas en el agente y la representación de las representaciones en el cliente.

## <a name="external-representations"></a>Representaciones externas

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] proporciona la capacidad de registrar un [`RepresentationProvider`][repp], que debe implementar una integración para convertir de un objeto arbitrario a un formulario independiente para representar. Estas formas independientes deben implementar la [`ISerializableObject`][serobj] interfaz.

Implementar el `ISerializableObject` interfaz agrega un método Serialize que precisamente controla cómo se serializan los objetos. El `Serialize` método espera que un desarrollador a especificar exactamente qué propiedades son serializarse y cuál será el nombre final. Examinando el `Person` objeto en nuestra [`KitchenSink` ejemplo] [ejemplo], podemos ver cómo funciona esto:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Si hubiera querido proporcionar un superconjunto o un subconjunto de propiedades del objeto original, podemos hacer esto con `Serialize`. Por ejemplo, podríamos hacer algo parecido a esto para proporcionar un previamente calculado `Age` propiedad `Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> Las API que generan `ISerializableObject` directamente los objetos no es necesario para que lo administre un `RepresentationProvider`. Si el objeto que desea mostrar es **no** una `ISerializableObject`, desea controlar encapsularlo en su `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Una representación en forma de representación

Los representadores se implementan en JavaScript y tendrán acceso a una versión de JavaScript del objeto que representa a través de `ISerializableObject`. La copia de JavaScript también tendrá una `$type` propiedad de cadena que indica el nombre de tipo. NET.

Se recomienda usar TypeScript para código de integración de cliente, que se compila por supuesto en vanilla JavaScript. En cualquier caso, el SDK proporciona [typings][typings] que puede al que hace referencia directamente TypeScript o referencia al mismo simplemente manualmente si escribiendo vainilla JavaScript es preferible.

El punto de integración principal para la representación es `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

En este caso, `PersonRenderer` implementa el `Renderer` interfaz. Consulte la [typings][typings] para obtener más detalles.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
