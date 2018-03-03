---
title: "Dibujar gráficos 3D con vértices en MonoGame"
description: "MonoGame admite el uso de matrices de vértices para definir cómo se representa un objeto 3D en una base por cada punto. Los usuarios pueden aprovechar las ventajas de matrices de vértices para crear geometría dinámico, implementar efectos especiales y mejorar la eficacia de su procesamiento mediante la selección."
ms.topic: article
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 1b139e460a4841f2174df166bf50bb276802d8d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Dibujar gráficos 3D con vértices en MonoGame

_MonoGame admite el uso de matrices de vértices para definir cómo se representa un objeto 3D en una base por cada punto. Los usuarios pueden aprovechar las ventajas de matrices de vértices para crear geometría dinámico, implementar efectos especiales y mejorar la eficacia de su procesamiento mediante la selección._

Los usuarios que han leído a través de la [guía en los modelos de representación](~/graphics-games/monogame/3d/part1.md) estarán familiarizados con la representación de un modelo 3D en MonoGame. La `Model` clase es una forma eficaz para representar gráficos 3D cuando se trabaja con datos definidos en un archivo (por ejemplo, .fbx) y cuando se trabaja con datos estáticos. Algunos juegos requieren geometría 3D definida o manipular dinámicamente en tiempo de ejecución. En estos casos, podemos usar matrices de *vértices* para definir y representar geometría. Un vértice es un término general para un punto en el espacio 3D que forma parte de una lista ordenada utilizada para definir la geometría. Normalmente los vértices se ordenan de manera que se defina una serie de triángulos.

Para poder visualizar cómo los vértices se usan para crear objetos 3D, veamos la esfera siguiente:

![](part2-images/image1.png "Para poder visualizar cómo los vértices se usan para crear objetos 3D, tenga en cuenta esta esfera")

Como se indicó anteriormente, la esfera es claramente formada por varios triángulos. Podemos ver el modelo de alambres de la esfera para ver cómo se conectan los vértices a triángulos de formulario:

![](part2-images/image2.png "Ver el modelo de alambres de la esfera para ver cómo se conectan los vértices a triángulos de formulario")

En este tutorial se tratará los siguientes temas:

 - Creación de un proyecto
 - Crear los vértices
 - Agregar código de dibujo
 - Representación con una textura
 - Modificación de las coordenadas de textura
 - Representación de vértices con modelos

El proyecto terminado contendrá un piso a cuadros que se dibujará utilizando una matriz de vértices:

![](part2-images/image3.png "El proyecto terminado contendrá un piso a cuadros que se dibujará utilizando una matriz de vértices")


# <a name="creating-a-project"></a>Crear un proyecto

En primer lugar, se descargará un proyecto que servirá como el punto de partida. Vamos a usar el proyecto de modelos [que se pueden encontrar aquí](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

Una vez descargado y descomprimido, abra y ejecute el proyecto. Esperamos que ver los modelos de robot seis que se va a dibujar en pantalla:

![](part2-images/image4.png "Seis modelos robot que se va a dibujar en pantalla")

Al final de este proyecto se podrá ser la combinación de nuestra propia representación vértice personalizado con el robot `Model`, por lo que no vamos a eliminar el código de representación de robot. En su lugar, se deberá just borrar el `Game1.Draw` llamada para quitar el dibujo de las 6 robots por ahora. Para ello, abra el **Game1.cs** de archivos y busque el `Draw` método. Modificarla para que contenga el código siguiente:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    base.Draw(gameTime);
}
```

El resultado será el juego muestra una pantalla azul vacía:

![](part2-images/image5.png "El resultado será el juego muestra una pantalla azul vacía")


# <a name="creating-the-vertices"></a>Crear los vértices

Se creará una matriz de vértices para definir la geometría. En este tutorial, se creará un plano 3D (un cuadrado en un espacio 3D, no un avión). Aunque el plano tiene cuatro lados y cuatro esquinas, se podría estar compuesta por dos triángulos, cada uno de los cuales requiere tres vértices. Por lo tanto, vamos a definir un total de seis puntos.

Hasta ahora hemos estado hablando acerca de vértices en sentido general, pero MonoGame proporciona algunas estructuras estándares que se pueden usar para vértices:

 - `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Nombre de cada tipo indica los componentes que contiene. Por ejemplo, `VertexPositionColor` contiene los valores de posición y el color. Echemos un vistazo a cada uno de los componentes:

 - Todos los tipos de vértice de posición: incluir un `Position` componente. El `Position` valores definen dónde se encuentra el vértice en un espacio 3D (X, Y y Z).
 - Color: vértices, opcionalmente, pueden especificar un `Color` valor para realizar la aplicación personalizada.
 - Normal: las normales de definen qué forma está orientada hacia la superficie del objeto. Las normales son necesarias que si la representación de un objeto con iluminación desde la dirección que una superficie está sufriendo impactos cuánto claro que recibe. Las normales se suelen especificar como un *vector unitario* : un vector 3D que tiene una longitud de 1.
 - Textura: textura hace referencia a las coordenadas de textura: es decir, qué parte de una textura debe aparecer en un vértice determinado. Los valores de textura son necesarios si la representación de un objeto 3D con una textura. Coordenadas de textura son coordenadas normalizadas, lo que significa que se encuentran valores entre 0 y 1. Trataremos las coordenadas de textura con más detalle más adelante en esta guía.

