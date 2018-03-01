---
title: "Inicio rápido de guiones gráficos"
description: "Compilación iniciada macOS la obtención de interfaces de usuario con guiones gráficos."
ms.topic: article
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 559179d2618ea41bf50362f2e5eb2aa735464b33
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="starting-a-new-storyboard-based-project"></a>A partir de un guión gráfico nuevo proyecto basado en

Como una introducción rápida al uso de guiones gráficos para definir la interfaz de usuario de la aplicación de Xamarin.Mac, vamos a iniciar un nuevo proyecto de Xamarin.Mac. Seleccione **Mac** > **Aplicación** > **Cocoa App** y haga clic en el botón **Siguiente**:

[ ![](quickstart-images/qs01.png "Agregar una nueva aplicación de cacao")](quickstart-images/qs01.png)

Use la **nombre de la aplicación** de `MacStoryboard` y haga clic en el **siguiente** botón:

[ ![](quickstart-images/qs02.png "Establecer el nombre de aplicación")](quickstart-images/qs02.png)

Use el valor predeterminado **nombre del proyecto** y **nombre de la solución** y haga clic en el **crear** botón:

[ ![](quickstart-images/qs03.png "Los nombres de proyecto y solución")](quickstart-images/qs03.png)

En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz de Xcode:

[ ![](quickstart-images/qs04.png "Editar el guión gráfico en Xcode.")](quickstart-images/qs04.png)

Como puede ver anteriormente, el valor predeterminado de guión gráfico define barra de menús de la aplicación y su ventana principal con él View Controller y vista. Para nuestra aplicación de ejemplo, vamos a crear una interfaz de usuario que tiene un principal _vista de contenido_ en el lado "uno" y un _Inspector vista_ en el segundo.

Para ello, tendrá que quitar el controlador de vista predeterminada y vista que viene con el guión gráfico mediante selecciónelo en el generador de interfaz y presione la **eliminar** clave:

[ ![](quickstart-images/qs05.png "Eliminación del controlador de vista predeterminada")](quickstart-images/qs05.png)

A continuación, escriba `split` en el **filtro** área, seleccione el controlador de vista de división Vertical y arrástrelo hasta el _superficie de diseño_:

[ ![](quickstart-images/qs06.png "Buscar el controlador de vista de división")](quickstart-images/qs06.png)

Tenga en cuenta que el controlador incluye automáticamente secundarios dos controladores de la vista (y sus vistas relacionadas), con cable telefónico a los lados izquierdo y derecho de la vista en dos paneles. Para unir la vista en dos paneles a su ventana primaria, presione el **Control** clave, haga clic en el controlador de ventana (el círculo azul en el marco del controlador de la ventana) y arrastre una línea para el controlador de vista de división. Seleccione **contenido de la ventana** desde la ventana emergente:

[ ![](quickstart-images/qs07.png "Configuración de las ventanas Vista de contenido")](quickstart-images/qs07.png)

Esto, asociará el elemento de la dos interfaz juntos mediante una Segue:

[ ![](quickstart-images/qs08.png "El Segue entre la ventana y el contenido")](quickstart-images/qs08.png)

Desea colocar una vista de texto en el lado izquierdo de la vista de división y tiene rellenar automáticamente el área disponible cuando se cambia de tamaño la ventana o la vista en dos paneles. Arrastre una vista de texto a la parte superior View Controller asociado a la vista de división y haga clic en el **Pin** automáticamente la restricción de diseño (el segundo icono desde la derecha en la parte inferior de la superficie de diseño).

[ ![](quickstart-images/qs09.png "Configurar las restricciones")](quickstart-images/qs09.png)

Desde aquí se hará clic en las cuatro el **i** iconos por delimitador en el cuadro situado al principio de la Popover restricciones y haga clic en el **agregar restricciones de 4** situado en la parte inferior para agregar las restricciones necesarias.

Si se vuelva a Visual Studio para Mac y ejecute el proyecto, tenga en cuenta que la vista de texto cambia automáticamente de tamaño para rellenar el lado izquierdo de la vista de división que la ventana o se cambia el tamaño de la división:

[ ![](quickstart-images/qs10.png "Un ejemplo de la aplicación en ejecución")](quickstart-images/qs10.png)

Puesto que vamos a usar el tipo del lado derecho de la vista en dos paneles como un área de Inspector, queremos que tienen un tamaño más pequeño y permitir que se puede contraer. Volver a Xcode y editar la vista de la derecha, seleccione en la superficie de diseño y haga clic en el **Inspector de tamaño**. Desde aquí, escriba un **ancho** de `250`:

[ ![](quickstart-images/qs11.png "Establecer el ancho")](quickstart-images/qs11.png)

Siguiente seleccione el elemento de división que representa el lado derecho, establecer un valor mayor **manteniendo prioridad** y haga clic en el **usuario puede contraer** casilla de verificación:

[ ![](quickstart-images/qs12.png "Edición de la prioridad de explotación")](quickstart-images/qs12.png)

Si se vuelva a Visual Studio para Mac y ejecutar el proyecto ahora, observe que mantiene el lado derecho es menor tamaño y la ventana se cambia el tamaño:

