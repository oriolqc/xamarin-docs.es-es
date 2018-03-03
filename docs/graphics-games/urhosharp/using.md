---
title: Usar UrhoSharp
description: "Información general sobre el motor de UrhoSharp"
ms.topic: article
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: cd30f17d631216a3ea119a262aca6d294394a77f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="using-urhosharp"></a>Usar UrhoSharp

_Información general sobre el motor de UrhoSharp_

Antes de escribir el primer juego, que desea obtener las nociones básicas de los conceptos básicos: cómo configurar la escena, cómo cargar recursos (que contiene el material gráfico) y cómo crear interacciones simples para el juego.

<a name="scenenodescomponentsandcameras"/>

# <a name="scenes-nodes-components-and-cameras"></a>Plano, nodos, componentes y cámaras

El modelo de la escena se puede describir como un gráfico de escena basado en componentes. La escena se compone de una jerarquía de nodos de escena, empezando por el nodo raíz, que también representa la totalidad de la escena. Cada [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/) tiene una transformación 3D (posición, rotación y escala), un nombre, un identificador, más un número arbitrario de componentes.  Componentes de dan vida a los un nodo, puede hacer que agregue una representación visual ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)), puede emitir sonido ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource)), pueden proporcionar un límite de colisión y así sucesivamente.

Puede crear su plano y nodos de instalación mediante la [Urho Editor](#UrhoEditor), o puede realizar acciones desde el código de C#.  En este documento se explorará las cosas configuración con código, tal y como muestran los elementos necesarios para realizar tareas que se mostrarán en la pantalla

Además de configurar la escena, tienes que configurar un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/), esto es lo que determina qué obtener mostrará al usuario.

## <a name="setting-up-your-scene"></a>Configuración de la escena

Normalmente, debe crear este formulario sobre el método de inicio:

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

## <a name="components"></a>Componentes

