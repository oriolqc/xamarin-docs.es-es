---
title: Inicio rápido de Xamarin.Forms
description: En este artículo se explica cómo crear una aplicación que convierte un número de teléfono alfanumérico especificado por el usuario en un número de teléfono numérico y que llama a ese número.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2018
ms.openlocfilehash: 5b5f8c80e49d66ed3bd8b008c975d1cfeda93ed4
ms.sourcegitcommit: 0be3d10bf08d1f76eab109eb891ed202615ac399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321410"
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

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Multiplataforma**, seleccione la plantilla **Aplicación móvil (Xamarin.Forms)**, establezca el nombre en **Phoneword**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Aceptar**:

    ![](quickstart-images/vs/new-project.w157.png "Plantillas de proyectos multiplataforma")

    > [!NOTE]
    > Si no se asigna un nombre correcto a la solución **Phoneword**, se producirán numerosos errores de compilación.

4. En el cuadro de diálogo **New Cross Platform App** (Nueva aplicación multiplataforma), haga clic en **Aplicación en blanco**, seleccione **.NET Standard** como estrategia de código de uso compartido y haga clic en el botón **Aceptar**:

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
                <On Platform="Android, UWP" Value="20" />
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
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
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

9. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item.png "Agregar nuevo elemento")

10. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Clase**, asigne al nuevo archivo el nombre **PhoneTranslator** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/add-translator-class.w157.png "Agregar nueva clase")

11. En **PhoneTranslator.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código convertirá una palabra del teléfono en un número de teléfono:

    ```csharp
    using System.Text;

    namespace Phoneword
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

12. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item.png "Agregar nuevo elemento")

13. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Interfaz**, asigne al nuevo archivo el nombre **IDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/add-idialer-interface.w157.png "Agregar nueva interfaz")

14. En **IDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define un método `Dial` que se debe implementar en cada plataforma para marcar un número de teléfono convertido:

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

15. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.iOS** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item-ios.png "Agregar nuevo elemento")

16. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Apple > Código > Clase**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/new-phone-dialer-ios.w157.png "Agregar nueva clase")

17. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método <code>Dial</code> que se usará en la plataforma iOS para marcar un número de teléfono convertido:

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

18. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.Android** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item-android.png "Agregar nuevo elemento")

19. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Android > Clase**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/new-phone-dialer-android.w157.png "Agregar nueva clase")

20. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial` que se usará en la plataforma Android para marcar un número de teléfono convertido:

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

                var intent = new Intent (Intent.ActionDial);
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

21. En el **Explorador de soluciones**, en el proyecto **Phoneword.Android**, haga doble clic en **MainActivity.cs** para abrirlo, quite todo el código de la plantilla y reemplácelo por el siguiente:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
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

22. En el **Explorador de soluciones**, en el proyecto **Phoneword.Android**, haga doble clic en **Propiedades** y seleccione la pestaña **Manifiesto de Android**:

    ![](quickstart-images/vs/android-manifest.png "Abrir el manifiesto de Android")

23. En la sección **Permisos necesarios**, habilite el permiso **CALL_PHONE**. Esto da permiso a la aplicación para realizar una llamada telefónica:

    ![](quickstart-images/vs/android-manifest-changed.png "Habilitar el permiso CallPhone")

    Guarde los cambios en el manifiesto presionando **CTRL+S** y cierre el archivo.

24. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.UWP** y seleccione **Agregar > Nuevo elemento...**:

    ![](quickstart-images/vs/add-new-item-uwp.png "Agregar nuevo elemento")

25. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Clase**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Agregar**:

    ![](quickstart-images/vs/new-phone-dialer-uwp.w157.png "Agregar nueva clase")

26. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial`, y los métodos auxiliares, que se usarán en la Plataforma universal de Windows para marcar un número de teléfono convertido:

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

27. En el **Explorador de soluciones**, en el proyecto **Phoneword.UWP**, haga clic con el botón derecho en **Referencias** y seleccione **Agregar referencia...**:

    ![](quickstart-images/vs/uwp-add-reference.png "Agregar referencia")

28. En el cuadro de diálogo **Administrador de referencias**, seleccione **Windows universal > Extensiones > Windows Mobile Extensions for UWP** y haga clic en el botón **Aceptar**:

    ![](quickstart-images/vs/uwp-add-reference-extensions.png "Agregar extensiones de Windows Mobile para UWP")

29. En el **Explorador de soluciones**, en el proyecto **Phoneword.UWP**, haga doble clic en **Package.appxmanifest**:

    ![](quickstart-images/vs/uwp-manifest.png "Abrir el manifiesto de UWP")

30. En la página **Capacidades**, habilite la capacidad **Llamada telefónica**. Esto da permiso a la aplicación para realizar una llamada telefónica:

    ![](quickstart-images/vs/uwp-manifest-changed.png "Habilitar la funcionalidad de llamada de teléfono")

    Guarde los cambios en el manifiesto presionando **CTRL+S** y cierre el archivo.

31. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o pulse **CTRL+MAYÚS+B**). La aplicación se compilará y aparecerá un mensaje de éxito en la barra de estado de Visual Studio:

    ![](quickstart-images/vs/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.

32. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.UWP** y seleccione **Establecer como proyecto de inicio**:

    ![](quickstart-images/vs/uwp-set-as-startup-project.png "Establecer como proyecto de inicio")

33. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación:

    ![](quickstart-images/vs/start.png "Barra de herramientas de Visual Studio")
    ![](quickstart-images/vs/phone-result-uwp.png "Aplicación Phoneword.UWP")

34. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword.Android** y seleccione **Establecer como proyecto de inicio**.
35. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en un emulador de Android.
36. Si tiene un dispositivo iOS y cumple los requisitos de sistema de Mac para el desarrollo de Xamarin.Forms, use una técnica similar para implementar la aplicación en el dispositivo iOS. Otra opción es implementar la aplicación en el [simulador remoto iOS](~/tools/ios-simulator.md).

    Nota: No se admiten las llamadas de teléfono en ningún simulador.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Para crear un nuevo proyecto, inicie Visual Studio para Mac y, en la página de inicio, haga clic en **Nuevo proyecto...**:

    ![](quickstart-images/xs/new-solution.png "Nueva solución")

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms en blanco** y haga clic en el botón **Siguiente**:

    ![](quickstart-images/xs/choose-template.png "Elija una plantilla")

3. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, asigne a la nueva aplicación el nombre **Phoneword**, asegúrese de que el botón de radio **Use .NET Standard** (Uso de .NET Standard) esté seleccionado y haga clic en el botón **Siguiente**:

    ![](quickstart-images/xs/configure-app.png "Configurar la aplicación de Forms")

4. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, deje los nombres de solución y proyecto establecidos en **Phoneword**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crear el proyecto:

    ![](quickstart-images/xs/configure-project.png "Configurar el proyecto de Forms")

    > [!NOTE]
    > Si no se asigna un nombre correcto a la solución y al proyecto **Phoneword**, se producirán numerosos errores de compilación.

5. En **Panel de solución**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](quickstart-images/xs/open-mainpage-xaml.png "Abrir MainPage.xaml")

6. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define mediante declaración la interfaz de usuario de la página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
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

7. En el **Panel de solución**, haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-images/xs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

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
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
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

9. En el **Panel de solución**, seleccione el proyecto **Phoneword**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-translator-file.png "Agregar nuevo archivo")

10. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne al nuevo archivo el nombre **PhoneTranslator** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/add-translator-class.png "Agregar nueva clase")

11. En **PhoneTranslator.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código convertirá una palabra del teléfono en un número de teléfono:

    ```csharp
    using System.Text;

    namespace Phoneword
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

12. En el **Panel de solución**, seleccione el proyecto **Phoneword**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-interface.png "Agregar nuevo archivo")

13. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Interfaz vacía**, asigne al nuevo archivo el nombre **IDialer** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/add-idialer-interface.png "Agregar nueva interfaz")

14. En **IDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código define un método `Dial` que se debe implementar en cada plataforma para marcar un número de teléfono convertido:

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

15. En el **Panel de solución**, seleccione el proyecto **Phoneword.iOS**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-file-ios.png "Agregar nuevo archivo")

16. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/new-phonedialer-ios.png "Agregar nueva clase")

17. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial` que se usará en la plataforma iOS para marcar un número de teléfono convertido:

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

18. En el **Panel de solución**, seleccione el proyecto **Phoneword.Droid**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...**:

    ![](quickstart-images/xs/add-new-file-android.png "Agregar nuevo archivo")

19. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne al nuevo archivo el nombre **PhoneDialer** y haga clic en el botón **Nuevo**:

    ![](quickstart-images/xs/new-phonedialer-android.png "Agregar nueva clase")

20. En **PhoneDialer.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código. Este código crea el método `Dial` que se usará en la plataforma Android para marcar un número de teléfono convertido:

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

                var intent = new Intent (Intent.ActionDial);
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

21. En el **Panel de solución**, en el proyecto **Phoneword.Droid**, haga doble clic en **MainActivity.cs** para abrirlo, quite todo el código de la plantilla y reemplácelo por el siguiente:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
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

22. En el **Panel de solución**, expanda la carpeta **Propiedades** y haga doble clic en el archivo **AndroidManifest.xml**:

    ![](quickstart-images/xs/android-manifest.png "Abrir el manifiesto de Android")

23. En la sección **Permisos necesarios**, habilite el permiso **CallPhone**. Esto da permiso a la aplicación para realizar una llamada telefónica:

    ![](quickstart-images/xs/android-manifest-changed.png "Habilitar el permiso CallPhone")

    Guarde los cambios en **AndroidManifest.xml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

24. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). La aplicación se compilará y aparecerá un mensaje de éxito en la barra de herramientas de Visual Studio para Mac.

    ![](quickstart-images/xs/build-successful.png "Compilación correcta")

25. Si hay errores, repita los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.
26. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en el simulador de iOS:

    ![](quickstart-images/xs/start.png "Barra de herramientas de Visual Studio para Mac")
    ![](quickstart-images/xs/phoneword-result-ios.png "Simulador de iOS")

    Nota: No se admiten llamadas de teléfono en el simulador de iOS.

27. En el **Panel de solución**, seleccione el proyecto **Phoneword.Droid**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

    ![](quickstart-images/xs/set-startup-project.png "Establecer como proyecto de inicio")

28. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en el simulador de Android:

    ![](quickstart-images/xs/phoneword-result-android.png "Android Emulator")

    Nota: No se admiten las llamadas de teléfono en los emuladores de Android.

-----

Enhorabuena, ha completado una aplicación de Xamarin.Forms. En el [siguiente tema](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md) de esta guía se repasan los pasos realizados en este tutorial para comprender los aspectos básicos del desarrollo de aplicaciones mediante Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Acceso a características nativas a través de DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
