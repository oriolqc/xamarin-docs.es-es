---
title: Representaciones en los libros de Xamarin
description: Este documento describe la canalización de representación de los libros de Xamarin, que permite la representación de resultados completos para cualquier código que devuelve un valor.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: d4d8fa164b9f52e2c5331aa2c08fdddf232572d4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794168"
---
# <a name="representations-in-xamarin-workbooks"></a>Representaciones en los libros de Xamarin

## <a name="representations"></a>representaciones

Dentro de una sesión de libro o el inspector, el código que se ejecuta y produce un resultado (por ejemplo, un método que devuelve un valor o el resultado de una expresión) se procesa a través de la canalización de representación en el agente. Todos los objetos, a excepción de primitivos, como números enteros, se reflejarán para generar gráficos de miembro interactivo y pasará a través de un proceso para proporcionar representaciones alternativas que el cliente puede representar con más detalle. Objetos de cualquier tamaño y la profundidad se admiten con seguridad (incluidos los ciclos y enumerables infinito) debido a la comunicación remota y reflexión diferida e interactivo.

Los libros de Xamarin proporciona unos tipos comunes a todos los agentes y los clientes que permiten enriquecidos para la representación de los resultados. [`Color`][xir-color] es un ejemplo de este tipo, donde por ejemplo en iOS, el agente es responsable de convertir `CGColor` o `UIColor` objetos en un `Xamarin.Interactive.Representations.Color` objeto.

Además de representaciones comunes, la integración de SDK proporciona API para serializar representaciones personalizadas en el agente y la representación representaciones en el cliente.

## <a name="external-representations"></a>Representaciones externas

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] proporciona la capacidad de registrar un [`RepresentationProvider`][repp], que debe implementar una integración para convertir de un objeto arbitrario a un formulario independiente para representar. Estas formas independientes deben implementar la [`ISerializableObject`][serobj] interfaz.

Implementar el `ISerializableObject` interfaz agrega un método de serialización que precisamente controla cómo se serializan los objetos. El `Serialize` método espera que un desarrollador exactamente especificará qué propiedades se van a serializar y cuál será el nombre final. Examinando el `Person` objeto en nuestro [`KitchenSink` ejemplo] [ejemplo], podemos ver cómo funciona esto:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Si desea proporcionar un supraconjunto ni un subconjunto de propiedades del objeto original, podemos hacer esto con `Serialize`. Por ejemplo, podríamos hacer algo parecido a esto para proporcionar un precalculadas `Age` propiedad `Person`:

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
> Las API que generan `ISerializableObject` objetos directamente no necesitan ser controlados por un `RepresentationProvider`. Si el objeto que desea mostrar es **no** una `ISerializableObject`, deseará controlar incluyéndolo en su `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Una representación en forma de representación

Representadores se implementan en JavaScript y tendrán acceso a una versión de JavaScript del objeto representado mediante `ISerializableObject`. La copia de JavaScript también tendrá un `$type` propiedad de cadena que indica el nombre de tipo. NET.

Se recomienda utilizar TypeScript para código de integración de cliente, que por supuesto se compila a vainilla JavaScript. En cualquier caso, el SDK proporciona [typings][typings] que puede al que hace referencia directamente TypeScript o referencia al mismo simplemente manualmente si escribiendo vainilla JavaScript es preferible.

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