Representar objetos 3D, reproducción de sonidos, física y lógica con scripts actualizaciones se habilitan mediante la creación de diferentes componentes en los nodos mediante una llamada a [ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/).  Por ejemplo, la instalación su nodo y el componente claro como el siguiente:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Hemos creado anteriormente un nodo con el nombre "`DirectionalLight`" y establezca una dirección, pero nada más.  Ahora, podemos volver el nodo anterior en un nodo de salida de la luz, adjuntando una [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) componente, con `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

Los componentes creados en el `Scene` dispone por sí misma una función especial: para implementar la funcionalidad de toda la escena. Se debe crearse antes que todos los demás componentes e incluye lo siguiente:

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/): implementa la creación de particiones espaciales y acelera las consultas de visibilidad. No se pueden representar objetos sin este 3D.
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/): implemente la simulación física. Física, como componentes [ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/) o [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) puede no funcionar correctamente sin ella.
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/): implementa depurar representación de geometría.

Componentes normales, como [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light), [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera) o [ `StaticModel` ](https://developer.xamarin.com/api/type/Urho.StaticModel) no deben crearse directamente en el [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene), pero en su lugar en los nodos secundarios.

La biblioteca incluye una amplia variedad de componentes que pueden adjuntar a los nodos para ellos cobren vida: elementos visibles para el usuario (modelos), sonidos, cuerpos rígidas, formas de colisión, cámaras, fuentes de luz, los emisores de partícula y mucho más.

## <a name="shapes"></a>Formas

Para su comodidad, están disponibles como nodos simples en el espacio de nombres Urho.Shapes varias formas.  Se incluyen cuadros, ámbitos, conos, cilindros y planos.

## <a name="camera-and-viewport"></a>Cámara y la ventanilla

Al igual que la luz, cámaras son componentes, por ello, debe asociar el componente a un nodo, esto se realiza la siguiente manera:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Con esto, ha creado una cámara y ha colocado la cámara en el mundo 3D, el siguiente paso es informar a los `Application` que se trata de la cámara que desea usar, esto se realiza con el código siguiente:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

Y ahora debe poder ver los resultados de su creación.

## <a name="identification-and-scene-hierarchy"></a>Jerarquía de identificación y escena

A diferencia de los nodos, los componentes no tienen nombres; componentes dentro del mismo nodo sólo se identifican por su tipo y el índice en la lista de componentes del nodo, que se rellena en orden de creación, por ejemplo, puede recuperar el [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light) componente fuera de la `lightNode` objeto encima de la forma siguiente:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

También puede obtener una lista de todos los componentes mediante la recuperación de la [ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/) propiedad que devuelve un `IList<Component>` que puede usar.

Cuando crea, nodos y componentes obtienen los identificadores de escena global de número entero. Se pueden consultar desde la escena utilizando las funciones [ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/) y [ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/). Esto es mucho más rápido que hacerlo por ejemplo recursivas consultas de escena basada en nombre de nodo.

No hay ningún concepto integrado de una entidad o un objeto del juego; en su lugar es hasta el programador para decidir la jerarquía de nodos y en los nodos que desea colocar cualquier lógica de script. Por lo general, se crearán mover liberar objetos en el mundo 3D como elementos secundarios del nodo raíz. Los nodos pueden crearse con o sin un nombre con [ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/). No se exige la unicidad de nombres del nodo.

Siempre que haya algunos Composición jerárquica, está recomendado (y de hecho necesaria, ya que los componentes no tienen sus propias transformaciones 3D) para crear un nodo secundario.

Por ejemplo si un carácter mantiene un objeto en su mano, el objeto debe tener su propio nodo, que se va a relacionar a hueso de mano del carácter (también una [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)).  La excepción es la física [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape), que puede ser offsetted y girado individualmente en relación con el nodo.

Tenga en cuenta que [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Node/)de poseer transformación intencionadamente se omite como una optimización al calcular transformaciones world derivado de nodos secundarios, por lo que cambiar, no tiene ningún efecto y debe dejarlo tal cual (posición en el origen, no hay un giro ningún ajuste de escala.)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) nodos pueden ser libremente cambia de elemento primario. En cambio componentes siempre pertenecen a para el nodo adjunta a y no se pueden mover entre nodos. Nodos y componentes proporcionan una [ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/) función para lograr esto, sin tener que pasar por el elemento primario. Una vez que se quita el nodo, ninguna operación en el nodo o el componente en cuestión es segura después de llamar a esa función.

También es posible crear un `Node` que no pertenece a una escena. Esto resulta útil por ejemplo con una cámara mover en una escena que puede cargar o guardar, ya que, a continuación, la cámara no se guardarán junto con la escena real y no se destruirá cuando se carga la escena. Sin embargo, tenga en cuenta que crear componentes de geometría, física o secuencia de comandos a un nodo no conectado y, a continuación, moverlo a una escena hará que estos componentes no funcionen correctamente.

## <a name="scene-updates"></a>Actualizaciones de la escena

Una escena cuyas actualizaciones están habilitadas (valor predeterminado) se actualizarán automáticamente en cada iteración del bucle principal.  La aplicación [ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/) se invoca el controlador de eventos en él.

Nodos y los componentes, puede excluir de la actualización de la escena deshabilitarlos, vea [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled).  El comportamiento depende del componente específico, pero, por ejemplo, un componente puede dibujar deshabilitar también lo hace invisible, mientras que al deshabilitar un componente de origen de sonido silencia. Si un nodo está deshabilitado, todos sus componentes se tratan como deshabilitados, independientemente de su propio estado de habilitar o deshabilitar.

# <a name="adding-behavior-to-your-components"></a>Agregar el comportamiento a los componentes

Es la mejor manera de estructurar el juego realizar su propio componente que encapsulan un actor o un elemento en el juego.  Esto hace que la característica autocontenidas, entre los activos que se utiliza para mostrar, a su comportamiento.

La manera más sencilla de agregar el comportamiento a un componente es utilizar acciones, que son instrucciones que puede poner en cola y combinarlo con la programación asincrónica en C#.  Esto permite el comportamiento de su componente como nivel muy alto y es más sencillo comprender lo que está sucediendo.

Como alternativa, puede controlar exactamente lo que ocurre al componente mediante la actualización de las propiedades de componente en cada fotograma (descrito en la sección de comportamiento basados en marcos).

## <a name="actions"></a>Acciones

Puede agregar comportamiento a nodos muy fácilmente con las acciones.  Acciones pueden modificar varias propiedades de nodo y ejecutarlas en un período de tiempo o repetirlo un número de veces con una curva de animación.

Por ejemplo, considere la posibilidad de un nodo de "nube" en la escena, se puede atenuar similar al siguiente:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Las acciones son objetos inmutables, lo que permite volver a usar la acción para impulsar los diferentes objetos.

Una forma habitual consiste en crear una acción que realiza la operación inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

En el ejemplo siguiente se podría difuminar el objeto para usted durante un período de tres segundos.  También puede ejecutar una acción tras otra, por ejemplo, se podría primero mover la nube y, a continuación, ocultarla:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Si desea que las acciones que se realicen al mismo tiempo, puede usar la acción paralela y proporcionar todas las acciones que desee realizadas en paralelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

En el ejemplo anterior la nube se mueva y fundido de salida al mismo tiempo.

Observará que se está usando C# espera, que le permite pensar linealmente el comportamiento que desea conseguir.

## <a name="basic-actions"></a>Acciones básicas

Estas son las acciones que admiten en UrhoSharp:

* Mover los nodos: [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo), [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo), [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy) , [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* Rotación de nodos: [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo), [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* Ajuste de escala en nodos: [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo), [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* Difuminado nodos: [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn), [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo), [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut), [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* Tintes: [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo), [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Momentos: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf), [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* Bucle: [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat), [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever), [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

Otras características avanzadas incluyen la combinación de la [ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn) y [ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence) acciones.

## <a name="easing---controlling-the-speed-of-your-actions"></a>Aceleración: controlar la velocidad de las acciones

Aceleración es una manera que dirige la forma en que la animación se expandir y pueden hacer mucho más agradable las animaciones.  De forma predeterminada las acciones tendrán un comportamiento lineal, por ejemplo un [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo) acción tendría un movimiento muy robótico.  Puede ajustar las acciones en una acción de entrada y salida lenta para cambiar el comportamiento, por ejemplo, que podría lenta iniciar el movimiento, acelerar y lenta llega al final ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut)).

Para ello, al ajustar una acción existente en una acción de entradas y salida, por ejemplo:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Hay muchos modos de aceleración, el gráfico siguiente muestra los distintos tipos de entradas y salidos y su comportamiento en el valor del objeto que se controlan en el período de tiempo, de principio a fin:

![Aceleración modos](using-images/easing.png "este gráfico muestra los distintos tipos de entradas y salidos y su comportamiento en el valor del objeto que se controlan en el período de tiempo")

## <a name="using-actions-and-async-code"></a>Uso de las acciones y código asincrónico

En su [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/) subclase, debe introducir un método asincrónico que prepara el comportamiento de componentes y las unidades de la funcionalidad para él.
A continuación, se invoca este método con C# `await` palabra clave desde otra parte del programa, ya sea su `Application.Start` método o en respuesta a un usuario o caso de punto de la aplicación.

Por ejemplo:

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

En el `Launch` se inician método anterior tres acciones: el robot entra en la escena, esta acción modificará la ubicación del nodo durante un período de 0,6 segundos.  Puesto que se trata de una opción de async, esto ocurrirá al mismo tiempo como la siguiente instrucción que es la llamada a `MoveRandomly`.  Este método modificará la posición del robot en paralelo a una ubicación aleatoria.  Esto se logra mediante dos acciones compuestas, el movimiento a una nueva ubicación y volviendo a la versión original colocar y repita este paso siempre y cuando el robot permanece activo.  Y para hacer cosas más interesante, el robot se grabar simultáneamente.  La solución solo se iniciará cada 0,1 segundos.

## <a name="frame-based-behavior-programming"></a>Programación de comportamiento basados en marcos

Si desea controlar el comportamiento del componente en forma de fotograma a fotograma en lugar de utilizar acciones, lo que haría es invalidar la [ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate) método de su [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component) subclase.  Este método se invoca una vez por fotograma y se invoca solo si establece la propiedad ReceiveSceneUpdates en true.

La siguiente muestra cómo crear un `Rotator` componente, que, a continuación, se adjunta a un nodo, que hace que el nodo que se va a girar:

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

Y trata cómo podría asociar este componente a un nodo:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

## <a name="combining-styles"></a>Estilos de combinación

Puede usar el modelo asincrónico/acción según para programar gran parte del comportamiento que es muy útil para enviar y olvidarse de estilo de la programación, pero se puede también optimizar el comportamiento de su componente para ejecutar algún código de actualización en cada fotograma.

Por ejemplo, en la demostración SamplyGame Esto se usa en la `Enemy` clase codifica las acciones de los usos de comportamiento básico, pero también se asegura de que los componentes señalarán hacia el usuario estableciendo la dirección del nodo sin `Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

