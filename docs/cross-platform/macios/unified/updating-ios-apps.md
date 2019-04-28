---
title: Actualización de aplicaciones iOS existentes
description: Este documento describe los pasos que se deben seguir para actualizar una aplicación de Xamarin.iOS desde la API clásica a Unified API.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4d506232903d4a94ac20a1fb9f93a39884d9099c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213717"
---
# <a name="updating-existing-ios-apps"></a>Actualización de aplicaciones iOS existentes

_Siga estos pasos para actualizar una aplicación de Xamarin.iOS existente para usar la API unificada._

Actualizar una aplicación existente para usar la API unificada requiere cambios en el archivo del proyecto, así como para los espacios de nombres y las API que se usan en el código de aplicación.

## <a name="the-road-to-64-bits"></a>El camino a 64 Bits

Las nuevas API unificada son necesarios para admitir arquitecturas de dispositivo de 64 bits desde una aplicación móvil Xamarin.iOS. A partir del 1 de febrero de 2015, Apple requiere que todos los envíos de aplicaciones nuevo para el Store de iTunes App con arquitecturas de 64 bits.

Xamarin ofrece herramientas para Visual Studio para Mac y Visual Studio automatizar el proceso de migración de la API clásica a Unified API o puede convertir los archivos de proyecto manualmente. Aunque el uso de las herramientas automáticas se sugerido, este artículo tratan ambos métodos.

### <a name="before-you-start"></a>Antes de empezar …

Antes de actualizar el código existente a Unified API, se recomienda encarecidamente que eliminará todos *advertencias de compilación*. Muchos *advertencias* en la API clásica se convertirán en errores cuando haya migrado a unificado. Corregirlos errores antes de empezar es más fácil porque los mensajes del compilador de la API clásica suelen ofrecen sugerencias sobre lo que actualizar.

## <a name="automated-updating"></a>Automatizar la actualización

Una vez que se han corregido las advertencias, seleccione un proyecto existente de iOS en Visual Studio para Mac o Visual Studio y elija **migrar a Unified API de Xamarin.iOS** desde el **proyecto** menú. Por ejemplo:

![](updating-ios-apps-images/beta-tool1.png "Elegir migrar a Unified API de Xamarin.iOS en el menú proyecto")

Deberá acepte esta advertencia antes de ejecuta la migración automatizada (Obviamente, debe asegurarse de tener control de las copias de seguridad y de origen antes de embarcarse en esta adventure):

![](updating-ios-apps-images/beta-tool2.png "Acepte esta advertencia antes de ejecuta la migración automática")

Básicamente, la herramienta automatiza todos los pasos descritos en la **actualización manualmente** sección presentados a continuación y es el método sugerido de convertir un proyecto de Xamarin.iOS existente a Unified API.

## <a name="steps-to-update-manually"></a>Pasos para actualizar manualmente

Nuevamente, una vez que se han corregido las advertencias, siga estos pasos para actualizar manualmente las aplicaciones de Xamarin.iOS para usar la nueva Unified API:

### <a name="1-update-project-type--build-target"></a>1. Tipo de proyecto de actualización & destino de compilación

Cambiar el tipo del proyecto en su **csproj** archivos desde `6BC8ED88-2882-458C-8E55-DFD12B67127B` a `FEACFBD2-3405-455C-9665-78FE426C6842`. Editar el **csproj** archivo en un editor de texto, reemplazando el primer elemento de la `<ProjectTypeGuids>` elemento tal como se muestra:

![](updating-ios-apps-images/csproj.png "Edite el archivo csproj en un editor de texto, reemplazando el primer elemento en el elemento ProjectTypeGuids tal como se muestra")

Cambiar el **importación** elemento que contiene `Xamarin.MonoTouch.CSharp.targets` a `Xamarin.iOS.CSharp.targets` tal como se muestra:

![](updating-ios-apps-images/csproj2.png "Cambiar el elemento Import que contiene Xamarin.MonoTouch.CSharp.targets a Xamarin.iOS.CSharp.targets tal como se muestra")

### <a name="2-update-project-references"></a>2. Actualizar las referencias de proyecto

Expanda el proyecto de aplicación de iOS **referencias** nodo. Inicialmente, se mostrará un * roto - **monotouch** referencia similar a esta captura de pantalla (porque se acaba de cambiar el tipo de proyecto):

![](updating-ios-apps-images/references.png "Mostrarán inicialmente una referencia monotouch divide similar a esta captura de pantalla porque puede cambiar el tipo de proyecto")