Nuestro plano funcionará como un piso y queremos aplicar una textura al realizar la representación, por lo que se usará el `VertexPositionTexture` tipo para definir nuestro vértices.

En primer lugar, vamos a agregar a un miembro a la clase Game1:


```csharp
VertexPositionTexture[] floorVerts; 
```

A continuación, defina nuestro vértices en `Game1.Initialize`. Tenga en cuenta que la plantilla proporcionada mencionada anteriormente en este artículo no contiene un `Game1.Initialize` método, por lo que necesitamos agregar el método completo para `Game1`:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

Para ayudar a visualizar el aspecto que tendrá nuestro vértices, considere el siguiente diagrama:

![](part2-images/image6.png "Para ayudar a visualizar el aspecto que tendrá los vértices, considere la posibilidad de este diagrama")

Es necesario depender de nuestro diagrama para visualizar los vértices hasta que se termine de implementar el código de representación.


# <a name="adding-drawing-code"></a>Agregar código de dibujo

Ahora que tenemos las posiciones para nuestro geometría definida, podemos escribir el código de representación.

En primer lugar, se necesitará definir un `BasicEffect` instancia que contendrá los parámetros de representación como posición e iluminación. Para ello, agregue un `BasicEffect` miembro a la `Game1` clase debajo donde la `floorVerts` se define el campo:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect; 
```

A continuación, modifique la `Initialize` método para definir el efecto:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Ahora podemos agregar código para realizar el dibujo:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start 
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
} 
```

Será necesario llamar a `DrawGround` en nuestra `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

La aplicación mostrará lo siguiente cuando se ejecuta:

![](part2-images/image7.png "La aplicación mostrará esto cuando se ejecuta")

Echemos un vistazo a algunos de los detalles en el código anterior.


## <a name="view-and-projection-properties"></a>Ver y propiedades de proyección

El `View` y `Projection` propiedades controlan cómo se ver la escena. Se va a modificar este código más adelante cuando se vuelva a agrega el código de representación del modelo. En concreto, `View` controla la ubicación y la orientación de la cámara y `Projection` controles el *campo de vista* (que puede utilizarse para acercar o alejar la cámara).


## <a name="techniques-and-passes"></a>Técnicas y pasadas

Una vez nos hemos propiedades asignadas en nuestro efecto que se puede realizar el procesamiento real. 

No va a modificar el `CurrentTechnique` propiedad en este tutorial, pero los juegos más avanzados puede tener un efecto único que puede realizar el dibujo de maneras diferentes (por ejemplo, cómo se aplica el valor de color). Cada uno de estos modos de representación se puede representar como una técnica que puede asignarse antes de representarlo. Además, cada una de ellas puede necesitar varios recorridos para representar correctamente. Efectos pueden necesitar varias fases si objetos visuales complejos, como una superficie brillante o traseros de representación.

Lo importante a tener en cuenta es que la `foreach` bucle permite el mismo código de C# representar cualquier efecto independientemente de la complejidad de subyacente `BasicEffect`.


## <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` es donde se representan los vértices. El primer parámetro indica el método cómo, hemos ordenado nuestro vértices. Nos hemos estructurado para que cada triángulo se define mediante tres vértices ordenadas, por lo que usamos el `PrimitiveType.TriangleList` valor.

El segundo parámetro es la matriz de vértices que se definió anteriormente.

El tercer parámetro especifica el primer índice para dibujar. Puesto que deseamos que nuestra matriz de vértices todo va a representar, se le pasa un valor de 0.

Por último, se especifican cuántos triángulos para representar. Nuestra matriz de vértices contiene dos triángulos, por lo que se pase un valor de 2.


