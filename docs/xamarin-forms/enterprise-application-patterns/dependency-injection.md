---
title: Inserción de dependencias
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers usa inyección de dependencia para desacoplar tipos concretos desde el código que dependa de estos tipos.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fb225349b9ffb1c950486a817897b3c26c6ffbe4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242575"
---
# <a name="dependency-injection"></a>Inserción de dependencias

Normalmente, se invoca un constructor de clase al crear una instancia de un objeto, y los valores que necesita el objeto se pasan como argumentos al constructor. Esto es un ejemplo de inserción de dependencias y, en concreto se conoce como *inyección de constructor*. Las dependencias que necesita el objeto se insertan en el constructor.

Al especificar dependencias como tipos de interfaz, inyección de dependencia permite la separación de los tipos concretos desde el código que dependa de estos tipos. Por lo general, usa un contenedor que contiene una lista de registros y las asignaciones entre tipos abstractos e interfaces y los tipos concretos que implementan o extienden estos tipos.

También hay otros tipos de inserción de dependencias, como *inyección de establecedor de propiedad*, y *por inyección de código de llamada de método*, pero que se ven menos frecuente. Por lo tanto, en este capítulo se centrará únicamente en realizar la inyección de constructor con un contenedor de inyección de dependencia.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introducción a la inyección de dependencia

Inserción de dependencias es una versión especializada del patrón de inversión de Control (IoC), donde la preocupación de que se invierte es el proceso de obtención de la dependencia necesaria. Con la inserción de dependencias, otra clase es responsable de insertar las dependencias en un objeto en tiempo de ejecución. El siguiente ejemplo de código muestra cómo el `ProfileViewModel` clase está estructurada al usar la inserción de dependencias:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

El `ProfileViewModel` constructor recibe un `IOrderService` instancia como un argumento, insertado por otra clase. La única dependencia en el `ProfileViewModel` clase está en el tipo de interfaz. Por lo tanto, la `ProfileViewModel` clase no tiene ningún conocimiento de la clase que es responsable de crear instancias de la `IOrderService` objeto. La clase que es responsable de crear instancias de la `IOrderService` objeto e insertarlos en la `ProfileViewModel` de clases, se conoce como el *contenedor inyección de dependencia*.

Contenedores de inyección de dependencia reducen el acoplamiento entre objetos por lo que proporciona una funcionalidad para crear instancias de clase y administrar su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que requiere el objeto en él. Si aún no se han creado esas dependencias, el contenedor crea y sus dependencias resuelve en primer lugar.

> [!NOTE]
> Inyección de dependencia también puede implementarse manualmente mediante generadores. Sin embargo, mediante un contenedor proporciona funcionalidades adicionales como la administración de la duración y el registro a través de análisis de ensamblado.

Hay varias ventajas con respecto al uso de un contenedor de inyección de dependencia:

-   Un contenedor elimina la necesidad de una clase encontrar sus dependencias y administrar sus duraciones.
-   Un contenedor permite la asignación de dependencias implementadas sin que afecte a la clase.
-   Un contenedor facilita la capacidad de prueba al permitir que las dependencias ser simulada.
-   Un contenedor aumenta la facilidad de mantenimiento permitiendo nuevas clases agregar fácilmente a la aplicación.

En el contexto de una aplicación de Xamarin.Forms que usa MVVM, un contenedor de inyección de dependencia normalmente se usará para registrar y resolver ver modelos y de registro de los servicios y a insertar en modelos de vista.

