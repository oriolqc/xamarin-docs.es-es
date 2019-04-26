---
title: API de juegos en Xamarin.iOS de iOS
description: En este artículo abarca las nuevas mejoras de juegos proporcionadas por iOS 9 que se puede usar para mejorar su juego Xamarin.iOS gráficos y funciones de audio.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d8a531e495a19be7437d4a600e758028594248ab
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953336"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>API de juegos en Xamarin.iOS de iOS

_En este artículo abarca las nuevas mejoras de juegos proporcionadas por iOS 9 que se puede usar para mejorar su juego Xamarin.iOS gráficos y funciones de audio._

Apple ha realizado varias mejoras tecnológicas a la API de juegos en iOS 9 que resulte más fácil implementar gráficos de juegos y audio en una aplicación de Xamarin.iOS.
Estos incluyen la facilidad de desarrollo a través de marcos de alto nivel y aprovechar la eficacia de GPU de su dispositivo iOS para mejorar la velocidad y las capacidades de gráficos.

[![](images/flocking01.png "Un ejemplo de una aplicación en ejecución aves")](images/flocking01.png#lightbox)

Esto incluye GameplayKit, ReplayKit, modelo de E/S, MetalKit y sombreadores de rendimiento del sistema operativo junto con las características nuevas y mejoradas de Metal, SceneKit y SpriteKit.

Este artículo presentan los procedimientos para mejorar su juego de Xamarin.iOS con las nuevas mejoras de juegos de iOS 9:

## <a name="introducing-gameplaykit"></a>Introducción a GameplayKit

El marco de Apple nuevo GameplayKit proporciona un conjunto de tecnologías que facilita la creación de juegos para dispositivos iOS reduciendo la cantidad de código repetitivo, comunes necesario para la implementación. GameplayKit proporciona herramientas para desarrollar rápidamente la mecánica de juego que, a continuación, se puede combinar fácilmente con un motor de gráficos (por ejemplo, SceneKit o SpriteKit) para entregar un juego completo.

GameplayKit incluye varias, comunes, juego jugar algoritmos, como:

- Un comportamiento en función, simulación de agente que le permite definir los movimientos y objetivos de la IA emprenderán automáticamente.
- Una inteligencia artificial minmax para el juego basado en turnos.
- Un sistema de regla para la lógica del juego controlada por datos con razonamiento aproximada para proporcionar el comportamiento real de emergencia.

Además, GameplayKit adopta un enfoque de apoyo al desarrollo de juegos mediante el uso de una arquitectura modular que proporciona las siguientes características:

- Sistemas en juegos basados en el equipo de estado para controlar el código complejo, procedimiento.
- Herramientas para proporcionar aleatoria juego e imprevisibilidad sin causar problemas de depuración.
- Arquitectura basada en una entidad reutilizable, formada por componentes.

Para obtener más información sobre GameplayKit, consulte Apple [Gameplaykit Programming Guide](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) y [GameplayKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Ejemplos de GameplayKit

Echemos un vistazo rápido a la implementación de algunos mecanismos de juego simple en una aplicación de Xamarin.iOS mediante el kit de juego.

### <a name="pathfinding"></a>Pathfinding

Pathfinding es la capacidad de un elemento de inteligencia artificial de un juego para encontrar el camino alrededor del tablero del juego.
Por ejemplo, un enemigo 2D búsqueda avanza a través de un laberinto o un carácter a través de un terreno Solucionador de primera persona mundo 3D.

Tenga en cuenta la asignación siguiente:

[![](images/gkpathfindpath.png "Un mapa de pathfinding de ejemplo")](images/gkpathfindpath.png#lightbox)

Mediante este pathfinding C# código puede encontrar una manera a través del mapa:

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

### <a name="classical-expert-system"></a>Sistema experto clásica

El siguiente fragmento de C# código muestra cómo se puede usar GameplayKit para implementar un sistema experto clásico:

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

En función de un conjunto determinado de reglas (`GKRule`) y un conjunto conocido de entradas, el sistema experto (`GKRuleSystem`) creará el resultado de predicción (`fizzbuzz` en nuestro ejemplo anterior).

### <a name="flocking"></a>Aves

Aves permite que entidades de juego se comporte como una manada, donde el grupo responde a los movimientos y las acciones de una entidad de clientes potenciales, como una manada de aves en tránsito o una escuela de peces nadando de control de un grupo de la inteligencia artificial.

El siguiente fragmento de C# código implementa el comportamiento agrupada con GameplayKit y SpriteKit para mostrar los gráficos:

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

A continuación, implementará esta escena en un controlador de vista:

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

Al ejecutarse, el poco animado _"Boids"_ se de ésta en torno a nuestro derivaciones dedo:

[![](images/flocking01.png "El poco animado Boids se de ésta en torno a las derivaciones del dedo")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Otros ejemplos de Apple

Además de los ejemplos presentados anteriormente, Apple ha proporcionado las siguientes aplicaciones de ejemplo que pueden ser transcodificados a C# y Xamarin.iOS:

- [FourInARow: Utilizando al estratega GameplayKit Minmax para AI oponente](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: Usa el sistema de agentes de GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Crear un juego multiplataforma con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

En iOS 9, Apple ha realizado varios cambios y adiciones en Metal para proporcionar acceso de baja sobrecarga a la GPU. Con el sistema operativo puede maximizar el potencial informática de las aplicaciones de iOS y el gráfico.

El marco de trabajo completa incluye las siguientes características nuevas:

- Privada nueva profundidad de la Galería de símbolos y texturas para OS X.
- Calidad de sombra mejorada con puestos frontal de filtrado e independiente de profundidad y Galería espera valores.
- Mejoras de biblioteca estándar de Metal y de lenguaje de sombreado de metal.
- Los sombreadores de cálculo admiten una amplia variedad de formatos de píxel.

### <a name="the-metalkit-framework"></a>El marco de trabajo MetalKit

El marco de trabajo MetalKit proporciona un conjunto de clases de utilidad y las características que reducen la cantidad de trabajo necesario para usar el sistema operativo en una aplicación iOS. MetalKit proporciona compatibilidad en tres áreas clave:

1. Cargar desde una variedad de orígenes como formatos comunes como PNG, JPEG, KTX y PVR de textura asincrónica.
2. Facilitar el acceso de E/S de modelo basa activos para el control de Metal modelo específico. Estas características se han altamente optimizadas para proporcionar transferencia de datos eficaz entre búferes de Metal y mallas de E/S del modelo.
3. Vistas predefinidas de Metal y administración de vista que reducen la cantidad de código necesario para mostrar las representaciones gráficas dentro de una aplicación de iOS.

Para obtener más información sobre MetalKit, consulte de Apple [MetalKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356), [Guía de programación de Metal](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), [Metal Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) y [Metal Guía del lenguaje de sombreado](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>El marco de trabajo de rendimiento metal sombreadores

El marco de trabajo del sombreador de rendimiento del sistema operativo proporciona un conjunto muy optimizada de los gráficos y en función de cálculo sombreadores para su uso en el sistema operativo en función de las aplicaciones de iOS. Cada sombreador en el sombreador de rendimiento de sistema operativo framework ha sido optimizado específicamente para proporcionar alto rendimiento en el sistema operativo admite iOS GPU.

Mediante el uso de las clases de sombreador de rendimiento del sistema operativo, puede lograr el máximo rendimiento posible en cada GPU de iOS concretos sin necesidad de destino y mantener bases de código individuales. Completa los sombreadores de rendimiento puede usarse con cualquier recurso metálico, como las texturas y los búferes.

El marco de trabajo del sombreador de rendimiento del sistema operativo proporciona un conjunto de sombreadores comunes, como:

- **Desenfoque gaussiano** (`MPSImageGaussianBlur`)
- **Detección de bordes Sobel** (`MPSImageSobel`)
- **Histograma de la imagen** (`MPSImageHistogram`)

Para obtener más información, consulte Apple [Guía del lenguaje de sombreado de Metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Introducción a E/S de modelo

El marco de Apple E/S modelo proporciona un profundo conocimiento de los recursos 3D (por ejemplo, modelos y sus recursos relacionados). E/S de modelo proporciona los juegos de iOS con materiales de base física, modelos e iluminación que se puede usar con GameplayKit, sistema operativo y SceneKit.

Con el modelo de E/S, puede admitir los siguientes tipos de tareas:

- Importación de iluminación, materiales, datos, configuración de la cámara y otra información basada en la escena desde una variedad de formatos de motor de juego y el software más populares de la malla.
- Procesar o generar información basada en la escena, tales como crear de manera procedimental con textura cielo domos u hornearlos en una malla de iluminación.
- Funciona con MetalKit, SceneKit y GLKit para cargar eficazmente los activos de juego en búferes GPU para la representación.
- Exportar información basada en la escena a una variedad de software más populares y formatos de motor de juego.

Para obtener más información sobre el modelo de E/S, vea Apple [referencia del modelo de marco de E/S](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>Introducción a ReplayKit

El marco de Apple nuevo ReplayKit le permite agregar grabación de juego a su juego de iOS con facilidad y permitir al usuario editar y compartir este vídeo desde dentro de la aplicación rápida y fácilmente.

Para obtener más información, consulte Apple [va Social con vídeo ReplayKit y Game Center](https://developer.apple.com/videos/wwdc/2015/?id=605) y sus [DemoBots: Crear un juego de plataforma cruzada con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) aplicación de ejemplo.

## <a name="scenekit"></a>SceneKit

Kit de escena es un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D. Apareció por primera vez en OS X 10.8 y ha llegado a iOS 8. Con el Kit de escena crear visualizaciones 3D envolventes y juegos en 3D ocasionales no requiere experiencia en OpenGL. A partir de los conceptos comunes de gráfico de escena, Scene Kit abstrae las complejidades de OpenGL y OpenGL ES, facilitando en gran medida 3D de agregar contenido a una aplicación. Sin embargo, si es un experto de OpenGL, Scene Kit tiene mayor compatibilidad para enlazar directamente con OpenGL también. También incluye numerosas características que complementan los gráficos 3D, como física y se integra perfectamente con otros marcos de Apple, por ejemplo, Core Animation, Core imagen y Sprite Kit.

Para obtener más información, consulte nuestra [SceneKit](~/ios/platform/gaming/scenekit.md) documentación.

### <a name="scenekit-changes"></a>Cambios de SceneKit

Apple ha agregado las siguientes características nuevas para SceneKit para iOS 9:

- Xcode ahora proporciona un Editor de escena que le permite crear rápidamente los juegos y aplicaciones 3D interactivas mediante la edición de escenas directamente desde dentro de Xcode.
- El `SCNView` y `SCNSceneRenderer` clases se pueden usar para habilitar la representación completa (en dispositivos iOS compatibles).
- El `SCNAudioPlayer` y `SCNNode` clases se pueden usar para agregar efectos de audio espaciales que automáticamente el seguimiento de una posición del jugador a una aplicación iOS.

Para obtener más información, consulte nuestra [SceneKit documentación](~/ios/platform/introduction-to-ios8.md#scenekit) y Apple [SceneKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) y [Fox: Crear un juego de SceneKit con el Editor de Xcode escena](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) proyecto de ejemplo.

## <a name="spritekit"></a>SpriteKit

Sprite Kit, el marco de juego 2D de Apple, tiene algunas nuevas características interesantes en iOS 8 y OS X Yosemite. Estos incluyen la integración con Scene Kit sombreador, iluminación, sombras, restricciones, generación de un mapa normal y la compatibilidad con las mejoras de leyes físicas. En concreto, las nuevas características de leyes físicas facilitan agregar efectos realistas a un juego.

Para obtener más información, consulte nuestra [SpriteKit](~/ios/platform/gaming/spritekit.md) documentación.

### <a name="spritekit-changes"></a>Cambios de SpriteKit

Apple ha agregado las siguientes características nuevas para SpriteKit de iOS 9:

- Efecto de audio espacial que automáticamente el seguimiento de la posición del jugador con el `SKAudioNode` clase.
- Xcode ahora cuenta con un Editor de escena y el Editor de acciones para la creación de aplicación y juego 2D fácil.
- Compatibilidad con juegos con nuevos nodos de cámara de desplazamiento fácil (`SKCameraNode`) objetos.
- En dispositivos iOS que admiten el sistema operativo, SpriteKit automáticamente usará para la representación, incluso si ya estaba usando a los sombreadores de OpenGL ES personalizados.

Para obtener más información, consulte nuestra [SpriteKit documentación](~/ios/platform/introduction-to-ios8.md#spritekit) Apple [SpriteKit Framework referencia](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) y sus [DemoBots: Crear un juego de plataforma cruzada con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) aplicación de ejemplo.

## <a name="summary"></a>Resumen

Este artículo trata las nuevas características de juegos que iOS 9 que se proporciona para las aplicaciones de Xamarin.iOS.
Introdujo GameplayKit y modelo de E/S; las mejoras principales de metales; y las nuevas características de SceneKit y SpriteKit.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
