---
title: Con las versiones anteriores proporciona compatibilidad con el paquete de compatibilidad con Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/12/2017
ms.openlocfilehash: f09aae1445cfcf9f4225af3de37b65ebb5a1b6b2
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Con las versiones anteriores proporciona compatibilidad con el paquete de compatibilidad con Android

La utilidad de los fragmentos sería limitada sin compatibilidad con dispositivos de (API nivel 11) 3.0 previamente Android con las versiones anteriores. Para proporcionar esta capacidad, Google introdujo el [biblioteca de compatibilidad de](http://developer.android.com/sdk/compatibility-library.html) (llamado originalmente el *biblioteca de compatibilidad de Android* cuando se publicó) que backports algunas de las API de versiones más recientes de Android a versiones anteriores de Android. Es el paquete de soporte técnico de Android que permite a dispositivos que ejecutan Android 1.6 (API nivel 4) para Android 2.3.3. (API nivel 10).

> [!NOTE]
> Solo el `ListFragment` y `DialogFragment` están disponibles mediante el paquete de compatibilidad con Android. Ninguno de los demás fragmentar subclases, como el `PreferenceFragment,` se admiten en el paquete de compatibilidad con Android. No funcionará en aplicaciones de 3.0 previamente Android. 


## <a name="adding-the-support-package"></a>Agregar el paquete de soporte técnico

El paquete de compatibilidad con Android no se agrega automáticamente a una aplicación Xamarin.Android. Xamarin ofrece la [paquete NuGet de biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar la adición de las bibliotecas de soporte técnico a una aplicación Xamarin.Android. Para incluir los paquetes de soporte técnico en su Xamarin.Android aplicación incluyen el [biblioteca admite Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente en el proyecto Xamarin.Android, como se muestra en la captura de pantalla siguiente: 

[![Paquete de captura de pantalla de la biblioteca de compatibilidad con Android v4 que se agrega al proyecto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Después de han realizado estos pasos, es posible utilizar fragmentos en versiones anteriores de Android. Las API de fragmento funcionará ahora mismo en estas versiones anteriores, con las siguientes excepciones: 

-   **Cambiar la versión mínima de Android** &ndash; la aplicación ya no necesita tener como destino Android 3.0 o superior, tal y como se muestra a continuación: 

    [![Captura de pantalla de mínimo Android destino que se va a establecer en el manifiesto de Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Extender FragmentActivity** &ndash; las actividades que hospedan fragmentos ahora debe heredar de `Android.Support.V4.App.FragmentActivity` y no desde `Android.App.Activity` . 

-   **Actualizar los espacios de nombres** &ndash; clases que heredan de `Android.App.Fragment` ahora debe heredar de `Android.Support.V4.App.Fragment` . Quitar el uso de la instrucción " `using Android.App;` " en la parte superior del código fuente de archivos y sustituirlo por " `using Android.Support.V4.App` ". 

-   **Usar SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expone un `SupportingFragmentManager` propiedad que debe utilizarse para obtener una referencia a la `FragmentManager` . Por ejemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Con estos cambios en su lugar, será posible ejecutar una aplicación basada en el fragmento en Android 1.6 o 2.x, así como en panal y Sandwich de helado. 


## <a name="related-links"></a>Vínculos relacionados

- [Compatibilidad con Android biblioteca v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
