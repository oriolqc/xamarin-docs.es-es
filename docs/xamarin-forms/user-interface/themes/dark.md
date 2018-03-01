---
title: Tema oscuro
ms.topic: article
ms.prod: xamarin
ms.assetid: 43A3798D-6F05-4734-AF5E-97235B46D9B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0b3a714e8295ed60f201c202fbe30e45dbdcc205
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="dark-theme"></a>Tema oscuro

![](~/media/shared/preview.png "Esta API está actualmente en vista previa")

> [!NOTE]
> Los temas requieren la versión de vista previa de Xamarin.Forms 2.3. Compruebe el [sugerencias para solucionar problemas](~/xamarin-forms/user-interface/themes/index.md) si se producen errores.

Para utilizar el tema oscuro:

## <a name="1-add-nuget-packages"></a>1. Agregar paquetes de Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Dark

## <a name="2-add-to-the-resource-dictionary"></a>2. Agregar al diccionario de recursos

En el **App.xaml** archivo agrega un nuevo personalizado `xmlns` para el tema y, a continuación, asegúrese de recursos del tema se combinan con el diccionario de recursos de la aplicación.
A continuación se muestra un ejemplo del archivo XAML:

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:dark="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Dark">
    <Application.Resources>
        <ResourceDictionary MergedWith="dark:DarkThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3. Clases de tema de carga

Siga este [solución de problemas de paso](~/xamarin-forms/user-interface/themes/index.md) y agregue el código necesario en los proyectos de aplicación de Android y iOS.

## <a name="4-use-styleclass"></a>4. Usar StyleClass

Este es un ejemplo de botones y etiquetas en el tema oscuro, junto con el marcado que se genera.

[ ![](dark-images/dark-theme-sml.png "Botones y etiquetas en el tema oscuro")](dark-images/dark-theme.png "botones y etiquetas en el tema oscuro")

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

El [lista completa de clases integradas](~/xamarin-forms/user-interface/themes/index.md) muestra qué estilos están disponibles para algunos de los controles comunes.

