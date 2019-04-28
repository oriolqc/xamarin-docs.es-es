---
title: Actualización de aplicaciones Mac existentes
description: Este documento describe los pasos que se deben seguir para actualizar una aplicación de Xamarin.Mac desde la API clásica a Unified API.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5e6034b079bba5e884872e4f2096d677fd3641d0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213565"
---
# <a name="updating-existing-mac-apps"></a>Actualización de aplicaciones Mac existentes

Actualizar una aplicación existente para usar la API unificada requiere cambios en el archivo del proyecto, así como para los espacios de nombres y las API que se usan en el código de aplicación.

## <a name="the-road-to-64-bits"></a>El camino a 64 Bits

Las nuevas API unificada son necesarios para admitir arquitecturas de dispositivo de 64 bits desde una aplicación de Xamarin.Mac. A partir del 1 de febrero de 2015, Apple requiere que todos los envíos de aplicaciones nuevo para el Store de la aplicación de Mac con arquitecturas de 64 bits.

Xamarin ofrece herramientas para Visual Studio para Mac y Visual Studio automatizar el proceso de migración de la API clásica a Unified API o puede convertir los archivos de proyecto manualmente. Aunque el uso de las herramientas automáticas se sugerido, este artículo tratan ambos métodos.

### <a name="before-you-start"></a>Antes de empezar …

Antes de actualizar el código existente a Unified API, se recomienda encarecidamente que eliminará todos *advertencias de compilación*. Muchos *advertencias* en la API clásica se convertirán en errores cuando haya migrado a unificado. Corregirlos errores antes de empezar es más fácil porque los mensajes del compilador de la API clásica suelen ofrecen sugerencias sobre lo que actualizar.

## <a name="automated-updating"></a>Automatizar la actualización

Una vez que se han corregido las advertencias, seleccione un proyecto existente de Mac en Visual Studio para Mac o Visual Studio y elija **migrar a Unified API de Xamarin.Mac** desde el **proyecto** menú. Por ejemplo:

![](updating-mac-apps-images/beta-tool1.png "Elegir migrar a Unified API de Xamarin.Mac en el menú proyecto")

Deberá acepte esta advertencia antes de ejecuta la migración automatizada (Obviamente, debe asegurarse de tener control de las copias de seguridad y de origen antes de embarcarse en esta adventure):

![](updating-mac-apps-images/migrate01.png "Acepte esta advertencia antes de ejecuta la migración automática")

Hay dos tipos de .NET Framework de destino admitidos que pueden seleccionarse cuando se usa la API unificada en una aplicación de Xamarin.Mac:

- **Xamarin.Mac Mobile Framework** -se trata de la misma optimizada de .NET framework usada Xamarin.iOS y Xamarin.Android que admiten un subconjunto de las completas **Desktop** framework. Esto es el marco de trabajo recomendado, ya que proporciona más pequeños archivos binarios promedio debido al comportamiento de vinculación superior.
- **Xamarin.Mac .NET Framework 4.5** -este marco de trabajo nuevo, es un subconjunto de la **Desktop** framework. Sin embargo, recorta mucho menos del perfil de **Desktop** framework distinta de la **Mobile** framework y debe _"simplemente funcionan"_ con la mayoría de los paquetes de NuGet o 3rd bibliotecas de terceros. Esto permite al desarrollador consumir estándar **Desktop** ensamblados mientras sigue usando un marco de trabajo compatible, pero esta opción genera paquetes de aplicaciones más grandes. Esto es el marco recomendado donde se emplean 3rd ensamblados de .NET de terceros que no son compatibles con el **Xamarin.Mac Mobile Framework**. Para obtener una lista de ensamblados compatibles, consulte nuestra [ensamblados](~/cross-platform/internals/available-assemblies.md) documentación.

Para obtener información detallada sobre las plataformas de destino y las implicaciones de selección de un destino específico para la aplicación de Xamarin.Mac, consulte nuestra [plataformas de destino](~/mac/platform/target-framework.md) documentación. 

Básicamente, la herramienta automatiza todos los pasos descritos en la **actualización manualmente** sección presentados a continuación y es el método sugerido de convertir un proyecto de Xamarin.Mac existente a Unified API.

## <a name="steps-to-update-manually"></a>Pasos para actualizar manualmente

Nuevamente, una vez que se han corregido las advertencias, siga estos pasos para actualizar manualmente las aplicaciones de Xamarin.Mac para usar la nueva Unified API:

### <a name="1-update-project-type--build-target"></a>1. Tipo de proyecto de actualización & destino de compilación

Cambiar el tipo del proyecto en su **csproj** archivos desde `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` a `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Editar el **csproj** archivo en un editor de texto, reemplazando el primer elemento de la `<ProjectTypeGuids>` elemento tal como se muestra:

![](updating-mac-apps-images/csproj.png "Edite el archivo csproj en un editor de texto, reemplazando el primer elemento en el elemento ProjectTypeGuids tal como se muestra")

Cambiar el **importación** elemento que contiene `Xamarin.Mac.targets` a `Xamarin.Mac.CSharp.targets` tal como se muestra:

![](updating-mac-apps-images/csproj2.png "Cambiar el elemento Import que contiene Xamarin.Mac.targets a Xamarin.Mac.CSharp.targets tal como se muestra")

Agregue las siguientes líneas de código tras el `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Ejemplo:

