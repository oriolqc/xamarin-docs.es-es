---
ms.openlocfilehash: a7a5d51b8931aa9dffd2c16b4ead51371390f033
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372991"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **ButtonTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **ButtonTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **ButtonTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>    
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Button`](xref:Xamarin.Forms.Button) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un botón en iOS y Android](../images/create-button.png "Botón que contiene texto")](../images/create-button-large.png#lightbox "Botón que contiene texto")

    Tenga en cuenta que, de forma predeterminada un objeto [`Button`](xref:Xamarin.Forms.Button) tiende a ocupar todo el espacio del que dispone; en este caso, el ancho completo de su elemento primario (el objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout)).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **ButtonTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **ButtonTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **ButtonTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Button`](xref:Xamarin.Forms.Button) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un botón en iOS y Android](../images/create-button.png "Botón que contiene texto")](../images/create-button-large.png#lightbox "Botón que contiene texto")

    Tenga en cuenta que, de forma predeterminada un objeto [`Button`](xref:Xamarin.Forms.Button) tiende a ocupar todo el espacio del que dispone; en este caso, el ancho completo de su elemento primario (el objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout)).
