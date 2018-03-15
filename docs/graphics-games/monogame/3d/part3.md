---
title: Coordenadas de 3D en MonoGame
description: "Descripción del sistema de coordenadas 3D es un paso importante en el desarrollo de juegos en 3D. MonoGame proporciona una serie de clases para colocar, orientación y tamaño de los objetos en un espacio 3D."
ms.topic: article
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e930004a91133f391f68221473f212b7caaf1b07
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="3d-coordinates-in-monogame"></a>Coordenadas de 3D en MonoGame

_Descripción del sistema de coordenadas 3D es un paso importante en el desarrollo de juegos en 3D. MonoGame proporciona una serie de clases para colocar, orientación y tamaño de los objetos en un espacio 3D._

Desarrollo de juegos 3D, requiere una comprensión de cómo manipular objetos en un sistema de coordenadas 3D. Este tutorial explica cómo manipular objetos visuales (específicamente un modelo). Vamos a crear en los conceptos de control de un modelo para crear una clase de cámara 3D.

Los conceptos presentados proceden de álgebra lineal, pero echaremos un enfoque práctico para que cualquier usuario sin un fondo de matemáticas seguro puede aplicar estos conceptos en sus propios juegos.

Se tratarán los siguientes temas:

 - Crear un proyecto
 - Crear una entidad de Robot
 - Mover la entidad de Robot
 - Multiplicación de matrices
 - Creación de la entidad de cámara
 - Mover la cámara con la Ayuda

Una vez finalizada, tendremos un proyecto con un robot mover en un círculo y una cámara que puede controlarse mediante la entrada táctil:

![](part3-images/image1.gif "Una vez finalizada, la aplicación incluirá un proyecto con un robot mover en un círculo y una cámara que puede controlarse mediante la entrada táctil")


# <a name="creating-a-project"></a>Crear un proyecto

En este tutorial se centra en mover objetos en un espacio 3D. Comenzaremos con el proyecto de modelos de procesamiento y matrices de vértice [que se pueden encontrar aquí](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). Una vez descargado, descomprima y abra el proyecto para asegurarse de que se ejecuta y deberíamos ver lo siguiente:

![](part3-images/image2.png "Una vez descargado, descomprima y abra el proyecto para asegurarse de que se ejecuta y se debe mostrar esta vista")


# <a name="creating-a-robot-entity"></a>Crear una entidad de Robot

Antes de comenzar mover nuestro robot alrededor, crearemos un `Robot` clase que contiene la lógica de dibujo y movimiento. Los desarrolladores de juegos hacen referencia a esta encapsulación de lógica y los datos como un *entidad*.

Agregue un nuevo archivo de clase vacía a la **MonoGame3D** biblioteca de clases Portable (no el ModelAndVerts.Android específico de la plataforma). Asígnele el nombre **Robot** y haga clic en **New**:

![](part3-images/image3.png "Asígnele el nombre Robot y haga clic en nuevo")

Modificar el `Robot` clase como sigue:


```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);
                        
                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

El `Robot` código es básicamente el mismo código en `Game1` para dibujar un `Model`. Para una revisión en `Model` cargar y dibujar, consulte [esta guía sobre cómo trabajar con modelos](~/graphics-games/monogame/3d/part1.md). Ahora podemos quitar todos los `Model` carga y representación de código de `Game1`y reemplazarlo con un `Robot` instancia:


```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;
                        
            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }                                          
}
```

Si se ejecuta el código ahora tenemos una escena con solo un robot que se dibuja principalmente en el límite inferior:

![](part3-images/image4.png "Si el código se ejecuta ahora, la aplicación mostrará una escena con solo un robot que se dibuja principalmente en el límite inferior")


# <a name="moving-the-robot"></a>Mover el Robot

Ahora que tenemos un `Robot` (clase), podemos agregar lógica de movimiento para el robot. En este caso, que simplemente haremos el robot mover en un círculo según el tiempo de juego. Ésta es una implementación un poco práctica para un juego real, ya que normalmente puede responder un carácter de entrada o inteligencia artificial, pero ofrece un entorno para que podamos explorar posicionamiento 3D y la rotación.

La única información que necesitamos desde fuera de la `Robot` clase es la hora actual del juego. Vamos a agregar un `Update` método que le llevará un `GameTime` parámetro. Esto `GameTime` parámetro se utilizará para las cuales incrementa una variable ángulo que vamos a usar para determinar la posición final para el robot.

En primer lugar, vamos a agregar el campo de ángulo para la `Robot` clase bajo el `model` campo:


```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ... 
```

 Ahora se puede incrementar este valor en un `Update` función:


```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Es necesario para asegurarse de que el `Update` método se llama desde `Game1.Update`:


