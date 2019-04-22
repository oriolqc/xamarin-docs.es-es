---
title: Creación de una aplicación de página única de Xamarin.Forms
description: En este artículo se explica cómo crear una aplicación de Xamarin.Forms multiplataforma de página única, lo que permite escribir una nota y enviarlo al almacenamiento del dispositivo.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 7b13ab5a4cc0d8e66e260329b5a53397778cb179
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855125"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Crear una aplicación de Xamarin.Forms de página única

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

En este tutorial, obtendrá información sobre cómo:

- Crear una aplicación de Xamarin.Forms multiplataforma.
- Definir la interfaz de usuario para una página con el lenguaje de marcado de aplicaciones eXtensible (XAML).
- Interactuar con elementos de interfaz de usuario XAML desde el código.

El tutorial le guía a través de la creación de una aplicación de Xamarin.Forms multiplataforma, lo que permite escribir una nota y enviarlo al almacenamiento del dispositivo. A continuación se muestra la aplicación final:

[![](single-page-images/screenshots-sml.png "Aplicación de notas")](single-page-images/screenshots.png#lightbox "Aplicación de notas")

::: zone pivot="windows"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2019 (versión más reciente), con el **desarrollo móvil con .NET** carga de trabajo instalada.
- Conocimiento de C#.
- (opcional) Un equipo Mac emparejado para compilar la aplicación en iOS.

Para obtener más información sobre estos requisitos previos, consulte [instalar Xamarin](~/get-started/installation/index.md). Para obtener información acerca de cómo conectar 2019 de Visual Studio a un host de compilación de Mac, consulte [emparejar con Mac para desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introducción a Visual Studio de 2019

1. Inicie Visual Studio de 2019 y haga clic en la ventana de inicio **crear un nuevo proyecto** para crear un nuevo proyecto:

    ![](single-page-images/vs/new-solution-2019.png "Nuevo proyecto")

2. En el **crear un nuevo proyecto** ventana, seleccione **Mobile** en el **tipo de proyecto** lista desplegable, seleccione el **Mobile App (Xamarin.Forms** plantilla) y haga clic en el **siguiente** botón:

    ![](single-page-images/vs/new-project-2019.png "Plantillas de proyectos multiplataforma")

3. En el **configurar el nuevo proyecto** ventana, establezca el **nombre del proyecto** a **notas**, elija una ubicación adecuada para el proyecto y haga clic en el **crear** botón:

    ![](single-page-images/vs/configure-project.png "Configurar el proyecto")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

4. En el **nueva aplicación multiplataforma** cuadro de diálogo, haga clic en **aplicación vacía**y haga clic en el **Aceptar** botón:

    ![](single-page-images/vs/new-app-2019.png "Nueva aplicación multiplataforma")

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

5. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

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
            <Editor x:Name="editor"
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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `Label` ](xref:Xamarin.Forms.Label) para mostrar texto, un [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto y dos [ `Button` ](xref:Xamarin.Forms.Button) instancias que dirigir la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre la creación de la interfaz de usuario, consulte [interfaz de usuario](deepdive.md#user-interface) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

7. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

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
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información acerca de la interacción del usuario, consulte [responde a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Compilación satisfactoria")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](single-page-images/vs/android-start.png "Barra de herramientas de Android de Visual Studio")

    [![](single-page-images/vs/notes-android.png "Notas en el emulador de Android")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información acerca de cómo se inicia la aplicación en cada plataforma, consulte [iniciando la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Establecer iOS como proyecto de inicio")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Barra de herramientas de iOS de Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Notas en el simulador de iOS")](single-page-images/vs/notes-ios-large.png#lightbox "notas en el simulador de iOS")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información acerca de cómo se inicia la aplicación en cada plataforma, consulte [iniciando la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2017, con el **desarrollo móvil con .NET** carga de trabajo instalada.
- Conocimiento de C#.
- (opcional) Un equipo Mac emparejado para compilar la aplicación en iOS.

Para obtener más información sobre estos requisitos previos, consulte [instalar Xamarin](~/get-started/installation/index.md). Para obtener información acerca de cómo conectar 2019 de Visual Studio a un host de compilación de Mac, consulte [emparejar con Mac para desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Introducción a Visual Studio 2017

1. Inicie Visual Studio 2017 y en la página de inicio, haga clic en **crear nuevo proyecto...**  para crear un nuevo proyecto:

    ![](single-page-images/vs/new-solution.png "Nuevo proyecto")

2. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Multiplataforma**, seleccione la plantilla **Aplicación móvil (Xamarin.Forms)**, establezca el nombre en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Aceptar**:

    ![](single-page-images/vs/new-project.png "Plantillas de proyectos multiplataforma")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

3. En el cuadro de diálogo **New Cross Platform App** (Nueva aplicación multiplataforma), haga clic en **Aplicación en blanco**, seleccione **.NET Standard** como estrategia de código de uso compartido y haga clic en el botón **Aceptar**:

    ![](single-page-images/vs/new-app.png "Nueva aplicación multiplataforma")

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

4. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

5. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `Label` ](xref:Xamarin.Forms.Label) para mostrar texto, un [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto y dos [ `Button` ](xref:Xamarin.Forms.Button) instancias que dirigir la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre la creación de la interfaz de usuario, consulte [interfaz de usuario](deepdive.md#user-interface) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

6. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

7. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

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
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información acerca de la interacción del usuario, consulte [responde a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Compilación satisfactoria")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](single-page-images/vs/android-start.png "Barra de herramientas de Android de Visual Studio")

    [![](single-page-images/vs/notes-android.png "Notas en el emulador de Android")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información acerca de cómo se inicia la aplicación en cada plataforma, consulte [iniciando la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Establecer iOS como proyecto de inicio")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Barra de herramientas de iOS de Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Notas en el simulador de iOS")](single-page-images/vs/notes-ios-large.png#lightbox "notas en el simulador de iOS")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información acerca de cómo se inicia la aplicación en cada plataforma, consulte [iniciando la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio para Mac (versión más reciente), con iOS y compatibilidad con la plataforma de Android instalado.
- Xcode (versión más reciente).
- Conocimiento de C#.

Para obtener más información sobre estos requisitos previos, consulte [instalar Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

1. Inicie Visual Studio para Mac y en la ventana de inicio, haga clic en **New** para crear un nuevo proyecto:

    ![](single-page-images/vsmac/new-project.png "Nueva solución")

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms en blanco** y haga clic en el botón **Siguiente**:

    ![](single-page-images/vsmac/choose-template.png "Elija una plantilla")

3. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, asigne a la nueva aplicación el nombre **Notes**, asegúrese de que el botón de radio **Usar .NET Standard** esté seleccionado y haga clic en el botón **Siguiente**:    

    ![](single-page-images/vsmac/configure-app.png "Configurar la aplicación de Forms")

4. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, deje los nombres de solución y de proyecto establecidos en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crearlo:

    ![](single-page-images/vsmac/configure-project.png "Configurar el proyecto de Forms")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que tanto la solución como el proyecto se denominen **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

5. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vsmac/mainpage-xaml.png "MainPage.xaml")

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
            <Editor x:Name="editor"
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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `Label` ](xref:Xamarin.Forms.Label) para mostrar texto, un [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto y dos [ `Button` ](xref:Xamarin.Forms.Button) instancias que dirigir la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre la creación de la interfaz de usuario, consulte [interfaz de usuario](deepdive.md#user-interface) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

7. En el **Panel de solución**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

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
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información acerca de la interacción del usuario, consulte [responde a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilación del inicio rápido

1. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). El proyecto se compilará y aparecerá un mensaje de operación correcta en la barra de herramientas de Visual Studio para Mac.

      ![](single-page-images/vsmac/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En el **panel de solución**, seleccione el **Notes.iOS** proyecto, secundario y seleccione **establecer como proyecto de inicio**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Establecer iOS como proyecto de inicio")

3. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de iOS Simulator:

      ![](single-page-images/vsmac/start.png "Barra de herramientas de Visual Studio para Mac")

      [![](single-page-images/vsmac/notes-ios.png "Notas en el simulador de iOS")](single-page-images/vsmac/notes-ios-large.png#lightbox "notas en el simulador de iOS")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información acerca de cómo se inicia la aplicación en cada plataforma, consulte [iniciando la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

4. En el **panel de solución**, seleccione el **Notes.Droid** proyecto, secundario y seleccione **establecer como proyecto de inicio**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Establecer Android como proyecto de inicio")

5. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

      [![](single-page-images/vsmac/notes-android.png "Notas en el emulador de Android")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información acerca de cómo se inicia la aplicación en cada plataforma, consulte [iniciando la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

- Crear una aplicación de Xamarin.Forms multiplataforma.
- Definir la interfaz de usuario para una página con el lenguaje de marcado de aplicaciones eXtensible (XAML).
- Interactuar con elementos de interfaz de usuario XAML desde el código.

Para activar esta aplicación de página única en una aplicación de varias página, continúe con el siguiente inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](multi-page.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
