---
title: "Compatibilidad con Android panal previa con los paquetes de soporte técnico"
ms.topic: article
ms.prod: xamarin
ms.assetid: DACD0C14-5DDF-7BDE-6844-80550D301307
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 9ffa3733908affb8a6f3cc5a1ae2e83c5a15f0f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="supporting-pre-honeycomb-android-using-support-packages"></a>Compatibilidad con Android panal previa con los paquetes de soporte técnico

El *paquete de compatibilidad con Android* consta de las bibliotecas de ese puerto nuevo parte de la nueva API &ndash; como fragmentos &ndash; en versiones anteriores de Android. Por lo que al agregar el paquete de compatibilidad con Android, podemos ejecutar nuestra aplicación en dispositivos Android 2.3, como se muestra en las pantallas siguientes:

![Captura de pantalla de tutorial de fragmentos](supporting-pre-honeycomb-images/00.png)

![Captura de pantalla de detalles de la actividad](supporting-pre-honeycomb-images/01.png)

<a name="Adding_the_Support_Package" />

## <a name="adding-the-support-package"></a>Agregar el paquete de soporte técnico

El paquete de compatibilidad con Android no se agrega automáticamente a una aplicación Xamarin.Android. Xamarin ofrece la [paquete NuGet de biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar la adición de las bibliotecas de soporte técnico a una aplicación Xamarin.Android.
Para incluir los paquetes de soporte técnico en su Xamarin.Android aplicación incluyen el [biblioteca admite Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente en el proyecto Xamarin.Android, como se muestra en la captura de pantalla siguiente:

![Agregar el paquete de biblioteca de compatibilidad de Android v4](supporting-pre-honeycomb-images/02.png)

Una vez que se ha agregado el paquete, cambiar la plataforma de destino para Android 2.2 o superior:

![Captura de pantalla de cambiar el nivel de API del marco de destino](supporting-pre-honeycomb-images/03.png)

Además, asegúrese de que la versión mínima de Android tiene como destino el mismo nivel de API:

![Captura de pantalla de configuración de la versión mínima Android](supporting-pre-honeycomb-images/04.png)


<a name="Change_MainActivity_to_derive_from_FragmentActivity" />

### <a name="change-mainactivity-to-derive-from-fragmentactivity"></a>Cambiar MainActivity pueden derivar FragmentActivity

Las actividades que usen fragmentos deben heredar de `Xamarin.Support.V4.App.FragmentActivity`. Esta clase es una parte necesaria del paquete de soporte porque permite que los fragmentos se hospedan en las actividades, independientemente de la versión de Android. `MainActivity` requiere solo un pequeño cambio, ahora debe heredar de `Android.Support.V4.App.FragmentActivity`:

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

<a name="Change_DetailsActivity_to_derive_from_FragmentActivity" />

### <a name="change-detailsactivity-to-derive-from-fragmentactivity"></a>Cambiar DetailsActivity pueden derivar FragmentActivity

`DetailsActivity` También debe cambiarse de un `Activity` a una `FragmentActivity`. Como `FragmentManager` es no es compatible con versiones panal previo de Android, el paquete de soporte técnico de Android incluye una clase contenedora, `SupportFragmentManager`, que proporciona compatibilidad con versiones anteriores. Cada `FragmentActivity` tiene un `SupportFragmentManager` propiedad, y `DetailsActivity` se cambió para usar el `SupportFragmentManager` en lugar de la `FragmentManager`:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("index", 0);
       var details = DetailsFragment.NewInstance(index);
       var fragmentTransaction = SupportFragmentManager.BeginTransaction(); // Notice the change from FragmentManager to SupportFragmentManager
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

Una vez se completarán estos cambios, ahora tenemos una aplicación que se puede ejecutar en Android 1.6 y versiones posteriores, y que usa fragmentos para ajustar la interfaz de usuario para el tamaño de nuestro dispositivo de destino.


## <a name="related-links"></a>Vínculos relacionados

- [Android admite v4 de biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v4)