Existen muchos contenedores de inyección de dependencia, con la aplicación móvil eShopOnContainers con Autofac para administrar la creación de instancias del modelo de vista y las clases en la aplicación del servicio. Autofac facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que se suelen encontrar en contenedores de inyección de dependencia, incluidos los métodos para registrar asignaciones de tipos e instancias de objeto, resuelva los objetos, administración la duración de los objetos e insertar objetos dependientes en constructores de objetos que resuelve. Para obtener más información sobre Autofac, consulte [Autofac](http://autofac.readthedocs.io/en/latest/index.html) en readthedocs.io.

En Autofac, la `IContainer` interfaz proporciona el contenedor de inyección de dependencia. Figura 3-1 se muestran las dependencias cuando se usa este contenedor, que se crea una instancia de un `IOrderService` objeto y lo inserta en la `ProfileViewModel` clase.

![](dependency-injection-images/dependencyinjection.png "Ejemplo de dependencias cuando se usa la inyección de dependencia")

**Figura 3-1:** dependencias cuando se usa la inyección de dependencia

En tiempo de ejecución, el contenedor debe saber qué implementación de la `IOrderService` interfaz debe crear una instancia, antes de que puede crear una instancia de un `ProfileViewModel` objeto. Esto implica:

-   El contenedor de la decisión sobre cómo crear una instancia de un objeto que implementa el `IOrderService` interfaz. Esto se conoce como *registro*.
-   El contenedor de crear instancias del objeto que implementa el `IOrderService` interfaz y la `ProfileViewModel` objeto. Esto se conoce como *resolución*.

Finalmente, la aplicación finalizará utilizando la `ProfileViewModel` objeto y estará disponibles para la recolección. En este punto, el recolector de elementos no utilizados debe desechar el `IOrderService` si otras clases no comparten la misma instancia de la instancia.

> [!TIP]
> Escribir código independiente del contenedor. Intente escribir siempre código independiente del contenedor para desacoplar la aplicación desde el contenedor de dependencia concreta que se va a usar.

## <a name="registration"></a>Registro

Antes de que las dependencias se pueden insertar en un objeto, los tipos de las dependencias en primer lugar deben registrarse con el contenedor. Registrar un tipo normalmente implica pasar el contenedor de una interfaz y un tipo concreto que implementa la interfaz.

Hay dos formas de registrar los tipos y objetos en el contenedor a través de código:

-   Registrar un tipo o una asignación con el contenedor. Cuando sea necesario, el contenedor creará una instancia del tipo especificado.
-   Registrar un objeto existente en el contenedor como singleton. Cuando sea necesario, el contenedor devolverá una referencia al objeto existente.

> [!TIP]
> Contenedores de inyección de dependencia no son siempre adecuados. Inyección de dependencia introduce una complejidad adicional y requisitos que podrían no ser útil o adecuado para pequeñas aplicaciones. Si una clase no tiene ninguna dependencia o no es una dependencia de otros tipos, no podría no sentido para colocarla en el contenedor. Además, si una clase tiene un único conjunto de dependencias que son fundamentales para el tipo y no cambia nunca, no tendría sentido lo coloque en el contenedor.

El registro de tipos que requieren la inserción de dependencias debe realizarse en un único método en una aplicación, y este método se debe invocar al principio de ciclo de vida de la aplicación para asegurarse de que la aplicación está al tanto de las dependencias entre sus clases. En la aplicación móvil eShopOnContainers Esto se realiza mediante la `ViewModelLocator` de la clase, qué compilaciones la `IContainer` del objeto y es la única clase en la aplicación que contiene una referencia a ese objeto. En el ejemplo de código siguiente se muestra cómo se declara la aplicación móvil eShopOnContainers el `IContainer` objeto en el `ViewModelLocator` clase:

```csharp
private static IContainer _container;
```

Tipos e instancias están registrados en el `RegisterDependencies` método en la `ViewModelLocator` clase. Esto se logra creando primero una `ContainerBuilder` instancia, que se muestra en el ejemplo de código siguiente:

```csharp
var builder = new ContainerBuilder();
```

Tipos e instancias, a continuación, se registran con el `ContainerBuilder` objeto y en el ejemplo de código siguiente se muestra la forma más común de registro del tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

El `RegisterType` método se muestra a continuación, asigna un tipo de interfaz a un tipo concreto. Indica el contenedor para crear instancias de un `RequestProvider` objeto cuando se crea una instancia de un objeto que requiere una inyección de una `IRequestProvider` a través de un constructor.

También se pueden registrar tipos concretos directamente sin una asignación de un tipo de interfaz, como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Cuando el `ProfileViewModel` tipo se resuelve, el contenedor insertan sus dependencias necesarias.

Autofac también permite el registro de instancia, donde el contenedor es responsable de mantener una referencia a una instancia de un tipo singleton. Por ejemplo, en el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers registra el tipo concreto que se utilizará cuando un `ProfileViewModel` instancia requiere un `IOrderService` instancia:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

El `RegisterType` método se muestra a continuación, asigna un tipo de interfaz a un tipo concreto. El `SingleInstance` método configura el registro para que todos los objetos dependientes reciba la misma instancia compartida. Por lo tanto, solo una `OrderService` instancia existirá en el contenedor, que es compartido por los objetos que requieren una inyección de una `IOrderService` a través de un constructor.

Registro de la instancia también pueden realizarse con el `RegisterInstance` método, que se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

El `RegisterInstance` crea un nuevo método que se muestra aquí `OrderMockService` instancia y lo registra con el contenedor. Por lo tanto, solo una `OrderMockService` instancia existe en el contenedor, que es compartido por los objetos que requieren una inyección de una `IOrderService` a través de un constructor.

Tras el registro de tipo y la instancia, la `IContainer` objeto debe compilarse, que se muestra en el ejemplo de código siguiente:

```csharp
_container = builder.Build();
```

Invocar la `Build` método en el `ContainerBuilder` instancia genera un nuevo contenedor de inyección de dependencia que contiene los registros que se han realizado.

>💡 **Sugerencia**: considere la posibilidad de un `IContainer` como inmutable. Si bien proporciona Autofac un `Update` método para actualizar los registros de un contenedor existente, llamar a este método debería evitarse siempre que sea posible. Existen riesgos para modificar un contenedor después de que se ha compilado, especialmente si se ha utilizado el contenedor. Para obtener más información, consulte [considere la posibilidad de un contenedor como inmutables](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) en readthedocs.io.

<a name="resolution" />

## <a name="resolution"></a>Resolución

Después de registra un tipo, puede resolver o insertado como una dependencia. Cuando se resuelve un tipo y el contenedor debe crear una nueva instancia, todas las dependencias inserta en la instancia.

Por lo general, cuando se resuelve un tipo, se produce una de estas tres cosas:

1.  Si el tipo no se ha registrado, el contenedor inicia una excepción.
1.  Si el tipo se ha registrado como un singleton, el contenedor devuelve la instancia de singleton. Si se trata de la primera vez que se llame al tipo de, el contenedor crea si es necesario y mantiene una referencia a él.
1.  Si el tipo no se ha registrado como un singleton, el contenedor devuelve una nueva instancia y no mantiene una referencia a él.

El siguiente ejemplo de código muestra cómo el `RequestProvider` se puede resolver el tipo que se registraron anteriormente con Autofac:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

En este ejemplo, se solicita Autofac para resolver el tipo concreto para la `IRequestProvider` tipo, junto con todas las dependencias. Normalmente, el `Resolve` método se llama cuando se necesita una instancia de un tipo específico. Para obtener información sobre cómo controlar la duración de objetos resueltos, consulte [administrar la duración de objetos resolver](#managing_the_lifetime_of_resolved_objects).

En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers crea una instancia de la vista tipos de modelo y sus dependencias:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

En este ejemplo, se solicita Autofac para resolver el tipo de modelo de vista para un modelo de vista solicitada y el contenedor también resolverá las dependencias. Al resolver el `ProfileViewModel` es de tipo, la dependencia para resolver un `IOrderService` objeto. Por lo tanto, primero crea Autofac una `OrderService` objeto y, a continuación, se pasa al constructor de la `ProfileViewModel` clase. Para obtener más información acerca de cómo la aplicación móvil eShopOnContainers construye ver modelos y las asocia a vistas, vea [creación automática de un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> Registrar y resolver los tipos con un contenedor afectará al rendimiento debido a uso del contenedor de la reflexión para crear cada tipo, especialmente si se está regenerando dependencias para cada navegación de una página en la aplicación. Si hay muchos o profundas dependencias, puede aumentar significativamente el costo de la creación.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Administrar la vigencia de objetos resueltos

Después de registrar un tipo, el comportamiento predeterminado para Autofac es crear una nueva instancia del tipo registrado cada vez que el tipo se resuelve, o cuando el mecanismo de dependencia inserta instancias en otras clases. En este escenario, el contenedor no contiene una referencia al objeto resuelto. Sin embargo, al registrar una instancia, el comportamiento predeterminado para Autofac es administrar la vigencia del objeto como un singleton. Por lo tanto, la instancia permanece en el ámbito mientras el contenedor está en ámbito y se elimina cuando el contenedor se sale del ámbito y se recolectan, o cuando el código elimina explícitamente el contenedor.

Un ámbito de la instancia de Autofac puede utilizarse para especificar el comportamiento de singleton para un objeto que Autofac crea a partir de un tipo registrado. Ámbitos de la instancia de Autofac administran la duración de los objetos crea una instancia del contenedor. El ámbito de la instancia predeterminada de la `RegisterType` método es el `InstancePerDependency` ámbito. Sin embargo, el `SingleInstance` ámbito se puede utilizar con la `RegisterType` método, para que el contenedor se crea o se devuelve una instancia de un tipo singleton al llamar a la `Resolve` método. En el ejemplo de código siguiente se muestra cómo se indica Autofac para crear una instancia de singleton de la `NavigationService` clase:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La primera vez que la `INavigationService` interfaz se resuelve, el contenedor crea un nuevo `NavigationService` de objetos y mantiene una referencia a él. En las siguientes resoluciones de la `INavigationService` interfaz, el contenedor devuelve una referencia a la `NavigationService` objeto que se ha creado previamente.

> [!NOTE]
> El ámbito de SingleInstance elimina los objetos creados cuando se elimina el contenedor.

Autofac incluye ámbitos de instancia adicional. Para obtener más información, consulte [ámbito de la instancia](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) en readthedocs.io.

## <a name="summary"></a>Resumen

Inyección de dependencia permite la separación de tipos concretos desde el código que dependa de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y las asignaciones entre tipos abstractos e interfaces y los tipos concretos que implementan o extienden estos tipos.

Autofac facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que se suelen encontrar en contenedores de inyección de dependencia, incluidos los métodos para registrar asignaciones de tipos e instancias de objeto, resuelva los objetos, administración la duración de los objetos e insertar objetos dependientes en constructores de objetos que se resuelve.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
