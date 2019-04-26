---
title: 'Guiones gráficos en Xamarin.Mac: Guía de inicio rápido'
description: Este documento proporciona una introducción rápida a la creación de interfaces de usuario con guiones gráficos en Xamarin.Mac de macOS. Describe cómo crear un segue y crear una ventana de preferencias.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7f7d23a01a3c3c6567d6bab45d0abbfb078fb512
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033686"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Guiones gráficos en Xamarin.Mac: Guía de inicio rápido

Como una introducción rápida al uso de guiones gráficos para definir la interfaz de usuario de una aplicación de Xamarin.Mac, vamos a iniciar un nuevo proyecto de Xamarin.Mac. Seleccione **Mac** > **Aplicación** > **Cocoa App** y haga clic en el botón **Siguiente**:

[![](quickstart-images/qs01.png "Agregar una nueva aplicación de Cocoa")](quickstart-images/qs01.png#lightbox)

Use la **nombre de la aplicación** de `MacStoryboard` y haga clic en el **siguiente** botón:

[![](quickstart-images/qs02.png "Establecer el nombre de la aplicación")](quickstart-images/qs02.png#lightbox)

Use el valor predeterminado **nombre del proyecto** y **nombre de la solución** y haga clic en el **crear** botón:

[![](quickstart-images/qs03.png "Los nombres de proyecto y solución")](quickstart-images/qs03.png#lightbox)

En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder de Xcode:

[![](quickstart-images/qs04.png "Editar el guión gráfico en Xcode")](quickstart-images/qs04.png#lightbox)

Como puede ver anteriormente, el valor predeterminado de guión gráfico define barra de menús de la aplicación y su ventana principal con la el controlador de vista y vista. Para nuestra aplicación de ejemplo, vamos a crear una interfaz de usuario que tiene un principal _vista contenido_ en un lado y un _Inspector vista_ en el segundo.

Para ello, debemos quitar primero el valor predeterminado de controlador de vista y vista que se incluye con el guión gráfico, selecciónelo en Interface Builder y presionar el **eliminar** clave:

[![](quickstart-images/qs05.png "Quitar el controlador de vista predeterminado")](quickstart-images/qs05.png#lightbox)

A continuación, escriba `split` en el **filtro** área, seleccione el controlador de vista dividida Vertical y arrástrelo hasta el _superficie de diseño_:

[![](quickstart-images/qs06.png "Buscando el controlador de vista dividida")](quickstart-images/qs06.png#lightbox)

Observe que el controlador incluye automáticamente secundario dos controladores de vista (y sus vistas relacionadas), con cable de seguridad en los lados izquierdo y derecho de la vista en dos paneles. Para adaptar la vista en dos paneles a su ventana primaria, presione el **Control** clave, haga clic en el controlador de ventana (el círculo azul en el marco del controlador de ventana) y arrastre una línea para el controlador de vista de división. Seleccione **contenido de la ventana** desde el menú emergente:

[![](quickstart-images/qs07.png "Configuración de las ventanas Vista de contenido")](quickstart-images/qs07.png#lightbox)

Esto, asociará el elemento de dos interfaz entre sí mediante un Segue:

[![](quickstart-images/qs08.png "El objeto Segue entre la ventana y el contenido")](quickstart-images/qs08.png#lightbox)

Deseamos colocar una vista de texto en el lado izquierdo de la vista en dos paneles y tiene rellenar automáticamente el área disponible cuando se cambia el tamaño de la ventana o la vista en dos paneles. Arrastre una vista de texto a la parte superior que el controlador de vista asociado a la vista de división y haga clic en el **Pin** auto restricción de diseño (el segundo icono desde la derecha en la parte inferior de la superficie de diseño).

[![](quickstart-images/qs09.png "Configurar las restricciones")](quickstart-images/qs09.png#lightbox)

Desde aquí, hará clic en las cuatro el **en forma de i** iconos por el rectángulo de selección en la parte superior del elemento flotante restricciones y haga clic en el **agregar 4 restricciones** situado en la parte inferior para agregar las restricciones necesarias.

Si se vuelva a Visual Studio para Mac y ejecutar el proyecto, tenga en cuenta que la vista de texto cambia automáticamente de tamaño para rellenar el lado izquierdo de la vista en dos paneles como la ventana o se cambia el tamaño de la división:

[![](quickstart-images/qs10.png "Un ejemplo de la aplicación en ejecución")](quickstart-images/qs10.png#lightbox)

Puesto que vamos a usar el lado derecho de la vista en dos paneles como un área de Inspector, deseamos que tienen un tamaño más pequeño y permita que se contrae. Vuelva a Xcode y editar la vista de la derecha, selecciónela en la superficie de diseño y al hacer clic en el **Inspector de tamaño**. Desde aquí, escriba un **ancho** de `250`:

[![](quickstart-images/qs11.png "Establecer el ancho")](quickstart-images/qs11.png#lightbox)

Siguiente seleccione el elemento de división que representa el lado derecho, puede establecer un valor mayor **manteniendo prioridad** y haga clic en el **usuario puede contraer** casilla de verificación:

[![](quickstart-images/qs12.png "Editar la prioridad de explotación")](quickstart-images/qs12.png#lightbox)

Si volvemos a Visual Studio para Mac y ejecutar el proyecto ahora, tenga en cuenta que mantiene el lado derecho es menor tamaño y la ventana se cambia el tamaño:

[![](quickstart-images/qs13.png "Un ejemplo de la aplicación en ejecución")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definir una presentación de Segue

Vamos al diseño del lado derecho de la vista dividida para que actúe como un Inspector de propiedades del texto seleccionado. Se arrastrará algunos controles de la vista de la parte inferior a la interfaz de usuario del inspector de diseño. Para el último control, desea mostrar un elemento flotante que permite al usuario seleccionar de los cuatro estilos de carácter preestablecido.

Vamos a agregar un botón a un controlador de vista a la superficie de diseño y el Inspector. Se cambiará el controlador de vista para que sea el tamaño que deseamos que nuestro elemento flotante y agregarle cuatro botones. A continuación vamos a **Control** clave y haga clic en el botón en la vista de Inspector y arrastre hasta el controlador de vista que representa el elemento flotante:

[![](quickstart-images/qs14.png "Arrastrar para crear un nuevo segue")](quickstart-images/qs14.png#lightbox)

En el menú emergente, seleccionaremos **elemento flotante**: 

[![](quickstart-images/qs15.png "Seleccionar el tipo de segue")](quickstart-images/qs15.png#lightbox)

Por último, seleccionaremos el Segue en la superficie de diseño y establecer el **Edge preferido** a **izquierda**. A continuación, se arrastra una línea desde el **delimitador vista** al botón que queremos que el elemento flotante se adjunte a:

[![](quickstart-images/qs16.png "Arrastrar para crear un nuevo segue")](quickstart-images/qs16.png#lightbox)

Si volvemos a Visual Studio para Mac, ejecute la aplicación y haga clic en el **ninguno** se mostrará el botón en el Inspector, el elemento flotante:

[![](quickstart-images/qs17.png "Un ejemplo de la ejecución de segue")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Creación de las preferencias de aplicación

Estándar más aplicaciones macOS proporcionan un _cuadro de diálogo Preferencias_ que permite al usuario definir varias opciones que controlan diversos aspectos de la aplicación, como las cuentas de usuario o apariencia.

Para definir una ventana de cuadro de diálogo estándar de preferencia, arrastre primero un controlador de vista de la ficha hasta la superficie de diseño:

[![](quickstart-images/qs18.png "Editar el guión gráfico en Xcode")](quickstart-images/qs18.png#lightbox)

Nuevamente, esto automáticamente procederán con dos secundarios adjunta los controladores de vista. Por ejemplo sencillo, vamos a agregar una etiqueta a cada vista que se centrar dentro de él:

[![](quickstart-images/qs19.png "Establecer las restricciones")](quickstart-images/qs19.png#lightbox)

A continuación, en la que queremos mostrar la ventana Preferencias cuando el usuario selecciona el **preferencias...**  elemento de menú. En la barra de menús, seleccione el elemento de menú Preferencias **Control** clave y haga clic y arrastre una línea a nuestro controlador de vista de la pestaña:

[![](quickstart-images/qs20.png "Arrastrar para crear un segue")](quickstart-images/qs20.png#lightbox)

En la ventana emergente, seleccionaremos **Modal** para mostrar esta ventana como un cuadro de diálogo Modal:

[![](quickstart-images/qs21.png "Seleccionar el tipo de segue")](quickstart-images/qs21.png#lightbox)

Si se guarden los cambios, vuelva a Visual Studio para Mac, ejecute la aplicación y seleccione el **preferencias...**  elemento de menú, nuestras nuevas preferencias se mostrará el cuadro de diálogo:

[![](quickstart-images/qs22.png "Un ejemplo de la ejecución de segue")](quickstart-images/qs22.png#lightbox)

Es posible que tenga en cuenta que esto no parece una aplicación estándar macOS ventana de cuadro de diálogo de preferencias. Para solucionar este problema, incluya dos archivos de imagen en la aplicación de Xamarin.Mac `Resources` carpeta en el **el Explorador de soluciones** y volver a Interface Builder de Xcode.

Seleccione el controlador de vista de pestaña y cambiar su **estilo** a **barra de herramientas**: 

[![](quickstart-images/qs23.png "Establecer el estilo de barra de pestaña")](quickstart-images/qs23.png#lightbox)

Seleccione cada pestaña y asígnele un **etiqueta** y seleccione una de las imágenes para representarlo:

[![](quickstart-images/qs24.png "Configuración de cada pestaña en Xcode")](quickstart-images/qs24.png#lightbox)

Si se guarden los cambios, vuelva a Visual Studio para Mac, ejecute la aplicación y seleccione el **preferencias...**  elemento de menú, el cuadro de diálogo se mostrará como una aplicación estándar de macOS:

[![](quickstart-images/qs25.png "Un ejemplo de la ventana de preferencias de ejecución")](quickstart-images/qs25.png#lightbox)

Para obtener más información, consulte nuestra [trabajar con imágenes](~/mac/app-fundamentals/image.md), [menús](~/mac/user-interface/menu.md), [Windows](~/mac/user-interface/window.md) y [cuadros de diálogo](~/mac/user-interface/dialog.md) documentación.

## <a name="related-links"></a>Vínculos relacionados

- [MacStoryboard (ejemplo)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
