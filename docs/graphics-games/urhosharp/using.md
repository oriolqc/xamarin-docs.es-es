---
title: Uso de UrhoSharp para crear un juego 3D
description: Este documento proporciona información general de UrhoSharp, describir escenas, componentes, formas, cámaras, acciones, proporcionados por el usuario, sonido y mucho más.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 7d07733ebf62e6e12ccee05f9b72eaf1a74afad2
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "34784044"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Uso de UrhoSharp para crear un juego 3D

Antes de escribir su primer juego, desea Familiarícese con los conceptos básicos: configuración de la escena, cómo cargar recursos (que contiene el material gráfico) y cómo crear interacciones sencillas para su juego.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Segundo plano, nodos, componentes y cámaras

El modelo de la escena se puede describir como un gráfico de escena basadas en componentes. La escena consta de una jerarquía de nodos de la escena, comenzando desde el nodo raíz, que también representa toda la escena. Cada [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/) tiene una transformación 3D (posición, rotación y escala), un nombre, un identificador, además de un número arbitrario de componentes.  Los componentes que un nodo cobren vida, hacen, puede agregar una representación visual ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)), pueden emitir sonido ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource)), pueden proporcionar un límite de colisiones y así sucesivamente.

Puede crear escenas y nodos de programa de instalación mediante la [Urho Editor](#UrhoEditor), o puede hacer cosas desde el código de C#.  En este documento se analiza el proceso configuración mediante código, tal como muestran los elementos necesarios para obtener las cosas se muestre en la pantalla

Además de configurar la escena, tienes que configurar un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/), esto es lo que determina qué obtener mostrará al usuario.

### <a name="setting-up-your-scene"></a>Configuración de la escena

Lo habitual sería crear este formulario su método de inicio:

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

### <a name="components"></a>Componentes

Representar objetos 3D, reproducción de sonido, física y lógica de los scripts actualizaciones están todos habilitadas mediante la creación de diferentes componentes en los nodos mediante una llamada a [ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/).  Por ejemplo, la instalación su nodo y el componente claro similar al siguiente:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Hemos creado anteriormente un nodo con el nombre "`DirectionalLight`" y establezca una dirección, pero nada más.  Ahora, podemos activar el nodo anterior en un nodo de salida de la luz, adjuntando un [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) componente, con `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

Los componentes creados en el `Scene` sí tienen una función especial: para implementar la funcionalidad de toda la escena. Se debe crearse antes de todos los demás componentes e incluye lo siguiente:

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/): implementa la creación de particiones espacial y acelerado por las consultas de visibilidad. No se pueden representar objetos sin este 3D.
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/): implementa la simulación física. Física, como componentes [ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/) o [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) puede no funcionar correctamente sin este.
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/): implementa depurar la representación de geometría.

Los componentes normales como [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light), [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera) o [`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)
no se deben crear directamente en el [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene), pero en su lugar en los nodos secundarios.

La biblioteca incluye una amplia variedad de componentes que puede adjuntar a los nodos que cobre vida: elementos visibles para el usuario (modelos), sonidos, cuerpos rígidos, formas de colisión, cámaras, fuentes de luz, los emisores de partículas y mucho más.

### <a name="shapes"></a>Formas

Para su comodidad, están disponibles como nodos simple en el espacio de nombres Urho.Shapes varias formas.  Estos incluyen cuadros, esferas, conos, cilindros y planos.

### <a name="camera-and-viewport"></a>Cámara y la ventanilla

Al igual que la luz, las cámaras son componentes, por lo que deberá asociar el componente a un nodo, esto se hace así:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Con esto, ha creado una cámara y ha colocado la cámara en el mundo 3D, el siguiente paso es informar a los `Application` que trata de la cámara que desea usar, esto se realiza con el código siguiente:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

Y ahora debe ser capaz de ver los resultados de su creación.

### <a name="identification-and-scene-hierarchy"></a>Jerarquía de identificación y la escena

