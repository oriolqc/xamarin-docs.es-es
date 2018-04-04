---
title: Orígenes de datos de ListView
description: Obtenga información acerca de cómo rellenar ListView con datos.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6f0553f2887d4acb1015da71395be3f316e9acee
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="listview-data-sources"></a>Orígenes de datos de ListView

ListView se utiliza para mostrar listas de datos. Se obtendrá información acerca de cómo rellenar un control ListView con los datos y cómo podemos enlazar en el elemento seleccionado.

- **[Establecer ItemsSource](#ItemsSource)**  &ndash; usa una lista simple o una matriz.
- **[Enlace de datos](#Data_Binding)**  &ndash; establece una relación entre un modelo y la vista de lista. El enlace es ideal para el patrón MVVM.

## <a name="itemssource"></a>ItemsSource
ListView se rellena con datos mediante el `ItemsSource` propiedad, que puede aceptar cualquier colección que implementa `IEnumerable`. La manera más sencilla para rellenar un `ListView` implica el uso de una matriz de cadenas:

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};

//monochrome will not appear in the list because it was added
//after the list was populated.
listView.ItemsSource.Add("monochrome");
```

![](data-and-databinding-images/itemssource-simple.png "ListView Mostrar lista de cadenas")

El enfoque anterior se llenará la `ListView` con una lista de cadenas. De forma predeterminada, `ListView` llamará `ToString` y mostrar el resultado en un `TextCell` para cada fila. Para personalizar cómo se muestran los datos, vea [aspecto de la celda](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Dado que `ItemsSource` se ha enviado a una matriz, no se actualizará el contenido como los cambios de lista o matriz subyacentes. Si desea que la vista de lista se actualizan automáticamente según elementos se agregan, quitan o cambian en la lista subyacente, debe utilizar un `ObservableCollection`. [`ObservableCollection`](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) se define en `System.Collections.ObjectModel` y es exactamente igual que `List`, excepto en que puede notificar a `ListView` de los cambios:

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>Enlace de datos
Enlace de datos es el "adherencia" que enlaza las propiedades de un objeto de interfaz de usuario a las propiedades de un objeto CLR, como una clase en el modelo de vista. Enlace de datos es útil porque simplifica el desarrollo de interfaces de usuario mediante la sustitución mucha aburrido código reutilizable.

Enlace de datos funciona manteniendo objetos sincronizados como cambian sus valores enlazados. En lugar de tener que escribir controladores de eventos para cada vez que cambia el valor de un control, establecer el enlace y habilita el enlace en el modelo de vista.

Para obtener más información sobre el enlace de datos, vea [conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) que forma parte de cuatro de los [serie de artículos de conceptos básicos de XAML de Xamarin.Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Enlazar celdas
Propiedades de las celdas (y los elementos secundarios de las celdas) se pueden enlazar a propiedades de objetos en el `ItemsSource`. Por ejemplo, un control ListView podría utilizarse para presentar una lista de empleados con imágenes.

La clase employee:

```csharp
public class Employee{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>` se crea y se establece como el `ListView`del `ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public EmployeeListPage()
{
  //defined in XAML to follow
  EmployeeView.ItemsSource = employees;
  ...
}
```

La lista se rellena con datos:

```csharp
public EmployeeListPage()
{
  ...
  employees.Add(new Employee{ DisplayName="Rob Finnerty"});
  employees.Add(new Employee{ DisplayName="Bill Wrestler"});
  employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
  employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
  employees.Add(new Employee{ DisplayName="Sheri Spruce"});
  employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

El siguiente fragmento muestra un `ListView` enlazada a una lista de empleados:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
Title="Employee List">
  <ListView x:Name="EmployeeView">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Tenga en cuenta que el enlace es el programa de instalación en el código para simplificar, aunque se pudo enlazar en XAML.

El bit anterior de XAML define una `ContentPage` que contiene un `ListView`. El origen de datos de la `ListView` se establece a través de la `ItemsSource` atributo. El diseño de cada fila en la `ItemsSource`se define dentro de la `ListView.ItemTemplate` elemento.

Éste es el resultado:

![](data-and-databinding-images/bound-data.png "ListView usando el enlace de datos")

### <a name="binding-selecteditem"></a>Enlace SelectedItem

A menudo es conveniente enlazar con el elemento seleccionado de un `ListView`, en lugar de usar un controlador de eventos para responder a cambios. Para hacer esto en XAML, enlazar la `SelectedItem` propiedad:

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

Suponiendo que `listView`del `ItemsSource` es una lista de cadenas, `SomeLabel` tendrán su propiedad de texto enlazada a la `SelectedItem`.



## <a name="related-links"></a>Vínculos relacionados

- [Dos forma enlace (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [notas de la versión 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [notas de la versión 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
