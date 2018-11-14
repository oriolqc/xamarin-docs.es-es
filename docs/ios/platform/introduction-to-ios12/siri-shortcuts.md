---
title: Métodos abreviados de Siri de Xamarin.iOS
description: Este documento describe cómo usar métodos abreviados de Siri en iOS 12. Describe NSUserActivities, intentos personalizados, asignar métodos abreviados de voz, accesos directos pertinentes y mucho más.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 38e71914d9f7f6bb46ebeee8d548968c207d3b16
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617766"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Métodos abreviados de Siri de Xamarin.iOS

En [iOS 10](~/ios/platform/sirikit/index.md), Apple introdujo SiriKit, lo que permite a la mensajería de compilación, llamadas de VoIP, pagos, entrenamientos, reservas de viaje y buscar aplicaciones que interactúan con Siri de fotografías.

En [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit adquirida más tipos de aplicaciones y una mayor flexibilidad para la personalización de la interfaz de usuario.

iOS 12 agrega accesos directos de Siri, lo que permite a todos los tipos de aplicaciones para exponer su funcionalidad a Siri. Aprende de Siri cuando ciertas tareas en la aplicación son más relevantes para el usuario y utiliza este conocimiento para sugerir posibles acciones a través de _accesos directos_. Pulsar en un acceso directo o invocarlo con un comando de voz se abre una aplicación o ejecutar una tarea en segundo plano.

Métodos abreviados se deben usar para acelerar la capacidad de un usuario para realizar una tarea común: en muchos casos, sin necesidad de abrir la aplicación en cuestión.

## <a name="sample-app-soup-chef"></a>Aplicación de ejemplo: Sopa de Chef

Para entender mejor los métodos abreviados de Siri, eche un vistazo a la [sopa Chef](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/) aplicación de ejemplo. Chef sopa permite a los usuarios realizar pedidos de un restaurante sopa imaginaria, ver su historial de pedidos y definir frases que desea usar al ordenar sopa interactuando con Siri.

> [!TIP]
> Antes de probar la sopa de Chef en un dispositivo o simulador de iOS 12, habilite a las dos opciones siguientes, que son útiles al depurar los accesos directos:
>
> - En el **configuración** aplicación, habilitar **Developer > accesos directos recientes de presentación**.
> - En el **configuración** aplicación, habilitar **Developer > donaciones de presentación en pantalla de bloqueo**.
>
> Estos depuración make configuración fácil de encontrar-creado recientemente (en lugar de predecir) accesos directos en iOS pantalla de bloqueo y pantalla de búsqueda.

Para usar la aplicación de ejemplo:

- Instalar y ejecutar el Chef Sopa de aplicación de ejemplo en un simulador de iOS 12 o [dispositivo](#testing-on-device).
- Haga clic en el **+** botón de la parte superior derecha para crear un nuevo pedido.
- Seleccione un tipo de sopa, especifique una cantidad y las opciones y pulse **realizar pedido**.
- En el **historial de pedidos** pantalla, puntee en el orden recién creada para ver sus detalles.
- En la parte inferior de la pantalla Detalles del pedido, puntee en **agregar a Siri**.
- Grabar una frase de voz para asociar con el pedido y pulse **realiza**.
- Minimizar la sopa de Chef, invocar Siri y realizar el pedido nuevo mediante el uso de la frase de voz que acaba de grabar.
- Después de Siri completa el pedido, vuelva a abrir Sopa de Chef y tenga en cuenta que el nuevo pedido aparece en la **historial de pedidos** pantalla.

La aplicación de ejemplo muestra cómo:

- [Use un acceso directo a NSUserActivity para abrir una aplicación](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Usar un acceso directo de intención personalizado para realizar una tarea](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Proporcionar una interfaz de usuario para un propósito personalizado](#providing-a-user-interface-for-a-custom-intent).
- [Crear un acceso directo de voz](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info.plist y Entitlements.plist

Antes de profundizar más profundamente el código de sopa Chef, eche un vistazo a su **Info.plist** y **Entitlements.plist** archivos.

### <a name="infoplist"></a>Info.plist

El **Info.plist** de archivos en el **SoupChef** proyecto define el **identificador de paquete** como `com.xamarin.SoupChef`. Este identificador de paquete que se usará como prefijo para los identificadores de lote de la IU de intenciones y calidades de extensiones que se analizan más adelante en este documento.

El **Info.plist** archivo también contiene lo siguiente:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Esto `NSUserActivityTypes` par clave/valor indica que Chef sopa sabe cómo tratar un `OrderSoupIntent`y un [ `NSUserActivity` ](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/) tener un [ `ActivityType` ](https://developer.xamarin.com/api/property/Foundation.NSUserActivity.ActivityType/) de "com.xamarin.SoupChef.viewMenu".

Las actividades y los intentos personalizados pasados a la aplicación, en lugar de sus extensiones, se controlan en el `AppDelegate` (un [ `UIApplicationDelegate` ](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/)) por el [ `ContinueUserActivity` ](https://developer.xamarin.com/api/member/UIKit.UIApplicationDelegate.ContinueUserActivity/) método.

### <a name="entitlementsplist"></a>Entitlements.plist

El **Entitlements.plist** de archivos en el **SoupChef** proyecto contiene lo siguiente:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Esta configuración indica que la aplicación usa el grupo de aplicaciones "group.com.xamarin.SoupChef". El **SoupChefIntents** extensión de la aplicación usa este mismo grupo de aplicaciones, lo que permite a los dos proyectos compartir [`NSUserDefaults`](https://developer.xamarin.com/api/type/Foundation.NSUserDefaults/)
datos.

El `com.apple.developer.siri` clave indica que la aplicación interactúa con Siri.

> [!NOTE]
> El **SoupChef** conjuntos de configuración de compilación del proyecto **derechos personalizados** a **Entitlements.plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Uso de un acceso directo a NSUserActivity para abrir una aplicación

Para crear un acceso directo que se abre una aplicación que muestre el contenido específico, cree un `NSUserActivity` y adjúntelo al controlador de vistas de la pantalla que desea que el acceso directo para abrir.

### <a name="setting-up-an-nsuseractivity"></a>Configurar un NSUserActivity

En la pantalla del menú, `SoupMenuViewController` crea un `NSUserActivity` y lo asigna al controlador de vista [ `UserActivity` ](https://developer.xamarin.com/api/property/UIKit.UIResponder.UserActivity/) propiedad:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

Establecer el `UserActivity` propiedad _dona_ que Siri la actividad. De esta donación, Siri obtiene información sobre cuándo y dónde esta actividad es relevante para el usuario y aprende mejor sugerir en el futuro.

`NSUserActivityHelper` se incluye una clase de utilidad en el **SoupChef** solución, en el **SoupKit** biblioteca de clases. Crea un `NSUserActivity` y establece varias propiedades relacionadas con Siri y la búsqueda:

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

Tenga en cuenta lo siguiente:

- Establecer `EligibleForPrediction` a `true` indica que puede predecir esta actividad Siri y la superficie, como un acceso directo.
- El [ `ContentAttributeSet` ](https://developer.xamarin.com/api/property/Foundation.NSUserActivity.ContentAttributeSet/) matriz es un estándar [ `CSSearchableItemAttributeSet` ](https://developer.xamarin.com/api/type/CoreSpotlight.CSSearchableItemAttributeSet/) usa para incluir un `NSUserActivity` en los resultados de búsqueda de iOS.
- [`SuggestedInvocationPhrase`](https://developer.xamarin.com/api/property/Foundation.NSUserActivity.SuggestedInvocationPhrase/) es una frase que Siri sugerirá al usuario como una opción posible al asignar una frase a un acceso directo.

### <a name="handling-an-nsuseractivity-shortcut"></a>Control de acceso directo de una NSUserActivity

Para controlar un `NSUserActivity` contextual invocada por un usuario, una aplicación de iOS debe invalidar el `ContinueUserActivity` método de la `AppDelegate` (clase), respondiendo en función el `ActivityType` campo en el pasado `NSUserActivity` objeto:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Este método llama a `HandleUserActivity`, que busca el segue a la pantalla del menú y lo invoca:

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Asignación de una frase a una NSUserActivity

Para asignar una frase a una `NSUserActivity`, abra el archivo iOS **configuración** aplicación y elija **Siri & búsqueda > accesos directos**. A continuación, seleccione el acceso directo (en este caso, "Pedir la comida") y registre una frase.

Invocar Siri y el uso de esta frase se abrirá a Sopa de Chef en la pantalla del menú.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Uso de un acceso directo de intención personalizado para realizar una tarea

### <a name="defining-a-custom-intent"></a>Definir un color personalizado

Para proporcionar un acceso directo que permite que un usuario completar rápidamente una tarea específica relacionada con la aplicación, cree una intención personalizada. Un intento personalizado representa una tarea de un usuario que desee completar, parámetros relevantes para esa tarea y respuestas posibles resultantes de la ejecución de la tarea. Dependiendo de cómo se define un color personalizado, que realiza la llamada puede abrir la aplicación o ejecutar una tarea en segundo plano.

Use 10 Xcode para crear los intentos personalizados. En el [SoupChef repositorio](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), la intención personalizada se define en **OrderSoupIntentCodeGen**, un proyecto de C de objetivo. Abra este proyecto y seleccione el **Intents.intentdefinition** de archivos en el **Project Navigator** para ver el **OrderSoup** intención.

Tenga en cuenta lo siguiente:

- Tiene la intención un **categoría** de **orden**. Existen varias categorías predefinidas que pueden usarse para intentos personalizados; Seleccione la que mejor coincida con la tarea que permitirá su intención personalizado. Puesto que se trata de una Sopa de ordenación de la aplicación, **OrderSoupIntent** usa **orden**.
- El **confirmación** casilla indica si Siri debe solicitar confirmación antes de ejecutar la tarea. Para el **Sopa de orden** intención de sopa de Chef, esta opción está habilitada porque el usuario realiza una compra.
- El **parámetros** sección del archivo .intentdefinition define los parámetros relevantes para un acceso directo. Para realizar un pedido de sopa, Chef sopa debe conocer el tipo de sopa, su cantidad y todas las opciones asociadas.
Cada parámetro tiene un tipo; parámetro que no puede representarse mediante un tipo predefinido se establecen como **personalizado**.
- El **tipos de método abreviado** interfaz describe las diversas combinaciones de parámetros puede usar Siri cuando se sugiere el acceso directo. El asociado **título** y **subtítulo** secciones le permiten definir los mensajes que se va a usar Siri cuando se presenta un método abreviado sugerido para el usuario.
- El **admite la ejecución en segundo plano** debe activarse la casilla de verificación para cualquier acceso directo que se puede ejecutar sin tener que abrir la aplicación para la intervención del usuario.

### <a name="defining-custom-intent-responses"></a>Definir las respuestas personalizadas de intención

El **respuesta** elementos anidan debajo de la **OrderSoup** intención representa las posibles respuestas que se derivan de un pedido de sopa.

En el **OrderSoup** definición de la respuesta de la intención, tenga en cuenta lo siguiente:

- Una respuesta **propiedades** puede usarse para personalizar el mensaje comunicado al usuario. El **OrderSoup** tiene intención respuesta **sopa** y **waitTime** propiedades.
- El **plantillas de respuesta** especificar los distintos mensajes de aciertos y errores que se pueden usar para indicar el estado una vez completada la tarea de una intención.
- El **éxito** debe activarse la casilla de verificación para las respuestas que indican el éxito.
 - El **OrderSoupIntent** respuesta correcta se usa el **sopa** y **waitTime** propiedades para proporcionar un mensaje descriptivo y útil que describe el orden de sopa cuándo estará listo.

### <a name="generating-code-for-the-custom-intent"></a>Generar el código de la intención personalizada

Compilar el proyecto de Xcode que contiene esta definición personalizada de intención hace Xcode generar el código que se puede usar para interactuar mediante programación con la intención personalizada y sus respuestas.

Para ver este código generado:

- Abra **AppDelegate.m**.
- Agregue una importación al archivo de encabezado de la intención personalizado: `#import "OrderSoupIntent.h"`
- En cualquier método en la clase, agregue una referencia a `OrderSoupIntent`.
- Haga doble clic en `OrderSoupIntent` y elija **ir a definición**.
- Haga doble clic en el archivo abierta recientemente, **OrderSoupIntent.h**y seleccione **mostrar en Finder**.
- Se abrirá un **buscador** ventana que contiene un archivo .h y. m, que contiene el código generado.

Este código generado incluye:

- `OrderSoupIntent` : Una clase que representa la intención personalizada.
- `OrderSoupIntentHandling` : Un protocolo que define los métodos que se usará para confirmar que se debe ejecutar la intención y el método que realmente se ejecuta.
- `OrderSoupIntentResponseCode` : Una enumeración que define los distintos Estados de respuesta.
- `OrderSoupIntentResponse` : una clase que representa la respuesta para la ejecución de una intención.

### <a name="creating-a-binding-to-the-custom-intent"></a>Crear un enlace a la intención personalizado

Para usar el código generado por Xcode en una aplicación de Xamarin.iOS, cree un C# enlace para él.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Crear una biblioteca estática y C# definiciones de enlace

En el [SoupChef repositorio](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), eche un vistazo en la **OrderSoupIntentStaticLib** carpeta y abra el **OrderSoupIntentStaticLib.xcodeproj** proyecto Xcode.

Esto **biblioteca estática de Cocoa Touch** proyecto contiene el **OrderSoupIntent.h** y **OrderSoupIntent.m** los archivos generados por Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configuración de la configuración de compilación del proyecto de biblioteca estática

En Xcode **Project Navigator**, seleccione el proyecto de nivel superior, **OrderSoupIntentStaticLib**y vaya a **fases de compilación > compilar orígenes**.
Tenga en cuenta que **OrderSoupIntent.m** (que importa **OrderSoupIntent.h**) se muestra aquí. En **vincular binario con bibliotecas**, tenga en cuenta que **Intents.framework** y **Foundation.framework** se incluyen.
Con esta configuración en su lugar, el marco de trabajo se compilará correctamente.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Crear la biblioteca estática y generar C# definiciones de enlaces

Para compilar la biblioteca estática y generar C# definiciones de los enlaces para él, siga estos pasos:

- [Instalar objetivo Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), la herramienta usada para generar definiciones de los enlaces de los archivos .h y. m creados por Xcode.

- Configurar el sistema para usar las herramientas de línea de comandos de Xcode 10:

    > [!WARNING]
    > Actualizar las herramientas de línea de comandos seleccionado afecta a todas las versiones instaladas de Xcode en el sistema. Cuando termine de usar la sopa de Chef aplicación de ejemplo, asegúrese de revertir esta configuración a su configuración original.

    - En Xcode, elija **Xcode > Preferencias > ubicaciones** y establecer **herramientas de línea de comandos** para la instalación de Xcode 10 más reciente disponible en el sistema.

- En el terminal, `cd` a la **OrderSoupIntentStaticLib** directory.

- Tipo `make`, qué compilaciones:

    - La biblioteca estática, **libOrderSoupIntentStaticLib.a**
    - En el **bo** directorio de salida, C# definiciones de enlaces:
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

El **OrderSoupIntentBindings** proyecto, que se basa en esta biblioteca estática y sus definiciones de enlaces asociados, estos elementos genera automáticamente.
Sin embargo, ejecutar manualmente a través del proceso anterior se asegurará de que se compila según lo previsto.

Para obtener más información sobre cómo crear una biblioteca estática y objetivo Sharpie para crear C# definiciones de los enlaces, eche un vistazo a la [enlace de una biblioteca de iOS Objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) tutorial.

#### <a name="creating-a-bindings-library"></a>Creación de una biblioteca de enlaces

Con la biblioteca estática y el C# creado en las definiciones de los enlaces, la pieza restante necesarios para consumir Xcode generado por el código relacionado con la intención en un proyecto de Xamarin.iOS es una biblioteca de enlaces.

En el [repositorio de Chef sopa](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), abra el **SoupChef.sln** archivo. Entre otras cosas, esta solución contiene **OrderSoupIntentBinding**, una biblioteca de enlaces para la biblioteca estática generada anteriormente.

En concreto, tenga en cuenta que este proyecto incluye:

- **ApiDefinitions.cs** : un archivo generado anteriormente por objetivo Sharpie y agregar a este proyecto. Este archivo **acción de compilación** está establecido en **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** – otro archivo generado anteriormente por objetivo Sharpie y agregar a este proyecto. Este archivo **acción de compilación** está establecido en **ObjcBindingCoreSource**.
- Un **referencia nativa** a **libOrderSoupIntentStaticLib.a**, la biblioteca estática compilada anteriormente.

> [!NOTE]
> Ambos **ApiDefinitions.cs** y **StructsAndEnums.cs** contienen atributos tales como `[Watch (5,0), iOS (12,0)]`. Estos atributos, generados por Sharpie objetivo, se convirtió en comentario ya que no son necesarias para este proyecto.

Para obtener más información acerca de cómo crear un C# biblioteca de enlaces, echar un vistazo a la [enlace a una biblioteca de Objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) tutorial.

Tenga en cuenta que el **SoupChef** proyecto contiene una referencia a **OrderSoupIntentBinding**, lo que significa que puede ahora tener acceso, en C#, las clases, interfaces y enumeraciones contiene:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Agregar el archivo .intentdefinition en la solución

En el C# **SoupChef** solución, el **SoupKit** proyecto contiene código compartido entre la aplicación y sus extensiones. El **Intents.intentdefinition** archivo se ha colocado en el **Base.lproj** del **SoupKit**, y tiene un **acción de compilación** de **Contenido**. El proceso de compilación copia este archivo en el lote de aplicaciones de sopa Chef, donde es necesario para la aplicación funcione correctamente.

### <a name="donating-an-intent"></a>Donar una intención

En el orden de Siri sugerir un acceso directo, primero debe comprender cuando el acceso directo es relevante.

Para proporcionar a Siri esta comprensión, Chef sopa _dona_ un intento de Siri cada vez que el usuario coloca un pedido de sopa. Según esta donación: cuando se donaron, donde se donaron, los parámetros contiene: aprende Siri cuando sugerir el acceso directo en el futuro.

**SoupChef** usa el `SoupOrderDataManager` clase para colocar donaciones.
Cuando se llama para realizar un pedido Sopa de un usuario, el `PlaceOrder` método a su vez llama a [ `DonateInteraction` ](https://developer.xamarin.com/api/member/Intents.INInteraction.DonateInteraction/):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

Después de capturar una intención, se encapsula en un [ `INInteraction` ](https://developer.xamarin.com/api/type/Intents.INInteraction/).
El `INInteraction` tiene una [`Identifier`](https://developer.xamarin.com/api/property/Intents.INInteraction.Identifier/)
que coincide con el identificador único del pedido (Esto le resultará útil más adelante al eliminar donaciones intención que ya no son válidos). A continuación, la interacción se dona a Siri.

La llamada a la `order.Intent` captador capturas un `OrderSoupIntent` que representa el orden estableciendo su `Quantity`, `Soup`, `Options`, imagen y y una frase de invocación que se usará como una sugerencia cuando el usuario registra una frase para Siri asociar con la intención de:

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Eliminación de donaciones no válidos

Es importante quitar donaciones que ya no son válidos para que Siri no realizar sugerencias de método abreviado confusa o poco práctico.

En Sopa de Chef, el **menú Configurar** pantalla puede usarse para marcar un menú de elemento como no disponible. Siri ya no debe sugerir un acceso directo para pedir un elemento de menú no está disponible, por lo que la `RemoveDonation` método `SoupMenuManager` elimina donaciones para elementos de menú que ya no están disponibles. Esto lo consigue:

- Encontrar pedidos asociados con el elemento de menú ahora disponible.
- Tomar sus identificadores.
- Eliminando las interacciones que tienen ese mismo identificador.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Creación de una extensión Intents

El código que se ejecuta cuando Siri invoca una intención se coloca en una extensión Intents, que puede agregarse como un nuevo proyecto a la misma solución que una aplicación de Xamarin.iOS existente como Sopa de Chef. En el **SoupChef** solución, se llama a la extensión **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – Info.plist y Entitlements.plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info.plist

El **Info.plist** en el **SoupChefIntents** proyecto define el **identificador de paquete** como `com.xamarin.SoupChef.SoupChefIntents`.

El **Info.plist** archivo también contiene lo siguiente:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

En la fórmula anterior **Info.plist**:

- `IntentsRestrictedWhileLocked` Enumera las intenciones que solo se deben administrar el dispositivo se desbloquea.
- `IntentsSupported` Enumera las intenciones controladas por esta extensión.
- `NSExtensionPointIdentifier` Especifica el tipo de extensión de la aplicación (consulte [la documentación de Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obtener más información).
- `NSExtensionPrincipalClass` Especifica la clase que debe usarse para controlar las intenciones compatibles con esta extensión.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – Entitlements.plist

El **Entitlements.plist** en el **SoupChefIntents** proyecto tiene el **grupos de aplicaciones** capacidad. Esta funcionalidad está configurada para usar el mismo grupo de aplicaciones como la **SoupChef** proyecto:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Chef sopa conserva los datos con `NSUserDefaults`. Para compartir datos entre la aplicación y la extensión de la aplicación, que hacen referencia al mismo grupo de aplicaciones en sus **Entitlements.plist** archivos.

> [!NOTE]
> El **SoupChefIntents** conjuntos de configuración de compilación del proyecto **derechos personalizados** a **Entitlements.plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Controlar una tarea en segundo plano OrderSoupIntent

Una extensión Intents ejecuta las tareas en segundo plano necesarias para un acceso directo en función de un color personalizado.

Las llamadas de Siri el [ `GetHandler` ](https://developer.xamarin.com/api/member/Intents.INExtension.GetHandler/) método de la `IntentHandler` clase (definido en **Info.plist** como el `NSExtensionPrincipalClass`) para obtener una instancia de una clase que extiende `OrderSoupIntentHandling`, que se puede usar para controlar un `OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, definido en el **SoupKit** proyecto de código compartido, implementa dos métodos importantes:

- `ConfirmOrderSoup` – Confirma si realmente se debe ejecutar la tarea asociada con la intención
- `HandleOrderSoup` : Realiza el pedido de sopa y responde al usuario mediante una llamada al controlador de finalización en el pasado

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Controlar un OrderSoupIntent que se abre la aplicación

Una aplicación debe administrar correctamente las intenciones que no se ejecutan en segundo plano.
Se administran en la misma manera que `NSUserActivity` accesos directos, en el `ContinueUserActivity` método `AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Proporcionar una interfaz de usuario para un color personalizado

Una extensión Intents UI proporciona una interfaz de usuario personalizada para una extensión Intents. En el **SoupChef** solución, **SoupChefIntentsUI** es una extensión de interfaz de usuario de Intents que proporciona una interfaz para **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info.plist y Entitlements.plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info.plist

El **Info.plist** en el **SoupChefIntentsUI** proyecto define el **identificador de paquete** como `com.xamarin.SoupChef.SoupChefIntentsui`.

El **Info.plist** archivo también contiene lo siguiente:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

En la fórmula anterior **Info.plist**:

- `IntentsSupported` indica que el `OrderSoupIntent` se controla mediante esta extensión Intents UI.
- `NSExtensionPointIdentifier` Especifica el tipo de extensión de la aplicación (consulte [la documentación de Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obtener más información).
- `NSExtensionMainStoryboard` Especifica el guión gráfico que define la interfaz principal de esta extensión

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – Entitlements.plist

El **SoupChefIntentsUI** proyecto no necesita un **Entitlements.plist** archivo.

### <a name="creating-the-user-interface"></a>Creación de la interfaz de usuario

Puesto que la **Info.plist** para **SoupChefIntentsUI** establece la `NSExtensionMainStoryboard` clave a `MainInterface`, el **MainInterace.storyboard** archivo define la interfaz para la extensión Intents UI.

En este guión gráfico, hay un controlador de vista única, de tipo **IntentViewController**. Hace referencia a dos vistas:

- **invoiceView**, de tipo `InvoiceView`
- **confirmationView**, de tipo `ConfirmOrderView`

> [!NOTE]
> Las interfaces para **invoiceView** y **confirmationView** se definen en **Main.storyboard** como vistas secundarias. IOS Designer en Visual Studio para Mac y Visual Studio 2017 no proporciona soporte técnico para ver o editar vistas secundarias; Para ello, abra **Main.storyboard** en Interface Builder de Xcode.

`IntentViewController` implementa el [`IINUIHostedViewControlling`](https://developer.xamarin.com/api/type/IntentsUI.IINUIHostedViewControlling/)
interfaz que utiliza para proporcionar una interfaz personalizada cuando se trabaja con las intenciones de Siri. El [`ConfigureView`](https://developer.xamarin.com/api/member/IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView/)
se invoca para personalizar la interfaz, mostrando la confirmación o la factura, dependiendo de si es que se va a confirmar la interacción ([`INIntentHandlingStatus.Ready`](https://developer.xamarin.com/api/type/Intents.INIntentHandlingStatus/)) o se ha ejecutado correctamente ([ `INIntentHandlingStatus.Success`](https://developer.xamarin.com/api/type/Intents.INIntentHandlingStatus/)):

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Para obtener más información sobre la `ConfigureView` método, vea la presentación de 2017 sesión WWDC de Apple, [What ' s New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Crear un acceso directo de voz

Sopa Chef proporciona una interfaz para asignar un acceso directo de voz a cada pedido, lo que permite a Sopa de orden con Siri. De hecho, la interfaz usada para registrar y asignar accesos directos de voz proporcionada por iOS y requiere muy poco código personalizado.

En `OrderDetailViewController`, cuando un usuario pulsa en la tabla **agregar a Siri** fila, el [ `RowSelected` ](https://developer.xamarin.com/api/member/UIKit.UITableViewSource.RowSelected/) método presenta una pantalla para agregar o editar un acceso directo de voz:

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

En función de si existe o no un acceso directo de voz existente para el orden mostrado actualmente, `RowSelected` presenta un controlador de vista de tipo [ `INUIEditVoiceShortcutViewController` ](https://developer.xamarin.com/api/type/IntentsUI.INUIEditVoiceShortcutViewController/) o [ `INUIAddVoiceShortcutViewController` ](https://developer.xamarin.com/api/type/IntentsUI.INUIAddVoiceShortcutViewController/).
En cada caso, `OrderDetailViewController` configura a sí mismo como el controlador de vista `Delegate`, motivo por el que también implementa [`IINUIAddVoiceShortcutViewControllerDelegate`](https://developer.xamarin.com/api/type/IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate/)
y [ `IINUIEditVoiceShortcutViewControllerDelegate` ](https://developer.xamarin.com/api/type/IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate/).

## <a name="testing-on-device"></a>Las pruebas en el dispositivo

Para ejecutar la sopa de Chef en un dispositivo, siga las instrucciones siguientes. Lea también el [Nota sobre el aprovisionamiento automático](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Grupo de aplicaciones, los identificadores de aplicación, perfiles de aprovisionamiento

En el **certificados, identificadores y perfiles** sección de la [Portal Apple Developer](https://developer.apple.com/), realice lo siguiente:

- Cree un grupo de aplicaciones para compartir datos entre la aplicación de sopa Chef y sus extensiones. Por ejemplo: **group.com.yourcompanyname.SoupChef**

- Crear tres identificadores de aplicación: uno para la propia aplicación, uno para la extensión Intents y otro para la extensión de Intents UI. Por ejemplo:

    - Aplicación: **com.yourcompanyname.SoupChef**
        - Este Id. de aplicación, asigne el SiriKit y **grupos de aplicaciones** capacidades.

    - Extensión de intents: **com.yourcompanyname.SoupChef.Intents**
        - Este Id. de aplicación, asigne el **grupos de aplicaciones** capacidad.

    - Extensión de interfaz de usuario de intents: **com.yourcompanyname.SoupChef.Intentsui**
        - Este identificador de aplicación, se necesita ninguna capacidad especial.

- Después de crear los identificadores de aplicación anterior, edite el **grupos de aplicaciones** capacidad asignada a la aplicación y la extensión de Intents, especificando el grupo de aplicación específica que creó anteriormente.

- Cree tres perfiles aprovisionamiento de desarrollo, uno para cada uno de los identificadores de aplicación nuevo.

- Descargue estos perfiles de aprovisionamiento y haga doble clic en cada uno de ellos para instalarlo. Si Visual Studio para Mac o Visual Studio 2017 ya se está ejecutando, reinícielo para asegurarse de que registra los nuevos perfiles de aprovisionamiento.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Editar archivo Info.plist, Entitlements.plist y código fuente

En Visual Studio para Mac o Visual Studio 2017, realice lo siguiente:

- Actualizar los diversos **Info.plist** archivos de la solución. Establecer la aplicación, la extensión Intents y la extensión Intents UI **identificador de paquete** para los identificadores de aplicación definidos anteriormente:

    - Aplicación: **com.yourcompanyname.SoupChef**
    - Extensión de intents: **com.yourcompanyname.SoupChef.Intents**
    - Extensión de interfaz de usuario de intents: **com.yourcompanyname.SoupChef.Intentsui**

- Actualización de la **Entitlements.plist** de archivos para el **SoupChef** proyecto:
    - Para el **grupos de aplicaciones** capacidad, establezca el grupo en el nuevo grupo de aplicaciones creado anteriormente (en el ejemplo anterior, era **group.com.yourcompanyname.SoupChef**).
    - Asegúrese de que **SiriKit** está habilitado.

- Actualización de la **Entitlements.plist** de archivos para el **SoupChefIntents** proyecto:
    - Para el **grupos de aplicaciones** capacidad, establezca el grupo en el nuevo grupo de aplicaciones creado anteriormente (en el ejemplo anterior, era **group.com.yourcompanyname.SoupChef**).

- Por último, abra **NSUserDefaultsHelper.cs**. Establecer el `AppGroup` variable en el valor del nuevo grupo de aplicación (por ejemplo, establézcalo en `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Configuración de la compilación

En Visual Studio para Mac o Visual Studio 2017:

- Abra las propiedades y opciones para la **SoupChef** proyecto. En el **firma de lote de iOS** pestaña, establezca **identidad de firma** en automático y **perfil de aprovisionamiento** para el aprovisionamiento de nuevos específicos de la aplicación de perfil que creó anteriormente.

- Abra las propiedades y opciones para la **SoupChefIntents** proyecto. En el **firma de lote de iOS** pestaña, establezca **identidad de firma** en automático y **perfil de aprovisionamiento** a las nuevas plataformas Intents específica de la extensión perfil de aprovisionamiento que creó anteriormente.

- Abra las propiedades y opciones para la **SoupChefIntentsUI** proyecto. En el **firma de lote de iOS** pestaña, establezca **identidad de firma** en automático y **perfil de aprovisionamiento** a la nueva interfaz de usuario de Intents específica de la extensión el perfil de aprovisionamiento, se creó anteriormente.

Con estos cambios en su lugar, la aplicación se ejecutará en un dispositivo iOS.

### <a name="automatic-provisioning"></a>El aprovisionamiento automático

Tenga en cuenta que puede usar [el aprovisionamiento automático](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) para realizar muchas de estas tareas directamente en el IDE de aprovisionamiento.
Sin embargo, el aprovisionamiento automático no configurar grupos de aplicaciones. Deberá configurar manualmente el **Entitlements.plist** archivos con el nombre del grupo de aplicaciones que le gustaría utilizar, visite el Portal para desarrolladores de Apple para crear el grupo de aplicaciones, asigne ese grupo de aplicación para cada identificador de aplicación creado por automático aprovisionamiento, volver a generar los perfiles de aprovisionamiento (aplicación, extensión Intents, extensión de Intents UI) para incluir el grupo de aplicación recién creada y descargarlas e instalarlas.

## <a name="related-links"></a>Vínculos relacionados

- [Chef sopa (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [Chef sopa (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introducción a los métodos abreviados de Siri: sesión WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Creación de voz con métodos abreviados de Siri: sesión WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Métodos abreviados de Siri en la pantalla del reloj de Siri: sesión WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Novedades de SiriKit: sesión WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Crear una extensión de aplicación de Intents (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
