---
title: Rendimiento de ListView
description: "Asegúrese de gran rendimiento con la aplicación basada en ListView."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 2acaef5fd42b867e88fb9b81d401ea752480124a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="listview-performance"></a>Rendimiento de ListView

Al escribir aplicaciones para dispositivos móviles, es importante el rendimiento. Los usuarios han llegado a esperar el desplazamiento suave y tiempos de carga rápida. No puede satisfacer las expectativas de los usuarios de costo que las clasificaciones en el almacén de aplicaciones o en el caso de una aplicación de línea de negocio, costo su organización tiempo y dinero.

Aunque [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) es una eficaz vista para mostrar los datos, tiene algunas limitaciones. Desplazamiento de rendimiento puede verse afectado cuando se utiliza celdas personalizadas, especialmente cuando contienen demasiados niveles de anidación ver jerarquías o usar ciertas diseños que requieren una gran cantidad de medida. Afortunadamente, hay técnicas que puede usar para evitar un bajo rendimiento.

En este artículo se tratan los temas siguientes:

- **[Estrategia de almacenamiento en caché](#cachingstrategy)**
- **[Mejorar el rendimiento de ListView](#improving-performance)**

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Estrategia de almacenamiento en caché

ListView a menudo se usa para mostrar muchos más datos que puede ajustar en la pantalla. Considere la posibilidad de una aplicación de música, por ejemplo. Una biblioteca de canciones puede tener miles de entradas. El enfoque simple, que sería crear una fila por cada canción, podría tener un rendimiento deficiente. Este enfoque desperdicia memoria valiosa y puede ralentizar el desplazamiento a un rastreo. Otro enfoque consiste en crear y destruir filas como datos se desplazan en la vista. Esto requiere la creación de instancias constante y limpieza de los objetos de vista, que puede ser muy lenta.

Para ahorrar memoria, nativo [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) equivalentes para cada plataforma tienen características integradas para volver a usarla filas. Solo las celdas visibles en la pantalla se cargan en memoria y la **contenido** se carga en las celdas existentes. Esto evita que la aplicación de la necesidad de crear instancias de miles de objetos, que permite ahorrar tiempo y memoria.

Permite Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) celda volver a usar a través de la [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) enumeración, que tiene los siguientes valores:

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> **Tenga en cuenta**: la plataforma Universal de Windows (UWP) pasa por alto el [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) estrategia, el almacenamiento en caché porque siempre utiliza el almacenamiento en caché para mejorar el rendimiento. Por lo tanto, de forma predeterminada se comporta como si la [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) almacenamiento en caché estrategia se aplica.

### <a name="retainelement"></a>RetainElement

El [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) estrategia el almacenamiento en caché especifica que la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) generará una celda para cada elemento en la lista y es el valor predeterminado `ListView` comportamiento. Por lo general debe utilizarse en las siguientes circunstancias:

- Cuando cada celda tiene un gran número de enlaces (20-30 +).
- Cuando la plantilla de celda cambia con frecuencia.
- Cuando prueba revela que el `RecycleElement` almacenamiento en caché de resultados de la estrategia en una velocidad de ejecución reducida.

Es importante reconocer las consecuencias de la [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) estrategia el almacenamiento en caché cuando se trabaja con celdas personalizadas. Cualquier código de inicialización de la celda tendrá que ejecutar la creación de cada celda, que puede ser varias veces por segundo. En este caso, técnicas de diseño que estaban correctamente en una página, como uso de varios anidados [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) instancias, se convierten en los cuellos de botella de rendimiento una vez que el programa de instalación y se destruye en tiempo real como el usuario se desplaza.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

El [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estrategia el almacenamiento en caché especifica que la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) intentará minimizar su velocidad de consumo y ejecución de memoria mediante el reciclado de las celdas de la lista. Este modo no siempre ofrece una mejora del rendimiento y pruebas se deben realizar para determinar las mejoras. Sin embargo, generalmente es la alternativa preferida y debe utilizarse en las siguientes circunstancias:

- Cuando cada celda tiene un pequeño número moderado de enlaces.
- Cuando cada celda [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) define todos los datos de celda.
- Cuando cada celda es similar en gran medida, con la plantilla de celda que no cambian.

Durante la virtualización de la celda tendrá su contexto de enlace que se actualiza y, por lo que si una aplicación utiliza este modo debe asegurarse de que las actualizaciones de contexto de enlace se administran correctamente. Todos los datos sobre la celda deben proceder del contexto de enlace o se pueden producir errores de coherencia. Esto puede realizarse mediante el uso de enlace de datos para mostrar datos de la celda. Como alternativa, se deben establecer los datos de las celdas el `OnBindingContextChanged` invalidar, en lugar de en el constructor de la celda personalizado, como se muestra en el ejemplo de código siguiente:

```csharp
public class CustomCell : ViewCell
{
  Image image = null;

  public CustomCell ()
  {
    image = new Image();
    View = image;
  }

  protected override void OnBindingContextChanged ()
  {
    base.OnBindingContextChanged ();

    var item = BindingContext as ImageItem;
    if (item != null) {
      image.Source = item.ImageUrl;
    }
  }
}
```

Para obtener más información, consulte [cambios de contexto de enlace](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

En iOS y Android, si las celdas usar a representadores personalizados, debe asegurarse de que la notificación de cambio de propiedad se implementa correctamente. Cuando las celdas se reutilizan sus valores de propiedad cambiará cuando se actualiza el contexto de enlace a la de una celda disponible, con `PropertyChanged` que se generen eventos. Para obtener más información, consulte [personalizar un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Cuando un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utiliza un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) para seleccionar un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) almacenamiento en caché estrategia no almacena en caché `DataTemplate`s. En su lugar, un `DataTemplate` está seleccionada para cada elemento de datos en la lista.

