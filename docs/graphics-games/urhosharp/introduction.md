---
title: Introducción a UrhoSharp
description: Este documento describe la estructura básica de una aplicación de UrhoSharp y vínculos a varias guías y aplicaciones de ejemplo que muestran cómo usar UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: d39faabc0851e3e89b03ad58a7f1ead3894efc15
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783560"
---
# <a name="an-introduction-to-urhosharp"></a>Introducción a UrhoSharp

![Logotipo de UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp es un eficaz motor de juegos 3D para los desarrolladores de Xamarin y. NET.  Es en esencia similares a los SceneKit y SpriteKit de Apple e incluyen física, navegación, redes y mucho más mientras continúa estando todas las plataformas.

Es un enlace de .NET para la [Urho3D](http://urho3d.github.io/) motor y permite a los desarrolladores escribir código de plataforma cruzada que puede usarse para Android, iOS, Windows y Mac con el mismo código base y se pueden representar en sistemas OpenGL y Direct3D.

UrhoSharp es un motor de juego con una gran cantidad de funcionalidad de fábrica:

- Representación de gráficos 3D eficaz
- [Física, simulación](https://developer.xamarin.com/api/namespace/Urho.Physics/) (mediante la biblioteca de viñetas)
- [Control de escena](https://developer.xamarin.com/api/type/Urho.Scene/)
- Compatibilidad con Async y await
- [Acciones descriptivas API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [Integración 2D en escenas 3D](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [Representación de fuentes con FreeType](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [Capacidades de red de servidor y cliente](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [Importar una amplia gama de activos](https://developer.xamarin.com/api/namespace/Urho.Resources/) (con la biblioteca de activos abiertos)
- [Malla de navegación y pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) (mediante refundido/desvío)
- [Generación de la forma convexa de detección de colisiones](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) (mediante StanHull)
- [Reproducción de audio](https://developer.xamarin.com/api/namespace/Urho.Audio/) (con **libvorbis**)

## <a name="getting-started"></a>Introducción

UrhoSharp cómodamente se distribuye como un [paquete NuGet](https://www.nuget.org/) y se puede agregar a los proyectos de C# o F # que tienen como destino Windows, Mac, Android o iOS.  NuGet incluye las bibliotecas necesarias para ejecutar el programa, así como los activos básicos (CoreData) utilizados por el motor.

### <a name="urho-as-a-portable-class-library"></a>Urho como una biblioteca de clases Portable

El paquete de Urho puede utilizarse desde un proyecto específico de la plataforma, o desde un proyecto de biblioteca de clases Portable, lo que le permite volver a usar todo el código en todas las plataformas.  Esto significa que todo lo que tiene que hacer en cada plataforma escribir el punto de entrada específica de la plataforma y, a continuación, transfiere el control a su código de juego compartido.

### <a name="samples"></a>Muestras

Puede obtener una visión general de las capacidades de Urho abriendo en Visual Studio para Mac o en Visual Studio de la solución de ejemplo:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La solución predeterminada contiene proyectos para Android, iOS, Windows y Mac.  Hemos estructurado esa solución para que tenemos un selector específico de plataforma pequeña, mientras que todo el código de ejemplo y código de juego reside en una biblioteca de clases portables, que ilustra cómo maximizar la reutilización del código en todas las plataformas.

Consulte la [Urho y su plataforma](~/graphics-games/urhosharp/platform/index.md) página para obtener más información sobre cómo crear sus propias soluciones.

Puesto que todos los ejemplos comparten un conjunto común de elementos de la interfaz de usuario, los ejemplos abstraen la configuración básica de este archivo:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Proporciona una clase base de ejemplo que controla algunas pulsaciones de teclas básicos y táctil a eventos, configuraciones de una cámara, proporciona elementos de la interfaz de usuario básica y esto permite que cada ejemplo para centrarse en la funcionalidad específica que se está expuesta.

El ejemplo siguiente muestra lo que el motor es capaz de hacerlo:

- [Samply juego](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clon de ShootySkies simple.

Mientras que los otros ejemplos muestran las propiedades individuales de cada ejemplo.

## <a name="basic-structure"></a>Estructura básica

El juego debe subclase la [ `Application` ](https://developer.xamarin.com/api/type/Urho.Application/) (clase), es donde se realizará la instalación de su juego (en el [ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/) método) e inicie el juego (en el [ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start) método).  A continuación, construir la interfaz de usuario principal.  Vamos a recorrer una pequeña muestra que muestra las API para la instalación de una escena 3D, algunos elementos de interfaz de usuario y adjuntar un comportamiento de simple a la misma.

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

Si ejecuta esta aplicación, descubrirá rápidamente que el tiempo de ejecución se queja sobre los activos no están disponibles.  Lo que necesita hacer es crear una jerarquía en el proyecto que se inicia con el nombre del directorio especial "Datos" y, dentro de esto, debe colocar los activos que se hace referencia en el programa.  A continuación, debe establecer en las propiedades del elemento para cada activo el "Copy to Output Directory" a "Copiar si más reciente", que garantizará que los datos se están allí.

Permítanos se explica lo que está sucediendo aquí.

Para iniciar la aplicación llame a la función de inicialización del motor, seguida por crear una nueva instancia de la clase de aplicación, como el siguiente:

```csharp
new MySample().Run();
```

El tiempo de ejecución va a invocar la `Setup` y `Start` métodos.  Si invalida `Setup` puede configurar los parámetros del motor (no se muestra en este ejemplo).

Debe invalidar `Start` tal y como se iniciará el juego.  En este método se cargar sus activos, conectar controladores de eventos, la instalación de la escena e iniciar las acciones que desea.  En nuestro ejemplo, ambas creamos un poco de interfaz de usuario para mostrar para el usuario, así como configurar una escena 3D.

El siguiente fragmento de código usa el marco de interfaz de usuario para crear un elemento de texto y agregarla a la aplicación:

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

El marco de interfaz de usuario está ahí para proporcionar una interfaz de usuario en el juego muy sencillo y funciona mediante la adición de nuevos nodos a la [ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/) nodo.

La segunda parte de nuestro configuraciones de ejemplo de la escena principal.  Esto implica a una serie de pasos, creación de una escena 3D, crear un cuadro 3D en la pantalla, agregar una luz, una cámara y un área de visualización.  Estos se describen con más detalle en la sección [escena, nodos, componentes y las cámaras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La tercera parte de nuestro ejemplo desencadena un par de acciones.  Las acciones son recetas que describen un efecto determinado y una vez crean pueden ser ejecutados por un nodo a petición mediante una llamada a la [ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync) método en un `Node`.

La primera acción ajusta el tamaño del cuadro con un efecto de rebote y la segunda gira el cuadro de forma permanente:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Los pasos anteriores muestran cómo es la primera acción que creamos un [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/) acción, esto es simplemente una receta que indique que desea escalar un segundo hacia el valor de la propiedad escala de un nodo.  Esta acción a su vez está ajustada alrededor de una acción de entradas y salida, el [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/) acción.  Las acciones de aceleración distorsionan la ejecución lineal de una acción y aplican un efecto, en este caso proporciona el efecto de rebote de salida.
Por lo que nuestro receta podría escribirse como:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Una vez creada la receta, ejecutamos la receta:

```csharp
await boxNode.RunActionsAsync (recipe)
```

La instrucción await indica que la conveniente reanudar la ejecución después de esta línea cuando se completa la acción.  Una vez completa la acción se activará la segunda animación.

El [UrhoSharp utilizando](~/graphics-games/urhosharp/using.md) documento explora en mayor profundidad los conceptos para Urho y cómo estructurar el código para compilar un juego.

## <a name="copyrights"></a>Derechos de autor

Esta documentación contiene contenido original de Xamarin Inc., pero dibuja ampliamente en la documentación de código abierto para el proyecto Urho3D y contiene las capturas de pantalla desde el proyecto Cocos2D.

### <a name="related-links"></a>Vínculos relacionados

- [Planeta Tierra libro](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [Explorar el libro de coordenadas](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