[ ![](quickstart-images/qs13.png "Un ejemplo de la aplicación en ejecución")](quickstart-images/qs13.png)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definir una presentación desplazará tranquilamente

Vamos a diseño el lado derecho de la vista de división para que actúe como un Inspector de propiedades del texto seleccionado. Se arrastrará algunos controles de la vista inferior al diseño de la interfaz de usuario del inspector. Para el control de la última, desea mostrar un popover que permite al usuario seleccionar de los cuatro estilos de carácter preestablecido.

Vamos a agregar un botón con el Inspector y un controlador de vista a la superficie de diseño. Se cambiará el controlador de vista para que sea el tamaño que deseamos que nuestra Popover y agregarle cuatro botones. A continuación te enviaremos un mensaje **Control** clave, haga clic en el botón en la vista de Inspector y arrastre hasta el controlador de vista que representará nuestra popover:

[ ![](quickstart-images/qs14.png "Arrastre para crear un nuevo segue")](quickstart-images/qs14.png)

En el menú emergente, se seleccionará **Popover**: 

[ ![](quickstart-images/qs15.png "Seleccionar el tipo de segue")](quickstart-images/qs15.png)

Por último, se podrá seleccionar el Segue en la superficie de diseño y establecer el **preferido borde** a **izquierda**. A continuación, se arrastra una línea desde el **delimitador vista** al botón que queremos que el popover estar conectado al:

[ ![](quickstart-images/qs16.png "Arrastre para crear un nuevo segue")](quickstart-images/qs16.png)

Si se vuelve a Visual Studio para Mac, ejecute la aplicación y haga clic en el **ninguno** botón en el Inspector, el popover se mostrará:

[ ![](quickstart-images/qs17.png "Un ejemplo de la ejecución de segue")](quickstart-images/qs17.png)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Creación de las preferencias de aplicación

Estándar más aplicaciones macOS proporcionan un _cuadro de diálogo de preferencias_ que permite al usuario definir varias opciones que controlan diversos aspectos de la aplicación, como las cuentas de usuario o apariencia.

Para definir una ventana de cuadro de diálogo de preferencias estándar, arrastre primero un controlador de vista de la pestaña a la superficie de diseño:

[ ![](quickstart-images/qs18.png "Editar el guión gráfico en Xcode.")](quickstart-images/qs18.png)

Una vez más, esto automáticamente vienen con dos secundarios conectados de controladores de la vista. Por ejemplo las cosas, agregaremos una etiqueta a cada vista que se centrar en su interior:

[ ![](quickstart-images/qs19.png "Establecer las restricciones")](quickstart-images/qs19.png)

A continuación, en el que deseamos mostrar la ventana de preferencias cuando el usuario selecciona el **preferencias...**  elemento de menú. En la barra de menús, seleccione el elemento de menú Preferencias **Control** clave y haga clic y arrastre una línea a nuestro controlador de vista de ficha:

[ ![](quickstart-images/qs20.png "Arrastre para crear un segue")](quickstart-images/qs20.png)

Desde la ventana emergente, se seleccionará **Modal** a mostrar esta ventana como un cuadro de diálogo Modal:

[ ![](quickstart-images/qs21.png "Seleccionar el tipo de segue")](quickstart-images/qs21.png)

Si se guarda los cambios, vuelva a Visual Studio para Mac, ejecute la aplicación y seleccione el **preferencias...**  elemento de menú, nuestras nuevas preferencias se mostrará el cuadro de diálogo:

[ ![](quickstart-images/qs22.png "Un ejemplo de la ejecución de segue")](quickstart-images/qs22.png)

Puede observar que esta no parece una ventana de cuadro de diálogo de preferencias de aplicación de macOS estándar. Para solucionar este problema, incluya dos archivos de imagen en la aplicación Xamarin.Mac `Resources` carpeta en el **el Explorador de soluciones** y volver al generador de interfaz de Xcode.

Seleccione la ficha View Controller y cambia su **estilo** a **barra de herramientas**: 

[ ![](quickstart-images/qs23.png "Establecer el estilo de barra de pestaña")](quickstart-images/qs23.png)

Seleccione cada pestaña y asígnele un **etiqueta** y seleccione una de las imágenes para representarlo:

[ ![](quickstart-images/qs24.png "Configuración de cada ficha en Xcode")](quickstart-images/qs24.png)

Si se guarda los cambios, vuelva a Visual Studio para Mac, ejecute la aplicación y seleccione el **preferencias...**  elemento de menú, el cuadro de diálogo se mostrarán como una aplicación de macOS estándar:

[ ![](quickstart-images/qs25.png "Un ejemplo de la ventana de preferencias de ejecución")](quickstart-images/qs25.png)

Para obtener más información, vea nuestra [trabajar con imágenes](~/mac/app-fundamentals/image.md), [menús](~/mac/user-interface/menu.md), [Windows](~/mac/user-interface/window.md) y [cuadros de diálogo](~/mac/user-interface/dialog.md) documentación.

## <a name="related-links"></a>Vínculos relacionados

- [MacStoryboard (ejemplo)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con ventanas](~/mac/user-interface/window.md)
- [Directrices de interfaz de sistema operativo X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
