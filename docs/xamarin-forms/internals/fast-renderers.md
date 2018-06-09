---
title: Representadores de Xamarin.Forms Fast
description: Este artículo detallan a los representadores rápidos, que reducen la inflación y los costos de representación de un control de Xamarin.Forms en Android mediante la reducción de la jerarquía de control nativo resultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 40cc095da41aaae5cb474987d8b03f7cf4a17322
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243066"
---
# <a name="xamarinforms-fast-renderers"></a>Representadores de Xamarin.Forms Fast

_Este artículo detallan a los representadores rápidos, que reducen la inflación y los costos de representación de un control de Xamarin.Forms en Android mediante la reducción de la jerarquía de control nativo resultante._

Tradicionalmente, la mayoría de los representadores de control original en Android se compone de dos vistas:

- Nativo de control, como un `Button` o `TextView`.
- Un contenedor `ViewGroup` que controla algunos del trabajo de diseño, el control de movimiento y otras tareas.

Sin embargo, este enfoque tiene las implicaciones de rendimiento en que dos vistas se crean para cada control lógico, lo que resulta en un árbol visual más complejo que requiere más memoria y más capacidad de procesamiento para que se representen en pantalla.

Representadores rápidos reducen los costes de representación de un control de Xamarin.Forms y la inflación en una sola vista. Por lo tanto, en lugar de crear dos vistas y agregarlos al árbol de la vista, se crea uno solo. Esto mejora el rendimiento mediante la creación de menos objetos, lo que a su vez significa un árbol de vista menos complejo, y menos uso de memoria (que también se da lugar a pausas menos de la colección de elementos no utilizados).

Están disponibles para los siguientes controles en Xamarin.Forms 2.4 en Android representadores rápidos:

- [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)
- [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)
- [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)

Funcionalmente, estos representadores rápidos no son diferentes a los representadores originales. Sin embargo, se actualmente experimentales y solo puede usarse agregando la siguiente línea de código para su `MainActivity` clase antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> Representadores rápidos sólo son aplicables a la aplicación compatibilidad Android back-end, por lo que este valor se omitirá en actividades de compatibilidad de aplicación anterior.

Mejoras de rendimiento varían para cada aplicación, dependiendo de la complejidad del diseño. Por ejemplo, son posibles mejoras de rendimiento de x2 al desplazarse a través de un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que contenga miles de filas de datos, donde se realizan las celdas de cada fila de controles que usan representadores rápidos, que da como resultado visiblemente desplazamiento más suave.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