> [!NOTE]
> **Tenga en cuenta**: el [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estrategia el almacenamiento en caché tiene un requisito previo, introducidas en 2.4 de Xamarin.Forms, que, cuando un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) se le pedirá que seleccione un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que cada `DataTemplate` debe devolver el mismo [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) tipo. Por ejemplo, dada una [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) con un `DataTemplateSelector` que puede devolver `MyDataTemplateA` (donde `MyDataTemplateA` devuelve un `ViewCell` de tipo `MyViewCellA`), o `MyDataTemplateB` (donde `MyDataTemplateB`devuelve un `ViewCell` de tipo `MyViewCellB`), cuando `MyDataTemplateA` devuelto debe devolver `MyViewCellA` o se producirá una excepción.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

El [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) estrategia el almacenamiento en caché se basa en el [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) asegurándose además que, cuando el almacenamiento en caché estrategia un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utiliza un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) para seleccionar un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), `DataTemplate`s se almacenan en caché por el tipo de elemento de la lista. Por lo tanto, `DataTemplate`s se seleccionan una vez por cada tipo de elemento, en lugar de una vez por cada instancia del elemento.

> [!NOTE]
> **Tenga en cuenta**: el [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) estrategia el almacenamiento en caché tiene un requisito previo que la `DataTemplate`s devuelto por la [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) debe utilizar el [ `DataTemplate` ](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) constructor que toma un `Type`.

### <a name="setting-the-caching-strategy"></a>Establecer la estrategia de almacenamiento en caché

El [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) se especifica el valor de enumeración con un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) sobrecarga del constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

En XAML, establezca la `CachingStrategy` atributo tal como se muestra en el código siguiente:

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Esto tiene el mismo efecto que establecer el argumento de estrategia de almacenamiento en caché en el constructor en C#; Tenga en cuenta que no hay ningún `CachingStrategy` propiedad `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Establecer la estrategia de almacenamiento en caché en un control ListView subclase

Establecer el `CachingStrategy` atributo de XAML en una subclase [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) no producirá el comportamiento deseado, porque no hay ningún `CachingStrategy` propiedad `ListView`. Además, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) está habilitado, se producirá el siguiente mensaje de error: **ninguna propiedad, puede enlazar propiedad o evento encontró para 'CachingStrategy'**

La solución a este problema consiste en especificar un constructor de la subclase [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que acepta un [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) parámetro y lo pasa a la clase base:

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

La [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) valor de enumeración se puede especificar desde XAML mediante la `x:Arguments` sintaxis:

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Mejorar el rendimiento de ListView

Existen numerosas técnicas para mejorar el rendimiento de un `ListView`:

-  Enlazar el `ItemsSource` propiedad a una `IList<T>` colección en lugar de un `IEnumerable<T>` colección, porque `IEnumerable<T>` colecciones no admiten el acceso aleatorio.
-  Utilice las celdas integradas (como `TextCell`  /  `SwitchCell` ) en lugar de `ViewCell` siempre que puede.
-  Use menos elementos. Considere, por ejemplo el uso de una sola `FormattedString` etiqueta en lugar de varias etiquetas.
-  Reemplace el `ListView` con un `TableView` al mostrar datos no homogéneos, es decir, datos de tipos diferentes.
-  Limitar el uso de la [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/) método. Si sobreutilizados, disminuirá el rendimiento.
-  En Android, evite establecer un `ListView`de visibilidad de separador de fila o de color después de que se ha creado una instancia, como resultado una reducción del rendimiento de gran tamaño.
-  Evite cambiar el diseño de la celda en función de la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Esto conlleva gastos de inicialización y de diseño grandes.
-  Evite las jerarquías de varios niveles de anidamiento de diseño. Use `AbsoluteLayout` o `Grid` para ayudar a reducir el anidamiento.
-  Evitar específico `LayoutOptions` distinto de `Fill` (relleno es el cheapest de proceso).
-  Evite colocar un `ListView` dentro de un `ScrollView` por las razones siguientes:
  - El `ListView` implementa su propio desplazamiento.
  - El `ListView` no recibirán los movimientos, tal y como se controlarán mediante el elemento primario `ScrollView`.
  - El `ListView` puede presentar un encabezado personalizado y un pie de página que se desplaza con los elementos de la lista, ofreciendo la funcionalidad que la `ScrollView` se usó para. Para obtener más información, consulte [encabezados y pies de página](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Considere la posibilidad de un representador personalizado si necesita un diseño muy específico, complejo presentan en sus celdas.

`AbsoluteLayout` tiene la posibilidad de realizar diseños sin una llamada única medida. Esto resulta muy eficaz para el rendimiento. Si `AbsoluteLayout` no puede ser utilizado, considere la posibilidad de [ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/). Si usa `RelativeLayout`, pasando las restricciones directamente será mucho más rápido que utilizar la API de expresión. Eso es porque la API de la expresión utiliza JIT y en iOS el árbol tiene que interpretarse, que es más lento. La API de la expresión sea adecuada para los diseños de página donde requiere únicamente en el diseño inicial y la rotación, pero en `ListView`, donde se ejecuta constantemente durante el desplazamiento, repercute en el rendimiento.

Creación de un representador personalizado para un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) o sus celdas es un enfoque para reducir el efecto de los cálculos de diseño de rendimiento de desplazamiento. Para obtener más información, consulte [personalizar un control ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) y [personalizar un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Vínculos relacionados

- [Vista personalizada de representador (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [ViewCell de representador personalizado (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
