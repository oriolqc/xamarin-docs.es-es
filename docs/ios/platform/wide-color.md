---
title: Color amplio en Xamarin.iOS
description: Este documento aborda color amplia y cómo se puede usar en una aplicación Xamarin.iOS o Xamarin.Mac. También proporciona una descripción general de muchos conceptos importantes relacionados con el color, como espacios de colores, canales y primarios.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f139bcceda12752e43a3a8330fa0a0e038e539f9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121313"
---
# <a name="wide-color-in-xamarinios"></a>Color amplio en Xamarin.iOS

_En este artículo se trata color amplia y cómo se puede usar en una aplicación Xamarin.iOS o Xamarin.Mac._

10 de iOS y macOS Sierra mejora la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema incluidos marcos como gráficos de Core, imagen básica, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

## <a name="about-wide-color"></a>Acerca de Color amplio

Como se indicó anteriormente, 10 de iOS y macOS Sierra mejora la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema, incluidos los marcos como gráficos de Core, imagen básica, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

En el 90 Apple creado ColorSync para controlar el color que se está procesando en el equipo Mac. También ayudó a encontrar International Color Consortium (ICC) para crear y promover un conjunto de estándares para controlar el color en el hardware del equipo. Trabajo de Apple con el ICC se incluyó en ColorSync y se ha compilado en el núcleo de OS X (ahora denominado macOS).

Apple ha sido también a la vanguardia de la tecnología de presentación con el hardware como la Retina mostrar, la nueva P3 mostrar y Mostrar espacio de colores P3 (publicada en el iMac en 2015) y muestra el TrueTone en los profesionales de iPad, iPhone 7 y iPhone 7 Plus.

Con iOS 10 y Color amplia en macOS Sierra, Apple está cambiando la forma en que macOS e iOS controlan el color para aprovechar al máximo estas nuevas tecnologías de presentación.

## <a name="core-color-concepts"></a>Conceptos de Color

Los siguientes conceptos de color deben abordarse antes de echar un vistazo más profundo a color en iOS y macOS:

### <a name="color-space"></a>Espacio de colores

Un espacio de colores es un entorno en el que pueden representados y compararse de colores. Puede ser un espacio dimensional de uno a cuatro definido por la intensidad de sus componentes de color. 

