---
title: Inicio rápido de Xamarin.Forms
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: e8d1064e76c2ccdeb14302d30428cd932444a0f5
ms.sourcegitcommit: 271d3f7ea4abfcf87734d2c747a68cb8114d743c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2018
---
# <a name="xamarinforms-quickstart"></a>Inicio rápido de Xamarin.Forms

En este tutorial se muestra cómo crear una aplicación que convierte un número de teléfono alfanumérico especificado por el usuario en un número de teléfono numérico y que llama a ese número. A continuación se muestra la aplicación final:

[![](quickstart-images/intro-app-examples-sml.png "Aplicación Phoneword")](quickstart-images/intro-app-examples.png#lightbox "Aplicación Phoneword")

Cree la aplicación Phoneword de la siguiente forma:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En la pantalla **Inicio**, inicie Visual Studio. Se abrirá la página de inicio:

    ![](quickstart-images/vs/start-page.png "Visual Studio")

2. Para crear un proyecto, en Visual Studio, haga clic en **Crear nuevo proyecto...**:

    ![](quickstart-images/vs/new-solution.png "Nuevo proyecto")

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Multiplataforma**, seleccione la plantilla **Aplicación móvil (Xamarin.Forms)**, establezca el nombre y el nombre de la solución en `Phoneword`, elija una ubicación adecuada para el proyecto y haga clic en el botón **Aceptar**:

    ![](quickstart-images/vs/new-project.png "Plantillas de proyectos multiplataforma")

4. En el cuadro de diálogo **Nueva aplicación multiplataforma**, haga clic en **Aplicación en blanco**, seleccione **Xamarin.Forms** como tecnología de IU, seleccione **.NET Standard** como estrategia de código de uso compartido y, luego, haga clic en el botón **Aceptar**:

    ![](quickstart-images/vs/new-app.png "Nueva aplicación multiplataforma")

5. En el **Explorador de soluciones**, en el proyecto **Phoneword**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](quickstart-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

6. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define mediante declaración la interfaz de usuario de la página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, WinPhone, Windows" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

7. En el **Explorador de soluciones**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

8. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Los métodos `OnTranslate` y `OnCall` se ejecutarán como respuesta al clic en los botones **Traducir** y **Llamar** en la interfaz de usuario, respectivamente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán más adelante.

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

9. En el **Explorador de soluciones**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo:

    ![](quickstart-images/vs/open-app-class.png "Abrir App.xaml.cs")

10. En **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. El constructor `App` establece la clase `MainPage` como la página que se muestra cuando se inicia la aplicación:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public App()
            {
                InitializeComponent();
                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

11. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item.png "Agregar nuevo elemento")

12. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Clase**, asigne al nuevo archivo el nombre **PhoneTranslator** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/add-translator-class.png "Agregar nueva clase")

13. En **PhoneTranslator.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código convertirá una palabra del teléfono en un número de teléfono:

    ```csharp
    using System.Text;

    namespace Core
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    Guarde los cambios en **PhoneTranslator.cs** presionando **CTRL+S** y cierre el archivo.

14. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item.png "Agregar nuevo elemento")

15. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Interfaz**, asigne al nuevo archivo el nombre **IDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/add-idialer-interface.png "Agregar nueva interfaz")

16. En **IDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define un método `Dial` que se debe implementar en cada plataforma para marcar un número de teléfono convertido:

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```

    Guarde los cambios en **IDialer.cs** presionando **CTRL+S** y cierre el archivo.

    > [!NOTE]
    > El código común de la aplicación ya está completo. El código del marcador de teléfono específico de la plataforma se implementará como [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

17. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.iOS** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item-ios.png "Agregar nuevo elemento")

18. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Apple > Código > Clase**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/new-phone-dialer-ios.png "Agregar nueva clase")

19. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método <code>Dial</code> que se usará en la plataforma iOS para marcar un número de teléfono convertido:

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    Guarde los cambios en **PhoneDialer.cs** presionando **CTRL+S** y cierre el archivo.

20. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.Android** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item-android.png "Agregar nuevo elemento")

21. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Android > Clase**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/new-phone-dialer-android.png "Agregar nueva clase")

22. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial` que se usará en la plataforma Android para marcar un número de teléfono convertido:

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionCall);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    Guarde los cambios en **PhoneDialer.cs** presionando **CTRL+S** y cierre el archivo.

23. En el **Explorador de soluciones**, en el proyecto **Phoneword.Android**, haga doble clic en **MainActivity.cs** para abrirlo, quite todo el código de la plantilla y reemplácelo por el siguiente:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);

                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }  
    ```

    Guarde los cambios en **MainActivity.cs** presionando **Ctrl+S** y cierre el archivo.

24. En el **Explorador de soluciones**, en el proyecto **Phoneword.Android**, haga doble clic en **Propiedades** y seleccione la pestaña **Manifiesto de Android**:

    ![](quickstart-images/vs/android-manifest.png "Abrir el manifiesto de Android")

25. En la sección **Permisos necesarios**, habilite el permiso **CALL_PHONE**. Esto da permiso a la aplicación para realizar una llamada telefónica:

    ![](quickstart-images/vs/android-manifest-changed.png "Habilitar el permiso CallPhone")

    Guarde los cambios en el manifiesto presionando **CTRL+S** y cierre el archivo.

26. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.UWP** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item-uwp.png "Agregar nuevo elemento")

27. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Clase**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/new-phone-dialer-uwp.png "Agregar nueva clase")

28. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial`, y los métodos auxiliares, que se usarán en la Plataforma universal de Windows para marcar un número de teléfono convertido:

    ```csharp
    using Phoneword.UWP;
    using System;
    using System.Threading.Tasks;
    using Windows.ApplicationModel.Calls;
    using Windows.UI.Popups;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.UWP
    {
        public class PhoneDialer : IDialer
        {
            bool dialled = false;

            public bool Dial(string number)
            {
                DialNumber(number);
                return dialled;
            }

            async Task DialNumber(string number)
            {
                var phoneLine = await GetDefaultPhoneLineAsync();
                if (phoneLine != null)
                {
                    phoneLine.Dial(number, number);
                    dialled = true;
                }
                else
                {
                    var dialog = new MessageDialog("No line found to place the call");
                    await dialog.ShowAsync();
                    dialled = false;
                }
            }

            async Task<PhoneLine> GetDefaultPhoneLineAsync()
            {
                var phoneCallStore = await PhoneCallManager.RequestStoreAsync();
                var lineId = await phoneCallStore.GetDefaultLineAsync();
                return await PhoneLine.FromIdAsync(lineId);
            }
        }
    }
    ```

    Guarde los cambios en **PhoneDialer.cs** presionando **CTRL+S** y cierre el archivo.

29. En el **Explorador de soluciones**, en el proyecto **Phoneword.UWP**, haga clic con el botón derecho en **Referencias** y seleccione **Agregar referencia...**:

    ![](quickstart-images/vs/uwp-add-reference.png "Agregar referencia")

30. En el cuadro de diálogo **Administrador de referencias**, seleccione **Windows universal > Extensiones > Windows Mobile Extensions for UWP** y haga clic en el botón **Aceptar**:

    ![](quickstart-images/vs/uwp-add-reference-extensions.png "Agregar extensiones de Windows Mobile para UWP")

31. En el **Explorador de soluciones**, en el proyecto **Phoneword.UWP**, haga doble clic en **Package.appxmanifest**:

    ![](quickstart-images/vs/uwp-manifest.png "Abrir el manifiesto de UWP")

31. En la página **Capacidades**, habilite la capacidad **Llamada telefónica**. Esto da permiso a la aplicación para realizar una llamada telefónica:

    ![](quickstart-images/vs/uwp-manifest-changed.png "Habilitar la funcionalidad de llamada de teléfono")

    Guarde los cambios en el manifiesto presionando **CTRL+S** y cierre el archivo.

32. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o pulse **CTRL+MAYÚS+B**). La aplicación se compilará y aparecerá un mensaje de éxito en la barra de estado de Visual Studio:

    ![](quickstart-images/vs/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.

33. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.UWP** y seleccione **Establecer como proyecto de inicio**:

    ![](quickstart-images/vs/uwp-set-as-startup-project.png "Establecer como proyecto de inicio")

34. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación:

    ![](quickstart-images/vs/start.png "Barra de herramientas de Visual Studio")
    ![](quickstart-images/vs/phone-result-uwp.png "Aplicación Phoneword.UWP")

35. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.Android** y seleccione **Establecer como proyecto de inicio**.
36. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en un emulador de Android.
37. Si tiene un dispositivo iOS y cumple los requisitos de sistema de Mac para el desarrollo de Xamarin.Forms, use una técnica similar para implementar la aplicación en el dispositivo iOS. Otra opción es implementar la aplicación en el [simulador remoto iOS](~/tools/ios-simulator.md).

    Nota: No se admiten las llamadas de teléfono en ningún simulador.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Para crear un nuevo proyecto, inicie Visual Studio para Mac y, en la página de inicio, haga clic en **Nuevo proyecto...**:

    ![](quickstart-images/xs/new-solution.png "Nueva solución")

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms en blanco** y haga clic en el botón **Siguiente**:

    ![](quickstart-images/xs/choose-template.png "Elija una plantilla")

3. En el cuadro de diálogo **Configurar su aplicación de Forms en blanco**, asígnele el nombre `Phoneword` a la nueva aplicación, asegúrese de que el botón de radio **Usar biblioteca de clases portable** y la casilla **Usar XAML para archivos de interfaz de usuario** estén activados, y haga clic en el botón **Siguiente**:

    ![](quickstart-images/xs/configure-app.png "Configurar la aplicación de Forms")

4. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, deje los nombres de solución y proyecto establecidos en `Phoneword`, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crear el proyecto:

    ![](quickstart-images/xs/configure-project.png "Configurar el proyecto de Forms")

5. En el **Panel de solución**, seleccione el proyecto **Phoneword**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-file.png "Agregar nuevo archivo")

6. En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > Formularios ContentPage Xaml**, asígnele al nuevo archivo el nombre **MainPage** y haga clic en el botón **Nuevo**. Esto agregará una página denominada **MainPage** al proyecto:

    ![](quickstart-images/xs/add-mainpage-class.png "Agregar nueva ContentPage")

7. En **Panel de solución**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](quickstart-images/xs/open-mainpage-xaml.png "Abrir MainPage.xaml")

8. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define mediante declaración la interfaz de usuario de la página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, WinPhone, Windows" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    Guarde los cambios en **MainPage.xaml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

9. En el **Panel de solución**, haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-images/xs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

10. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Los métodos `OnTranslate` y `OnCall` se ejecutarán como respuesta al clic en los botones **Traducir** y **Llamar** en la interfaz de usuario, respectivamente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán más adelante.

    Guarde los cambios en **MainPage.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

11. En el **Panel de solución**, haga doble clic en **App.xaml.cs** para abrirlo:

    ![](quickstart-images/xs/open-app-class.png "Abrir App.xaml.cs")

12. En **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. El constructor `App` establece la clase `MainPage` como la página que se muestra cuando se inicia la aplicación:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public App()
            {
                InitializeComponent();
                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Guarde los cambios en **Phoneword.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

13. En el **Panel de solución**, seleccione el proyecto **Phoneword**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-translator-file.png "Agregar nuevo archivo")

14. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne al nuevo archivo el nombre **PhoneTranslator** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/add-translator-class.png "Agregar nueva clase")

15. En **PhoneTranslator.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código convertirá una palabra del teléfono en un número de teléfono:

    ```csharp
    using System.Text;

    namespace Core
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    Guarde los cambios en **PhoneTranslator.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

