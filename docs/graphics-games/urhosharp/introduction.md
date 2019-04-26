---
title: Una introducción a UrhoSharp
description: Este documento describe la estructura básica de una aplicación de UrhoSharp y vínculos a diversas guías y aplicaciones de ejemplo que muestran el uso de UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: a3e14ebca961e828fc578035adaca5ba2a809438
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288526"
---
# <a name="an-introduction-to-urhosharp"></a>Una introducción a UrhoSharp

![Logotipo de UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp es un eficaz motor de juego 3D para los desarrolladores de Xamarin y. NET.  Es similar en espíritu a SceneKit y SpriteKit de Apple e incluyen física, navegación, redes y mucho más mientras siendo multiplataforma.

Es un enlace de .NET para la [Urho3D](http://urho3d.github.io/) del motor y permite a los desarrolladores escribir código de plataforma cruzada que puede tener como destino Android, iOS, Windows y Mac con el mismo código base y se pueden representar en los sistemas de OpenGL y Direct3D.

UrhoSharp es un motor de juego con una gran cantidad de funcionalidad de fábrica:

- Representación de gráficos 3D eficaces
- [Simulación de física](https://developer.xamarin.com/api/namespace/Urho.Physics/) (mediante la biblioteca de viñetas)
- [Control de la escena](https://developer.xamarin.com/api/type/Urho.Scene/)
- Compatibilidad con Async y await
- [Acciones descriptivas API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [Integración 2D en escenas 3D](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [Representación de fuentes con FreeType](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [Funcionalidades de red de servidor y cliente](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [Importar una amplia gama de activos](https://developer.xamarin.com/api/namespace/Urho.Resources/) (con la biblioteca de activos abiertos)
- [Malla de navegación y pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) (mediante refundición/desvío)
- [Generación de la forma convexa para detección de colisiones](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) (mediante StanHull)
- [Reproducción de audio](https://developer.xamarin.com/api/namespace/Urho.Audio/) (con **libvorbis**)

## <a name="getting-started"></a>Introducción

UrhoSharp cómodamente se distribuye como un [paquete NuGet](https://www.nuget.org/) y se puede agregar a su C# o F# proyectos cuyo destino es Windows, Mac, Android o iOS.  El paquete NuGet incluye las bibliotecas necesarias para ejecutar el programa, así como los recursos básicos (CoreData) utilizados por el motor.

### <a name="urho-as-a-portable-class-library"></a>Urho como una biblioteca de clases Portable

El paquete Urho puede utilizarse desde un proyecto específico de plataforma, o desde un proyecto de biblioteca de clases Portable, lo que permite reutilizar todo el código en todas las plataformas.  Esto significa que todo lo que tendría que hacer en cada plataforma es escribir el punto de entrada específicos de plataforma y, a continuación, transfiere el control al código compartido juegos.

### <a name="samples"></a>Muestras

Puede obtener una idea de las capacidades de Urho abriendo en Visual Studio para Mac o Visual Studio desde la solución de ejemplo:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La solución predeterminada contiene proyectos para Android, iOS, Windows y Mac.  Nos hemos estructurado esa solución para que tenemos un iniciador específico de plataforma diminutos, y todo el código de ejemplo y el código de juego reside en una biblioteca de clases portable, que ilustra cómo maximizar la reutilización del código en todas las plataformas.

Consulte la [Urho y su plataforma](~/graphics-games/urhosharp/platform/index.md) para obtener más información sobre cómo crear sus propias soluciones.

Puesto que todos los ejemplos de comparten un conjunto común de elementos de la interfaz de usuario, los ejemplos abstrae la configuración básica de este archivo:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Proporciona una clase base de ejemplo que controla algunas pulsaciones de teclas básicos y táctil a eventos, configuraciones de una cámara, proporciona elementos de la interfaz de usuario básica y esto permite centrarse en la funcionalidad específica que se que se muestra cada ejemplo de.

El ejemplo siguiente muestra lo que es capaz de hacer el motor:

- [Juegos samply](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clon sencillo de ShootySkies.

Mientras que los otros ejemplos muestran las propiedades individuales de cada ejemplo.

## <a name="basic-structure"></a>Estructura básica

El juego debe subclase el [`Application`](https://developer.xamarin.com/api/type/Urho.Application/)
clase, esto es donde configurará su juego (en el [ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/) método) e iniciar el juego (en el [ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start) método).  A continuación, construir la interfaz de usuario principal.  Vamos a recorrer una pequeña muestra que muestra las API de configuración de una escena 3D, algunos elementos de interfaz de usuario y adjuntar un comportamiento simple a él.

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

Si ejecuta esta aplicación, descubrirá rápidamente que el tiempo de ejecución se queja sobre los recursos ya no existen.  Lo que necesita hacer es crear una jerarquía en el proyecto que se inicia con el nombre del directorio especial "Datos" y, dentro de esto, debe colocar los activos que se hace referencia en el programa.  A continuación, debe establecer en las propiedades del elemento para cada activo el "Copy to Output Directory" a "Copiar si más reciente", que se asegurará de que los datos están allí.

Vamos a explicar lo que está ocurriendo aquí.

Para iniciar la aplicación llame a la función de inicialización del motor, seguida por crear una nueva instancia de la clase de aplicación, similar al siguiente:

```csharp
new MySample().Run();
```

El runtime invocará el `Setup` y `Start` métodos para usted.  Si invalida `Setup` puede configurar los parámetros del motor (no mostrar en este ejemplo).

Debe invalidar `Start` tal como se iniciará el juego.  En este método se cargue sus recursos, conectar controladores de eventos, programa de instalación de la escena e iniciar todas las acciones que desee.  En nuestro ejemplo, ambos creamos un poco de interfaz de usuario para mostrar para el usuario, así como la configuración de una escena 3D.

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

La segunda parte de nuestro configuraciones de ejemplo de la escena principal.  Esto implica a una serie de pasos, la creación de una escena 3D, crear un cuadro de 3D en la pantalla, agregar una luz, una cámara y un área de visualización.  Estos se describen con más detalle en la sección [escena, nodos, componentes y las cámaras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La tercera parte de nuestro ejemplo desencadena un par de acciones.  Las acciones son recetas que describen un efecto en particular y, una vez crean pueden ser ejecutadas por un nodo bajo demanda mediante una llamada a la [ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync) método en un `Node`.

El cuadro con un efecto de rebote de escala de la primera acción y la segunda gira el cuadro para siempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Lo anterior muestra cómo es la primera acción que creamos un [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/) acción, esto es simplemente una receta que indica que desea escalar un segundo hacia el valor de la propiedad de escala de un nodo.  Esta acción se ajusta a su vez en torno a una acción de entradas y salidas lenta, el [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/) acción.  Las acciones de entradas y salidas lentas distorsionen la ejecución lineal de una acción y aplican un efecto, en este caso proporciona el efecto de rebote.
Por lo tanto, nuestra receta podría escribirse como:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Una vez creada la receta, ejecutamos la receta:

```csharp
await boxNode.RunActionsAsync (recipe)
```

La instrucción await indica que el querrán reanudar la ejecución después de esta línea cuando se completa la acción.  Una vez que se completa la acción se desencadena la segunda animación.

El [uso de UrhoSharp](~/graphics-games/urhosharp/using.md) documento analizan los conceptos Urho y cómo estructurar el código para crear un juego en más profundidad.

## <a name="copyrights"></a>Derechos de autor

Esta documentación contiene contenido original de Xamarin Inc., pero buena ampliamente en la documentación de código abierto para el proyecto Urho3D y contiene las capturas de pantalla del proyecto Cocos2D.

