---
title: Dibujar gráficos en 3D con vértices en MonoGame
description: MonoGame admite el uso de matrices de vértices para definir cómo se representa un objeto 3D en una base por cada punto. Los usuarios pueden aprovechar las ventajas de matrices de vértices para crear geometría dinámico, implementar efectos especiales y mejorar la eficacia de su procesamiento mediante la selección.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: df36c149e98e8c0cbb16de4c2cf52def5713ec13
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121443"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Dibujar gráficos en 3D con vértices en MonoGame

_MonoGame admite el uso de matrices de vértices para definir cómo se representa un objeto 3D en una base por cada punto. Los usuarios pueden aprovechar las ventajas de matrices de vértices para crear geometría dinámico, implementar efectos especiales y mejorar la eficacia de su procesamiento mediante la selección._

Los usuarios que han leído a través de la [guía en los modelos de representación](~/graphics-games/monogame/3d/part1.md) estarán familiarizados con la representación de un modelo 3D en MonoGame. La `Model` clase es una forma eficaz para representar gráficos 3D cuando se trabaja con datos definidos en un archivo (por ejemplo, .fbx) y cuando se trabaja con datos estáticos. Algunos juegos requieren geometría 3D que se define o manipular dinámicamente en tiempo de ejecución. En estos casos, podemos usar matrices de *vértices* para definir y representar geometría. Un vértice es un término general para un punto en el espacio 3D que forma parte de una lista ordenada que se usan para definir la geometría. Normalmente los vértices se ordenan de manera que se defina una serie de triángulos.

Para ayudar a visualizar cómo se usan los vértices para crear objetos 3D, consideremos la esfera siguiente:

![](part2-images/image1.png "Para ayudar a visualizar cómo se usan los vértices para crear objetos 3D, tenga en cuenta esta esfera")

Como se indicó anteriormente, la esfera es claramente formada por varios triángulos. Podemos ver la estructura de alambre de la esfera para ver cómo los vértices se conectan a triángulos del formulario:

![](part2-images/image2.png "Ver la estructura de alambre de la esfera para ver cómo los vértices se conectan a triángulos de formulario")

En este tutorial se trata los temas siguientes:

- Creación de un proyecto
- Creación de los vértices
- Agregar código de dibujo
- Representación con una textura
- Modificación de las coordenadas de textura
- Representación de vértices con los modelos

El proyecto finalizado contendrá un suelo a cuadros que se dibuja utilizando una matriz de vértice:

![](part2-images/image3.png "El proyecto finalizado contendrá un suelo a cuadros que se dibuja utilizando una matriz de vértice")

## <a name="creating-a-project"></a>Crear un proyecto

