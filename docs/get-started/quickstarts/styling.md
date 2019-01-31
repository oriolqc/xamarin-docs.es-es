---
title: Estilo de una aplicación de Xamarin.Forms multiplataforma
description: Este artículo explica cómo diseñar una aplicación de Xamarin.Forms multiplataforma con estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 56f8632c9cc7d170b4c6594fd51e6aa3e92ad02b
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293341"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Aplicar estilo a una aplicación de Xamarin.Forms multiplataforma

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

En este tutorial, obtendrá información sobre cómo:

- Aplicar estilo a una aplicación de Xamarin.Forms con estilos XAML.

La Guía de inicio rápido le guía en una aplicación de Xamarin.Forms multiplataforma de estilo con estilos XAML. A continuación se muestra la aplicación final:

[![](styling-images/screenshots1-sml.png "Notas de la página")](styling-images/screenshots1.png#lightbox "notas de la página")
[![](styling-images/screenshots2-sml.png "tenga en cuenta la página de entrada")](styling-images/screenshots2.png#lightbox "Nota Página de entrada")

### <a name="prerequisites"></a>Requisitos previos

Debe completar correctamente el [inicio rápido anterior](database.md) antes de intentar este inicio rápido. Como alternativa, descargue el [anterior ejemplo de tutorial rápido](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/) y usarlo como punto de partida para este inicio rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución de notas.

2. En **el Explorador de soluciones**, en el **notas** del proyecto, haga doble clic en **App.xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Este código define un [ `Thickness` ](xref:Xamarin.Forms.Thickness) valor, una serie de [ `Color` ](xref:Xamarin.Forms.Color) valores y los estilos implícitos para el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) y [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Observe que estos estilos, que se encuentran en el nivel de aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), pueden utilizarse en toda la aplicación. Para obtener más información acerca de la aplicación de estilos de XAML, vea [estilo](deepdive.md#styling) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **App.xaml** presionando **CTRL+S**y cierre el archivo.

3. En **el Explorador de soluciones**, en el **notas** del proyecto, haga doble clic en **NotesPage.xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código agrega un estilo implícito para la [ `ListView` ](xref:Xamarin.Forms.ListView) para el nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y establece el `ListView.Margin` propiedad a un valor definido en el nivel de aplicación `ResourceDictionary` . Tenga en cuenta que el `ListView` estilo implícito se agregó en el nivel de página `ResourceDictionary`, ya que sólo es usado por el `NotesPage`. Para obtener más información acerca de la aplicación de estilos de XAML, vea [estilo](deepdive.md#styling) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NotesPage.xaml** presionando **CTRL+S**y cierre el archivo.

5. En el **panel de solución**, en el **notas** del proyecto, haga doble clic en **NoteEntryPage.xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Este código agrega los estilos implícitos para el [ `Editor` ](xref:Xamarin.Forms.Editor) y [ `Button` ](xref:Xamarin.Forms.Button) vistas en el nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y establece el `StackLayout.Margin` propiedad a un valor definido en el nivel de aplicación `ResourceDictionary`. Tenga en cuenta que el `Editor` y `Button` se agregaron los estilos implícitos en el nivel de página `ResourceDictionary`, ya que solo consumen el `NoteEntryPage`. Para obtener más información acerca de la aplicación de estilos de XAML, vea [estilo](deepdive.md#styling) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NoteEntryPage.xaml** presionando **CTRL+S**y cierre el archivo.

6. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, consulte [crear la Guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** presione el **+** botón para navegar a la **NoteEntryPage** y escriba una nota. En cada página, observe cómo ha cambiado el estilo desde el inicio rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto de notas.

2. En el **panel de solución**, en el **notas** del proyecto, haga doble clic en **App.xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Este código define un [ `Thickness` ](xref:Xamarin.Forms.Thickness) valor, una serie de [ `Color` ](xref:Xamarin.Forms.Color) valores y los estilos implícitos para el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) y [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Observe que estos estilos, que se encuentran en el nivel de aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), pueden utilizarse en toda la aplicación. Para obtener más información acerca de la aplicación de estilos de XAML, vea [estilo](deepdive.md#styling) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **App.xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

3. En **el Explorador de soluciones**, en el **notas** del proyecto, haga doble clic en **NotesPage.xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código agrega un estilo implícito para la [ `ListView` ](xref:Xamarin.Forms.ListView) para el nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y establece el `ListView.Margin` propiedad a un valor definido en el nivel de aplicación `ResourceDictionary` . Tenga en cuenta que el `ListView` estilo implícito se agregó en el nivel de página `ResourceDictionary`, ya que sólo es usado por el `NotesPage`. Para obtener más información acerca de la aplicación de estilos de XAML, vea [estilo](deepdive.md#styling) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NotesPage.xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

5. En **el Explorador de soluciones**, en el **notas** del proyecto, haga doble clic en **NoteEntryPage.xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Este código agrega los estilos implícitos para el [ `Editor` ](xref:Xamarin.Forms.Editor) y [ `Button` ](xref:Xamarin.Forms.Button) vistas en el nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y establece el `StackLayout.Margin` propiedad a un valor definido en el nivel de aplicación `ResourceDictionary`. Tenga en cuenta que el `Editor` y `Button` se agregaron los estilos implícitos en el nivel de página `ResourceDictionary`, ya que solo consumen el `NoteEntryPage`. Para obtener más información acerca de la aplicación de estilos de XAML, vea [estilo](deepdive.md#styling) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NoteEntryPage.xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

6. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, consulte [crear la Guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** presione el **+** botón para navegar a la **NoteEntryPage** y escriba una nota. En cada página, observe cómo ha cambiado el estilo desde el inicio rápido anterior.

::: zone-end


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

- Aplicar estilo a una aplicación de Xamarin.Forms con estilos XAML.

Para obtener más información sobre los aspectos básicos del desarrollo de aplicaciones con Xamarin.Forms, continúe con el análisis detallado de inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](deepdive.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
