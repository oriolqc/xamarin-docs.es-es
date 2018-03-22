---
title: Actualizar aplicaciones de iOS existente
description: "Siga estos pasos para actualizar una aplicación de Xamarin.iOS existente para usar la API unificada."
ms.topic: article
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4eaa486fddc23ad18670bef4043a5adf30a1e9ac
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="updating-existing-ios-apps"></a>Actualizar aplicaciones de iOS existente

_Siga estos pasos para actualizar una aplicación de Xamarin.iOS existente para usar la API unificada._

Actualizar una aplicación existente para usar la API unificada requiere cambios en el propio archivo de proyecto, así como para los espacios de nombres y las API que se usan en el código de aplicación.

## <a name="the-road-to-64-bits"></a>Viaje a 64 Bits

Las nuevas API unificada necesarios para admitir arquitecturas de dispositivos de 64 bits desde una aplicación móvil Xamarin.iOS. A partir del 1 de febrero de 2015 Apple requiere que todos los envíos de aplicaciones nueva a la tienda de aplicaciones de iTunes compatible con arquitecturas de 64 bits.

Xamarin ofrece herramientas para Visual Studio para Mac y Visual Studio automatizar el proceso de migración de la API clásica a la API unificada o puede convertir los archivos de proyecto manualmente. Mientras que el uso de las herramientas automática es sugerido, este artículo explica ambos métodos.

### <a name="before-you-start"></a>Antes de empezar a...

Antes de actualizar el código existente a la API unificada, se recomienda eliminar todos *advertencias de compilación*. Muchos *advertencias* en la API clásica se convertirán en errores cuando haya migrado a unificado. Corregirlos antes de empezar es más fácil porque los mensajes del compilador a través de la API clásica suelen ofrecen sugerencias acerca de lo que actualizar.

## <a name="automated-updating"></a>Automatizar la actualización

Una vez que se han corregido las advertencias, seleccione un proyecto existente de iOS en Visual Studio para Mac o en Visual Studio y elija **migrar a API unificada de Xamarin.iOS** desde el **proyecto** menú. Por ejemplo:

![](updating-ios-apps-images/beta-tool1.png "Elegir migrar a API unificada de Xamarin.iOS desde el menú proyecto")

Debe aceptar esta advertencia antes de que se ejecutará la migración automática (Obviamente debe asegurarse de que tiene control de las copias de seguridad y de origen antes de embarcarse en esta adventure):

![](updating-ios-apps-images/beta-tool2.png "Acepte esta advertencia antes de que se ejecutará la migración automática")

Básicamente, la herramienta automatiza todos los pasos que se describen en la **actualización manualmente** sección presentados a continuación y es el método sugerido de convertir un proyecto existente de Xamarin.iOS a la API unificada.

## <a name="steps-to-update-manually"></a>Pasos para actualizar manualmente

Una vez más, una vez que se han corregido las advertencias, siga estos pasos para actualizar manualmente las aplicaciones existentes Xamarin.iOS para usar la nueva API unificada:

### <a name="1-update-project-type--build-target"></a>1. Tipo de proyecto de actualización y el destino de compilación

Cambiar el tipo de proyecto en el **csproj** archivos de `6BC8ED88-2882-458C-8E55-DFD12B67127B` a `FEACFBD2-3405-455C-9665-78FE426C6842`. Editar la **csproj** archivo en un editor de texto, reemplazando el primer elemento de la `<ProjectTypeGuids>` elemento tal como se muestra:

![](updating-ios-apps-images/csproj.png "Edite el archivo csproj en un editor de texto, reemplazando el primer elemento en el elemento ProjectTypeGuids, tal como se muestra")

Cambiar el **importación** elemento que contiene `Xamarin.MonoTouch.CSharp.targets` a `Xamarin.iOS.CSharp.targets` tal como se muestra:

![](updating-ios-apps-images/csproj2.png "Cambiar el elemento de importación que contiene Xamarin.MonoTouch.CSharp.targets a Xamarin.iOS.CSharp.targets tal como se muestra")

### <a name="2-update-project-references"></a>2. Actualizar las referencias de proyecto

Expanda el proyecto de aplicación de iOS **referencias** nodo. Inicialmente, se mostrará un * roto - **monotouch** referencia similar a esta captura de pantalla (dado que se acaba de cambiar el tipo de proyecto):

![](updating-ios-apps-images/references.png "Mostrarán inicialmente una referencia monotouch rotos similar a esta captura de pantalla porque ha cambiado el tipo de proyecto")

