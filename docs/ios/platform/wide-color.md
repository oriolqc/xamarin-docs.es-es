---
title: Color amplio en Xamarin.iOS
description: Este documento aborda color amplia y cómo se puede usar en una aplicación Xamarin.iOS o Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 523aa1a97e1c536b5afbdb66c52f605382fa338d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268809"
---
# <a name="wide-color-in-xamarinios"></a>Color amplio en Xamarin.iOS

_En este artículo se trata color amplia y cómo se puede usar en una aplicación Xamarin.iOS o Xamarin.Mac._

10 de iOS y macOS Sierra mejora la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema incluidos marcos como gráficos básicos, la imagen de Core, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

## <a name="asset-catalogs"></a>Catálogos de activos

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
