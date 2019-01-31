---
title: Realizar la navegación en una aplicación de varias página de Xamarin.Forms
description: En este artículo se explica cómo activar la aplicación una sola página, capaz de almacenar una sola nota, en una aplicación de varias página, capaz de almacenar varias notas.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 48b1c93afa22bf069a2b47b6c9a259641e8ed70f
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293340"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Realizar la navegación en una aplicación de Xamarin.Forms de varias páginas

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)

En este tutorial, obtendrá información sobre cómo:

- Agregar páginas adicionales a una solución de Xamarin.Forms.
- Realizar la navegación entre páginas.
- Utilice el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

El tutorial le guía a través de cómo convertir una aplicación de Xamarin.Forms multiplataforma de página única, que puede almacenar una sola nota, en una aplicación de varias página, capaz de almacenar varias notas. A continuación se muestra la aplicación final:

[![](multi-page-images/screenshots1-sml.png "Notas de la página")](multi-page-images/screenshots1.png#lightbox "notas de la página")
[![](multi-page-images/screenshots2-sml.png "tenga en cuenta la página de entrada")](multi-page-images/screenshots2.png#lightbox "Nota Página de entrada")

### <a name="prerequisites"></a>Requisitos previos

Debe completar correctamente el [inicio rápido anterior](single-page.md) antes de intentar este inicio rápido. Como alternativa, descargue el [anterior ejemplo de tutorial rápido](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/) y usarlo como punto de partida para este inicio rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio. En la página de inicio, haga clic en **Abrir proyecto / solución**y en el **Abrir proyecto** cuadro de diálogo Seleccionar archivo de solución para el proyecto de notas:

    ![](multi-page-images/vs/open-solution.png "Abrir proyecto")

2. En **el Explorador de soluciones**, haga doble clic en el **notas** del proyecto y seleccione **Agregar > nueva carpeta**:

    ![](multi-page-images/vs/add-new-item.png "Agregar nuevo elemento")

3. En **el Explorador de soluciones**, asigne el nombre de la nueva carpeta **modelos**:

    ![](multi-page-images/vs/name-folder.png "Carpeta Models")

4. En **el Explorador de soluciones**, seleccione el **modelos** carpeta, con el botón secundario y seleccione **Agregar > nuevo elemento...** :

    ![](multi-page-images/vs/add-new-models-file.png "Agregar nuevo archivo")

5. En el **Agregar nuevo elemento** cuadro de diálogo, seleccione **Visual C# elementos > clase**, asigne al nuevo archivo **Nota**y haga clic en el **agregar** botón:

    ![](multi-page-images/vs/add-note-class.png "Agregar clase de nota")

    Esto agregará una clase denominada **Nota** a la **modelos** carpeta de la **notas** proyecto.

6. En **Note.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un `Note` modelo donde se almacenará los datos sobre todas las notas de la aplicación.    

    Guarde los cambios en **Note.cs** presionando **CTRL+S**y cierre el archivo.

7. En **el Explorador de soluciones**, haga doble clic en el **notas** del proyecto y seleccione **Agregar > nuevo elemento...** . En el **Agregar nuevo elemento** cuadro de diálogo, seleccione **Visual C# elementos > Xamarin.Forms > página de contenido**, asigne al nuevo archivo **NoteEntryPage**y haga clic en el  **Agregar** botón:

    ![](multi-page-images/vs/add-note-entry-page.png "Agregar Xamarin.Forms ContentPage")

    Esto agregará una nueva página denominada **NoteEntryPage** a la carpeta raíz del proyecto. Esta página será la segunda página de la aplicación.

8. En **NoteEntryPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto y dos [ `Button` ](xref:Xamarin.Forms.Button) instancias que dirigir la aplicación para guardar o eliminar un archivo. Los dos `Button` instancias están dispuestas horizontalmente en un [ `Grid` ](xref:Xamarin.Forms.Grid), con el `Editor` y `Grid` se dispuestos verticalmente en una [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Además, el `Editor` utiliza el enlace de datos para enlazar el `Text` propiedad de la `Note` modelo. Para obtener más información sobre el enlace de datos, vea [enlace de datos](deepdive.md#data-binding) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage.xaml** presionando **CTRL+S**y cierre el archivo.

9. En **NoteEntryPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Este código se almacena un `Note` instancia, que representa una sola nota, en el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando el **guardar** [ `Button` ](xref:Xamarin.Forms.Button) se presiona el `OnSaveButtonClicked` se ejecuta el controlador de eventos, ya sea guarda el contenido de la `Editor` en un archivo nuevo con un nombre de archivo generado aleatoriamente, o a un archivo existente si se está actualizando una nota. En ambos casos, el archivo se almacena en la carpeta de datos de aplicación local para la aplicación. A continuación, el método se desplaza a la página anterior. Cuando el **eliminar** `Button` se presiona el `OnDeleteButtonClicked` se ejecuta el controlador de eventos, lo que elimina el archivo, siempre que exista y se desplaza a la página anterior. Para obtener más información sobre la navegación, consulte [navegación](deepdive.md#navigation) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage.xaml.cs** presionando **CTRL+S**y cierre el archivo.

      > [!WARNING]
      > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

10. En **el Explorador de soluciones**, haga doble clic en el **notas** del proyecto y seleccione **Agregar > nuevo elemento...** . En el **Agregar nuevo elemento** cuadro de diálogo, seleccione **Visual C# elementos > Xamarin.Forms > página de contenido**, asigne al nuevo archivo **NotesPage**y haga clic en el **agregar**  botón.

      Esto agregará una página denominada **NotesPage** a la carpeta raíz del proyecto. Esta página será la página raíz de la aplicación.

11. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `ListView` ](xref:Xamarin.Forms.ListView) y un [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). El `ListView` utiliza enlace de datos para mostrar las notas que se recuperan por la aplicación y seleccionar una nota, irá a la `NoteEntryPage` donde se puede modificar la nota. Como alternativa, puede crearse una nueva nota presionando el `ToolbarItem`. Para obtener más información sobre el enlace de datos, vea [enlace de datos](deepdive.md#data-binding) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NotesPage.xaml** presionando **CTRL+S**y cierre el archivo.

12. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Este código define la funcionalidad para el `NotesPage`. Cuando aparezca la página, el `OnAppearing` se ejecuta el método, que rellena la [ `ListView` ](xref:Xamarin.Forms.ListView) con las notas que se han recuperado desde la carpeta de datos de aplicación local. Cuando el [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) se presiona el `OnNoteAddedClicked` se ejecuta el controlador de eventos. Este método se desplaza a la `NoteEntryPage`, estableciendo el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la `NoteEntryPage` a un nuevo `Note` instancia. Cuando un elemento de la `ListView` está seleccionado el `OnListViewItemSelected` se ejecuta el controlador de eventos. Este método se desplaza a la `NoteEntryPage`, estableciendo el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la `NoteEntryPage` seleccionada `Note` instancia. Para obtener más información sobre la navegación, consulte [navegación](deepdive.md#navigation) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NotesPage.xaml.cs** presionando **CTRL+S**y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

13. En **el Explorador de soluciones**, haga doble clic en **App.xaml.cs** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Este código agrega una declaración de espacio de nombres para el `System.IO` espacio de nombres y agrega una declaración para una variable static `FolderPath` propiedad de tipo `string`. El `FolderPath` propiedad se utiliza para almacenar la ruta de acceso en el dispositivo donde se almacenarán los datos de la nota. Además, el código inicializa el `FolderPath` propiedad en el `App` constructor e inicializa el [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propiedad sea un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que hospeda una instancia de `NotesPage`. Para obtener más información sobre la navegación, consulte [navegación](deepdive.md#navigation) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

14. En **el Explorador de soluciones**, en el **notas** del proyecto, haga clic en **MainPage.xaml**y seleccione **eliminar**. En el cuadro de diálogo que aparece presione el **Aceptar** botón para quitar el archivo de disco duro.

    Esto quita una página que ya no se utiliza.

15. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, consulte [crear la Guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** presione el **+** botón para navegar a la **NoteEntryPage** y escriba una nota. Después de guardar la nota de la aplicación navegará a la **NotesPage**.

    Escriba un número de notas, de longitud variable, observar el comportamiento de la aplicación.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac. En la página de inicio, haga clic en **abrir...** y en el cuadro de diálogo, seleccione el archivo de solución para el proyecto de notas:

    ![](multi-page-images/vsmac/open-solution.png "Abrir solución")

2. En el **panel de solución**, seleccione el **notas** proyecto, secundario y seleccione **Agregar > nueva carpeta**:

    ![](multi-page-images/vsmac/add-new-folder.png "Agregar nueva carpeta")

3. En el **panel de solución**, asigne el nombre de la nueva carpeta **modelos**:

    ![](multi-page-images/vsmac/name-folder.png "Carpeta Models")

4. En el **panel de solución**, seleccione el **modelos** carpeta, con el botón secundario y seleccione **Agregar > nuevo archivo...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Agregar nuevo archivo")

5. En el **nuevo archivo** cuadro de diálogo, seleccione **General > clase vacía**, asigne al nuevo archivo **Nota**y haga clic en el **New** botón:

    ![](multi-page-images/vsmac/add-note-class.png "Agregar clase de nota")

    Esto agregará una clase denominada **Nota** a la **modelos** carpeta de la **notas** proyecto.

6. En **Note.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un `Note` modelo donde se almacenará los datos sobre todas las notas de la aplicación.

    Guarde los cambios en **Note.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

7. En el **panel de solución**, seleccione el **notas** proyecto, con el botón secundario y seleccione **Agregar > nuevo archivo...** . En el **nuevo archivo** cuadro de diálogo, seleccione **formularios > formularios ContentPage XAML**, asigne al nuevo archivo **NoteEntryPage**y haga clic en el **New** botón:

    ![](multi-page-images/vsmac/add-note-entry-page.png "Agregar Xamarin.Forms ContentPage")

    Esto agregará una nueva página denominada **NoteEntryPage** a la carpeta raíz del proyecto. Esta página será la segunda página de la aplicación.

8. En **NoteEntryPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto y dos [ `Button` ](xref:Xamarin.Forms.Button) instancias que dirigir la aplicación para guardar o eliminar un archivo. Los dos `Button` instancias están dispuestas horizontalmente en un [ `Grid` ](xref:Xamarin.Forms.Grid), con el `Editor` y `Grid` se dispuestos verticalmente en una [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Además, el `Editor` utiliza el enlace de datos para enlazar el `Text` propiedad de la `Note` modelo. Para obtener más información sobre el enlace de datos, vea [enlace de datos](deepdive.md#data-binding) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage.xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

9. En **NoteEntryPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Este código se almacena un `Note` instancia, que representa una sola nota, en el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando el **guardar** [ `Button` ](xref:Xamarin.Forms.Button) se presiona el `OnSaveButtonClicked` se ejecuta el controlador de eventos, ya sea guarda el contenido de la `Editor` en un archivo nuevo con un nombre de archivo generado aleatoriamente, o a un archivo existente si se está actualizando una nota. En ambos casos, el archivo se almacena en la carpeta de datos de aplicación local para la aplicación. A continuación, el método se desplaza a la página anterior. Cuando el **eliminar** `Button` se presiona el `OnDeleteButtonClicked` se ejecuta el controlador de eventos, lo que elimina el archivo, siempre que exista y se desplaza a la página anterior. Para obtener más información sobre la navegación, consulte [navegación](deepdive.md#navigation) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage.xaml.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

      > [!WARNING]
      > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

10. En el **panel de solución**, seleccione el **notas** proyecto, con el botón secundario y seleccione **Agregar > nuevo archivo...** . En el **nuevo archivo** cuadro de diálogo, seleccione **formularios > formularios ContentPage XAML**, asigne al nuevo archivo **NotesPage**y haga clic en el **New** botón.

      Esto agregará una página denominada **NotesPage** a la carpeta raíz del proyecto. Esta página será la página raíz de la aplicación.

11. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de un [ `ListView` ](xref:Xamarin.Forms.ListView) y un [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). El `ListView` utiliza enlace de datos para mostrar las notas que se recuperan por la aplicación y seleccionar una nota, irá a la `NoteEntryPage` donde se puede modificar la nota. Como alternativa, puede crearse una nueva nota presionando el `ToolbarItem`. Para obtener más información sobre el enlace de datos, vea [enlace de datos](deepdive.md#data-binding) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NotesPage.xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

12. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Este código define la funcionalidad para el `NotesPage`. Cuando aparezca la página, el `OnAppearing` se ejecuta el método, que rellena la [ `ListView` ](xref:Xamarin.Forms.ListView) con las notas que se han recuperado desde la carpeta de datos de aplicación local. Cuando el [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) se presiona el `OnNoteAddedClicked` se ejecuta el controlador de eventos. Este método se desplaza a la `NoteEntryPage`, estableciendo el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la `NoteEntryPage` a un nuevo `Note` instancia. Cuando un elemento de la `ListView` está seleccionado el `OnListViewItemSelected` se ejecuta el controlador de eventos. Este método se desplaza a la `NoteEntryPage`, estableciendo el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la `NoteEntryPage` seleccionada `Note` instancia. Para obtener más información sobre la navegación, consulte [navegación](deepdive.md#navigation) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **NotesPage.xaml.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

13. En el **panel de solución**, haga doble clic en **App.xaml.cs** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Este código agrega una declaración de espacio de nombres para el `System.IO` espacio de nombres y agrega una declaración para una variable static `FolderPath` propiedad de tipo `string`. El `FolderPath` propiedad se utiliza para almacenar la ruta de acceso en el dispositivo donde se almacenarán los datos de la nota. Además, el código inicializa el `FolderPath` propiedad en el `App` constructor e inicializa el [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propiedad sea un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que hospeda una instancia de `NotesPage`. Para obtener más información sobre la navegación, consulte [navegación](deepdive.md#navigation) en el [análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

14. En el **panel de solución**, en el **notas** del proyecto, haga clic en **MainPage.xaml**y seleccione **quitar**. En el cuadro de diálogo que aparece presione el **eliminar** botón para quitar el archivo de disco duro.

    Esto quita una página que ya no se utiliza.

15. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, consulte [crear la Guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** presione el **+** botón para navegar a la **NoteEntryPage** y escriba una nota. Después de guardar la nota de la aplicación navegará a la **NotesPage**.

    Escriba un número de notas, de longitud variable, observar el comportamiento de la aplicación.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

- Agregar páginas adicionales a una solución de Xamarin.Forms.
- Realizar la navegación entre páginas.
- Utilice el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

Para modificar la aplicación para que almacena sus datos en una base de datos local de SQLite.NET, continúe con el siguiente inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](database.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