# <a name="rendering-with-a-texture"></a>Representación con una textura

En este punto nuestra aplicación representa un plano blanco (en perspectiva). A continuación agregaremos una textura a nuestro proyecto que se usará al representar el plano. 

Para no complicar las cosas, vamos a agregar la .png directamente a nuestro proyecto, en lugar de usar la herramienta de canalización MonoGame. Para ello, descargue [este archivo .png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) a su equipo. Una vez descargado, haga doble clic en el **contenido** carpeta en el panel de la solución y seleccione **Agregar > Agregar archivos...**  . Si trabaja en Android, a continuación, esta carpeta se encuentra en la **activos** carpeta en el proyecto específico de Android. Si lo hiciera en iOS, a continuación, esta carpeta estará en la raíz del proyecto de iOS. Navegue hasta la ubicación donde **checkerboard.png** se guarda y seleccione este archivo. Seleccione esta opción para copiar el archivo en el directorio.

A continuación, vamos a agregar el código para crear nuestra `Texture2D` instancia. En primer lugar, agregue el `Texture2D` como miembro del `Game1` en el `BasicEffect` instancia:


```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificar `Game1.LoadContent` como se indica a continuación:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
} 
```

A continuación, modifique la `DrawGround` método. La modificación sola es necesaria consiste en asignar `effect.TextureEnabled` a `true` y establecer el `effect.Texture` a `checkerboardTexture`:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Por último, es necesario modificar el `Game1.Initialize` método para asignar una textura también coordina en nuestro vértices:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Si se ejecuta el código, podemos ver que nuestra plano muestra ahora un patrón de tablero de ajedrez:

![](part2-images/image8.png "Ahora, el plano muestra un patrón de cuadros bicolores")


# <a name="modifying-texture-coordinates"></a>Modificación de textura coordina

Usa MonoGame normaliza las coordenadas de textura, que son las coordenadas entre 0 y 1 en lugar de entre 0 y el ancho de la textura o el alto. El siguiente diagrama puede ayudar a visualizar coordenadas normalizados:

![](part2-images/image9.png "Este diagrama puede ayudar a visualizar coordenadas normalizadas")

Coordenadas de textura normalizado permiten cambiar el tamaño de textura sin necesidad de volver a escribir código o volver a crear modelos (como archivos .fbx). Esto es posible porque normalizados coordenadas representan una relación en lugar de píxeles específicos. Por ejemplo, (1,1) tendrán siempre representan la esquina inferior derecha sin tener en cuenta el tamaño de la textura.

Podemos cambiar la asignación de coordenadas de textura para utilizar una variable única para el número de repeticiones:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

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

    base.Initialize ();
} 
```

Esto da como resultado la textura 20 veces de repetición:

![](part2-images/image10.png "Esto da como resultado la textura repetir 20 veces")


# <a name="rendering-vertices-with-models"></a>Representación de vértices con modelos

Ahora que nuestro plano está representando correctamente, podemos agregar volver a los modelos para ver todos los elementos juntos. En primer lugar, vamos a volver a agregar el código de modelo para nuestro `Game1.Draw` método (con posiciones modificadas):


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

También se creará un `Vector3` en `Game1` para representar la posición de la cámara. Vamos a agregar un campo en nuestra `checkerboardTexture` declaración:


```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

A continuación, quite la variable local `cameraPosition` variable desde el `DrawModel` método:


```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Quitar del mismo modo local `cameraPosition` variable desde el `DrawGround` método:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

Ahora si se ejecuta el código podemos ver los modelos y el principio al mismo tiempo:

![](part2-images/image11.png "Los modelos y el principio se muestran al mismo tiempo")

Si se modifica la posición de la cámara (por ejemplo, aumentando el valor de X, que en este caso mueve la cámara a la izquierda) podemos ver que el valor incide en el suelo y los modelos:


```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Este código genera el siguiente:

![](part2-images/image3.png "Este código produce esta vista")


# <a name="summary"></a>Resumen

Este tutorial ha mostrado cómo usar una matriz de vértices para realizar la representación personalizada. En este caso, hemos creado un piso bandera mediante la combinación de nuestro basada en el vértice con la representación de una textura y `BasicEffect`, pero el código aquí expuesta actúa como base para cualquier representación en 3D. También hemos mostrado que representación vértices según se puede combinar con los modelos en la misma escena.

## <a name="related-links"></a>Vínculos relacionados

- [Archivo de cuadros bicolores (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Proyecto completado (ejemplo)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