![](updating-mac-apps-images/csproj3.png "Agregue estas líneas de código después del elemento < AssemblyName >")

### <a name="2-update-project-references"></a>2. Actualizar las referencias de proyecto

Expanda el proyecto de aplicación de Mac **referencias** nodo. Inicialmente, se mostrará un * roto - **XamMac** referencia similar a esta captura de pantalla (porque se acaba de cambiar el tipo de proyecto):

![](updating-mac-apps-images/references.png "Mostrarán inicialmente una referencia XamMac divide similar a esta captura de pantalla")

Haga clic en el **icono de engranaje** junto a la **XamMac** entrada y seleccione **eliminar** para quitar la referencia errónea.

A continuación, haga doble clic en el **referencias** carpeta en el **el Explorador de soluciones** y seleccione **editar referencias**. Desplácese hasta la parte inferior de la lista de referencias y coloque una marca de verificación además de **Xamarin.Mac**.

![](updating-mac-apps-images/references2.png "Desplácese hasta la parte inferior de la lista de referencias y coloque una marca de verificación además de Xamarin.Mac")

Presione **Aceptar** para guardar los cambios de las referencias de proyecto.

### <a name="3-remove-monomac-from-namespaces"></a>3. Quitar MonoMac de espacios de nombres

Quitar el **MonoMac** prefijo de espacio de nombres de `using` instrucciones o siempre que un valor de classname ha sido totalmente calificado (p ej. `MonoMac.AppKit` se convierte en solo `AppKit`).

### <a name="4-remap-types"></a>4. Asignar tipos

[Tipos nativos](~/cross-platform/macios/nativetypes.md) han sido introdujo que reemplace algunos tipos que se utilizaron con anterioridad, como las instancias de `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (por ejemplo). La lista completa de tipos puede encontrarse en el [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

### <a name="5-fix-method-overrides"></a>5. Corregir las invalidaciones de método

Algunos `AppKit` métodos han tenido su firma cambió para usar el nuevo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Si las subclases personalizadas invalidación estos métodos las firmas dejarán de coincidir con y se producirán errores. Corregir estas invalidaciones de método cambiando la subclase para que coincida con la nueva firma con tipos nativos. 

## <a name="considerations"></a>Consideraciones

Las siguientes consideraciones se deben tener en cuenta al convertir un proyecto de Xamarin.Mac existente de la API clásica a la nueva Unified API si esa aplicación se basa en uno o varios componentes o paquetes de NuGet. 

### <a name="components"></a>Componentes

También tendrá que actualizarse a Unified API de cualquier componente que se ha incluido en la aplicación o se producirá un conflicto al intentar compilar. Para cualquier componente incluido, reemplace la versión actual con una nueva versión desde el Store del componente de Xamarin que es compatible con la API unificada y realizar una compilación limpia. Cualquier componente que aún no se ha convertido el autor, se mostrará una advertencia solo en el almacén de componentes de 32 bits.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Mientras nos ha aportado los cambios de NuGet para trabajar con el soporte técnico de Unified API, no ha habido una nueva versión de NuGet, por lo que estamos evaluando la obtención de NuGet para reconocer las nuevas API. 

Hasta ese momento, al igual que los componentes, deberá cambiar cualquier paquete de NuGet se han incluido en el proyecto a una versión que admite las API unificada y realizar después una compilación limpia.

> [!IMPORTANT]
> Si tiene un error en el formulario _"Error 3 no puede incluir 'monomac.dll' y 'Xamarin.Mac.dll' en el mismo proyecto de Xamarin.Mac: 'Xamarin.Mac.dll' se hace referencia explícitamente, mientras que se hace referencia a 'monomac.dll' por ' xxx, versión = 0.0.000, referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificada, suele ser debido a que un componente o el paquete NuGet en el proyecto que no se ha actualizado a Unified API. Deberá quitar el componente o NuGet existente, actualice a una versión que admite las API unificada y realizar una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.Mac

Para una aplicación móvil de Xamarin.Mac que se ha convertido a Unified API, el desarrollador debe seguir habilitar la compilación de la aplicación para equipos de 64 bits de las opciones de la aplicación. Consulte la **habilitación de 64 bits compilaciones de las aplicaciones de Xamarin.Mac** de la [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md) generaciones de documentos para obtener instrucciones detalladas sobre la habilitación de 64 bits.
    
## <a name="finishing-up"></a>Finalizando

Si decide usar el método manual o automático para convertir la aplicación de Xamarin.Mac de clásica a la API unificada, hay varias instancias que aún más, requerirán una intervención manual. Consulte nuestra [sugerencias para actualizar código a Unified API](~/cross-platform/macios/unified/updating-tips.md) documentar los problemas conocidos y soluciones alternativas.

## <a name="related-links"></a>Vínculos relacionados

- [Sugerencias para actualizar el código a Unified API](~/cross-platform/macios/unified/updating-tips.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