A diferencia de los nodos, los componentes no tienen nombres; componentes dentro del mismo nodo solo se identifican por su tipo y el índice en la lista de componentes del nodo, que se rellena en orden de creación, por ejemplo, puede recuperar el [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light) componente fuera de la `lightNode` objeto versiones posteriores como este:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

También puede obtener una lista de todos los componentes mediante la recuperación de la [ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/) propiedad que devuelve un `IList<Component>` que puede usar.

Cuando crea, nodos y los componentes obtención identificadores de enteros escena global. Puede consultar desde la escena con las funciones [ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/) y [ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/). Esto es mucho más rápido que por ejemplo, al hacerlo recursiva las consultas de nodo de escena basada en nombres.

No hay ningún concepto de una entidad o un objeto de juego integrado en su lugar es hasta el programador decida la jerarquía de nodos y en qué nodos desea colocar cualquier lógica de los scripts. Normalmente, se crearía mover liberar objetos en el mundo 3D como elementos secundarios del nodo raíz. Se pueden crear nodos con o sin un nombre con [ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/). No se exige la unicidad de nombres del nodo.

Siempre que hay algunos Composición jerárquica, es recomendado (y de hecho necesaria, ya que los componentes no tienen sus propias transformaciones 3D) para crear un nodo secundario.

Por ejemplo si un carácter mantiene un objeto en su mano, el objeto debe tener su propio nodo, lo que podría tener ósea de mano del carácter (también una [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)).  La excepción es la física [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape), que puede resultar offsetted y girado individualmente en relación con el nodo.

Tenga en cuenta que [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Node/)del posea transformación intencionados se omite como una optimización al calcular las transformaciones de mundo derivado de los nodos secundarios, por lo que cambiar, no tiene ningún efecto y debe dejarse como lo es (posición en origen, no hay un giro ningún ajuste de escala.)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) los nodos pueden ser duplicando libremente. En cambio componentes siempre pertenecen al nodo se adjunta a y no se pueden mover entre nodos. Los nodos y los componentes proporcionan un [ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/) función para realizar esta acción sin tener que pasar por el elemento primario. Una vez que se quite el nodo, ninguna operación en el nodo o el componente en cuestión es segura después de llamar a esa función.

También es posible crear un `Node` que no pertenece a una escena. Esto es útil por ejemplo con una cámara móvil en una escena que se han cargado o guardada, ya que, a continuación, la cámara no se guardarán junto con la escena real y no se destruyen cuando se cargue la escena. Sin embargo, tenga en cuenta que crear componentes de geometría, físicas o script a un nodo no adjunta y, a continuación, moverlo a una escena hará que dichos componentes no funcionen correctamente.

### <a name="scene-updates"></a>Actualizaciones de la escena

Una escena cuyas actualizaciones están habilitadas (valor predeterminado) se actualizarán automáticamente en cada iteración del bucle principal.  La aplicación [ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/) se invoca el controlador de eventos en él.

Los nodos y los componentes se pueden excluir de la actualización de la escena mediante la deshabilitación, vea [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled).  El comportamiento depende del componente específico, pero, por ejemplo, un componente drawable deshabilitar también lo hace invisible, mientras que la deshabilitación de un componente de origen de sonido silencia. Si un nodo está deshabilitado, todos sus componentes se tratan como deshabilitado, independientemente de su propio estado habilitar o deshabilitar.

## <a name="adding-behavior-to-your-components"></a>Agregar un comportamiento a los componentes

Es la mejor manera de estructurar su juego realizar su propio componente que encapsulan un actor o un elemento en su juego.  Esto hace que la característica independiente, de los recursos que se utiliza para mostrar que, a su comportamiento.

La manera más sencilla de agregar comportamiento a un componente es usar las acciones, que son instrucciones que puede poner en cola y combinarlo con la programación asincrónica en C#.  Esto permite el comportamiento para el componente se muy alto nivel y hace que sea más fácil de comprender lo que sucede.

