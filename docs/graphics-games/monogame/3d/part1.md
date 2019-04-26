---
title: Uso de la clase de modelo
description: La clase del modelo simplifica en gran medida la representación de objetos 3D complejos en comparación con el método tradicional de representación de gráficos 3D. Los objetos del modelo se crean a partir de archivos de contenido, lo que permite una integración fácil de contenido con ningún código personalizado.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d9c73dfcee6321ecb314ca229db407c6d0438977
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342590"
---
# <a name="using-the-model-class"></a>Uso de la clase de modelo

_La clase del modelo simplifica en gran medida la representación de objetos 3D complejos en comparación con el método tradicional de representación de gráficos 3D. Los objetos del modelo se crean a partir de archivos de contenido, lo que permite una integración fácil de contenido con ningún código personalizado._

La API de MonoGame incluye un `Model` clase que se puede usar para almacenar los datos cargados desde un archivo de contenido y para realizar la representación. Archivos de modelo pueden ser muy sencillas (por ejemplo, un triángulo color sólido) o pueden incluir información para el procesamiento complejo, incluyendo textura e iluminación.

Este tutorial usa [un modelo 3D de un robot](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) y abarca lo siguiente:

- Iniciar un nuevo proyecto de juego
- Creación de XNBs para el modelo y su textura
- Incluir el XNBs en el proyecto de juego
- Dibujo de un modelo 3D
- Dibujo de varios modelos

Cuando termine, nuestro proyecto aparecerán como sigue:

