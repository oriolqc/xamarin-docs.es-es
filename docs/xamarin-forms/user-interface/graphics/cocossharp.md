---
title: Uso de CocosSharp en Xamarin.Forms
description: CocosSharp puede usarse para agregar de forma precisa, la imagen y la representación de texto a una aplicación de visualización avanzada
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/03/2016
ms.openlocfilehash: 7ce541134e6db9a26699f96ab3114ced2ad22244
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="using-cocossharp-in-xamarinforms"></a>Uso de CocosSharp en Xamarin.Forms

_CocosSharp puede usarse para agregar de forma precisa, la imagen y la representación de texto a una aplicación de visualización avanzada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolucionar 2016: Cocos # de Xamarin.Forms**

## <a name="overview"></a>Información general

CocosSharp es una tecnología flexible y eficaz para mostrar gráficos, leer la entrada táctil, reproducir contenido de audio y administración. Esta guía explica cómo agregar CocosSharp a una aplicación de Xamarin.Forms. Trata lo siguiente:

* [¿Qué es CocosSharp?](#what)
* [Agregar los paquetes de CocosSharp Nuget](#nuget)
* [Tutorial: Agregar CocosSharp a una aplicación de Xamarin.Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>¿Qué es CocosSharp?

[CocosSharp](~/graphics-games/cocossharp/index.md) es un motor de juegos de código abierto que está disponible en la plataforma de Xamarin.
CocosSharp es una biblioteca en tiempo de ejecución eficaz que incluye las siguientes características:

* Representación de imágenes mediante el [CCSprite (clase)](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* Representación de forma utilizando el [CCDrawNode (clase)](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* La lógica de cada marco utilizando el [CCNode.Schedule (método)](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* Administración de contenido (carga y descarga de recursos, como archivos .png) mediante el [CCTextureCache (clase)](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* Las animaciones mediante el [CCAction (clase)](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

Nos centramos en del CocosSharp es simplificar la creación de juegos 2D multiplataforma; Sin embargo, también puede ser una excelente incorporación a aplicaciones de los formularios de Xamarin. Puesto que juegos normalmente requieren un control preciso sobre objetos visuales y representación eficaz, CocosSharp puede usarse para agregar visualización eficaces y efectos a las aplicaciones de juego no.

Xamarin.Forms se basa en los sistemas de interfaz de usuario nativos, específico de la plataforma. Por ejemplo, [ `Button`s](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) aparecen de manera diferente en iOS y Android e incluso pueda diferir en la versión del sistema operativo. Por el contrario, CocosSharp no usa los objetos visuales específicos de la plataforma, por lo que todos los objetos visuales se muestran idénticos en todas las plataformas. Por supuesto, resolución y relación de aspecto difieren entre los dispositivos, y esto puede afectar a cómo CocosSharp representa los objetos visuales. Estos detalles se explicará más adelante en esta guía.

Encontrará información más detallada en el [CocosSharp sección](~/graphics-games/cocossharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Agregar los paquetes de CocosSharp Nuget

Antes de usar CocosSharp, los desarrolladores necesitan realizar algunas adiciones a su proyecto de Xamarin.Forms.
Esta guía se da por supuesto un proyecto de Xamarin.Forms con un iOS, Android y .NET estándar proyecto de biblioteca.
Todo el código se escribirá en el proyecto de biblioteca estándar. NET; Sin embargo, las bibliotecas deben agregarse a los proyectos de Android y iOS.

El paquete de CocosSharp Nuget contiene todos los objetos necesarios para crear objetos de CocosSharp.
El paquete de nuget CocosSharp.Forms incluye la `CocosSharpView` (clase), que se utiliza para hospedar CocosSharp de Xamarin.Forms.
Agregar el **CocosSharp.Forms** NuGet y **CocosSharp** se agregarán automáticamente también.
Para ello, haga doble clic en el <span class="UIItem">paquetes</span> carpeta en el proyecto de biblioteca de .NET estándar y seleccione <span class="UIItem">agregar paquetes... </span>. Escriba el término de búsqueda <span class="UIItem">CocosSharp.Forms</span>, seleccione <span class="UIItem">CocosSharp de Xamarin.Forms</span>, a continuación, haga clic en <span class="UIItem">Agregar paquete</span>.

![](cocossharp-images/image1.png "Agregar cuadro de diálogo de paquetes")

Ambos **CocosSharp** y **CocosSharp.Forms** paquetes de NuGet se agregarán al proyecto:

![](cocossharp-images/image2.png "Carpeta de paquetes")

Repita los pasos anteriores para los proyectos específicos de la plataforma (por ejemplo, iOS y Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Tutorial: Agregar CocosSharp a una aplicación de Xamarin.Forms

Siga estos pasos para agregar una vista CocosSharp sencilla a una aplicación de Xamarin.Forms:

1. [Crear un Xamarin Forms página](#1)
1. [Agregar un CocosSharpView](#2)
1. [Crear el GameScene](#3)
1. [Agregar un círculo](#4)
1. [Interactuar con CocosSharp](#5)

Una vez que se ha agregado correctamente una vista CocosSharp a una aplicación de Xamarin.Forms, visite la [CocosSharp documentación](~/graphics-games/cocossharp/index.md) para obtener más información sobre la creación de contenido con CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Crear un Xamarin Forms página

CocosSharp se pueden hospedar en cualquier contenedor de Xamarin.Forms. Este ejemplo de esta página utiliza una página denominada `HomePage`. `HomePage` se divide por la mitad por un `Grid` para mostrar cómo Xamarin.Forms y CocosSharp se pueden representar simultáneamente en la misma página.

En primer lugar, configure la página de modo que contenga un `Grid` y dos `Button` instancias:


```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

En iOS, el `HomePage` aparece como se muestra en la siguiente imagen:

![](cocossharp-images/image3.png "Captura de pantalla de página principal")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. Agregar un CocosSharpView

La `CocosSharpView` clase se utiliza para insertar CocosSharp en una aplicación de Xamarin.Forms. Puesto que `CocosSharpView` hereda de la [Xamarin.Forms.View](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) (clase), proporciona una interfaz conocida para el diseño y se puede utilizar dentro de contenedores de diseño como [Xamarin.Forms.Grid](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). Agregue un nuevo `CocosSharpView` al proyecto siguiendo el `CreateTopHalf` método:


```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

CocosSharp inicialización no es inmediata, por lo que se registre un evento para saber cuándo el `CocosSharpView` ha terminado su creación. Hacer esto en el `HandleViewCreated` método:


```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

El `HandleViewCreated` método tiene dos detalles importantes que se utilizan en. La primera es la `GameScene` (clase), que se creará en la siguiente sección. Es importante tener en cuenta que la aplicación no se compilará hasta que el `GameScene` se crea y `gameScene` se resuelve la referencia a la instancia.

El segundo detalle importante es la `DesignResolution` propiedad, que define el área visible del juego para objetos CocosSharp. El `DesignResolution` se analizan propiedad después de crear `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Crear el GameScene

El `GameScene` clase hereda de CocosSharp `CCScene`. `GameScene` es el primer punto donde se tratan exclusivamente con CocosSharp. Código de `GameScene` funcionará en cualquier aplicación CocosSharp, si se aloja dentro de un proyecto de Xamarin.Forms o no.

La `CCScene` clase es la raíz visual de todas las presentaciones de CocosSharp. Debe estar dentro de cualquier objeto CocosSharp visible un `CCScene`. Más específicamente, los objetos visuales deben agregarse a `CCLayer` instancias y los `CCLayer` instancias deben agregarse a un `CCScene`.

En el gráfico siguiente puede ayudar a visualizar una jerarquía CocosSharp típica:

![](cocossharp-images/image4.png "Jerarquía de CocosSharp típico")

Solo un `CCScene` pueden estar activas al mismo tiempo. Mayoría de los juegos usa varios `CCLayer` instancias de contenido de ordenación, pero nuestra aplicación utiliza sólo uno. De forma similar, mayoría de los juegos utiliza varios objetos visuales, pero solo tengamos en nuestra aplicación. Una más detallados debate sobre el CocosSharp jerarquía visual puede encontrarse en el [BouncingGame tutorial](~/graphics-games/cocossharp/bouncing-game.md).

Inicialmente la `GameScene` clase será casi vacía: vamos a crear solo para satisfacer la referencia en `HomePage`. Agregue una nueva clase al proyecto de biblioteca estándar de .NET con el nombre `GameScene`. Debe heredar de la `CCScene` clase como sigue:


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Ahora que `GameScene` está definido, podemos volver a `HomePage` y agregar un campo:


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Ahora podemos compilar el proyecto y ejecútelo para ver CocosSharp ejecutando. No hemos agregamos nada para nuestro `GameScene,` por lo que la mitad superior de la página es negro, el color predeterminado de una escena CocosSharp:

![](cocossharp-images/image5.png "GameScene en blanco")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Agregar un círculo

Actualmente, la aplicación tiene una instancia en ejecución del motor CocosSharp, mostrar vacío `CCScene`. A continuación, vamos a agregar un objeto visual: un círculo. El `CCDrawNode` clase puede utilizarse para dibujar una variedad de formas geométricas, tal como se describe en el [geometría de dibujo a la Guía de CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md).

Agregar un círculo y nuestro `GameScene` clase y crear instancias de él en el constructor como se muestra en el código siguiente:


```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

Ejecutar la aplicación ahora, muestra un círculo en el lado izquierdo del área de presentación CocosSharp:

![](cocossharp-images/image6.png "Círculo en GameScene")


#### <a name="understanding-designresolution"></a>Descripción DesignResolution

Ahora que se muestra un objeto visual de CocosSharp, nos permita investigar la `DesignResolution` propiedad.

El `DesignResolution` representa el ancho y alto del área de colocación y ajustar el tamaño de objetos CocosSharp. La resolución real de la zona se mide en *píxeles* mientras el `DesignResolution` se mide en world *unidades*. El siguiente diagrama muestra la resolución de diversas partes de la vista, tal como se muestra en un iPhone 5 con una resolución de pantalla de 640 x 1136 píxeles:

![](cocossharp-images/image7.png "iPhone 5 s resolución de diseño")

El diagrama anterior muestra dimensiones en píxeles en la parte exterior de la pantalla en texto negro. Unidades se muestran en el interior del diagrama en el texto en blanco. Estos son algunos detalles importantes mostrados sobre:

* El origen de la pantalla CocosSharp está en la parte inferior izquierda. Mover a la derecha aumenta el valor de X, y mover hacia arriba el valor Y. Tenga en cuenta que el valor Y se invierte en comparación con otros motores de diseño 2D, donde (0,0) es la parte superior izquierda del lienzo.
* El comportamiento predeterminado de CocosSharp es mantener la relación de aspecto de la vista. Puesto que la primera fila de la cuadrícula es más ancha alta, CocosSharp no ocupen todo el ancho de la celda, como se muestra en el rectángulo blanco con puntos. Se puede cambiar este comportamiento, como se describe en el [Guía de control de varias resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md).
* En este ejemplo, CocosSharp, mantendrán la relación de aspecto de su dispositivo o un área de visualización de 100 unidades de ancho y altos independientemente del tamaño. Esto significa que el código puede asumir que constituye una X = 100 enlazado el extremo derecho de la CocosSharp Mostrar Mantener diseño coherente en todos los dispositivos.


#### <a name="ccdrawnode-details"></a>Detalles de CCDrawNode

Nuestra aplicación sencilla utiliza la `CCDrawNode` clase para dibujar un círculo. Esta clase puede ser muy útil para aplicaciones de negocio, puesto que ofrece la representación basada en vectores geometría: una característica que faltan en Xamarin.Forms. Además de círculos; el `CCDrawNode` clase puede usarse para dibujar rectángulos, b-spline, líneas y polígonos personalizados. `CCDrawNode` También es fácil de usar ya que no requiere el uso de archivos de imagen (por ejemplo, .png). Para obtener una explicación más detallada de CCDrawNode puede encontrarse en el [geometría de dibujo a la Guía de CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. Interactuar con CocosSharp

Elementos visuales de CocosSharp (como `CCDrawNode`) heredan de la `CCNode` clase. `CCNode` proporciona dos propiedades que pueden usarse para colocar un objeto con respecto a su elemento primario: `PositionX` y `PositionY`. El código utiliza actualmente estas dos propiedades para colocar el centro del círculo, como se muestra en este fragmento de código:


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

Es importante tener en cuenta que se colocan CocosSharp objetos por los valores de posición explícitos, en lugar de la mayoría de Xamarin.Forms las vistas, que se coloca automáticamente según el comportamiento de sus controles de diseño del elemento primario.

Vamos a agregar código para permitir que el usuario haga clic en uno de los dos botones para mover el círculo a la izquierda o a la derecha 10 unidades (no píxeles, ya que el círculo se dibuja en el espacio de unidad del mundo CocosSharp). Primero vamos a crear dos métodos públicos en el `GameScene` clase:


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

A continuación, vamos a agregar controladores a los dos botones en `HomePage` para responder a clics. Cuando termine, nuestro `CreateBottomHalf` método contiene el código siguiente:


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

El círculo CocosSharp mueve ahora en respuesta a los clics. Podemos ver también claramente los límites del lienzo CocosSharp moviendo el círculo suficientemente alejado hacia la izquierda o derecha:

![](cocossharp-images/image8.png "GameScene con el traslado de círculo")

## <a name="summary"></a>Resumen

Esta guía muestra cómo agregar CocosSharp a un Xamarin.Forms existente del proyecto, cómo crear una interacción entre Xamarin.Forms y CocosSharp y se explican varias consideraciones al crear diseños en CocosSharp.

El motor de juegos de CocosSharp ofrece una gran cantidad de funcionalidad y niveles de profundidad, por lo que esta guía sólo un resumen de lo que puede hacer CocosSharp. Los desarrolladores interesados en leer sobre CocosSharp encontrará muchos artículos en la [CocosSharp sección](~/graphics-games/cocossharp/index.md).



## <a name="related-links"></a>Vínculos relacionados

- [API de CocosSharp](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