Como alternativa, puede controlar exactamente lo que ocurre con el componente mediante la actualización de las propiedades de componente en cada fotograma (se describe en la sección de comportamiento basado en fotogramas).

### <a name="actions"></a>Acciones

Puede agregar el comportamiento a los nodos muy fácilmente mediante las acciones.  Acciones pueden modificar diversas propiedades de nodo y ejecutarlos en un período de tiempo o repetirlos en un número de veces con una curva de animación determinado.

Por ejemplo, considere la posibilidad de un nodo de "nube" en la escena, se puede atenuar similar al siguiente:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Las acciones son objetos inmutables, lo que permite reutilizar la acción para impulsar los diferentes objetos.

Una forma habitual consiste en crear una acción que realiza la operación inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

El ejemplo siguiente podría difuminar el objeto automáticamente durante un período de tres segundos.  También puede ejecutar una acción tras otra, por ejemplo, se podría primero mover a la nube y ocultarla, a continuación:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Si desea que ambas acciones que ocurren al mismo tiempo, puede usar la acción paralela y proporcionar todas las acciones que desee realizadas en paralelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

En el ejemplo anterior, la nube moverá y atenuar al mismo tiempo.

Observará que estos utilizan C# await, lo que permite pensar linealmente el comportamiento que desea conseguir.

### <a name="basic-actions"></a>Acciones básicas

Estas son las acciones de UrhoSharp admitidas:

