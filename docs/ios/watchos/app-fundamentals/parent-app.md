---
title: Trabajar con la aplicación principal en Xamarin watchOS
description: Este documento describe cómo trabajar con una aplicación de padre watchOS en Xamarin. Se trata de WatchKit extensiones de aplicaciones, aplicaciones de iOS, almacenamiento compartido y mucho más.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 3af2cce0d84e3934eeb89917990f111d29aadef1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790697"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Trabajar con la aplicación principal en Xamarin watchOS

> [!IMPORTANT]
> Acceso a la aplicación principal con los ejemplos siguientes solo funciona en aplicaciones de inspección de watchOS 1.


Hay diferentes maneras de comunicación entre la aplicación de inspección y la aplicación de iOS que se incluye con:

- Inspección extensiones can [llamar a un método](#code) en la aplicación principal que se ejecuta en segundo plano en el iPhone.

- Inspección extensiones can [comparten una ubicación de almacenamiento](#storage) con la aplicación de iPhone primario.

- Uso de entrega para pasar datos de un vistazo o de notificación a la aplicación de inspección, que se envía al usuario a un controlador de la interfaz específica en la aplicación.

La aplicación principal se conoce a veces también que la aplicación de contenedor.


<a name="code" />

## <a name="run-code"></a>Ejecutar código

Comunicación entre una extensión de inspección y la aplicación de iPhone principal se muestra en el [GpsWatch ejemplo](https://developer.xamarin.com/samples/GpsWatch).
La extensión de inspección puede solicitar la aplicación de iOS primario para realizar algún procesamiento en su nombre utilizando el `OpenParentApplication` método.

Esto es especialmente útil para tareas en ejecución largas (incluidas las solicitudes de red): solo el elemento principal aplicación de iOS pueden aprovechar las ventajas de procesamiento en segundo plano para completar estas tareas y guardar los datos recuperados en una ubicación accesible para la extensión de inspección.



### <a name="watch-kit-app-extension"></a>Extensión de la aplicación de inspección Kit

Llame a la `WKInterfaceController.OpenParentApplication` en la extensión de la aplicación de inspección. Devuelve un `bool` que indica si la solicitud del método se ha enviado correctamente o no. Compruebe el `error` parámetro en la respuesta `Action` determinar si existe, se ha producido durante el método que se ejecuta en la aplicación de iPhone.

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```


### <a name="ios-app"></a>Aplicación iOS

Todas las llamadas de una extensión de la aplicación de inspección se enrutan a través de la aplicación de iPhone `HandleWatchKitExtensionRequest` método.
Si está realizando diferentes solicitudes en la aplicación de inspección, a continuación, este método deberá consultar la `userInfo` diccionario para determinar cómo procesar la solicitud.


```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```


<a name="storage" />

## <a name="shared-storage"></a>Almacenamiento compartido

Si configura un [grupo de aplicación](~/ios/watchos/app-fundamentals/app-groups.md) , a continuación, las extensiones de iOS 8 (incluidas las extensiones de inspección) pueden compartir datos con la aplicación primaria.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

El código siguiente puede escribirse en la extensión de la aplicación de inspección y la aplicación de iPhone primario para que puede hacer referencia a un conjunto común de `NSUserDefaults`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>Archivos

La extensión de inspección y aplicación de iOS también puede compartir archivos mediante una ruta de acceso de archivo comunes.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Nota: si la ruta de acceso es `null` , a continuación, compruebe el [configuración del grupo de aplicación](~/ios/watchos/app-fundamentals/app-groups.md) para asegurarse de los perfiles de aprovisionamiento se hayan configurado correctamente y han descargado o instalado en el equipo de desarrollo.

Para obtener más información, consulte el [las capacidades de grupo de aplicaciones de](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentación.

## <a name="wormholesharp"></a>WormHoleSharp

Un mecanismo de código abierto popular para watchOS 1 (basado en [MMWormHole](https://github.com/mutualmobile/MMWormhole)) para pasar datos o comandos entre la aplicación primaria y la aplicación de inspección.

Puede configurar con un grupo de aplicación similar al siguiente en la aplicación de iOS de túnel y ver extensión:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Descargue la versión de C# [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).



## <a name="related-links"></a>Vínculos relacionados

- [GpsWatch (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WormHoleSharp (ejemplo)](https://github.com/Clancey/WormHoleSharp)
- [Referencia de WKInterfaceController de Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Datos de uso compartido de Apple con la aplicación que lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
