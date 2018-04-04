---
title: Uso de la MonoGame PipelineTool
description: La herramienta de canalización MonoGame se usa para crear y administrar proyectos de contenido de MonoGame. Los archivos en proyectos de contenido son procesados por la herramienta de canalización Monogame y arroja como archivos .xnb para su uso en aplicaciones CocosSharp y MonoGame.
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 672c57aded149200b32501a6b48165ca88726ee1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="using-the-monogame-pipeline-tool"></a>Con la herramienta de canalización MonoGame

_La herramienta de canalización MonoGame se usa para crear y administrar proyectos de contenido de MonoGame. Los archivos en proyectos de contenido son procesados por la herramienta de canalización Monogame y arroja como archivos .xnb para su uso en aplicaciones CocosSharp y MonoGame._

La herramienta de canalización MonoGame proporciona un entorno fácil de usar para convertir archivos de contenido en **.xnb** archivos para su uso en aplicaciones CocosSharp y MonoGame. Para obtener información sobre las canalizaciones de contenido y por qué son útiles para el desarrollo de juegos, consulte [esta introducción sobre las canalizaciones de contenido](~/graphics-games/cocossharp/content-pipeline/introduction.md)

Este tutorial trata lo siguiente:

 - Instalación de la herramienta de canalización MonoGame
 - Crear un proyecto CocosSharp
 - Crear un proyecto de contenido
 - Procesamiento de archivos en la herramienta de canalización MonoGame
 - Uso de archivos en tiempo de ejecución

Este tutorial usa un proyecto CocosSharp para demostrar cómo **.xnb** archivos se pueden cargar y usar en una aplicación. Los usuarios de MonoGame también podrán hacer referencia a este tutorial como CocosSharp y MonoGame utilizan el mismo **.xnb** archivos de contenido.

La aplicación finalizada mostrará un único sprite mostrando una textura de un **.xnb** archivo y una sola etiqueta mostrar una fuente sprite desde una **.xnb** archivo:

![](walkthrough-images/image1.png "La aplicación finalizada mostrará un único sprite mostrando una textura de un archivo de .xnb")


## <a name="monogame-pipeline-tool-discussion"></a>Descripción de la herramienta de canalización MonoGame

La herramienta de canalización MonoGame está disponible en Windows, OS X y Linux. En este tutorial se ejecutará la herramienta en Windows, pero solo puede ir seguido a lo largo de Mac y Linux también. Para obtener información sobre cómo obtener la herramienta configurada en Máx o Linux, consulte [esta página](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/).

Es capaz de crear contenido para aplicaciones de iOS incluso cuando se ejecutan en Windows, de modo que los desarrolladores mediante la herramienta de canalización MonoGame [Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md) podrán continuar desarrollando en Windows.


## <a name="installing-the-monogame-pipeline-tool"></a>Instalación de la herramienta de canalización MonoGame

