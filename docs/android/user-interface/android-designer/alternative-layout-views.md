---
title: "Vistas de diseño alternativo"
description: "Este tema explica cómo pueden estar diseños con control de versiones mediante los calificadores de recursos. Por ejemplo, puede haber una versión de un diseño que solo se usa cuando el dispositivo esté en modo horizontal y una versión de diseño es solo para modo vertical."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: c2df60a79ea3b5a0ff226cfaade0440db13fd5ea
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="alternative-layout-views"></a>Vistas de diseño alternativo

_Este tema explica cómo pueden estar diseños con control de versiones mediante los calificadores de recursos. Por ejemplo, puede haber una versión de un diseño que solo se usa cuando el dispositivo esté en modo horizontal y una versión de diseño es solo para modo vertical._


## <a name="creating-alternative-layouts"></a>Crear diseños alternativos

Al hacer clic en el **otra vista de diseño** icono (a la izquierda del **dispositivo**), se abre un panel de vista previa para obtener una lista de los diseños alternativos disponibles en el proyecto. Si no hay ningún diseños alternativos, el **predeterminado** se presenta la vista: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Panel de vista de diseño alternativa](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "panel de vista de diseño alternativa")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Panel de vista de diseño alternativa](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

-----

Al hacer clic en el signo más verde junto al **nueva versión**, **crear variaciones de diseño** abre el cuadro de diálogo para que pueda seleccionar los calificadores de recursos para esta variación de diseño: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Crear variaciones de diseño](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "crear variaciones de diseño")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Crear variaciones de diseño](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

-----


En el ejemplo siguiente, el calificador de recursos de **orientación de pantalla** está establecido en **horizontal**y el **tamaño de pantalla** se cambia a **grande**. Esto crea una nueva versión de diseño denominada **grandes terrenos**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Variación de tierra grande](alternative-layout-views-images/vs/03-large-land-sml.png "variación de tierra de gran tamaño")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Variación de la tierra de gran tamaño](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

-----


Tenga en cuenta que el panel de vista previa de la izquierda muestra los efectos de las selecciones de calificador de recurso. Haga clic en **agregar** crea el diseño alternativo y el diseñador se activa en ese diseño. El **la vista de diseño alternativa** panel de vista previa indica qué diseño se carga en el diseñador a través de un pequeño puntero derecho como se indica en la siguiente captura de pantalla: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Indicador de diseño cargado](alternative-layout-views-images/vs/04-new-layout-sml.png "indicador de distribución de carga")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Indicador de distribución de carga](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

-----



## <a name="editing-alternative-layouts"></a>Editar diseños alternativos

Al crear diseños alternativos, a menudo es conveniente realizar un único cambio que se aplica a todas las versiones bifurcadas de un diseño. Por ejemplo, puede que desee cambiar el texto del botón a amarillo en todos los diseños. Si tiene un gran número de diseños, mantenimiento puede volverse rápidamente complicadas y propensa a errores si se necesita propagar un cambio único en todas las versiones. 

Para simplificar el mantenimiento de varias versiones de diseño, el diseñador proporciona una **editar varios** modo que propaga los cambios en uno o varios diseños. Cuando hay más de un diseño, el **editar varios** icono se muestra: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Icono de edición múltiple](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "icono de edición múltiple")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Icono de edición múltiple](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

-----


Al hacer clic en el **editar varios** icono, aparecen las líneas que indican que los diseños están vinculados (como se muestra a continuación); es decir, cuando se realiza un cambio en un diseño, ese cambio se propaga a los diseños vinculados. Puede desvincular todos los diseños, haga clic en el icono con un círculo que se indica en la siguiente captura de pantalla: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Desvincular todos los diseños](alternative-layout-views-images/vs/06-multi-linked-sml.png "desvincular todos los diseños")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Desvincular todos los diseños](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

-----


Si tiene más de dos diseños, selectivamente puede alternar el botón de edición a la izquierda de cada vista previa del diseño para determinar qué diseños están vinculados entre sí. Por ejemplo, si desea realizar un solo cambio se propaga a la primera y última de tres diseños, ¿desvincular el diseño intermedio como se muestra aquí: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Desvincular intermedio diseño](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "desvincular intermedio diseño")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Desvincular diseño central](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)
 
-----
 

En este ejemplo, un cambio realizado en la **predeterminado** o **largo** diseño se propagará al otro diseño pero no a la **grandes terrenos** diseño. 



### <a name="multi-edit-example"></a>Ejemplo de edición múltiple 

En general, al realizar un cambio en un diseño, ese mismo cambio se propaga a todos los demás diseños vinculados. Por ejemplo, al agregar un nuevo `TextView` widget para la **predeterminado** diseño y cambiar el texto de cadena para `Portrait` hará que los mismos cambios que se realizan en diseños vinculados. Este es su aspecto la **predeterminado** diseño: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Agregar TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "agregar TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Agregar TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)
 
-----
 

El `TextView` también se agrega a la **grandes terrenos** la vista Diseño porque está vinculado a la **predeterminado** diseño: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Panorama TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "panorama TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![TextView horizontal](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
-----
 

