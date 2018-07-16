---
title: Funcionalidad y los elementos de RecyclerView
description: Información general del Administrador de diseño de RecyclerView, el adaptador y el titular de la vista.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/13/2018
ms.openlocfilehash: 4d55124e9a02489d1f55e900c537939ff3450509
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038513"
---
# <a name="recyclerview-parts-and-functionality"></a>Funcionalidad y los elementos de RecyclerView


`RecyclerView` identificadores de algunas tareas internamente (por ejemplo, el desplazamiento y el reciclado de vistas), pero es esencialmente un administrador que coordina las clases auxiliares para mostrar una colección. `RecyclerView` tareas de delegados para las clases de auxiliares siguientes:

-   **`Adapter`** &ndash; Infla los diseños de elemento (crea el contenido de un archivo de diseño) y enlaza los datos a las vistas que se muestran dentro de un `RecyclerView`. El adaptador también informa de eventos click del elemento.

-   **`LayoutManager`** &ndash; Mide y coloca el elemento vistas dentro de un `RecyclerView` y administra la directiva para el reciclaje de la vista.

-   **`ViewHolder`** &ndash; Busca y almacena las referencias de la vista. El titular de la vista también ayuda a detectar los clics de vista de elemento.

-   **`ItemDecoration`** &ndash; Permite que una aplicación agregar los desplazamientos de diseño y dibujo especiales a vistas específicas para dibujar los separadores entre los elementos, información destacada y límites de agrupación visual.

-   **`ItemAnimator`** &ndash; Define las animaciones que tienen lugar durante las acciones de elemento o como los cambios se realizan en el adaptador.

La relación entre el `RecyclerView`, `LayoutManager`, y `Adapter` clases se muestra en el diagrama siguiente:

![Diagrama de RecyclerView que contiene LayoutManager, utilizando el adaptador para tener acceso a conjunto de datos](parts-and-functionality-images/01-recyclerview-diagram.png)

Como se muestra en esta ilustración, el `LayoutManager` puede considerarse como intermediario entre la `Adapter` y `RecyclerView`. El `LayoutManager` realice llamadas al `Adapter` métodos en nombre de la `RecyclerView`. Por ejemplo, el `LayoutManager` llamadas un `Adapter` método cuando llega el momento de crear una nueva vista para una posición de elemento en particular en el `RecyclerView`. El `Adapter` aumenta el diseño para ese elemento y crea un `ViewHolder` instancia (no mostrado) las referencias a las vistas en esa posición. Cuando el `LayoutManager` llamadas la `Adapter` para enlazar un elemento determinado en el conjunto de datos, el `Adapter` busca los datos para ese elemento, lo recupera del conjunto de datos y lo copia en la vista del elemento asociado.

Cuando se usa `RecyclerView` en su aplicación, es necesario crear tipos derivados de las clases siguientes:

-   **`RecyclerView.Adapter`** &ndash; Proporciona un enlace desde el conjunto de datos de la aplicación (que es específico de la aplicación) a las vistas de elemento que se muestran dentro de la `RecyclerView`. El adaptador sabe cómo asociar cada posición de la vista de elemento en el `RecyclerView` en una ubicación específica en el origen de datos. Además, el adaptador controla el diseño del contenido dentro de la vista de cada elemento individual y crea el titular de la vista para cada vista. El adaptador también informa de eventos de clic de elemento que se detectan mediante la vista de elemento.

-   **`RecyclerView.ViewHolder`** &ndash; Almacena en caché las referencias a las vistas en el archivo de diseño de elemento para que las búsquedas de recursos no se repiten innecesariamente. También organiza el titular de la vista de eventos de clic de elemento que se reenviarán al adaptador cuando un usuario pulsa la vista artículo asociado de la vista del titular de la.

-   **`RecyclerView.LayoutManager`** &ndash; Coloca los elementos dentro de la `RecyclerView`. Puede usar uno de varios administradores de diseño predefinido o puede implementar su propio administrador de diseño personalizado.
    `RecyclerView` los delegados que cambia la directiva de diseño para el Administrador de diseño, por lo que puede conectar un administrador de diseño diferente sin tener que realizar importantes a la aplicación.

También, opcionalmente, puede ampliar las clases siguientes para cambiar la apariencia y comportamiento de `RecyclerView` en la aplicación:

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