Haga doble clic en el proyecto de aplicación de iOS a **editar referencias**, a continuación, haga clic en el **monotouch** hacen referencia y elimínela con el botón "X" roja.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Haga doble clic en el proyecto de aplicación de iOS para editar referencias, a continuación, haga clic en la referencia de monotouch y eliminarlo con el color rojo botón X")

Ahora desplácese hasta el final de la lista de referencias y marca el **Xamarin.iOS** ensamblado.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Ahora desplácese hasta el final de la lista de referencias y el ensamblado Xamarin.iOS de graduación")

Presione **Aceptar** para guardar los cambios de las referencias de proyecto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Quitar MonoTouch de espacios de nombres

Quitar el **MonoTouch** prefijo de espacio de nombres de `using` instrucciones o siempre que un valor de classname ha sido totalmente calificado (p ej. `MonoTouch.UIKit` se convierte en solo `UIKit`).

### <a name="4-remap-types"></a>4. Asignar tipos

[Tipos nativos](~/cross-platform/macios/nativetypes.md) han sido introdujo que reemplace algunos tipos que se utilizaron con anterioridad, como las instancias de `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (por ejemplo). La lista completa de tipos puede encontrarse en el [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

### <a name="5-fix-method-overrides"></a>5. Corregir las invalidaciones de método

Algunos `UIKit` métodos han tenido su firma cambió para usar el nuevo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Si las subclases personalizadas invalidación estos métodos las firmas dejarán de coincidir con y se producirán errores. Corregir estas invalidaciones de método cambiando la subclase para que coincida con la nueva firma con tipos nativos.

Ejemplos incluyen cambiar `public override int NumberOfSections (UITableView tableView)` para devolver `nint` y cambiar ambos devuelven el tipo y los tipos de parámetro en `public override int RowsInSection (UITableView tableView, int section)` a `nint`.

## <a name="considerations"></a>Consideraciones

Las siguientes consideraciones se deben tener en cuenta al convertir un proyecto de Xamarin.iOS existente de la API clásica a la nueva Unified API si esa aplicación se basa en uno o varios componentes o paquetes de NuGet.

### <a name="components"></a>Componentes

También tendrá que actualizarse a Unified API de cualquier componente que se ha incluido en la aplicación o se producirá un conflicto al intentar compilar. Para cualquier componente incluido, reemplace la versión actual con una nueva versión desde el Store del componente de Xamarin que es compatible con la API unificada y realizar una compilación limpia. Cualquier componente que aún no se ha convertido el autor, se mostrará una advertencia solo en el almacén de componentes de 32 bits.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Mientras nos ha aportado los cambios de NuGet para trabajar con el soporte técnico de Unified API, no ha habido una nueva versión de NuGet, por lo que estamos evaluando la obtención de NuGet para reconocer las nuevas API.

Hasta ese momento, al igual que los componentes, deberá cambiar cualquier paquete de NuGet se han incluido en el proyecto a una versión que admite las API unificada y realizar después una compilación limpia.

> [!IMPORTANT]
> Si tiene un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' se hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, versión = 0.0.000, Referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificada, suele ser debido a que un componente o el paquete NuGet en el proyecto que no se ha actualizado a Unified API. Deberá quitar el componente o NuGet existente, actualice a una versión que admite las API unificada y realizar una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.iOS

Para una aplicación móvil Xamarin.iOS que se ha convertido a Unified API, el desarrollador debe seguir habilitar la compilación de la aplicación para equipos de 64 bits de las opciones de la aplicación. Consulte la **habilitación de 64 bits se basa de aplicaciones de Xamarin.iOS** de la [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) generaciones de documentos para obtener instrucciones detalladas sobre la habilitación de 64 bits.

## <a name="finishing-up"></a>Finalizando

Si decide usar el método manual o automático para convertir la aplicación de Xamarin.iOS de clásica a la API unificada, hay varias instancias que aún más, requerirán una intervención manual. Consulte nuestra [sugerencias para actualizar código a Unified API](~/cross-platform/macios/unified/updating-tips.md) documentar los problemas conocidos y soluciones alternativas.

## <a name="related-links"></a>Vínculos relacionados

- [Sugerencias para actualizar el código a Unified API](~/cross-platform/macios/unified/updating-tips.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Migrar a Unified API de (vídeo)](http://university.xamarin.com/lightninglectures/migrating-to-the-unified-api)