Haga doble clic en el proyecto de aplicación de iOS a **editar referencias**, a continuación, haga clic en el **monotouch** hacen referencia a y haga clic en el botón "X" roja.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Haga doble clic en el proyecto de aplicación de iOS para editar referencias, a continuación, haga clic en la referencia de monotouch y eliminarla mediante el rojo botón X")

Ahora, desplácese hasta el final de la lista de referencias y graduación el **Xamarin.iOS** ensamblado.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Ahora, desplácese hasta el final de la lista de referencias y el ensamblado de Xamarin.iOS de graduación")

Presione **Aceptar** para guardar los cambios de referencias de proyecto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Quitar MonoTouch de espacios de nombres

Quitar el **MonoTouch** prefijo de espacios de nombres de `using` instrucciones o, donde un classname ha sido totalmente calificado (p. ej. `MonoTouch.UIKit` se convierte en solo `UIKit`).

### <a name="4-remap-types"></a>4. Asignar tipos de

[Los tipos nativos](~/cross-platform/macios/nativetypes.md) han sido introdujo que reemplazar algunos tipos que se han utilizado anteriormente, como las instancias de `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (por ejemplo). La lista completa de tipos puede encontrarse en el [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

### <a name="5-fix-method-overrides"></a>5. Corrija las invalidaciones de método

Algunos `UIKit` métodos han tenido su firma cambió para usar el nuevo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Si las subclases personalizadas invalidan estos métodos las firmas ya no coincidirán y se producirán errores. Corregir estas invalidaciones de método cambiando la subclase para que coincida con la nueva firma utilizar tipos nativos.

Algunos ejemplos incluyen el cambio de `public override int NumberOfSections (UITableView tableView)` para devolver `nint` y cambiar ambos devuelven el tipo y los tipos de parámetro en `public override int RowsInSection (UITableView tableView, int section)` a `nint`.

## <a name="considerations"></a>Consideraciones

Deben tener en cuenta las siguientes consideraciones al convertir un proyecto existente de Xamarin.iOS de la API clásica a la nueva API unificada si esa aplicación se basa en el componente o paquete de NuGet.

### <a name="components"></a>Componentes

Cualquier componente que se ha incluido en la aplicación tendrá que actualizarse a la API unificado o se producirá un conflicto cuando intente compilar. Para cualquier componente incluye, reemplaza la versión actual con una versión nueva desde el almacén de componentes de Xamarin que es compatible con la API unificada y realice una compilación limpia. Cualquier componente que aún no se ha convertido el autor, se mostrará una advertencia sólo en el almacén de componentes de 32 bits.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Mientras se han contribuido cambios de NuGet para que funcione con la compatibilidad de API unificada, aún no ha recibido una nueva versión de NuGet, por lo que estamos evaluando cómo obtener NuGet para que reconozca las nuevas API.

Hasta ese momento, igual que los componentes, debe cambiar los paquetes de NuGet se han incluido en el proyecto a una versión que admite las API unificada y realice una compilación limpia posteriormente.

> [!IMPORTANT]
> Si hay un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, versión = 0.0.000, Referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificado, suele ser debido a que un componente o un paquete de NuGet en el proyecto que no se ha actualizado a la API unificada. Debe quitar el componente/NuGet existente, actualice a una versión que admite las API unificada y realice una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitación de 64 bits la compilación de Xamarin.iOS aplicaciones

Para una aplicación móvil de Xamarin.iOS que se ha convertido en la API unificada, el desarrollador debe seguir habilitar la creación de la aplicación para equipos de 64 bits de las opciones de la aplicación. Vea la **habilitación de 64 bits compilaciones de Xamarin.iOS aplicaciones de** de la [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) generaciones de documentos para obtener instrucciones detalladas sobre la habilitación de 64 bits.

## <a name="finishing-up"></a>Finalizar la reunión

Si no desea utilizar el método automático o manual para convertir la aplicación Xamarin.iOS de clásico a las API unificada, hay varias instancias que aún más, requerirá intervención manual. Visite nuestro [sugerencias para actualizar el código a la API unificada](~/cross-platform/macios/unified/updating-tips.md) documentos para los problemas conocidos y soluciones alternativas.

## <a name="related-links"></a>Vínculos relacionados

- [Sugerencias para actualizar el código a Unified API](~/cross-platform/macios/unified/updating-tips.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Migrar a la API unificada (vídeo)](http://university.xamarin.com/lightninglectures/migrating-to-the-unified-api)
