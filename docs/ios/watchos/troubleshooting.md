---
title: watchOS solución de problemas
description: Este documento describen problemas conocidos y soluciones alternativas para el desarrollo de watchOS con Xamarin. Describe las imágenes con problemas, agregar manualmente archivos de controlador de interfaz, iniciar una aplicación de inspección de la línea de comandos y mucho más.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 70ef341c066c77e214761d75c173faef00266e4c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892790"
---
# <a name="watchos-troubleshooting"></a>watchOS solución de problemas

Esta página contiene información adicional y soluciones alternativas para problemas que surjan.

- [Problemas conocidos](#knownissues)

- [Quitar el canal alfa de imágenes de icono](#noalpha)

- [Cómo agregar manualmente archivos de controlador de interfaz](#add) de Interface Builder de Xcode.

- [Iniciando la WatchApp desde la línea de comandos](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemas conocidos

### <a name="general"></a>General

<a name="deploy" />

- Las versiones anteriores de Visual Studio para Mac muestran incorrectamente una de las **AppleCompanionSettings** iconos como 88 x 88 píxeles; lo que resulta en un **falta el icono de Error** si intenta enviar a la aplicación de Store.
    Este icono debe ser 87 x 87 píxeles (29 unidades para **@3x** pantallas de Retina). No se puede solucionar este problema en Visual Studio para Mac: modificar el recurso de imagen en Xcode o editar manualmente el **Contents.json** archivo (para que coincida con [este ejemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Si el proyecto de extensión de inspección **Info.plist > Id. de lote de WKApp** no es [establecer correctamente](~/ios/watchos/get-started/project-references.md) para que coincida con la aplicación Watch **Id. de agrupación**, el depurador no podrá conectarse y Visual Esperará Studio para Mac con el mensaje *"En espera para que conectar el depurador"*.

- Se admite la depuración en **notificaciones** modo pero puede ser poco confiable. Volver a intentar a veces funcionará. Confirme que la aplicación Watch **Info.plist** `WKCompanionAppBundleIdentifier` se establece para que coincida con el identificador de paquete de la aplicación de iOS/contenedor primario (es decir. lo que se ejecuta en el iPhone).

- Diseñador de iOS no muestra flechas de punto de entrada para los controladores de interfaz de vista o una notificación.

- No se puede agregar dos `WKNotificationControllers` a un guión gráfico.
    Solución: El `notificationCategory` siempre se inserta el elemento en el guión gráfico XML con el mismo `id`. Para solucionar este problema, puede agregar controladores de notificación de dos (o más), abra el archivo de guión gráfico en un editor de texto y, a continuación, cambie manualmente el `id` elemento para que sea único.

    [![](troubleshooting-images/duplicate-id-sml.png "Abriendo el guión gráfico de archivos en un editor de texto y cambie manualmente el elemento id para que sean únicos")](troubleshooting-images/duplicate-id.png#lightbox)

- Puede ver un error "no se ha compilado la aplicación" al intentar iniciar la aplicación. Se produce tras una **Clean** cuando el proyecto de inicio se establece en el proyecto de extensión de inspección.
    La solución consiste en seleccionar **compilar > recompilar todo** y, a continuación, volver a iniciar la aplicación.

### <a name="visual-studio"></a>Programa para la mejora

Compatibilidad con iOS Designer inspección Kit *requiere* la solución para configurarse correctamente. Si no se establecen las referencias del proyecto (vea [cómo establecer referencias](~/ios/watchos/get-started/project-references.md)), a continuación, la superficie de diseño no funcionará correctamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Quitar el canal alfa de imágenes de icono

Los iconos no deben contener un canal alfa (el canal alfa define áreas transparentes de una imagen), en caso contrario, se rechazará la aplicación durante el envío de App Store con un error similar al siguiente:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Es fácil quitar el canal alfa en Mac OS X utilizando el **Preview** app:

1. Abra la imagen del icono en **Preview** y, a continuación, elija **archivo > Exportar**.

2. El cuadro de diálogo que aparece se incluirá un **alfa** casilla de verificación si existe un canal alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "El cuadro de diálogo que aparece incluye una casilla de verificación alfa si existe un canal alfa")

3. *Deje* el **alfa** checkbox y **guardar** el archivo a la ubicación correcta.

4. La imagen del icono ahora debe pasar comprobaciones de validación de Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Cómo agregar manualmente archivos de controlador de interfaz

> [!IMPORTANT]
> Soporte técnico de WatchKit de Xamarin incluye el diseño de los guiones gráficos de inspección en el Diseñador de iOS (en Visual Studio para Mac y Visual Studio), que no requieren que los pasos descritos a continuación. Simplemente proporcionar un controlador de interfaz de un nombre de clase en Visual Studio para Mac propiedades de relleno y el C# se creará automáticamente los archivos de código.


*Si* utilizas Interface Builder de Xcode, siga estos pasos para crear nuevos controladores de interfaz para la aplicación del reloj y habilitar la sincronización con Xcode para que estén disponibles en las salidas y acciones C#:

1. Abra la aplicación watch **Interface.storyboard** en **Interface Builder de Xcode**.
    
    ![](troubleshooting-images/add-6.png "Al abrir el guion gráfico en Interface Builder de Xcode")

2. Arrastre una nueva `InterfaceController` en el guión gráfico:

    ![](troubleshooting-images/add-1.png "Un InterfaceController")

3. Ahora puede arrastrar controles a la controladora de interfaz (p ej. las etiquetas y botones) pero no se puede crear acciones o salidas todavía, porque no hay ningún **.h** archivo de encabezado. Los siguientes pasos hará que el necesario **.h** que se cree el archivo de encabezado.

    ![](troubleshooting-images/add-2.png "Un botón en el diseño")

4. Cierre el guión gráfico y vuelva a Visual Studio para Mac. Cree un nuevo C# archivo **MyInterfaceController.cs** (o cualquier nombre que desee utilizar) en el **ver la extensión de la aplicación** proyecto (no la aplicación del reloj propio donde es el guión gráfico). Agregue el código siguiente (actualizando el espacio de nombres, nombre de clase y el nombre de constructor):

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

5. Crear otra nueva C# archivo **MyInterfaceController.designer.cs** en el **ver la extensión de la aplicación** del proyecto y agregue el código siguiente. Asegúrese de actualizar el espacio de nombres, el nombre de clase y el `Register` atributo:

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
    
    Sugerencia: Se puede (opcionalmente) convertir este archivo en un nodo secundario del primer archivo arrastrarlo a otro C# archivo en Visual Studio para Mac panel de solución. A continuación, aparecerá como esta:
    
    ![](troubleshooting-images/add-5.png "El panel de solución")

6. Seleccione **compilar > compilar todo** para que reconozca la nueva clase de sincronización de Xcode (a través de la `Register` atributo) que se ha usado.

7. Vuelva a abrir el guión gráfico con el botón secundario en el archivo de guión gráfico de la aplicación de inspección y seleccionando **abrir con > Interface Builder de Xcode**:

    ![](troubleshooting-images/add-6.png "Abrir el guión gráfico en el generador de interfaz")

8. Seleccione el nuevo controlador de interfaz y asígnele el nombre de clase definida anteriormente, por ejemplo. `MyInterfaceController`.
Si ha funcionado correctamente, debe aparecer automáticamente en el **clase:** lista desplegable y puede seleccionarlo desde allí.

    ![](troubleshooting-images/add-4.png "Configuración de una clase personalizada")

9. Elija la **Editor del asistente** ver en Xcode (el icono con dos círculos superpuestos) para que puedan ver el guión gráfico y el código paralelo:

    ![](troubleshooting-images/add-7.png "El elemento de barra de herramientas del Editor del Asistente")

    Cuando el foco está en el panel de código, asegúrese de que se observa el **.h** archivo de encabezado y si no, haga clic en la barra de ruta de navegación y seleccione el archivo correcto (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Seleccione MyInterfaceController")

10. Ahora puede crear salidas y acciones por **Ctrl + arrastrar** desde el guión gráfico en el **.h** archivo de encabezado.

    ![](troubleshooting-images/add-9.png "Creación de salidas y acciones")

    Al soltar la operación de arrastrar, se le pedirá que seleccione si desea crear un enchufe o una acción y elija su nombre:

    ![](troubleshooting-images/add-a.png "La toma de corriente y un cuadro de diálogo acción")

11. Una vez que se guardan los cambios de guión gráfico y Xcode está cerrado, vuelva a Visual Studio para Mac. Detectará los cambios del archivo de encabezado y agregar automáticamente el código para el **. designer.cs** archivo:


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


Ahora puede hacer referencia al control (o implementar la acción) en C#!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Iniciar la aplicación del reloj desde la línea de comandos

> [!IMPORTANT]
> Puede iniciar la aplicación del reloj en modo de aplicación normal de forma predeterminada y también en **vista** o **notificación** modos mediante [parámetros de ejecución personalizado](~/ios/watchos/get-started/installation.md#custommodes) en Visual Studio para Mac y Visual Studio.


También puede usar la línea de comandos para controlar el simulador de iOS. Es la herramienta de línea de comandos usada para iniciar aplicaciones de watch **mtouch**.

Este es un ejemplo completo (que se ejecutan como una sola línea en el terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

El parámetro deberá actualizarse para reflejar la aplicación es `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

La ruta de acceso completa del paquete de aplicaciones principal *para la aplicación de iOS que contiene la aplicación de inspección y extensión*.

> [!NOTE]
> Es la ruta de acceso, deberá suministrar la *archivo .app de aplicación de iPhone*, es decir, aquella que se va a implementar el simulador de iOS y que contiene la extensión de inspección y la aplicación del reloj.

Ejemplo:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modo de notificación

Para probar la aplicación [ **notificación** modo](~/ios/watchos/platform/notifications.md), establezca el `watchlaunchmode` parámetro `Notification` y proporcione una ruta de acceso a un archivo JSON que contenga una carga de notificación de prueba.

El parámetro de carga es *requiere* para el modo de notificación.

Por ejemplo, agregue estos argumentos para el comando mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Otros argumentos

Los argumentos restantes se explican a continuación:

### <a name="--sdkroot"></a>--sdkroot

Obligatorio. Especifica la ruta de acceso de Xcode (6.2 o posterior).

Ejemplo:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--device

El dispositivo simulador para ejecutar. Esto se puede especificar de dos maneras: mediante el udid de un dispositivo específico, o mediante una combinación de tipo en tiempo de ejecución y el dispositivo.

Los valores exactos varía entre máquinas y se pueden consultar mediante Apple **simctl** herramienta:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Ejemplo:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Tipo en tiempo de ejecución y el dispositivo**

Ejemplo:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
