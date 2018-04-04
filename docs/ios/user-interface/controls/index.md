---
title: Controles
description: Xamarin.iOS expone todos los objetos de interfaz de usuario nativa proporcionados por Apple. Se agregan fácilmente a las aplicaciones de Xamarin.iOS con iOS diseñador, el generador de interfaz de Xcode o mediante programación. Independientemente del método que elija, Xamarin.iOS expone todas las propiedades del objeto de interfaz de usuario y los métodos en C#.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 82b2998319d4e78ee4f58a6d024032a509724537
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="controls"></a>Controles

_Xamarin.iOS expone todos los objetos de interfaz de usuario nativa proporcionados por Apple. Se agregan fácilmente a las aplicaciones de Xamarin.iOS con iOS diseñador, el generador de interfaz de Xcode o mediante programación. Independientemente del método que elija, Xamarin.iOS expone todas las propiedades del objeto de interfaz de usuario y los métodos en C#._

Este documento describe algunos de los controles de interfaz de usuario de iOS más comunes y cómo utilizarlas.

## <a name="alertsalertsmd"></a>[Alertas](alerts.md)

A partir de iOS 8, UIAlertController tiene completado UIActionSheet reemplazado y UIAlertView de los cuales están en desuso.

## <a name="buttonsbuttonsmd"></a>[Botones](buttons.md)

La clase UIButton se usa para representar distintos diferentes estilos de botón en las pantallas de iOS. Esta sección presentan las distintas opciones para trabajar con botones de iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Vistas de colecciones](uicollectionview.md)

Vistas de colección, disponibles en la `UICollectionView` clase, son un nuevo concepto de iOS 6 que presentan presentar varios elementos en la pantalla mediante los diseños. Los patrones para proporcionar datos a una `UICollectionView` para crear elementos de e interactuar con los elementos, siga el mismo delegación y origen de datos patrones que se usan habitualmente en el desarrollo de iOS.

## <a name="imagesimagemd"></a>[Imágenes](image.md)

Agregar imágenes a la aplicación requiere dos pasos: en primer lugar, agregue las imágenes al proyecto; a continuación, agregar controles y código para que se muestren en una pantalla. Hacer referencia a la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) artículo para cobertura de imagen de control en Xamarin.iOS más detallada.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controles manuales de la cámara](intro-to-manual-camera-controls.md)

Los controles de cámara Manual, proporcionados por el `AVFoundation Framework` en iOS 8, permitir que una aplicación móvil tomar el control completo sobre la cámara de un dispositivo iOS. Este nivel específica del control se puede utilizar para crear aplicaciones de la cámara de nivel profesional y proporcionar las composiciones de intérprete ajustando los parámetros de la cámara al desconectar un vídeo o imagen fija.

## <a name="mapsios-mapsindexmd"></a>[Mapas](ios-maps/index.md)

Los mapas son una característica común en todos los sistemas operativos de dispositivos móviles modernos. iOS ofrece compatibilidad de asignación forma nativa mediante el marco del Kit de mapa. Con el Kit de mapa, las aplicaciones pueden agregar fácilmente mapas enriquecidas e interactivas. Estas asignaciones se pueden personalizar de varias maneras, como agregar anotaciones para marcar las ubicaciones en un mapa y superponer los gráficos de formas arbitrarias. Kit de mapa incluso tiene compatibilidad integrada para mostrar la ubicación actual de un dispositivo.

## <a name="labelslabelsmd"></a>[Etiquetas](labels.md)

El `UILabel` control se utiliza para mostrar único y varias líneas de texto de sólo lectura.

## <a name="pickers-and-date-pickerspickermd"></a>[Selectores y selectores de fecha](picker.md)

El control de selector muestra control 'rueda-like' que contiene una lista desplazable de valores con el valor seleccionado se resalta. Los usuarios gira la rueda para seleccionar la opción que deseen.

Un usuario específico caso para selectores para establecer la fecha o tiempo. Para proporcionar esta Apple ha creado una subclase personalizada de la clase de UIPickerView denominada UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicadores de progreso y actividad](progress-activity-indicator.md)

iOS proporciona dos mecanismos principales para indicar el progreso de la aplicación: indicadores de actividad (incluido un determinado _red_ indicador de actividad) y barras de progreso.

## <a name="search-barssearchbarmd"></a>[Barras de búsqueda](searchbar.md)

El UISearchBar se utiliza para buscar a través de una lista de valores. 

## <a name="sliders-steppers-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Controles deslizantes, Steppers y controles segmentados](slider-switch-segmented-controls.md)

El control deslizante permite simple selección de un valor numérico dentro de un intervalo. iOS usa el `UISwitch` como un valor booleano de entrada que se puede representar con un botón de opción en otras plataformas. Un Control segmentados es una manera organizada para permitir a los usuarios interactuar con un número pequeño de opciones.

## <a name="stack-viewuistackviewmd"></a>[Vista de pila](uistackview.md)

El control de vista de pila (`UIStackView`) aprovecha la eficacia de diseño automático y las clases de tamaño para administrar una pila de subvistas, horizontal o verticalmente, que responde dinámicamente para el tamaño de pantalla y la orientación del dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tablas y vistas](tables/index.md)

su sección presenta las clases utilizadas para crear y mostrar tablas, a continuación, proporciona ejemplos de cómo se utilizan en Xamarin.iOS. Se tratará con la apariencia predeterminada para las tablas, personalizar el diseño, implementación de la edición y usar el Diseñador de iOS de Xamarin para diseñar una tabla visualmente. A veces la presentación obviamente es una lista de filas (por ejemplo, la aplicación de música) y otras veces se difíciles de reconocer el control de tabla (como la modificación en la aplicación de contactos o una conversación en la aplicación de mensajes).

## <a name="text-inputtext-inputmd"></a>[Entrada de texto](text-input.md)

Aceptar entradas de texto del usuario se logra con la `UITextField` para las entradas de línea y UITextView de varias líneas de texto editable. Puede arrastrar cualquiera de estos controles a una pantalla y haga doble clic para establecer el texto inicial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barras de pestañas y controladores de la barra de pestañas](creating-tabbed-applications.md)

aplicaciones de iOS con una interfaz de usuario de navegación de la ficha se compilan con la clase UITabBarController. En este artículo le mostraremos cómo configurar una aplicación con pestañas que contiene varios controladores y vistas. A continuación, examinaremos cómo cargar un UITabBarController cuando no es el controlador de raíz, como después de una pantalla de inicio de sesión.

## <a name="web-viewsuiwebviewmd"></a>[Vistas web](uiwebview.md)

En este artículo, analizaremos cada una de las tres vistas Web proporcionadas por Apple: `UIWebView`, `WKWebview`, y `SFSafariViewController`, sus similitudes y diferencias y cómo se puede usar.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