```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
} 
```

Por supuesto, en este momento el campo ángulo no hace nada: se necesita escribir código para que lo utilice. Se modificará la `Draw` método por lo que podemos calcular el mundo `Matrix` en un método dedicado: 


```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
                
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
} 
```

A continuación, implementaremos el `GetWorldMatrix` método en la `Robot` clase:


```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;
    
    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
} 
```

El resultado de ejecutar este código genera el robot mover en un círculo:

![](part3-images/image5.gif "Ejecuta los resultados de este código en el robot mover en un círculo")


# <a name="matrix-multiplication"></a>Multiplicación de matrices

El código anterior gira el robot mediante la creación de un `Matrix` en el `GetWorldMatrix` método. El `Matrix` struct contiene 16 valores flotantes que se pueden usar para traducir (establecer que la posición), girar y escalar (establecer el tamaño). Cuando se asigna el `effect.World` propiedad, se está indicando subyacente representación sistema cómo ubicar, tamaño y orientación lo que se parecen estar consumiendo (un `Model` o geometría de vértices). 

Afortunadamente, la `Matrix` struct incluye una serie de métodos que simplifican la creación de tipos comunes de matrices. El primero que se usa en el código anterior es `Matrix.CreateTranslation`. El término matemático *traducción* hace referencia a una operación que resulta en un punto (o en nuestro caso un modelo) mover de una ubicación a otra sin modificaciones (por ejemplo, girar o cambiar el tamaño). La función toma un valor de X, Y y Z para la traducción. Si observamos nuestra escena de arriba a abajo, nuestro `CreateTranslation` (método) (en el nivel de aislamiento) realiza lo siguiente:

![](part3-images/image6.png "El método CreateTranslation de forma aislada para realizar esta acción")

Segunda matriz que crearemos es una matriz de rotación usando la `CreateRotationZ` matriz. Es uno de los tres métodos que pueden usar para crear la rotación:

 - `CreateRotationX`
 - `CreateRoationY`
 - `CreateRotationZ`

Cada método crea una matriz de rotación girando sobre un eje especificado. En nuestro caso, nos estamos rotación alrededor del eje Z, que señala "arriba". Lo siguiente puede ayudar a visualizar cómo basada en el eje de rotación funciona:

![](part3-images/image7.png "Esto puede ayudar a visualizar cómo basada en el eje de rotación funciona")

También estamos utilizando la `CreateRotationZ` método con el campo de ángulo, que se incrementa con el tiempo debido a nuestro `Update` llamada al método. El resultado es que el `CreateRotationZ` método provoca nuestro robot en órbita alrededor del origen paso del tiempo.

La última línea del código combina las dos matrices en uno:


```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Esto se conoce como la multiplicación de matrices, que funciona ligeramente diferentes de multiplicación regular. El *propiedad conmutativa de multiplicación* indica que el orden de los números de una operación de multiplicación no cambia el resultado. Es decir, 3 * 4 equivale a 4 * 3. Multiplicación de matrices difiere en que no es conmutativa. Es decir, la línea anterior puede leerse como "Aplicar el translationMatrix para mover el modelo, a continuación, Girar todo aplicando la rotationMatrix". Se puede visualizar la manera en que la línea anterior afecta a la posición y rotación como sigue:

![](part3-images/image8.png "Una la manera en que la línea anterior afecta a la posición y rotación de visualización pf")

Para ayudar a entender cómo el orden de multiplicación de matrices puede afectar a los resultados, tenga en cuenta lo siguiente, donde se invierte la multiplicación de matrices:


```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

