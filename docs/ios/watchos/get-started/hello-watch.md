---
title: "Hola, inspección"
description: "Introducción a Xamarin y watchOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/14/2016
ms.openlocfilehash: 88f9a86173756738d44f099b13177489226fa0e1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="hello-watch"></a>Hola, inspección

Después de haber creado una solución siguiendo los pasos descritos en [configuración e instalación](~/ios/watchos/get-started/installation.md), tendrá 3 proyectos:

- La aplicación primaria de iOS que se utiliza para el programa de instalación u otras tareas administrativas en el dispositivo. (Con otros tipos de extensiones de iOS, esto es a menudo denominado para que la aplicación de "Contenedor".) Con aplicaciones de inspección, será posible para los usuarios iniciar la ejecución de la aplicación de inspección sin **alguna vez** ejecuta la aplicación principal;
- La extensión de inspección que contiene el código del programa para la aplicación de inspección; y
- La aplicación de inspección, que contiene los recursos de guión gráfico y la imagen que se representan en el reloj.

Compruebe que la [las referencias son correctas](~/ios/watchos/get-started/project-references.md): que la aplicación principal tiene una referencia a la extensión, y que la extensión tiene una referencia a la aplicación de inspección.

Confirme que los identificadores de paquete siga la \*.watchkitextension \*.watchkitapp convención y que el archivo Info.plist de la extensión tiene tiene **Id. del lote WKApp** valor se establece en el identificador de paquete de la aplicación de la inspección.

Debe ser capaz de ejecutar la aplicación de inspección ahora, pero dado que el archivo del guión gráfico dentro de la aplicación de inspección está en blanco, no podrán saber.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](hello-watch-images/projectstructure.png "El Explorador de soluciones")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-projectstructure.png "El Explorador de soluciones")

-----

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
Haga doble clic en el Interface.storyboard en su aplicación de inspección para iniciar el Diseñador de iOS de Xamarin (si se encuentra en un equipo Mac también puede hacer clic y **abrir con > Generador de interfaz de Xcode**)


1.  Asegúrese del **cuadro de herramientas** y **propiedades** rellena está visible,
1.  Haga clic para seleccionar el controlador de interfaz
1.  Establecer el identificador y el título del controlador de interfaz para **interfaceController** y **inspección Hi**,
1.  Compruebe el **clase** está establecido en **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Establece el identificador y el título de la interfaz de controlador en interfaceController y Hi inspección")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Haga doble clic en el Interface.storyboard en su aplicación de inspección para editar con el Diseñador de Visual Studio de iOS de Xamarin:

1.  Abra el panel de propiedades;
1.  Cambie la clase para **InterfaceController**;
1.  Haga clic en el controlador de interfaz y
1.  Establecer el identificador y el título del controlador de interfaz para **interfaceController** y **Hi inspección**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Establece el identificador y el título de la interfaz de controlador en interfaceController y Hi inspección")

-----


Crear la interfaz de usuario:

1. Desde el **cuadro de herramientas** pad,
1. Arrastre y coloque una **botón** y un **etiqueta** en la pantalla, y
1. Establecer el texto y los atributos de los controles, como se muestra:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](hello-watch-images/draganddrop.png "Establecer el texto y los atributos de los controles, como se muestra")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-draganddrop.png "Establecer el texto y los atributos de los controles, como se muestra")

-----

1. Establecer el **nombre** en el **propiedades** panel para cada control. En este ejemplo hemos usado la `myButton` y `myLabel`.

1. Seleccione el botón en el guión gráfico y vaya a la **propiedades** del panel de **eventos** lista, a continuación,

1. Crear un nuevo **acción** escribiendo `OnButtonPress` y presionando **ENTRAR**.
  La acción aparecerá en la lista y, automáticamente se creará un método parcial en C#.

![](hello-watch-images/buttonaction.png "La acción OnButtonPress se agregan a un botón")

Después de guardar el guión gráfico, el **InterfaceController.designer.cs** se actualiza con los nombres de los controles y las acciones... Si abre este archivo una vez ha actualizado, puede ver cómo la `RegisterAttribute` se corresponde con el controlador y cómo se corresponden los controles de interfaz de usuario con C# variables de instancia marcadas con el `OutletAttribute` y cómo se asignan acciones a los métodos parciales etiquetados con el `ActionAttribute`:

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

Ahora, abra **InterfaceController.cs** (*no* InterfaceController.designer.cs) y agregue el código siguiente:

```csharp
int clickCount = 0;

partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}

```

Este código debe ser bastante transparente: la variable de instancia `clickCount` es incrementa cada vez que la función `OnButtonPress` se llama. El texto de `myLabel` se cambia para reflejar este recuento; `myLabel`, por supuesto, es el nombre de uno de los distribuidores que creó en XCode. El `partial` función es la implementación de la función asociada con el nombre de la acción que ha especificado.

Si ya no es el proyecto de inicio

1. Haga doble clic en el proyecto de extensión de inspección y elija **establecer como proyecto de inicio**,

1. Establecer el destino de implementación en una imagen del simulador compatible del Kit de inspección (por ejemplo, iOS de iPhone 6 8.2),

1. Presione el **depurar** botón para desencadenar un inicio de compilación y del simulador.

    [ ![](hello-watch-images/readytodebug-sml.png "Los elementos de la interfaz de Visual Studio")](hello-watch-images/readytodebug.png)

Cuando se inicia el simulador, presione el botón para incrementar la etiqueta.
Enhorabuena, usted mismo tienes una aplicación de la inspección.

![](hello-watch-images/running.png "La aplicación que se ejecuta en el simulador")


## <a name="related-links"></a>Vínculos relacionados

- [Introducción (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Configuración e instalación](~/ios/watchos/get-started/installation.md)
- [Primer vídeo de aplicación de inspección](http://blog.xamarin.com/your-first-watch-kit-app/)