# <a name="loading-and-saving-scenes"></a>Cargar y guardar escenas

Plano se pueda cargar y guardar en formato XML; vea las funciones [ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml) y [ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml). Cuando se carga una escena, primero se quita todo el contenido en él (nodos secundarios y los componentes). Nodos y los componentes que se marcan temporales con el `Temporary` propiedad no se guardarán. El serializador controla todos los componentes integrados y propiedades, pero no es lo suficientemente inteligente como administrar propiedades personalizadas y campos definidos en las subclases de su componente. Sin embargo proporciona dos métodos virtuales para esto:

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) donde se pueden registrar estados personalizados para la serialización

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) donde puede obtener los Estados personalizados guardados.

Normalmente, un componente personalizado tendrá un aspecto similar al siguiente:

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

## <a name="object-prefabs"></a>Objeto Prefabs

Solo cargar o guardar todo plano no es lo suficientemente flexible como para juegos donde deben crearse de forma dinámica nuevos objetos. Por otro lado, creación de objetos complejos y establecer sus propiedades en el código también será una tarea tediosa. Por este motivo, también es posible guardar un nodo de escena que incluirá sus nodos secundarios, los componentes y los atributos. Estos cómodamente más tarde se pueden cargar como un grupo.  Este tipo de objeto guardado a menudo se conoce como un prefab. Existen tres formas de hacer esto:

