---
title: RecyclerView
description: RecyclerView es un grupo de vista para mostrar colecciones; está diseñado para ser un sustituto más flexible para los grupos de la vista anterior como ListView y GridView.  Esta guía explica cómo usar y personalizar RecyclerView en las aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 1332a7ef5b8e5bb2f1178582bcf058123f1e177c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110152"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView es un grupo de vista para mostrar colecciones; está diseñado para ser un sustituto más flexible para los grupos de la vista anterior como ListView y GridView.  Esta guía explica cómo usar y personalizar RecyclerView en las aplicaciones de Xamarin.Android._

## <a name="recyclerview"></a>RecyclerView

Muchas aplicaciones deben mostrar colecciones del mismo tipo (por ejemplo, los mensajes, contactos, imágenes o canciones); a menudo, esta colección es demasiado grande para caber en la pantalla, por lo que se presenta la colección en una ventana pequeña que puede desplazarse fácilmente por todos los elementos de la colección.
`RecyclerView` es un widget de Android que muestra una colección de elementos de una lista o una cuadrícula, que permite al usuario desplazarse por la colección. El siguiente es una captura de pantalla de una aplicación de ejemplo que usa `RecyclerView` para mostrar el contenido de la Bandeja de entrada de correo electrónico en una lista de desplazamiento vertical:

[![Aplicación de ejemplo con RecyclerView para mensajes de la Bandeja de entrada de lista](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` ofrece dos características atractivas:

-  Tiene una arquitectura flexible que le permite modificar su comportamiento mediante la conexión de los componentes preferidos.

-  Es eficaz con colecciones grandes porque vuelve a usar vistas de elementos y se requiere el uso de *ver titulares* ver las referencias.

Esta guía explica cómo usar `RecyclerView` en aplicaciones de Xamarin.Android; se explica cómo agregar la `RecyclerView` paquete para el proyecto de Xamarin.Android y se describe cómo `RecyclerView` funciones en una aplicación típica. Se proporcionan ejemplos de código real para mostrar cómo integrar `RecyclerView` en su aplicación, cómo implementar la vista de elemento haga clic en y cómo actualizar `RecyclerView` cuando cambian los datos subyacentes. Esta guía se da por supuesto que está familiarizado con el desarrollo de Xamarin.Android.


### <a name="requirements"></a>Requisitos

Aunque `RecyclerView` es a menudo asociado con Android 5.0 Lollipop, se ofrece como una biblioteca de soporte técnico &ndash; `RecyclerView` funciona con aplicaciones de ese nivel de API de destino (Android 2.1) 7 y versiones posteriores. Se requiere lo siguiente para usar `RecyclerView` en aplicaciones basadas en Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

-  El proyecto de aplicación debe incluir el **Xamarin.Android.Support.v7.RecyclerView** paquete. Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: incluidos unos NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Información general

`RecyclerView` puede considerarse como un reemplazo para el `ListView` y `GridView` widgets en Android. Al igual que sus predecesores, `RecyclerView` está diseñado para mostrar un gran conjunto de datos en una ventana pequeña, pero `RecyclerView` ofrece más opciones de diseño y está mejor optimizado para mostrar las colecciones grandes. Si está familiarizado con `ListView`, hay varias diferencias importantes entre `ListView` y `RecyclerView`:

-   `RecyclerView` es un poco más compleja para usar: tendrá que escribir más código para usar `RecyclerView` en comparación con `ListView`.

-   `RecyclerView` no proporciona un adaptador predefinido; debe implementar el código del adaptador que tiene acceso a su origen de datos. Sin embargo, Android incluye varios adaptadores predefinidos que funcionan con `ListView` y `GridView`.

-   `RecyclerView` no ofrece un evento click del elemento cuando un usuario puntea un elemento; en su lugar, los eventos click de elemento se controlan mediante clases auxiliares. Por el contrario, `ListView` ofrece un evento click del elemento.

-   `RecyclerView` mejora el rendimiento mediante el reciclado de vistas y aplicando el patrón del titular de la vista, lo que elimina las búsquedas de recursos de diseño innecesarios. Uso del patrón de titular de la vista es opcional en `ListView`.

-   `RecyclerView` se basa en un diseño modular que facilita su personalización. Por ejemplo, puede conectar en una directiva de diseño diferente sin cambios de código significativos para la aplicación.
    Por el contrario, `ListView` es relativamente monolítico en la estructura.

-   `RecyclerView` incluye animaciones integradas para el elemento Agregar y quitar. `ListView` las animaciones requieren algún esfuerzo adicional por parte del desarrollador de la aplicación.


### <a name="sections"></a>Secciones

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Funcionalidad y los elementos de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Este tema se explica cómo el `Adapter`, `LayoutManager`, y `ViewHolder` funcionan conjuntamente como clases auxiliares para admitir `RecyclerView`.
Proporciona información general de cada una de estas clases auxiliares y explica cómo usarlos en su aplicación.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

En este tema se basa en la información proporcionada en [RecyclerView elementos y funcionalidad](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) proporcionando ejemplos de código real de los distintos `RecyclerView` elementos se implementan para compilar una aplicación de exploración de fotografías en tiempo real.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Ampliando el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

En este tema agrega el código adicional a la aplicación de ejemplo presentada en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para demostrar cómo controlar eventos click de elemento y actualizar `RecyclerView` cuando los cambios de origen de datos subyacente.


### <a name="summary"></a>Resumen

Esta guía presentan lo Android `RecyclerView` widget; se ha explicado cómo agregar la `RecyclerView` admite la biblioteca de proyectos de Xamarin.Android, cómo `RecyclerView` recicla las vistas, cómo aplica el patrón de titular de la vista para mejorar la eficacia y cómo los distintos clases auxiliares que componen `RecyclerView` colaboran para mostrar colecciones. Proporciona código de ejemplo para demostrar cómo `RecyclerView` está integrado en una aplicación, se explica cómo adaptar `RecyclerView`de la directiva de diseño agregando los administradores de diseño diferentes y se describe cómo controlar el elemento eventos de clic y notificar a `RecyclerView`de cambios del origen de datos.

Para obtener más información acerca de `RecyclerView`, consulte el [referencia de clase RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introducción al círculo](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