16. En el **Panel de solución**, seleccione el proyecto **Phoneword**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-interface.png "Agregar nuevo archivo")

17. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Interfaz vacía**, asigne al nuevo archivo el nombre **IDialer** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/add-idialer-interface.png "Agregar nueva interfaz")

18. En **IDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define un método `Dial` que se debe implementar en cada plataforma para marcar un número de teléfono convertido:

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```
    Guarde los cambios en **IDialer.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

    > [!NOTE]
    > El código común de la aplicación ya está completo. El código del marcador de teléfono específico de la plataforma se implementará como [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

19. En el **Panel de solución**, seleccione el proyecto **Phoneword.iOS**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-file-ios.png "Agregar nuevo archivo")

20. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/new-phonedialer-ios.png "Agregar nueva clase")

21. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial` que se usará en la plataforma iOS para marcar un número de teléfono convertido:

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    Guarde los cambios en **PhoneDialer.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

22. En el **Panel de solución**, seleccione el proyecto **Phoneword.Droid**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-file-android.png "Agregar nuevo archivo")

23. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/new-phonedialer-android.png "Agregar nueva clase")

24. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial` que se usará en la plataforma Android para marcar un número de teléfono convertido:

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionCall);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    Guarde los cambios en **PhoneDialer.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

25. En el **Panel de solución**, en el proyecto **Phoneword.Droid**, haga doble clic en **MainActivity.cs** para abrirlo, quite todo el código de la plantilla y reemplácelo por el siguiente:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MyTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);

                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }        
    ```

    Guarde los cambios en **MainActivity.cs** en **Archivo > Guardar**, o bien presione **&#8984;+S** y cierre el archivo.

    > [!NOTE]
    > En el código de ejemplo se usa `Theme="@style/MainTheme"` porque se basa en una plantilla anterior. Puede comprobar el nombre de estilo correcto en **Phoneword/Droid/Resources/values/styles.xml** si se produce un error del compilador para el nombre del tema.

26. En el **Panel de solución**, expanda la carpeta **Propiedades** y haga doble clic en el archivo **AndroidManifest.xml**:

    ![](quickstart-images/xs/android-manifest.png "Abrir el manifiesto de Android")

27. En la sección **Permisos necesarios**, habilite el permiso **CallPhone**. Esto da permiso a la aplicación para realizar una llamada telefónica:

    ![](quickstart-images/xs/android-manifest-changed.png "Habilitar el permiso CallPhone")

    Guarde los cambios en **AndroidManifest.xml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

28. En el **Panel de solución**, quite la clase **PhonewordPage** del proyecto **Phoneword**. Esta página se agregó automáticamente al crear el proyecto y ya no es necesaria.
29. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). La aplicación se compilará y aparecerá un mensaje de éxito en la barra de herramientas de Visual Studio para Mac.

    ![](quickstart-images/xs/build-successful.png "Compilación correcta")

30. Si hay errores, repita los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.
31. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en el simulador de iOS:

    ![](quickstart-images/xs/start.png "Barra de herramientas de Visual Studio para Mac")
    ![](quickstart-images/xs/phoneword-result-ios.png "Simulador de iOS")

    Nota: No se admiten llamadas de teléfono en el simulador de iOS.

32. En el **Panel de solución**, seleccione el proyecto **Phoneword.Droid**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

    ![](quickstart-images/xs/set-startup-project.png "Establecer como proyecto de inicio")

33. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en el simulador de Android:

    ![](quickstart-images/xs/phoneword-result-android.png "Android Emulator")

    Nota: No se admiten las llamadas de teléfono en los emuladores de Android.

-----

Enhorabuena, ha completado una aplicación de Xamarin.Forms. En el [siguiente tema](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md) de esta guía se repasan los pasos realizados en este tutorial para comprender los aspectos básicos del desarrollo de aplicaciones mediante Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Acceso a características nativas a través de DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
