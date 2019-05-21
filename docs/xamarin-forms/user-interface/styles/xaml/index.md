---
title: Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML
description: Esta guía explica cómo personalizar la apariencia de una aplicación de Xamarin.Forms con estilos XAML.
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: adafcb85f4e3d3bd93f7b2afe69c0ca0bb37f3d0
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926606"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

A menudo, las aplicaciones de Xamarin.Forms contienen varios controles que tienen una apariencia idéntica. Establecer la apariencia de cada control individual puede ser repetitiva y propensas a errores. En su lugar, se pueden crear estilos que personalizar la apariencia del control mediante la agrupación y establecer las propiedades disponibles en el tipo de control.

## <a name="explicit-stylesexplicitmd"></a>[Estilos explícitos](explicit.md)

Un *explícita* estilo es aquel que se aplica de manera selectiva a los controles estableciendo sus [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

## <a name="implicit-stylesimplicitmd"></a>[Estilos implícitos](implicit.md)

Un *implícita* estilo es aquella que se usa por todos los controles del mismo [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), sin necesidad de cada control para hacer referencia al estilo.

## <a name="global-stylesapplicationmd"></a>[Estilos globales](application.md)

Los estilos pueden estar disponibles globalmente agregándolos a la aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Esto ayuda a evitar la duplicación de estilos a través de las páginas o controles.

## <a name="style-inheritanceinheritancemd"></a>[Herencia de estilo](inheritance.md)

Los estilos pueden heredar de otros estilos para reducir la duplicación y habilitar la reutilización.

## <a name="dynamic-stylesdynamicmd"></a>[Estilos dinámicos](dynamic.md)

Los estilos no responder a los cambios de propiedad y permanecen sin cambios para la duración de una aplicación. Sin embargo, las aplicaciones pueden responder a cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos.

## <a name="device-stylesdevicemd"></a>[Estilos de dispositivo](device.md)

Xamarin.Forms incluye seis *dinámica* estilos, conocidos como *dispositivo* estilos, en el [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) clase. Todos los seis estilos pueden aplicarse a [ `Label` ](xref:Xamarin.Forms.Label) solo instancias.

## <a name="style-classesstyle-classmd"></a>[Clases de estilo](style-class.md)

Las clases de estilo de Xamarin.Forms permiten varios estilos para aplicarse a un control, sin tener que recurrir a la herencia de estilo.
