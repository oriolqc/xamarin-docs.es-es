---
title: Actualizar aplicaciones existentes de Mac
description: "Siga estos pasos para actualizar una aplicación de Xamarin.Mac existente para usar la API unificada."
ms.topic: article
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1967dea3b7f3a870950cdc7732292e9d4e24a1a8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="updating-existing-mac-apps"></a>Actualizar aplicaciones existentes de Mac

_Siga estos pasos para actualizar una aplicación de Xamarin.Mac existente para usar la API unificada._

Actualizar una aplicación existente para usar la API unificada requiere cambios en el propio archivo de proyecto, así como para los espacios de nombres y las API que se usan en el código de aplicación.

# <a name="the-road-to-64-bits"></a>Viaje a 64 Bits

Las nuevas API unificada necesarios para admitir arquitecturas de dispositivos de 64 bits desde una aplicación de Xamarin.Mac. A partir del 1 de febrero de 2015 Apple requiere que todos los envíos de aplicaciones nueva a la tienda de aplicaciones Mac compatible con arquitecturas de 64 bits.

Xamarin ofrece herramientas para Visual Studio para Mac y Visual Studio automatizar el proceso de migración de la API clásica a la API unificada o puede convertir los archivos de proyecto manualmente. Mientras que el uso de las herramientas automática es sugerido, este artículo explica ambos métodos.

## <a name="before-you-start"></a>Antes de empezar a...

Antes de actualizar el código existente a la API unificada, se recomienda eliminar todos *advertencias de compilación*. Muchos *advertencias* en la API clásica se convertirán en errores cuando haya migrado a unificado. Corregirlos antes de empezar es más fácil porque los mensajes del compilador a través de la API clásica suelen ofrecen sugerencias acerca de lo que actualizar.


# <a name="automated-updating"></a>Automatizar la actualización

Una vez que se han corregido las advertencias, seleccione un proyecto existente de Mac en Visual Studio para Mac o en Visual Studio y elija **migrar a API unificada de Xamarin.Mac** desde el **proyecto** menú. Por ejemplo:

![](updating-mac-apps-images/beta-tool1.png "Elegir migrar a API unificada de Xamarin.Mac desde el menú proyecto")

Debe aceptar esta advertencia antes de que se ejecutará la migración automática (Obviamente debe asegurarse de que tiene control de las copias de seguridad y de origen antes de embarcarse en esta adventure):

![](updating-mac-apps-images/migrate01.png "Acepte esta advertencia antes de que se ejecutará la migración automática")

Hay dos tipos de .NET Framework de destino compatibles que se pueden seleccionar cuando se usa la API unificada en una aplicación Xamarin.Mac:

- **Xamarin.Mac Mobile Framework** -se trata de la misma optimizadas .NET framework utilizada Xamarin.iOS y Xamarin.Android admiten un subconjunto de toda la **Desktop** framework. Esto es el marco de trabajo recomendado porque proporciona más pequeños archivos binarios promedio debido al comportamiento de vinculación superior.
- **Xamarin.Mac .NET 4.5 Framework** -este marco de trabajo de nuevo, es un subconjunto de la **Desktop** framework. Sin embargo, recorta mucho menor de toda la **Desktop** framework distinta de la **Mobile** framework y debe _"simplemente funcionan"_ con la mayoría de paquetes de NuGet o 3rd bibliotecas de entidad. Esto permite al desarrollador consumir estándar **Desktop** ensamblados mientras sigue usando un marco de trabajo admitida, pero esta opción genera mayor paquetes de aplicaciones. Esto es el marco recomendado donde están usando los ensamblados de .NET parte 3ª que no son compatibles con el **Xamarin.Mac Mobile Framework**. Para obtener una lista de ensamblados compatibles, consulte nuestro [ensamblados](~/cross-platform/internals/available-assemblies.md) documentación.

Para obtener información detallada sobre .NET Framework de destino y las implicaciones de selección de un destino específico de la aplicación Xamarin.Mac, consulte nuestro [.NET Framework de destino](~/mac/platform/target-framework.md) documentación. 

Básicamente, la herramienta automatiza todos los pasos que se describen en la **actualización manualmente** sección presentados a continuación y es el método sugerido de convertir un proyecto existente de Xamarin.Mac a la API unificada.

# <a name="steps-to-update-manually"></a>Pasos para actualizar manualmente

Una vez más, una vez que se han corregido las advertencias, siga estos pasos para actualizar manualmente las aplicaciones existentes Xamarin.Mac para usar la nueva API unificada:

## <a name="1-update-project-type--build-target"></a>1. Tipo de proyecto de actualización y el destino de compilación

Cambiar el tipo de proyecto en el **csproj** archivos de `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` a `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Editar la **csproj** archivo en un editor de texto, reemplazando el primer elemento de la `<ProjectTypeGuids>` elemento tal como se muestra:

![](updating-mac-apps-images/csproj.png "Edite el archivo csproj en un editor de texto, reemplazando el primer elemento en el elemento ProjectTypeGuids, tal como se muestra")

Cambiar el **importación** elemento que contiene `Xamarin.Mac.targets` a `Xamarin.Mac.CSharp.targets` tal como se muestra:

![](updating-mac-apps-images/csproj2.png "Cambiar el elemento de importación que contiene Xamarin.Mac.targets a Xamarin.Mac.CSharp.targets tal como se muestra")

Agregue las siguientes líneas de código después de la `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Ejemplo:

