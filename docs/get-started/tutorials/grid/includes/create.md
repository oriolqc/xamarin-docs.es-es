---
ms.openlocfilehash: 28e10d1a2404d70d5329936e9bfeeea032a6fe99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61375994"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **GridTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **GridTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **GridTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Label`](xref:Xamarin.Forms.Label) en un [`Grid`](xref:Xamarin.Forms.Grid). De forma predeterminada, `Grid` coloca las vistas secundarias en una única ubicación. Por lo tanto, un elemento `Grid` que contiene varios elementos secundarios tiene que especificar columnas y filas, lo que se explicará en el próximo ejercicio. Además, la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posición de representación del `Grid` en [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Además de la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin), la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) también se puede establecer en un elemento [`Grid`](xref:Xamarin.Forms.Grid). El valor de la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica la distancia entre las vistas en el elemento `Grid`. Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de una etiqueta en una cuadrícula en iOS y Android](../images/create-grid.png "Cuadrícula que contiene una etiqueta")](../images/create-grid-large.png#lightbox "Cuadrícula que contiene una etiquetas")

    Para obtener más información sobre [`Grid`](xref:Xamarin.Forms.Grid), vea [Cuadrícula de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **GridTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **GridTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **GridTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Label`](xref:Xamarin.Forms.Label) en un [`Grid`](xref:Xamarin.Forms.Grid). De forma predeterminada, `Grid` coloca las vistas secundarias en una única ubicación. Por lo tanto, un elemento `Grid` que contiene varios elementos secundarios tiene que especificar columnas y filas, lo que se explicará en el próximo ejercicio. Además, la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posición de representación del `Grid` en [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Además de la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin), la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) también se puede establecer en un elemento [`Grid`](xref:Xamarin.Forms.Grid). El valor de la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica la distancia entre las vistas en el elemento `Grid`. Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una etiqueta en una cuadrícula en iOS y Android](../images/create-grid.png "Cuadrícula que contiene una etiqueta")](../images/create-grid-large.png#lightbox "Cuadrícula que contiene una etiquetas")

    Para obtener más información sobre [`Grid`](xref:Xamarin.Forms.Grid), vea [Cuadrícula de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
