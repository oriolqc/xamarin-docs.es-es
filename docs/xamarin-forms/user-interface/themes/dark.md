---
title: Tema oscuro de Xamarin.Forms
description: En este artículo se explica cómo consumir el tema oscuro de Xamarin.Forms en una aplicación.
ms.prod: xamarin
ms.assetid: 43A3798D-6F05-4734-AF5E-97235B46D9B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 1fc329f506afde04b0dc59dc637d999865aafbe1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186347"
---
# <a name="xamarinforms-dark-theme"></a>Tema oscuro de Xamarin.Forms

![](~/media/shared/preview.png "Esta API está actualmente en versión preliminar")

> [!NOTE]
> Los temas requieren la versión preliminar de Xamarin.Forms 2.3. Compruebe el [sugerencias para solucionar problemas](~/xamarin-forms/user-interface/themes/index.md) si se producen errores.

Para usar el tema oscuro:

## <a name="1-add-nuget-packages"></a>1. Agregar paquetes de Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Dark

## <a name="2-add-to-the-resource-dictionary"></a>2. Agregar al diccionario de recursos

En el **App.xaml** archivo agregar un nuevo personalizado `xmlns` para el tema y, a continuación, asegúrese de los recursos del tema se combinan con el diccionario de recursos de la aplicación.
Un ejemplo del archivo XAML se muestra a continuación:

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:dark="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Dark">
    <Application.Resources>
        <ResourceDictionary MergedWith="dark:DarkThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3. Carga las clases de tema

Siga este [solución de problemas paso](~/xamarin-forms/user-interface/themes/index.md) y agregue el código necesario en los proyectos de aplicación de Android y iOS.

## <a name="4-use-styleclass"></a>4. Usar StyleClass

Este es un ejemplo de botones y etiquetas en el tema oscuro, junto con el marcado que los genera.

[![](dark-images/dark-theme-sml.png "Los botones y etiquetas en el tema oscuro")](dark-images/dark-theme.png#lightbox "botones y etiquetas en el tema oscuro")

```xaml
<StackLayout Padding="20">
    <Button Text="Button Default" />
    <Button Text="Button Class Default" StyleClass="Default" />
    <Button Text="Button Class Primary" StyleClass="Primary" />
    <Button Text="Button Class Success" StyleClass="Success" />
    <Button Text="Button Class Info" StyleClass="Info" />
    <Button Text="Button Class Warning" StyleClass="Warning" />
    <Button Text="Button Class Danger" StyleClass="Danger" />
    <Button Text="Button Class Link" StyleClass="Link" />

    <Button Text="Button Class Default Small" StyleClass="Small" />
    <Button Text="Button Class Default Large" StyleClass="Large" />
</StackLayout>
```

El [lista completa de las clases integradas](~/xamarin-forms/user-interface/themes/index.md) muestra qué estilos están disponibles para algunos controles comunes.
