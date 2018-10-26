---
title: XAML estándar (versión preliminar)
description: En este artículo se explica cómo empezar a trabajar con la exploración de la versión preliminar estándar de XAML en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 4e65f31d76d9540bed6110198d7cafaab9fe78f5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114904"
---
# <a name="xaml-standard-preview"></a>XAML estándar (versión preliminar)

![Vista previa](~/media/shared/preview.png)

Siga estos pasos para experimentar con el estándar de XAML en Xamarin.Forms:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Descargue el [obtener una vista previa de los paquetes de NuGet aquí](https://aka.ms/xf-xamlstandard-nuget).
2. Agregar el **Xamarin.Forms.Alias** paquete NuGet a los proyectos de Xamarin.Forms .NET Standard y plataforma.
3. Inicializar el paquete con `Alias.Init()`
4. Agregar un `xmlns:a` referencia `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Usar los tipos de XAML, consulte el [referencia controles](controls.md) para obtener más información.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Descargue el [obtener una vista previa de los paquetes de NuGet aquí](https://aka.ms/xf-xamlstandard-nuget).
2. Agregar el **Xamarin.Forms.Alias** paquete NuGet a los proyectos de Xamarin.Forms .NET Standard y plataforma.
3. Inicializar el paquete con `Alias.Init()`
4. Agregar un `xmlns:a` referencia `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Usar los tipos de XAML, consulte el [referencia controles](controls.md) para obtener más información.

-----

El siguiente XAML muestra algunos controles XAML estándar que se usan en un objeto Xamarin.Forms `ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> Requerir xmlns `a:` prefijo en los controles estándar de XAML es una limitación de la vista previa actual.


## <a name="related-links"></a>Vínculos relacionados

- [NuGet de versión preliminar](https://aka.ms/xf-xamlstandard-nuget)
- [Controls Reference](controls.md) (Referencia de controles)