Pero ¿qué ocurre si desea realizar un cambio que es local para un solo diseño (es decir, no desea que el cambio se propagará a cualquiera de los otros diseños)? Para ello, debe desvincular el diseño que desea cambiar antes de modificarlo, tal y como se explica a continuación. 



### <a name="making-local-changes"></a>Realización de cambios locales 

Supongamos que queremos ambos diseños tener agregado `TextView`, pero también queremos cambiar la cadena de texto en el **grandes terrenos** diseño a `Landscape` en lugar de `Portrait`. Si se realiza este cambio **grandes terrenos** mientras ambos diseños están vinculados, el cambio se propagará a la **predeterminado** diseño. Por lo tanto, primero se deben desvincular los dos diseños antes de realizar el cambio. Cuando se modifica el texto en **grandes de terrenos** a `Landscape`, el Diseñador de marca este cambio con un marco rojo para indicar que el cambio es local para el **grandes de terrenos** diseño y es *no* volver a propagarse a la **predeterminado** diseño: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cambio local](alternative-layout-views-images/vs/10-local-change-sml.png "cambio Local")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cambio local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)
 
-----
 

Al hacer clic en el **predeterminado** diseño para verlo, el `TextView` todavía se establece la cadena de texto en `Portrait`. 



## <a name="handling-conflicts"></a>Controlar los conflictos 

Si decide cambiar el color del texto de la **predeterminado** diseño a verde, aparecerá un icono de advertencia en el diseño vinculado. Haga clic en ese diseño abrir el diseño para que muestre el conflicto. Se resalta el widget que provocó el conflicto con un marco rojo y se muestra el siguiente mensaje: *cambios recientes han producido conflictos en este diseño alternativo*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cambio en conflicto](alternative-layout-views-images/vs/11-conflicting-change-sml.png "cambio en conflicto")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cambio conflictivo](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)
 
-----
 

A *cuadro conflicto* se muestra a la derecha del widget para explicar el conflicto: 

[![Advertencia de conflicto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

El cuadro de conflictos muestra la lista de propiedades que han cambiado y muestra sus valores. Haga clic en **omitir conflicto** solo se aplica el cambio de propiedad a este widget. Haga clic en **aplicar** se aplica el cambio de propiedad a este widget, así como para el widget correspondiente en la que está vinculada **predeterminado** diseño. Si se aplican todos los cambios de propiedad, se descarta automáticamente el conflicto. 


### <a name="view-group-conflicts"></a>Grupo ver conflictos 

Cambios de propiedades no son la única fuente de conflictos. Se pueden detectar conflictos al insertar o quitar widgets. Por ejemplo, cuando el **grandes de terrenos** diseño se desvincula de la **predeterminado** diseño y el `TextView` en el **grandes de terrenos** diseño de arrastrar y colocar encima de la `Button`, el Diseñador de marca el widget movido para indicar el conflicto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ver conflictos de grupo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Ver conflictos de grupo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ver conflictos de grupo](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
-----
 

Sin embargo, no hay ningún marcador en el `Button`. Aunque la posición de la `Button` ha cambiado, el `Button` no muestra ningún cambio aplicado que es específico de la **grandes terrenos** configuración. 

Si un `CheckBox` se agrega a la **predeterminado** diseño, se genera otro conflicto y se muestra un icono de advertencia sobre la **grandes terrenos** diseño: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Conflicto de casilla de verificación](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "conflicto de casilla de verificación")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Conflicto de casilla de verificación](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
-----
 

Al hacer clic en el **grandes terrenos** diseño revela el conflicto. Se muestra el siguiente mensaje: *cambios recientes han producido conflictos en este diseño alternativo*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Conflicto de diseño ALT](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "conflictos de diseño Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Conflicto de diseño ALT](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
-----
 

Además, el cuadro de conflictos muestra el mensaje siguiente:

[![Mensaje de conflicto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Agregar el `CheckBox` produce un conflicto porque los **grandes de terrenos** diseño tiene cambios el `LinearLayout` que lo contiene. Sin embargo, en este caso el cuadro de conflictos muestra el widget que se acaba de insertar en la **predeterminado** diseño (el `CheckBox`).

Si hace clic en **omitir conflicto**, el diseñador resuelve el conflicto, lo que permite el widget muestra en el cuadro de conflictos para poder arrastrar y colocar en el diseño donde el widget es que faltan (en este caso, el **grandes tierra**  diseño): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Resolver conflictos de grupo](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "resolver conflictos de grupo")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Resolver conflictos de grupo](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
-----
 

Tal como se muestra en el ejemplo anterior con el `Button`, el `CheckBox` no tiene un marcador de cambio de color rojo porque sólo la `LinearLayout` tiene cambios que se aplicaron en el **grandes terrenos** diseño.



### <a name="conflict-persistence"></a>Persistencia de conflictos

Está en conflicto se conserva en el archivo de diseño como comentarios XML, como se muestra aquí:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Por lo tanto, cuando se cierra y vuelve a abrir un proyecto, todos los conflictos seguirá estando ahí &ndash; incluso a los que se han ignorado.

