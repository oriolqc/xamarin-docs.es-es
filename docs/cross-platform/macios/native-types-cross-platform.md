---
title: Trabajar con tipos nativos en aplicaciones multiplataforma
description: Este artículo incluye el uso de la nueva iOS tipos nativos de API unificada (nint, nuint, nfloat) en una aplicación multiplataforma donde el código se comparte con dispositivos de iOS no, como Android o sistemas operativos de Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: eb6979691eeef6dd7436d74fdfd501c747d9b3c6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918163"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Trabajar con tipos nativos en aplicaciones multiplataforma

_Este artículo incluye el uso de la nueva iOS tipos nativos de API unificada (nint, nuint, nfloat) en una aplicación multiplataforma donde el código se comparte con dispositivos de iOS no, como Android o sistemas operativos de Windows Phone._


Los tipos nativos de 64 tipos funcionan con iOS y Mac API. Si está escribiendo código compartido que ejecuta también en Android o Windows, debe administrar la conversión de tipos unificado en los tipos de .NET normales que se pueden compartir.

Este documento describen distintas formas de interactuar con la API unificada desde el código común compartido.

## <a name="when-to-use-the-native-types"></a>Cuándo utilizar los tipos nativos

Xamarin.iOS y las API unificada de Xamarin.Mac todavía incluyen la `int`, `uint` y `float` tipos de datos, así como el `RectangleF`, `SizeF` y `PointF` tipos. Estos tipos de datos existentes deben continuar para usarse en cualquier código compartido multiplataforma. Los nuevos tipos de datos nativos solo deben usarse al realizar una llamada a una API de Mac o iOS donde la compatibilidad con los tipos compatibles con la arquitectura son necesarios.

Según la naturaleza del código compartido, puede haber ocasiones en código multiplataforma tal vez necesite tratar con el `nint`, `nuint` y `nfloat` tipos de datos. Por ejemplo: una biblioteca que administra las transformaciones de datos rectangulares que estaba utilizando `System.Drawing.RectangleF` compartir la funcionalidad entre versiones Xamarin.iOS y Xamarin.Android de una aplicación, se debe actualizarse para controlar los tipos nativos en iOS.

Cómo se administran estos cambios depende del tamaño y complejidad de la aplicación y se ha utilizado el formato de código de acceso compartido, tal y como se verá en las siguientes secciones.

## <a name="code-sharing-considerations"></a>Consideraciones de uso compartido de código

Como se indica en la [opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) de documento, hay dos formas principales para compartir código entre los proyectos multiplataforma: compartir proyectos y bibliotecas de clases Portable. Cuál de los dos tipos se ha utilizado, se limitarán las opciones disponibles cuando se administran los tipos de datos nativos de código entre plataformas.

### <a name="portable-class-library-projects"></a>Proyectos de biblioteca de clases Portable

Una biblioteca de clases portables (PCL) permite a las plataformas que se va a admitir y usar Interfaces para proporcionar la funcionalidad específica de la plataforma de destino.

Puesto que se compila el tipo de proyecto de PCL hacia abajo hasta un `.DLL` y no tiene ningún sentido de la API unificada, tendrá que seguir usando los tipos de datos existentes (`int`, `uint`, `float`) en la PCL código fuente y el tipo convierten las llamadas a la PCLs las clases y métodos en las aplicaciones front-end. Por ejemplo:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Proyectos compartidos

El tipo de proyecto de recurso compartido permite organizar el código fuente en un proyecto independiente que, a continuación, obtiene incluyen y se compila en las aplicaciones individuales específica de la plataforma de front-end y usar `#if` según sea necesario para administrar las directivas de compilador requisitos específicos de la plataforma.

El tamaño y la complejidad de la parte delantera terminan las aplicaciones móviles que están consumiendo código compartido, junto con el tamaño y la complejidad del código compartido, debe tener en cuenta al elegir el método de datos nativos de compatibilidad de tipos entre plataformas con el Tipo de proyecto de recurso compartido.

En función de estos factores, los siguientes tipos de soluciones se puede implementar mediante la `if __UNIFIED__ ... #endif` directivas de compilador para controlar los cambios específicos de la API unificada para el código.

#### <a name="using-duplicate-methods"></a>Uso de métodos duplicados

Tome como ejemplo una biblioteca que realiza transformaciones en datos rectangulares indicadas anteriormente. Si la biblioteca solo contiene uno o dos métodos muy sencillas, puede elegir crear versiones duplicadas de estos métodos para Xamarin.iOS y Xamarin.Android. Por ejemplo:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