- En el código mediante una llamada a [ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml) en el nodo
- En el editor, seleccione el nodo en la ventana de la jerarquía y elija "Guardar nodo como" en el menú "Archivo".
- Mediante el comando "node" en `AssetImporter`, que guardará la jerarquía de nodos de escena y los modelos de contenido en el recurso de entrada (p. ej. un archivo Collada)

Para crear una instancia del nodo guardado en una escena, llame a [ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml). El nodo se creará como un elemento secundario de la escena, pero puede ser libremente cambia de elemento primario después de eso. Posición y rotación para colocar el nodo deben especificarse. El código siguiente muestra cómo crear una instancia de un prefab `Ninja.xm` a una escena con la posición deseada y la rotación:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

# <a name="events"></a>Eventos

UrhoObjects plantear una serie de eventos, estos aparecen como eventos de C# en las diferentes clases generan.  Además de C#-modelo basado en eventos, también es posible utilizar un el `SubscribeToXXX` métodos que le permitirá suscribirse y mantener un token de suscripción que más adelante puede usar para cancelar la suscripción.  La diferencia es que el primero le permitirá muchos de los llamadores para suscribirse, mientras que la segunda se solo permite uno, pero permite el estilo de lambda más adecuado aproximarse al que se usará y permitir, para quitar de la suscripción de forma sencilla.  Únicamente son mutuamente excluyentes.

Cuando se suscribe a un evento, debe proporcionar un método que toma un argumento con los argumentos de evento correspondiente.

Por ejemplo, se trata cómo se suscribe a un botón del mouse hacia abajo de evento:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

Con estilo de la expresión lambda:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

A veces desea dejar de recibir notificaciones para el evento, en esos casos, guarde el valor devuelto de la llamada a `SubscribeTo` método e invocar el método Unsubscribe en él:

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

