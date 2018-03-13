---
title: Color amplia
description: "Este artículo tratan de color amplia y cómo se puede usar en una aplicación Xamarin.iOS o Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 95098cd5c97ccc8357531feb79e55600f53a4be5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="wide-color"></a>Color amplia

_Este artículo tratan de color amplia y cómo se puede usar en una aplicación Xamarin.iOS o Xamarin.Mac._

iOS 10 y macOS Sierra mejora la compatibilidad con formatos de píxel de rango extendido y los espacios de la amplia gama de colores en todo el sistema incluidos los marcos, como los gráficos esenciales, imagen Core, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas de color amplia adicional se ve facilitado por proporcionar este comportamiento en toda la pila completa de gráficos.

## <a name="about-wide-color"></a>Acerca de Color amplia

Como se mencionó anteriormente, iOS 10 y macOS Sierra mejora la compatibilidad con formatos de píxel de rango extendido y los espacios de la amplia gama de colores en todo el sistema, incluidos los marcos, como los gráficos esenciales, imagen Core, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas de color amplia adicional se ve facilitado por proporcionar este comportamiento en toda la pila completa de gráficos.

En el 90 Apple crea ColorSync para controlar el color que se está procesando en el equipo Mac. También ayudó a encontrar International Color Consortium (ICC) para crear y promover un conjunto de estándares para controlar el color en el hardware del equipo. Trabajo de Apple con el ICC se incluyó en ColorSync y se creó en el núcleo de OS X (que ahora se denomina macOS).

Apple ha sido también a la vanguardia de la tecnología de presentación con hardware, como la Retina mostrar, la nueva P3 mostrar y Mostrar espacio de colores P3 (publicada en el iMac en 2015) y muestra el TrueTone de los profesionales de TI iPad, iPhone 7 e iPhone 7 más.

Con Color amplia en macOS Sierra y iOS 10, Apple está cambiando la forma en que iOS y Mac OS controlan color para aprovechar al máximo de estas nuevas tecnologías de presentación.

## <a name="core-color-concepts"></a>Conceptos básicos de Color

Los siguientes conceptos de color principal deben cubrir antes de realizar un examen más profundo de color en iOS y Mac OS:

### <a name="color-space"></a>Espacio de colores

Un espacio de colores es un entorno en el que se pueden representados y comparar colores. Puede ser un espacio de dimensiones de uno a cuatro definido por la intensidad de sus componentes de color. 

