---
title: "Estándar XAML (versión preliminar)"
description: "Cómo empezar a explorar la vista previa estándar de XAML de Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 3214a76ee3f976f5dd2afb28edde07100db5a7a1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-standard-preview"></a>Estándar XAML (versión preliminar)

![Vista previa](~/media/shared/preview.png)

Siga estos pasos para experimentar con el estándar de XAML de Xamarin.Forms:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Descargue el [obtener una vista previa de paquete de NuGet aquí](https://aka.ms/xf-xamlstandard-nuget).
2. Agregar el **Xamarin.Forms.Alias** paquete NuGet para los proyectos de Xamarin.Forms PCL, estándar de .NET y plataforma.
3. Inicializar el paquete con `Alias.Init()`
4. Agregar un `xmlns:a` referencia `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Utilizar los tipos en XAML, vea el [referencia de controles](controls.md) para obtener más información.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Descargue el [obtener una vista previa de paquete de NuGet aquí](https://aka.ms/xf-xamlstandard-nuget).
2. Agregar el **Xamarin.Forms.Alias** paquete NuGet para los proyectos de Xamarin.Forms PCL, estándar de .NET y plataforma.
3. Inicializar el paquete con `Alias.Init()`
4. Agregar un `xmlns:a` referencia `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Utilizar los tipos en XAML, vea el [referencia de controles](controls.md) para obtener más información.

-----

El XAML siguiente muestra algunos controles estándar de XAML que se va a usar en un Xamarin.Forms `ContentPage`:

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
> **Nota:** que requieren el xmlns `a:` prefijo en los controles estándar de XAML es una limitación de la vista previa actual.


## <a name="related-links"></a>Vínculos relacionados

- [NuGet de vista previa](https://aka.ms/xf-xamlstandard-nuget)
- [Referencia de controles](controls.md)
