---
title: Con las versiones anteriores proporcionar compatibilidad con el paquete de compatibilidad con Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 48ef40ce8560fd9fbb842dde70622d968591ab98
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022249"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Con las versiones anteriores proporcionar compatibilidad con el paquete de compatibilidad con Android

La utilidad de fragmentos sería limitados sin compatibilidad con Android dispositivos (API nivel 11) 3.0 con versiones anteriores. Para proporcionar esta funcionalidad, Google introdujo el [Support Library](https://developer.android.com/sdk/compatibility-library.html) (llamado originalmente el *Android Compatibility Library* cuando se lanzó) qué backports algunas de las API desde las versiones más recientes de Android a versiones anteriores de Android. Es el paquete de soporte técnico de Android que permite a los dispositivos que ejecutan Android 1.6 (API nivel 4) para Android 2.3.3. (Nivel de API 10).

> [!NOTE]
> Solo el `ListFragment` y `DialogFragment` están disponibles a través del paquete de compatibilidad con Android. Ninguno de los demás fragmentos subclases, tales como el `PreferenceFragment,` se admiten en el paquete de compatibilidad con Android. No funcionará en las aplicaciones Android 3.0. 


## <a name="adding-the-support-package"></a>Agregar el paquete de soporte técnico

El paquete de soporte Android no se agrega automáticamente a una aplicación de Xamarin.Android. Xamarin ofrece el [paquete NuGet de biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar la adición de las bibliotecas de compatibilidad para una aplicación de Xamarin.Android. Para incluir los paquetes de soporte técnico en su aplicación incluir de Xamarin.Android el [Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente en el proyecto de Xamarin.Android, como se muestra en la captura de pantalla siguiente: 

[![Paquete de v4 de captura de pantalla de la biblioteca de compatibilidad con Android que se agrega al proyecto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Después de han realizado estos pasos, es posible utilizar fragmentos en versiones anteriores de Android. Las API del fragmento funcionará ahora mismo en estas versiones anteriores, con las siguientes excepciones: 

-   **Cambiar la versión Android mínima** &ndash; la aplicación ya no necesita tener como destino Android 3.0 o posterior, como se muestra a continuación: 

    [![Captura de pantalla de Android mínima de destino que se va a establecer en el manifiesto de Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Extender FragmentActivity** &ndash; las actividades que alojan fragmentos ahora debe heredar de `Android.Support.V4.App.FragmentActivity` y no desde `Android.App.Activity` . 

-   **Actualice los espacios de nombres** &ndash; clases que heredan de `Android.App.Fragment` ahora debe heredar de `Android.Support.V4.App.Fragment` . Quitar el uso de la instrucción " `using Android.App;` " en la parte superior del código fuente de archivos y reemplácelo por " `using Android.Support.V4.App` ". 

-   **Usar SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expone un `SupportingFragmentManager` propiedad que debe usarse para obtener una referencia a la `FragmentManager` . Por ejemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Con estos cambios en su lugar, será posible ejecutar una aplicación basada en el fragmento de Android 1.6 o 2.x, así como en Honeycomb y Ice Cream Sandwich. 


## <a name="related-links"></a>Vínculos relacionados

- [Compatibilidad con Android biblioteca v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