* Mover los nodos: [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo), [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo), [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy) , [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* Rotación de nodos: [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo), [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* Escalado de los nodos: [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo), [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* Nodos de difuminación: [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn), [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo), [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut), [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* Tintes: [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo), [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Instantes: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf), [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* Bucle: [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat), [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever), [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

Otras características avanzadas incluyen la combinación de la [ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn) y [ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence) acciones.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Aceleración: controlar la velocidad de las acciones

Aceleración es una manera que dirige a la forma en que la animación se expandir y las animaciones pueden hacer mucho más agradable.  De forma predeterminada las acciones tendrá un comportamiento lineal, por ejemplo un [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo) acción tendría un movimiento muy robótico.  Puede ajustar las acciones en una acción de entrada y salida lenta para cambiar el comportamiento, por ejemplo, uno que lentamente iniciar el movimiento, acelerar y lentamente llega al final ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut)).

Hacer esto ajustando una acción existente en una acción de entradas y salidas lenta, por ejemplo:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Hay muchos modos de entradas y salidas lentas, el gráfico siguiente muestra los distintos tipos de entradas y salidas lentas y su comportamiento en el valor del objeto que se controlan en el período de tiempo, de principio a fin:

![Modos de aceleración](using-images/easing.png "este gráfico muestra los distintos tipos de entradas y salidas lentas y su comportamiento en el valor del objeto que se controlan en el período de tiempo")

### <a name="using-actions-and-async-code"></a>Uso de acciones y código asincrónico

En su [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/) subclase, debe introducir un método asincrónico que prepara el comportamiento de componentes y las unidades de la funcionalidad para él.
A continuación, podría invocar este método con C# `await` palabra clave desde otra parte del programa, ya sea su `Application.Start` método o en respuesta a un punto de usuario o de artículo en la aplicación.

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

En el `Launch` se han iniciado el método anterior tres acciones: el robot entra en la escena, esta acción afectará a la ubicación del nodo durante un período de 0,6 segundos.  Puesto que esta es una opción de async, esto ocurrirá al mismo tiempo como la siguiente instrucción que es la llamada a `MoveRandomly`.  Este método se modificará la posición del robot en paralelo a una ubicación aleatoria.  Esto se logra mediante dos acciones compuestas, el traslado a una nueva ubicación y volviendo a la versión original, colocar y repita este paso, siempre permanece activo el robot.  Y para hacer las cosas más interesante, el robot se grabar simultáneamente.  La solución solo se iniciará cada 0,1 segundos.

### <a name="frame-based-behavior-programming"></a>Programación de comportamiento basado en fotogramas

Si desea controlar el comportamiento del componente según el fotograma a fotograma en lugar de usar las acciones, lo que haría es invalidar el [ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate) método de su [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component) subclase.  Este método se invoca una vez por fotograma y se invoca solo si establece la propiedad ReceiveSceneUpdates en true.

La siguiente muestra cómo crear un `Rotator` componente, que, a continuación, se adjunta a un nodo, lo que hace que el nodo se va a girar:

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

Y esto es cómo desea asociar este componente a un nodo:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Estilos de combinación

Puede usar el modelo asincrónico o acción en función de programación de gran parte del comportamiento que es ideal para enviar y olvidarse de estilo de programación, pero puede también ajustar el comportamiento de su componente para también ejecutar algún código de actualización en cada fotograma.

Por ejemplo, en la demostración SamplyGame se utiliza en el `Enemy` clase codifica las acciones de usos de comportamiento básico, pero también garantiza que los componentes apunte hacia el usuario estableciendo la dirección del nodo con `Node.LookAt`:

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

## <a name="loading-and-saving-scenes"></a>Cargar y guardar en segundo plano

Segundo plano puede cargar y guardar en formato XML; Consulte las funciones [ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml) y [ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml). Cuando se carga una escena, primero se quita todo el contenido existente en él (nodos secundarios y componentes). Los nodos y los componentes que se marcan temporales con el `Temporary` propiedad no se guardarán. El serializador que controla todas las propiedades y los componentes integrados, pero no es lo suficientemente inteligente como controlar las propiedades personalizadas y los campos definidos en sus subclases de componente. Sin embargo proporciona dos métodos virtuales para esto:

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) donde se pueden registrar estados personalizados para la serialización

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) donde puede obtener los estados guardados personalizados.

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

### <a name="object-prefabs"></a>Objeto prefabricados

Simplemente cargar o guardar todos escenas no es lo suficientemente flexible como para juegos donde deben crearse de forma dinámica nuevos objetos. Por otro lado, la creación de objetos complejos y establecer sus propiedades en el código también será una tarea tediosa. Por este motivo, también es posible guardar una escena del nodo donde se incluye sus nodos secundarios, los componentes y los atributos. Estos se pueden cargar más adelante cómodamente como un grupo.  Este tipo de objeto guardado a menudo se conoce como un prefabricado. Existen tres formas de hacer esto:

- En el código mediante una llamada a [ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml) en el nodo
- En el editor, seleccionando el nodo en la ventana de jerarquía y elegir "Guardar del nodo como" en el menú "Archivo".
- Mediante el comando "nodo" en `AssetImporter`, que se guardará la jerarquía de nodos de la escena y todos los modelos incluidos en el recurso de entrada (p ej. un archivo Collada)

Para crear una instancia del nodo guardado en una escena, llame a [ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml). El nodo se creará como un elemento secundario de la escena, pero puede ser duplicando libremente después de eso. Posición y rotación para colocar el nodo deben especificarse. El código siguiente muestra cómo crear una instancia de un prefabricado `Ninja.xm` a una escena con la posición deseada y la rotación:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Eventos

UrhoObjects plantear una serie de eventos, estos aparecen como eventos de C# en las diversas clases que los generan.  Además de C#-modelo basado en eventos, también es posible usar un el `SubscribeToXXX` métodos que le permitirá suscribirse y mantener un token de suscripción que puede usar más adelante para cancelar la suscripción.  La diferencia es que la primera opción permitirá que muchos autores de llamadas para suscribirse, mientras la otra solo permite uno, sino el estilo de lambda mejor enfoque para usarse y todavía, permite que quitar de la suscripción de forma sencilla.  Se excluyen mutuamente.

Cuando se suscribe a un evento, debe proporcionar un método que toma un argumento con los argumentos de evento adecuado.

Por ejemplo, se trata cómo suscribirse a un botón del mouse hacia abajo de evento:

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

Con el estilo de la expresión lambda:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

A veces desea dejar de recibir notificaciones para el evento, en esos casos, guarde el valor devuelto de la llamada a `SubscribeTo` método e invocar el método de cancelación de suscripción en él:

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

El parámetro recibido por el controlador de eventos es una clase de argumentos de evento fuertemente tipado que será específicos para cada evento y que contiene la carga del evento.

## <a name="responding-to-user-input"></a>Responder a la entrada de usuario

Puede suscribirse a diversos eventos, como pulsaciones de tecla hacia abajo por la suscripción al evento y responde a la entrada que se va a entregar:

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

Pero en muchos escenarios, desea que los controladores de actualización de la escena para comprobar el estado actual de las claves cuando se están actualizando y actualizar el código en consecuencia.  Por ejemplo, la siguiente puede usarse para actualizar la ubicación de la cámara basándose en la entrada del teclado:

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

## <a name="resources-assets"></a>Recursos (activos)

Los recursos incluyen la mayoría de las cosas en UrhoSharp que se carga desde el almacenamiento masivo durante la inicialización o en tiempo de ejecución:

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -usar animaciones de esqueleto
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -representa las imágenes almacenadas en una variedad de formatos de gráfico
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -Los modelos 3D
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -materiales que se usa para representar los modelos.
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [Describe](http://urho3d.github.io/documentation/1.4/_particles.html) cómo funciona un emisor de partículas, vea "[partículas](#particles)" a continuación.
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -sombreadores personalizados
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -sonidos para reproducir, consulte "[sonido](#sound)" a continuación.
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -técnicas de materiales de presentación
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -Textura 2D
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -Textura 3D
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) : Textura de cubo
- `XmlFile`

Se administran y cargados por el [ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/) subsistema (disponible como [ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/)).

Los propios recursos se identifican por sus rutas de acceso de archivo, en relación con los directorios de recursos registrado o archivos de paquete. De forma predeterminada, el motor registra los directorios de recursos `Data` y `CoreData`, o los paquetes `Data.pak` y `CoreData.pak` si existen.

Si no se puede cargar un recurso, se registrará un error y se devuelve una referencia nula.

El ejemplo siguiente muestra una forma habitual de obtención de un recurso de la caché del recurso.  En este caso, una textura para un elemento de interfaz de usuario, se usa el `ResourceCache` propiedad desde la `Application` clase.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Recursos también pueden crear manualmente y almacenar a la caché del recurso como si se hubieran cargado desde el disco.

Se pueden establecer los presupuestos de memoria por tipo de recurso: si los recursos consumen más memoria que permite, los recursos más antiguos se quitará de la memoria caché si no se encuentra en uso ya. De forma predeterminada los presupuestos de memoria están establecidos en ilimitado.

### <a name="bringing-3d-models-and-images"></a>Poner imágenes y modelos 3D

Urho3D intenta usar formatos de archivo existentes siempre que sea posible y definir los formatos de archivo personalizados solo cuando sea absolutamente necesario, como para los modelos (*.mdl) y para las animaciones (*. ani). Para estos tipos de recursos, Urho proporciona un convertidor de - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) que puede consumir muchos formatos 3D populares como fbx, dae, 3ds y obj, etcetera.

También hay un complemento práctico para Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) que puede exportar sus activos de Blender con el formato adecuado para Urho3D.

