---
title: Elementos y funcionalidad de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 248baa5daceff6db01098a155600ea204547e845
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319749"
---
# <a name="listview-parts-and-functionality"></a>Elementos y funcionalidad de ListView


## <a name="overview"></a>Información general

Un `ListView` consta de las siguientes partes:

- **Filas** &ndash; la representación visible de los datos en la lista.

- **Adaptador** &ndash; una clase no visual que se enlaza el origen de datos a la vista de lista.

- **Desplazamiento rápido** &ndash; un identificador que permite al usuario desplazarse a la longitud de la lista.

- **Índice de sección** &ndash; filas de un elemento de interfaz de usuario que flota sobre el desplazamiento para indicar dónde se encuentran las filas actuales en la lista.

Estas capturas de pantalla usan básico `ListView` control para mostrar cómo se representan el desplazamiento rápido y el índice de sección:

[![Capturas de pantalla de aplicaciones con las filas antiguas sin formato, rápida de desplazamiento y el índice de sección](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Los elementos que componen un `ListView` se describen más detalladamente a continuación:


## <a name="rows"></a>Filas

Cada fila tiene su propio `View`. La vista puede ser cualquiera de las vistas integradas definidas en `Android.Resources`, o una vista personalizada. Cada fila puede usar el mismo diseño de vista o pueden ser diferentes. Hay ejemplos de este documento del uso de los diseños integrados y otros que se explica cómo definir los diseños personalizados.


## <a name="adapter"></a>Adaptador

El `ListView` control requiere una `Adapter` para proporcionar el formato `View` para cada fila. Android tiene integrada adaptadores y las vistas que se pueden usar o se pueden crear clases personalizadas.


## <a name="fast-scrolling"></a>Desplazamiento rápido

Cuando un `ListView` contiene muchas filas de datos de desplazamiento rápido puede habilitarse para ayudar al usuario navegar a cualquier parte de la lista. El rápido desplazamiento 'barra de desplazamiento' puede ser opcionalmente habilitada (y personalizados en el nivel de API 11 y versiones posteriores).


## <a name="section-index"></a>Índice de sección

Al desplazarse a través de las listas largas, el índice de la sección opcional proporciona al usuario con comentarios en qué parte de la lista que se está viendo actualmente. Solo es adecuado en las listas largas, normalmente en combinación con desplazamiento rápido.


## <a name="classes-overview"></a>Información general de clases

Las clases primarias utilizadas para mostrar `ListViews` se muestra aquí:

[![Diagrama UML que muestra las relaciones entre ListView y las clases asociadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

El propósito de cada clase se describe a continuación:

- **ListView** &ndash; elemento de la interfaz de usuario que se muestra una colección desplazable de filas. En teléfonos normalmente utiliza seguridad de toda la pantalla (en cuyo caso el `ListActivity` clase puede utilizarse) o podría ser parte de un diseño más grande en teléfonos o dispositivos de tableta gráfica.

- **Vista** &ndash; una vista en Android puede ser cualquier elemento de interfaz de usuario, pero en el contexto de un `ListView` requiere un `View` va a proporcionar para cada fila.

- **BaseAdapter** &ndash; clase Base para implementaciones del adaptador enlazar un `ListView` a un origen de datos.

- **ArrayAdapter** &ndash; clase de adaptadores integrados que se enlaza a una matriz de cadenas para un `ListView` para su presentación. La interfaz genérica `ArrayAdapter<T>` hace lo mismo para otros tipos.

- **CursorAdapter** &ndash; Use `CursorAdapter` o `SimpleCursorAdapter` para mostrar datos en función de una consulta de SQLite.

Este documento contiene ejemplos sencillos que usan un `ArrayAdapter` , así como ejemplos más complejos que requieren las implementaciones personalizadas de `BaseAdapter` o `CursorAdapter`.