![Final de ejemplo que muestra seis robots](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Crear un proyecto de juego vacío

Deberá configurar un proyecto de juego MonoGame3D llamado por primera vez. Para obtener información sobre cómo crear un nuevo proyecto MonoGame, consulte [en este tutorial sobre cómo crear un proyecto de Monogame de plataforma cruzada](~/graphics-games/monogame/introduction/part1.md).

Antes de continuar debemos comprobar que el proyecto se abre y se implementa correctamente. Una vez implementado, deberíamos ver una pantalla azul vacía:

![Pantalla de juego azul en blanco](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>Incluir el XNBs en el proyecto de juego

El formato de archivo .xnb es una extensión estándar para el contenido compilado (contenido que ha creado el [MonoGame canalización herramienta](http://www.monogame.net/documentation/?page=Pipeline)). Todo el contenido generado tiene un archivo de origen (que es un archivo .fbx en el caso de nuestro modelo) y un archivo de destino (un archivo .xnb). El formato .fbx es un formato común de modelo 3D que se puede crear aplicaciones como [Maya](http://www.autodesk.com/products/maya/overview) y [Blender](http://www.blender.org/). 

La `Model` clase puede crearse mediante la carga de un archivo .xnb desde un disco que contiene datos de geometría 3D.   Este archivo .xnb se crea a través de un proyecto de contenido. Plantillas de Monogame incluyen automáticamente un proyecto de contenido (con la extensión .mgcp) en nuestra carpeta de contenido. Para obtener una explicación detallada sobre la herramienta de canalización MonoGame, consulte el [Guía de la canalización de contenido](~/graphics-games/cocossharp/content-pipeline/index.md).

En esta guía a través de la canalización MonoGame omitiremos herramienta y usará el. Archivos XNB incluidos aquí. Tenga en cuenta que el. Archivos XNB difieren por plataforma, así que asegúrese de usar el conjunto correcto de los archivos XNB para cualquier plataforma que está trabajando.

Se le descomprima la [Content.zip archivo](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) por lo que podemos usar los archivos de contenido .xnb en nuestro juego. Si trabaja en un proyecto de Android, haga doble clic en el **activos** carpeta en el **WalkingGame.Android** proyecto. Si trabaja en un proyecto de iOS, haga doble clic en el **WalkingGame.iOS** proyecto. Seleccione **Agregar -> Agregar archivos...**  y seleccione ambos archivos .xnb en la carpeta de la plataforma que está trabajando.

Los dos archivos deben ser parte de nuestro proyecto ahora:

![Carpeta de contenido de explorador de soluciones con archivos xnb](part1-images/xnbsinxs.png)

Visual Studio para Mac no puede establecer automáticamente la acción de compilación para XNBs recién agregados. Para iOS, haga doble clic en cada uno de los archivos y seleccione **acción de compilación -> BundleResource**. Para Android, haga doble clic en cada uno de los archivos y seleccione **acción de compilación -> AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Representación de un modelo 3D

Es el último paso necesario para ver el modelo en la pantalla Agregar la carga y el código de dibujo. En concreto, se va a realizar lo siguiente:

- Definir un `Model` instancia en nuestra `Game1` clase
- Cargando el `Model` en la instancia `Game1.LoadContent`
- Dibujar el `Model` en la instancia `Game1.Draw`

Reemplace el `Game1.cs` archivo de código (que se encuentra en la **WalkingGame** PCL) con los siguientes:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Si ejecutamos este código que veremos el modelo en la pantalla:

![Modelo que se muestra en la pantalla](part1-images/image8.png "si se ejecuta este código, el modelo se mostrará en la pantalla")

### <a name="model-class"></a>Clase de modelo

La `Model` clase es la clase principal para realizar la representación en 3D de los archivos de contenido (como archivos .fbx). Contiene toda la información necesaria para la representación, incluida la geometría 3D, las referencias de textura y `BasicEffect` instancias que controlan los valores de posicionamiento, la iluminación y la cámara.

La `Model` clase en sí no tiene variables para el posicionamiento puesto que una instancia del modelo solo se puede representar en varias ubicaciones, como veremos más adelante en esta guía directamente.

Cada `Model` se compone de uno o varios `ModelMesh` instancias, que se exponen a través de la `Meshes` propiedad. Aunque es posible que consideremos una `Model` como un solo juego de objeto (como un robot o un automóvil), cada uno `ModelMesh` se pueden dibujar con diferentes `BasicEffect` valores. Por ejemplo, las partes individuales de malla pueden representar las patas de un robot o las ruedas en un automóvil, y podemos asignamos la `BasicEffect` mover los valores para hacer que el número de ruedas o las piernas. 

### <a name="basiceffect-class"></a>Clase BasicEffect

La `BasicEffect` clase proporciona propiedades para controlar las opciones de representación. La primera modificación que realice en el `BasicEffect` consiste en llamar a la `EnableDefaultLighting` método. Como el nombre implica, esto permite una iluminación, de forma predeterminada, que es muy útil para comprobar que un `Model` aparece en el juego según lo previsto. Si se marque como comentario el `EnableDefaultLighting` llamar, a continuación, veremos el modelo representado simplemente su textura, pero con ningún sombreado o especular resplandor:

```csharp
//effect.EnableDefaultLighting ();
```

![El modelo representado simplemente su textura, pero con ningún sombreado o especular resplandor](part1-images/image9.png "el modelo representado simplemente su textura, pero con ningún sombreado o especular resplandor")

El `World` propiedad puede usarse para ajustar la posición, rotación y escala del modelo. El código anterior usa el `Matrix.Identity` valor, lo que significa que el `Model` se representará en juego exactamente como se especifica en el archivo .fbx. Se tratarán las matrices y las coordenadas 3D con más detalle en [parte 3](~/graphics-games/monogame/3d/part3.md), pero como ejemplo, se puede cambiar la posición de la `Model` cambiando el `World` propiedad tal como se indica a continuación:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Este código produce el objeto moviéndose por 3 unidades universales:

![Este código produce el objeto moviéndose por 3 unidades universales](part1-images/image10.png "este código produce el objeto moviéndose por 3 unidades universales")

Las dos propiedades finales asignadas en el `BasicEffect` son `View` y `Projection`. Se tratarán las cámaras 3D en [parte 3](~/graphics-games/monogame/3d/part3.md), pero como por ejemplo, podemos modificar la posición de la cámara cambiando local `cameraPosition` variable:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Podemos ver la cámara ha movido más atrás, lo que produce el `Model` que aparecen más pequeños, debido a la perspectiva:

![La cámara ha movido más atrás, lo que resulta en el modelo que aparecen más pequeños, debido a la perspectiva](part1-images/image11.png "la cámara ha movido más atrás, lo que resulta en el modelo que aparecen más pequeños, debido a la perspectiva")

## <a name="rendering-multiple-models"></a>Varios modelos de representación

Como se mencionó anteriormente, una sola `Model` se pueden dibujar varias veces. Para facilitar esta tarea se pasará el `Model` código de dibujo en su propio método que toma el deseado `Model` posición como un parámetro. Una vez finalizado, nuestra `Draw` y `DrawModel` métodos tendrá el siguiente aspecto:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
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
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

Esto da como resultado el robot de modelo que se va a dibujar seis veces:

![Esto da como resultado el robot de modelo que se va a dibujar seis veces](part1-images/image1.png "Esto da como resultado el robot de modelo que se va a dibujar seis veces")

## <a name="summary"></a>Resumen

En este tutorial introdujo del MonoGame `Model` clase. Trata de convertir un archivo .fbx en un .xnb, que a su vez se puede cargar en un `Model` clase. También se muestra cómo las modificaciones en un `BasicEffect` puede afectar a instancia `Model` dibujo.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia del modelo de MonoGame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Proyecto completado (ejemplo)](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
