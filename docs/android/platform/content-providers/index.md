---
title: Introducción a ContentProviders
description: El sistema operativo Android usa proveedores de contenido para facilitar el acceso a los datos compartidos como archivos multimedia, contactos y la información del calendario. En este artículo se presenta la clase ContentProvider y proporciona dos ejemplos de cómo se usa.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: e534c02820bfeab3a5bc1211bf0cbb20b9821af3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="intro-to-contentproviders"></a>Introducción a ContentProviders

_El sistema operativo Android usa proveedores de contenido para facilitar el acceso a los datos compartidos como archivos multimedia, contactos y la información del calendario. En este artículo se presenta la clase ContentProvider y proporciona dos ejemplos de cómo se usa._


## <a name="content-providers-overview"></a>Información general sobre proveedores de contenido

A *ContentProvider* encapsula un repositorio de datos y proporciona una API para tener acceso a él. El proveedor existe como parte de una aplicación de Android que normalmente también proporciona una interfaz de usuario para mostrar y administrar los datos. La principal ventaja de usar un proveedor de contenido es habilitar otras aplicaciones acceder fácilmente a los datos encapsulados mediante un objeto de proveedor de cliente (denominado un *ContentResolver*). Juntos, un proveedor de contenido y el Solucionador de contenido ofrecen una API coherente entre aplicaciones de acceso a datos que es fácil de generar y consumir. Cualquier aplicación puede optar por usar `ContentProviders` para administrar datos internamente y también va a exponer a otras aplicaciones.

Un `ContentProvider` también es necesario para la aplicación proporcionar sugerencias de búsqueda personalizada, o si desea proporcionar la capacidad de copiar datos complejos desde su aplicación para pegar en otras aplicaciones. Este documento muestra cómo obtener acceso y generar `ContentProviders` con Xamarin.Android.

La estructura de esta sección es como sigue:

- **Cómo funciona** &ndash; una visión general de lo que el `ContentProvider` está diseñado para y cómo funciona.

- **Consumir un proveedor de contenido** &ndash; muestra un ejemplo de acceso a la lista de contactos.

- **Utilizar ContentProvider para compartir datos** &ndash; escritura y consume un `ContentProvider` en la misma aplicación.

`ContentProviders` y, a menudo se usan los cursores que operan sobre sus datos para rellenar ListView. Hacer referencia a la [guía ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre cómo usar esas clases.

`ContentProviders` expone Android (u otras aplicaciones) son una manera fácil de incluir datos de otros orígenes en la aplicación. Se permite el acceso y presentar los datos como la lista de contactos, fotografías o eventos de calendario desde dentro de la aplicación y permiten al usuario interactuar con esos datos.

Personalizado `ContentProviders` son una forma cómoda para empaquetar los datos para su uso dentro de su propia aplicación, o para su uso por otras aplicaciones (incluidos los usos especiales como búsqueda personalizada y copiar y pegar).

Los temas de esta sección proporcionan algunos ejemplos sencillos de escribir y utilizar `ContentProvider` código.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guía para desarrolladores de proveedores de contenido](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Referencia de clase ContentProvider](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Referencia de clase ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Referencia de clase de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Referencia de clase CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Referencia de clase UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Referencia de clase ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
