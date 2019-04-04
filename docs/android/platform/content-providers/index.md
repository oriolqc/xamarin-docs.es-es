---
title: Introducción a ContentProviders
description: El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos como archivos multimedia, contactos y la información del calendario. Este artículo presenta la clase ContentProvider y proporciona dos ejemplos de cómo se usa.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 60f9420fc630bcfc44a6b19356b2315ac8bcd523
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670323"
---
# <a name="intro-to-contentproviders"></a>Introducción a ContentProviders

_El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos como archivos multimedia, contactos y la información del calendario. Este artículo presenta la clase ContentProvider y proporciona dos ejemplos de cómo se usa._


## <a name="content-providers-overview"></a>Información general de los proveedores de contenido

Un *ContentProvider* encapsula un repositorio de datos y proporciona una API para acceder a ella. El proveedor existe como parte de una aplicación Android que habitualmente también proporciona una interfaz de usuario para mostrar y administrar los datos. La principal ventaja de usar un proveedor de contenido es permitir que otras aplicaciones acceder fácilmente a los datos encapsulados con un objeto de cliente de proveedor (llamado un *ContentResolver*). Juntos, un proveedor de contenido y el Solucionador de contenido ofrecen una API coherente entre aplicaciones de acceso a datos que sea fácil de crear y consumir. Cualquier aplicación puede elegir usar `ContentProviders` para administrar datos internamente y también para exponerlo a otras aplicaciones.

Un `ContentProvider` también es necesario para la aplicación proporcionar sugerencias de búsqueda personalizada, o si desea proporcionar la capacidad de copiar datos complejos desde su aplicación para pegar en otras aplicaciones. Este documento muestra cómo obtener acceso y generar `ContentProviders` con Xamarin.Android.

La estructura de esta sección es como sigue:

- **Cómo funciona** &ndash; una visión general de lo que el `ContentProvider` está diseñado para y cómo funciona.

- **Consumo de un proveedor de contenido** &ndash; un ejemplo de acceso de la lista de contactos.

- **Uso de ContentProvider para compartir datos** &ndash; escribir y consumir un `ContentProvider` en la misma aplicación.

`ContentProviders` y, a menudo se usan los cursores que operan sobre sus datos para rellenar ListView. Hacer referencia a la [guía ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre cómo usar esas clases.

`ContentProviders` expuestos por Android (u otras aplicaciones) son una manera fácil de incluir datos de otros orígenes en la aplicación. Se permite el acceso y presentar los datos como la lista de contactos, las fotos o los eventos de calendario desde dentro de la aplicación y permiten al usuario interactuar con esos datos.

Personalizado `ContentProviders` son una forma cómoda para empaquetar los datos para su uso dentro de su propia aplicación, o para su uso por otras aplicaciones (incluidos los usos especiales, como búsqueda personalizada y copiar y pegar).

Los temas de esta sección proporcionan algunos ejemplos sencillos de consumir y escribir `ContentProvider` código.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guía para desarrolladores de proveedores de contenido](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referencia de clase ContentProvider](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Referencia de clase ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Referencia de clase de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Referencia de clase CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Referencia de clase UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Referencia de clase ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
