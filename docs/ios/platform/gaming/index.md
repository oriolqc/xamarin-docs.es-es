---
title: Juegos de API de iOS
description: Este artículo tratan las nuevas mejoras de juegos proporcionadas por iOS 9 que puede usarse para mejorar su juego Xamarin.iOS gráficos y funciones de audio.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 34d3d6980819510a3390e2c30069818d6dfd721f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="ios-gaming-apis"></a>Juegos de API de iOS

_Este artículo tratan las nuevas mejoras de juegos proporcionadas por iOS 9 que puede usarse para mejorar su juego Xamarin.iOS gráficos y funciones de audio._

Apple ha realizado varias mejoras tecnológicas a la API de juegos en iOS 9 que resulten más fácil implementar gráficos de juegos y audio en una aplicación de Xamarin.iOS.
Esto incluye la facilidad de desarrollo a través de los marcos de trabajo de alto nivel y el aprovechamiento de la potencia de GPU del dispositivo iOS para mejorar la velocidad y la capacidad de gráficos.

[![](images/flocking01.png "Un ejemplo de una aplicación que se ejecuta flocado")](images/flocking01.png#lightbox)

Esto incluye GameplayKit, ReplayKit, E/S de modelo, MetalKit y los sombreadores de rendimiento de sistema operativo junto con características nuevas y mejoradas del sistema operativo, SceneKit y SpriteKit.

En este artículo, se impondrán todas las formas para mejorar su juego Xamarin.iOS con iOS de 9 nuevas mejoras de juegos:

## <a name="introducing-gameplaykit"></a>Introducción a GameplayKit

Marco de trabajo nuevo GameplayKit de Apple proporciona un conjunto de tecnologías que hace más fácil crear juegos para dispositivos iOS reduciendo la cantidad de código repetitivo, comunes necesaria para la implementación. GameplayKit proporciona herramientas para desarrollar rápidamente la mecánica de juego que, a continuación, se pueden combinar fácilmente con un motor de gráficos (por ejemplo, SceneKit o SpriteKit) para entregar un juego completado.

GameplayKit incluye varias, comunes, del juego algoritmos como:

- Un comportamiento basado en, simulación de agente que le permite definir los movimientos y los objetivos que los AI emprenderán automáticamente.
- Una inteligencia artificial minmax para el juego basado en turnos.
- Un sistema de regla para la lógica de juego controladas por datos con razonamiento aproximada para proporcionar un comportamiento emergente.

Además, GameplayKit adopta un enfoque de bloques de creación para el desarrollo de juegos mediante el uso de una arquitectura modular que proporciona las siguientes características:

- Sistemas en juego basados en la máquina de Estados para controlar el código complejo y los procedimiento.
- Herramientas para proporcionar el juego e imprecisión aleatorio sin causar problemas de depuración.
- Arquitectura basada en una entidad dividida en componentes reutilizable.

Para obtener más información sobre GameplayKit, vea de Apple [Guía de programación de Gameplaykit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) y [GameplayKit Framework Reference](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Ejemplos de GameplayKit

Vamos a echar un vistazo rápido a implementar algunos mecanismos de juego simple en una aplicación de Xamarin.iOS mediante el kit de juego.

### <a name="pathfinding"></a>Pathfinding

Pathfinding es la capacidad de un elemento AI de un juego para encontrar el camino alrededor del tablero de juego.
Por ejemplo, un enemigo 2D buscar camino a través de un laberinto o un carácter 3D a través de un terreno world Solucionador de persona primero.

Tenga en cuenta la asignación siguiente:

[![](images/gkpathfindpath.png "Un ejemplo de asignación de pathfinding")](images/gkpathfindpath.png#lightbox)

Usar pathfinding este código de C# puede encontrar una manera a través del mapa:

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>Sistema experto clásico

El siguiente fragmento de código de C# muestra cómo se puede utilizar GameplayKit para implementar un sistema experto clásico:

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

En función de un conjunto determinado de reglas (`GKRule`) y un conjunto conocido de entradas, el sistema experto (`GKRuleSystem`) se creará el resultado de predicción (`fizzbuzz` en nuestro ejemplo anterior).

### <a name="flocking"></a>Flocado

Flocado permite a que un grupo de AI controla las entidades de juegos se comporte como un genealógico, donde el grupo se responde a los movimientos y las acciones de una entidad cliente potencial como un genealógico de pájaros en vuelo o una escuela de pez nadando.

El siguiente fragmento de código de C# implementa comportamiento agrupada mediante GameplayKit y SpriteKit para la visualización de gráficos:

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;


        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }


        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

A continuación, implemente esta escena en un controlador de vista:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

Cuando se ejecuta, de forma poco animado _"Boids"_ se ésta alrededor de nuestro derivaciones dedo:

[![](images/flocking01.png "El poco animado Boids se ésta alrededor de las derivaciones dedo")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Otros ejemplos de Apple

Además de los ejemplos anteriores, Apple ha proporcionado las siguientes aplicaciones de ejemplo que pueden ser transcodifica en C# y Xamarin.iOS:

- [FourInARow: Uso de las estrategias de GameplayKit Minmax para adversario AI](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: Usar el sistema de agentes en GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Crear un juego multiplataforma con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

En iOS 9, Apple ha realizado varios cambios y adiciones al sistema operativo para proporcionar acceso de carga baja a la GPU. Con el sistema operativo puede maximizar los gráficos y el potencial de informática de las aplicaciones de iOS.

El marco de trabajo completa incluye las siguientes características nuevas:

- Nueva dimensión privada profundidad de la Galería de símbolos y texturas para OS X.
- Calidad de sombra mejorada con frontal de cierre e independiente de profundidad y valores de espera de la Galería de símbolos.
- Mejoras de lenguaje de sombreado y la biblioteca estándar de sistema operativo completa.
- Los sombreadores de cálculo admiten una amplia variedad de formatos de píxel.

### <a name="the-metalkit-framework"></a>El marco de trabajo MetalKit

El marco de trabajo de MetalKit proporciona un conjunto de clases de utilidad y características que reducen la cantidad de trabajo necesario para usar el sistema operativo en una aplicación de iOS. MetalKit proporciona compatibilidad en tres áreas clave:

1. Cargar desde una variedad de orígenes como formatos comunes como PNG, JPEG, KTX y PVR de textura asincrónica.
2. Facilitar el acceso de E/S de modelo basa activos para el control del sistema operativo específicos del modelo. Estas características se han optimizado alta para proporcionar transferencia de datos eficaz entre mallas de E/S de modelo y los búferes de sistema operativo.
3. Vistas predefinidas de sistema operativo y la administración de vista que reducen en gran medida la cantidad de código necesario para mostrar las representaciones gráficas dentro de una aplicación de iOS.

Para obtener más información sobre MetalKit, vea de Apple [MetalKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356), [Guía de programación de sistema operativo](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), [referencia de marco de sistema operativo](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) y [sistema operativo Guía del lenguaje de sombreado](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Marco de trabajo de sombreadores de rendimiento de sistema operativo

El marco de trabajo de sombreador de rendimiento del sistema operativo proporciona un conjunto muy optimizada de gráficos y en función de cálculo sombreadores para su uso en el sistema operativo en función de las aplicaciones iOS. Cada sombreador en el sombreador de rendimiento del sistema operativo framework ha sido específicamente optimizado para proporcionar un rendimiento alto en el sistema operativo admite iOS GPU.

Mediante el uso de las clases de sombreador de rendimiento del sistema operativo, puede lograr el mayor rendimiento posible en cada GPU de iOS específica sin necesidad de destino y mantener bases de código individuales. Sistema operativo de los sombreadores de rendimiento puede usarse con cualquier recurso de sistema operativo, como las texturas y búferes.

El marco de trabajo de sombreador de rendimiento del sistema operativo proporciona un conjunto de sombreadores comunes como:

- **Desenfoque gausiano** (`MPSImageGaussianBlur`)
- **Detección de bordes Sobel** (`MPSImageSobel`)
- **Histograma de la imagen** (`MPSImageHistogram`)

Para obtener más información, vea de Apple [Guía del lenguaje de sombreado de sistema operativo](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Introducción a E/S de modelo

Marco de trabajo de E/S de modelo de Apple proporciona una comprensión profunda de 3D activos (por ejemplo, modelos y sus recursos relacionados). E/S de modelo proporciona los juegos de iOS con materiales basados en física, modelos y la iluminación que puede usarse con GameplayKit, sistema operativo y SceneKit.

Con E/S de modelo, puede admitir los siguientes tipos de tareas:

- Importar de iluminación, materiales, datos, configuración de la cámara y otra información basada en escena desde una variedad de formatos de motor de juegos y software más populares de la malla.
- Procesar o generar información basada en escena como crear mediante procedimientos con textura sky domos o apoya con esta iluminación en una malla.
- Funciona con MetalKit, SceneKit y GLKit para cargar eficazmente los activos de juego en búferes GPU para la representación.
- Exportar información basada en escena a una variedad de formatos de motor de juegos y software más populares.

Para obtener más información sobre E/S de modelo, vea de Apple [referencia del modelo de E/S Framework](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>Introducción a ReplayKit

Marco de trabajo nuevo ReplayKit de Apple permite agregar grabación de juego al juego iOS fácilmente y permiten al usuario de forma rápida y sencilla, editar y compartir este vídeo desde dentro de la aplicación.

Para obtener más información, vea de Apple [va sociales con vídeo ReplayKit y centro de juegos](https://developer.apple.com/videos/wwdc/2015/?id=605) y sus [DemoBots: crear un juego de plataforma cruzada con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) aplicación de ejemplo.

## <a name="scenekit"></a>SceneKit

Kit de escena es un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D. Apareció por primera vez en OS X 10.8 y ha llegado a iOS 8. Con el Kit de escena crear visualizaciones 3D envolventes y juegos 3D ocasionales no requiere tener conocimientos de OpenGL. Basándose en los conceptos de gráfico de escena comunes, Kit de escena abstrae las complejidades de OpenGL y OpenGL ES, facilitando muy 3D de agregar contenido a una aplicación. Sin embargo, si es un experto de OpenGL, Kit de escena tiene mayor compatibilidad para enlazar directamente con OpenGL así. También incluye varias características que complementan a los gráficos 3D, como física y se integra perfectamente con otros marcos de Apple, como animación de núcleo, imagen Core y Sprite Kit.

Para obtener más información, vea nuestra [SceneKit](~/ios/platform/gaming/scenekit.md) documentación.

### <a name="scenekit-changes"></a>Cambios de SceneKit

Apple ha agregado las siguientes características nuevas para SceneKit IOS 9:

- Xcode ahora proporciona un Editor de escena que le permite crear rápidamente juegos y aplicaciones 3D interactivas mediante la edición de escenas directamente desde dentro de Xcode.
- El `SCNView` y `SCNSceneRenderer` clases pueden utilizarse para habilitar la representación completa (en dispositivos iOS compatible).
- El `SCNAudioPlayer` y `SCNNode` clases se pueden utilizar para agregar efectos de audio espaciales que automáticamente el seguimiento de una posición del Reproductor a una aplicación iOS.

Para obtener más información, vea nuestra [SceneKit documentación](~/ios/platform/introduction-to-ios8.md#scenekit) y de Apple [SceneKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) y [Fox: crear un juego de SceneKit con el Editor de escena Xcode](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)proyecto de ejemplo.

## <a name="spritekit"></a>SpriteKit

Kit de Sprite, el marco de juego 2D de Apple, tiene algunas nuevas características interesantes de iOS 8 y OS X Yosemite. Estos incluyen la integración con el Kit de escena, compatibilidad con el sombreador, iluminación, sombras, restricciones, la generación de asignación normal y mejoras de ciencias físicas. En particular, las nuevas características de ciencias físicas asegurarse muy fácil agregar efectos realistas a un juego.

Para obtener más información, vea nuestra [SpriteKit](~/ios/platform/gaming/spritekit.md) documentación.

### <a name="spritekit-changes"></a>Cambios de SpriteKit

Apple ha agregado las siguientes características nuevas para SpriteKit IOS 9:

- Efecto de audio espacial que realiza el seguimiento automático de posición del Reproductor con el `SKAudioNode` clase.
- Xcode ahora cuenta con un Editor de escena y el Editor de acciones para la creación de aplicaciones y juegos 2D fácil.
- Un desplazamiento fácil juego compatibilidad con nuevos nodos de cámara (`SKCameraNode`) objetos.
- En dispositivos iOS que admiten el sistema operativo, SpriteKit usarán automáticamente, para la representación, incluso si ya estaban utilizando a los sombreadores de OpenGL ES personalizados.

Para obtener más información, vea nuestra [SpriteKit documentación](~/ios/platform/introduction-to-ios8.md#spritekit) de Apple [SpriteKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) y sus [DemoBots: crear un juego de plataforma cruzada con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) aplicación de ejemplo.

## <a name="summary"></a>Resumen

En este artículo ha cubierto las nuevas características de juegos que iOS 9 que se proporciona para las aplicaciones de Xamarin.iOS.
Han introducido GameplayKit y E/S del modelo; las principales mejoras para el sistema operativo; y las nuevas características de SceneKit y SpriteKit.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