### <a name="background-loading-of-resources"></a>Carga en segundo plano de los recursos

Normalmente, cuando se solicitan los recursos mediante uno de los `ResourceCache`del `Get` método, se cargan inmediatamente en el subproceso principal, lo que puede tardar varios milisegundos para todos los pasos necesarios (cargar archivo de disco, analizar datos, cargar en GPU, si es necesario ) y, por tanto, puede dar lugar a caídas de velocidad de fotogramas.

Si sabe de antemano qué recursos que necesita, puede solicitar que se va a cargar en un subproceso en segundo plano mediante una llamada a `BackgroundLoadResource()`. Puede suscribirse al evento recurso cargado en segundo plano mediante el `SubscribeToResourceBackgroundLoaded` método. le indicará si la carga realmente era un éxito o un error. Según el recurso, solo una parte del proceso de carga puede moverse a un subproceso en segundo plano, por ejemplo el paso de carga GPU acabado siempre debe ocurrir en el subproceso principal. Tenga en cuenta que si se llama a uno de los métodos para un recurso que se pone en cola para la carga en segundo plano de la carga de recursos, se detiene el subproceso principal hasta que finalice su carga.

La escena asincrónica cargar la funcionalidad de `LoadAsync()` y `LoadAsyncXML()` tiene la opción de carga en segundo plano los recursos primero antes de continuar para cargar el contenido de la escena. También se puede usar para cargar solo los recursos sin modificar la escena, especificando el `LoadMode.ResourcesOnly`. Esto permite para preparar un archivo prefabricado de escena o del objeto para la rápida creación de instancias.

