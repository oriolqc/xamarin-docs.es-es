---
title: Uso de ARKit con UrhoSharp en Xamarin.iOS
description: Este documento describe cómo configurar una aplicación de ARKit en Xamarin.iOS, a continuación, examina cómo se representan marcos, cómo ajustar la cámara, cómo detectar planos, cómo trabajar con la iluminación y mucho más. También se explica UrhoSharp y escribir código para HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/01/2017
ms.openlocfilehash: 728082eb27684c2176feb2038b7948986ce6a694
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351696"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Uso de ARKit con UrhoSharp en Xamarin.iOS

Con la introducción de [ARKit](https://developer.apple.com/arkit/), Apple ha simplificado el proceso para los desarrolladores crear aplicaciones de realidad aumentada. Puede realizar un seguimiento de la posición exacta del dispositivo y detectar distintas superficies en el mundo ARKit y, a continuación, resulta depende del desarrollador para combinar los datos que salen de ARKit en el código.

[UrhoSharp](~/graphics-games/urhosharp/index.md) proporciona una API 3D completa y fáciles de usar que puede usar para crear aplicaciones de 3D.   Ambos de estos pueden ser fusionados, ARKit para proporcionar la información acerca del mundo física y Urho para representar los resultados.

Esta página explica cómo conectar estos dos mundos juntos para crear aplicaciones de realidad aumentada excelente.


## <a name="the-basics"></a>Conceptos básicos

Lo que queremos hacer es presentan contenido 3D en la parte superior del mundo tal como lo ve el iPhone.   La idea es combinar el contenido procedente de la cámara del teléfono con el contenido 3D y cuando el usuario del teléfono se mueve en torno a la sala de reuniones para asegurarse de que el objeto 3D se comportan como que forma parte de esa sala: Esto se hace mediante la delimitación de los objetos en este mundo.

![Ilustración animada de ARKit](urhosharp-images/image1.gif)


Vamos a usar la biblioteca Urho cargar nuestros activos 3D y colocarlos en el mundo, y vamos a usar ARKit para obtener la secuencia de vídeo procedentes de la cámara, así como la ubicación del teléfono en el mundo.   Cuando el usuario se mueve con su teléfono, usaremos los cambios en la ubicación para actualizar el sistema de coordenadas que el motor de Urho está mostrando.

De este modo, cuando se coloca un objeto en el espacio 3D y el usuario se mueve, la ubicación del objeto 3D refleja el contexto y la ubicación donde se colocó.

## <a name="setting-up-your-application"></a>Configuración de la aplicación

### <a name="ios-application-launch"></a>Inicie la aplicación de iOS

La aplicación de iOS se necesita crear e iniciar el contenido 3D, hacerlo mediante la creación de una implementación de una subclase de la [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) y proporcione el código de programa de instalación invalidando el `Start` método.  Esto es donde la escena se rellena con datos de evento son de controladores de instalación y así sucesivamente.

Hemos introducido una `Urho.ArkitApp` que cree subclases de clases `Urho.Application` y en su `Start` método hace el trabajo pesado.   Todo lo que necesita para realizar su Urho existente es de la aplicación cambie la clase base para que sea de tipo `Urho.ArkitApp` y tiene una aplicación que se ejecutará su urho escena en el mundo.

### <a name="the-arkitapp-class"></a>La clase ArkitApp

Esta clase proporciona un conjunto de valores predeterminados convenientes, ambas una escena con algunos objetos de clave, así como el procesamiento de eventos de ARKit que se entregan por el sistema operativo.

El programa de instalación realiza el `Start` método virtual.   Cuando se reemplaza este método en la subclase, necesita para asegurarse de que encadenar a tus padres mediante el uso de `base.Start()` en su propia implementación.

El `Start` método configura la escena, ventanilla, cámara y una luz direccional y Emerge como propiedades públicas:

- un [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) para contener los objetos,
- un direccional [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) con sombras y cuya ubicación está disponible a través de la `LightNode` propiedad
- un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) cuyos componentes se actualizan cuando ARKit ofrece una actualización a la aplicación y
- un [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) mostrar los resultados.


### <a name="your-code"></a>El código

A continuación, deberá subclase la `ArkitApp` clase e invalidar el `Start` método.   Lo primero que debe hacer el método es cadena hasta el `ArkitApp.Start` mediante una llamada a `base.Start()`.  Después de eso, puede usar cualquiera de la configuración de propiedades por ArkitApp para agregar los objetos a la escena, personalizar las luces, sombras o eventos que desea administrar.

El ejemplo de ARKit/UrhoSharp carga un objeto animado con texturas y reproduce la animación, con la siguiente implementación:

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

Y eso es todo lo que tendrá que hacer en este momento para que el contenido 3D que se muestra en realidad aumentada.

