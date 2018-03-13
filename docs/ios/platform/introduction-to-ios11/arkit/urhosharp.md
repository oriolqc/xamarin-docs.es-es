---
title: Uso de ARKit con UrhoSharp
ms.topic: article
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/01/2016
ms.openlocfilehash: 94963e92f8372316a982bbe38f1fb653d38b2a3b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="using-arkit-with-urhosharp"></a>Uso de ARKit con UrhoSharp

Con la introducción de [ARKit](https://developer.apple.com/arkit/), Apple ha realizado simple para que los desarrolladores crear aplicaciones de realidad aumentada. Puede realizar un seguimiento de la posición exacta del dispositivo y detectar distintas superficies en todo el mundo ARKit, y, a continuación, resulta depende del desarrollador para mezclar los datos que salen ARKit en el código.

[UrhoSharp](~/graphics-games/urhosharp/index.md) proporciona una API 3D completas y fáciles de usar que puede usar para crear aplicaciones 3D.   Ambos pueden ser fusionen ARKit para proporcionar la información acerca del mundo física y Urho para representar los resultados.

Esta página explica cómo conectar estos dos ámbitos para crear aplicaciones de gran realidad aumentada.


## <a name="the-basics"></a>Conceptos básicos

Lo que queremos hacer es presente contenido 3D en la parte superior del mundo tal como lo ve el iPhone.   La idea es mezclar el contenido procedentes de la cámara del teléfono con el contenido 3D y cuando el usuario del teléfono mueve alrededor de la sala para asegurarse de que el objeto 3D comportarse ya que forma parte de ese espacio; para ello, la delimitación de los objetos en este mundo.

![Ilustración animada de ARKit](urhosharp-images/image1.gif)


Vamos a usar la biblioteca Urho cargar nuestros activos 3D y colocarlos en el mundo y usaremos ARKit para obtener la secuencia de vídeo procedentes de la cámara, así como la ubicación del teléfono en el mundo.   Cuando el usuario se mueve con su teléfono, usaremos los cambios en la ubicación para actualizar el sistema de coordenadas que se está mostrando el motor Urho.

De esta manera, cuando se coloca un objeto en el espacio 3D y el usuario se mueve, la ubicación del objeto 3D refleja el contexto y la ubicación donde se encontraba.

## <a name="setting-up-your-application"></a>Configurar la aplicación

### <a name="ios-application-launch"></a>Inicie la aplicación de iOS

La aplicación de iOS necesaria para crear y ejecutar el contenido 3D, hacer esto mediante la creación de una implementación de una subclase de la [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) y proporcionar el código de programa de instalación invalidando el `Start` método.  Esto es donde la escena se rellena con datos de evento son de controladores de la instalación y así sucesivamente.

Hemos introducido un `Urho.ArkitApp` clase que cree subclases `Urho.Application` y, en su `Start` método hace el trabajo pesado.   Todo lo que necesita para realizar su Urho existente es de la aplicación cambie la clase base para que sea de tipo `Urho.ArkitApp` y tiene una aplicación que se ejecutará la escena urho en todo el mundo.

### <a name="the-arkitapp-class"></a>La clase ArkitApp

Esta clase proporciona un conjunto de valores predeterminados cómodos, tanto una escena con algunos objetos de clave, así como el procesamiento de eventos de ARKit que se entregan por el sistema operativo.

El programa de instalación tiene lugar en el `Start` método virtual.   Al invalidar este método en la subclase, necesario para asegurarse de que encadenar a tu padre o tutor utilizando `base.Start()` en su propia implementación.

El `Start` método configura la escena, ventanilla, cámara y una luz direccional y superficies de estos archivos como propiedades públicas:

- un [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) para contener los objetos
- una dirección [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) con sombras y cuya ubicación está disponible a través de la `LightNode` propiedad
- un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) cuyos componentes se actualizan cuando ARKit envía una actualización a la aplicación y
- un [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) se muestran los resultados.


### <a name="your-code"></a>El código

A continuación, se necesita para crear una subclase la `ArkitApp` clase e invalidar el `Start` método.   Lo primero que debe hacer el método es cadena hasta el `ArkitApp.Start` mediante una llamada a `base.Start()`.  Después de eso, puede usar cualquiera de la configuración de propiedades por ArkitApp para agregar los objetos a la escena, personalizar las luces, sombras o eventos que desea controlar.

El ejemplo de ARKit/UrhoSharp carga un objeto animado con texturas y reproduce la animación, con la implementación siguiente:

    ```csharp
    public class MutantDemo : ArkitApp
    {
        [Preserve]
        public MutantDemo(ApplicationOptions opts) : base(opts) { }

        Node mutantNode;

        protected override void Start()
        {
            base.Start ();

            // Mutant
            mutantNode = Scene.CreateChild();
            mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
            mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
            mutantNode.SetScale(0.5f);

            var mutant = mutantNode.CreateComponent<AnimatedModel>();
            mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
            mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

            var animation = mutantNode.CreateComponent<AnimationController>();
            animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
        }
    }
    ```

Y eso es todo lo que tienes que hacer en este momento para que el contenido 3D que se muestra en realidad aumentada.

