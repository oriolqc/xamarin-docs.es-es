---
title: Funcionalidad y los elementos de RecyclerView
ms.topic: article
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 89679f7d825422ab34dd77b31a7a3fde60f36e99
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="recyclerview-parts-and-functionality"></a>Funcionalidad y los elementos de RecyclerView


`RecyclerView` identificadores de algunas tareas internamente (por ejemplo, el desplazamiento y el reciclaje de vistas), pero es esencialmente un administrador que coordina las clases auxiliares para mostrar una colección. `RecyclerView` delega una tarea en las clases auxiliares siguientes:

-   **`Adapter`** &ndash; Infla diseños de elemento (crea el contenido de un archivo de diseño) y enlaza los datos a las vistas que se muestran dentro de un `RecyclerView`. El adaptador también notifica los eventos de elemento y haga clic en.

-   **`LayoutManager`** &ndash; Mide y coloca el elemento vistas dentro de un `RecyclerView` y administra la directiva para el reciclado de vista.

-   **`ViewHolder`** &ndash; Busca y almacena las referencias de la vista. El titular de la vista también ayuda a detectar los clics de vista de artículo.

-   **`ItemDecoration`** &ndash; Permite que una aplicación agregar los desplazamientos de diseño y dibujo especiales a determinadas vistas para dibujar los separadores entre los elementos, aspectos destacados y límites de agrupación visual.

-   **`ItemAnimator`** &ndash; Define las animaciones que tienen lugar durante las acciones del elemento o como los cambios se realizan en el adaptador.

La relación entre el `RecyclerView`, `LayoutManager`, y `Adapter` clases está representado en el diagrama siguiente:

![Diagrama de RecyclerView LayoutManager, el uso de adaptador para tener acceso a conjuntos de datos](parts-and-functionality-images/01-recyclerview-diagram.png)

Como se muestra en esta ilustración, el `LayoutManager` puede considerarse como intermediario entre la `Adapter` y `RecyclerView`. El `LayoutManager` realice llamadas al `Adapter` métodos en nombre de la `RecyclerView`. Por ejemplo, el `LayoutManager` llamadas un `Adapter` método cuando llega el momento de crear una nueva vista para una posición de elemento determinado en el `RecyclerView`. El `Adapter` aumenta el diseño para ese elemento y crea un `ViewHolder` instancia (no mostrado) a la memoria caché las referencias a las vistas en esa posición. Cuando el `LayoutManager` llamadas el `Adapter` para enlazar un elemento determinado en el conjunto de datos, la `Adapter` busca los datos para ese elemento, recupera desde el conjunto de datos y lo copia en la vista del elemento asociado.

Al usar `RecyclerView` en su aplicación, es necesario crear tipos derivados de las clases siguientes:

-   **`RecyclerView.Adapter`** &ndash; Proporciona un enlace de conjunto de datos de la aplicación (que es específico de la aplicación) a las vistas de elementos que se muestran en la `RecyclerView`. El adaptador sabe cómo asociar cada posición de la vista de elemento en el `RecyclerView` en una ubicación específica del origen de datos. Además, el adaptador controla la presentación del contenido dentro de cada vista de elemento individual y crea el titular de la vista para cada vista. El adaptador también notifica los eventos de clic de elemento que se detectan mediante la vista de elemento.

-   **`RecyclerView.ViewHolder`** &ndash; Almacena en caché las referencias a las vistas en el archivo de diseño de elemento para que las búsquedas de recursos no se repitan innecesariamente. El titular de la vista también se organiza para que eventos de clic de elemento reenviarlos al adaptador cuando un usuario puntee en vista de la vista-titular elemento asociado.

-   **`RecyclerView.LayoutManager`** &ndash; Coloca los elementos dentro de la `RecyclerView`. Puede usar uno de varios administradores de diseño predefinido o puede implementar su propio administrador de diseño personalizado.
    `RecyclerView` los delegados de la directiva de diseño para el Administrador de diseño, por lo que puede conectar un administrador de diseño diferentes sin tener que hacer significativos cambia a la aplicación.

Además, si lo desea puede extender las clases siguientes para cambiar la apariencia y funcionamiento de `RecyclerView` en la aplicación:

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