[![](wide-color-images/color00.png "Un espacio de colores")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>Canales de color

También se pueden hacer referencia a los componentes de color como canales de Color. Algunas representaciones familiarizados sería el RGB espacios, espacios de color gris, CMYK, espacios o espacios independientes del dispositivo. 

[![](wide-color-images/color02.png "Los componentes de color también se conoce como canales de Color")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>Elementos de color

Los elementos de color proporcionan el sistema de coordenadas que se utiliza para comparar y colores de proceso. Colores primarios suelen pertenecen a más intensa versión del color especificado que se pueden generar en el canal de Color.

[![](wide-color-images/color01.png "Los elementos de color proporcionan el sistema de coordenadas que se utiliza para comparar y colores de proceso")](wide-color-images/color01.png#lightbox)

En el caso del espacio de colores RGB representado por encima, el Color de los elementos están dónde el `1.0` están delimitadas coordenadas (como `[1.0, 0.0, 0.0]` para el rojo).

### <a name="color-gamut"></a>Gama de colores

Gama de colores hace referencia a todos los colores que se pueden definir como una combinación de los canales de Color individuales dentro de un espacio de colores determinada.

[![](wide-color-images/color03.png "Ejemplo de la gama de color")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>¿Qué es el Color amplia

Antes de tratar el tema de Color amplia, debe tenerse una discusión sobre el estándar actual para el color, el espacio de Color RGB estándar (sRGB). El espacio de Color más usado en informática de hoy en día y el espacio de color predeterminado para iOS y Mac OS.

El espacio de Color sRGB tiene las siguientes propiedades:

- Se basa en el estándar de ITU-R BT.709.
- Tiene un aproximado Gamma de 2.2.
- Representa las condiciones de iluminación típica (D65).

Puesto que sRGB ampliamente que utilizado en la industria, un desarrollador puede realizar algunas suposiciones que es el color especificado se representarán fielmente en cualquier dispositivo que aparece. Sin embargo, esto no siempre sería el caso. Además, existen varios colores que no caben en el espacio de Color sRGB y, en consecuencia, no se puede representar en él.

Por ejemplo, muchos tejidos están diseñadas con los subprocesos que utilizan muchas tintas y tintes acumulando fuera sRGB. También se crean muchos productos de una persona que se encuentra en su vida diaria con colores brillantes y colores vivos que quedan fuera del espacio de Color sRGB. Algunos de los ejemplos más contundente de colores que no pueden representarse en sRGB son cosas por naturaleza como puestas de sol, otoño, flores exóticas y aguas tropicales.

Los usuarios que han sido capturar imágenes digitales en el formato pueden tener imágenes en sus dispositivos que contienen todos estos datos de color, aunque no puede representarse correctamente en el espacio de Color sRGB y, en consecuencia, no se muestran correctamente en la pantalla.

### <a name="the-display-p3-color-space"></a>El espacio de Color de pantalla P3

2015, Apple lanzado nuevos productos (iMac y iPad Pro 9.7") que proporcionan el nuevo espacio de Color de P3 de presentación para controlar los problemas creados por el espacio de Color sRGB.

[![](wide-color-images/color04.png "El nuevo espacio de Color de pantalla P3")](wide-color-images/color04.png#lightbox)

El espacio de Color de pantalla P3 tiene las siguientes propiedades:

- Admite un espacio de color amplia para plataformas de hardware moderno.
- Se basa en el estándar de SMPTE DCI-P3. P3 DCI se diseñó para los proyectores digitales pero fue modificado por Apple para admitir a monitores.
- Tiene un aproximado Gamma de 2.2.
- Representa las condiciones de iluminación típica (D65).

Según Apple, los usuarios están cambiando sus flujos de trabajo a las plataformas móviles. Para resolver los problemas de presentación de color presentados sRGB en los dispositivos móviles (iPad los profesionales de TI), profesionales, requerida, incluido algo más que una pantalla de color amplia. Una de las soluciones era actualizar la calibración de fábrica, por lo que cada dispositivo se ha calibrado en la fábrica que garantice que desde un dispositivo a otro, presentación en color precisos y coherentes.

Otra solución es la inclusión completa, todo el sistema de administración de color que Apple ha incorporado en iOS 10 y macOS Sierra. 

### <a name="the-extended-range-srgb-color-space"></a>El espacio de Color sRGB de rango extendido

Nuevo iOS 10 color de todo el sistema de administración tiene en cuenta para todas las aplicaciones existentes de iOS compiladas e ajustar para sRGB. Se diseñó para asegurarse de que no afecte a cualquier rendimiento de representación o una aplicación de color de estas aplicaciones.

Para controlar esta situación, Apple ha incluido el espacio de Color del rango extendido sRGB en iOS 10 (y macOS Sierra).

El espacio de Color sRGB de rango extendido tiene las siguientes propiedades:

- Tiene todos la misma sRGB primarios.
- Tiene un aproximado Gamma de 2.2.
- Representa las condiciones de iluminación típica (D65).
- Es compatible con los valores negativos y los valores de mayor que uno (1).

Por lo cual permite los valores menores que cero y mayor que uno, el sRGB de rango extendido que no solo permite el espacio de Color para las aplicaciones existentes a los colores presentes en sRGB sin aciertos de rendimiento o distorsión, pero permite el espacio de colores representar cualquier color dentro visibles espectro. Todo esto se logra mientras mantiene los mismos puntos de anclaje como el espacio de Color sRGB.

### <a name="extended-range-srgb-in-action"></a>SRGB de rango extendido en acción

Para ver cómo funcionan los valores fuera de cero y uno en el espacio de Color sRGB de rango extendido, considere el ejemplo siguiente de la de la saturación máxima roja disponible en el espacio de Color de pantalla P3:

[![](wide-color-images/color05.png "Cómo funcionan los valores fuera de cero y uno en el espacio de Color sRGB de rango extendido")](wide-color-images/color05.png#lightbox)

En la pantalla P3, este color se representará como `[1.0, 0.0, 0.0]` y en sRGB de rango extendido sería `[1.358, -0.074, -0.012]`. Porque están llenos sRGB valores contenidos dentro de P3 de presentación y los valores de presentación P3 sentar "fuera" de los intervalos de sRGB.

Para el hardware físico que permite a los valores de píxel de extrema positivo hacia extremos valores negativos, puede mostrar cualquier color disponible en el espectro visible y estos valores se pueden representar en el espacio de Color sRGB de rango extendido.

### <a name="device-pixel-formats"></a>Formatos de píxel de dispositivo 

El espacio de colores ha sido en gran medida de sRGB estandarizar sobre el uso de un formato de píxel de 8 bits, ya que es de 8 bits por canal de color principalmente suficiente para describir los colores en sRGB. Esto no es perfecta pero suficientemente bueno y proporciona un buen equilibrio entre el uso de memoria y procesador para mostrar imágenes.

Puesto que mostrar P3 puede representar coordenadas de color fuera del espacio de colores sRGB, requiere 16 bits por canal de color para que represente correctamente los colores con el espacio de Color sRGB de rango extendido.

## <a name="system-wide-wide-color-support"></a>Compatibilidad de Color amplia de todo el sistema

Para satisfacer por completo color amplia y la amplia gama de colores dentro de iOS 10 y macOS Sierra, Apple amplió los siguientes marcos para aprovechar el espacio de Color sRGB de rango extendido y la presentación P3:

- UIKit (solo para iOS)
- SceneKit
- Gráficos de núcleo
- ImageIO
- Imagen del núcleo
- WebKit
- SpriteKit
- Animación de núcleo
- AppKit (para macOS solo)

Además, ha mejorado la compatibilidad de rango extendido sRGB espacio de colores de pantalla de Retina y mostrar P3 muestran.

Color amplia es compatible y puede utilizarse en los siguientes tipos de contenido de aplicación:

- Recursos de imagen estática incluidos en el paquete de aplicación.
- Documento y red basadas en recursos de imagen.
- Multimedia avanzada como Live fotografías o imágenes en formato RAW.
- Imágenes de textura de sombreador de gráficos 3D.

## <a name="solving-the-color-problem"></a>Para resolver el problema de Color

El contenido que se muestra en una aplicación puede proceder de una amplia gama de orígenes de color con datos completos. Además, este contenido puede mostrarse en una amplia gama de dispositivos, cada uno con su propio intervalo de capacidades de visualización de color.

Una aplicación de iOS 10 llena la diferencia entre estos dos problemas con los nuevos integrados, todo el sistema Color administrar sistemas. Este sistema garantiza que una imagen tiene el mismo aspecto en cualquier dispositivo iOS, con independencia de qué espacio de Color de la imagen se codificó en.

Administración del color se inicia con cada imagen con un espacio de Color asociado (o un perfil de Color). Esta información se usa en la _el proceso de coincidencia de Color_ donde se comparan los colores de la imagen de origen a los colores en el dispositivo de salida. Puesto que cada píxel de la imagen debe ser de Color coincidente, puede llevar mucho tiempo y se coloca una carga en CPU del dispositivo.

Debido a la naturaleza de la _el proceso de coincidencia de Color_, esta conversión puede ser potencialmente "pérdida" si el dispositivo de salida tiene una gama más pequeña que la imagen de origen.

Afortunadamente, los cálculos que entran en el _el proceso de coincidencia de Color_ puede fácilmente ser acelerados por hardware (ya sea en la CPU o la GPU) y que funciona automáticamente mediante la compatibilidad de creación en sistemas de base como cuarzo 2D, se asegura de Apple ColorSync y animación de núcleo. Para el contenido correctamente etiquetada, codificación no es necesario para aprovechar estas características.

Administración del color compatible en cada plataforma como se indica a continuación:

- **macOS** -macOS ha sido color administrado desde el comienzo.
- **iOS** -iOS admite la administración de color automático desde iOS 9.3 (y versiones posteriores).

### <a name="designing-for-wide-gamut"></a>Diseño de la amplia gama de colores

Apple tiene la siguiente sugerencia para el diseño y el uso de ancho de color, contenido de la imagen de amplia gama de aplicaciones para iOS y Mac OS:

- Usar solo contenido de amplia gama de marca tienen sentido en la aplicación, que no deben automáticamente usarse en cualquier lugar.
- Sólo utilice el contenido de la amplia gama de colores en colores vivos mejorará la experiencia del usuario.
- En no es necesario cambiar todo el contenido a P3 para las aplicaciones existentes.

Cadena de herramientas de Apple hace admiten para el contenido de la imagen de gama amplia un gradual participar en, por lo que admitir color amplia en una aplicación no es una situación de todo o nada.

### <a name="upgrading-existing-content-to-wide-color"></a>Actualizar el contenido existente a Color amplia

Apple tiene las siguientes sugerencias para actualizar el contenido existente de la imagen a color amplia:

- No solo "asignar" un perfil P3 el contenido de la aplicación de edición de imágenes. Si lo hace, simplemente volverá a asignar el contenido para el nuevo espacio de Color con resultados inesperados, como el ajuste de los colores para caber en el nuevo espacio, por tanto, modificar la imagen de color existente.
- Será necesario que el contenido de la imagen "convertirá" en el perfil de presentación P3 mediante una aplicación de edición de imágenes.

### <a name="file-formats-and-color-profiles"></a>Formatos de archivo y los perfiles de Color

Apple tiene las siguientes sugerencias para los formatos de archivo y los perfiles de color utilizados en contenido de imagen de color amplia de la aplicación:

- Use el perfil de color "Mostrar P3" para los espacios de trabajo de RGB.
- Use un 16 bits por modo de canal de color.
- Utilizar un retraso 2015 iMac (o posterior) para previsualizar el contenido de la imagen de manera precisa.
- Exportar recursos de imágenes como archivos PNG de 16 bits con un perfil ICC "Mostrar P3" incrustado.

> [!IMPORTANT]
> **Nota:** mediante la **Guardar para Web** o **exportar activos** en las características que se encuentran en el software de edición de imágenes más populares _no_ profesional para las imágenes de color amplia desde Estas características no se actualizaron para admitir aún las especificaciones de formato de archivo necesario.

### <a name="supporting-wide-color-with-asset-catalogs"></a>Compatibilidad con Color amplia con catálogos de activos

En iOS 10 y macOS Sierra, Apple ha ampliado los catálogos de recurso utilizado para incluir y clasificar el contenido de las imágenes estáticas en el paquete de la aplicación para admitir color amplia.

Con catálogos de activos ofrecen las siguientes ventajas a una aplicación:

- Proporcionan la mejor opción de implementación para los activos de imagen estática.
- Admite la corrección de color automático.
- Admiten la optimización de formato de píxel automática.
- Admiten la segmentación de datos de aplicación y reducción de aplicación, lo que garantiza que sólo el contenido relevante get se entregue al dispositivo del usuario final.

Apple ha realizado las siguientes mejoras en catálogos de activos para la compatibilidad de color amplia:

- Admite el contenido de origen de 16 bits (por canal de color).
- Admite el contenido de catalogación por gama de colores de pantalla. Se puede etiquetar contenido para el sRGB o gamas de presentación P3.

El desarrollador tiene tres opciones para admitir contenido de color amplia en sus aplicaciones:

1. **No hacer nada** -puesto que el contenido de color amplia sólo debe utilizarse en situaciones donde la aplicación necesita para mostrar colores vivos (donde mejorará la experiencia del usuario), debería quedar dejarse contenido fuera de este requisito-es. Continuarán puede representarse correctamente en todas las situaciones de hardware.
2. **Actualizar el contenido a mostrar P3** -Esto exige al programador reemplazar el contenido de imagen existente en su catálogo de activos con un archivo nuevo y actualizado en el formato de P3 y etiquetarlo como tal en el Editor activo. En tiempo de compilación, se generará una imagen derivado sRGB en estos activos.
3. **Proporcionar contenido de activos con optimización para** -en este caso, el desarrollador proporcionará un sRGB de 8 bits y una visión de P3 de presentación de 16 bits de cada imagen en el catálogo de activos (etiquetado correctamente en el editor activo).

### <a name="asset-catalog-deployment"></a>Implementación del catálogo de activos

Se producirá lo siguiente cuando el desarrollador envía una aplicación a la tienda de aplicaciones con catálogos de activos que incluya contenido de la imagen de color amplia:

- Cuando se implemente la aplicación para el usuario final, la segmentación de aplicación se asegurará de que se entrega solo la variante de contenido adecuada para el dispositivo del usuario.
- En dispositivos que no son compatibles con color amplia, no hay ningún costo de carga para incluir el contenido de color amplia, tal y como lo nunca se envía al dispositivo.
- `NSImage` en Mac OS Sierra (y versiones posteriores) seleccionará automáticamente la mejor representación contenida para la presentación del hardware.
- El contenido mostrado se actualizarán automáticamente cuando o si el hardware de dispositivos muestra el cambio de características.

### <a name="asset-catalog-storage"></a>Almacenamiento de información de catálogo de activos

Almacenamiento de información de catálogo de Asset tiene las propiedades y las implicaciones para las aplicaciones siguientes:

- En tiempo de compilación, Apple intenta optimizar el almacenamiento de contenido de la imagen a través de las conversiones de imágenes de alta calidad.
- se utilizan 16 bits por canal de color para el contenido de color amplia.
- Compresión de contenido de imagen dependiente se utiliza para reducir los tamaños entregas de contenido. Se agregaron nuevos compresiones "pérdidas" para optimizar aún más los tamaños de contenido.

## <a name="colors-in-user-interfaces"></a>Colores de las Interfaces de usuario

Cuando se trabaja con los colores de una interfaz de usuario, la mayoría de los píxeles en pantalla está en un color sólido. Además, la mayoría de estos píxeles no se derivan de activos de imágenes, pero se dibuja directamente por la aplicación (o por el sistema operativo en el nombre de la aplicación).

Color de la amplia gama de colores puede presentar varios desafíos cuando se trabaja en el nivel de interfaz de usuario:

- **Comunicación colores** : si hablamos de color entre los diseñadores y desarrolladores normalmente un _supone_ sRGB implicado de espacio de colores. Por lo que se puede comunicar un color como `rgb(128, 45, 56)` o `#FF0456`. En un diseño de la amplia gama de colores, estas representaciones no proporcionan información suficiente para representar con exactitud el color especificado, también se debe incluir el espacio de Color de trabajo. Apple sugiere utilizando `P3(128, 45, 56)` y `P3#FF0456` en su lugar. 
- **Seleccionar colores** : la mayoría de la edición de imágenes populares y el diseño software sufren las mismas limitaciones que el espacio de Color sRGB al utilizar los selectores de colores integrada. El diseñador debe asegurarse de que se encuentran en el espacio de Color "Mostrar P3" en el selector de colores al trabajar con diseños de color amplia.
- **Codificación de colores** : ambos `NSColor` (macOS) y `UIColor` (iOS & tvOS) tiene nuevos métodos de conveniencia para generar colores P3 directamente y ambos se han ampliado para admitir los colores en el espacio de Color sRGB de rango extendido también.
- **Almacenamiento de los colores** -debe tener cuidado al almacenar amplia gama de colores colorea de documento de una aplicación. Donde 8 bits por canal de color funcionado bien para el espacio de color sRGB, deben utilizarse 16 bits por canal de color para los colores ancho. El desarrollador también debe controlar instancias de espacio de colores asumido (ya que todo estaba tradicionalmente sRGB solo).

## <a name="colors-on-the-web"></a>Colores en la Web

Se debe tener cuidado al trabajar con color amplia en páginas web y en dispositivos que admiten la presentación de color amplia. Si se ha etiquetado correctamente todo el contenido de imagen que se ha incluido en el sitio Web, iOS y Mac OS automáticamente coincidencia de color y mostrarlos correctamente.

También están disponibles para ayudar a resolver activos entre P3 y sRGB dispositivos compatibles con las consultas de medios:

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple tiene también una propuesta de WebKit que le permitirá CSS en otros espacios de Color además el espacio asumido sRGB.

## <a name="rendering-off-screen-images-in-app"></a>Representar imágenes fuera de la pantalla de aplicación

Según el tipo de aplicación que se está creando, podría permitir al usuario que incluya contenido de la imagen que han recopilado desde internet o creación el contenido de la imagen directamente dentro de la aplicación (por ejemplo, un dibujo vectorial aplicación por ejemplo).

En ambos casos, la aplicación puede representar las imágenes necesarias fuera de la pantalla de color amplia mediante mejoradas características agregadas a iOS y Mac OS.

### <a name="drawing-wide-color-in-ios"></a>Dibujo de Color amplia en iOS

Antes de hablar sobre cómo dibujar correctamente una imagen de color amplia en iOS 10, eche un vistazo al código de dibujo iOS comunes siguientes:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...
    
    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
}
```

Hay problemas con el código estándar que será necesario llevarla a cabo _antes de_ se puede utilizar para dibujar una imagen de color amplia. El `UIGraphics.BeginImageContext (size)` método usado para iniciar el dibujo de la imagen de iOS tiene las siguientes limitaciones:

- No puede crear contextos de imagen con más de 8 bits por canal de color.
- No puede representar colores en el espacio de Color sRGB de rango extendido.
- No tiene la capacidad para proporcionar una interfaz para crear contextos en un espacio de colores sRGB no debido a las rutinas de C de bajo nivel que se llama en segundo plano.

Para administrar estas limitaciones y dibujar una imagen de color amplia en 10 de iOS, utilice en su lugar, el código siguiente:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

El nuevo `UIGraphicsImageRenderer` clase crea un nuevo contexto de la imagen que es capaz de controlar el espacio de Color sRGB de rango extendido y tiene las siguientes características:

- Es totalmente color administrado de forma predeterminada.
- Es compatible con el espacio de Color sRGB de rango extendido de forma predeterminada.
- Determina de manera inteligente si deben representarse en el sRGB o rango extendido sRGB según las capacidades del dispositivo iOS que la aplicación se ejecuta en el espacio de colores.
- Completamente y automáticamente administra el contexto de la imagen (`CGContext`) duración, por lo que el programador no tiene que preocuparse de que realiza la llamada comienzan y terminan comandos contextuales.
- Es compatible con la `UIGraphics.GetCurrentContext()` método.

El `CreateImage` método de la `UIGraphicsImageRenderer` clase se llama para crear una imagen de color amplia y se pasa con el contexto de la imagen para dibujar en un controlador de finalización. Todo el dibujo se realiza dentro de este controlador de finalización como sigue:

- El `UIColor.FromDisplayP3` método crea un nuevo color rojo saturado dentro de la amplia gama de espacio de colores de pantalla P3 y se utiliza para dibujar la primera mitad del rectángulo. 
- La segunda mitad del rectángulo se dibuja en el sRGB normal totalmente saturado color rojo para la comparación.

### <a name="drawing-wide-color-in-macos"></a>Dibujo de Color amplia en macOS

La `NSImage` clase se ha expandido en macOS Sierra para admitir el dibujo de imágenes de Color amplia. Por ejemplo:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);
    
    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();
    
    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();
    
    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>Representación en pantalla imágenes de aplicación

Para representar imágenes de color amplia en la pantalla, el proceso funciona de forma similar a dibujar una imagen de color amplia fuera de la pantalla para iOS presentada anteriormente y macOS.

### <a name="rendering-on-screen-in-ios"></a>Representación en pantalla de iOS

Cuando la aplicación necesita representar una imagen en color amplia en pantalla en iOS, invalidar la `Draw` método de la `UIView` en cuestión como de costumbre. Por ejemplo:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
            ...
        }
    }
}
``` 

Igual con iOS 10 el `UIGraphicsImageRenderer` clase mostrado anteriormente, de forma inteligente decide si deben representarse en el sRGB o rango extendido sRGB espacio de colores en función de las capacidades del dispositivo iOS que se ejecuta la aplicación cuando el `Draw` se llama al método. Además, la `UIImageView` ha sido color administrado desde iOS 9.3.

Si la aplicación necesita saber cómo se realiza la representación en un `UIView` o `UIViewController`, puede comprobar la nueva `DisplayGamut` propiedad de la `UITraitCollection` clase. Este valor será una `UIDisplayGamut` enum de las acciones siguientes:

- P3
- SRGB
- Sin especificar

Si desea que la aplicación para controlar qué espacio de Color se utiliza para dibujar una imagen, puede usar una nueva `ContentsFormat` propiedad de la `CALayer` para especificar el espacio de Color deseado. Este valor puede ser un `CAContentsFormat` enum de las acciones siguientes:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Representación en pantalla en macOS

Cuando la aplicación necesita representar una imagen en color amplia en pantalla en Mac OS, invalidar la `DrawRect` método de la `NSView` en cuestión como de costumbre. Por ejemplo:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

De nuevo, determina de manera inteligente si deben representarse en el sRGB o rango extendido sRGB según las capacidades del hardware de Mac que se ejecuta la aplicación cuando el espacio de colores del `DrawRect` se llama al método.

Si desea que la aplicación para controlar qué espacio de Color se utiliza para dibujar una imagen, puede usar una nueva `DepthLimit` propiedad de la `NSWindow` clase para especificar el espacio de Color deseado. Este valor puede ser un `NSWindowDepth` enum de las acciones siguientes:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>Resumen

En este artículo ha cubierto color amplia y las formas que se pueden implementarse y utilizarse dentro de una aplicación Xamarin.iOS o Xamarin.Mac.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
