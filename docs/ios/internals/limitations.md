---
title: Limitaciones
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a75d76ad1292955003705a5ddc1d52381addc796
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="limitations"></a>Limitaciones

Puesto que las aplicaciones en el iPhone con Xamarin.iOS se compilan en código estático, no es posible utilizar los servicios que requieren la generación de código en tiempo de ejecución.

Éstas son las limitaciones de Xamarin.iOS en comparación con el escritorio Mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>La compatibilidad con genéricos limitado

A diferencia de .NET/Mono tradicional, el código en el iPhone se compila estáticamente antelación en lugar de que se compila a petición mediante un compilador JIT.

De mono [AOT completa](http://www.mono-project.com/docs/advanced/aot/#full-aot) tecnología tiene algunas limitaciones con respecto a los genéricos, estos se deben a que se puede determinar por adelantado no cada posible genérica la creación de instancias en tiempo de compilación. Esto no es un problema para regular tiempos de ejecución de .NET o Mono como siempre se compile el código en tiempo de ejecución mediante el sólo en el compilador de tiempo. Pero esto supone un desafío para el compilador estático como Xamarin.iOS.

Algunos de los problemas comunes que los desarrolladores experimenta, incluyen:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Las subclases de NSObjects genérico están limitadas

Xamarin.iOS actualmente tiene compatibilidad limitada para crear subclases genéricas de la clase NSObject, por ejemplo, no se admite para métodos genéricos. A partir de 7.2.1, utilizando las subclases genéricas de NSObjects es posible, como esta:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Aunque las subclases genéricas de NSObjects son posibles, hay algunas limitaciones. Leer la [genéricas subclases de NSObject](~/ios/internals/api-design/nsobject-generics.md) documento para obtener más información



### <a name="pinvokes-in-generic-types"></a>P/invoca en tipos genéricos

P/Invokes en clases genéricas no se admiten:

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>No se admite Property.SetInfo en un tipo que acepta valores null

Uso Property.SetInfo de reflexión para establecer el valor en un tipo Nullable&lt;T&gt; no se admite actualmente.

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>Tipos de valor como claves de diccionario

Con un tipo de valor como un diccionario&lt;TKey, TValue&gt; clave es problemática, como el valor predeterminado el constructor de diccionario intenta usar EqualityComparer&lt;TKey&gt;. De forma predeterminada. EqualityComparer&lt;TKey&gt;. De manera predeterminada, a su vez, intenta usar la reflexión para crear instancias de un nuevo tipo que implementa el objeto IEqualityComparer&lt;TKey&gt; interfaz.

Esto funciona para los tipos de referencia (como la reflexión + crear un nuevo paso de tipo se omite), pero en caso de valor tipos de bloqueos y lo grabe en su lugar rápidamente una vez que se intenta utilizar en el dispositivo.

 **Solución alternativa**: implementar manualmente el [IEqualityComparer&lt;TKey&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/) en un nuevo tipo de interfaz y proporcionar una instancia de ese tipo para el [diccionario&lt;TKey, TValue&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%3CTKey,TValue%3E/) [(IEqualityComparer&lt;TKey&gt;)](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/) constructor.


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>No se genera código dinámico

Puesto que el kernel del iPhone impide que una aplicación generando código dinámicamente Mono en el iPhone no admite ninguna forma de generación de código dinámico. Se incluyen los siguientes:

-  Espacio de nombres System.Reflection.Emit no está disponible.
-  No se admite para System.Runtime.Remoting.
-  No se admite para crear tipos dinámicamente (sin Type.GetType ("MyType" 1")), aunque buscar tipos existentes (Type.GetType ("System.String"), por ejemplo, funciona correctamente). 
-  Las devoluciones de llamada inversas deben registrarse con el tiempo de ejecución en tiempo de compilación.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

La falta de System.Reflection. **Emitir** significa que no funcionará ningún código que dependa de generación de código en tiempo de ejecución. Esto incluye cosas como:

-  Dynamic Language Runtime.
-  Los idiomas que se basa en Dynamic Language Runtime.
-  Remoting TransparentProxy o cualquier otra cosa que puede provocar que el tiempo de ejecución generar código de forma dinámica. 


 **Importante:** no confunda **Reflection.Emit** con **reflexión**. Reflection.Emit consiste en generar código de forma dinámica y tiene ese código JIT y el código compilado en código nativo. Debido a las limitaciones en el iPhone (ninguna compilación JIT) no se admite.

Pero la API de reflexión completa, incluidos Type.GetType ("someClass"), lista de métodos, lista de propiedades, atributos y valores de filas funcionará correctamente.

 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Invertir las devoluciones de llamada

En Mono estándar es posible pasar instancias de delegados de C# a código no administrado en lugar de un puntero a función. Normalmente, el tiempo de ejecución transformaría los punteros de función en un pequeño código thunk que permite que el código no administrado para volver a llamar a código administrado.

Just-in-Time en Mono se implementan estos puentes compilador. Al usar el compilador de anticipado requiere iPhone en este momento no hay dos limitaciones importantes:

-  Se deben marcar todos los métodos de devolución de llamada con el [MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  Los métodos deben ser métodos estáticos, no hay compatibilidad para la instancia métodos. 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>No hay comunicación remota

La pila de comunicación remota no está disponible en Xamarin.iOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>En tiempo de ejecución deshabilita características

Las siguientes características se han deshabilitado en iOS de Mono en tiempo de ejecución:

-  generador de perfiles
-  Reflection.Emit
-  Funcionalidad de Reflection.Emit.Save
-  Enlaces de COM
-  El motor JIT
-  Comprobador de metadatos (porque no hay ningún JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitaciones de la API de .NET

La API de .NET que se expone es un subconjunto del marco completo que no todo el contenido disponible en iOS. Consulte las preguntas más frecuentes para un [lista de ensamblados compatibles actualmente](~/cross-platform/internals/available-assemblies.md).



En concreto, el perfil de API utilizado por Xamarin.iOS no incluye System.Configuration, por lo que no es posible utilizar archivos XML externos para configurar el comportamiento del tiempo de ejecución.