Urho usa formatos personalizados para los modelos 3D y las animaciones, por lo que necesita exportar los activos en este formato.   También puede usar herramientas como el [Urho3D Blender complemento](https://github.com/reattiva/Urho3D-Blender) y [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) que puede convertir estos recursos de formatos populares como DBX, DAE, OBJ, Blend, máxima 3D en el formato requerido por Urho.

Para más información acerca de cómo crear aplicaciones 3D mediante Urho, visite la [Introducción a UrhoSharp](~/graphics-games/urhosharp/introduction.md) guía.

## <a name="arkitapp-in-depth"></a>ArkitApp en profundidad

> [!NOTE]
> Esta sección está destinada a desarrolladores que desee personalizar la experiencia predeterminada de UrhoSharp y ARKit o desean obtener una visión más detallada sobre el funcionamiento de la integración.   No es necesario leer esta sección.

La API de ARKit es bastante sencilla, crear y configurar un [ARSession](https://developer.apple.com/documentation/arkit/arsession) objeto que, a continuación, empiece a entregar [ARFrame](https://developer.apple.com/documentation/arkit/arframe) objetos.   Estos planos contienen tanto la imagen capturada por la cámara, así como la posición del mundo real estimada del dispositivo.

Agregaremos puede crear las imágenes de la cámara que se entregó con nosotros con nuestro contenido 3D y ajustar el UrhoSharp para que coincida con las posibilidades en la ubicación del dispositivo y la posición de la cámara.

El diagrama siguiente muestra lo que tiene lugar la `ArkitApp` clase:

[![Diagrama de clases y las pantallas de la ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Los marcos de representación

La idea es simple, puede combinar el vídeo que salen de la cámara con nuestro gráficos 3D para generar la imagen combinada.     Se obtendrá una serie de estas imágenes capturadas en secuencia, y esta entrada se se mezcle con la escena Urho.

La manera más sencilla de hacerlo consiste en Insertar un [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) en el método main [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Se trata de un conjunto de comandos que se realizan para dibujar un solo fotograma.  Este comando rellenará la ventanilla con cualquier textura que pasamos a él.    Configurar esto en el primer fotograma que es el proceso y la definición real se realiza en th **ARRenderPath.xml** archivo que se carga en este momento.

Sin embargo, nos estamos enfrentan dos problemas para mezclar estos dos mundos juntos:


1. En iOS, las texturas de GPU debe tener una resolución que es una potencia de dos, pero los fotogramas que se obtendrá de la cámara no tiene resolución que son una potencia de dos, por ejemplo: 1280 x 720.
2. Los marcos están codificados en [YUV](https://en.wikipedia.org/wiki/YUV) formato, representado por dos imágenes - luminancia y la intensidad de color.

Los marcos YUV vienen en dos soluciones diferentes.  una imagen de 1280 x 720 que representa luminancia (básicamente una imagen en escala de grises) y mucho más pequeños 640 x 360 para el componente cromática:

![Imagen que muestra la combinación Y y los componentes de UV](urhosharp-images/image3.png)


Para dibujar una imagen completa de color con OpenGL ES tenemos un pequeño sombreador que toma luminancia (componente de Y) y cromática (UV planos) desde las ranuras de textura de escritura.  En UrhoSharp que tienen nombres - "sDiffMap" y "sNormalMap" y convierten en formato RGB:

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

Para representar la textura que no tiene una potencia de dos resolución tenemos que definir Texture2D con los siguientes parámetros:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Por lo tanto se pueden representar imágenes capturadas como fondo y representar cualquier escena por encima de él mutante terror similar.

### <a name="adjusting-the-camera"></a>Ajuste de la cámara

El `ARFrame` objetos también contienen la posición de dispositivo estimado.  Se ahora es necesario mover la cámara juego ARFrame: antes de ARKit no era un hologramas importante para realizar un seguimiento de orientación del dispositivo (rotación, rotación y rotación) y representar un holograma anclado en la parte superior del vídeo - pero si mueve un poco el dispositivo - se desviará.

Esto ocurre porque no se puede realizar el seguimiento de movimientos sensores integrados como giroscopio, pueden sólo la aceleración.  Análisis de ARKit cada característica marco y extrae puntos para realizar un seguimiento y, por tanto, es capaz de enviar un tipo de objeto transformación de matriz que contiene los datos de movimiento y la rotación.

Por ejemplo, se trata cómo podemos obtener posición actual:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Usamos `-row.Z` porque ARKit utiliza un sistema de coordenadas diestro.


### <a name="plane-detection"></a>Detección de plano

ARKit es capaz de detectar planos horizontales y esta capacidad le permite interactuar con el mundo real, por ejemplo, podemos colocamos el mutante en una tabla real o una planta. La manera más sencilla de hacerlo es usar HitTest (método) (raycasting). Convierte las coordenadas de pantalla (0,5; 0,5 es el centro) en las coordenadas del mundo real (0; 0; 0 es la primera posición del fotograma).

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

Ahora podemos aplicar al mutante sobre una superficie horizontal función de dónde aparecen en la pantalla del dispositivo que se pulse:

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![Ilustración animada cambiar planos mientras se mueve de la vista](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Iluminación realista

Según las condiciones de iluminación del mundo real, la escena virtual debería ser más clara u oscura que mejor se adapten a su entorno. ARFrame contiene una propiedad LightEstimate que podemos usar para ajustar la luz ambiental Urho, esto se realiza la siguiente manera:


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>Más allá de iOS - HoloLens

UrhoSharp [se ejecuta en todos los sistemas operativos principales](~/graphics-games/urhosharp/platform/index.md), por lo que puede reutilizar el código existente en otra parte.

HoloLens es una de las plataformas más interesantes que se ejecuta.   Esto significa que puede cambiar fácilmente entre iOS y HoloLens para compilar aplicaciones de realidad aumentada maravillas mediante UrhoSharp.

Puede encontrar el origen de MutantDemo en [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Vínculos relacionados

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (con UrhoSharp) (ejemplo)](https://github.com/EgorBo/ARKitXamarinDemo)
