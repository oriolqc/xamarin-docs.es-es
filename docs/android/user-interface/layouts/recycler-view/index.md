---
title: RecyclerView
description: RecyclerView es un grupo de vista para mostrar colecciones; está diseñado para ser un reemplazo más flexible para los grupos de la vista anteriores como ListView, GridView.  Esta guía explica cómo usar y personalizar RecyclerView en aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/03/2018
ms.openlocfilehash: 187339244d53c154cc22672a3d2ceba7e0a75bcf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView es un grupo de vista para mostrar colecciones; está diseñado para ser un reemplazo más flexible para los grupos de la vista anteriores como ListView, GridView.  Esta guía explica cómo usar y personalizar RecyclerView en aplicaciones de Xamarin.Android._

## <a name="recyclerview"></a>RecyclerView

Muchas aplicaciones deben mostrar recopilaciones del mismo tipo (por ejemplo, mensajes, contactos, imágenes o canciones); a menudo, esta colección es demasiado grande para caber en la pantalla, por lo que la colección se presenta en una ventana pequeña que se puede desplazar sin problemas a través de todos los elementos de la colección.
`RecyclerView` es un widget de Android que muestra una colección de elementos de una lista o una cuadrícula, lo que permite al usuario desplazarse a través de la colección. La siguiente es una captura de pantalla de una aplicación de ejemplo que usa `RecyclerView` para mostrar el contenido de la Bandeja de entrada de correo electrónico en una lista de desplazamiento vertical:

[![Aplicación de ejemplo mediante RecyclerView a los mensajes de la Bandeja de entrada de lista](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` ofrece dos características atractivas:

-  Tiene una arquitectura flexible que permite modificar su comportamiento agregando los componentes preferidos.

-  Es eficaz con las colecciones grandes porque se vuelve a utilizar vistas de elementos y requiere el uso de *ver titulares* en memoria caché las referencias de la vista.

Esta guía explica cómo usar `RecyclerView` en aplicaciones de Xamarin.Android; se explica cómo agregar la `RecyclerView` paquete a su proyecto Xamarin.Android y se describe cómo `RecyclerView` funciones en una aplicación típica. Se proporcionan ejemplos de código real para mostrar cómo integrar `RecyclerView` en la aplicación, cómo implementar la vista de elemento haga clic en y cómo actualizar `RecyclerView` cuando cambien sus datos subyacentes. Esta guía se da por supuesto que está familiarizado con el desarrollo de Xamarin.Android.


### <a name="requirements"></a>Requisitos

Aunque `RecyclerView` es a menudo asociado con el círculo 5.0 Android, se ofrece como una biblioteca de compatibilidad de &ndash; `RecyclerView` funciona con aplicaciones de ese nivel de API de destino (Android 2.1) 7 y versiones posteriores. Se requiere lo siguiente para usar `RecyclerView` en aplicaciones basadas en Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe instalarse y configurarse con Visual Studio o Visual Studio para Mac.

-  El proyecto de aplicación debe incluir el **Xamarin.Android.Support.v7.RecyclerView** paquete. Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: incluidos unos NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Información general

`RecyclerView` puede considerarse como un reemplazo para el `ListView` y `GridView` los widgets de Android. Al igual que sus predecesores, `RecyclerView` está diseñado para mostrar un conjunto de datos grande en una ventana pequeña, pero `RecyclerView` ofrece más opciones de diseño y se optimizan mejor para mostrar las colecciones grandes. Si está familiarizado con `ListView`, hay algunas diferencias importantes entre `ListView` y `RecyclerView`:

-   `RecyclerView` es ligeramente más complejo para usar: tendrá que escribir más código para usar `RecyclerView` en comparación con `ListView`.

-   `RecyclerView` no proporciona un adaptador predefinido; debe implementar el código del adaptador que tiene acceso a su origen de datos. Sin embargo, Android incluye varios adaptadores predefinidos que funcionan con `ListView` y `GridView`.

-   `RecyclerView` no ofrece un evento de clic de elemento cuando un usuario puntee en un elemento; en su lugar, las clases auxiliares controla los eventos de elemento y haga clic en. Por el contrario, `ListView` ofrece un evento de elemento y haga clic en.

-   `RecyclerView` mejora el rendimiento mediante el reciclado de vistas y aplicando el patrón de titular de la vista, lo que elimina las búsquedas de recursos de diseño innecesarios. Uso del patrón de titular de la vista es opcional en `ListView`.

-   `RecyclerView` se basa en un diseño modular que resulta más fácil de personalizar. Por ejemplo, puede introducir en una directiva de diseño diferente sin cambios de código significativos para la aplicación.
    Por el contrario, `ListView` es relativamente monolítico de estructura.

-   `RecyclerView` incluye animaciones integradas para elemento add y remove. `ListView` las animaciones requieren algunos esfuerzo adicional por parte del desarrollador de la aplicación.


### <a name="sections"></a>Secciones

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Funcionalidad y los elementos de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Este tema se explica cómo el `Adapter`, `LayoutManager`, y `ViewHolder` funcionan conjuntamente como clases auxiliares para admitir `RecyclerView`.
Se proporciona una descripción general de cada una de estas clases auxiliares y se explica cómo usarlos en su aplicación.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

En este tema se basa en la información proporcionada en [RecyclerView partes y funcionalidad](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) proporcionando ejemplos de código reales de cómo los distintos `RecyclerView` elementos se implementan para compilar una aplicación de exploración de fotografías de mundo real.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Siguiendo con el ejemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

En este tema agrega el código adicional para la aplicación de ejemplo que se presentan en [un ejemplo básico se los RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para demostrar cómo controlar eventos click de elemento y actualizar `RecyclerView` cuando cambios de origen de datos subyacente.


### <a name="summary"></a>Resumen

Esta guía introdujo el Android `RecyclerView` widget; explica cómo agregar el `RecyclerView` admite la biblioteca de proyectos de Xamarin.Android, cómo `RecyclerView` recicla vistas, cómo aplica el patrón de titular de la vista para mejorar la eficacia y cómo los distintos clases auxiliares que componen `RecyclerView` colaboración para mostrar las colecciones. Proporciona código de ejemplo para demostrar cómo `RecyclerView` está integrado en una aplicación, explica cómo adaptar `RecyclerView`de la directiva de diseño para comprobarlo, conecte en administradores de diseño diferentes y se describe cómo controlar el elemento click (eventos) y notifíquelo a `RecyclerView`de cambios del origen de datos.

Para obtener más información acerca de `RecyclerView`, consulte el [referencia de clase RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introducción a círculo](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
