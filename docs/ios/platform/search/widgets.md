---
title: Búsqueda y Home mejoras el Widget de pantalla en iOS 10
description: Este documento describe las mejoras de que Apple ha realizado en los Widgets en iOS 10, incluidas las actualizaciones de widgets de la pantalla principal y de búsqueda.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f693b480fff141c177ed135ced60afd65abd77de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082182"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Búsqueda y Home mejoras el Widget de pantalla en iOS 10

_En este artículo se trata las mejoras de que Apple ha realizado en el sistema de Widget en iOS 10._

Apple ha introducido varias mejoras en el sistema de Widget para asegurarse de que los widgets tienen un aspecto excelentes en cualquier en segundo plano que existe en el nuevo iOS 10 pantalla de bloqueo. Además, los widgets ahora contienen una [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propiedad que permite al desarrollador describir el contenido que está disponible y permite al usuario expandir y contraer el contenido.

Widgets (también conocido como hoy en día extensiones) son un tipo especial de extensión de iOS que muestre una pequeña cantidad de información útil o exponer la funcionalidad específica de la aplicación de manera oportuna. Por ejemplo, la aplicación de noticias tiene un widget que muestra los titulares más importantes y la aplicación de calendario ofrece dos widgets distintos: uno para mostrar hoy los eventos y otro para mostrar los eventos próximos.

Widgets son muy personalizables y pueden contener elementos de interfaz de usuario como texto, imágenes, botones, etcetera. Además, el desarrollador puede personalizar aún más el diseño de los widgets.

[![](widgets-images/widgets01.png "Widgets de ejemplo")](widgets-images/widgets01.png#lightbox)

Hay dos lugares principales que un usuario puede ver e interactuar con los Widgets de una aplicación:

- **La pantalla de búsqueda** -los usuarios pueden agregar los Widgets que les resulten más útiles para su pantalla de búsqueda. Se tiene acceso a la pantalla de búsqueda deslizándose hacia la derecha en las pantallas principal y de bloqueo.
- **La pantalla Inicio** -desde la pantalla principal, el usuario puede usar un toque 3D para abrir la lista de acciones rápidas aplicando presión en el icono de la aplicación. Widgets de una aplicación se mostrarán por encima de la lista de acciones rápidas. Consulte nuestra [Introducción a 3D Touch](~/ios/platform/3d-touch.md) documentación para obtener más información.

## <a name="widgets-developer-suggestions"></a>Sugerencias para desarrolladores de widgets

Idealmente, el desarrollador siempre debe probar y diseñar Widgets que el usuario desea agregar a las pantallas de búsqueda. Para ello, Apple tiene las siguientes sugerencias:

- **Crear una gran, ofrecer experiencia** -usuario desee Widgets que proporcionan breve y ofrecer información de actualizaciones de estado o les permiten realizar rápidamente tareas sencillas. Esto hace que proporciona la cantidad adecuada de información e interactividad un esenciales. Siempre que sea posible, permite al usuario llevar a cabo una tarea determinada con un solo toque. Además, puesto que no admiten Widgets panorámica o el desplazamiento, esto deberá tenerse en cuenta en el diseño del Widget.
- **Mostrar contenido rápidamente** -Widgets están diseñados para ser ofrecer, por lo que el usuario no debe tener nunca debe esperar para que el contenido cargar una vez que se muestra un Widget. Widgets deben almacenar en caché su contenido localmente, por lo que pueden mostrar el contenido reciente mientras se está cargando contenido nuevo en segundo plano.
- **Proporcione los márgenes y relleno adecuado** -Widgets nunca debería buscar atestados, lo que evita la extensión de contenido a los bordes de la vista de un Widget. Siempre debe haber un margen ancho de píxel varios entre los bordes y el contenido. Apple también sugiere el uso de icono de la aplicación que se muestra en la parte superior del Widget, como una guía de alineación. Si el Widget presenta un diseño de cuadrícula, asegúrese de que hay adecuado el relleno entre los elementos de la cuadrícula e intente limitar el número de elementos que el número máximo de cuatro.
- **Usar diseños adaptables** -ancho de un Widget variará según el dispositivo que se ejecuta y la orientación del dispositivo. Alto de un Widget también puede variar en función de si se muestra en estado expandido (no admitido todos los Widgets) o contraído (predeterminado). Un Widget contraído tiene un alto de filas de tabla de aproximadamente 2,5 iOS estándar. El desarrollador puede solicitar el tamaño de un Widget expandido pero lo ideal es que debe ser menor que el alto de la pantalla. En el estado contraído, el Widget debe mostrar información esencial solo e independiente. Cuando expandido, el Widget debe mostrar información adicional que mejora el contenido principal que se muestra en el estado contraído. Widgets que se muestra en la lista de acciones rápidas sólo estará en estado contraído.
- **No personalizar en segundo plano del Widget** -Widgets se muestran en un fondo claro, difuminado proporcionado por el sistema. Esto se hace para fomentar la coherencia entre los Widgets y mejorar la legibilidad de su contenido. Evite usar una imagen como fondo de un Widget porque podría entrar en conflicto con papeles tapiz de bloqueo y la pantalla principal del usuario.
- **Usar la fuente del sistema en negro o en gris oscuro** : al mostrar texto en un Widget de la fuente del sistema funciona mejor. La fuente debe ser de color gris oscuro o negro para destacar con el fondo del Widget claro, difuminado.
- **Proporcionar la aplicación acceso cuando adecuado** -Widget siempre debería funcionar por separado desde su aplicación, sin embargo, si se requiere una funcionalidad más profunda, el Widget debe ser capaz de iniciar la aplicación para ver o editar una parte específica de la información. Basta con no incluir nunca un botón "abrir la aplicación", permitir que el usuario puntee en el propio contenido y abra nunca un 3rd party la aplicación.
- **Seleccione un claro, conciso nombre de Widget** -icono de aplicación y el nombre del Widget siempre se muestran sobre el contenido del Widget. Apple sugiere el uso de nombre de la aplicación para su Widget principal y un nombre claro y conciso para cualquier otro que proporciona. Al proporcionar un título de Widget personalizado, debe agregarse como prefijo el nombre de la aplicación (por ejemplo, mapas cercanos, mapas de restaurantes, etcetera.).
- **Informar a cuando la autenticación agrega valor** : si una funcionalidad adicional o información está disponible solo cuando el usuario se autentica y ha iniciado sesión, presentarla al usuario. Por ejemplo, un viaje a la aplicación de uso compartido puede decir "Iniciar sesión en el manejo del libro".
- **Seleccione un Widget de lista de acción rápida** -si la aplicación proporciona más de un Widget, el desarrollador debe elegir uno para que se presentan cuando el usuario abre la lista de acciones rápidas aplicando presión en el icono de la aplicación con 3D Touch.

Para obtener más información sobre cómo trabajar con los widgets, consulte nuestra [Introducción a las extensiones](~/ios/platform/extensions.md), [Introducción a 3D Touch](~/ios/platform/3d-touch.md) documentación y Apple [Guía de programación de la extensión de aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>Trabajar con dinamismo

Dinamismo garantiza que el texto de un Widget permanece legible cuando se presentan en la luz del Widget, difuminado en segundo plano (proporcionado por el sistema). Antes de iOS 10, el desarrollador utilizaría una [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) de dinamismo del Widget. Por ejemplo:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Esto se ha desusado en iOS 10 y debe reemplazarse por un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o un [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Por ejemplo:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Trabajar con los Widgets contraídos y expandidos

Nuevo en iOS 10, widgets contienen ahora un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propiedad que permite al desarrollador describir el contenido que está disponible y permite al usuario expandir y contraer el contenido.

Cuando inicialmente se muestra un Widget, está en estado contraído. Un Widget contraído tiene un alto de filas de tabla de aproximadamente 2,5 iOS estándar. El desarrollador puede solicitar el tamaño de un Widget expandido pero lo ideal es que debe ser menor que el alto de la pantalla. 

En el estado contraído, el Widget debe mostrar información esencial solo e independiente. Cuando expandido, el Widget debe mostrar información adicional que mejora el contenido principal que se muestra en el estado contraído. Por ejemplo, la aplicación del tiempo muestra las condiciones meteorológicas actuales cuando se contrae y agrega los por hora cuando se expande de previsión.

Widgets que se muestra en la lista de acciones rápidas sólo estará en estado contraído. Si la aplicación proporciona más de un Widget, el desarrollador debe elegir uno para que se presentan cuando el usuario abre la lista de acciones rápidas aplicando presión en el icono de la aplicación con 3D Touch.

El ejemplo siguiente es de una simple hoy extensión (Widget) que controla los estados contraído y expandido:

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

Eche un vistazo en el código específico del modo de presentación del Widget en detalle. Para informar al sistema que este Widget es compatible con el estado expandido, usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Para configurar el modo de presentación actual del Widget, usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Para obtener el tamaño máximo para el estado contraído o expandido, usa:

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

Además de establecer el tamaño solicitado para cada estado (contraído o expandido), también actualiza el contenido que se muestra para que coincida con el nuevo tamaño.

## <a name="summary"></a>Resumen

En este artículo se tratan las mejoras de que Apple ha realizado en el sistema de Widget en iOS 10 y se muestra cómo implementarlas en Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introducción a las extensiones](~/ios/platform/extensions.md)
- [Introducción a 3D Touch](~/ios/platform/3d-touch.md)
- [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
