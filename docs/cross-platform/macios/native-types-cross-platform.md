---
title: Trabajo con tipos nativos en aplicaciones multiplataforma
description: En este artículo se describe el uso de iOS nuevos tipos nativos de API unificada (nint, nuint, nfloat) en una aplicación multiplataforma donde se comparte código con los dispositivos que no son iOS como Android o sistemas operativos de Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: 489d2a76e6eff661360b24d1872ed1343c74b85e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261186"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Trabajo con tipos nativos en aplicaciones multiplataforma

_En este artículo se describe el uso de iOS nuevos tipos nativos de API unificada (nint, nuint, nfloat) en una aplicación multiplataforma donde se comparte código con los dispositivos que no son iOS como Android o sistemas operativos de Windows Phone._


Los tipos nativos de 64 tipos funcionan con las API de Mac y iOS. Si está escribiendo código compartido que se ejecuta en Android o Windows, así, deberá administrar la conversión de tipos unificado en los tipos .NET habituales que puede compartir.

Este documento describe diferentes maneras para interoperar con la API unificada desde el código común compartido.

## <a name="when-to-use-the-native-types"></a>Cuándo usar los tipos nativos

Xamarin.iOS y Xamarin.Mac Unified API todavía incluyen la `int`, `uint` y `float` tipos de datos, así como el `RectangleF`, `SizeF` y `PointF` tipos. Estos tipos de datos existente deben continuar para usarse en cualquier código compartido multiplataforma. Los nuevos tipos de datos nativos solo deben usarse al realizar una llamada a una API de iOS o Mac donde la compatibilidad con tipos de arquitectura son necesarios.

Según la naturaleza del código que se comparte, puede haber ocasiones donde código multiplataforma que tenga que tratar con la `nint`, `nuint` y `nfloat` tipos de datos. Por ejemplo: una biblioteca que controla las transformaciones de datos rectangulares que estaba utilizando `System.Drawing.RectangleF` compartir la funcionalidad entre versiones de Xamarin.iOS y Xamarin.Android de una aplicación, deberá actualizarse para controlar los tipos nativos en iOS.

Cómo se administran estos cambios depende del tamaño y complejidad de la aplicación y el formulario de código compartido que se ha utilizado, como veremos en las siguientes secciones.

## <a name="code-sharing-considerations"></a>Consideraciones de uso compartido de código

Como se indica en la [opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) documento, hay dos formas principales para compartir código entre proyectos multiplataforma: Compartir proyectos y bibliotecas de clases portables. Cuál de los dos tipos se ha usado, se limitarán las opciones disponibles cuando se administran los tipos de datos nativos de código multiplataforma.

### <a name="portable-class-library-projects"></a>Proyectos de biblioteca de clases Portable

Una biblioteca de clases Portable (PCL) le permite para tener como destino las plataformas que desea admitir y usar Interfaces para proporcionar funcionalidad específica de la plataforma.

Puesto que el tipo de proyecto de PCL se compila hasta un `.DLL` y no tiene ningún sentido de la API unificada, tendrá que seguir usando los tipos de datos existente (`int`, `uint`, `float`) en la PCL código fuente y el tipo de conversión de las llamadas a la PCL las clases y métodos en las aplicaciones front-end. Por ejemplo:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Proyectos compartidos

El tipo de proyecto de activos compartidos permite organizar el código fuente en un proyecto independiente que, a continuación, obtiene incluyen y se compila en las aplicaciones individuales específicos de la plataforma de front-end y usar `#if` directivas de compilador según sea necesario para administrar requisitos específicos de la plataforma.

El tamaño y la complejidad de la parte delantera terminan las aplicaciones móviles que están consumiendo el código compartido, junto con el tamaño y la complejidad del código que se comparte, debe tenerse en cuenta al elegir el método de la compatibilidad con datos nativos tipos multiplataforma con el Tipo de proyecto de recursos compartido.

En función de estos factores, los siguientes tipos de soluciones se podrían implementar mediante la `if __UNIFIED__ ... #endif` directivas de compilador para controlar los cambios específicos de API unificada para el código.

#### <a name="using-duplicate-methods"></a>Uso de métodos duplicados

Tome como ejemplo una biblioteca que está realizando las transformaciones de datos rectangulares indicados anteriormente. Si la biblioteca solo contiene uno o dos métodos muy sencillos, puede crear versiones duplicadas de esos métodos para Xamarin.iOS y Xamarin.Android. Por ejemplo:

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

