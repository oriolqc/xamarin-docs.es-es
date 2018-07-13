---
title: Indexación de la aplicación y vinculación en profundidad
description: En este artículo se muestra cómo usar la indexación de la aplicación y vinculación en profundidad para que el contenido de la aplicación de Xamarin.Forms que se puede buscar en los dispositivos iOS y Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: 7a102765a3633b8abaf01b3f090d8253230bc16b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996101"
---
# <a name="application-indexing-and-deep-linking"></a>Indexación de la aplicación y vinculación en profundidad

_Indexación de la aplicación permite a las aplicaciones que de lo contrario serían olvidadas después de unos pocos se usa para mantener su relevancia por aparezcan en los resultados de búsqueda. Vinculación en profundidad permite a las aplicaciones responder a un resultado de búsqueda que contiene los datos de la aplicación, normalmente, vaya a una página que se hace referencia desde un vínculo profundo. En este artículo se muestra cómo usar la indexación de la aplicación y vinculación en profundidad para que el contenido de la aplicación de Xamarin.Forms que se puede buscar en los dispositivos iOS y Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Vinculación profunda con Xamarin.Forms y Azure, por [Xamarin University](https://university.xamarin.com/)**


Indexación de la aplicación de Xamarin.Forms y vinculación en profundidad proporcionan una API para la publicación de metadatos para la indexación de la aplicación como los usuarios navegan por las aplicaciones. Contenido indizado, a continuación, se puede buscar en búsqueda de Spotlight, en búsqueda de Google o en una búsqueda web. Pulsar un resultado de búsqueda que contiene un vínculo profundo desencadenará un evento que puede controlarse mediante una aplicación y se utiliza normalmente para navegar a la página al que hace referencia desde el vínculo profundo.

La aplicación de ejemplo muestra una aplicación de lista de tareas pendientes donde los datos se almacenan en una base de datos SQLite local, como se muestra en las capturas de pantalla siguiente:

![](deep-linking-images/screenshots.png "Aplicación TodoList")

Cada `TodoItem` instancia creada por el usuario está indizada. Búsqueda específica de la plataforma, a continuación, puede utilizarse para localizar los datos indizados de la aplicación. Cuando el usuario puntea un elemento de resultado de búsqueda para la aplicación, se inicia la aplicación, el `TodoItemPage` se navega a y el `TodoItem` al que hace referencia desde el vínculo se muestra.

Para obtener más información sobre el uso de una base de datos de SQLite, consulte [trabajar con una base de datos Local](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> Indexación de la aplicación de Xamarin.Forms y la funcionalidad de vinculación profunda solo está disponibles en las plataformas iOS y Android y requiere iOS 9 y API 23, respectivamente.

## <a name="setup"></a>Programa de instalación

Las secciones siguientes proporcionan las instrucciones de instalación adicionales para usar esta característica en las plataformas iOS y Android.

### <a name="ios"></a>iOS

En la plataforma iOS, no hay ninguna configuración adicional necesaria para usar esta funcionalidad.

### <a name="android"></a>Android

En la plataforma Android, hay una serie de requisitos previos que deben cumplirse para que use la indexación de la aplicación y la funcionalidad de vinculación profunda:

1. Una versión de la aplicación debe estar activa en Google Play.
1. Un sitio de Web complementario debe estar registrado en la aplicación de consola del desarrollador de Google. Una vez que la aplicación está asociada con un sitio Web, las direcciones URL pueden ser indizadas ese trabajo para el sitio Web y la aplicación, que puede proporcionarse en resultados de búsqueda. Para obtener más información, consulte [aplicación indización en búsqueda de Google](https://support.google.com/googleplay/android-developer/answer/6041489) en el sitio Web de Google.
1. La aplicación debe admitir las intenciones de dirección URL de HTTP en el `MainActivity` (clase), que indican la indización de los tipos de esquemas de datos de dirección URL de aplicación en la aplicación puede responder a. Para obtener más información, consulte [configuración del filtro de intención](~/android/platform/app-linking.md#configure-intent-filter).

Una vez que se cumplen estos requisitos previos, se requiere la siguiente configuración adicional para usar la indexación de la aplicación de Xamarin.Forms y vinculación en profundidad en la plataforma Android:

1. Instalar el [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) paquete de NuGet en el proyecto de aplicación de Android.
1. En el `MainActivity.cs` archivo, importe el `Xamarin.Forms.Platform.Android.AppLinks` espacio de nombres.
1. En el `MainActivity.OnCreate` invalide, agregue la siguiente línea de código debajo `Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

Para obtener más información, consulte [vínculo profundo contenido con la navegación a direcciones URL Xamarin.Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) en el blog de Xamarin.

## <a name="indexing-a-page"></a>Indexación de una página

El proceso de indización de una página y exponerlo a búsqueda de Spotlight y de Google es como sigue:

1. Crear un [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) que contiene los metadatos necesarios para la página, junto con un vínculo profundo para volver a la página cuando el usuario selecciona el contenido indizado en resultados de búsqueda de índice.
1. Registrar el [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia para indizar para la búsqueda.

En el ejemplo de código siguiente se muestra cómo crear un [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia:

```csharp
AppLinkEntry GetAppLink (TodoItem item)
{
  var pageType = GetType ().ToString ();
  var pageLink = new AppLinkEntry {
    Title = item.Name,
    Description = item.Notes,
    AppLinkUri = new Uri (string.Format ("http://{0}/{1}?id={2}",
      App.AppName, pageType, WebUtility.UrlEncode (item.ID)), UriKind.RelativeOrAbsolute),
    IsLinkActive = true,
    Thumbnail = ImageSource.FromFile ("monkey.png")
  };

  return pageLink;
}
```

El [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia contiene un número de propiedades cuyos valores son necesarios para la página de índice y crear un vínculo profundo. El [ `Title` ](xref:Xamarin.Forms.IAppLinkEntry.Title), [ `Description` ](xref:Xamarin.Forms.IAppLinkEntry.Description), y [ `Thumbnail` ](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) propiedades se utilizan para identificar el contenido indizado cuando aparece en los resultados de búsqueda. El [ `IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propiedad está establecida en `true` para indicar que actualmente se está viendo el contenido indizado. El [ `AppLinkUri` ](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) propiedad es un `Uri` que contiene la información necesaria para volver a la página actual y mostrar actual `TodoItem`. El ejemplo siguiente muestra un ejemplo `Uri` para la aplicación de ejemplo:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

Esto `Uri` contiene toda la información necesaria para iniciar el `deeplinking` aplicación, vaya a la `DeepLinking.TodoItemPage`y mostrar el `TodoItem` que tiene un `ID` de `ec38ebd1-811e-4809-8a55-0d028fce7819`.

## <a name="registering-content-for-indexing"></a>Registra el contenido para la indexación

Una vez un [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) se ha creado la instancia, se debe registrar para la indización para aparecer en los resultados de búsqueda. Esto se consigue con la [ `RegisterLink` ](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) método, como se muestra en el ejemplo de código siguiente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Esto agrega el [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia a la aplicación [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) colección.

> [!NOTE]
> El `RegisterLink` método también se puede utilizar para actualizar el contenido que se ha indexado para una página.

Una vez un [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia se ha registrado para la indexación, puede aparecer en los resultados de búsqueda. Captura de pantalla siguiente muestra el contenido indizado aparezcan en los resultados de búsqueda en la plataforma de iOS:

![](deep-linking-images/ios-search.png "Contenido indizado en los resultados de búsqueda en iOS")

## <a name="de-registering-indexed-content"></a>Anular el registro de indiza contenido

El [ `DeregisterLink` ](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) método se usa para quitar contenido indizado de resultados de búsqueda, como se muestra en el ejemplo de código siguiente:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Esto quita el [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia de la aplicación [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) colección.

> [!NOTE]
> En Android no es posible quitar contenido indizado de los resultados de búsqueda.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Responder a un vínculo profundo

Cuando el contenido indizado aparece en los resultados de búsqueda y un usuario, se selecciona el `App` la clase para la aplicación recibirá una solicitud para controlar la `Uri` contenidos en el contenido indizado. Se puede procesar esta solicitud en el [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) invalidar, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class App : Application
{
  ...

  protected override async void OnAppLinkRequestReceived (Uri uri)
  {
    string appDomain = "http://" + App.AppName.ToLowerInvariant () + "/";
    if (!uri.ToString ().ToLowerInvariant ().StartsWith (appDomain)) {
      return;
    }

    string pageUrl = uri.ToString ().Replace (appDomain, string.Empty).Trim ();
    var parts = pageUrl.Split ('?');
    string page = parts [0];
    string pageParameter = parts [1].Replace ("id=", string.Empty);

    var formsPage = Activator.CreateInstance (Type.GetType (page));
    var todoItemPage = formsPage as TodoItemPage;
    if (todoItemPage != null) {
      var todoItem = App.Database.Find (pageParameter);
      todoItemPage.BindingContext = todoItem;
      await MainPage.Navigation.PushAsync (formsPage as Page);
    }

    base.OnAppLinkRequestReceived (uri);
  }
}
```

El [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) método comprueba que los datos recibidos `Uri` está destinado a la aplicación, antes de analizar el `Uri` en la página para navegar a y el parámetro que se pasará a la página. Una instancia de la página para navegar a se crea y el `TodoItem` representado por la página se recupera el parámetro. El [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página para navegar, a continuación, se establece en el `TodoItem`. Esto garantiza que, cuando el `TodoItemPage` se muestra por el [ `PushAsync` ](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) método, se mostrarán el `TodoItem` cuyo `ID` se encuentra en el vínculo profundo.

## <a name="making-content-available-for-search-indexing"></a>Hace que el contenido disponible para la indización de búsqueda

Cada vez que se muestra la página representada por un vínculo profundo, el [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propiedad puede establecerse en `true`. En iOS y Android Esto hace que el [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instancia disponible para la indización de búsqueda y solo en iOS, también facilita la `AppLinkEntry` instancia disponible para su entrega. Para obtener más información acerca de la entrega, vea [Introducción a la entrega](~/ios/platform/handoff.md).

El ejemplo de código siguiente se muestra cómo establecer el [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propiedad `true` en el [ `Page.OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidar:

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

De forma similar, cuando la página representada por un vínculo profundo se navega fuera de la [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propiedad puede establecerse en `false`. En iOS y Android, Esto detiene el [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) publicidad detiene la instancia que se anuncian para la indización de búsqueda y en iOS únicamente, TI también el `AppLinkEntry` instancia para su entrega. Esto puede realizarse en el [ `Page.OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) invalidar, tal y como se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>Proporciona datos a la entrega

En iOS, se pueden almacenar datos específicos de la aplicación al realizar la indización la página. Esto se logra mediante la adición de datos a la [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) colección, que es un `Dictionary<string, string>` para almacenar los pares clave-valor que se usan en entrega. Comportamiento de la transición es una forma para el usuario iniciar una actividad en uno de sus dispositivos y seguir esa actividad en otro de sus dispositivos (según se identifica mediante la cuenta de iCloud del usuario). El código siguiente muestra un ejemplo de almacenar pares de clave-valor específicos de la aplicación:

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Los valores almacenados en el [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) colección se almacenarán en los metadatos de la página indizado y se restaurará cuando el usuario puntea en un resultado de búsqueda que contiene un vínculo profundo (o cuando se usa el comportamiento de la transición para ver el contenido en otro ha iniciado sesión dispositivo).

Además, se pueden especificar valores para las claves siguientes:

- `contentType` : un `string` que especifica el identificador uniforme de tipo del contenido indizado. La convención recomendada que se utilizará para este valor es el nombre de tipo de la página que contiene el contenido indizado.
- `associatedWebPage` : un `string` que representa la página web para visitar si también se puede ver el contenido indizado en la web, o si la aplicación admite vínculos profundos de Safari.
- `shouldAddToPublicIndex` – una `string` del `true` o `false` que controla si se debe o no agregar el contenido indizado para el índice de la nube pública de Apple, que, a continuación, se puede presentar a los usuarios que no ha instalado la aplicación en su dispositivo iOS. Sin embargo, sólo porque se ha establecido el contenido para la indización de público, no significa que se agregará automáticamente al índice de la nube pública de Apple. Para obtener más información, consulte [pública indización de búsqueda](~/ios/platform/search/nsuseractivity.md). Tenga en cuenta que esta clave se debe establecer en `false` al agregar datos personales a la [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) colección.

> [!NOTE]
> El `KeyValues` colección no se usa en la plataforma Android.

Para obtener más información acerca de la entrega, vea [Introducción a la entrega](~/ios/platform/handoff.md).

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar la indexación de la aplicación y vinculación en profundidad para que el contenido de la aplicación de Xamarin.Forms que se puede buscar en los dispositivos iOS y Android. Indexación de la aplicación permite a las aplicaciones destacar por aparezcan en los resultados de búsqueda que de lo contrario serían olvidados sobre después de unos pocos usa.


## <a name="related-links"></a>Vínculos relacionados

- [Vinculación profunda (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [API de búsqueda de iOS](~/ios/platform/search/index.md)
- [Vinculación a la aplicación en Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