Si no extiende `ItemDecoration` y `ItemAnimator`, `RecyclerView` usa las implementaciones predeterminadas. Esta guía no explica cómo crear personalizado `ItemDecoration` y `ItemAnimator` clases; para obtener más información sobre estas clases, vea [RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) y [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Ver cómo funciona de reciclaje

`RecyclerView` no asigna una vista de elementos para todos los elementos del origen de datos. En su lugar, asigna solo el número de vistas de elementos que caben en la pantalla y vuelve a utilizar los diseños de elemento como el usuario se desplaza. Cuando la vista en primer lugar se desplaza fuera de la vista, recorre el proceso de reciclaje que se muestra en la ilustración siguiente:

[![Diagrama que ilustra los seis pasos de reciclado de vista](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  Cuando una vista se desplaza fuera de la vista y ya no se muestra, se convierte en un *descartar vista*.

2.  La vista de recorte se coloca en un grupo y se convierte en un *reciclaje vista*.
    Este grupo es una memoria caché de las vistas que muestran el mismo tipo de datos.

3.  Cuando un nuevo elemento que se va a mostrarse, se toma una vista desde el grupo de reciclaje para su reutilización. Dado que esta vista se debe enlazar volver a por el adaptador antes de mostrarse, se denomina un *dirty vista*.

4.  La vista de datos sucia se recicla: el adaptador busca los datos para el elemento siguiente que se mostrará y copiará estos datos a las vistas para este elemento. Las referencias de estas vistas se recuperan desde el titular de la vista asociado con la vista recicla.

5.  La vista recicla se agrega a la lista de elementos en el `RecyclerView` que están a punto de entrar en la pantalla.

6.  La vista recicla se sitúa en pantalla como el usuario se desplaza el `RecyclerView` al elemento siguiente en la lista. Mientras tanto, otra vista se desplaza fuera de la vista y se recicla según los pasos anteriores.

Además de reutilización de vista de elemento, `RecyclerView` también usa otra optimización de la eficacia: ver titulares. Un *titular de la vista* es una clase sencilla que las memorias caché ver referencias. Cada vez que el adaptador aumenta un archivo de diseño del elemento, también crea un titular de la vista correspondiente. El titular de la vista utiliza `FindViewById` obtener referencias a las vistas dentro del archivo de diseño del elemento aumentada. Estas referencias se usan para cargar datos nuevos en las vistas de cada vez que el diseño se recicla para mostrar los datos nuevos.
 


### <a name="the-layout-manager"></a>El Administrador de diseño

El Administrador de diseño es responsable de colocar elementos en el `RecyclerView` mostrar; lo determina el tipo de presentación (una lista o una cuadrícula), la orientación (si los elementos se muestran verticalmente u horizontalmente) y se deben mostrar los elementos de dirección (en orden normal o en orden inverso). El Administrador de diseño también es responsable de calcular el tamaño y posición de cada elemento en el **RecycleView** mostrar.

El Administrador de diseño tiene un propósito adicional: determina la directiva para cuándo se debe reciclar las vistas de elementos que ya no son visibles para el usuario.
Dado que el Administrador de diseño es consciente de las vistas que son visibles (y cuáles no), está en la mejor posición para decidir cuándo se puede reciclar una vista. Para reciclar una vista, el Administrador de diseño normalmente realiza llamadas al adaptador para reemplazar el contenido de una vista recicla con datos diferentes, como se describió anteriormente en [cómo vista funciona reciclado](#recycling).

Puede extender `RecyclerView.LayoutManager` para crear su propio diseño manager, o bien puede usar un administrador de diseño predefinido. `RecyclerView` Proporciona los siguientes administradores de diseño predefinido:

-   **`LinearLayoutManager`** &ndash; Organiza los elementos en una columna que se puede desplazar verticalmente o en una fila que se puede desplazar horizontalmente.

-   **`GridLayoutManager`** &ndash; Muestra los elementos en una cuadrícula.

-   **`StaggeredGridLayoutManager`** &ndash; Muestra los elementos en una cuadrícula escalonada, donde algunos elementos tienen diferentes altos y anchos.

Para especificar el Administrador de diseño, cree una instancia de su administrador de diseño elegido y páselo a la `SetLayoutManager` método. Tenga en cuenta que *debe* especificar el Administrador de diseño &ndash; `RecyclerView` no seleccione un administrador de diseño predefinido de forma predeterminada.

Para obtener más información acerca del Administrador de diseño, vea el [referencia de clase RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).


### <a name="the-view-holder"></a>El titular de la vista

El titular de la vista es una clase que defina para el almacenamiento en caché las referencias de la vista. El adaptador utiliza estas referencias de vista para enlazar cada vista a su contenido. Todos los elementos de la `RecyclerView` tiene una instancia del titular de la vista asociada que almacena en caché las referencias de vista para ese elemento. Para crear un marcador de vista, use los pasos siguientes para definir una clase para contener el conjunto exacto de vistas por cada elemento:

1.  Subclase `RecyclerView.ViewHolder`.
2.  Implementar un constructor que busca y almacena la vista hace referencia.
3.  Implementar propiedades que puede usar el adaptador para tener acceso a estas referencias.

Un ejemplo detallado de un `ViewHolder` implementación se presenta en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obtener más información acerca de `RecyclerView.ViewHolder`, consulte el [referencia de clase RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="the-adapter"></a>El adaptador

La mayoría de lo "pesado" de la `RecyclerView` código de integración tiene lugar en el adaptador. `RecyclerView` requiere que proporcione un adaptador que se deriva de `RecyclerView.Adapter` para tener acceso al origen de datos y rellenar cada elemento con el contenido del origen de datos.
Dado que el origen de datos es específico de la aplicación, debe implementar la funcionalidad del adaptador que entiende cómo tener acceso a los datos. El adaptador extrae información del origen de datos y los carga en cada elemento en el `RecyclerView` colección.

La ilustración siguiente muestra cómo asigna el adaptador de contenido en un origen de datos a través de los titulares de la vista a las vistas individuales dentro de cada elemento de fila en la `RecyclerView`:

[![Diagrama que ilustra el adaptador que conecta el origen de datos a ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

El adaptador carga cada `RecyclerView` fila con datos de un elemento de fila concreta. Para la posición de fila *P*, por ejemplo, el adaptador busca los datos asociados en la posición *P* dentro del origen de datos y copias de estos datos a la fila de elementos en la posición *P* en el `RecyclerView` colección.
En el plano anterior, por ejemplo, el adaptador utiliza el titular de la vista para buscar las referencias para el `ImageView` y `TextView` en esa posición, por lo que no tiene que llamar repetidamente a `FindViewById` de esas vistas que el usuario se desplaza por la colección y vuelve a utilizar las vistas.

Al implementar un adaptador, debe reemplazar siguiente `RecyclerView.Adapter` métodos:

-   **`OnCreateViewHolder`** &ndash; Crea una instancia el titular de archivo y la vista de diseño de elemento.

-   **`OnBindViewHolder`** &ndash; Carga los datos en la posición especificada en las vistas cuyas referencias se almacenan en el titular de la vista especificada.

-   **`ItemCount`** &ndash; Devuelve el número de elementos del origen de datos.

El Administrador de diseño llama a estos métodos mientras coloca elementos dentro de la `RecyclerView`. 



### <a name="notifying-recyclerview-of-data-changes"></a>RecyclerView notificador de cambios de datos

`RecyclerView` no se actualiza automáticamente su presentación cuando el contenido de sus datos de origen de los cambios; el adaptador debe notificar `RecyclerView` cuando se produce un cambio en el conjunto de datos. Puede cambiar el conjunto de datos en muchas formas distintas. Por ejemplo, puede cambiar el contenido dentro de un elemento o se puede modificar la estructura general de los datos.
`RecyclerView.Adapter` Proporciona una serie de métodos que se pueden llamar para que `RecyclerView` responde a los cambios de datos de la manera más eficaz:

-  **`NotifyItemChanged`** &ndash; Indica que ha cambiado el elemento situado en la posición especificada.

-  **`NotifyItemRangeChanged`** &ndash; Indica que los elementos del intervalo especificado de posiciones han cambiado.

-  **`NotifyItemInserted`** &ndash; Indica que el elemento en la posición especificada se ha insertado recientemente.

-  **`NotifyItemRangeInserted`** &ndash; Indica que los elementos del intervalo especificado de posiciones que se han insertado recientemente.

-  **`NotifyItemRemoved`** &ndash; Indica que se ha quitado el elemento en la posición especificada.

-  **`NotifyItemRangeRemoved`** &ndash; Indica que se han quitado los elementos del intervalo especificado de posiciones.

-  **`NotifyDataSetChanged`** &ndash; Indica que el conjunto de datos ha cambiado (fuerza una actualización completa).

Si sabe exactamente cómo ha cambiado el conjunto de datos, puede llamar a los métodos adecuados anteriores para actualizar `RecyclerView` de la manera más eficaz. Si no sabe exactamente cómo ha cambiado el conjunto de datos, puede llamar a `NotifyDataSetChanged`, que es mucho menos eficiente porque `RecyclerView` debe actualizar todas las vistas que son visibles para el usuario. Para obtener más información acerca de estos métodos, consulte [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

En el tema siguiente, [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), se implementa una aplicación de ejemplo para demostrar los ejemplos de código real de los elementos y funcionalidad se ha descrito anteriormente.


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Ampliando el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
