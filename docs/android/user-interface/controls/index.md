---
title: Controles de Android (Widgets)
description: Bloques de creación para crear Interfaces de usuario de Xamarin.Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 28418c3b3fedcd24963008eb3b59ffa782d791f1
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="android-controls-widgets"></a>Controles de Android (Widgets)

Xamarin.Android expone todos los controles de interfaz de usuario nativa (widgets) proporcionados por Android. Estos controles pueden agregarse fácilmente a las aplicaciones de Xamarin.Android mediante el Diseñador de Android o mediante programación a través de los archivos de diseño XML. Independientemente del método que elija, Xamarin.Android expone todas las propiedades del objeto de interfaz de usuario y los métodos en C#. Las siguientes secciones presentan los controles de interfaz de usuario de Android más comunes y explican cómo incorporarlos a aplicaciones Xamarin.Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Barra de acciones](~/android/user-interface/controls/action-bar.md) 

`ActionBar` es una barra de herramientas que muestra el título de la actividad, interfaces de navegación y otros elementos interactivos. Normalmente, la barra de acciones aparece en la parte superior de la ventana de la actividad.

![Ejemplo ActionBar](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Autocompletar](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` es un elemento de vista de texto editable que muestra las sugerencias de finalización automáticamente mientras el usuario está escribiendo. La lista de sugerencias se muestra en un menú desde el que el usuario puede elegir un elemento para reemplazar el contenido del cuadro de edición con desplegable.

![Ejemplo de Autocompletar](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Botones](~/android/user-interface/controls/buttons/index.md)

Botones son elementos de interfaz de usuario que el usuario puntea para llevar a cabo una acción.

![Botones de ejemplo](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

La `Calendar` clase se utiliza para convertir una instancia específica de tiempo (un valor de milisegundos que se desplaza desde la época) para valores como el año, mes, hora, día del mes y la fecha de la semana siguiente.
`Calendar` admite una gran variedad de opciones de interacción con los datos de calendario, incluida la capacidad para leer y escribir los eventos, los asistentes y recordatorios. Mediante el proveedor de calendario en la aplicación, agregar a través de la API de datos aparecerá en la aplicación de calendario integrado que viene con Android.

![Calendario de ejemplo](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. `CardView` se implementa como un `FrameLayout` widget con esquinas redondeadas y una sombra. Normalmente, un `CardView` se utiliza para presentar un elemento de fila única en una `ListView` o `GridView` grupo de vista.

![Vista de tarjetas de ejemplo](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Editar texto](~/android/user-interface/controls/edit-text.md)

`EditText` es un elemento de interfaz de usuario que se utiliza para escribir y modificar texto.

![Editar texto de ejemplo](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Galería](~/android/user-interface/controls/gallery.md)

`Gallery` es un widget de diseño que se usa para mostrar los elementos en una lista de desplazamiento horizontal; la selección actual coloca en el centro de la vista.

![Galería de ejemplo](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra de navegación](~/android/user-interface/controls/navigation-bar.md)

El *barra de navegación* proporciona controles de navegación en los dispositivos que no incluyen botones de hardware para **inicio**, **Atrás**, y **menú**.

![Barra de navegación de ejemplo](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Selectores](~/android/user-interface/controls/pickers/index.md)

*Selectores* son elementos de interfaz de usuario que permiten al usuario seleccionar una fecha o una hora mediante el uso de los cuadros de diálogo que se proporcionan con Android.

![Selector de ejemplo](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menú emergente](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` se utiliza para mostrar los menús emergentes que están conectados a una vista determinada.

![Menú emergente de ejemplo](images/popup-menu.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` es un elemento de interfaz de usuario que proporciona una forma rápida de seleccionar un valor de un conjunto. Es simmilar a una lista desplegable. 

![Número de ejemplo](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` es un elemento de interfaz de usuario que permite al usuario alternar entre dos Estados, como en o desactivado. El `Switch` valor predeterminado es OFF.

![Conmutador de ejemplo](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` es una vista que usa la representación 2D y acelerados por hardware para habilitar un vídeo o una secuencia de contenido OpenGL para mostrarse.

![Vista de textura de ejemplo](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

El `Toolbar` widget (introducido en el círculo 5.0 Android) puede considerarse como una generalización de la interfaz de la barra de acción &ndash; está diseñado para reemplazar la barra de acciones. La `Toolbar` puede usarse en cualquier lugar en un diseño de la aplicación y es mucho más personalizable que una barra de acción.

![Barra de herramientas de ejemplo](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

El `ViewPager` es un administrador de diseño que permite al usuario examinar izquierdo y derecho a través de páginas de datos.

![Ejemplo ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` es un elemento de interfaz de usuario que le permite crear su propia ventana para ver las páginas web (o incluso desarrollar un explorador completo).

![Vista Web de ejemplo](images/web-view.png)

