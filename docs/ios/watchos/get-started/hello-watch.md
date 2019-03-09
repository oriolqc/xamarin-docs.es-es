---
title: 'Hola, watchOS: tutorial'
description: Este documento proporciona un tutorial de creación de una aplicación simple de watchOS con Xamarin. Describe cómo trabajar en Visual Studio y Visual Studio para Mac, trabajar con guiones gráficos y responder a eventos en el código.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: de04a2d7f42ec36c464c75ced73bf8f8029ec1da
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672617"
---
# <a name="hello-watchos--walkthrough"></a>Hola, watchOS: tutorial

Después de haber creado una solución de los pasos descritos en [configuración e instalación](~/ios/watchos/get-started/installation.md), tendrá 3 proyectos:

- La aplicación principal de iOS que se usa para el programa de instalación o de otras tareas administrativas en el dispositivo. (Con otros tipos de extensiones de iOS, esto es a menudo denominado para que la aplicación de "Contenedor".) Con las aplicaciones de Watch, será posible para los usuarios iniciar la ejecución de la aplicación Watch sin **nunca** ejecutando la aplicación principal;
- La extensión de inspección que contiene el código del programa para la aplicación del reloj; y
- La aplicación del reloj, que contiene los recursos de guión gráfico y la imagen que se representan en el reloj.

Compruebe que su [referencias sean correctas](~/ios/watchos/get-started/project-references.md): que la aplicación primaria tiene una referencia a la extensión, y que la extensión tiene una referencia a la aplicación del reloj.

Confirme que los identificadores de lote siguen el \*.watchkitextension \*.watchkitapp convención y lo que tiene el archivo Info.plist de extensión tienen **Id. del lote de WKApp** valor establecido en el identificador de paquete de la aplicación del reloj.

Debe ser capaz de ejecutar la aplicación del reloj ahora, pero dado que el archivo de guión gráfico dentro de la aplicación del reloj está en blanco, no podrá saber.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "El Explorador de soluciones")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "El Explorador de soluciones")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
Haga doble clic en el Interface.storyboard de la aplicación del reloj para iniciar el Diseñador de iOS de Xamarin (si se encuentra en un equipo Mac también puede haga y **abrir con > Interface Builder de Xcode**)


1.  Asegúrese del **cuadro de herramientas** y **propiedades** paneles están visibles,
1.  Haga clic para seleccionar el controlador de interfaz
1.  Establecer el identificador y el título del controlador de interfaz al que **interfaceController** y **inspección Hi**,
1.  Compruebe el **clase** está establecido en **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Establece el identificador y el título del controlador de interfaz en interfaceController e inspección de Hi")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Haga doble clic en el Interface.storyboard de la aplicación del reloj para editar con Xamarin iOS Designer en Visual Studio:

1.  Abra el panel de propiedades.
1.  Cambie la clase a **InterfaceController**;
1.  Haga clic en el controlador de interfaz y
1.  Establecer el identificador y el título del controlador de interfaz al que **interfaceController** y **Hi inspección**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Establece el identificador y el título del controlador de interfaz en interfaceController e inspección de Hi")

-----


Crear la interfaz de usuario:

1. Desde el **cuadro de herramientas** pad,
1. Arrastre y coloque un **botón** y un **etiqueta** a la escena, y
1. Establecer el texto y los atributos de los controles, como se muestra:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "Establecer el texto y los atributos de los controles, como se muestra")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "Establecer el texto y los atributos de los controles, como se muestra")

-----

1. Establecer el **nombre** en el **propiedades** pad para cada control. En este ejemplo hemos usado `myButton` y `myLabel`.

1. Seleccione el botón en el guión gráfico y vaya a la **propiedades** del panel **eventos** lista, a continuación,

1. Cree un nuevo **acción** escribiendo `OnButtonPress` y presionando **ENTRAR**.
  La acción aparecerá en la lista y, automáticamente se creará un método parcial en C#.

![](hello-watch-images/buttonaction.png "La acción OnButtonPress se agrega a un botón")

Después de guardar el guión gráfico, el **InterfaceController.designer.cs** se actualiza con los nombres de los controles y acciones... Si abre este archivo después de haberse actualizado, puede ver cómo el `RegisterAttribute` se corresponde con el controlador y cómo se corresponden con los controles de interfaz de usuario para C# variables de instancia marcan con el `OutletAttribute` y cómo las acciones se asignan a los métodos parciales etiquetados con el `ActionAttribute`:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

Ahora abra **InterfaceController.cs** (*no* InterfaceController.designer.cs) y agregue el código siguiente:

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Este código debe ser bastante transparente: la variable de instancia `clickCount` es incrementa cada vez que la función `OnButtonPress` se llama. El texto de `myLabel` se cambia para reflejar este recuento; `myLabel`, por supuesto, es el nombre de una de las salidas que creó en XCode. El `partial` función es la implementación de la función asociada con el nombre de la acción especificada.

Si ya no es el proyecto de inicio

1. Haga doble clic en el proyecto de extensión de inspección y elija **establecer como proyecto de inicio**,

1. Establecer el destino de implementación en una imagen del simulador compatible con el Kit inspección (como iPhone 6 iOS 8.2)

1. Presione el **depurar** botón para desencadenar un inicio de la compilación y el simulador.

    [![](hello-watch-images/readytodebug-sml.png "Los elementos de interfaz de Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

Cuando se inicia el simulador, presione el botón para incrementar la etiqueta.
Enhorabuena, ha obtenido por sí mismo una aplicación de inspección.

![](hello-watch-images/running.png "La aplicación se ejecuta en el simulador")


## <a name="related-links"></a>Vínculos relacionados

- [Introducción (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Configuración e instalación](~/ios/watchos/get-started/installation.md)
- [Primer vídeo de la aplicación del reloj](https://blog.xamarin.com/your-first-watch-kit-app/)