Empezaremos instalando el MonoGame, que incluye la canalización de contenido de MonoGame. Tenga en cuenta que la canalización de contenido de MonoGame es una descarga independiente para Mac. Todos los instaladores de MonoGame pueden encontrarse en el [página de descargas de MonoGame](http://www.monogame.net/downloads/). Se descargará MonoGame para Visual Studio, pero una vez instalado el desarrollador puede usar MonoGame en Visual Studio para Mac demasiado:

![](walkthrough-images/image2.png "Descargar MonoGame para Visual Studio, pero una vez instalado el uso de desarrollador MonoGame en Visual Studio para Mac demasiado")

Una vez descargado, se podrá ejecutar el programa de instalación y acepte las opciones de los valores predeterminados. Una vez finalizado el instalador, la herramienta de canalización MonoGame está instalada y puede encontrarse en la búsqueda del menú Inicio:

![](walkthrough-images/image3.png "Una vez finalizado el instalador, la herramienta de canalización MonoGame está instalada y puede encontrarse en la búsqueda del menú Inicio")

Inicie la herramienta de canalización MonoGame:

![](walkthrough-images/image4.png "Inicie la herramienta de canalización MonoGame")

Una vez que se ejecuta la herramienta de canalización de MonoGame, comenzamos podemos realizar nuestros proyectos juegos y contenidos.


## <a name="creating-an-empty-cocossharp-project"></a>Crear un proyecto vacío de CocosSharp

El siguiente paso es crear un proyecto de CocosSharp. Es importante que se crea el proyecto CocosSharp en primer lugar por lo que podemos guardar el proyecto de contenido en la estructura de carpetas creada por el proyecto CocosSharp. Para entender la estructura de un proyecto CocosSharp, eche un vistazo a la [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), que va a utilizar en esta guía. Sin embargo, si tiene un proyecto existente de CocosSharp que le gustaría agregar contenido a, no dude en utilizar ese proyecto en lugar de BouncingGame.

Una vez creado el proyecto, se podrá ejecutarla para comprobar que compila y que tenemos todo configurado correctamente:

![](walkthrough-images/image5.png "Una vez creado el proyecto, ejecutar para comprobar que se compila y que todo lo que ha configurado correctamente")


## <a name="creating-a-content-project"></a>Crear un proyecto de contenido

Ahora que tenemos un proyecto de juego, podemos crear un proyecto de canalización MonoGame. Para ello, en la instrucción select de la herramienta de canalización MonoGame **archivo > nuevo...**  y navegue hasta la carpeta de contenido del proyecto. Para Android, se encuentra en la carpeta **[proyecto root]\BouncingGame.Android\Assets\Content\**. Para iOS, se encuentra en la carpeta **[proyecto root]\BouncingGame.iOS\Content\**.

Cambiar el **nombre de archivo** a **ContentProject** y haga clic en el **guardar** botón:

![](walkthrough-images/image8.png "Cambie el nombre de archivo a ContentProject y haga clic en el botón Guardar")

Una vez creado el proyecto, la herramienta de canalización MonoGame mostrará información sobre el proyecto cuando la raíz **ContentProject** elemento está seleccionado:

![](walkthrough-images/image9.png "Una vez creado el proyecto, la herramienta de canalización MonoGame mostrará información sobre el proyecto cuando se selecciona el elemento raíz de ContentProject")

Echemos un vistazo a algunas de las opciones más importantes para el proyecto de contenido.


### <a name="output-folder"></a>Carpeta de salida

Ésta es la carpeta (en relación con el propio proyecto de contenido) en la salida **.xnb** se guardarán los archivos. Para no complicar las cosas, vamos a usar la misma carpeta para contener la entrada y archivos de salida. En otras palabras, cambiaremos la **carpeta de salida** sea **.\**  :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>Plataforma

Esto define la plataforma de destino para el contenido. Observe que esto es **Windows** de forma predeterminada, por lo que queremos cambiar esto por nuestra plataforma de destino que es **Android** (o iOS si van a continuación junto con un proyecto de iOS).

![](walkthrough-images/image11.png "Tenga en cuenta que se trata de Windows de forma predeterminada, por lo que cambiarlo a la plataforma de destino que es Android o iOS si van a continuación junto con un proyecto de iOS")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>Procesamiento de archivos en la herramienta de canalización MonoGame

A continuación, iremos agregando contenido en nuestro **ContentProject**. Para este proyecto, iremos agregando archivos a la raíz del proyecto, pero normalmente va a organizar los proyectos grandes su contenido en las carpetas.

Vamos a agregar dos archivos al proyecto:

 - A **.png** archivo que se utilizará para dibujar un sprite. Este archivo puede [descargarse aquí](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true).
 - A **.spritefont** archivo que se usará para dibujar texto en pantalla. La herramienta de canalización de contenido es compatible con la creación de nuevos archivos de .spritefont, así que no hay ningún archivo para descargar.


### <a name="adding-a-png-file"></a>Agregar un archivo .png

Para agregar una **.png** archivo al proyecto, se podrá copiarlo en el mismo directorio que el proyecto de canalización, que tiene el **.mgcb** extensión.

![](walkthrough-images/image12.png "Agregue un archivo .png al proyecto")

A continuación, vamos a agregar el archivo al proyecto de canalización. Para hacer esto en la herramienta de canalización MonoGame, seleccione **Editar > Agregar elemento...** , seleccione la **ball.png** de archivo y haga clic en **abiertos**. El archivo ahora va a formar parte del proyecto de contenido y, cuando se selecciona, mostrará sus propiedades:

![](walkthrough-images/image13.png "El archivo ahora va a formar parte del proyecto de contenido y, cuando se selecciona, mostrará sus propiedades")

Te enviaremos un mensaje que se va a salir de todos los valores en sus valores predeterminados tal y como se requiere para cargar el archivo .xnb en CocosSharp ningún cambio. Podemos crear el archivo seleccionando la **generar > compilar** opción de menú, que se inicie una compilación y mostrar los resultados relativos a la compilación. Podemos comprobar que la compilación funcionó correctamente comprobando el **contenido** carpeta para un nuevo **ball.xnb** archivo:

![](walkthrough-images/image14.png "Compruebe que la compilación ha funcionado correctamente mediante la comprobación de la carpeta de contenido de un nuevo archivo ball.xnb")


### <a name="adding-a-spritefont-file"></a>Agregar un archivo de .spritefont

Podemos crear archivo .spritefont a través de la herramienta de canalización MonoGame. CocosSharp requiere fuentes en una **fuentes** carpeta y las plantillas de CocosSharp crear automáticamente una carpeta de fuentes automáticamente. Podemos agregar esta carpeta a la herramienta de canalización MonoGame seleccionando **Editar > Agregar > carpeta existente...** . Vaya a la **contenido** carpeta y seleccione la **fuentes** carpeta y haga clic en **Aceptar**:

![](walkthrough-images/browsetofonts.png "Vaya a la carpeta de contenido y seleccione la carpeta de fuentes, haga clic en Aceptar")

Para agregar un nuevo archivo .sprintefont, haga doble clic en la carpeta de fuentes y seleccione **Agregar > nuevo elemento...** , seleccione la **SpriteFont descripción** opción, escriba el nombre **36 arial**y haga clic en **Aceptar**. CocosSharp requiere muy específica de nomenclatura de archivos de fuente: deben estar en el formato de [FontType]-[FontSize]. Si una fuente no coincide con este formato de nombres no se cargará por CocosSharp en tiempo de ejecución.

![](walkthrough-images/image15.png "Si una fuente no coincide con este formato de nombres no se cargará por CocosSharp en tiempo de ejecución")

El archivo .spritefont es realmente un archivo XML que se puede editar en cualquier editor de texto, incluidos Visual Studio para Mac. Las variables más habituales editadas en un archivo .spritefont son la `FontName` y `Size` propiedad:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

Se abrirá el archivo en cualquier editor de texto. Como nuestro **arial 36.spritefont** sugiere su nombre, dejaremos la `FontName` como `Arial` , pero cambie el `Size` valor a `36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>Uso de archivos en tiempo de ejecución

Los archivos de .xnb ahora se compilan y está listo para usarse en el proyecto. Iremos agregando los archivos de Visual Studio para Mac, a continuación, vamos a agregar código a nuestro `GameScene.cs` archivo para cargar estos archivos y mostrarlos.


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>Agregar archivos de .xnb a Visual Studio para Mac

Primero vamos a agregar los archivos al proyecto. En Visual Studio para Mac, se podrá expandir la **BouncingGame.Android** proyecto de equipo y expanda el **activos** carpeta, el botón secundario en el **contenido** carpeta y seleccione **Agregar > Agregar archivos...** En primer lugar, se seleccionará el **ball.xnb** se generó anteriormente y haga clic en **abiertos**. A continuación, repita los pasos anteriores, pero agregue el **arial 36.xnb** archivo. Se seleccionará el **mantener el archivo en el subdirectorio actual** opción si Visual Studio para Mac le pregunta cómo agregar el archivo. Una vez terminado ambos archivos deben formar parte de nuestro proyecto:

![](walkthrough-images/image20.png "Una vez terminado ambos archivos deben formar parte del proyecto")


### <a name="adding-gamescenecs"></a>Agregar **GameScene.cs**

Vamos a crear una clase denominada `GameScene,` que contendrá los objetos sprite y texto. Para ello, haga doble clic en el **BouncingGame** (no BouncingGame.Android) del proyecto y seleccione **Agregar > nuevo archivo...** . Seleccione el **General** categoría, seleccione la **clase vacía** opción y, a continuación, escriba el nombre **GameScene**.

Una vez creado, modificaremos la `GameScene.cs` archivo para que contenga el código siguiente:


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

No va a tratar el código anterior ya que trabajar con objetos visuales de CocosSharp como CCSprite y CCLabelTtf se trata en la [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md).

También es necesario agregar código para cargar nuestro recién creado `GameScene`. Para ello abriremos la `GameAppDelegate.cs` archivo (que se encuentra en la **BouncingGame** PCL) y modificar el `ApplicationDidFinishLaunching` método para que aparezca:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

Cuando se ejecuta, el juego tendrá un aspecto como:

![](walkthrough-images/image1.png "Cuando se ejecuta, el juego tendrá aspecto")


## <a name="summary"></a>Resumen

Este tutorial ha mostrado cómo usar la herramienta de canalización MonoGame para crear archivos de .xnb desde un archivo .png de entrada, así como cómo crear un nuevo archivo .xnb desde un archivo .sprintefont recién creado. También se describe cómo estructurar CocosSharp proyectos para usar archivos de .xnb y cómo cargar estos archivos en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [Descargas de MonoGame](http://www.monogame.net/downloads/)
- [Documentación de canalización MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
- [Iniciar proyecto BouncingGame para Android (ejemplo)](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [ball.png gráfico (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
