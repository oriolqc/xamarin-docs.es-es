---
title: "watchOS solución de problemas"
description: Problemas conocidos y soluciones para problemas de desarrollo watchOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: ce850b7890265b82774534ca0daaf25bed7e0c2d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="watchos-troubleshooting"></a>watchOS solución de problemas

Esta página contiene información adicional y soluciones alternativas para características aún se está desarrollando. Algunas de estas soluciones se aplican sólo a nuestras versiones de vista previa.

- [Problemas conocidos](#knownissues)

- [Quitar el canal alfa de imágenes de icono](#noalpha)

- [Cómo agregar manualmente archivos de controlador de interfaz](#add) para el generador de interfaz de Xcode.

- [Iniciar el WatchApp desde la línea de comandos](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemas conocidos

### <a name="general"></a>General

<a name="deploy" />
<!--
* You cannot deploy to the App Store *from within Visual Studio for Mac or Visual Studio*
    in the current release. You should create an **Archive** in Visual Studio for Mac
    and then switch to Xcode to upload the archive to iTunes Connect. Visual Studio
    is not currently supported (but will be a future release). Refer to the
    [deployment guide](~/ios/watchos/deploy-test/appstore.md) for more information.
-->

- Las versiones anteriores de Visual Studio para Mac muestran incorrectamente una de la la **AppleCompanionSettings** iconos como 88 x 88 píxeles; lo que resulta en un **falta el icono de Error** si intentan volver a enviar a la aplicación Almacén.
    Este icono debería ser 87 x 87 píxeles (29 unidades para  **@3x**  pantallas de Retina). No se puede solucionar este problema en Visual Studio para Mac: modificar el recurso de imagen en Xcode o editar manualmente el **Contents.json** archivo (para que coincida con [este ejemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Si el proyecto de extensión de inspección **Info.plist > Id. del lote WKApp** no es [establecer correctamente](~/ios/watchos/get-started/project-references.md) para que coincida con la aplicación de inspección **Id. del lote**, el depurador no podrá conectarse y Visual Esperará Studio para Mac con el mensaje *"En espera de conexión de depurador"*.

- Se admite la depuración en **notificaciones** modo pero puede ser poco confiables. Volver a intentar a veces funcionará. Confirme que la aplicación de inspección **Info.plist** `WKCompanionAppBundleIdentifier` se establece para que coincida con el identificador de paquete de la aplicación o el contenedor primario de iOS (ie. que se ejecuta en el iPhone).

<!--
- **Can't launch application on Watch simulator.** This seems to
    be an issue with the iOS Simulator hanging when trying to
    install an app that has changed. Xcode release notes (beta 4)
    includes a similar known issue:
    If the issue persists, reset the Simulator (**iOS Simulator > Reset Content and Settings...**).
-->

- Diseñador de iOS no muestra flechas de punto de entrada para los controladores de interfaz de vista o notificación.

- No se puede agregar dos `WKNotificationControllers` a un guión gráfico.
    Solución alternativa: La `notificationCategory` siempre se inserta el elemento en el guión gráfico XML con el mismo `id`. Para solucionar este problema, puede agregar controladores de notificación dos (o más), abra el archivo del guión gráfico en un editor de texto y, a continuación, cambie manualmente el `id` elemento para que sea único.

    [![](troubleshooting-images/duplicate-id-sml.png "Abrir el guión gráfico en un editor de texto y cambie manualmente el elemento Id. para que sea único")](troubleshooting-images/duplicate-id.png#lightbox)

- Puede ver un error "no se ha creado la aplicación" al intentar iniciar la aplicación. Se produce tras un **limpiar** cuando el proyecto de inicio se establece en el proyecto de extensión de inspección.
    La solución consiste en seleccionar **generar > volver a generar todo** y, a continuación, vuelva a iniciar la aplicación.

### <a name="visual-studio"></a>Programa para la mejora

Compatibilidad con el Diseñador de iOS para el Kit de inspección de *requiere* la solución que se configure correctamente. Si no se establecen las referencias del proyecto (vea [cómo establecer referencias](~/ios/watchos/get-started/project-references.md)), a continuación, la superficie de diseño no funcionará correctamente.

<!--
* New Watch Kit apps created in Visual Studio might not allow
    starting in Notifications mode.

* You cannot deploy to the App Store from Visual Studio (see [notes above](#deploy)
    and the [deployment guide](~/ios/watchos/deploy-test/appstore.md)). Use
    Visual Studio for Mac and Xcode on your Mac Build Host.
    -->

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Quitar el canal alfa de imágenes de icono

Iconos no deben contener un canal alfa (el canal alfa define las áreas transparentes de una imagen), en caso contrario, se rechazará la aplicación durante el envío de la tienda de aplicaciones con un error similar al siguiente:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Es fácil quitar el canal alfa en Mac OS X utilizando la **vista previa** aplicación:

1. Abra la imagen del icono de **vista previa** y, a continuación, elija **archivo > Exportar**.

2. El cuadro de diálogo que aparece incluirá un **alfa** casilla de verificación si hay un canal alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "El cuadro de diálogo que aparece incluye una casilla de verificación alfa si hay un canal alfa")

3. *Deje* el **alfa** casilla de verificación y **guardar** el archivo a la ubicación correcta.

4. La imagen del icono ahora debe pasar las comprobaciones de validación de Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Cómo agregar manualmente archivos de controlador de interfaz

> [!IMPORTANT]
> Compatibilidad del Kit de inspección de Xamarin incluye el diseño de guiones gráficos de inspección en el Diseñador de iOS (en Visual Studio para Mac y Visual Studio), que no requiere los pasos descritos a continuación. Simplemente asigne un controlador de interfaz de un nombre de clase en Visual Studio para el panel de propiedades de Mac y los archivos de código de C# se creará automáticamente.


*Si* usa el generador de interfaz de Xcode, siga estos pasos para crear nuevos controladores de interfaz para la aplicación de inspección y habilitar la sincronización con Xcode para que las salidas y las acciones están disponibles en C#:


1. Abra la aplicación de inspección **Interface.storyboard** en **Xcode interfaz generador**.
    
    ![](troubleshooting-images/add-6.png "Abrir el guión gráfico en el generador de interfaz de Xcode")

2. Arrastre una nueva `InterfaceController` en el guión gráfico:

    ![](troubleshooting-images/add-1.png "A InterfaceController")

3. Ahora puede arrastrar controles a la controladora de interfaz (p. ej. botones y etiquetas), pero no puede crear acciones o salidas todavía, porque no hay ningún **.h** archivo de encabezado. Los siguientes pasos hará que el necesario **.h** que se cree el archivo de encabezado.

    ![](troubleshooting-images/add-2.png "Un botón en el diseño")

4. Cierre el guión gráfico y vuelva a Visual Studio para Mac. Crear un nuevo archivo de C# **MyInterfaceController.cs** (o cualquier nombre que desee utilizar) en el **ver la extensión de la aplicación** proyecto (no la inspección propia aplicación donde es el guión gráfico). Agregue el código siguiente (actualizar el espacio de nombres, nombre de clase y el nombre de constructor):

        using System;
        using WatchKit;
        using Foundation;
        
        namespace WatchAppExtension  // remember to update this
        {
            public partial class MyInterfaceController // remember to update this
            : WKInterfaceController
            {
                public MyInterfaceController // remember to update this
                (IntPtr handle) : base (handle)
                {
                }
                public override void Awake (NSObject context)
                {
                    base.Awake (context);
                    // Configure interface objects here.
                    Console.WriteLine ("{0} awake with context", this);
                }
                public override void WillActivate ()
                {
                    // This method is called when the watch view controller is about to be visible to the user.
                    Console.WriteLine ("{0} will activate", this);
                }
                public override void DidDeactivate ()
                {
                    // This method is called when the watch view controller is no longer visible to the user.
                    Console.WriteLine ("{0} did deactivate", this);
                }
            }
        }

5. Cree otro nuevo archivo C# **MyInterfaceController.designer.cs** en el **ver la extensión de la aplicación** del proyecto y agregue el código siguiente. Asegúrese de actualizar el espacio de nombres, el nombre de clase y el `Register` atributo:

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;
    
    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```
    
    Sugerencia: Puede (opcionalmente) convertir este archivo en un nodo secundario del primer archivo con arrastrarlo hasta el otro archivo de C# en Visual Studio para Mac solución panel. A continuación, aparecerá como el siguiente:
    
    ![](troubleshooting-images/add-5.png "El panel de la solución")

6. Seleccione **generar > compilar todos los** para que reconozca la nueva clase de sincronización de Xcode (a través de la `Register` atributo) que hemos usado.

7. Vuelva a abrir el guión gráfico con el botón secundario en el archivo de guión gráfico de la aplicación de inspección y seleccionando **abrir con > Generador de interfaz de Xcode**:

    ![](troubleshooting-images/add-6.png "Abrir el guión gráfico en el generador de interfaz")

8. Seleccione el nuevo controlador de interfaz y asígnele el nombre de clase definida anteriormente, p. ej. `MyInterfaceController`.
Si todo lo que ha funcionado correctamente, debería aparecer automáticamente en el **clase:** lista desplegable y puede seleccionarlo desde allí.

    ![](troubleshooting-images/add-4.png "Configuración de una clase personalizada")

9. Elija la **Asistente Editor** ver en Xcode (el icono con dos círculos superpuestos) para que puedan ver el guión gráfico y el código en paralelo:

    ![](troubleshooting-images/add-7.png "El elemento de barra de herramientas del Editor de Asistente")

    Cuando el foco está en el panel de código, asegúrese de que también mirar la **.h** archivo de encabezado y si no, haga clic en la barra de ruta de navegación y seleccione el archivo correcto (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Seleccione MyInterfaceController")

10. Ahora puede crear salidas y las acciones por **Ctrl + arrastrar** desde el guión gráfico en el **.h** archivo de encabezado.

    ![](troubleshooting-images/add-9.png "Crear acciones y las salidas")

    Al soltar la operación de arrastre, se le pedirá que seleccione si desea crear un enchufe o una acción y elija su nombre:

    ![](troubleshooting-images/add-a.png "La salida y un cuadro de diálogo de acción")

11. Una vez que se guardan los cambios de guión gráfico y se cierra Xcode, vuelva a Visual Studio para Mac. Detectará los cambios del archivo de encabezado y agregar automáticamente el código para el **. designer.cs** archivo:


        [Register ("MyInterfaceController")]
        partial class MyInterfaceController
        {
            [Outlet]
            WatchKit.WKInterfaceButton myButton { get; set; }
        
            void ReleaseDesignerOutlets ()
            {
                if (myButton != null) {
                    myButton.Dispose ();
                    myButton = null;
                }
            }
        }


Ahora puede hacer referencia al control (o implementar la acción) en C#.


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Iniciar la aplicación de inspección de la línea de comandos

> [!IMPORTANT]
> Puede iniciar la aplicación de inspección en modo de aplicación normal de forma predeterminada y también en **vista** o **notificación** modos mediante [parámetros de ejecución personalizado](~/ios/watchos/get-started/installation.md#custommodes) en Visual Studio para Mac y Visual Studio.


También puede utilizar la línea de comandos para controlar el simulador de iOS. Es la herramienta de línea de comandos utilizada para iniciar aplicaciones de inspección **mtouch**.

Este es un ejemplo completo (que se ejecuta como una sola línea en el terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

El parámetro que se debe actualizar para reflejar la aplicación es `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

La ruta de acceso completa a la agrupación de la aplicación principal *de la aplicación de iOS que contiene la aplicación de inspección y la extensión*.

> [!NOTE]
> *Nota:* es la ruta de acceso se deben proporcionar para la *archivo .app de aplicación de iPhone*, es decir, aquella que se implementarán en el simulador de iOS y que contiene la extensión de inspección y la aplicación de inspección.

Ejemplo:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modo de notificación

Para probar la aplicación [ **notificación** modo](~/ios/watchos/platform/notifications.md), establezca el `watchlaunchmode` parámetro `Notification` y proporcione una ruta de acceso a un archivo JSON que contenga una carga de notificación de prueba.

El parámetro de carga es *necesario* para el modo de notificación.

Por ejemplo, agregue estos argumentos para el comando mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Otros argumentos

Los argumentos restantes se explican a continuación:

### <a name="--sdkroot"></a>--sdkroot

Requerido. Especifica la ruta de acceso a Xcode (6.2 o posterior).

Ejemplo:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--dispositivo

El dispositivo simulador para ejecutar. Esto se puede especificar de dos maneras: mediante la udid de un dispositivo específico, o mediante una combinación de tipo en tiempo de ejecución y el dispositivo.

Los valores exactos varía entre máquinas y se pueden consultar mediante de Apple **simctl** herramienta:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Ejemplo:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Tipo de tiempo de ejecución y dispositivo**

Ejemplo:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