[![](wide-color-images/color00.png "Un espacio de colores")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>Los canales de color

También se pueden hacer referencia a los componentes de color como canales de Color. Algunas representaciones familiares sería el RGB espacios, espacios de color gris, CMYK, espacios o espacios independientes del dispositivo. 

[![](wide-color-images/color02.png "Los componentes de color también se conoce como canales de Color")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>Color de

Color de proporciona el sistema de coordenadas que se utiliza para comparar y calcular los colores. Color de suelen pertenecer a más intensa versión del color especificado que se puede generar en el canal de Color.

[![](wide-color-images/color01.png "Color de proporciona el sistema de coordenadas que se utiliza para comparar y calcular los colores")](wide-color-images/color01.png#lightbox)

En el caso del espacio de colores RGB representado anteriormente, los primarios de Color son dónde el `1.0` ancladas coordenadas (como `[1.0, 0.0, 0.0]` para el rojo).

### <a name="color-gamut"></a>Gama de colores

Gama de colores se refiere a todos los colores que se pueden definir como una combinación de los canales de Color individuales dentro de un determinado espacio de colores.

[![](wide-color-images/color03.png "Ejemplo de la gama de colores")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>¿Qué es el Color amplio

Antes de tratar el tema de Color amplia, se debería tenía una discusión sobre el estándar actual para el color, el espacio de Color RGB estándar (sRGB). Es el espacio de colores usados en la informática de hoy en día y es el espacio de colores predeterminado para iOS y macOS.

El espacio de Color sRGB tiene las siguientes propiedades:

- Se basa en el estándar de ITU-R BT.709.
- Tiene un aproximado Gamma de 2.2.
- Representa las condiciones de iluminación típico (D65).

Puesto que sRGB es ampliamente que utilizado en la industria, un desarrollador puede realizar algunas suposiciones que se representará fielmente el color especificado en cualquier dispositivo en que se muestra. Sin embargo, esto no siempre puede suceder. Además, existen varios colores que no caben en el espacio de Color sRGB y, por tanto, no se puede representar en él.

Por ejemplo, muchos hmong está diseñados con subprocesos utilizando muchas tintas y tintes queda fuera de sRGB. También se crean muchos productos que una persona que se encuentra en su vida diaria con colores brillantes intensos que se encuentran fuera del espacio de Color sRGB. Algunos de los ejemplos más atractivos de colores que no se puede representar en sRGB son cosas por naturaleza, como puestas de sol, otoño, flores exóticas y aguas tropicales.

Los usuarios que han sido capturar imágenes digitales en formato RAW pueden tener imágenes en sus dispositivos que contienen todos estos datos de color, aunque no puede representarse correctamente en el espacio de Color sRGB y, por tanto, no se muestra correctamente en la pantalla.

### <a name="the-display-p3-color-space"></a>El espacio de colores de pantalla P3

En 2015, Apple publicó nuevos productos (iMac e iPad Pro 9.7") que proporcionan el nuevo espacio de Color para mostrar P3 para controlar los problemas creados por el espacio de Color sRGB.

[![](wide-color-images/color04.png "El nuevo espacio de Color de pantalla P3")](wide-color-images/color04.png#lightbox)

El espacio de colores de pantalla P3 tiene las siguientes propiedades:

- Admite un espacio de color amplia para plataformas de hardware modernos.
- Según el estándar de SMPTE DCI-P3. DCI P3 se diseñó para proyectores digitales pero se ha modificado por Apple para admitir los monitores.
- Tiene un aproximado Gamma de 2.2.
- Representa las condiciones de iluminación típico (D65).

Con arreglo a Apple, los usuarios están cambiando sus flujos de trabajo a sus plataformas móviles. Solucionar los problemas de presentación del color presentados por sRGB en los dispositivos móviles (iPad profesionales), professional, requerida, incluido algo más que una pantalla a color amplia. Una de las soluciones era actualizar la calibración de fábrica, por lo que se haya calibrado cada dispositivo en la fábrica que garantice que desde un dispositivo a otro, presentación en color precisos y coherentes.

Otra solución es la inclusión completa, todo el sistema de administración de color que Apple ha incorporado en iOS 10 y macOS Sierra. 

### <a name="the-extended-range-srgb-color-space"></a>El espacio de Color sRGB de rango ampliado

La nueva administración del color de todo el sistema iOS 10 debe tener en cuenta todas las aplicaciones de iOS existente que se compila y perfeccionadas para sRGB. Se ha diseñado para asegurarse de que no afecten a cualquier rendimiento de representación o una aplicación de color de estas aplicaciones.

Para controlar esta situación, Apple ha incluido el espacio de Color sRGB de rango ampliado en iOS 10 (y macOS Sierra).

El espacio de Color sRGB de rango ampliado tiene las siguientes propiedades:

- Tiene todos la misma sRGB principales.
- Tiene un aproximado Gamma de 2.2.
- Representa las condiciones de iluminación típico (D65).
- Es compatible con los valores negativos y los valores mayor que uno (1).

Por lo que permite valores inferiores a cero y mayor que uno, sRGB de rango ampliado que espacio de colores no sólo permite que las aplicaciones existentes a los colores presentes en sRGB sin descensos en el rendimiento o distorsión, pero permite el espacio de colores representar cualquier color dentro de la visible espectro. Todo esto se logra manteniendo todavía los mismos puntos de anclaje que el espacio de Color sRGB.

### <a name="extended-range-srgb-in-action"></a>SRGB de rango extendido en acción

Para ver cómo funcionan los valores fuera de cero y uno en el espacio de Color sRGB de rango ampliado, tome el ejemplo siguiente de la de la saturación máxima rojo disponible en el espacio de colores de pantalla P3:

[![](wide-color-images/color05.png "Cómo funcionan los valores fuera de cero y uno en el espacio de Color sRGB de rango ampliado")](wide-color-images/color05.png#lightbox)

En la pantalla P3, este color se representaría como `[1.0, 0.0, 0.0]` y en sRGB de rango ampliado sería `[1.358, -0.074, -0.012]`. Porque están llenos sRGB valores contenidos dentro de la pantalla P3 y los valores de la pantalla P3 diseñar "fuera" de los intervalos de sRGB.

Para el hardware físico que permite a los valores de píxel vaya de extreme positivo a valores negativos extremos, puede mostrar cualquier color disponible en el espectro visible y estos valores se pueden representar en el espacio de Color sRGB de rango ampliado.

### <a name="device-pixel-formats"></a>Formatos de píxel de dispositivo 

El espacio de colores ha sido en gran medida de sRGB estandarizar sobre el uso de un formato de píxel de 8 bits, ya que es de 8 bits por canal de color principalmente suficiente para describir los colores de sRGB. Esto no es perfecta, pero lo suficientemente bueno y proporciona un buen equilibrio entre el uso del procesador y memoria para mostrar imágenes.

Dado que pantalla P3 pueden representar las coordenadas de color fuera del espacio de color sRGB, requiere 16 bits por canal de color para representar correctamente los colores con el espacio de Color sRGB de rango ampliado.

## <a name="system-wide-wide-color-support"></a>Compatibilidad de Color amplia de todo el sistema

Para admitir completamente color amplia y amplia gama dentro de 10 iOS y macOS Sierra, Apple ha extendido los siguientes marcos para aprovechar al máximo el espacio de Color sRGB de rango ampliado y pantalla P3:

- UIKit (solo para iOS)
- SceneKit
- Gráficos básicos
- ImageIO
- Imagen de Core
- WebKit
- SpriteKit
- Animación básica
- AppKit (para macOS solo)

Además, ha mejorado la compatibilidad para el espacio de Color sRGB de rango ampliado pantalla retina y pantalla P3 muestran.

Color amplio es compatible y puede usarse en los siguientes tipos de contenido de la aplicación:

- Recursos de imagen estática incluidos en el paquete de aplicación.
- Documento y red basadas en recursos de imagen.
- Multimedia avanzada como Live fotografías o imágenes en formato RAW.
- Imágenes de textura de sombreador de gráficos 3D.

## <a name="solving-the-color-problem"></a>Para solucionar el problema de Color

El contenido que se muestra en una aplicación puede proceder de una amplia gama de orígenes y llena de color. Además, este contenido se puede mostrar en una amplia gama de dispositivos, cada uno con su propia gama de capacidades de visualización de color.

Una aplicación de iOS 10 salva la diferencia entre estos dos problemas con los nuevos integrados, todo el sistema Color administrar sistemas. Este sistema garantiza que una imagen tiene el mismo aspecto en cualquier dispositivo iOS, independientemente de qué espacio de Color de la imagen se codificó en.

Administración del color comienza con todas las imágenes que tienen un espacio de colores asociado (o perfil de Color). Esta información se usa en el _el proceso de coincidencia de Color_ donde se buscan los colores de la imagen de origen a los colores en el dispositivo de salida. Puesto que cada píxel de la imagen debe ser coincide con el Color, puede llevar mucho tiempo y coloque una presión en la CPU del dispositivo.

Debido a la naturaleza de la _el proceso de coincidencia de Color_, esta conversión puede ser potencialmente "con pérdida de datos" si el dispositivo de salida tiene una gama más pequeña que la imagen de origen.

Afortunadamente, los cálculos que se incluyen en el _el proceso de coincidencia de Color_ puede fácilmente ser acelerados por hardware (ya sea en la CPU o la GPU) y Apple garantiza que funciona automáticamente mediante la creación de soporte técnico en sistemas de bases como Quartz 2D, ColorSync y animación básica. Para obtener contenido etiquetado correctamente, codificación no es necesario para aprovechar estas características.

Administración del color se admite en cada plataforma como sigue:

- **macOS** -macOS ha sido administrado desde comienzos de color.
- **iOS** -iOS admite la administración del color automático desde iOS 9.3 (y versiones posteriores).

### <a name="designing-for-wide-gamut"></a>Diseño de la amplia gama de colores

Apple tiene la siguiente sugerencia para el diseño y uso de ancho de color, contenido de la imagen amplia gama de aplicaciones para iOS y macOS:

- Usar solo el contenido de la amplia gama donde en la marca sentido en la aplicación, que no automáticamente se deben usar en todas partes.
- Use solo contenido de la amplia gama donde colores vivos mejorará la experiencia del usuario.
- No es necesario cambiar todo el contenido a P3 para las aplicaciones existentes en.

Cadena de herramientas de Apple hace que se admiten para el contenido de imagen amplia gama un gradual opt-de, por lo que admiten color amplia en una aplicación no es una situación de todo o nada.

### <a name="upgrading-existing-content-to-wide-color"></a>Actualizar el contenido existente a Color amplio

Apple tiene las siguientes sugerencias para actualizar el contenido existente de la imagen a color amplio:

- No sólo "asignar" un perfil de P3 para el contenido de la aplicación de edición de imágenes. Si lo hace, simplemente volverá a asignar el contenido al nuevo espacio de Color con resultados inesperados, como el ajuste de los colores para caber en el nuevo espacio, por tanto, modificar la imagen de color existente.
- El contenido de la imagen debe "convertirse" en el perfil de pantalla P3 mediante una aplicación de edición de imágenes.

### <a name="file-formats-and-color-profiles"></a>Formatos de archivo y los perfiles de Color

Apple tiene las siguientes sugerencias para los formatos de archivo y los perfiles de color usados en contenido de imagen de color amplia de la aplicación:

- Usar el perfil de color de la "Pantalla P3" para los espacios de trabajo de RGB.
- Utilice un 16 bits por modo de canal de color.
- Usar un iMac Late 2015 (o posterior) para la vista previa precisa del contenido de imagen.
- Exportar activos de imagen como archivos PNG de 16 bits con un perfil ICC "Pantalla P3" incrustado.

> [!IMPORTANT]
> Mediante el **Guardar para Web** o **exportar activos** las características que se encuentran en el software de edición de imágenes más populares _no_ profesional para las imágenes de color amplia puesto que estas características no han sido actualizado para admitir aún las especificaciones de formato de archivo necesario.

### <a name="supporting-wide-color-with-asset-catalogs"></a>Compatibilidad con Color amplia con catálogos de activos

En iOS 10 y macOS Sierra, Apple ha ampliado el catálogos de recursos usa para incluir y clasificación de contenido de imagen estática de lote de la aplicación para admitir color amplia.

Uso de los catálogos de activos proporcionan las siguientes ventajas a una aplicación:

- Proporcionan la mejor opción de implementación para los activos de imagen estática.
- Admite la corrección del color automático.
- Admiten la optimización de formato de píxel automática.
- Admiten App segmentación y reducción de aplicación, lo que garantiza que solo el contenido que es relevante de get se entrega al dispositivo del usuario final.

Apple ha realizado las siguientes mejoras en catálogos de recursos para obtener soporte técnico de color amplia:

- Admite el contenido de origen de 16 bits (por canal de color).
- Admite el contenido de catalogación mediante la gama de colores de visualización. Contenido se puede etiquetar de pantalla P3 gamas o sRGB.

El desarrollador tiene tres opciones para la compatibilidad con contenido en color amplia en sus aplicaciones:

1. **No hacer nada** -dado que el contenido en color amplia solo debe usarse en situaciones donde la aplicación debe mostrar colores vivos (donde mejorará la experiencia del usuario), el contenido fuera de este requisito debe dejarse como-es. Continuarán representarse correctamente en todas las situaciones de hardware.
2. **Actualizar el contenido existente a la pantalla P3** -Esto requiere que el desarrollador reemplazar el contenido de imagen existente en su catálogo de recursos con un archivo nuevo y actualizado en el formato de P3 y etiquetarla como tal en el Editor activo. En tiempo de compilación, se generará una imagen derivados sRGB de estos recursos.
3. **Proporcionar contenido optimizado de activos** -en este caso, el desarrollador proporcionará un sRGB de 8 bits y una visión de la pantalla P3 de 16 bits de cada imagen en el catálogo de activos (etiquetado correctamente en el editor de recursos).

### <a name="asset-catalog-deployment"></a>Implementación del catálogo de activos

Se producirá lo siguiente cuando el desarrollador envía una aplicación en el Store de la aplicación con los catálogos de recursos que incluyen contenido de la imagen de color amplia:

- Cuando la aplicación se implementa para el usuario final, la segmentación de la aplicación garantizará que se entrega solo la variante de contenido adecuada para el dispositivo del usuario.
- En el dispositivo que no son compatibles con color amplia, no hay ningún costo de carga para incluir contenido en color amplia, nunca está incluido en el dispositivo.
- `NSImage` en macOS Sierra (y versiones posteriores) seleccionará automáticamente la mejor representación de contenido para la presentación del hardware.
- El contenido mostrado se actualizarán automáticamente cuando o si el hardware de dispositivos muestra el cambio de características.

### <a name="asset-catalog-storage"></a>Almacenamiento del catálogo de activos

Almacenamiento del catálogo activo tiene implicaciones para una aplicación y propiedades siguientes:

- En tiempo de compilación, Apple intenta optimizar el almacenamiento de contenido de la imagen a través de las conversiones de imágenes de alta calidad.
- se usan 16 bits por canal de color para el contenido de color amplia.
- Compresión de imágenes dependientes se usa para reducir los tamaños de contenido de entrega de contenido. Se han agregado nuevas compresiones "con pérdida de datos" para optimizar aún más el contenido de los tamaños.

## <a name="colors-in-user-interfaces"></a>Colores de las Interfaces de usuario

Cuando se trabaja con los colores de una interfaz de usuario, la mayoría de los píxeles en pantalla está en un color sólido. Además, la mayoría de estos píxeles no proceden de los recursos de imágenes, pero se dibuja directamente por la aplicación (o por el sistema operativo en el nombre de la aplicación).

Color de la amplia gama de colores puede presentar varios desafíos cuando se trabaja en el nivel de interfaz de usuario:

- **Comunicación colores** : al hablar de color entre los diseñadores y desarrolladores suele haber una _supone_ sRGB implica de espacio de colores. Es posible que se comunica un color como `rgb(128, 45, 56)` o `#FF0456`. En un diseño de la amplia gama de colores, estas representaciones no proporcionan información suficiente para representar con exactitud el color especificado, también se debe incluir el espacio de colores de trabajo. Apple sugiere el uso de `P3(128, 45, 56)` y `P3#FF0456` en su lugar. 
- **Seleccionar colores** : la mayor parte de la edición de imágenes populares y el diseño de software sufren las mismas limitaciones que el espacio de Color sRGB al usar los selectores de colores integrada. El diseñador debe asegurarse de que se encuentran en el espacio de colores "Pantalla P3" en el selector de colores al trabajar con diseños de color amplia.
- **Codificación de colores** : ambos `NSColor` (macOS) y `UIColor` (iOS y tvOS) tiene nuevos métodos de conveniencia para generar los colores de P3 directamente y ambos se han ampliado para admitir los colores en el espacio de Color sRGB de rango ampliado también.
- **Almacenamiento de los colores** -debe tener cuidado al almacenar amplia gama de colores en el documento de una aplicación. Donde los 8 bits por canal de color funcionado bien para el espacio de color sRGB, 16 bits por canal de color se deben usar para los colores amplios. El desarrollador debe también esté atento a las instancias de espacio de colores asumido (ya que todo lo que tradicionalmente era sRGB solo).

## <a name="colors-on-the-web"></a>Colores de la Web

Debe tener cuidado al trabajar con colores amplia en páginas web y en dispositivos que admiten la presentación en color amplia. Si se ha etiquetado correctamente todo el contenido de imagen que se ha incluido en el sitio Web, iOS y macOS automáticamente coincidencia de color y mostrarlos correctamente.

También están disponibles para ayudar a resolver activos entre P3 y sRGB de los dispositivos compatibles con las consultas de medios:

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple también tiene una propuesta de WebKit que permitirá CSS que se especifique en otros espacios de colores además el espacio asumido sRGB.

## <a name="rendering-off-screen-images-in-app"></a>Representar imágenes fuera de la pantalla en la aplicación

Según el tipo de aplicación que se está creando, podría permitir al usuario que incluya contenido de la imagen que han recopilado desde internet o creación contenido de la imagen directamente dentro de la aplicación (por ejemplo, un dibujo vectorial app por ejemplo).

En ambos casos, la aplicación puede representar las imágenes necesarias fuera de la pantalla en color amplio uso de características mejoradas que se agregó a iOS y macOS.

### <a name="drawing-wide-color-in-ios"></a>Color amplio de dibujo en iOS

Antes de explicar cómo dibujar una imagen de color amplia correctamente en iOS 10, eche un vistazo al código de dibujo iOS comunes siguientes:

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

Hay problemas con el código estándar que deberá solucionarse _antes_ se puede usar para dibujar una imagen de color amplia. El `UIGraphics.BeginImageContext (size)` método usado para iniciar el dibujo de imagen de iOS tiene las siguientes limitaciones:

- No puede crear contextos de imagen con más de 8 bits por canal de color.
- No puede representar los colores en el espacio de Color sRGB de rango ampliado.
- No tiene la capacidad para proporcionar una interfaz para crear contextos en un espacio de colores no sRGB debido a las rutinas de C de bajo nivel que se llama en segundo plano.

Para controlar estas limitaciones y dibujar una imagen de color amplia en iOS 10, use el siguiente código en su lugar:

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

El nuevo `UIGraphicsImageRenderer` clase crea un nuevo contexto de la imagen que es capaz de controlar el espacio de Color sRGB de rango ampliado y tiene las siguientes características:

- Es totalmente administrado de forma predeterminada el color.
- Es compatible con el espacio de Color sRGB de rango ampliado de manera predeterminada.
- Determina de manera inteligente si debe representar en el sRGB o sRGB de rango ampliado según las capacidades del dispositivo iOS que se ejecuta la aplicación en el espacio de colores.
- Totalmente y automáticamente administra el contexto de la imagen (`CGContext`) duración, por lo que el desarrollador no tiene que preocuparse sobre cómo llamar a begin y end comandos de contexto.
- Es compatible con la `UIGraphics.GetCurrentContext()` método.

El `CreateImage` método de la `UIGraphicsImageRenderer` clase se llama para crear una imagen de color amplia y se pasa con el contexto de la imagen para dibujar en un controlador de finalización. Todo el dibujo se realiza dentro de este controlador de finalización como sigue:

- El `UIColor.FromDisplayP3` método crea un nuevo color rojo saturado en la amplia gama de espacio de colores de pantalla P3 y se usa para dibujar la primera mitad del rectángulo. 
- La segunda mitad del rectángulo se dibuja en el normal sRGB totalmente saturado en color rojo para la comparación.

### <a name="drawing-wide-color-in-macos"></a>Color amplio de dibujo en macOS

La `NSImage` clase se ha ampliado en macOS Sierra para admitir el dibujo de imágenes de Color amplia. Por ejemplo:

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

## <a name="rendering-on-screen-images-in-app"></a>Representación en pantalla de imágenes en la aplicación

Para representar las imágenes de color amplia en pantalla, el proceso funciona de manera similar a dibujar una imagen fuera de la pantalla de color amplia para macOS e iOS presentadas más arriba.

### <a name="rendering-on-screen-in-ios"></a>Representación en pantalla en iOS

Si la aplicación necesita representar una imagen en color amplia en pantalla en iOS, reemplazar el `Draw` método de la `UIView` en cuestión como de costumbre. Por ejemplo:

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

Como con iOS 10 el `UIGraphicsImageRenderer` clase mostrado anteriormente, de manera inteligente si debe representar en el sRGB o sRGB de rango ampliado según las capacidades del dispositivo iOS que se ejecuta la aplicación cuando el espacio de colores del `Draw` se llama al método. Además, la `UIImageView` ha sido color administrado desde iOS 9.3.

Si la aplicación necesita saber cómo se realiza la representación en un `UIView` o `UIViewController`, puede comprobar el nuevo `DisplayGamut` propiedad de la `UITraitCollection` clase. Este valor será una `UIDisplayGamut` enumeración de las acciones siguientes:

- P3
- SRGB
- Sin especificar

Si desea que la aplicación para controlar qué espacio de colores se usa para dibujar una imagen, puede usar un nuevo `ContentsFormat` propiedad de la `CALayer` para especificar el espacio de Color deseado. Este valor puede ser un `CAContentsFormat` enumeración de las acciones siguientes:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Representación en pantalla en macOS

Cuando la aplicación necesita representar una imagen en color amplia en pantalla en macOS, reemplazar el `DrawRect` método de la `NSView` en cuestión como de costumbre. Por ejemplo:

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

De nuevo, decide de forma inteligente si debe representar en el sRGB o sRGB de rango ampliado según las capacidades del hardware de Mac que se ejecuta la aplicación cuando el espacio de colores del `DrawRect` se llama al método.

Si desea que la aplicación para controlar qué espacio de colores se usa para dibujar una imagen, puede usar un nuevo `DepthLimit` propiedad de la `NSWindow` clase para especificar el espacio de Color deseado. Este valor puede ser un `NSWindowDepth` enumeración de las acciones siguientes:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>Resumen

En este artículo ha cubierto color amplio y las formas que se pueden implementarse y utilizarse dentro de una aplicación Xamarin.iOS o Xamarin.Mac.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