El código anterior primero girar el modelo en contexto y después convertirlo:

![](part3-images/image9.png "El código anterior primero girar el modelo en contexto y después convertirlo")

Si se ejecuta el código con la multiplicación invertida, se notará que, puesto que la rotación se aplica en primer lugar, solo afecta a la orientación del modelo y la posición del modelo sigue siendo el mismo. En otras palabras, el modelo se gira en su lugar:

![](part3-images/image10.gif "Gira el modelo en su lugar")


# <a name="creating-the-camera-entity"></a>Creación de la entidad de cámara

El `Camera` entidad contendrá toda la lógica necesaria para realizar el movimiento de entrada y proporcionan las propiedades para la asignación de las propiedades en la `BasicEffect` clase.

En primer lugar deberá implementar una cámara estática (ningún movimiento, basándose en la entrada) e integrarlo en nuestro proyecto existente. Agregue una nueva clase a la **MonoGame3D** biblioteca de clases Portable (el mismo proyecto con `Robot.cs`) y asígnele el nombre **cámara**. Reemplace el contenido del archivo por el código siguiente:


```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;
                
                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

El código anterior es muy similar al código de `Game1` y `Robot` que asigne las matrices en `BasicEffect`. 

Ahora podemos integrar la nueva `Camera` clase en nuestros proyectos existentes. En primer lugar, modificaremos la `Robot` clase para realizar un `Camera` de instancia de su` Draw `método, que eliminará una gran cantidad de código duplicado. Reemplace el `Robot.Draw` método con lo siguiente:


```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
} 
```

A continuación, modifique el `Game1.cs` archivo:


```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;
                        
            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
} 
```

Las modificaciones en el `Game1` desde la versión anterior (que se identifican con `// New camera code` ) son:

 - `Camera` Campo de `Game1`
 - `Camera` creación de instancias de `Game1.Initialize`
 - `Camera.Update` llamar a en `Game1.Update`
 - `Robot.Draw` Ahora tiene un `Camera` parámetro
 - `Game1.Draw` ahora usa `Camera.ViewMatrix` y `Camera.ProjectionMatrix`


# <a name="moving-the-camera-with-input"></a>Mover la cámara con la Ayuda

Hasta ahora, hemos agregado un `Camera` entidad pero aún no lo ha hecho nada con él para cambiar el comportamiento en tiempo de ejecución. Se agregará el comportamiento que permite al usuario al:

 - Pulse en el lado izquierdo de la pantalla para activar la cámara hacia la izquierda
 - Pulse en el lado derecho de la pantalla para activar la cámara a la derecha
 - Pulse en el centro de la pantalla para desplazarse hacia delante de la cámara


## <a name="making-lookat-relative"></a>Realizar lookAt relativa

En primer lugar actualizaremos el `Camera` clase para que incluya un `angle` campo que se utilizará para establecer la dirección que el `Camera` está sufriendo. En la actualidad, nuestra `Camera` determina la dirección está sufriendo a través de la variable local `lookAtVector`, que se asigna a `Vector3.Zero`. En otras palabras, nuestra `Camera` siempre busca en el origen. Si se mueve la cámara, el ángulo de la cámara está sufriendo también cambiará:

![](part3-images/image11.gif "Si se mueve la cámara, a continuación, el ángulo de la cámara está sufriendo también cambiará")

Queremos que el `Camera` para ser accesibles desde la misma dirección, independientemente de su posición: al menos hasta que se implementan la lógica para girar el` Camera `con entradas. El primer cambio será ajustar el `lookAtVector` variable debe basarse en la ubicación actual, en lugar de consultar una posición absoluta:


```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    } 
    ...
```

Esto da como resultado la `Camera` ver todo el mundo en directo. Tenga en cuenta que la inicial `position` valor se ha modificado para `(0, 20, 10)` por lo que el `Camera` se centra en el eje X. Ejecutar la muestra de juego:

![](part3-images/image12.png "Ejecutar el juego muestra esta vista")


## <a name="creating-an-angle-variable"></a>Crear un Variable de ángulo

El `lookAtVector` variable controla el ángulo que está viendo la cámara. Actualmente es fija para ver hacia abajo el eje Y negativo y un poco inclinada hacia abajo (desde el `-.5f` valor Z). Vamos a crear un `angle` variable que se usan para ajustar el `lookAtVector` propiedad. 

En las secciones anteriores de este tutorial se ha explicado que matrices pueden usarse para girar cómo se dibujan los objetos. También podemos usar matrices para girar vectores como la `lookAtVector` mediante el `Vector3.Transform` método. 

Agregar un `angle` campo y modificar el `ViewMatrix` propiedad tal como se indica a continuación:


```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    } 
    ...
```


## <a name="reading-input"></a>Leer la entrada

Nuestro `Camera` entidad ahora se puede controlar a través de su posición y las variables de ángulo: necesitamos cambiar según los datos proporcionados.

En primer lugar, se obtendrá la `TouchPanel` estado para buscar donde el usuario toca la pantalla. Para obtener más información sobre el uso de la `TouchPanel` de clases, consulte [la referencia de API de TouchPanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Si el usuario toca en el tercer izquierdo, a continuación, se podrá ajustar la `angle` valor por lo que el `Camera` gira hacia la izquierda, y si el usuario se usa en la tercera derecho, se podrá girar la otra forma. Si el usuario toca en el tercio medio de la pantalla, se moverá el `Camera` al día.

En primer lugar, agregue un uso de la instrucción que se debe calificar el `TouchPanel` y `TouchCollection` clases en `Camera.cs`:


```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

A continuación, modifique la `Update` método para leer el panel táctil y ajustar el `angle` y `position` variables correctamente:


```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
} 
```

Ahora el `Camera` responderá a la entrada táctil:

![](part3-images/image1.gif "Ahora la cámara responderá a la entrada táctil")

El método Update se inicia llamando a `TouchPanel.GetState`, que devuelve una colección de los últimos retoques. Aunque `TouchPanel.GetState` puede devolver varios puntos de entrada táctil, solo se le preocupa la primera de ellas para simplificar el trabajo.

Si el usuario toca la pantalla, a continuación, el código comprueba si el primer toque está en la izquierda, central o derecho de la tercera de la pantalla. Los tercios izquierdos y derecho girar la cámara aumentando o reduciendo la `angle` variable según el `TotalSeconds` valor (de modo que el juego comporta del mismo, independientemente de la velocidad de fotogramas).

Si el usuario toca en tercer lugar el centro de la pantalla, a continuación, la cámara se moverá hacia adelante. Esto se logra mediante la obtención del vector hacia delante, que inicialmente se define como apuntando hacia el eje Y negativo y girar una matriz que se creó mediante primero `Matrix.CreateRotationZ` y `angle` valor. Por último, el `forwardVector` se aplica a `position` mediante el `unitsPerSecond` coeficiente.


# <a name="summary"></a>Resumen

Este tutorial describe cómo mover y girar `Models` en 3D espacio mediante `Matrices` y `BasicEffect.World` propiedad. Esta forma de movimiento proporciona la base para mover objetos de juego en 3D. Este tutorial también describe cómo implementar un `Camera` entidad para ver el mundo desde cualquier posición y el ángulo.

## <a name="related-links"></a>Vínculos relacionados

- [Vínculo de la API de MonoGame](http://www.monogame.net/documentation/?page=api)
- [Proyecto terminado (ejemplo)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
