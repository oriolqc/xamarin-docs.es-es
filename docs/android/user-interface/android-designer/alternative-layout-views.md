---
title: Vistas de diseños alternativos
description: En este tema se explica cómo pueden ser los diseños con control de versiones mediante el uso de calificadores de recursos. Por ejemplo, puede haber una versión de un diseño que solo se usa cuando el dispositivo está en modo horizontal y una versión de diseño que es solo para el modo vertical.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 03b80d3fb1ed7c8db108f86b3b3923c20e1d908f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109983"
---
# <a name="alternative-layout-views"></a>Vistas de diseños alternativos

_Este tema explica cómo pueden los diseños de versión mediante el uso de calificadores de recursos. Por ejemplo, cree una versión de un diseño que solo se usa cuando el dispositivo está en modo horizontal y una versión de diseño que es solo para el modo vertical._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Crear diseños alternativos

Al hacer clic en el **vista alternativa de diseño** icono (a la izquierda del **dispositivo**), se abre un panel de vista previa para obtener una lista de los diseños alternativos disponibles en el proyecto. Si no hay ningún diseños alternativos, el **predeterminado** se presenta la vista: 

[![Panel de vista de diseño alternativo](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "panel de vista de diseño alternativo")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Al hacer clic en el signo más verde junto a **nueva versión**, **crear variación de diseño** abre el cuadro de diálogo para que pueda seleccionar los calificadores de recursos de esta variación de diseño: 

[![Crear variación de diseño](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "crear variación de diseño")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

En el ejemplo siguiente, el calificador de recursos de **orientación de pantalla** está establecido en **horizontal**y el **tamaño de la pantalla** cambia a **grande**. Esto crea una nueva versión de diseño denominada **grandes land**:

[![Variación grande land](alternative-layout-views-images/vs/03-large-land-sml.png "variación grande land")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Tenga en cuenta que el panel de vista previa de la izquierda muestra los efectos de las selecciones del calificador de recursos. Al hacer clic en **agregar** crea el diseño alternativo y se activa el diseñador para que el diseño. El **vista de diseño alternativa** panel de vista previa indica qué diseño se carga en el diseñador mediante un pequeño puntero derecho como se indica en la captura de pantalla siguiente: 

[![Indicador de distribución de carga](alternative-layout-views-images/vs/04-new-layout-sml.png "indicador de distribución de carga")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)


## <a name="editing-alternative-layouts"></a>Edición de diseños alternativos

Al crear diseños alternativos, suele ser deseable para realizar un único cambio que se aplica a todas las versiones bifurcadas de un diseño. Por ejemplo, es posible que desee cambiar el texto del botón amarillo en todos los diseños. Si tiene un gran número de diseños y deba propagar un único cambio a todas las versiones, mantenimiento puede volverse rápidamente complicado y propensas a errores.

Para simplificar el mantenimiento de varias versiones de diseño, el diseñador proporciona una **editar varios** modo que propaga los cambios a través de uno o varios diseños. Cuando hay más de un diseño, el **editar varios** icono se muestra: 

[![Icono de edición múltiple](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "icono de edición múltiple")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Al hacer clic en el **editar varios** icono, aparecen líneas que indican que los diseños están vinculados (como se muestra a continuación); es decir, al realizar un cambio en un diseño, dicho cambio se propaga a los diseños vinculados. Puede desvincular todos los diseños, haga clic en el icono con un círculo indicado en la siguiente captura de pantalla: 

[![Desvincular todos los diseños](alternative-layout-views-images/vs/06-multi-linked-sml.png "desvincular todos los diseños")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Si tiene más de dos diseños, selectivamente puede alternar el botón Editar a la izquierda de cada vista previa del diseño para determinar qué diseños están vinculados entre sí. Por ejemplo, si desea realizar un único cambio que se propaga a la primera y última de tres diseños, desea desvincular el diseño central como se muestra aquí: 

[![Desvincular intermedio diseño](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "desvincular intermedio diseño")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

En este ejemplo, realiza un cambio en el **predeterminado** o **largo** diseño se propagará al diseño del, pero no a la **grandes land** diseño.

### <a name="multi-edit-example"></a>Ejemplo de edición múltiple 

En general, al realizar un cambio en un diseño, ese mismo cambio se propaga a todos los demás diseños vinculados. Por ejemplo, al agregar un nuevo `TextView` widget a la **predeterminado** diseño y cambiar su texto de string a `Portrait` hará que el mismo cambio para diseños vinculados. Este es su aspecto la **predeterminado** diseño: 

[![Agregar TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "agregar TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
El `TextView` también se agrega a la **grandes land** la vista Diseño porque está vinculado a la **predeterminado** diseño: 

[![Horizontal TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "horizontal TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
Pero ¿qué ocurre si desea realizar un cambio local en un solo diseño (es decir, no desea que los cambios se propaguen a cualquiera de los otros diseños)? Para ello, debe desvincular el diseño que desee cambiar antes de modificarlo, tal como se explica a continuación. 

### <a name="making-local-changes"></a>Realización de cambios locales 

Supongamos que queremos ambos diseños tener agregado `TextView`, pero también queremos cambiar la cadena de texto en el **grandes land** diseño `Landscape` lugar `Portrait`. Si se realiza este cambio a **grandes land** mientras ambos diseños están vinculados, el cambio se propagará a la **predeterminado** diseño. Por lo tanto, en primer lugar, debemos desvincular los dos diseños antes de realizar el cambio. Cuando se modifica el texto en **grandes land** a `Landscape`, el Diseñador de marca este cambio con un marco rojo para indicar que el cambio es local para el **grandes land** diseño y es *no* volver a propagarse a la **predeterminado** diseño: 

[![Cambio local](alternative-layout-views-images/vs/10-local-change-sml.png "cambio Local")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
Al hacer clic en el **predeterminado** diseño para verlo, el `TextView` todavía se establece la cadena de texto en `Portrait`. 

## <a name="handling-conflicts"></a>Control de conflictos 

Si decide cambiar el color del texto en el **predeterminado** diseño a verde, aparecerá un icono de advertencia en el diseño vinculado. Al hacer que el diseño, abre el diseño para mostrar el conflicto. Se resalta el widget que provocó el conflicto con un marco rojo y se muestra el siguiente mensaje: *cambios recientes han generado conflictos en este diseño alternativo*. 

[![Cambio en conflicto](alternative-layout-views-images/vs/11-conflicting-change-sml.png "cambio en conflicto")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
Un *cuadro conflicto* se muestra a la derecha del widget para explicar el conflicto: 

[![Advertencia de conflicto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

El cuadro de conflicto muestra la lista de propiedades que han cambiado y enumera sus valores. Al hacer clic en **omitir conflicto** solo se aplica el cambio de propiedad a este widget. Al hacer clic en **aplicar** se aplica el cambio de propiedad para este widget, así como para el widget homólogo en vinculado **predeterminado** diseño. Si se aplican a todos los cambios de propiedad, se descarta automáticamente el conflicto. 

### <a name="view-group-conflicts"></a>Ver conflictos de grupo 

Los cambios de propiedades no son la única fuente de conflictos. Al insertar o extraer widgets, se pueden detectar conflictos. Por ejemplo, cuando el **grandes land** se desvincula el diseño de la **predeterminado** diseño y el `TextView` en el **grandes land** diseño de arrastrar y colocar encima de la `Button`, el Diseñador de marca del widget movido para indicar el conflicto:

[![Ver conflictos de grupo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Ver conflictos de grupo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
Sin embargo, no hay ningún marcador en el `Button`. Aunque la posición de la `Button` ha cambiado, el `Button` no muestra cambios aplicados a los que son específicos de la **grandes land** configuración. 

Si un `CheckBox` se agrega a la **predeterminado** diseño, se genera otro conflicto y se muestra un icono de advertencia sobre la **grandes land** diseño: 

[![Conflicto de casilla de verificación](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "conflicto de casilla de verificación")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
Al hacer clic en el **grandes land** diseño revela el conflicto. Muestra el siguiente mensaje: *cambios recientes han generado conflictos en este diseño alternativo*: 

[![Conflictos de diseño ALT](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "conflictos de diseño Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

Además, en el cuadro de conflicto se muestra el mensaje siguiente:

[![Mensaje de conflicto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Agregar el `CheckBox` provoca un conflicto porque los **grandes land** diseño tiene cambios en el `LinearLayout` que lo contiene. Sin embargo, en este caso el cuadro de conflictos muestra el widget que se acaba de insertar en el **predeterminado** diseño (el `CheckBox`).

Si hace clic en **omitir conflicto**, el diseñador resuelve el conflicto, lo que permite el widget que aparece en el cuadro de conflictos para poder arrastrar y colocar en el diseño que falta el widget (en este caso, el **grandes land**  diseño): 

[![Resolver conflictos de grupo](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "Resolver conflicto de grupo")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Tal como se muestra en el ejemplo anterior con el `Button`, el `CheckBox` no tiene un marcador de cambio de color rojo, porque sólo la `LinearLayout` tiene cambios que se aplicaron en el **grandes land** diseño.



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Crear diseños alternativos

Al hacer clic en el **vista alternativa de diseño** icono (a la izquierda del **dispositivo**), se abre un panel de vista previa para obtener una lista de los diseños alternativos disponibles en el proyecto. Si no hay ningún diseños alternativos, el **predeterminado** se presenta la vista: 

[![Panel de vista de diseño alternativo](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Al hacer clic en el signo más verde junto a **nueva versión**, **crear variación de diseño** abre el cuadro de diálogo para que pueda seleccionar los calificadores de recursos de esta variación de diseño: 

[![Crear variación de diseño](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

En el ejemplo siguiente, el calificador de recursos de **orientación de pantalla** está establecido en **horizontal**y el **tamaño de la pantalla** cambia a **grande**. Esto crea una nueva versión de diseño denominada **grandes land**:

[![Variación de la tierra de grande](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Tenga en cuenta que el panel de vista previa de la izquierda muestra los efectos de las selecciones del calificador de recursos. Al hacer clic en **agregar** crea el diseño alternativo y se activa el diseñador para que el diseño. El **vista de diseño alternativa** panel de vista previa indica qué diseño se carga en el diseñador mediante un pequeño puntero derecho como se indica en la captura de pantalla siguiente: 

[![Indicador de distribución de carga](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Edición de diseños alternativos

Al crear diseños alternativos, suele ser deseable para realizar un único cambio que se aplica a todas las versiones bifurcadas de un diseño. Por ejemplo, es posible que desee cambiar el texto del botón amarillo en todos los diseños. Si tiene un gran número de diseños y deba propagar un único cambio a todas las versiones, mantenimiento puede volverse rápidamente complicado y propensas a errores.

Para simplificar el mantenimiento de varias versiones de diseño, el diseñador proporciona una **editar varios** modo que propaga los cambios a través de uno o varios diseños. Cuando hay más de un diseño, el **editar varios** icono se muestra: 

[![Icono de edición múltiple](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Al hacer clic en el **editar varios** icono, aparecen líneas que indican que los diseños están vinculados (como se muestra a continuación); es decir, al realizar un cambio en un diseño, dicho cambio se propaga a los diseños vinculados. Puede desvincular todos los diseños, haga clic en el icono con un círculo indicado en la siguiente captura de pantalla: 

[![Desvincular todos los diseños](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Si tiene más de dos diseños, selectivamente puede alternar el botón Editar a la izquierda de cada vista previa del diseño para determinar qué diseños están vinculados entre sí. Por ejemplo, si desea realizar un único cambio que se propaga a la primera y última de tres diseños, desea desvincular el diseño central como se muestra aquí: 

[![Desvincular el diseño central](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

En este ejemplo, realiza un cambio en el **predeterminado** o **largo** diseño se propagará a otro diseño, pero no a la **grandes land** diseño. 

### <a name="multi-edit-example"></a>Ejemplo de edición múltiple 

En general, al realizar un cambio en un diseño, ese mismo cambio se propaga a todos los demás diseños vinculados. Por ejemplo, al agregar un nuevo `TextView` widget a la **predeterminado** diseño y cambiar su texto de string a `Portrait` hará que el mismo cambio para diseños vinculados. Este es su aspecto la **predeterminado** diseño: 

[![Agregar vista de texto](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

El `TextView` también se agrega a la **grandes land** la vista Diseño porque está vinculado a la **predeterminado** diseño: 

[![TextView horizontal](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
Pero ¿qué ocurre si desea realizar un cambio local en un solo diseño (es decir, no desea que los cambios se propaguen a cualquiera de los otros diseños)? Para ello, debe desvincular el diseño que desee cambiar antes de modificarlo, tal como se explica a continuación. 

### <a name="making-local-changes"></a>Realización de cambios locales 

Supongamos que queremos ambos diseños tener agregado `TextView`, pero también queremos cambiar la cadena de texto en el **grandes land** diseño `Landscape` lugar `Portrait`. Si se realiza este cambio a **grandes land** mientras ambos diseños están vinculados, el cambio se propagará a la **predeterminado** diseño. Por lo tanto, en primer lugar, debemos desvincular los dos diseños antes de realizar el cambio. Cuando se modifica el texto en **grandes land** a `Landscape`, el Diseñador de marca este cambio con un marco rojo para indicar que el cambio es local para el **grandes land** diseño y es *no* volver a propagarse a la **predeterminado** diseño: 

[![Cambio local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Al hacer clic en el **predeterminado** diseño para verlo, el `TextView` todavía se establece la cadena de texto en `Portrait`. 

## <a name="handling-conflicts"></a>Control de conflictos 

Si decide cambiar el color del texto en el **predeterminado** diseño a verde, aparecerá un icono de advertencia en el diseño vinculado. Al hacer que el diseño, abre el diseño para mostrar el conflicto. Se resalta el widget que provocó el conflicto con un marco rojo y se muestra el siguiente mensaje: *cambios recientes han generado conflictos en este diseño alternativo*. 

[![Cambio conflictivo](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Un *cuadro conflicto* se muestra a la derecha del widget para explicar el conflicto: 

[![Advertencia de conflicto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

El cuadro de conflicto muestra la lista de propiedades que han cambiado y enumera sus valores. Al hacer clic en **omitir conflicto** solo se aplica el cambio de propiedad a este widget. Al hacer clic en **aplicar** se aplica el cambio de propiedad para este widget, así como para el widget homólogo en vinculado **predeterminado** diseño. Si se aplican a todos los cambios de propiedad, se descarta automáticamente el conflicto. 

### <a name="view-group-conflicts"></a>Ver conflictos de grupo 

Los cambios de propiedades no son la única fuente de conflictos. Al insertar o extraer widgets, se pueden detectar conflictos. Por ejemplo, cuando el **grandes land** se desvincula el diseño de la **predeterminado** diseño y el `TextView` en el **grandes land** diseño de arrastrar y colocar encima de la `Button`, el Diseñador de marca del widget movido para indicar el conflicto:

[![Conflicto de vista de grupo](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
Sin embargo, no hay ningún marcador en el `Button`. Aunque la posición de la `Button` ha cambiado, el `Button` no muestra cambios aplicados a los que son específicos de la **grandes land** configuración. 

Si un `CheckBox` se agrega a la **predeterminado** diseño, se genera otro conflicto y se muestra un icono de advertencia sobre la **grandes land** diseño: 

[![Conflicto de casilla de verificación](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
Al hacer clic en el **grandes land** diseño revela el conflicto. Muestra el siguiente mensaje: *cambios recientes han generado conflictos en este diseño alternativo*. 

[![Conflictos de diseño ALT](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
Además, en el cuadro de conflicto se muestra el mensaje siguiente:

[![Mensaje de conflicto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Agregar el `CheckBox` provoca un conflicto porque los **grandes land** diseño tiene cambios en el `LinearLayout` que lo contiene. Sin embargo, en este caso el cuadro de conflictos muestra el widget que se acaba de insertar en el **predeterminado** diseño (el `CheckBox`).

Si hace clic en **omitir conflicto**, el diseñador resuelve el conflicto, lo que permite el widget que aparece en el cuadro de conflictos para poder arrastrar y colocar en el diseño que falta el widget (en este caso, el **grandes land**  diseño): 

[![Resolver conflictos de grupo](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
Tal como se muestra en el ejemplo anterior con el `Button`, el `CheckBox` no tiene un marcador de cambio de color rojo, porque sólo la `LinearLayout` tiene cambios que se aplicaron en el **grandes land** diseño.


-----


### <a name="conflict-persistence"></a>Persistencia de conflicto

Los conflictos se almacenan en el archivo de diseño como comentarios XML, como se muestra aquí:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Por lo tanto, cuando se cierra y se vuelve a abrir un proyecto, todos los conflictos igualmente estar allí &ndash; incluso a los que se han omitido.



