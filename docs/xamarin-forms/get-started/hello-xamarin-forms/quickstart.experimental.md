---
title: Inicio rápido de Xamarin.Forms
description: En este artículo se explica cómo crear una sencilla aplicación multiplataforma de notas con Xamarin.Forms, que permite escribir una nota y conservarla en el almacenamiento del dispositivo.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/21/2018
ms.openlocfilehash: 880fa527d91098cf8c5f8c46cd9c5cce9ec9a489
ms.sourcegitcommit: 744c0a50420bb091fca8b92a84c20e61c741cf9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2018
ms.locfileid: "52743160"
---
# <a name="xamarinforms-quickstart"></a>Inicio rápido de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

En este inicio rápido se explica cómo crear una sencilla aplicación multiplataforma de notas con Xamarin.Forms, que permite escribir una nota y conservarla en el almacenamiento del dispositivo. A continuación se muestra la aplicación final:

[![](quickstart-experimental-images/screenshots-sml.png "Aplicación de notas")](quickstart-experimental-images/screenshots.png#lightbox "Aplicación de notas")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Introducción a Visual Studio

1. En la pantalla **Inicio**, inicie Visual Studio. Se abrirá la página de inicio:

    ![](quickstart-experimental-images/vs/start-page.png "Visual Studio")

2. Para crear un proyecto, en Visual Studio, haga clic en **Crear nuevo proyecto...**:

    ![](quickstart-experimental-images/vs/new-solution.png "Nuevo proyecto")

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Multiplataforma**, seleccione la plantilla **Aplicación móvil (Xamarin.Forms)**, establezca el nombre en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Aceptar**:

    ![](quickstart-experimental-images/vs/new-project.png "Plantillas de proyectos multiplataforma")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

4. En el cuadro de diálogo **New Cross Platform App** (Nueva aplicación multiplataforma), haga clic en **Aplicación en blanco**, seleccione **.NET Standard** como estrategia de código de uso compartido y haga clic en el botón **Aceptar**:

    ![](quickstart-experimental-images/vs/new-app.png "Nueva aplicación multiplataforma")

5. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](quickstart-experimental-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

6. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
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
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Label`](xref:Xamarin.Forms.Label) para mostrar texto, una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

7. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-experimental-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

8. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`.

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](quickstart-experimental-images/vs/build-succeeded.png "Compilación satisfactoria")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](quickstart-experimental-images/vs/android-start.png "Barra de herramientas de Android de Visual Studio")

    ![](quickstart-experimental-images/vs/notes-android.png "Notas en Android Emulator")

    Escriba una nota y pulse el botón **Guardar**.

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](quickstart-experimental-images/vs/set-as-startup-project-ios.png "Establecer iOS como proyecto de inicio")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](quickstart-experimental-images/vs/ios-start.png "Barra de herramientas de iOS de Visual Studio")

    ![](quickstart-experimental-images/vs/notes-ios.png "Notas en iOS Simulator")

    Escriba una nota y pulse el botón **Guardar**.

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

1. Para crear un nuevo proyecto, inicie Visual Studio para Mac y, en la página de inicio, haga clic en **Nuevo proyecto...**:

    ![](quickstart-experimental-images/vsmac/new-project.png "Nueva solución")

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms en blanco** y haga clic en el botón **Siguiente**:

    ![](quickstart-experimental-images/vsmac/choose-template.png "Elija una plantilla")

3. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, asigne a la nueva aplicación el nombre **Notes**, asegúrese de que el botón de radio **Usar .NET Standard** esté seleccionado y haga clic en el botón **Siguiente**:    

    ![](quickstart-experimental-images/vsmac/configure-app.png "Configurar la aplicación de Forms")

4. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, deje los nombres de solución y de proyecto establecidos en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crearlo:

    ![](quickstart-experimental-images/vsmac/configure-project.png "Configurar el proyecto de Forms")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que tanto la solución como el proyecto se denominen **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

5. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
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
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Label`](xref:Xamarin.Forms.Label) para mostrar texto, una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Guarde los cambios en **MainPage.xaml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

7. En el **Panel de solución**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`.

    Guarde los cambios en **MainPage.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilación del inicio rápido

1. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). El proyecto se compilará y aparecerá un mensaje de operación correcta en la barra de herramientas de Visual Studio para Mac.

      ![](quickstart-experimental-images/vsmac/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En el **Panel de solución**, seleccione el proyecto **Notes.iOS**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-ios.png "Establecer iOS como proyecto de inicio")

3. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de iOS Simulator:

      ![](quickstart-experimental-images/vsmac/start.png "Barra de herramientas de Visual Studio para Mac")

      ![](quickstart-experimental-images/vsmac/notes-ios.png "Notas en iOS Simulator")

    Escriba una nota y pulse el botón **Guardar**.

4. En el **Panel de solución**, seleccione el proyecto **Notes.Droid**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-android.png "Establecer Android como proyecto de inicio")

5. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

      ![](quickstart-experimental-images/vsmac/notes-android.png "Notas en Android Emulator")

    Escriba una nota y pulse el botón **Guardar**.

::: zone-end

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