En el código anterior, puesto que la `CalculateArea` rutina es muy simple, hemos utilizado la compilación condicional y crear una versión de API unificada del método, pero independiente. Por otro lado, si la biblioteca contiene muchas rutinas o varias rutinas complejas, esta solución no sería viable, tal y como se producirían un problema mantener todos los métodos de sincronización para las modificaciones o correcciones de errores.

#### <a name="using-method-overloads"></a>Utilizando el método de sobrecarga

En ese caso, podría ser la solución crear una versión de sobrecarga de los métodos con los tipos de datos de 32 bits, de modo que ahora `CGRect` como parámetro o valor devuelto, convertir ese valor a un `RectangleF` (conocer dicha conversión de `nfloat` a `float` es una conversión con pérdida de datos) y llamar a la versión original de la rutina para hacer el trabajo real. Por ejemplo:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

            // Call original routine to calculate area
            return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

Una vez más, esto es una buena solución como la pérdida de precisión no afecta a los resultados de las necesidades específicas de su aplicación.

#### <a name="using-alias-directives"></a>Directivas using Alias

Para las áreas donde la pérdida de precisión es un problema, otra posible solución consiste en usar `using` directivas para crear un alias para los tipos de datos nativos y CoreGraphics, incluido el código siguiente a la parte superior de los archivos de código fuente compartido y convierte uno necesita `int`, `uint` o `float` valores `nint`, `nuint` o `nfloat`:

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

Por lo que, a continuación, se convierte en nuestro ejemplo de código:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

Tenga en cuenta que aquí hemos cambiado el `CalculateArea` método lo devuelva un `nfloat` en lugar de la norma `float`. Esto se hacía para que no se podría obtener un error de compilación al intentar _implícitamente_ convertir el `nfloat` resultado del cálculo (puesto que los dos valores que se va a multiplicar son `nfloat`) en un `float` valor devuelto.

Si el código se compila y se ejecuta en un dispositivo no API unificada, el `using nfloat = global::System.Single;` asigna el `nfloat` a una `Single` que se convertirá implícitamente a un `float` permitir que la aplicación front-end usada llamar a la `CalculateArea` método sin modificación.


#### <a name="using-type-conversions-in-the-front-end-app"></a>El uso de conversiones de tipo en la aplicación Front-End

En caso de que las aplicaciones front-end sólo realizan una serie de llamadas a la biblioteca de código compartido, otra solución podría ser dejar la biblioteca sin cambios y conversión de tipos en la aplicación Xamarin.iOS o Xamarin.Mac cuando se llama a la rutina existente. Por ejemplo:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Si la aplicación que consume realiza cientos de llamadas a la biblioteca de código compartido, este nuevo, podría no ser una buena solución.

En función de la arquitectura de nuestra aplicación, se tengamos que terminan con una o varias de las soluciones anteriores para admitir datos nativos tipos (si es necesario) en el código multiplataforma.


## <a name="xamarinforms-applications"></a>Aplicaciones de Xamarin.Forms

Para uso de Xamarin.Forms para interfaces de usuario entre plataformas que también se compartirá con una aplicación de API unificada, es necesario lo siguiente:

- Toda la solución debe contar con la versión 1.3.1 (o posterior) del paquete de NuGet Xamarin.Forms.
- Para cualquier representa Xamarin.iOS personalizado, use los mismos tipos de soluciones presentadas anteriormente en función de cómo el código de interfaz de usuario ha sido compartido (proyecto compartido o PCL).

Como en una aplicación multiplataforma estándar, los 32 bits existente tipos de datos deben usarse en cualquier código compartido multiplataforma para la mayoría de todas las situaciones. Los nuevos tipos de datos nativo solo debe usarse al realizar una llamada a una API de Mac o iOS donde la compatibilidad con los tipos compatibles con la arquitectura son necesarios.

Para obtener más información, consulte nuestro [actualizar aplicaciones existentes de Xamarin.Forms](http://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) documentación.

## <a name="summary"></a>Resumen

En este artículo tenemos vea cuando que debemos usar los tipos de datos nativos en una aplicación de API unificada y su implicaciones y multiplataforma. Nos hemos presentan varias soluciones que pueden utilizarse en situaciones donde se debe usar los nuevos tipos de datos nativos en bibliotecas multiplataforma. Y hemos visto a una guía rápida para admitir las API unificada en las aplicaciones entre plataformas de Xamarin.Forms.



## <a name="related-links"></a>Vínculos relacionados

- [Unified API](~/cross-platform/macios/unified/index.md)
- [Tipos nativos](~/cross-platform/macios/nativetypes.md)
- [Opciones de código compartido](~/cross-platform/app-fundamentals/code-sharing.md)
- [Ejemplo de uso compartido de código](https://developer.xamarin.com/samples/mobile/SharingCode/)
