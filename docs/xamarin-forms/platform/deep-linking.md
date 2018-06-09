---
title: Indización de la aplicación y vinculación en profundidad
description: En este artículo se muestra cómo utilizar la indización de la aplicación y vinculación en profundidad para que se pueda buscar en dispositivos iOS y Android contenido de la aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: 9cc5177a585af1569385840ab8c370993984ca2b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242497"
---
# <a name="application-indexing-and-deep-linking"></a>Indización de la aplicación y vinculación en profundidad

_Indización de aplicación permite a las aplicaciones que de lo contrario serían olvidadas después de que algunos se usa para seguir siendo relevante que aparecen en los resultados de la búsqueda. Vinculación en profundidad permite a las aplicaciones responder a un resultado de búsqueda que contiene datos de aplicación, por lo general, vaya a una página que se hace referencia desde un vínculo profundo. En este artículo se muestra cómo utilizar la indización de la aplicación y vinculación en profundidad para que se pueda buscar en dispositivos iOS y Android contenido de la aplicación de Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Profundidad de vinculación con Xamarin.Forms y Azure, por [Universidad de Xamarin](https://university.xamarin.com/)**


Indización de aplicación de Xamarin.Forms y vinculación profunda proporcionan una API para la publicación de metadatos para la indización de aplicación como a los usuarios navegan por las aplicaciones. A continuación, se puede buscar contenido indizado para en la búsqueda de Spotlight, en la búsqueda de Google o en una búsqueda en la web. Puntee en un resultado de búsqueda que contiene un vínculo profundo desencadenará un evento que pueda ser controlado por una aplicación y se utiliza normalmente para navegar a la página de referencia desde el vínculo profundo.

La aplicación de ejemplo muestra una aplicación de lista de tareas donde se almacenan los datos en una base de datos local de SQLite, tal y como se muestra en las capturas de pantalla siguiente:

![](deep-linking-images/screenshots.png "Aplicación TodoList")

Cada `TodoItem` se indiza a la instancia creada por el usuario. Búsqueda específica de la plataforma, a continuación, puede utilizarse para localizar los datos indizados de la aplicación. Cuando el usuario puntea en un elemento de resultado de búsqueda para la aplicación, se inicia la aplicación, el `TodoItemPage` se navega a y el `TodoItem` al que hace referencia desde el vínculo se muestra.

Para obtener más información sobre el uso de una base de datos de SQLite, consulte [trabajar con una base de datos Local](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> Indización de aplicación de Xamarin.Forms y profunda vinculación funcionalidad solo está disponibles en las plataformas iOS y Android y requiere iOS 9 y API 23 respectivamente.

## <a name="setup"></a>Programa de instalación

Las secciones siguientes proporcionan las instrucciones de instalación adicionales para usar esta característica en las plataformas iOS y Android.

### <a name="ios"></a>iOS

En la plataforma iOS, no hay ninguna instalación adicional necesaria para utilizar esta funcionalidad.

### <a name="android"></a>Android

En la plataforma Android, hay una serie de requisitos previos que deben cumplirse para utilizar la indización de la aplicación y profunda vinculación funcionalidad:

1. Una versión de la aplicación debe ser en vivo en Google Play.
1. Un sitio Web complementaria debe estar registrado en la aplicación en la consola de Google para desarrolladores. Una vez que la aplicación está asociada a un sitio Web, las direcciones URL pueden ser indizada que funcionan para el sitio Web y la aplicación, que, a continuación, puede proporcionarse en resultados de búsqueda. Para obtener más información, consulte [aplicación indización de búsqueda de Google](https://support.google.com/googleplay/android-developer/answer/6041489) en el sitio Web de Google.
1. La aplicación debe admitir los intentos de dirección URL HTTP en el `MainActivity` (clase), lo que saber qué tipos de esquemas de datos de dirección URL de la indización de aplicación de la aplicación puede responder a. Para obtener más información, consulte [configurar el filtro de intención](~/android/platform/app-linking.md#configure-intent-filter).

Una vez que se cumplen estos requisitos previos, es necesario usar indización de aplicación de Xamarin.Forms y vinculación en profundidad en la plataforma Android el programa de instalación adicional siguiente:

1. Instalar el [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) paquete de NuGet en el proyecto de aplicación de Android.
1. En el `MainActivity.cs` de archivos, importe la `Xamarin.Forms.Platform.Android.AppLinks` espacio de nombres.
1. En el `MainActivity.OnCreate` invalidar, agregue la siguiente línea de código bajo `Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

Para obtener más información, consulte [vínculo profundo contenido con navegación a direcciones URL Xamarin.Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) en el blog de Xamarin.

## <a name="indexing-a-page"></a>Indización de una página

El proceso de indización de una página y exponer en la búsqueda de Google y Spotlight es como sigue:

1. Crear un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) que contiene los metadatos necesarios para la página, junto con un vínculo profundo para volver a la página cuando el usuario selecciona el contenido indizado en resultados de búsqueda de índice.
1. Registrar el [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia hasta el índice de búsqueda.

En el ejemplo de código siguiente se muestra cómo crear un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia:

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

El [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia contiene un número de propiedades cuyos valores son necesarios para la página de índice y crear un vínculo profundo. El [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Title/), [ `Description` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Description/), y [ `Thumbnail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Thumbnail/) propiedades se utilizan para identificar el contenido indizado cuando aparece en los resultados de la búsqueda. El [ `IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propiedad está establecida en `true` para indicar que actualmente se está viendo el contenido indizado. El [ `AppLinkUri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.AppLinkUri/) propiedad es una `Uri` que contiene la información necesaria para volver a la página actual y mostrar actual `TodoItem`. En el ejemplo siguiente se muestra un ejemplo `Uri` para la aplicación de ejemplo:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

Esto `Uri` contiene toda la información necesaria para iniciar el `deeplinking` aplicación, vaya a la `DeepLinking.TodoItemPage`y mostrar la `TodoItem` que tiene un `ID` de `ec38ebd1-811e-4809-8a55-0d028fce7819`.

## <a name="registering-content-for-indexing"></a>Registra el contenido para la indexación

Una vez un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) se ha creado la instancia, se debe registrar para la indización para que aparezcan en resultados de búsqueda. Esto se logra con la [ `RegisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.RegisterLink/p/Xamarin.Forms.IAppLinkEntry/) método, como se muestra en el ejemplo de código siguiente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Esto agrega el [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia a la aplicación [ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/) colección.

> [!NOTE]
> El `RegisterLink` método también se puede utilizar para actualizar el contenido que se ha indexado para una página.

Una vez un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia se ha registrado para la indización, pueden aparecer en los resultados de búsqueda. Captura de pantalla siguiente muestra el contenido indizado que aparecen en los resultados de búsqueda en la plataforma iOS:

![](deep-linking-images/ios-search.png "Contenido indizado en los resultados de búsqueda en iOS")

## <a name="de-registering-indexed-content"></a>Anular registro de indizar contenido

El [ `DeregisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.DeregisterLink/p/Xamarin.Forms.IAppLinkEntry/) método se usa para quitar contenido indizado de los resultados de búsqueda, como se muestra en el ejemplo de código siguiente:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Esto quita la [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia a partir de la aplicación [ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/) colección.

> [!NOTE]
> En Android no es posible quitar contenido indizado de los resultados de búsqueda.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Responder a un vínculo profundo

Cuando el contenido indizado aparece en los resultados de búsqueda y se selecciona un usuario, el `App` la clase para la aplicación recibirá una solicitud para controlar la `Uri` contenidos en el contenido indizado. Esta solicitud se pueden procesar en el [ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/) invalidar, tal y como se muestra en el ejemplo de código siguiente:

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

El [ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/) método comprueba que los datos recibidos `Uri` está diseñado para la aplicación, antes de analizar el `Uri` en la página para navegar y el parámetro que se pasan a la página. Una instancia de la página para navegar a se crea y el `TodoItem` representado por la página de parámetro se recupera. El [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de la página que se abrirá, a continuación, se establece en el `TodoItem`. Esto garantiza que, cuando la `TodoItemPage` se muestra por el [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/) método, mostrará el `TodoItem` cuyo `ID` se encuentra en el vínculo profundo.

## <a name="making-content-available-for-search-indexing"></a>Hace que el contenido disponible para la indización de búsqueda

Cada vez que se muestra la página representada por un vínculo profundo, el [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propiedad puede establecerse en `true`. En iOS y Android Esto hace que la [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instancia disponible para la indización de búsqueda y en solo iOS, también facilita la `AppLinkEntry` disponibles para la entrega de la instancia. Para obtener más información acerca de la entrega, vea [Introducción a la entrega](~/ios/platform/handoff.md).

En el ejemplo de código siguiente se demuestra cómo establecer el [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propiedad `true` en el [ `Page.OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) invalidar:

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

De forma similar, cuando se navega a la página representada por un vínculo profundo de fuera de la [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propiedad puede establecerse en `false`. En iOS y Android, esto impide la [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) publicidad detiene la instancia se publica para la indización de búsqueda y en iOS única, TI también el `AppLinkEntry` instancia para la entrega. Esto puede realizarse en el [ `Page.OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/) invalidar, tal y como se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>Proporciona datos para la entrega

En iOS, pueden almacenarse los datos específicos de la aplicación al indizar la página. Esto se logra mediante la adición de datos a la [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) colección, que es un `Dictionary<string, string>` para almacenar pares de clave-valor que se usan en la entrega. Entrega es una manera para que el usuario iniciar una actividad en uno de sus dispositivos y siga esa actividad en otro de sus dispositivos (como identificada por la cuenta de iCloud del usuario). El código siguiente muestra un ejemplo de almacenar pares de clave-valor específicos de la aplicación:

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Valores almacenados en el [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) colección se almacenan en los metadatos de la página indizada y se restaurará cuando el usuario puntea en un resultado de búsqueda que contiene un vínculo profundo (o cuando se usa la entrega para ver el contenido en otro firmado en el dispositivo).

Además, pueden especificarse valores para las claves siguientes:

- `contentType` : un `string` que especifica el identificador uniforme de tipo de contenido indizado. La convención recomendada que se usará para este valor es el nombre de tipo de la página que contiene el contenido indizado.
- `associatedWebPage` : un `string` que representa la página web para visitar si el contenido indizado también pueden verse en la web, o si la aplicación admite vínculos profundos de Safari.
- `shouldAddToPublicIndex` : un `string` del `true` o `false` que controla si se va a agregar el contenido indizado al índice de nube pública de Apple, que, a continuación, se puede presentar a los usuarios que no ha instalado la aplicación en su dispositivo iOS o no. Sin embargo, solo porque se ha establecido el contenido para la indización de público, no significa que, se agregará automáticamente al índice de nube pública de Apple. Para obtener más información, consulte [público indización de búsqueda](~/ios/platform/search/nsuseractivity.md). Tenga en cuenta que esta clave debe establecerse en `false` al agregar datos personales a la [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) colección.

> [!NOTE]
> El `KeyValues` colección no se usa en la plataforma Android.

Para obtener más información acerca de la entrega, vea [Introducción a la entrega](~/ios/platform/handoff.md).

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar la indización de la aplicación y vinculación en profundidad para que se pueda buscar en dispositivos iOS y Android contenido de la aplicación de Xamarin.Forms. Indización de aplicación permite a las aplicaciones a permanecer relevante ya que aparecen en los resultados de búsqueda que en caso contrario se olvidan sobre después de unos pocos usa.


## <a name="related-links"></a>Vínculos relacionados

- [Vinculación de profundidad (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [API de búsqueda de iOS](~/ios/platform/search/index.md)
- [Vincular a la aplicación en Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)
- [IAppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinkEntry/)
- [IAppLinks](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinks/)
