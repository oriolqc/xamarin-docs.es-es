---
title: Guía de inicio rápido sobre Multiscreen de Xamarin.Forms
description: En este artículo se muestra cómo ampliar la aplicación Phoneword mediante la adición de una segunda pantalla para realizar un seguimiento del historial de llamadas para la aplicación.
ms.prod: quickstart
ms.assetid: 255d93b9-518c-4e5d-a9cd-4dd8a7945a7f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2018
ms.openlocfilehash: 538ca831e51069f345a8a7a53aa844352c207faa
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268841"
---
# <a name="xamarinforms-multiscreen-quickstart"></a>Guía de inicio rápido sobre Multiscreen de Xamarin.Forms

Este tutorial muestra cómo ampliar la aplicación Phoneword agregando una segunda pantalla para realizar un seguimiento del historial de llamadas para la aplicación. A continuación se muestra la aplicación final:

[![](quickstart-images/intro-app-examples-sml.png "Aplicación Phoneword")](quickstart-images/intro-app-examples.png#lightbox "Aplicación Phoneword")

Amplíe la aplicación Phoneword de la siguiente forma:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie Visual Studio. En la página de inicio, haga clic en **Abrir proyecto...** y, en el cuadro de diálogo **Abrir proyecto**, seleccione el archivo de solución para el proyecto de Phoneword:

    ![](quickstart-images/vs/open-solution.png "Abrir proyecto")

2. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item.png "Agregar nuevo elemento")

3. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Xamarin.Forms > Página de contenido**, asigne al nuevo elemento el nombre **CallHistoryPage** y haga clic en el botón **Aceptar**. Esto agregará una página llamada **CallHistoryPage** al proyecto:

    ![](quickstart-images/vs/add-callhistorypage-class.png "Plantillas de proyecto de Xamarin.Forms")

4. En **CallHistoryPage.xaml**, quite todo el código de plantilla y reemplácelo por el siguiente código. Este código define mediante declaración la interfaz de usuario de la página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>
    ```

    Guarde los cambios en **CallHistoryPage.xaml** presionando **CTRL+S** y cierre el archivo.

5. En el **Explorador de soluciones**, haga doble clic en el archivo **App.xaml.cs** en el proyecto **Phoneword** compartido para abrirlo:

    ![](quickstart-images/vs/open-app-class.png "Abrir App.xaml.cs")

6. En **App.xaml.cs**, importe el espacio de nombres `System.Collections.Generic`, agregue la declaración de la propiedad `PhoneNumbers`, inicialice la propiedad en el constructor `App` e inicialice la propiedad [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) para que sea una [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). La colección `PhoneNumbers` se usará para almacenar una lista de cada número de teléfono traducido al que se ha llamado desde la aplicación:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

7. En el **Explorador de soluciones**, haga doble clic en el archivo **MainPage.xaml** en el proyecto **Phoneword** compartido para abrirlo:

    ![](quickstart-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

8. En **MainPage.xaml**, agregue un control [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) al final del control [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Se usará el botón para navegar a la página del historial de llamadas:

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

9. En el **Explorador de soluciones**, haz doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

10. En **MainPage.xaml.cs**, agregue el método de controlador de eventos `OnCallHistory` y modifique el método de controlador de eventos `OnCall` para agregar el número de teléfono traducido a la colección `App.PhoneNumbers` y habilitar la `callHistoryButton`, siempre que la variable `dialer` no sea `null`:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

11. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o pulse **CTRL+MAYÚS+B**). La aplicación se compilará y aparecerá un mensaje de éxito en la barra de estado de Visual Studio:

    ![](quickstart-images/vs/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.

12. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación:

    ![](quickstart-images/vs/start.png "Barra de herramientas de Visual Studio")
    ![](quickstart-images/vs/phone-result-uwp.png "Aplicación Phoneword.UWP")

13. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.Droid** y seleccione **Establecer como proyecto de inicio**.
14. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en un emulador de Android.
15. Si tiene un dispositivo iOS y cumple los requisitos de sistema de Mac para el desarrollo de Xamarin.Forms, use una técnica similar para implementar la aplicación en el dispositivo iOS. Otra opción es implementar la aplicación en el [simulador remoto iOS](~/tools/ios-simulator.md).

    Nota: No se admiten las llamadas de teléfono en ningún simulador.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Visual Studio para Mac. En la página de inicio, haga clic en **Abrir...** y, en el cuadro de diálogo, seleccione el archivo de solución para el proyecto de Phoneword:

    ![](quickstart-images/xs/open-solution.png "Abrir solución")

2. En el **Panel de solución**, seleccione el proyecto **Phoneword**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-file.png "Agregar nuevo archivo")

3. En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > Formularios ContentPage Xaml**, asígnele al nuevo archivo el nombre **CallHistoryPage**, y haga clic en el botón **Nuevo**. Esto agregará una página llamada **CallHistoryPage** al proyecto:

    ![](quickstart-images/xs/add-callhistorypage-class.png "Agregar ContentPage de Forms")

4. En el **Panel de solución**, haga doble clic en **CallHistoryPage.xaml** para abrirlo:

    ![](quickstart-images/xs/open-callhistorypage-xaml.png "Abrir CallHistoryPage.xaml")

5. En **CallHistoryPage.xaml**, quite todo el código de plantilla y reemplácelo por el siguiente código. Este código define mediante declaración la interfaz de usuario de la página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>      
    ```

    Guarde los cambios en **CallHistoryPage.xaml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

