---
title: "Búsqueda y mejoras de Widget de pantalla principal"
description: "Este artículo tratan las mejoras de que Apple ha realizado en el sistema de Widget en iOS 10."
ms.topic: article
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: a6749ca9d8a793372ec088433780d622f2f05b41
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="search-and-home-screen-widget-enhancements"></a>Búsqueda y mejoras de Widget de pantalla principal

_Este artículo tratan las mejoras de que Apple ha realizado en el sistema de Widget en iOS 10._


Apple ha introducido varias mejoras en el sistema de Widget para asegurarse de que los widgets tienen un aspecto excelentes en cualquier fondo que existe en el nuevo iOS 10 la pantalla de bloqueo. Además, los widgets ahora contienen una [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propiedad que permite al desarrollador describir el contenido está disponible y permite al usuario expandir y contraer el contenido.

Widgets (también conocido como hoy en día extensiones) son un tipo especial de iOS extensión que muestre una pequeña cantidad de información útil o exponer la funcionalidad de específico de la aplicación de manera oportuna. Por ejemplo, la aplicación de noticias tiene un widget que muestra los titulares más importantes y la aplicación de calendario proporciona dos widgets diferentes: uno para mostrar hoy en día los eventos y uno para que muestren los próximos eventos.

Widgets son personalizables y pueden contener elementos de interfaz de usuario como texto, imágenes, botones, etcetera. Además, el desarrollador puede personalizar aún más el diseño de los widgets.

[ ![](widgets-images/widgets01.png "Widgets de ejemplo")](widgets-images/widgets01.png)

Hay dos lugares principales que un usuario puede ver e interactuar con los Widgets de una aplicación:

- **La pantalla de búsqueda** -los usuarios pueden agregar los Widgets que le resulte más útil a su pantalla de búsqueda. Se tiene acceso a la pantalla de búsqueda deslizando derecha en la página principal y el bloqueo de pantallas.
- **La pantalla Inicio** -desde la pantalla principal, el usuario puede usar un toque 3D para abrir la lista de acciones rápidas aplicando presión al icono de la aplicación. Widgets de una aplicación se muestra encima de la lista de acción rápida. Visite nuestro [Introducción a la entrada táctil 3D](~/ios/platform/3d-touch.md) documentación para obtener más información.

## <a name="widgets-developer-suggestions"></a>Sugerencias de desarrollador de widgets

Idealmente, el desarrollador siempre debe intente y diseñar Widgets que el usuario desee agregar a sus pantallas de búsqueda. Para ello, Apple tiene las siguientes sugerencias:

- **Crear una gran, experiencia concisa** -usuario desee Widgets que proporcionan información breve y concisa de las actualizaciones de estado o permitan realizar tareas sencillas rápidamente. Esto hace que proporciona la cantidad correcta de información e interactividad un esenciales. Siempre que sea posible, permite al usuario llevar a cabo una tarea determinada con un único punteo. Además, puesto que los Widgets no son compatibles con panorámica o desplazar, esto tendrá que tenerse en cuenta en el diseño del Widget.
- **Mostrar contenido rápidamente** -Widgets están diseñados para ser concisa, por lo que el usuario nunca debería tener que esperar contenido una vez que se muestra un Widget de carga. Widgets deben almacenar en caché su contenido localmente por lo que puede mostrar contenido reciente mientras se está cargando contenido actualizada en segundo plano.
- **Proporcione los márgenes y relleno adecuado** -Widgets nunca debería buscar abarrotados, lo que evita extender el contenido con los bordes de la vista del Widget. Siempre debe haber un margen ancho de píxel varios entre los bordes y el contenido. Apple también sugiere utilizando el icono de la aplicación, se muestra en la parte superior del Widget, como una guía de alineación. Si el Widget presenta un diseño de cuadrícula, asegúrese de que hay apropiado de relleno entre los elementos de la cuadrícula e intente limitar el número de elementos a cuatro max.
- **Usar diseños adaptables** -ancho de un Widget varían según el dispositivo se está ejecutando en y la orientación del dispositivo. Alto del Widget también puede variar en función de si se está mostrando en estado expandido (no admitido todos los Widgets) o Collapsed (valor predeterminado). Un Widget contraído tiene un valor de alto de filas de la tabla de aproximadamente 2,5 iOS estándar. El desarrollador puede solicitar el tamaño de un Widget de expandir pero idealmente debería ser menor que el alto de la pantalla. En el estado contraído, el Widget debe mostrar información esencial solo e independiente. Cuando expandido, el Widget debe mostrar información adicional que mejora el contenido principal que se muestra en el estado contraído. Widgets que se muestra en la lista de acciones rápido sólo estará en estado contraído.
- **No personalizar fondo del Widget** -Widgets se muestran sobre un fondo claro, difuminado proporcionado por el sistema. Esto se hace para fomentar la coherencia entre los Widgets y mejorar la legibilidad de su contenido. Evite usar una imagen como fondo Widget porque podría entrar en conflicto con papeles tapiz de bloqueo y la pantalla principal del usuario.
- **Usar la fuente del sistema en negro o en gris oscuro** : al mostrar texto en un Widget de la fuente del sistema funciona mejor. La fuente debe estar en un color gris oscuro o negro a destacarse con el fondo del Widget difuminado y ligero.
- **Proporcionar aplicación acceso cuando adecuado** -Widget siempre debe funcionar por separado desde su aplicación, sin embargo, si se requiere un poco más funcionalidad, el Widget debe ser capaz de iniciar la aplicación para ver o editar una parte específica de la información. Simplemente nunca incluye un botón "abrir la aplicación", permitir que el usuario pulse el contenido en Sí y abra nunca un 3rd party la aplicación.
- **Seleccione un método Clear, nombre de Widget concisa** -icono de la aplicación y el nombre del Widget siempre se muestran sobre el contenido del Widget. Apple sugiere utilizando nombre de la aplicación para su Widget principal y un nombre claro y conciso, para cualquier otra proporciona. Cuando se proporciona un título de Widget personalizado, debe agregarse como prefijo con el nombre de la aplicación (por ejemplo, cerca de mapas, mapas de restaurantes, etcetera.).
- **Informar cuando favorece la autenticación** : si una funcionalidad adicional o información está disponible solo cuando el usuario se autentica y firmado, se presenta al usuario. Por ejemplo, un transporte aplicación de uso compartido mi decir "Inicio de sesión para el transporte de libro".
- **Seleccione un Widget de lista de acción rápida** -si la aplicación proporciona más de un Widget, el desarrollador debe elegir uno para que se presentan cuando el usuario abre la lista de acciones rápido mediante la aplicación de presión al icono de la aplicación con 3D Touch.

Para obtener más información sobre cómo trabajar con los widgets, visite nuestro [Introducción a las extensiones](~/ios/platform/extensions.md), [Introducción a la entrada táctil 3D](~/ios/platform/3d-touch.md) documentación y de Apple [Guía de programación de la extensión de aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>Trabajar con dinamismo

Dinamismo garantiza que al texto del Widget sigue siendo legible cuando se presenta en la luz del Widget, fondo difuminado (proporcionado por el sistema). Antes de iOS 10, el desarrollador utilizaría una [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) para dinamismo del Widget. Por ejemplo:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Esto ha desusado en iOS 10 y debe reemplazarse por un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o un [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Por ejemplo:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Trabajar con los Widgets contraídos y expandidos

Nuevo iOS 10, widgets ahora contienen una [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propiedad que permite al desarrollador describir el contenido está disponible y permite al usuario expandir y contraer el contenido.

Cuando inicialmente se muestra un Widget, está en un estado contraído. Un Widget contraído tiene un valor de alto de filas de la tabla de aproximadamente 2,5 iOS estándar. El desarrollador puede solicitar el tamaño de un Widget de expandir pero idealmente debería ser menor que el alto de la pantalla. 

En el estado contraído, el Widget debe mostrar información esencial solo e independiente. Cuando expandido, el Widget debe mostrar información adicional que mejora el contenido principal que se muestra en el estado contraído. Por ejemplo, la aplicación del tiempo muestra las condiciones de tiempo actual cuando se contrae y agrega la hora cuando se expanden de previsión.

Widgets que se muestra en la lista de acciones rápido sólo estará en estado contraído. Si la aplicación proporciona más de un Widget, el desarrollador debe elegir uno para que se presentan cuando el usuario abre la lista de acciones rápido mediante la aplicación de presión al icono de la aplicación con 3D Touch.

El ejemplo siguiente es de una simple hoy extensión (el Widget) que controla los estados contraído y expandido:

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

Observe el código específico de modo de presentación de Widget en detalle. Para informar al sistema que este Widget es compatible con el estado expandido, usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Para obtener el modo de presentación actual del Widget, utiliza:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Para obtener el tamaño máximo de estado del contraído o expandido, usa:

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

Y para controlar el cambio de estado (modo de presentación), utiliza:

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

Además de establecer el tamaño solicitado para cada estado (contraído o expandido), también actualiza el contenido que se va a mostrar para que coincida con el nuevo tamaño.

## <a name="summary"></a>Resumen

Este artículo tiene cubiertos las mejoras de que Apple ha realizado en el sistema de Widget en iOS 10 y se muestra cómo se implementan en Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introducción a las extensiones](~/ios/platform/extensions.md)
- [Introducción a la entrada táctil 3D](~/ios/platform/3d-touch.md)
- [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