Por último, el tiempo máximo (en milisegundos) empleado en cada fotograma en Finalizar en segundo plano los recursos cargados se pueden configurar estableciendo el `FinishBackgroundResourcesMs` propiedad en el `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>Sonido

Sonido es una parte importante del juego y el marco de UrhoSharp proporciona una manera de reproducir sonidos en el juego.  Reproducir sonidos adjuntando un [`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)
componente a un [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) y, a continuación, reproducir un archivo con nombre de los recursos.

Se trata de cómo hacerlo:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Partículas

Partículas proporcionan una manera sencilla de agregar algunos efectos simples y económicos para la aplicación.  Puede consumir objetos almacenados en formato PEX, mediante herramientas como [ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/).

Partículas son componentes que se pueden agregar a un nodo.  Se debe llamar a la del nodo `CreateComponent<ParticleEmitter2D>` método para crear la partícula y, a continuación, configurar la partícula estableciendo la propiedad Effect un efecto de 2D que se carga desde la caché del recurso.

Por ejemplo, puede invocar este método en el componente para mostrar algunos objetos que se representan como un aumento vertiginoso cuando llega a:

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

El código anterior creará un nodo explosion que está asociado al componente actual, dentro de este nodo explosion creamos un emisor de partículas 2D y configurarlo estableciendo la propiedad Effect.  Ejecutamos las dos acciones, uno que se puede utilizar el nodo para que sea más pequeño y otro que deja en ese tamaño de 0,5 segundos.  A continuación, eliminamos la expansión, que también quita el efecto de partículas de la pantalla.

Representa la partícula anterior de esta forma cuando se usa una textura de la esfera:

![Las partículas con una textura esfera](using-images/image-1.png "representa la partícula anterior de esta forma cuando se usa una textura esfera")

Y esto es lo que busca si usas una textura fragmentada:

![Las partículas con una textura cuadro](using-images/image-2.png "y esto es lo que busca si usa una textura fragmentada")

## <a name="multithreading-support"></a>Compatibilidad con multithreading

UrhoSharp es una biblioteca de subproceso única.  Esto significa que no debe intentar invocar métodos en UrhoSharp desde un subproceso en segundo plano, o el riesgo de dañar el estado de la aplicación y es probable que bloquee la aplicación.

Si desea ejecutar código en segundo plano y, a continuación, actualizar los componentes de Urho en la interfaz de usuario principal, puede usar el [`Application.InvokeOnMain(Action)`](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)
.  Además, puede usar await de C# y .NET API para asegurarse de que el código se ejecuta en el subproceso adecuado de tareas.

## <a name="urhoeditor"></a>UrhoEditor

Puede descargar el Editor Urho para su plataforma desde el [sitio Web de Urho](http://urho3d.github.io/), vaya a descargas y elegir la versión más reciente.

## <a name="copyrights"></a>Derechos de autor

Esta documentación contiene contenido original de Xamarin Inc., pero buena ampliamente en la documentación de código abierto para el proyecto Urho3D y contiene las capturas de pantalla del proyecto Cocos2D.