En primer lugar, se descargará un proyecto que servirá como nuestro punto de partida. Vamos a usar el proyecto de modelos [que puede encontrarse aquí](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

Una vez descargado y descomprimido, abra y ejecute el proyecto. Esperamos ver seis modelos robot que se va a dibujar en la pantalla:

![](part2-images/image4.png "Seis modelos robot que se va a dibujar en la pantalla")

Al final de este proyecto se podrá combinar nuestra propia representación vértice personalizado con el robot `Model`, por lo que no vamos a eliminar el código de representación de robot. En su lugar, simplemente deberá borrar la `Game1.Draw` llamada para quitar el dibujo de los 6 robots por ahora. Para ello, abra el **Game1.cs** de archivo y busque el `Draw` método. Modifíquelo para que contenga el siguiente código:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Esto dará como resultado de nuestro juego mostrar una pantalla azul vacía:

![](part2-images/image5.png "Esto dará como resultado el juego de mostrar una pantalla azul vacía")

## <a name="creating-the-vertices"></a>Creación de los vértices

Crearemos una matriz de vértices para definir la geometría. En este tutorial, crearemos un plano 3D (un cuadrado en el espacio 3D, no un avión). Aunque nuestro plano tiene cuatro lados y las esquinas de cuatro, se estará compuesta por dos triángulos, cada uno de los cuales requiere tres vértices. Por lo tanto, vamos a definir un total de seis puntos.

Hasta ahora hemos estado hablando acerca de los vértices en sentido general, pero MonoGame proporciona algunas estructuras estándares que se pueden usar para los vértices:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Nombre de cada tipo indica los componentes que contiene. Por ejemplo, `VertexPositionColor` contiene los valores de posición y color. Echemos un vistazo a cada uno de los componentes:

- Todos los tipos de vértices de posición: incluyen un `Position` componente. El `Position` valores definen dónde se encuentra el vértice en espacio 3D (X, Y y Z).
- Los vértices de color: opcionalmente, pueden especificar un `Color` valor para realizar tintes personalizado.
- Normal: normales de definen qué modo está orientado la superficie del objeto. Normales son necesarias si la representación de un objeto con la iluminación desde la dirección que una superficie está orientado a efectos de claridad que lo recibe. Las normales se especifican normalmente como un *vector unitario* : un vector 3D que tiene una longitud de 1.
- Textura – textura se refiere a las coordenadas de textura: es decir, qué parte de una textura debe aparecer en un vértice determinado. Los valores de la textura son necesarios si la representación de un objeto 3D con una textura. Las coordenadas de textura son coordenadas normalizadas, lo que significa que los valores estarán entre 0 y 1. Trataremos las coordenadas de textura con más detalle más adelante en esta guía.

Nuestro plano actuará como un piso y deseamos aplicar una textura al realizar la representación, por lo que se usará el `VertexPositionTexture` tipo para definir nuestros vértices.

En primer lugar, vamos a agregar a un miembro a la clase Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

A continuación, defina nuestro vértices en `Game1.Initialize`. Tenga en cuenta que no contiene la plantilla proporcionada anteriormente en este artículo hace referencia a un `Game1.Initialize` método, por lo que necesitamos agregar todo el método a `Game1`:

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

Para ayudar a visualizar el aspecto que tendrá nuestra vértices, considere el siguiente diagrama:

![](part2-images/image6.png "Para ayudar a visualizar el aspecto que tendrá los vértices, considere la posibilidad de este diagrama")

Se debe confiar en nuestro diagrama para visualizar los vértices hasta que se termine de implementar nuestro código de representación.

## <a name="adding-drawing-code"></a>Agregar código de dibujo

Ahora que tenemos las posiciones para nuestro geometría definida, podemos escribir el código de representación.

En primer lugar, se deberá definir un `BasicEffect` instancia que va a contener los parámetros de representación, como la posición y la iluminación. Para ello, agregue un `BasicEffect` miembro a la `Game1` clase debajo dónde el `floorVerts` se define el campo:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

A continuación, modifique el `Initialize` método para definir el efecto:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Ahora podemos agregar código para llevar a cabo el dibujo:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
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

Necesitamos llamar `DrawGround` en nuestra `Game1.Draw`:

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

### <a name="view-and-projection-properties"></a>Ver y propiedades de proyección

El `View` y `Projection` propiedades controlan cómo percibimos la escena. Se va a modificar este código más adelante cuando se vuelva a agrega el código de representación del modelo. En concreto, `View` controla la ubicación y la orientación de la cámara, y `Projection` controles el *campo de visión* (que se puede utilizar para hacer zoom de la cámara).

### <a name="techniques-and-passes"></a>Las técnicas y pasadas

Una vez se ha asignado las propiedades en el efecto que se puede realizar la representación real. 

Hemos no cambiará la `CurrentTechnique` propiedad de este tutorial, pero los juegos más avanzados puede tener un único efecto que puede realizar el dibujo de formas diferentes (por ejemplo, cómo se aplica el valor de color). Cada uno de estos modos de representación se puede representar como una técnica que puede asignarse antes de representarlo. Además, cada una de ellas puede requerir varias fases para representar correctamente. Los efectos que necesite varias pasadas si representar objetos visuales complejos como una superficie resplandeciente traseros.

Lo importante a tener en cuenta que es el `foreach` bucle permite el mismo C# código para representar cualquier efecto independientemente de la complejidad de subyacente `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` es donde se representan los vértices. El primer parámetro indica el método cómo nos hemos organizados nuestros vértices. Nos hemos estructurado para que cada triángulo es definido por tres vértices ordenados, por lo que usamos el `PrimitiveType.TriangleList` valor.

El segundo parámetro es la matriz de los vértices que se definió anteriormente.

El tercer parámetro especifica el primer índice se va a dibujar. Puesto que deseamos que nuestra matriz de vértice completo va a representar, se le pasa un valor de 0.

Por último, se especifican cuántas triángulos para representar. La matriz de vértice contiene dos triángulos, por lo tanto, pasar un valor de 2.

## <a name="rendering-with-a-texture"></a>Representación con una textura

En este momento, nuestra aplicación representa un plano blanco (en la perspectiva). A continuación agregaremos una textura a nuestro proyecto al que se utiliza al representar el plano. 

Para simplificar las cosas, vamos a agregar el .png directamente a nuestro proyecto en lugar de utilizar la herramienta de canalización MonoGame. Para ello, descargue [este archivo .png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) a su equipo. Una vez descargado, haga doble clic en el **contenido** carpeta en el panel de solución y seleccione **Agregar > Agregar archivos...**  . Si trabaja en Android, a continuación, esta carpeta se encuentra en la **activos** carpeta en el proyecto específico de Android. Si se encuentra en iOS, a continuación, esta carpeta estará en la raíz del proyecto de iOS. Desplácese hasta la ubicación donde **checkerboard.png** se guarda y seleccione este archivo. Seleccione esta opción para copiar el archivo en el directorio.

A continuación, agregaremos el código para crear nuestro `Texture2D` instancia. En primer lugar, agregue el `Texture2D` como miembro del `Game1` bajo el `BasicEffect` instancia:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificar `Game1.LoadContent` como sigue:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

A continuación, modifique el `DrawGround` método. Es la única modificación necesaria asignar `effect.TextureEnabled` a `true` y establecer el `effect.Texture` a `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

Por último, tenemos que modificar el `Game1.Initialize` coordina el método para asignar una textura en nuestra vértices:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Si se ejecuta el código, podemos ver que nuestro plano muestra ahora un patrón de tablero de ajedrez:

![](part2-images/image8.png "El plano ahora muestra un patrón de tablero")

## <a name="modifying-texture-coordinates"></a>Coordina la modificación de textura

Usa MonoGame normaliza las coordenadas de textura, que son las coordenadas entre 0 y 1 en lugar de entre 0 y el ancho de la textura o el alto. El diagrama siguiente puede ayudar a visualizar las coordenadas normalizadas:

![](part2-images/image9.png "Este diagrama puede ayudar a visualizar las coordenadas normalizadas")

Las coordenadas de textura normalizado permiten cambiar el tamaño de textura sin necesidad de volver a escribir código o volver a crear modelos (como archivos .fbx). Esto es posible porque normalizadas coordenadas representan una relación en lugar de píxeles específicos. Por ejemplo, will (1,1) siempre representan la esquina inferior derecha, independientemente del tamaño de textura.

Podemos cambiar la asignación de coordenadas de textura para usar una única variable para el número de repeticiones:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Esto da como resultado de la textura 20 veces de repetición:

![](part2-images/image10.png "Esto da como resultado de la textura 20 veces de repetición")


## <a name="rendering-vertices-with-models"></a>Representación de vértices con los modelos

Ahora que nuestro plano es representar correctamente, podemos agregar volver a los modelos para ver todo el contenido de forma conjunta. En primer lugar, vamos a volver a agregar el código de modelo para nuestro `Game1.Draw` método (con posiciones modificadas):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

También crearemos un `Vector3` en `Game1` para representar la posición de la cámara. Vamos a agregar un campo en nuestra `checkerboardTexture` declaración:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

A continuación, quite la variable local `cameraPosition` variable desde el `DrawModel` método:

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Quitar del mismo modo local `cameraPosition` variable desde el `DrawGround` método:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

Ahora si se ejecuta el código podemos ver los modelos y cero al mismo tiempo:

![](part2-images/image11.png "Los modelos y el suelo se muestran al mismo tiempo")

Si se modifica la posición de la cámara (como aumentando su valor X, que en este caso mueve la cámara a la izquierda) podemos ver que el valor afecta a cero y los modelos:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Este código genera lo siguiente:

![](part2-images/image3.png "Este código produce esta vista")

## <a name="summary"></a>Resumen

Este tutorial ha mostrado cómo usar una matriz de vértices para realizar la representación personalizada. En este caso, se crea un suelo a cuadros combinando nuestra representación basada en el vértice con una textura y `BasicEffect`, pero el código presentado aquí sirve como base para cualquier representación en 3D. También hemos mostrado que se puede mezclar representación en función de vértice con modelos en la misma escena.

## <a name="related-links"></a>Vínculos relacionados

- [Archivo de tablero de cuadros (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Proyecto completado (ejemplo)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
