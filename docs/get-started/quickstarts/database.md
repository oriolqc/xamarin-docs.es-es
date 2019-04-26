---
title: Almacenamiento de datos en una base de datos local de SQLite.NET
description: En este artículo se explica cómo almacenar datos en una base de datos local de SQLite.NET.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 5c3daf04c08e2109c46b24c198fef8e71fac2f3d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61263090"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Store los datos en una base de datos Local de SQLite.NET

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

En este tutorial, obtendrá información sobre cómo:

- Use el Administrador de paquetes de NuGet para agregar un paquete de NuGet a un proyecto.
- Store datos localmente en una base de datos de SQLite.NET.

La Guía de inicio rápido le guía a través almacenar datos en una base de datos local de SQLite.NET. A continuación se muestra la aplicación final:

[![](database-images/screenshots1-sml.png "Notas de la página")](database-images/screenshots1.png#lightbox "notas de la página")
[![](database-images/screenshots2-sml.png "tenga en cuenta la página de entrada")](database-images/screenshots2.png#lightbox "Nota Página de entrada")

### <a name="prerequisites"></a>Requisitos previos

Debe completar correctamente el [inicio rápido anterior](multi-page.md) antes de intentar este inicio rápido. Como alternativa, descargue el [anterior ejemplo de tutorial rápido](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/) y usarlo como punto de partida para este inicio rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución de notas.

2. En **el Explorador de soluciones**, seleccione el **notas** del proyecto, haga clic en y seleccione **administrar paquetes NuGet...** :

    ![](database-images/vs/add-nuget-packages.png "Agregue paquetes NuGet")    

3. En el **Administrador de paquetes NuGet**, seleccione la pestaña **Examinar**, busque el paquete NuGet **sqlite-net-pcl**, selecciónelo y haga clic en el botón **Instalar** para agregarlo al proyecto:

    ![](database-images/vs/add-package.png "Agregar paquete")

    > [!NOTE]
    > Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Autores:** Frank A. Krueger
    > - **Id.:** sqlite-net-pcl
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En **el Explorador de soluciones**, en el **notas** proyecto, abra **Note.cs** en el **modelos** reemplazar el existente y carpeta de código con el código siguiente:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un `Note` modelo donde se almacenará los datos sobre todas las notas de la aplicación. El `ID` propiedad está marcada con `PrimaryKey` y `AutoIncrement` atributos para asegurarse de que cada `Note` instancia en la base de datos de SQLite.NET tendrá un identificador único proporcionado por SQLite.NET.

    Guarde los cambios en **Note.cs** presionando **CTRL+S**y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

5. En **el Explorador de soluciones**, agregue una nueva carpeta denominada **datos** a la **notas** proyecto.

6. En **el Explorador de soluciones**, en el **notas** proyecto, agregar una nueva clase denominada **NoteDatabase** a la **datos** carpeta.

7. En **NoteDatabase.cs**, reemplace el código existente por el código siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leen datos, escribir datos en él y eliminar datos. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. Esta ruta de acceso será proporcionado por el `App` clase en el paso siguiente.

    Guarde los cambios en **NoteDatabase.cs** presionando **CTRL+S**y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

8. En **el Explorador de soluciones**, en el **notas** del proyecto, haga doble clic en **App.xaml.cs** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Este código define un `Database` propiedad que se crea un nuevo `NoteDatabase` instancia como un singleton, pasando el nombre de archivo de la base de datos como el argumento para el `NoteDatabase` constructor. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

9. En **el Explorador de soluciones**, en el **notas** del proyecto, haga doble clic en **NotesPage.xaml.cs** para abrirlo. A continuación, reemplace el `OnAppearing` método con el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Este código rellena la [ `ListView` ](xref:Xamarin.Forms.ListView) con las notas que se almacenan en la base de datos.

    Guarde los cambios en **NotesPage.xaml.cs** presionando **CTRL+S**y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

10. En **el Explorador de soluciones**, haga doble clic en **NoteEntryPage.xaml.cs** para abrirlo. A continuación, reemplace el `OnSaveButtonClicked` y `OnDeleteButtonClicked` métodos con el código siguiente:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      El `NoteEntryPage` almacena un `Note` instancia, que representa una sola nota, en el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando el `OnSaveButtonClicked` se ejecuta el controlador de eventos, el `Note` instancia se guarda en la base de datos y la aplicación regresa a la página anterior. Cuando el `OnDeleteButtonClicked` se ejecuta el controlador de eventos, el `Note` instancia se elimina de la base de datos y la aplicación regresa a la página anterior.

      Guarde los cambios en **NoteEntryPage.xaml.cs** presionando **CTRL+S**y cierre el archivo.

11. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, consulte [crear la Guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** presione el **+** botón para navegar a la **NoteEntryPage** y escriba una nota. Después de guardar la nota de la aplicación navegará a la **NotesPage**.

    Escriba un número de notas, de longitud variable, observar el comportamiento de la aplicación.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto de notas.

2. En el **panel de solución**, seleccione el **notas** del proyecto, haga clic en y seleccione **Agregar > Agregar paquetes NuGet...** :

    ![](database-images/vsmac/add-nuget-packages.png "Agregue paquetes NuGet")    

3. En la ventana **Agregar paquetes**, busque el paquete NuGet **sqlite-net-pcl**, selecciónelo y haga clic en el botón **Agregar paquete** para agregarlo al proyecto:

    ![](database-images/vsmac/add-package.png "Agregar paquete")

    > [!NOTE]
    > Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Autor:** Frank A. Krueger
    > - **Id.:** sqlite-net-pcl
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En el **panel de solución**, en el **notas** proyecto, abra **Note.cs** en el **modelos** carpeta y reemplace el código existente por lo siguiente código:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un `Note` modelo donde se almacenará los datos sobre todas las notas de la aplicación. El `ID` propiedad está marcada con `PrimaryKey` y `AutoIncrement` atributos para asegurarse de que cada `Note` instancia en la base de datos de SQLite.NET tendrá un identificador único proporcionado por SQLite.NET.

    Guarde los cambios en **Note.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

5. En el **panel de solución**, agregue una nueva carpeta denominada **datos** a la **notas** proyecto.

6. En el **panel de solución**, en el **notas** proyecto, agregar una nueva clase denominada **NoteDatabase** a la **datos** carpeta.

7. En **NoteDatabase.cs**, reemplace el código existente por el código siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leen datos, escribir datos en él y eliminar datos. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. Esta ruta de acceso será proporcionado por el `App` clase en el paso siguiente.

    Guarde los cambios en **NoteDatabase.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

8. En el **panel de solución**, en el **notas** del proyecto, haga doble clic en **App.xaml.cs** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Este código define un `Database` propiedad que se crea un nuevo `NoteDatabase` instancia como un singleton, pasando el nombre de archivo de la base de datos como el argumento para el `NoteDatabase` constructor. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

9. En el **panel de solución**, en el **notas** del proyecto, haga doble clic en **NotesPage.xaml.cs** para abrirlo. A continuación, reemplace el `OnAppearing` método con el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Este código rellena la [ `ListView` ](xref:Xamarin.Forms.ListView) con las notas que se almacenan en la base de datos.

    Guarde los cambios en **NotesPage.xaml.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Al intentar compilar la aplicación en este momento se producirá errores que se corregirán en pasos posteriores.

10. En el **panel de solución**, haga doble clic en **NoteEntryPage.xaml.cs** para abrirlo. A continuación, reemplace el `OnSaveButtonClicked` y `OnDeleteButtonClicked` métodos con el código siguiente:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      El `NoteEntryPage` almacena un `Note` instancia, que representa una sola nota, en el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando el `OnSaveButtonClicked` se ejecuta el controlador de eventos, el `Note` instancia se guarda en la base de datos y la aplicación regresa a la página anterior. Cuando el `OnDeleteButtonClicked` se ejecuta el controlador de eventos, el `Note` instancia se elimina de la base de datos y la aplicación regresa a la página anterior.

      Guarde los cambios en **NoteEntryPage.xaml.cs** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

11. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, consulte [crear la Guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** presione el **+** botón para navegar a la **NoteEntryPage** y escriba una nota. Después de guardar la nota de la aplicación navegará a la **NotesPage**.

    Escriba un número de notas, de longitud variable, observar el comportamiento de la aplicación.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

- Use el Administrador de paquetes de NuGet para agregar un paquete de NuGet a un proyecto.
- Store datos localmente en una base de datos de SQLite.NET.

Para aplicar estilo a la aplicación con los estilos XAML, continúe con el siguiente inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](styling.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
