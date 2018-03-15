---
title: "Resumen de capítulo 19. Vistas de colección"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a085eb306ad81b3c9214df269f69558bc8fbfaa7
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumen de capítulo 19. Vistas de colección

Xamarin.Forms define tres vistas que mantienen colecciones y mostrar sus elementos:

- [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) es una lista de elementos de cadena relativamente corta que permite al usuario elegir una
- [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) a menudo es una lista larga de elementos normalmente del mismo tipo y el formato, que permite al usuario elegir uno
- [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) es una colección de *celdas* (normalmente de varios tipos y repeticiones) para mostrar los datos o administrar proporcionados por el usuario

Es común para las aplicaciones de MVVM para usar el `ListView` para mostrar una colección de objetos seleccionable.

## <a name="program-options-with-picker"></a>Opciones del programa con el selector

El [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) es una buena elección cuando se necesita permitir al usuario elegir una opción entre una lista relativamente corta de `string` elementos.

### <a name="the-picker-and-event-handling"></a>El selector y el control de eventos

El [ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) ejemplo muestra cómo utilizar XAML para establecer el `Picker` [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) propiedad y agregue `string` elementos a la [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección. Cuando el usuario selecciona el `Picker`, muestra los elementos en el `Items` colección de una manera depende de la plataforma.

El [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento indica cuándo el usuario ha seleccionado un elemento. Base cero [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propiedad indica a continuación, el elemento seleccionado. Si se selecciona ningún elemento, `SelectedIndex` es igual a &ndash;1.

También puede usar `SelectedIndex` inicializar el elemento seleccionado, pero se debe establecer después de la `Items` se rellena la colección. En XAML, esto significa que probablemente utilizará un elemento de propiedad para establecer `SelectedIndex`.

### <a name="data-binding-the-picker"></a>El selector de enlace de datos

El `SelectedIndex` propiedad está respaldada por una propiedad enlazable pero `Items` no lo es, por lo que usar enlace de datos con un `Picker` es difícil. Una solución consiste en utilizar el `Picker` en combinación con un [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como la de la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. El [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) muestra cómo funciona.

## <a name="rendering-data-with-listview"></a>Representación de datos con el control ListView

El [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) es la única clase que deriva de [ `ItemsView<TVisual>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) del que hereda el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) y [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propiedades.

`ItemsSource` es de tipo `IEnumerable` pero es `null` de forma predeterminada y se debe inicializar explícitamente o set (normalmente) a una colección a través de un enlace de datos. Los elementos de esta colección pueden ser de cualquier tipo.

`ListView` define un [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) propiedad que está establecida en uno de los elementos de la `ItemsSource` colección o `null` si se selecciona ningún elemento. `ListView` se activa la [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) eventos cuando se selecciona un nuevo elemento.

### <a name="collections-and-selections"></a>Colecciones y selecciones

El [ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) ejemplo rellena un `ListView` con 17 `Color` valores en un `List<Color>` colección. Los elementos son seleccionables, pero de forma predeterminada se muestran con sus poco atractivo `ToString` representaciones. Varios ejemplos en este capítulo muestran cómo corregir que muestran y hacerla tan atractivas según sea necesario.

### <a name="the-row-separator"></a>El separador de filas

En iOS y Android muestra, una línea fina separa las filas. Puede hacer esto con el [ `SeparatorVisibiliy` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorVisibility/) y [ `SeparatorColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorColor/) propiedades. `SeparatorVisibility` propiedad es de tipo [ `SeparatorVisbility` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SeparatorVisibility/), una enumeración con dos miembros:

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.Default/), la configuración predeterminada
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.None/)

### <a name="data-binding-the-selected-item"></a>El elemento seleccionado de enlace de datos

El `SelectedItem` propiedad está respaldada por una propiedad enlazable, por lo que puede ser el origen o el destino de un enlace de datos. Su valor predeterminado `BindingMode` es `OneWayToSource`, pero normalmente es el destino de un enlace de datos bidireccional, especialmente en escenarios MVVM. El [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) muestra este tipo de enlace.

### <a name="the-observablecollection-difference"></a>La diferencia ObservableCollection

El [ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) ejemplo establece el `ItemsSource` propiedad de un `ListView` para un `List<DateTime>` colección y, a continuación, progresivamente agrega un nuevo `DateTime` objeto a la colección cada segundo a través de un temporizador.

Sin embargo, el `ListView` no actualizará automáticamente porque la `List<T>` colección no tiene un mecanismo de notificación para indicar cuando se agregan o se quitan de la colección de elementos.

Una clase mucho mejor usar en tales escenarios es [ `ObservableCollection<T>` ](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) definido en el `System.Collections.ObjectModel` espacio de nombres. Esta clase implementa la [ `INotifyCollectionChanged` ](https://developer.xamarin.com/api/type/System.Collections.Specialized.INotifyCollectionChanged/) interfaz y, por consiguiente, se activa un [ `CollectionChanged` ](https://developer.xamarin.com/api/event/System.Collections.ObjectModel.ObservableCollection%3CT%3E.CollectionChanged/) eventos cuando se agregan o se quitan de la colección, o cuando se reemplaza o se mueve dentro de elementos la colección. Cuando el `ListView` internamente detecta que una clase que implemente `INotifyCollectionChanged` se ha establecido en su `ItemsSource` propiedad, se adjunta un controlador para el `CollectionChanged` eventos y actualice su presentación cuando cambia la colección.

El [ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) ejemplo muestra el uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Plantillas y las celdas

De forma predeterminada, un `ListView` muestra elementos en su colección de uso de cada elemento `ToString` método. Un mejor enfoque implica definir una plantilla para mostrar los elementos.

Para experimentar con esta característica, puede usar el [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. Esta clase define una variable static `All` propiedad de tipo `IList<NamedColor>` que contiene 141 `NamedColor` objetos correspondientes a los campos públicos de la `Color` estructura.

El [ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) conjuntos de ejemplo la `ItemsSource` de un `ListView` a este `NamedColor.All` propiedad, pero solo los nombres de clase completo de la `NamedColor` son objetos muestra.

`ListView` necesita una plantilla para mostrar estos elementos. En el código, puede establecer la [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propiedad definida por `ItemsView<TVisual>` a una [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) objeto mediante la [ `DataTemplate` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) que hace referencia a un derivado de la [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) clase. `Cell` tiene cinco derivados:

- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) &mdash; contiene dos `Label` vistas (en términos conceptuales)
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) &mdash; Agrega un `Image` ver a `TextCell`
- [`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) &mdash; contiene un `Entry` ver con un `Label`
- [`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) &mdash; contiene un `Switch` con un `Label`
- [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) &mdash; puede ser cualquier `View` (probablemente con elementos secundarios)

A continuación, llamar a [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) y [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) en el `DataTemplate` objeto para asociar valores con el `Cell` propiedades, o establecer enlaces de datos en el `Cell` propiedades que hacen referencia a propiedades de los elementos de la `ItemsSource` colección. Esto se muestra en el [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) ejemplo.

Tal y como se muestra cada elemento por el `ListView`, se crea un pequeño árbol visual de la plantilla y se establecen los enlaces de datos entre el elemento y las propiedades de los elementos de este árbol visual. Puede hacerse una idea de este proceso mediante la instalación de controladores para la [ `ItemAppearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemAppearing/) y [ `ItemDisappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemDisappearing/) eventos de la `ListView`, o mediante una alternativa [ `DataTemplate`constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Func%7BSystem.Object%7D/) que usa una función que se llama cada vez que se debe crear el árbol visual de un elemento.

El [ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) muestra un programa funcionalmente idéntico completamente en XAML. A `DataTemplate` etiqueta se establece en el `ItemTemplate` propiedad de la `ListView`y, a continuación, el `TextCell` está establecido en el `DataTemplate`. Enlaces a las propiedades de los elementos de la colección se establecen directamente en el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/) y [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/) propiedades de la `TextCell`.

### <a name="custom-cells"></a>Celdas personalizadas

En XAML es posible establecer un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) a la `DataTemplate` y, a continuación, definir un árbol visual personalizado como el [ `View` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ViewCell.View/) propiedad de `ViewCell`. (`View` es la propiedad de contenido de `ViewCell` por lo que el `ViewCell.View` etiquetas no son obligatorios.) El [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) ejemplo muestra esta técnica:

[![Captura de pantalla triple de la lista de colores personalizada con el nombre](images/ch19fg11-small.png "lista de colores personalizada denominada")](images/ch19fg11-large.png#lightbox "personalizado denominado lista de colores")

Obtener el tamaño adecuado para todas las plataformas puede ser complicado. El [ `RowHeight` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RowHeight/) propiedad es útil, pero en algunos casos es conveniente que recurrir a la [ `HasUnevenRows` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HasUnevenRows/) propiedad, que es menos eficaz, pero se fuerza la `ListView` para cambiar el tamaño de las filas. Para iOS y Android, debe utilizar una de estas dos propiedades para obtener el tamaño de fila adecuados.

### <a name="grouping-the-listview-items"></a>Agrupar los elementos de ListView

`ListView` admite la agrupación de elementos y navegar entre esos grupos. El `ItemsSource` propiedad debe establecerse en una colección de colecciones: el objeto que `ItemsSource` está establecido en debe implementar `IEnumerable`, y también debe implementar cada elemento de la colección `IEnumerable`. Cada grupo debe incluir dos propiedades: una descripción de texto de los grupos y una abreviatura de tres letras.

El [ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca crea siete grupos de `NamedColor` objetos. El [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) ejemplo muestra cómo utilizar estos grupos con el [ `IsGroupingEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsGroupingEnabled/) propiedad de `ListView` establecido en `true`y el [ `GroupDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/) y [ `GroupShortNameBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/) propiedades se enlazan a las propiedades de cada grupo.

### <a name="custom-group-headers"></a>Encabezados de grupo personalizado

Es posible crear encabezados personalizados para el `ListView` grupos si se reemplaza el `GroupDisplayBinding` propiedad con el [ `GroupHeaderTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/) definir una plantilla para los encabezados.

### <a name="listview-and-interactivity"></a>ListView e interactividad

Por lo general, una aplicación obtiene interacción del usuario con un `ListView` adjuntando un controlador para el `ItemSelected` o [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) eventos, o estableciendo un enlace de datos en el `SelectedItem` propiedad. Pero algunos tipos de celda (`EntryCell` y `SwitchCell`) permiten la interacción del usuario, y también es posible crear celdas personalizadas que a su vez interactuar con el usuario. El [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 instancias de [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) y permite al usuario cambiar cada color mediante un trío de `Slider` elementos. El programa también hace uso de la [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) en el [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView y MVVM

`ListView` desempeña un papel importante en escenarios MVVM. Cada vez que un `IEnumerable` colección existe en un modelo de vista, a menudo se enlaza a un `ListView`. Además, los elementos de la colección a menudo implementan `INotifyPropertyChanged` para enlazar con las propiedades de una plantilla.

### <a name="a-collection-of-viewmodels"></a>Una colección de ViewModels

Para explorar, el [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca crea varias clases basadas en una [archivo de datos XML](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) y las imágenes de alumnos ficticias de la escuela ficticio.

El [ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) clase se deriva de [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). El [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) clase es una colección de `Student` objetos y también se deriva de `ViewModelBase`. El [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) descarga el archivo XML y reúne todos los objetos.

El [ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) programa utiliza una `ImageCell` para mostrar a los alumnos y sus imágenes en un `ListView`:

[![Captura de pantalla triple de la lista de estudiantes](images/ch19fg18-small.png "lista de estudiantes")](images/ch19fg18-large.png#lightbox "lista de estudiantes")

El [ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) ejemplo agrega un [ `Header` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.Header/) propiedad pero solo se muestra en Android.

### <a name="selection-and-the-binding-context"></a>Selección y el contexto de enlace

El [ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) programa enlaza la `BindingContext` de un `StackLayout` a la `SelectedItem` propiedad de la `ListView`. Esto permite que el programa mostrar información detallada acerca de los estudiantes seleccionados.

### <a name="context-menus"></a>Menús contextuales

Una celda puede definir un menú contextual que se implementa de manera específica de la plataforma. Para crear este menú, agregue [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) objetos a la [ `ContextActions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Cell.ContextActions/) propiedad de la `Cell`.

`MenuItem` define cinco propiedades:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) de tipo `string`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) de tipo `FileImageSource`
- [`IsDestructive`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.IsDestructive/) de tipo `bool`
- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Command/) de tipo `ICommand`
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.CommandParameter/) de tipo `object`

El `Command` y `CommandParameter` propiedades implican que el modelo de vista para cada elemento contiene los métodos para llevar a cabo los comandos de menú deseado. En escenarios no MVVM, `MenuItem` también define un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) eventos.

El [ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) muestra esta técnica. El `Command` propiedad de cada `MenuItem` se enlaza a una propiedad de tipo `ICommand` en la `Student` clase. Establecer el `IsDestructive` propiedad `true` para un `MenuItem` que quita o elimina el objeto seleccionado.

### <a name="varying-the-visuals"></a>Variable los objetos visuales

A veces es conveniente pequeñas variaciones en los objetos visuales de los elementos de la `ListView` en función de una propiedad. Por ejemplo, cuando grado-promedio un estudiante cae por debajo de la 2.0, el [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) ejemplo muestra el nombre de ese estudiante en rojo.
Esto se logra mediante el uso de un convertidor de valores de enlace, [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), en la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="refreshing-the-content"></a>Actualizando el contenido

El `ListView` es compatible con un gesto de lista desplegable para actualizar sus datos. El programa debe establecer el [ `IsPullToRefresh` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsPullToRefreshEnabled/) propiedad `true` para habilitar esta opción. El `ListView` responde al movimiento del menú desplegable estableciendo su [ `IsRefreshing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsRefreshing/) propiedad `true`y generando el [ `Refreshing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.Refreshing/) eventos y (para escenarios de MVVM) al llamar a el `Execute` método de su [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/) propiedad.

Control de código la `Refresh` eventos o la `RefreshCommand` , a continuación, posiblemente actualiza los datos mostrados por el `ListView` y establece `IsRefreshing` a `false`.

El [ **fuente RSS** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) ejemplo muestra cómo utilizar un [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa `RefreshCommand` y `IsRefreshing` propiedades de enlace de datos.

## <a name="the-tableview-and-its-intents"></a>El TableView y sus intentos

Mientras el `ListView` generalmente muestra varias instancias del mismo tipo, el [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) por lo general se centra en proporcionar una interfaz de usuario de varias propiedades de distintos tipos. Cada elemento está asociado a su propio [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) derivado para mostrar la propiedad o para proporcionar una interfaz de usuario.

### <a name="properties-and-hierarchies"></a>Propiedades y jerarquías

`TableView` define solo cuatro propiedades:

- [`Intent`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Intent/) de tipo [ `TableIntent` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableIntent/), una enumeración
- [`Root`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) de tipo [ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/), la propiedad de contenido de `TableView`
- [`RowHeight`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.RowHeight/) de tipo `int`
- [`HasUnevenRows`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.HasUnevenRows/) de tipo `bool`

El `TableIntent` enumeración indica cómo piensa usar el `TableView`:

- [`Data`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Data/)
- [`Form`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Form/)
- [`Settings`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Settings/)
- [`Menu`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Menu/)

Estos miembros también sugieren algunos usos de la `TableView`.

Algunas otras clases implicadas en la definición de una tabla:

- [`TableSectionBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase/) es una clase abstracta que deriva de `BindableObject` y define un [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableSectionBase.Title/) propiedad

- [`TableSectionBase<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase%3CT%3E/) es una clase abstracta que deriva de `TableSectionBase` e implementa `IList<T>` y `INotifyCollectionChanged`

- [`TableSection`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) se deriva de `TableSectionBase<Cell>`

- [`TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/) se deriva de `TableSectionBase<TableSection>`

En resumen, `TableView` tiene un `Root` propiedad que se establece en un `TableRoot` objeto, que es una colección de `TableSection` objetos, cada uno de los cuales es una colección de `Cell` objetos. Una tabla tiene varias secciones, y cada sección tiene varias celdas. La propia tabla puede tener un título, y cada sección puede tener un título. Aunque `TableView` hace uso de `Cell` derivados, que no tiene el uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Un formulario explicaciones

El [ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) ejemplo define un [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modelo de vista, se convierte en una instancia de los cuales el `BindingContext` de la `TableView`. Cada `Cell` derivado en su `TableSection` , a continuación, puede tener enlaces a las propiedades de la `PersonalInformation` clase.

### <a name="custom-cells"></a>Celdas personalizadas

El [ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) ejemplo expande **EntryForm**. El [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) clase incluye una propiedad booleana que rige la aplicabilidad de dos propiedades adicionales. Para estas dos propiedades adicionales, el sistema utiliza un personalizado `PickerCell` tomando como base un [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) y [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) en el [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

Aunque la `IsEnabled` propiedades de los dos `PickerCell` elementos se enlazan a la propiedad booleana de `ProgrammerInformation`, esta técnica no parece funcionar, que solicita el siguiente ejemplo.

### <a name="conditional-sections"></a>Secciones condicionales

El [ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) ejemplo coloca los dos elementos que son condicionales en la selección del elemento booleano en otro `TableSection`. El archivo de código subyacente quita esta sección de la `TableView` o agrega nuevo según la propiedad booleana.

### <a name="a-tableview-menu"></a>Un menú TableView

Otro uso de un `TableView` es un menú. El [ **MenuCommands** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) ejemplo muestra un menú que le permite mover un poco `BoxView` alrededor de la pantalla.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 19 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Ejemplos de capítulo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
