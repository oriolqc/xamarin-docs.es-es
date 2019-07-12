---
title: Limitaciones de Xamarin.iOS
description: Este documento describe las limitaciones de Xamarin.iOS, explicar los genéricos, subclases genéricas de NSObjects, P/Invoke en los objetos genéricos y mucho más.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: 86f41bf227b97ef24651f482ff133a065226ee14
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830403"
---
# <a name="limitations-of-xamarinios"></a>Limitaciones de Xamarin.iOS

Puesto que las aplicaciones que usan Xamarin.iOS se compilan en código estático, no es posible usar las funciones que requieren la generación de código en tiempo de ejecución.

Éstas son las limitaciones de Xamarin.iOS en comparación con Mono de escritorio:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>La compatibilidad con genéricos limitado

A diferencia de Mono y .NET tradicional, el código en el iPhone se compila estáticamente antelación en lugar de que se compila a petición mediante un compilador JIT.

Mono [AOT completa](https://www.mono-project.com/docs/advanced/aot/#full-aot) tecnología tiene algunas limitaciones con respecto a los genéricos, estos se deben a que no todas las instancias genéricas posibles se pueden determinar por adelantado en tiempo de compilación. Esto no es un problema para tiempos de ejecución de .NET o Mono regular como siempre se compila el código en tiempo de ejecución mediante el Just-in compilador Time. Pero esto supone un desafío para un compilador estático como Xamarin.iOS.

Algunos de los problemas comunes que surgen a los desarrolladores, son:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Subclases genéricas de NSObjects están limitados

Xamarin.iOS actualmente tiene limitado el soporte técnico para la creación de subclases genéricas de NSObject (clase), por ejemplo, no se admite para métodos genéricos. A partir de 7.2.1, utilizando subclases genéricas de NSObjects es posible, como la siguiente:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Aunque subclases genéricas de NSObjects son posibles, hay algunas limitaciones. Leer el [subclases genéricas de NSObject](~/ios/internals/api-design/nsobject-generics.md) documento para obtener más información


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Sin generación de código dinámico

Puesto que el kernel de iOS impide que una aplicación de generación de código dinámicamente, Xamarin.iOS no admite ninguna forma de generación de código dinámico. Entre ellas se incluyen las siguientes:

-  Espacio de nombres System.Reflection.Emit no está disponible.
-  System.Runtime.Remoting no son compatibles.
-  No se admite para la creación dinámica de tipos (ningún Type.GetType ("MyType" 1")), aunque la búsqueda de los tipos existentes (Type.GetType ("System.String"), por ejemplo, funciona bastante bien). 
-  Las devoluciones de llamada inversos deben registrarse con el tiempo de ejecución en tiempo de compilación.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

La falta de System.Reflection. **Emitir** significa que no funcionará ningún código que depende de la generación de código en tiempo de ejecución. Esto incluye cosas como:

-  Dynamic Language Runtime.
-  Los idiomas que se basa en Dynamic Language Runtime.
-  Remoting TransparentProxy o cualquier otra cosa que harían que el tiempo de ejecución generar código de forma dinámica. 


  > [!IMPORTANT]
  > No confunda **Reflection.Emit** con **reflexión**. Reflection.Emit consiste en generar código de forma dinámica y tiene ese código JIT y el código compilado en código nativo. Esto no se admite debido a las limitaciones en iOS (ninguna compilación JIT).

Pero toda la API de reflexión, incluidos Type.GetType ("someClass"), lista de métodos, lista de propiedades, la obtención de atributos y valores funciona perfectamente.

### <a name="using-delegates-to-call-native-functions"></a>Uso de delegados para llamar a funciones nativas

Para llamar a una función nativa a través de un delegado de C#, declaración del delegado debe decorarse con uno de los siguientes atributos:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (opción preferida, ya que es compatible con .NET Standard 1.1 + y multiplataforma)
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

No se puede proporcionar uno de estos atributos se producirá un error en tiempo de ejecución, como:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Invertir las devoluciones de llamada

En Mono estándar es posible pasar instancias de delegados de C# a código no administrado en lugar de un puntero de función. Normalmente, el tiempo de ejecución transformaría esos punteros de función en un pequeño código thunk que permite que el código no administrado para volver a llamar a código administrado.

En Mono se implementan estos puentes mediante Just-in-Time compilador. Al usar el compilador ahead of time requiere iPhone hay dos limitaciones importantes en este momento:

-  Se deben marcar todos los métodos de devolución de llamada con el [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)
-  Los métodos deben ser métodos estáticos, no hay compatibilidad por ejemplo los métodos.
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>No hay comunicación remota

La pila de comunicación remota no está disponible en Xamarin.iOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>En tiempo de ejecución deshabilitada características

Se han deshabilitado las siguientes características en tiempo de ejecución de iOS de Mono:

-  generador de perfiles
-  Reflection.Emit
-  Funcionalidad Reflection.Emit.Save
-  Enlaces de COM
-  El motor de JIT
-  Comprobador de metadatos (ya que no hay ningún JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitaciones de la API de .NET

Expone la API de .NET es un subconjunto del marco completo como no todo lo que está disponible en iOS. Consulte las preguntas más frecuentes para una [lista de ensamblados admitidos actualmente](~/cross-platform/internals/available-assemblies.md).



En concreto, el perfil de API utilizado por Xamarin.iOS no incluye System.Configuration, por lo que no es posible usar archivos XML externos para configurar el comportamiento del tiempo de ejecución.
