---
title: Animación
description: Xamarin.Forms incluye su propia infraestructura de animación que es sencillo para crear animaciones simples, pero también son lo suficientemente versátiles como para crear animaciones complejas.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 7cff122e7ecc24f5ad93bd863ee422981871f857
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="animation"></a>Animación

_Xamarin.Forms incluye su propia infraestructura de animación que es sencillo para crear animaciones simples, pero también son lo suficientemente versátiles como para crear animaciones complejas._

Las clases de animación de Xamarin.Forms apunta a diferentes propiedades de los elementos visuales, con una animación típica progresivamente al cambiar una propiedad de un valor a otro durante un período de tiempo. Tenga en cuenta que no hay ninguna interfaz XAML para las clases de animación de Xamarin.Forms. Sin embargo, se pueden encapsular animaciones en [comportamientos](~/xamarin-forms/app-fundamentals/behaviors/index.md) y posteriormente ser referenciadas desde XAML.

## <a name="simple-animationssimplemd"></a>[Animaciones simples](simple.md)

La clase [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) proporciona métodos de extensión que pueden usarse para construir las animaciones simples que giran, escalan, traducen y desvanecen instancias [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/). Este artículo muestra cómo crear y cancelar las animaciones mediante la clase `ViewExtensions`.

## <a name="easing-functionseasingmd"></a>[Funciones de aceleración ](easing.md)

Xamarin.Forms incluye la clase [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) que permite especificar una función de transferencia, que controla cómo se aceleran las animaciones o reducen la velocidad tal y como se está ejecutando. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.

## <a name="custom-animationscustommd"></a>[Animaciones personalizadas](custom.md)

La clase [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) es el bloque de creación de todas las animaciones de Xamarin.Forms, con los métodos de extensión de la clase [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) creando uno o más objetos `Animation`. Este artículo muestra cómo utilizar la `Animation` clase para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animados por los métodos existentes de la animación.

