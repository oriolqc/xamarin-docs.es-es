---
title: Funcionalidad y los elementos de ListView
ms.topic: article
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: a963922dfdc07164b7c20f84be4f0b87440bff13
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="listview-parts-and-functionality"></a>Funcionalidad y los elementos de ListView

<a name="overview" />

## <a name="overview"></a>Información general

Un `ListView` consta de las siguientes partes:

- **Filas** &ndash; la representación visible de los datos en la lista.

- **Adaptador** &ndash; una clase no visual que enlaza el origen de datos a la vista de lista.

- **Desplazamiento rápido** &ndash; un identificador que permite al usuario desplazarse la longitud de la lista.

- **Índice de sección** &ndash; filas de un elemento de la interfaz de usuario que flota sobre el desplazamiento para indicar dónde se encuentran las filas actuales en la lista.

Estas capturas de pantalla usan básico `ListView` control para mostrar cómo se representan desplazamiento rápido y el índice de sección:

[![Capturas de pantalla de aplicaciones que usan las filas antiguas sencillo, rápido desplazamiento y el índice de sección](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png)

Los elementos que componen un `ListView` se describen con más detalle a continuación:

<a name="Rows" />

## <a name="rows"></a>Filas

Cada fila tiene su propio `View`. La vista puede ser cualquiera de las vistas integradas definidas en `Android.Resources`, o una vista personalizada. Cada fila puede usar el mismo diseño de vista o todos ellos pueden tener diferentes. Hay ejemplos de este documento del uso de diseños integrados y otros que explica cómo definir diseños personalizados.

<a name="Adapter" />

## <a name="adapter"></a>Adaptador

El `ListView` control requiere una `Adapter` para proporcionar el formato `View` para cada fila. Android tiene integrada adaptadores y vistas que se pueden usar, o se pueden crear clases personalizadas.

<a name="Fast_Scrolling" />

## <a name="fast-scrolling"></a>Desplazamiento rápido

Cuando un `ListView` contiene muchas filas de datos fast desplazamiento puede habilitarse para ayudar al usuario a desplazarse a cualquier parte de la lista. El rápido desplazamiento 'barra de desplazamiento' puede ser opcionalmente habilitado (y personalizados en el nivel de API 11 y versiones posteriores).

<a name="Section_Index" />

## <a name="section-index"></a>Índice de sección

Mientras se desplaza a través de listas largas, el índice de sección opcional proporciona al usuario con comentarios en qué parte de la lista que se está viendo actualmente. Solo es adecuado en listas largas, por lo general en junto con un desplazamiento rápido.

<a name="Classes_Overview" />

## <a name="classes-overview"></a>Información general de clases

Las clases principales utilizadas para mostrar `ListViews` se muestran a continuación:

[![Diagrama UML que ilustra las relaciones entre las clases asociadas y de ListView](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png)

El propósito de cada clase se describe a continuación:

- **ListView** &ndash; elemento de la interfaz de usuario que muestra una colección de filas desplazable. En teléfonos normalmente utiliza toda la pantalla (en cuyo caso, el `ListActivity` clase puede usarse) o podría ser parte de un diseño más grande en teléfonos o dispositivos de tableta gráfica.

- **Vista** &ndash; una vista en Android puede ser cualquier elemento de la interfaz de usuario, pero en el contexto de un `ListView` requiere un `View` va a proporcionar para cada fila.

- **BaseAdapter** &ndash; la clase Base para implementaciones del adaptador enlazar un `ListView` a un origen de datos.

- **ArrayAdapter** &ndash; clase de adaptadores integrados que se enlaza a una matriz de cadenas a un `ListView` para su presentación. La interfaz genérica `ArrayAdapter<T>` hace lo mismo para otros tipos.

- **CursorAdapter** &ndash; Use `CursorAdapter` o `SimpleCursorAdapter` para mostrar los datos en función de una consulta de SQLite.

Este documento contiene ejemplos sencillos que usan un `ArrayAdapter` , así como ejemplos más complejos que requieren las implementaciones personalizadas de `BaseAdapter` o `CursorAdapter`.

