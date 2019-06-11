---
title: Redes sociales en Xamarin.iOS
description: El marco de redes sociales proporciona una API unificada para interactuar con las redes sociales como Facebook y Twitter, así como SinaWeibo para los usuarios en China.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 4548f74495ff7ed8ef07d31cdb3f1370c5275dd4
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827887"
---
# <a name="social-framework-in-xamarinios"></a>Redes sociales en Xamarin.iOS

_El marco de redes sociales proporciona una API unificada para interactuar con las redes sociales como Facebook y Twitter, así como SinaWeibo para los usuarios en China._

Mediante el marco de redes sociales permite a las aplicaciones interactúan con las redes sociales desde una sola API sin tener que administrar la autenticación. Incluye un sistema que proporciona el controlador de vista para crear publicaciones, así como una abstracción que permite utilizar las API de cada red social a través de HTTP.

> [!IMPORTANT]
> Para que una API multiplataforma para conectarse a varias redes sociales, consulte el [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) componente en el Store del componente de Xamarin.

## <a name="connecting-to-twitter"></a>Conectarse a Twitter

### <a name="twitter-account-settings"></a>Configuración de la cuenta de Twitter

Para conectarse a Twitter mediante el marco de redes sociales, una cuenta debe configurarse en la configuración del dispositivo tal como se muestra a continuación:

 [![](social-framework-images/twitter01.png "Configuración de la cuenta de Twitter")](social-framework-images/twitter01.png#lightbox)

Una vez que se han incluido una cuenta y se ha comprobado con Twitter, cualquier aplicación en el dispositivo que utiliza las clases de redes sociales para tener acceso a Twitter usará esta cuenta.

### <a name="sending-tweets"></a>Envío de Tweets

El marco de redes sociales incluye un controlador llamado `SLComposeViewController` que presenta una vista de sistema proporcionado para la edición y enviar un tweet. Captura de pantalla siguiente muestra un ejemplo de esta vista:

 [![](social-framework-images/twitter02.png "Esta captura de pantalla muestra un ejemplo de la SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Para usar un `SLComposeViewController` con Twitter, debe crearse una instancia del controlador mediante una llamada a la `FromService` método con `SLServiceType.Twitter` tal como se muestra a continuación:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Después de la `SLComposeViewController` se devuelve la instancia, se puede usar para presentar una interfaz de usuario para publicar en Twitter. Sin embargo, la primera cosa que hacer es comprobar la disponibilidad de la red social, Twitter en este caso, mediante una llamada a `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` Nunca envíe un tweet directamente sin interacción del usuario. Sin embargo, se puede inicializar con los métodos siguientes:

-   `SetInitialText` : Agrega el texto inicial para mostrar en el tweet. 
-  `AddUrl` : Agrega una dirección Url para el tweet.
-  `AddImage` : Agrega una imagen al tweet.


Una vez inicializado, una llamada a `PresentVIewController` muestra la vista creada por el `SLComposeViewController`. El usuario puede, a continuación, opcionalmente, editar y enviar el tweet o cancelar enviarlo. En cualquier caso, se debe descartar el controlador en el `CompletionHandler`, donde el resultado también se puede comprobar para ver si se envió el tweet o se cancela, tal como se muestra a continuación:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Ejemplo de tweet

El código siguiente muestra cómo utilizar el `SLComposeViewController` para presentar una vista que se usa para enviar un tweet:

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>Llamar a API de Twitter

El marco de redes sociales también incluye compatibilidad para realizar solicitudes HTTP a las redes sociales. Encapsula la solicitud en un `SLRequest` clase que se usa como destino la API de la red social determinado.

Por ejemplo, el código siguiente realiza una solicitud a Twitter para la escala de tiempo pública (si se expande en el código anterior):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Echemos un vistazo a este código en detalle. En primer lugar, se obtiene acceso a la cuenta de Store y obtiene el tipo de una cuenta de Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

A continuación, pide al usuario si la aplicación puede tener acceso a su cuenta de Twitter y, si se concede acceso, la cuenta se carga en memoria y la interfaz de usuario actualizado:

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

Cuando el usuario solicita los datos de escala de tiempo (pulsando un botón en la interfaz de usuario), la aplicación de formularios en primer lugar una solicitud de acceso a los datos de Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
En este ejemplo está limitando los resultados devueltos a las últimas diez entradas mediante la inclusión de `?count=10` en la dirección URL. Por último, adjunta la solicitud a la cuenta de Twitter (que se cargó anteriormente) y realiza la llamada a Twitter para capturar los datos:

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

Si los datos se cargan correctamente, se mostrará los datos JSON sin procesar (como se muestra en la salida de ejemplo siguiente):

[![](social-framework-images/twitter03.png "Un ejemplo de la presentación de datos JSON sin formato")](social-framework-images/twitter03.png#lightbox)

En una aplicación real, los resultados JSON, a continuación, se puede analizar como normal y los resultados presentados al usuario. Consulte [servicios Web de introducción](~/cross-platform/data-cloud/web-services/index.md) para obtener información sobre cómo analizar JSON.

## <a name="connecting-to-facebook"></a>Conexión a Facebook

### <a name="facebook-account-settings"></a>Configuración de la cuenta de Facebook

Conexión a Facebook con el marco de trabajo Social es casi idéntico al proceso usado para Twitter mostrado anteriormente. Debe configurarse una cuenta de usuario de Facebook en la configuración del dispositivo tal como se muestra a continuación:

[![](social-framework-images/facebook01.png "Configuración de la cuenta de Facebook")](social-framework-images/facebook01.png#lightbox)

Una vez configurado, cualquier aplicación en el dispositivo que usa el marco de redes sociales usará esta cuenta para conectar con Facebook.

### <a name="posting-to-facebook"></a>Exponer en Facebook

Puesto que el marco de redes sociales es una API unificada que se ha diseñado para tener acceso a varias redes sociales, el código sigue siendo casi idéntico independientemente de la red social que se va a usar.

Por ejemplo, el `SLComposeViewController` puede utilizarse exactamente como se muestra en el ejemplo de Twitter mostrado anteriormente, la única diferencia es cambiar las opciones y configuración específica de Facebook. Por ejemplo:

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

Cuando se usa con Facebook, el `SLComposeViewController` muestra una vista que es casi idéntica al ejemplo de Twitter, que muestra **Facebook** como título en este caso:

[![](social-framework-images/facebook02.png "La presentación SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Llamada a API Graph de Facebook

Similar a Twitter, el marco de redes sociales del ejemplo `SLRequest` objeto puede utilizarse con graph API de Facebook. Por ejemplo, el código siguiente devuelve información de graph API sobre la cuenta de Xamarin (mediante el aumento en el código anterior):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

La única diferencia entre este código y la versión de Twitter presentada anteriormente, es el requisito de Facebook para obtener un desarrollador o aplicación identificador específico (que puede generar desde el Portal para desarrolladores de Facebook) que se debe establecer como una opción al realizar la solicitud:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

No se pudo establecer esta opción (o mediante una clave no válida) producirá un error o no hay datos que se devuelven.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar el marco de redes sociales para interactuar con Twitter y Facebook. Se ha mostrado cómo configurar las cuentas para cada red social en la configuración del dispositivo. También describen cómo usar el `SLComposeViewController` para presentar una vista unificada para el registro en las redes sociales. Además, examina la `SLRequest` clase que se usa para llamar a API de cada red social.


## <a name="related-links"></a>Vínculos relacionados

- [SocialFrameworkDemo (sample)](https://developer.xamarin.com/samples/monotouch/SocialFrameworkDemo/)
- [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)
