---
ms.openlocfilehash: 8957a75ac693874bfbea738d17fbdd7e013c3199
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277458"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **StackLayoutTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **StackLayoutTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **StackLayoutTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en tres instancias de [`Label`](xref:Xamarin.Forms.Label) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). El elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) coloca sus vistas secundarias (las instancias de `Label`) en una misma línea, que tiene de forma predeterminada una orientación vertical. Además, la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posición de representación del `StackLayout` en [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Además de la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin), las propiedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) y [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) también se pueden establecer en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). El valor de la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica la distancia entre las vistas en `StackLayout`, mientras que el valor de la propiedad [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) especifica la cantidad de espacio entre cada elemento secundario de `StackLayout`. Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de vistas secundarias en un elemento StackLayout en iOS y Android](../images/create-stacklayout.png "Elemento StackLayout que contiene instancias de etiquetas")](../images/create-stacklayout-large.png#lightbox "Elemento StackLayout que contiene instancias de etiquetas")

    Para obtener más información sobre [`StackLayout`](xref:Xamarin.Forms.StackLayout), vea [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **StackLayoutTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **StackLayoutTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **StackLayoutTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en tres instancias de [`Label`](xref:Xamarin.Forms.Label) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). El elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) coloca sus vistas secundarias (las instancias de `Label`) en una misma línea, que tiene de forma predeterminada una orientación vertical. Además, la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posición de representación del `StackLayout` en [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Además de la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin), las propiedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) y [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) también se pueden establecer en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). El valor de la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica la distancia entre las vistas en `StackLayout`, mientras que el valor de la propiedad [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) especifica la cantidad de espacio entre cada elemento secundario de `StackLayout`. Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de vistas secundarias en un elemento StackLayout en iOS y Android](../images/create-stacklayout.png "Elemento StackLayout que contiene instancias de etiquetas")](../images/create-stacklayout-large.png#lightbox "Elemento StackLayout que contiene instancias de etiquetas")

    Para obtener más información sobre [`StackLayout`](xref:Xamarin.Forms.StackLayout), vea [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).