![](updating-mac-apps-images/csproj3.png "Agregue estas líneas de código después del elemento < AssemblyName >")


## <a name="2-update-project-references"></a>2. Actualizar las referencias de proyecto

Expanda el proyecto de aplicación de Mac **referencias** nodo. Inicialmente, se mostrará un * roto - **XamMac** referencia similar a esta captura de pantalla (dado que se acaba de cambiar el tipo de proyecto):

![](updating-mac-apps-images/references.png "Mostrarán inicialmente una referencia XamMac rotos similar a esta captura de pantalla")

Haga clic en el **icono de engranaje** junto a la **XamMac** entrada y seleccione **eliminar** para quitar la referencia errónea.

A continuación, haga doble clic en el **referencias** carpeta en el **el Explorador de soluciones** y seleccione **editar referencias**. Desplácese hasta la parte inferior de la lista de referencias y colocar una marca de verificación además **Xamarin.Mac**.

![](updating-mac-apps-images/references2.png "Desplácese hasta la parte inferior de la lista de referencias y colocar una marca de verificación además Xamarin.Mac")

Presione **Aceptar** para guardar los cambios de referencias de proyecto.

## <a name="3-remove-monomac-from-namespaces"></a>3. Quitar MonoMac de espacios de nombres

Quitar el **MonoMac** prefijo de espacios de nombres de `using` instrucciones o, donde un classname ha sido totalmente calificado (p. ej. `MonoMac.AppKit` se convierte en solo `AppKit`).

## <a name="4-remap-types"></a>4. Asignar tipos de

[Los tipos nativos](~/cross-platform/macios/nativetypes.md) han sido introdujo que reemplazar algunos tipos que se han utilizado anteriormente, como las instancias de `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (por ejemplo). La lista completa de tipos puede encontrarse en el [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

## <a name="5-fix-method-overrides"></a>5. Corrija las invalidaciones de método

Algunos `AppKit` métodos han tenido su firma cambió para usar el nuevo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Si las subclases personalizadas invalidan estos métodos las firmas ya no coincidirán y se producirán errores. Corregir estas invalidaciones de método cambiando la subclase para que coincida con la nueva firma utilizar tipos nativos. 

# <a name="considerations"></a>Consideraciones

Deben tener en cuenta las siguientes consideraciones al convertir un proyecto existente de Xamarin.Mac de la API clásica a la nueva API unificada si esa aplicación se basa en el componente o paquete de NuGet. 

## <a name="components"></a>Componentes

Cualquier componente que se ha incluido en la aplicación tendrá que actualizarse a la API unificado o se producirá un conflicto cuando intente compilar. Para cualquier componente incluye, reemplaza la versión actual con una versión nueva desde el almacén de componentes de Xamarin que es compatible con la API unificada y realice una compilación limpia. Cualquier componente que aún no se ha convertido el autor, se mostrará una advertencia sólo en el almacén de componentes de 32 bits.


## <a name="nuget-support"></a>Compatibilidad con NuGet

Mientras se han contribuido cambios de NuGet para que funcione con la compatibilidad de API unificada, aún no ha recibido una nueva versión de NuGet, por lo que estamos evaluando cómo obtener NuGet para que reconozca las nuevas API. 

Hasta ese momento, igual que los componentes, debe cambiar los paquetes de NuGet se han incluido en el proyecto a una versión que admite las API unificada y realice una compilación limpia posteriormente.

> [!IMPORTANT]
> **Nota:** si hay un error en el formulario _"Error 3 no puede incluir 'monomac.dll' y 'Xamarin.Mac.dll' en el mismo proyecto Xamarin.Mac: 'Xamarin.Mac.dll' hace referencia explícitamente, mientras que se hace referencia a 'monomac.dll' por ' xxx, Versión = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificado, suele ser debido a que un componente o un paquete de NuGet en el proyecto que no se ha actualizado a la API unificada. Debe quitar el componente/NuGet existente, actualice a una versión que admite las API unificada y realice una compilación limpia.




# <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.Mac

Para una aplicación móvil Xamarin.Mac en el que se ha convertido en la API unificada, el desarrollador debe seguir habilitar la creación de la aplicación para equipos de 64 bits de las opciones de la aplicación. Vea la **habilitación de 64 bits compilaciones de Xamarin.Mac aplicaciones de** de la [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64.md) generaciones de documentos para obtener instrucciones detalladas sobre la habilitación de 64 bits.
    
# <a name="finishing-up"></a>Finalizar la reunión

Si no desea utilizar el método automático o manual para convertir la aplicación Xamarin.Mac de clásico a las API unificada, hay varias instancias que aún más, requerirá intervención manual. Visite nuestro [sugerencias para actualizar el código a la API unificada](~/cross-platform/macios/unified/updating-tips.md) documentos para los problemas conocidos y soluciones alternativas.


## <a name="related-links"></a>Vínculos relacionados

- [Sugerencias para actualizar el código a la API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabajar con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