Si no se extienden `ItemDecoration` y `ItemAnimator`, `RecyclerView` utiliza las implementaciones predeterminadas. Esta guía no explica cómo crear personalizado `ItemDecoration` y `ItemAnimator` clases; para obtener más información sobre estas clases, vea [RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) y [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Ver cómo funciona el reciclaje

`RecyclerView` no asigna una vista de elemento para cada elemento en el origen de datos. En su lugar, asigna sólo el número de vistas de elementos que caben en la pantalla y vuelve a utilizar los diseños de elemento como el usuario se desplaza. Cuando la vista en primer lugar se desplaza fuera de la vista, pasa por el proceso de reciclaje que se muestra en la ilustración siguiente:

[![Diagrama que ilustra los seis pasos de reciclaje de vista](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  Cuando una vista se desplaza fuera de la vista y ya no aparece, se convierte en una *desperdicio vista*.

2.  La vista de rechazo se coloca en un grupo y se convierte en una *reciclaje vista*.
    Este grupo es una caché de vistas que muestran el mismo tipo de datos.

3.  Una vez un nuevo elemento que se mostrará, se toma una vista desde el grupo de reciclaje para su reutilización. Dado que esta vista debe estar enlazada volver a por el adaptador antes de mostrarse, se denomina un *desfasadas vista*.

4.  La vista desfasada se recicla: el adaptador busca los datos para que se muestre el elemento siguiente y copiará estos datos a las vistas para este elemento. Las referencias de estas vistas se recuperan desde el titular de la vista asociado a la vista recicla.

5.  La vista recicla se agrega a la lista de elementos de la `RecyclerView` que están a punto de entrar en la pantalla.

6.  La vista recicla entra en pantalla como el usuario se desplaza el `RecyclerView` al elemento siguiente en la lista. Mientras tanto, otra vista se desplaza fuera de la vista y se recicla según los pasos anteriores.

Además de reutilización de la vista de artículo, `RecyclerView` también usa otra optimización eficacia: ver titulares. A *titular de la vista* es una clase simple que las memorias caché ven referencias. Cada vez que el adaptador infla un archivo de diseño del elemento, también crea un titular de la vista correspondiente. El titular de la vista utiliza `FindViewById` para obtener referencias a las vistas dentro del archivo de diseño de elemento aumentada. Estas referencias se utilizan para cargar datos nuevos en las vistas cada vez que el diseño se recicla para mostrar nuevos datos.
 


### <a name="the-layout-manager"></a>El Administrador de diseño

El Administrador de diseño es responsable de colocar elementos en el `RecyclerView` mostrar; lo determina el tipo de presentación (una lista o una cuadrícula), la orientación (si los elementos se muestran verticalmente u horizontalmente) y se deben mostrar los elementos de dirección (en orden normal o en el orden inverso). El Administrador de diseño también es responsable de calcular el tamaño y la posición de cada elemento en el **RecycleView** mostrar.

El Administrador de diseño tiene un propósito adicional: determina la directiva para cuándo se debe reciclar vistas de elementos que ya no son visibles para el usuario.
Porque el Administrador de diseño es consciente de las vistas que son visibles (y que no son), está en la mejor posición para decidir cuándo una vista puede reciclar. Para reciclar una vista, el Administrador de diseño normalmente realiza llamadas al adaptador para reemplazar el contenido de una vista recicla con datos diferentes, como se describió anteriormente en [la vista de reciclaje de trabajos](#recycling).

Puede extender `RecyclerView.LayoutManager` para crear un diseño propio administrador, o bien puede usar un administrador de diseño predefinido. `RecyclerView` Proporciona los siguientes administradores de diseño predefinido:

-   **`LinearLayoutManager`** &ndash; Organiza los elementos en una columna que se puede desplazar verticalmente o en una fila que se puede desplazar horizontalmente.

-   **`GridLayoutManager`** &ndash; Muestra los elementos en una cuadrícula.

-   **`StaggeredGridLayoutManager`** &ndash; Muestra elementos en una cuadrícula escalonada, donde algunos elementos tienen anchos y altos diferentes.

Para especificar el Administrador de diseño, crear una instancia del Administrador de su diseño elegido y pasarlo a la `SetLayoutManager` método. Tenga en cuenta que *debe* especificar el Administrador de diseño &ndash; `RecyclerView` no selecciona un administrador de diseño predefinido de forma predeterminada.

Para obtener más información acerca del Administrador de diseño, vea la [referencia de clase RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).


### <a name="the-view-holder"></a>El titular de la vista

El titular de la vista es una clase que defina para almacenar en caché las referencias de la vista. El adaptador utiliza estas referencias de vista para enlazar cada vista a su contenido. Todos los elementos de la `RecyclerView` tenga una instancia de titular de la vista asociada que almacena en caché las referencias de vista para ese elemento. Para crear un titular de la vista, use los pasos siguientes para definir una clase para contener el conjunto exacto de vistas por cada elemento:

1.  Subclase `RecyclerView.ViewHolder`.
2.  Implemente un constructor que busca y almacena las referencias de la vista.
3.  Implementar propiedades que puede usar el adaptador para tener acceso a estas referencias.

Un ejemplo detallado de un `ViewHolder` implementación se presenta en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obtener más información acerca de `RecyclerView.ViewHolder`, consulte el [referencia de clase RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="the-adapter"></a>El adaptador

La mayoría de lo "pesado" de la `RecyclerView` código de integración realiza en el adaptador. `RecyclerView` requiere que proporcione un adaptador que se deriva de `RecyclerView.Adapter` para tener acceso al origen de datos y rellenar todos los elementos en el contenido del origen de datos.
Dado que el origen de datos es específico de la aplicación, debe implementar la funcionalidad del adaptador que entienda cómo obtener acceso a los datos. El adaptador extrae la información del origen de datos y los carga en cada elemento de la `RecyclerView` colección.

La ilustración siguiente muestra cómo el adaptador asigna el contenido de un origen de datos a través de los titulares de la vista vistas individuales dentro de cada elemento de fila en la `RecyclerView`:

[![Diagrama que ilustra el adaptador que conecta el origen de datos a ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

El adaptador carga cada uno `RecyclerView` fila con datos de un elemento de fila concreta. Para la posición de la fila *P*, por ejemplo, el adaptador busca los datos asociados en la posición *P* dentro del origen de datos y copias de estos datos a la fila de elemento en la posición *P* en el `RecyclerView` colección.
En el plano anterior, por ejemplo, el adaptador utiliza el titular de la vista para buscar las referencias para el `ImageView` y `TextView` en esa posición, por lo que no tiene que llamar repetidamente a `FindViewById` de esas vistas como el usuario se desplaza a través de la colección y vuelve a utilizar vistas.

Al implementar un adaptador, es necesario reemplazar los siguientes `RecyclerView.Adapter` métodos:

-   **`OnCreateViewHolder`** &ndash; Crea una instancia el titular de archivo y la vista de diseño de elemento.

-   **`OnBindViewHolder`** &ndash; Carga los datos en la posición especificada en las vistas cuyas referencias se almacenan en el titular de la vista especificada.

-   **`ItemCount`** &ndash; Devuelve el número de elementos del origen de datos.

El Administrador de diseño llama a estos métodos mientras es colocar elementos dentro de la `RecyclerView`. 



### <a name="notifying-recyclerview-of-data-changes"></a>Notificar RecyclerView cambios de datos

`RecyclerView` no se actualiza automáticamente su presentación cuando el contenido de sus datos de origen de cambios; el adaptador debe notificar `RecyclerView` cuando se produce un cambio en el conjunto de datos. Puede cambiar el conjunto de datos de muchas maneras; Por ejemplo, puede cambiar el contenido dentro de un elemento o la estructura general de los datos que se hayan modificado.
`RecyclerView.Adapter` Proporciona una serie de métodos que se pueden llamar para que `RecyclerView` responde a los cambios de datos de la manera más eficaz:

-  **`NotifyItemChanged`** &ndash; Indica que ha cambiado el elemento situado en la posición especificada.

-  **`NotifyItemRangeChanged`** &ndash; Indica que los elementos en el intervalo especificado de posiciones que han cambiado.

-  **`NotifyItemInserted`** &ndash; Indica que se ha recién insertado el elemento en la posición especificada.

-  **`NotifyItemRangeInserted`** &ndash; Indica que los elementos en el intervalo especificado de posiciones recién insertados.

-  **`NotifyItemRemoved`** &ndash; Indica que se ha quitado el elemento en la posición especificada.

-  **`NotifyItemRangeRemoved`** &ndash; Indica que se han quitado los elementos en el intervalo especificado de posiciones.

-  **`NotifyDataSetChanged`** &ndash; Indica que ha cambiado el conjunto de datos (fuerza una actualización completa).

Si sabe exactamente cómo ha cambiado el conjunto de datos, puede llamar a los métodos adecuados anteriores para actualizar `RecyclerView` de la manera más eficaz. Si no sabe exactamente cómo ha cambiado el conjunto de datos, puede llamar a `NotifyDataSetChanged`, lo que resulta mucho más eficaz porque `RecyclerView` debe actualizar todas las vistas que son visibles para el usuario. Para obtener más información acerca de estos métodos, consulte [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

En el tema siguiente, [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), se implementa una aplicación de ejemplo donde se muestran ejemplos de código reales de los elementos y la funcionalidad se ha descrito anteriormente.


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Siguiendo con el ejemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
