---
title: Controles de interfaz de usuario en Xamarin.iOS
description: Este documento incluye vínculos a guías que describen los distintos controles de interfaz de usuario de iOS disponibles para los desarrolladores de Xamarin.iOS. Contenido vinculado describe alertas, botones, las vistas de colección, imágenes, controles manuales de la cámara, mapas, etiquetas, selectores, selectores de fecha y mucho más.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 25b17dbebebf7bcae92ebdc294c798101d39b987
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121625"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controles de interfaz de usuario en Xamarin.iOS

Este documento presentan algunos de los controles de interfaz de usuario de iOS más comunes y cómo usarlas.

## <a name="alertsalertsmd"></a>[Alertas](alerts.md)

A partir de iOS 8, UIAlertController tiene completado UIActionSheet reemplazado y UIAlertView de los cuales están en desuso.

## <a name="buttonsbuttonsmd"></a>[Botones](buttons.md)

La clase UIButton se usa para representar varios estilos diferentes del botón en las pantallas de iOS. Esta sección presentan las distintas opciones para trabajar con los botones de iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Vistas de colecciones](uicollectionview.md)

Las vistas de colección, disponibles en el `UICollectionView` class, son un concepto nuevo en iOS 6 que introducen presentar varios elementos en la pantalla con los diseños. Los patrones para proporcionar datos a un `UICollectionView` para crear elementos e interactuar con esos elementos, siga el misma delegación y los datos del origen de patrones de uso frecuente en el desarrollo de iOS.

## <a name="imagesimagemd"></a>[Imágenes](image.md)

Agregar imágenes a la aplicación requiere dos pasos: en primer lugar, agregue las imágenes al proyecto; a continuación, agregar controles y código para mostrarlos en una pantalla. Hacer referencia a la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) artículo para obtener más cobertura de Xamarin.iOS de control de imagen.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controles manuales de la cámara](intro-to-manual-camera-controls.md)

Los controles de cámara Manual, proporcionados por el `AVFoundation Framework` en iOS 8, permitir que una aplicación móvil tomar el control total sobre la cámara de un dispositivo iOS. Este nivel específico de control puede utilizarse para crear aplicaciones de la cámara de nivel profesional y proporcionar las composiciones de intérprete al ajustar los parámetros de la cámara al mismo tiempo que un vídeo o imagen fija.

## <a name="mapsios-mapsindexmd"></a>[Mapas](ios-maps/index.md)

Mapas son una característica común en todos los sistemas operativos de dispositivos móviles modernos. iOS ofrece compatibilidad con la asignación forma nativa mediante el marco del Kit de mapa. Con el Kit de mapa, las aplicaciones pueden agregar fácilmente mapas interactivos y. Estas asignaciones se pueden personalizar en una variedad de formas, como agregar anotaciones para marcar las ubicaciones en un mapa y superponer los gráficos de formas arbitrarias. Mapkit incluso tiene compatibilidad integrada para mostrar la ubicación actual de un dispositivo.

## <a name="labelslabelsmd"></a>[Etiquetas](labels.md)

El `UILabel` control se utiliza para mostrar único y varias líneas, texto de sólo lectura.

## <a name="pickers-and-date-pickerspickermd"></a>[Selectores y selectores de fecha](picker.md)

El control de selector de muestra 'rueda similar a' control que contiene una lista desplazable de valores con el valor seleccionado está resaltada. Los usuarios gira la rueda para seleccionar la opción que deseen.

Un usuario específico póngala selectores de establecer la fecha o la hora. Para proporcionar esta Apple ha creado una subclase de la clase UIPickerView llamada UIDatePicker personalizada.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicadores de progreso y actividad](progress-activity-indicator.md)

iOS ofrece dos maneras principales para indicar el progreso de la aplicación: indicadores de actividad (incluida una determinada _red_ indicador de actividad) y barras de progreso.

## <a name="search-barssearchbarmd"></a>[Las barras de búsqueda](searchbar.md)

El UISearchBar se usa para buscar a través de una lista de valores. 

## <a name="sliders-steppers-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Controles deslizantes, Steppers y controles segmentados](slider-switch-segmented-controls.md)

El control deslizante permite la selección simple de un valor dentro de un intervalo numérico. iOS usa el `UISwitch` como un valor booleano de entrada que puede representarse mediante un botón de radio en otras plataformas. Un Control segmentado es una forma organizada para permitir a los usuarios interactuar con un número pequeño de opciones.

## <a name="stack-viewuistackviewmd"></a>[Vista de pila](uistackview.md)

El control de vista de pila (`UIStackView`) aprovecha la eficacia del diseño automático y las clases de tamaño para administrar una pila de subvistas, ya sea horizontal o verticalmente, que responde dinámicamente para el tamaño de pantalla y la orientación del dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tablas y vistas](tables/index.md)

su sección presenta las clases utilizadas para crear y mostrar tablas, a continuación, proporciona ejemplos de cómo se usan en Xamarin.iOS. Tratará con la apariencia predeterminada para las tablas, personalizar el diseño, implementación de la edición y utilizando el Diseñador de iOS de Xamarin para diseñar una tabla visualmente. A veces la presentación es obviamente una lista de filas (por ejemplo, la aplicación de música) y otras veces es difícil para que reconozca el control de tabla (como la modificación de la aplicación de contactos, o una conversación en la aplicación de mensajes).

## <a name="text-inputtext-inputmd"></a>[Entrada de texto](text-input.md)

Aceptar la entrada de texto del usuario se logra con la `UITextField` para entradas de línea y UITextView de varias líneas de texto editable. Puede arrastrar cualquiera de estos controles a una pantalla y haga doble clic para establecer el texto inicial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barras de pestañas y controladores de la barra de pestañas](creating-tabbed-applications.md)

las aplicaciones de iOS mediante una interfaz de usuario de navegación de la pestaña se crean mediante la clase UITabBarController. En este artículo, le guiaremos a través de cómo configurar una aplicación con pestañas que contiene varios controladores y vistas. A continuación, examinaremos cómo cargar un UITabBarController cuando no es el controlador de raíz, como después de una pantalla de inicio de sesión.

## <a name="web-viewsuiwebviewmd"></a>[Vistas web](uiwebview.md)

En este artículo, analizaremos cada una de las tres vistas Web proporcionadas por Apple: `UIWebView`, `WKWebview`, y `SFSafariViewController`, sus similitudes y diferencias y cómo se puede usar.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