El parámetro recibido por el controlador de eventos es una clase de argumentos de evento fuertemente tipado que será específicas para cada evento y contiene la carga del evento.

# <a name="responding-to-user-input"></a>Responder a las entradas de usuario

Puede suscribirse a varios eventos, como pulsaciones de teclas hacia abajo por la suscripción al evento y responde a la entrada que se va a entregar:

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

Pero en muchos escenarios, desea que los controladores de actualización de la escena para comprobar el estado actual de las claves cuando se están actualizando y actualizar el código en consecuencia.  Por ejemplo, la siguiente puede utilizarse para actualizar la ubicación de la cámara en función de las entradas de teclado:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

# <a name="resources-assets"></a>Recursos (activos)

Los recursos incluyen la mayoría de las cosas en UrhoSharp que se carga desde el almacenamiento masivo durante la inicialización o en tiempo de ejecución:

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -utilizado para animaciones de esqueleto
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -representa imágenes almacenadas en una variedad de formatos de gráfico
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -Modelos 3D
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -materiales utilizados para representar los modelos.
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [Describe](http://urho3d.github.io/documentation/1.4/_particles.html) cómo funciona un emisor de partículas, vea "[partículas](#particles)" a continuación.
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -sombreadores personalizados
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -sonidos para la reproducción, consulte "[sonido](#sound)" a continuación.
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -técnicas de presentación material
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -Textura 2D
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -Textura 3D
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) : Textura del cubo
- `XmlFile`

Administra y cargados por el [ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/) subsistema (disponible como [ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/)).

Los propios recursos se identifican por sus rutas de acceso de archivo, en relación con los directorios de recursos registrado o archivos del paquete. De forma predeterminada, el motor registra los directorios de recursos `Data` y `CoreData`, o los paquetes `Data.pak` y `CoreData.pak` si existen.

Si no se puede cargar un recurso, se registrará un error y se devuelve una referencia nula.

En el ejemplo siguiente se muestra una forma habitual de obtener un recurso de la caché de recursos.  En este caso, una textura de un elemento de interfaz de usuario, se usa el `ResourceCache` propiedad desde el `Application` clase.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Recursos también pueden crear manualmente y almacena en la caché de recursos como si se hubieran cargado desde el disco.

Se pueden establecer los presupuestos de memoria por tipo de recurso: si recursos consumen más memoria a la permitida, los recursos más antiguos se quitará de la memoria caché si no se encuentra en uso ya. De forma predeterminada los presupuestos de memoria se establecen en ilimitado.

## <a name="bringing-3d-models-and-images"></a>Volver a poner modelos 3D e imágenes

Urho3D intenta usar formatos de archivo existentes siempre que sea posible y definir los formatos de archivo personalizado sólo cuando sea absolutamente necesario, como para los modelos (*.mdl) y para animaciones (*.ani). Para estos tipos de activos, Urho proporciona un convertidor de - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) que puede consumir muchos formatos 3D populares, como fbx dae, 3ds y obj, etcetera.

También hay un complemento útil para Blender [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) que puede exportar sus activos Blender en el formato adecuado para Urho3D.

## <a name="background-loading-of-resources"></a>Carga en segundo plano de recursos

Normalmente, cuando se solicitan recursos mediante uno de los `ResourceCache`del `Get` método, se cargan inmediatamente en el subproceso principal, lo que puede tardar varios milisegundos para todos los pasos necesarios (cargar el archivo de disco, analizar los datos, cargar en la GPU, si es necesario ) y, por tanto, puede dar lugar a caídas de velocidad de fotogramas.

Si sabe de antemano qué recursos que necesita, puede solicitar que se va a cargar en un subproceso en segundo plano mediante una llamada a `BackgroundLoadResource()`. Puede suscribirse al evento de carga de fondo de recursos mediante el `SubscribeToResourceBackgroundLoaded` método. le indicará si la carga realmente era correcta o un error. Dependiendo de los recursos, solo una parte del proceso de carga se puede mover a un subproceso en segundo plano, por ejemplo el paso de carga GPU acabado siempre debe producirse en el subproceso principal. Tenga en cuenta que si se llama a uno de los métodos para un recurso que está en cola para cargar en segundo plano de la carga de recursos, el subproceso principal pausará hasta que finalice su carga.

