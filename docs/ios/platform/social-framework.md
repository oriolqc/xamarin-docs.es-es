---
title: Marco sociales en Xamarin.iOS
description: El marco de trabajo Social proporciona una API unificada para interactuar con redes sociales incluido Twitter y Facebook, así como SinaWeibo para los usuarios en China.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 9b7269282b18adc46f53b708a0af4934a1621d23
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788200"
---
# <a name="social-framework-in-xamarinios"></a>Marco sociales en Xamarin.iOS

_El marco de trabajo Social proporciona una API unificada para interactuar con redes sociales incluido Twitter y Facebook, así como SinaWeibo para los usuarios en China._

Mediante el marco sociales permite a las aplicaciones interactuar con las redes sociales de una única API sin tener que administrar la autenticación. Incluye un sistema que proporciona el controlador de vista para crear publicaciones, así como una abstracción que permite consumir API de cada red social a través de HTTP.

> [!IMPORTANT]
> Para que una API de multiplataforma para conectarse a distintas redes sociales, consulte el [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) componente en el almacén de componentes de Xamarin.

## <a name="connecting-to-twitter"></a>Conectarse a Twitter

### <a name="twitter-account-settings"></a>Configuración de la cuenta de Twitter

Para conectarse a Twitter mediante el marco de trabajo sociales, una cuenta debe configurarse en la configuración del dispositivo tal y como se muestra a continuación:

 [![](social-framework-images/twitter01.png "Configuración de la cuenta de Twitter")](social-framework-images/twitter01.png#lightbox)

Una vez que una cuenta se ha indicado y verificado con Twitter, cualquier aplicación en el dispositivo que utiliza las clases de Framework sociales para acceder a Twitter usará esta cuenta.

### <a name="sending-tweets"></a>Enviar Tweets

El marco de trabajo sociales incluye un controlador denominado `SLComposeViewController` que presenta una vista proporcionado por el sistema para la edición y enviar un tweet. Captura de pantalla siguiente muestra un ejemplo de esta vista:

 [![](social-framework-images/twitter02.png "Esta captura de pantalla muestra un ejemplo de la SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Para usar un `SLComposeViewController` con Twitter, debe crearse una instancia del controlador mediante una llamada a la `FromService` método con `SLServiceType.Twitter` tal y como se muestra a continuación:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Después de la `SLComposeViewController` se devuelve la instancia, se puede utilizar para presentar una interfaz de usuario para enviar a Twitter. Sin embargo, lo primero que hay que hacer es comprobar la disponibilidad de la red social, Twitter en este caso, mediante una llamada a `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` nunca envía un tweet directamente sin interacción del usuario. Sin embargo, se puede inicializar con los métodos siguientes:

-   `SetInitialText` : Agrega el texto inicial que se va a mostrar en el tweet. 
-  `AddUrl` : Agrega una dirección Url para el tweet.
-  `AddImage` : Agrega una imagen a la tweet.


Una vez inicializado, una llamada a `PresentVIewController` muestra la vista creada por el `SLComposeViewController`. El usuario puede, a continuación, opcionalmente, editar y enviar el tweet o cancele enviarlo. En cualquier caso, se debe descartar el controlador en el `CompletionHandler`, donde el resultado también se puede comprobar para ver si se envió el tweet o se cancela, tal y como se muestra a continuación:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>En el ejemplo se tweet

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

El marco de trabajo sociales también incluye compatibilidad para realizar solicitudes HTTP a las redes sociales. Encapsula la solicitud en un `SLRequest` clase que se usa para tener como destino la API de la red social determinado.

Por ejemplo, el código siguiente realiza una solicitud en Twitter para obtener la escala de tiempo pública (mediante la expansión en el código anterior):

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

Echemos un vistazo a este código en detalle. En primer lugar, se obtiene acceso para el almacén de cuentas y obtiene el tipo de una cuenta de Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

A continuación, pide al usuario si la aplicación puede tener acceso a su cuenta de Twitter y, si se concede el acceso, la cuenta se carga en memoria y actualiza la interfaz de usuario:

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

Cuando el usuario solicita los datos de la escala de tiempo (punteando en un botón de la interfaz de usuario), la aplicación primero formula una solicitud para obtener acceso a los datos de Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
Este ejemplo está limitando los resultados devueltos a las últimas diez entradas mediante la inclusión de `?count=10` en la dirección URL. Por último, se adjunta la solicitud a la cuenta de Twitter (que se cargó anteriormente) y realiza la llamada a Twitter para capturar los datos:

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

Si los datos se cargaron correctamente, se mostrará los datos JSON sin formato (como en la salida de ejemplo siguiente):

[![](social-framework-images/twitter03.png "Un ejemplo de la presentación de datos sin formato de JSON")](social-framework-images/twitter03.png#lightbox)

En una aplicación real, los resultados JSON, a continuación, se puede analizar como normal y los resultados que se presentan al usuario. Vea [servicios Web de introducción](~/cross-platform/data-cloud/web-services/index.md) para obtener información acerca de cómo analizar JSON.

## <a name="connecting-to-facebook"></a>Conectar con Facebook

### <a name="facebook-account-settings"></a>Configuración de la cuenta de Facebook

Conectarse a Facebook con el marco de trabajo Social es casi idéntico al proceso usado para Twitter mostrado anteriormente. Debe configurarse una cuenta de usuario de Facebook en la configuración del dispositivo tal y como se muestra a continuación:

[![](social-framework-images/facebook01.png "Configuración de la cuenta de Facebook")](social-framework-images/facebook01.png#lightbox)

Una vez configurado, cualquier aplicación en el dispositivo que utiliza el marco de trabajo sociales usará esta cuenta para conectar con Facebook.

### <a name="posting-to-facebook"></a>Exponer en Facebook

Puesto que el marco de trabajo Social es una API unificada que se ha diseñado para tener acceso a varias redes sociales, el código siga siendo casi idéntico independientemente de la red social que se va a usar.

Por ejemplo, el `SLComposeViewController` puede utilizarse exactamente como se muestra en el ejemplo de Twitter mostrado anteriormente, solo diferentes que cambia las opciones y la configuración específica de Facebook. Por ejemplo:

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

Cuando se usa con Facebook, el `SLComposeViewController` muestra una vista que es casi idéntica al ejemplo de Twitter, que muestra **Facebook** como el título en este caso:

[![](social-framework-images/facebook02.png "La presentación de SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Llamada a API Graph de Facebook

Similar a Twitter, el marco de trabajo sociales del ejemplo `SLRequest` objeto puede utilizarse con API graph de Facebook. Por ejemplo, el código siguiente devuelve información de la API de graph acerca de la cuenta de Xamarin (mediante el aumento en el código anterior):

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

La única diferencia entre este código y la versión de Twitter presentada anteriormente, es el requisito de Facebook para obtener un aplicación/Developer identificador específico (que puede generar desde Portal para desarrolladores de Facebook) que se debe establecer como una opción al realizar la solicitud:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Error al configurar esta opción (o mediante una clave no válida) se producirá un error o no hay datos que se devuelven.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar el marco de trabajo sociales para interactuar con Twitter y Facebook. Ha mostrado cómo configurar las cuentas para cada red social en la configuración del dispositivo. También explica cómo usar el `SLComposeViewController` para presentar una vista unificada para el registro en las redes sociales. Además, examina la `SLRequest` clase que se usa para llamar a API de cada red social.


## <a name="related-links"></a>Vínculos relacionados

- [SocialFrameworkDemo (ejemplo)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)