En el código anterior, puesto que el `CalculateArea` rutina es muy sencilla, hemos utilizado la compilación condicional y creó una independiente, la versión de API unificada del método. Por otro lado, si la biblioteca contiene muchas rutinas o varias rutinas complejas, esta solución no sería factible, ya que podría suponer un problema de mantener todos los métodos de sincronización para las modificaciones o correcciones de errores.

#### <a name="using-method-overloads"></a>Utilizando el método de sobrecarga

En ese caso, podría ser la solución crear una versión de la sobrecarga de los métodos con tipos de datos de 32 bits para que tomen ahora `CGRect` como parámetro o valor devuelto, convertir ese valor a un `RectangleF` (sabiendo que convertir de `nfloat` a `float` es una conversión con pérdida de datos) y llamar a la versión original de la rutina para realizar el trabajo real. Por ejemplo:

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

Nuevamente, esto es una buena solución siempre y cuando la pérdida de precisión no afecta a los resultados de las necesidades específicas de su aplicación.

#### <a name="using-alias-directives"></a>Directivas de Alias Using

Para las áreas donde la pérdida de precisión es un problema, otra posible solución consiste en usar `using` directivas para crear un alias para los tipos de datos nativos y CoreGraphics incluyendo el código siguiente en la parte superior de los archivos de código fuente compartido y convierte uno es necesario `int`, `uint` o `float` valores `nint`, `nuint` o `nfloat`:

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

Tenga en cuenta que aquí se ha cambiado el `CalculateArea` devuelto del método un `nfloat` en lugar del estándar `float`. Esto se hace para que no se obtendría un error de compilación al intentar _implícitamente_ convertir el `nfloat` resultado del cálculo (ya que son los dos valores que se va a multiplicar `nfloat`) en un `float` valor devuelto.

Si el código se compila y ejecuta en un dispositivo que no sean Unified API, el `using nfloat = global::System.Single;` asigna el `nfloat` a un `Single` que convertirá implícitamente a un `float` lo que permite la aplicación consumidora front-end llamar a la `CalculateArea` método sin modificación.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Uso de conversiones de tipos en la aplicación de Front-End

En caso de que las aplicaciones de front-end sólo realiza una serie de llamadas a la biblioteca de código compartido, otra solución podría ser que salir de la biblioteca sin cambios y conversión de tipos en la aplicación de Xamarin.iOS o Xamarin.Mac cuando se llama a la rutina existente. Por ejemplo:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Si la aplicación que realiza cientos de llamadas a la biblioteca de código compartido, de nuevo, no sería una buena solución.

Según la arquitectura de la aplicación, se podría acabar con una o varias de las soluciones anteriores para admitir datos nativos (si es necesario) los tipos en nuestro código multiplataforma.


## <a name="xamarinforms-applications"></a>Aplicaciones de Xamarin.Forms

Para usar Xamarin.Forms para las interfaces de usuario multiplataforma que también se compartirá con una aplicación de API unificada, es necesario lo siguiente:

- Toda la solución debe usar la versión 1.3.1 (o superior) del paquete de NuGet Xamarin.Forms.
- Para cualquier procesamientos personalizados de Xamarin.iOS, use los mismos tipos de soluciones presentadas más arriba en función de cómo ha sido el código de interfaz de usuario compartida (proyecto compartido o PCL).

Como se muestra en una aplicación multiplataforma estándar, 32 bits existentes tipos de datos deben usarse en cualquier código compartido multiplataforma para la mayoría de todas las situaciones. Los nuevos tipos de datos nativo solo debe usarse al realizar una llamada a una API de iOS o Mac donde la compatibilidad con tipos de arquitectura son necesarios.

Para obtener más información, consulte nuestra [actualizar aplicaciones de Xamarin.Forms existente](https://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) documentación.

## <a name="summary"></a>Resumen

En este artículo, tenemos vea cuando debemos usar los tipos de datos nativos en una aplicación de API unificada y su implicaciones multiplataforma. Hemos presentado varias soluciones que pueden usarse en situaciones donde se debe usar los nuevos tipos de datos nativos de bibliotecas multiplataforma. Y hemos visto a una guía rápida para admitir las API unificada en las aplicaciones de Xamarin.Forms multiplataforma.



## <a name="related-links"></a>Vínculos relacionados

- [Unified API](~/cross-platform/macios/unified/index.md)
- [Tipos nativos](~/cross-platform/macios/nativetypes.md)
- [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Ejemplo de uso compartido de código](https://developer.xamarin.com/samples/mobile/SharingCode/)
