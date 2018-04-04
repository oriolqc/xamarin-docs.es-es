---
title: Controles XAML estándar (versión preliminar)
description: Cómo empezar a explorar la vista previa estándar de XAML de Xamarin.Forms
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 2fc7fb9581f344e0d54bd9f690d334eda78cc97a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-standard-preview-controls"></a>Controles XAML estándar (versión preliminar)

![Vista previa](~/media/shared/preview.png)

Esta página enumera los controles de XAML estándar disponibles en la vista previa, junto con su equivalente control Xamarin.Forms.

También es una lista de controles que tienen nombres de propiedad y enumeración nuevos en XAML estándar.

## <a name="controls"></a>Controles

|Xamarin.Forms|Estándar XAML|
|--- |--- |
|Fotograma|Borde|
|Selector de|ComboBox|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Etiqueta|TextBlock|
|Entrada|TextBox|
|Modificador|ToggleSwitch|
|ContentView|Control de usuario|


## <a name="properties-and-enumerations"></a>Propiedades y enumeraciones

|Controles de Xamarin.Forms con propiedades actualizadas|Propiedad Xamarin.Forms o enum|XAML estándar equivalente|
|--- |--- |--- |
|Botón, entrada, etiqueta, DatePicker, Editor, SearchBar, TimePicker|TextColor|Primer plano|
|VisualElement|BackgroundColor|Fondo *|
|Selector de botón|BorderColor, OutlineColor|BorderBrush|
|Botón|BorderWidth|Grosor|
|ProgressBar|Progreso|Valor|
|Botón de entrada, etiqueta, Editor, SearchBar, intervalo, la fuente,|FontAttributesBold, cursiva, ninguno|FontStyleItalic, Normal|
|Botón de entrada, etiqueta, Editor, SearchBar, intervalo, la fuente,|Atributos de fuente|FontWeights * negrita, Normal|
|InputView|KeyboardDefault, dirección Url, número, teléfono, texto, Chat, enviar por correo electrónico|InputScopeNameValue * predeterminado, dirección Url, número, TelephoneNumber, texto, Chat, EmailNameOrAddress|
|StackPanel|StackOrientation|Orientación *|

> [!IMPORTANT]
> Elementos marcados con * están incompletos en la vista previa actual

## <a name="related-links"></a>Vínculos relacionados

- [NuGet de vista previa](https://aka.ms/xf-xamlstandard-nuget)
