---
title: Controles XAML estándar (versión preliminar)
description: Este artículo explora los controles XAML estándar disponibles en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 1b01d0773f0c2150db575875b770957eb6452f41
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38843948"
---
# <a name="xaml-standard-preview-controls"></a>Controles XAML estándar (versión preliminar)

![Vista previa](~/media/shared/preview.png)

Esta página enumera los controles XAML estándar disponibles en la vista previa, junto con su control equivalente de Xamarin.Forms.

También hay una lista de los controles que tienen los nuevos nombres de propiedad y enumeración en XAML estándar.

## <a name="controls"></a>Controles

|Xamarin.Forms|XAML estándar|
|--- |--- |
|Fotograma|Borde|
|Selector|ComboBox|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Etiqueta|TextBlock|
|Entrada|TextBox|
|Modificador|ToggleSwitch|
|ContentView|Control de usuario|


## <a name="properties-and-enumerations"></a>Las propiedades y enumeraciones

|Controles de Xamarin.Forms con las propiedades actualizadas|Propiedad Xamarin.Forms o enumeración|XAML estándar equivalente|
|--- |--- |--- |
|Botón, entrada, etiqueta, DatePicker, Editor, SearchBar, TimePicker|TextColor|Primer plano|
|VisualElement|BackgroundColor|En segundo plano *|
|Selector de botón|BorderColor, OutlineColor|BorderBrush|
|Botón|BorderWidth|BorderThickness|
|ProgressBar|Progreso|Valor|
|Botón, entrada, etiqueta, Editor, SearchBar, intervalo, fuente|FontAttributesBold, cursiva, ninguno|FontStyleItalic, Normal|
|Botón, entrada, etiqueta, Editor, SearchBar, intervalo, fuente|Atributos de fuente|FontWeights * Normal, en negrita|
|InputView|KeyboardDefault, dirección Url, número, telefónica, texto, Chat, enviar por correo electrónico|InputScopeNameValue * predeterminado, dirección Url, número, TelephoneNumber, texto, Chat, EmailNameOrAddress|
|StackPanel|StackOrientation|Orientación *|

> [!IMPORTANT]
> Los elementos marcados con * están incompletos en la vista previa actual

## <a name="related-links"></a>Vínculos relacionados

- [NuGet de versión preliminar](https://aka.ms/xf-xamlstandard-nuget)
