---
title: iOS 9 compatibilidad
description: "Incluso si no planea agregar características de iOS 9 a la aplicación inmediatamente, debe volver a generar sus aplicaciones con la versión más reciente de Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bfdf0c73226eec472eb671d5543f5ce124919ab8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="ios-9-compatibility"></a>iOS 9 compatibilidad

_Incluso si no planea agregar características de iOS 9 a la aplicación inmediatamente, debe volver a generar sus aplicaciones con la versión más reciente de Xamarin._

> [!IMPORTANT]
> **Nota:** esta información en esta página es para clientes con aplicaciones ya se encuentran en la tienda de aplicaciones iOS 8 o versiones anteriores, de destino que no se han enviado ya las actualizaciones para la compatibilidad de iOS 9. Si ya usa las versiones más recientes: Xcode 7 y 9 Xamarin.iOS - para el desarrollo de aplicaciones, visitan la [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Cuando aparecían la primera beta de iOS 9, hemos identificado dos problemas con versiones anteriores de Xamarin que aparece como aplicaciones antiguas que no se pueda iniciar en iOS 9.

Estos dos problemas (como [detallada en nuestros foros](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) fueron:

- Compilación aplicaciones para iOS 8 o una versión anterior de la imposibilidad de iniciar en dispositivos de 32 bits (incluidas las aplicaciones compiladas con la [API unificada](~/cross-platform/macios/unified/index.md)).
- No se especifica con la ruta de acceso completa de P/Invoke.

Actualizar la instalación de Xamarin en las últimas versión estable canal y, a continuación, volver a generar y volver a implementar sus aplicaciones, corrige estos dos problemas.

_Incluso si no se pretende actualizar inmediatamente la aplicación con las características de iOS 9, se recomienda volver a compilar con la versión más reciente de Xamarin y volver a enviar a la tienda de aplicaciones_.



Esto garantizará que la aplicación se ejecutará en iOS 9 después de la actualización de los clientes.
Puede seguir son compatibles con iOS 8 - volver a generar con la versión más reciente no afecta a la versión de destino de la aplicación.

Si tiene más problemas durante la comprobación de las aplicaciones existentes en iOS 9, lea la [compatibilidad mejorar](#compat) sección más adelante.


### <a name="updating-with-visual-studio"></a>Actualizar con Visual Studio

Se recomienda que se compruebe explícitamente la que Visual Studio está actualizado a la versión estable más reciente.

## <a name="what-about-components-nugets-and-other-libraries"></a>¿Qué ocurre con otras bibliotecas, componentes y Nugets?

Lo hace **no** tenga que esperar para las nuevas versiones de los componentes o Nugets que usa para resolver los dos problemas anteriormente mencionados.
Basta con volver a compilar la aplicación con la versión estable más reciente de Xamarin.iOS haya solucionado estos problemas.

De forma similar, los proveedores de componentes y los autores de Nuget son **no** deberán enviar nuevas compilaciones para corregir los dos problemas anteriormente mencionados. Sin embargo, si existe un componente o Nuget usa `UICollectionView` o cargar vistas de **Xib** archivos, una actualización *puede* ser necesarios para solucionar los problemas de compatibilidad de iOS 9 que se mencionan más abajo.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Mejora de la compatibilidad en el código

Hay algunos casos de código patrones *utiliza* para que funcione en versiones anteriores de iOS importantes en iOS 9. Estas son algunas causas posibles (y sus soluciones) que pueden surgir al realizar pruebas en iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView es nulo en constructores

**Motivo:** en iOS 9 la `initWithFrame:` constructor está ahora necesario debido a cambios de comportamiento en iOS 9 como el [Estados de documentación de UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si ha registrado una clase para el identificador especificado y se debe crear una nueva celda, la celda se ha inicializado mediante una llamada a su `initWithFrame:` método.

**Corrección:** agregar el `initWithFrame:` constructor similar al siguiente:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Relacionadas con ejemplos: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView produce un error init con codificador al cargar una vista desde un Xib/Nib

**Motivo:** el `initWithCoder:` constructor es el que se llama cuando se carga una vista desde un archivo de interfaz generador Xib. Si este constructor no se exporta a código no administrado no puede llamar a nuestra versión administrada del mismo. Anteriormente (p. ej. en iOS 8) el `IntPtr` se invoca el constructor para inicializar la vista.

**Corrección:** crear y exportar el `initWithCoder:` constructor similar al siguiente:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplo relacionado: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Mensaje Dyld: ninguna imagen de la caché con nombre...

Podría experimentar un bloqueo con la siguiente información en el registro:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** se trata de un error en el vinculador nativo de Apple, lo que sucede cuando realicen un marco de trabajo privada pública (JavaScriptCore se hizo público en iOS 7, antes que es un marco de trabajo privado), y el destino de implementación de la aplicación es para una versión de iOS cuando el Framework estaba privada. En este caso se vinculará el vinculador de Apple con la versión de framework en lugar de la versión pública privada.

**Corrección:** este problema se solucionará IOS 9, pero no hay una solución sencilla que se puede aplicar a usted mismo entretanto: simplemente como destino una versión posterior de iOS en su proyecto (puede intentar iOS 7 en este caso). Otros marcos de trabajo podrían tener problemas similares, por ejemplo el marco de trabajo de WebKit se hizo público en iOS 8 (y por lo que los destinatarios de iOS 7 se producirá este error; se debe tener como destino iOS 8 para usar WebKit en la aplicación).



## <a name="related-links"></a>Vínculos relacionados

- [información de versión de compatibilidad de iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Actualizar las aplicaciones de Xamarin.iOS a iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
