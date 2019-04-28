---
title: Compatibilidad con iOS 9
description: Incluso si no va a agregar las características de iOS 9 a la aplicación inmediatamente, debe volver a generar sus aplicaciones con la versión más reciente de Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6ade1c05c8e1cc64a4d24df1284d86175083ab80
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293657"
---
# <a name="ios-9-compatibility"></a>Compatibilidad con iOS 9

_Incluso si no va a agregar las características de iOS 9 a la aplicación inmediatamente, debe volver a generar sus aplicaciones con la versión más reciente de Xamarin._

> [!IMPORTANT]
> La información de esta página es para los clientes con las aplicaciones ya se encuentran en la aplicación de Store compatibles con iOS 8 o versiones anteriores, que no ya ha enviado las actualizaciones para la compatibilidad de iOS 9. Si ya usa las versiones más recientes: Xcode 7 y 9 Xamarin.iOS - para el desarrollo de aplicaciones, visite la [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Cuando aparecieron la primera beta de iOS 9, hemos identificado dos problemas con versiones anteriores de Xamarin que aparece como aplicaciones más antiguas que no se pueda iniciar en iOS 9.

Estos dos problemas (como [detallada en nuestros foros](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) eran:

- Compilación aplicaciones para iOS 8 o una versión anterior de no poder iniciar en dispositivos de 32 bits (incluidas las aplicaciones compiladas con la [Unified API](~/cross-platform/macios/unified/index.md)).
- No se especifica con la ruta de acceso completa de P/Invoke.

Actualización de la instalación de Xamarin a la más reciente versión de canal estable y, a continuación, volver a generar y volver a implementar sus aplicaciones, corrige estos dos problemas.

_Incluso si no piensa actualizar inmediatamente la aplicación con las características de iOS 9, se recomienda volver a compilar con la versión más reciente de Xamarin y volver a enviar a la aplicación de Store_.



Esto garantizará que la aplicación se ejecute en iOS 9 después de la actualización de sus clientes.
Puede seguir compatible con iOS 8: volver a compilar con la versión más reciente no afecta a la versión de destino de la aplicación.

Si tiene otros problemas al probar las aplicaciones existentes en iOS 9, lea el [compatibilidad mejora](#compat) sección más adelante.


### <a name="updating-with-visual-studio"></a>Actualización con Visual Studio

Se recomienda que explícitamente Compruebe que Visual Studio se actualiza a la última versión estable.

## <a name="what-about-components-nugets-and-other-libraries"></a>¿Qué sucede con otras bibliotecas, componentes y paquetes NuGet?

De lo **no** deba esperar nuevas versiones de los componentes o paquetes de NuGet que usa para abordar los dos problemas mencionados anteriormente.
Basta con volver a compilar la aplicación con la última versión estable de Xamarin.iOS haya solucionado estos problemas.

De igual forma, proveedores de componentes y los autores de Nuget son **no** deberán enviar nuevas compilaciones para corregir los dos problemas mencionados anteriormente. Sin embargo, si existe un componente o Nuget usa `UICollectionView` o cargue las vistas de **Xib** archivos, una actualización *puede* se han necesitado para tratar los problemas de compatibilidad de iOS 9 que se mencionan a continuación.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Mejora de la compatibilidad en el código

Hay algunos casos de código que patrones *usa* para trabajar en versiones anteriores de iOS importantes en iOS 9. Estos son algunos posibles problemas (y sus soluciones) que pueden surgir al realizar pruebas en iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView es nulo en constructores

**Motivo:** En iOS 9 el `initWithFrame:` constructor ahora es necesario debido a cambios de comportamiento en iOS 9 como el [UICollectionView Estados de documentación](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si ha registrado una clase para el identificador especificado y se debe crear una nueva celda, la celda se ha inicializado mediante una llamada a su `initWithFrame:` método.

**Fix:** Agregar el `initWithFrame:` constructor como este:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Muestras relacionadas: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView produce un error init con el codificador al cargar una vista desde un Xib/Nib

**Motivo:** El `initWithCoder:` constructor es lo que se llama cuando la carga de una vista desde un archivo Xib de generador de interfaz. Si este constructor no se exporta a código no administrado no puede llamar a nuestra versión administrada del mismo. Anteriormente (p ej. en iOS 8) el `IntPtr` se invoca el constructor para inicializar la vista.

**Fix:** Crear y exportar el `initWithCoder:` constructor como este:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplo relacionado: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Mensaje Dyld: ninguna imagen de la caché con nombre...

Puede experimentar un bloqueo con la siguiente información en el registro:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Se trata de un error en el enlazador nativo de Apple, lo que sucede cuando los gobiernos realizan un marco de trabajo privada a pública (JavaScriptCore se hizo público en iOS 7, antes que era un marco de trabajo privado), y el destino de implementación de la aplicación es para una versión de iOS cuando el marco de trabajo era privado. En este caso se vinculará el vinculador de Apple con la versión de framework en lugar de la versión pública privada.

**Fix:** Esto se solucionará para iOS 9, pero no hay una solución sencilla que se puede aplicar a usted mismo mientras tanto: simplemente tener como destino una versión posterior de iOS en el proyecto (puede intentar iOS 7 en este caso). Otros marcos de trabajo pueden presentar problemas similares, por ejemplo el marco de trabajo de WebKit se hizo público en iOS 8 (y por lo que como destino iOS 7 se producirá este error; se debe tener como destino iOS 8 para usar WebKit en la aplicación).



## <a name="related-links"></a>Vínculos relacionados

- [información de versión de compatibilidad de iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Actualizar las aplicaciones de Xamarin.iOS para IOS 9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