6. En el **Panel de solución**, haga doble clic en **App.xaml.cs** para abrirlo:

    ![](quickstart-images/xs/open-app-class.png "Abrir App.xaml.cs")

7. En **App.xaml.cs**, importe el espacio de nombres `System.Collections.Generic`, agregue la declaración de la propiedad `PhoneNumbers`, inicialice la propiedad en el constructor `App` e inicialice la propiedad [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) para que sea una [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). La colección `PhoneNumbers` se usará para almacenar una lista de cada número de teléfono traducido al que se ha llamado desde la aplicación:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

8. En **Panel de solución**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](quickstart-images/xs/open-mainpage-xaml.png "Abrir MainPage.xaml")

9. En **MainPage.xaml**, agregue un control [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) al final del control [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Se usará el botón para navegar a la página del historial de llamadas:

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    Guarde los cambios en **MainPage.xaml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

10. En el **Panel de solución**, haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-images/xs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

11. En **MainPage.xaml.cs**, agregue el método de controlador de eventos `OnCallHistory` y modifique el método de controlador de eventos `OnCall` para agregar el número de teléfono traducido a la colección `App.PhoneNumbers` y habilitar la `callHistoryButton`, siempre que la variable `dialer` no sea `null`:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    Guarde los cambios en **MainPage.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

12. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). La aplicación se compilará y aparecerá un mensaje de éxito en la barra de herramientas de Visual Studio para Mac:

    ![](quickstart-images/xs/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.

13. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en el simulador de iOS:

    ![](quickstart-images/xs/start.png "Barra de herramientas de Visual Studio para Mac")
    ![](quickstart-images/xs/phone-result-ios.png "Simulador de iOS")

    Nota: No se admiten llamadas de teléfono en el simulador de iOS.

14. En el **Panel de solución**, seleccione el proyecto **Phoneword.Droid**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

    ![](quickstart-images/xs/set-startup-project.png "Establecer como proyecto de inicio")

15. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en el simulador de Android:

    ![](quickstart-images/xs/phone-result-android.png "Android Emulator")

    Nota: No se admiten las llamadas de teléfono en los emuladores de Android.

-----

Enhorabuena, ha completado una aplicación de pantalla dividida de Xamarin.Forms. En el [siguiente tema](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/deepdive.md) de esta guía se explican los pasos que se han llevado a cabo en este tutorial con el fin de comprender la navegación de páginas y el enlace de datos mediante Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Phoneword (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
- [PhonewordMultiscreen (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/PhonewordMultiscreen/)
