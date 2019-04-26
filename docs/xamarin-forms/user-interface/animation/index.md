---
title: Animación de Xamarin.Forms
description: Xamarin.Forms incluye su propia infraestructura de animación es sencillo para crear animaciones sencillas, pero también lo suficientemente versátil para crear animaciones complejas.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: bebb3e32f298a2079069787dba3453e1817cf64f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158607"
---
# <a name="animation-in-xamarinforms"></a>Animación de Xamarin.Forms

_Xamarin.Forms incluye su propia infraestructura de animación es sencillo para crear animaciones sencillas, pero también lo suficientemente versátil para crear animaciones complejas._

Las clases de animación de Xamarin.Forms tienen como objetivo diferentes propiedades de elementos visuales. Una animación típica que cambia de forma progresiva una propiedad de un valor a otro durante un período de tiempo. Tenga en cuenta que no hay ninguna interfaz XAML para las clases de animación de Xamarin.Forms. Sin embargo, estas se pueden encapsular en [comportamientos](~/xamarin-forms/app-fundamentals/behaviors/index.md) y se puede hacer referencia a ellas posteriormente desde XAML.

## <a name="simple-animationssimplemd"></a>[Animaciones simples](simple.md)

La clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)proporciona métodos de extensión que pueden usarse para construir animaciones simples que giran, escalan, traducen y desvanecen instancias [`VisualElement`](xref:Xamarin.Forms.VisualElement). En este artículo se muestra cómo crear y cancelar animaciones mediante la clase `ViewExtensions`.

## <a name="easing-functionseasingmd"></a>[Funciones de aceleración ](easing.md)

Xamarin.Forms incluye la clase [`Easing`](xref:Xamarin.Forms.Easing), que permite especificar una función de transferencia que controla cómo se aceleran o ralentizan las animaciones durante su ejecución. En este artículo se muestra cómo usar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.

## <a name="custom-animationscustommd"></a>[Animaciones personalizadas](custom.md)

La clase [`Animation`](xref:Xamarin.Forms.Animation)es el bloque de creación de todas las animaciones de Xamarin.Forms. Con los métodos de extensión de la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) se pueden crar uno o varios objetos `Animation`. En este artículo se muestra cómo usar la clase `Animation` para crear y cancelar animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animadas mediante métodos existentes de animación.