La escena asincrónica cargar la funcionalidad de `LoadAsync()` y `LoadAsyncXML()` tiene la opción de carga en segundo plano los recursos en primer lugar antes de seguir para cargar el contenido de la escena. También se puede utilizar para cargar solo los recursos sin modificar la escena, especificando el `LoadMode.ResourcesOnly`. Esto permite que prepare un archivo prefab escena u objeto para la rápida creación de instancias.

Por último, el tiempo máximo (en milisegundos) empleado cada fotograma en acabado fondo recursos cargados se pueden configurar estableciendo la `FinishBackgroundResourcesMs` propiedad en el `ResourceCache`.

<a name="sound"/>

# <a name="sound"></a>Sonido

Sonido es una parte importante de juego, y el marco de trabajo de UrhoSharp proporciona una forma de reproducir sonidos en el juego.  Reproducir sonidos adjuntando un [ `SoundSource` ](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/) componente a un [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) y, a continuación, reproducir un archivo con nombre de sus recursos.

Se trata cómo llevarlo a cabo:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

# <a name="particles"></a>Partículas

Las partículas proporcionan una manera sencilla de agregar algunos efectos simples y económicas a la aplicación.  Puedes utilizar las partículas almacenadas en formato PEX, con herramientas como [http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/).

Las partículas son componentes que se pueden agregar a un nodo.  Debe llamar el nodo `CreateComponent<ParticleEmitter2D>` método para crear la partícula y, a continuación, configurar la partícula estableciendo la propiedad de efecto un efecto 2D que se carga desde la caché de recursos.

Por ejemplo, puede invocar este método en el componente para mostrar algunos objetos que se representan como una explosión cuando llega:

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

El código anterior creará un nodo de expansión que se adjunta al componente actual, dentro de este nodo de expansión se cree un emisor de partícula 2D y configúrelo estableciendo la propiedad de efecto.  Ejecutar dos acciones, uno que amplía el nodo para que sea más pequeño y otro que deja en ese tamaño de 0,5 segundos.  A continuación, se quite la expansión, que también quita el efecto de la partícula de la pantalla.

La partícula anterior representa similar al siguiente cuando se usa una textura esfera:

![Las partículas con una textura de esfera](using-images/image-1.png "la partícula anterior representa similar al siguiente cuando se usa una textura esfera")

Y esto es lo que parece si utiliza una textura fragmentada:

![Las partículas con una textura cuadro](using-images/image-2.png "y esto es lo que parece si utiliza una textura fragmentada")

# <a name="multithreading-support"></a>Compatibilidad con subprocesamiento múltiple

UrhoSharp es una biblioteca de subproceso única.  Esto significa que no debería intentar invocar métodos en UrhoSharp desde un subproceso en segundo plano, o el riesgo de dañar el estado de la aplicación y es probable que bloquee la aplicación.

Si desea ejecutar el código en segundo plano y, a continuación, actualizar los componentes de Urho en la interfaz de usuario principal, puede usar el [ `Application.InvokeOnMain(Action)` ](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain) método.  Además, puede usar C# await y .NET tarea API para asegurarse de que el código se ejecuta en el subproceso adecuado.


# <a name="urhoeditor"></a>UrhoEditor

Puede descargar el Editor de Urho para la plataforma de la [sitio Web de Urho](http://urho3d.github.io/), vaya a las descargas y elegir la versión más reciente.

# <a name="copyrights"></a>Derechos de autor

Esta documentación contiene contenido original de Xamarin Inc., pero dibuja ampliamente en la documentación de código abierto para el proyecto Urho3D y contiene las capturas de pantalla desde el proyecto Cocos2D.



## <a name="related-links"></a>Vínculos relacionados

- [Planeta Tierra libro](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [Explorar el libro de coordenadas](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
