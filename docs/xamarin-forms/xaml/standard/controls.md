---
title: "Controles XAML estándar (versión preliminar)"
description: "Cómo empezar a explorar la vista previa estándar de XAML de Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 3f30a77975a9f42380ecf7efd73426763ec83ef0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-standard-preview-controls"></a>Controles XAML estándar (versión preliminar)

![Vista previa](~/media/shared/preview.png)

Esta página enumera los controles de XAML estándar disponibles en la vista previa, junto con su equivalente control Xamarin.Forms.

También es una lista de controles que tienen nombres de propiedad y enumeración nuevos en XAML estándar.

## <a name="controls"></a>Controles

<table style="width:300px">
  <tr><th>Xamarin.Forms</th><th>Estándar XAML</th></tr>
  <tr><td>Fotograma</td><td>Borde</td></tr>
  <tr><td>Selector de</td><td>ComboBox</td></tr>
  <tr><td>ActivityIndicator</td><td>ProgressRing</td></tr>
  <tr><td>StackLayout</td><td>StackPanel</td></tr>
  <tr><td>Etiqueta</td><td>TextBlock</td></tr>
  <tr><td>Entrada</td><td>TextBox</td></tr>
  <tr><td>Modificador</td><td>ToggleSwitch</td></tr>
  <tr><td>ContentView</td><td>Control de usuario</td></tr>
</table>

## <a name="properties-and-enumerations"></a>Propiedades y enumeraciones

<table>
  <tr><th>Xamarin.Forms<br/>Controles con propiedades actualizadas</th><th>Xamarin.Forms<br/>Propiedad o Enum</th><th>Estándar XAML<br/>Equivalente</th></tr>
  <tr><td>Botón, entrada, etiqueta, DatePicker, Editor, SearchBar, TimePicker</td><td>TextColor</td><td>Primer plano</td></tr>
  <tr><td>VisualElement</td><td>BackgroundColor</td><td><i>Fondo *</i></td></tr>
  <tr><td>Selector de botón</td><td>BorderColor, OutlineColor</td><td>BorderBrush</td></tr>
  <tr><td>Botón</td><td>BorderWidth</td><td>Grosor</td></tr>
  <tr><td>ProgressBar</td><td>Progreso</td><td>Valor</td></tr>
  <tr><td>Botón de entrada, etiqueta, Editor, SearchBar, intervalo, la fuente,</td><td>Atributos de fuente<br/>Negrita, cursiva, ninguno</td><td>FontStyle<br/>Italic, Normal</td></tr>
  <tr><td>Botón de entrada, etiqueta, Editor, SearchBar, intervalo, la fuente,</td><td>Atributos de fuente</td><td><i>FontWeights *</i><br/>Bold, Normal</td></tr>
  <tr><td>InputView</td><td>Teclado<br/>De forma predeterminada, dirección Url, número, teléfono, texto, Chat, correo electrónico</td><td><i>InputScopeNameValue *</i><br/>De forma predeterminada, dirección Url, número, TelephoneNumber, texto, Chat, EmailNameOrAddress</td></tr>
  <tr><td>StackPanel</td><td>StackOrientation</td><td><i>Orientación *</i></td></tr>
</table>

> [!IMPORTANT]
> Elementos marcados con * están incompletos en la vista previa actual


## <a name="related-links"></a>Vínculos relacionados

- [NuGet de vista previa](https://aka.ms/xf-xamlstandard-nuget)