Urho usa formatos personalizados para los modelos 3D y animaciones, por lo que deberá exportar sus activos en este formato.   También puede usar herramientas como el [Urho3D Blender complemento](https://github.com/reattiva/Urho3D-Blender) y [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) que puede convertir estos recursos de formatos populares como DBX, DAE, OBJ, Blend, máxima 3D en el formato requerido por Urho.

Para más información sobre cómo crear aplicaciones 3D mediante Urho, visite la [Introducción a UrhoSharp](~/graphics-games/urhosharp/introduction.md) guía.

## <a name="arkitapp-in-depth"></a>ArkitApp en profundidad

> [!NOTE]
> En esta sección está destinada a desarrolladores que desean personalizar la experiencia predeterminada de UrhoSharp y ARKit o desean obtener una información más detallada sobre cómo funciona la integración.   No es necesario leer esta sección.

La API de ARKit es bastante simple, crear y configurar un [ARSession](https://developer.apple.com/documentation/arkit/arsession) objeto que, a continuación, empezar a entregar [ARFrame](https://developer.apple.com/documentation/arkit/arframe) objetos.   Éstos contienen tanto la imagen capturada por la cámara, así como la posición del mundo real estimada del dispositivo.

Se se componer las imágenes de la cámara que entrega nos con nuestros contenidos 3D y ajustar la cámara a través de UrhoSharp para que coincida con las posibilidades en la ubicación del dispositivo y la posición.

El siguiente diagrama muestra lo que tiene lugar en el `ArkitApp` clase:

[![Diagrama de clases y las pantallas de la ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Los fotogramas de representación

La idea es sencilla, puede combinar el vídeo que salen de la cámara con nuestros gráficos en 3D para generar la imagen combinada.     Se obtendrá una serie de estas imágenes capturadas en secuencia, y esta entrada se se mezcle con la escena Urho.

La manera más sencilla de hacerlo consiste en Insertar un [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) en el método main [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Se trata de un conjunto de comandos que se realizan para dibujar un solo fotograma.  Este comando rellenará la ventanilla con cualquier textura que se pasa a él.    Configuramos esto en el primer fotograma es proceso, y la definición real se realiza en th **ARRenderPath.xml** archivo cargado en este momento.

Sin embargo, nos estamos enfrentamos con dos problemas al combinar estos dos mundos de ellos:


1. En iOS, las texturas de GPU debe tener una resolución que es una potencia de dos, pero los fotogramas que obtenemos de la cámara no tiene resolución que sean la potencia de dos, por ejemplo: 1280 x 720.
2. Los fotogramas se codifican en [YUV](https://en.wikipedia.org/wiki/YUV) formato, representado por dos imágenes: luminancia y la intensidad de color.

Los marcos YUV vienen en dos diferentes resoluciones.  una imagen de 1280 x 720 que representa la luminancia (básicamente, una imagen en escala de grises) y mucho más pequeños 640 x 360 para el componente cromáticas:

![Imagen que muestra la combinación Y y los componentes de UV](urhosharp-images/image3.png)


Para dibujar una imagen coloreada completa con OpenGL ES tenemos que escribir a un pequeño sombreador que toma la luminancia (Y componente) y cromáticas (UV planos) a las ranuras de textura.  En UrhoSharp deben tengan nombres - "sDiffMap" y "sNormalMap" y conversión en formato RGB:

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

Para representar la textura que no tiene una potencia de dos resolución, tenemos que definir Texture2D con los siguientes parámetros:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Por lo tanto es posible representar las imágenes capturadas como un fondo y representar cualquier escena por encima de él que constituía mutantes.

### <a name="adjusting-the-camera"></a>Ajuste de la cámara

El `ARFrame` objetos también contienen la posición de dispositivo estimado.  Ahora es necesario mover la cámara juego ARFrame: antes de ARKit no era un hologramas gran cosa pista de orientación del dispositivo (rollo, cabeceo y rotación) y un holograma anclado en la parte superior del vídeo de presentación -, pero si quedan un poco el dispositivo - se desviará.

Esto ocurre porque no se puede realizar el seguimiento de movimientos sensores integrados como giroscopio, pueden que sólo la aceleración.  ARKit análisis cada característica de marco y extrae puntos para realizar un seguimiento y, por tanto, puede enviarnos una lista precisa de transformación de matriz que contiene los datos de movimiento y la rotación.

Por ejemplo, se trata cómo nos podemos obtener la posición actual:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Usamos `-row.Z` como ARKit utiliza un sistema de coordenadas para diestros.


### <a name="plane-detection"></a>Detección de plano

Es capaz de detectar planos horizontales ARKit y esta capacidad le permite interactuar con el mundo real, por ejemplo, podemos colocamos el mutantes en una tabla real o una planta. La manera más sencilla de hacerlo es usar el método HitTest (detalles). Convierte las coordenadas de pantalla (0,5; 0,5 es el centro) en las coordenadas reales (0; 0; 0 es la ubicación del primer fotograma).

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

Ahora podemos aplicar al mutantes en una superficie horizontal dependiendo de dónde en la pantalla del dispositivo, pulse:

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

Dependiendo de las condiciones de iluminación del mundo real, la escena virtual debe ser más claras u oscuras para que coincidan mejor con su entorno. ARFrame contiene una propiedad LightEstimate que podemos usar para ajustar la luz ambiente Urho, esto se hace así:


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>Más allá de iOS: HoloLens

UrhoSharp [se ejecuta en todos los sistemas operativos principales](~/graphics-games/urhosharp/platform/index.md), por lo que puede reutilizar el código existente en otro lugar.

HoloLens es una de las plataformas más emocionantes que se ejecuta.   Esto significa que puede cambiar fácilmente entre iOS y HoloLens para crear increíbles aplicaciones de realidad aumentada, uso de UrhoSharp.

Puede encontrar el origen MutantDemo en [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Vínculos relacionados

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (con UrhoSharp) (ejemplo)](https://github.com/EgorBo/ARKitXamarinDemo)
