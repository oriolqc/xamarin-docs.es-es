---
title: Inserción de dependencias
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers utiliza inserción de dependencias para desacoplar tipos concretos desde el código que dependa de estos tipos.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 97b95ccb3e756f02c945adc63b9e173a9f9e0226
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832684"
---
# <a name="dependency-injection"></a>Inserción de dependencias

Normalmente, se invoca un constructor de clase al crear una instancia de un objeto y todos los valores que el objeto necesita se pasan como argumentos al constructor. Esto es un ejemplo de inserción de dependencias y, en concreto, se conoce como *inserción del constructor*. Las dependencias que necesita el objeto se insertan en el constructor.

Mediante la especificación de dependencias como tipos de interfaz, inserción de dependencias permite desacoplar de los tipos concretos desde el código que dependa de estos tipos. Por lo general, usa un contenedor que contiene una lista de registros y las asignaciones entre tipos e interfaces abstractos y los tipos concretos que implementan o amplían estos tipos.

También existen otros tipos de inserción de dependencias, como *inserción del establecedor de propiedad*, y *inserción de llamadas de método*, pero que se ven con menos frecuencia. Por lo tanto, este capítulo se centrará únicamente en realizar la inserción del constructor con un contenedor de inserción de dependencia.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introducción a la inserción de dependencias

Inserción de dependencias es una versión especializada del patrón de inversión de Control (IoC), donde la preocupación de que se invierte el proceso de obtención de la dependencia necesaria. Con la inserción de dependencia, otra clase es responsable de la inyección de dependencias en un objeto en tiempo de ejecución. El siguiente ejemplo de código muestra cómo el `ProfileViewModel` está estructurada la clase cuando se usa la inserción de dependencias:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

El `ProfileViewModel` constructor recibe un `IOrderService` instancia como un argumento, insertado por otra clase. La única dependencia en el `ProfileViewModel` clase está en el tipo de interfaz. Por lo tanto, el `ProfileViewModel` clase no tiene ningún conocimiento de la clase que es responsable de crear una instancia de la `IOrderService` objeto. La clase que es responsable de crear una instancia de la `IOrderService` de objetos y se inserta en la `ProfileViewModel` de clase, se conoce como el *contenedor de inserción de dependencia*.

Contenedores de inserción de dependencia reducen el acoplamiento entre objetos por lo que proporciona una funcionalidad para crear instancias de clase y administra su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que requiere el objeto en él. Si aún no se han creado esas dependencias, el contenedor crea y resuelve primero sus dependencias.

> [!NOTE]
> Inserción de dependencias también se puede implementar manualmente mediante generadores. Sin embargo, mediante un contenedor proporciona funcionalidades adicionales, como la administración de la duración y el registro a través de análisis de ensamblado.

Existen ventajas al usar un contenedor de inserción de dependencia:

-   Un contenedor elimina la necesidad de una clase localizar sus dependencias y administrar sus duraciones.
-   Un contenedor permite la asignación de dependencias implementadas sin que afecte a la clase.
-   Un contenedor facilita la capacidad de prueba al permitir que las dependencias que se simularán.
-   Un contenedor aumenta la facilidad de mantenimiento permitiendo nuevas clases que fácilmente se agregarán a la aplicación.

En el contexto de una aplicación de Xamarin.Forms que usa MVVM, normalmente, se usará un contenedor de inserción de dependencia para registrar y resolver los modelos de vista y para registrar servicios e insertarlas en modelos de vista.

Existen muchos contenedores de inserción de dependencias, con la aplicación móvil de eShopOnContainers usa Autofac para administrar la creación de instancias del modelo de vista y las clases en la aplicación del servicio. Autofac facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que normalmente se encuentran en contenedores de inserción de dependencia, incluidos los métodos para registrar asignaciones de tipos e instancias de objeto, resuelva los objetos, administración la duración de los objetos e inyectar objetos dependientes en constructores de objetos que resuelve. Para obtener más información acerca de Autofac, consulte [Autofac](http://autofac.readthedocs.io/en/latest/index.html) en readthedocs.io.

En Autofac, el `IContainer` interfaz proporciona el contenedor de inserción de dependencia. Figura 3-1 se muestran las dependencias cuando se usa este contenedor, que crea una instancia de un `IOrderService` objeto y los inserta en la `ProfileViewModel` clase.

![](dependency-injection-images/dependencyinjection.png "Ejemplo de las dependencias cuando se usa la inserción de dependencias")

**Figura 3-1:** Dependencias cuando se usa la inserción de dependencias

En tiempo de ejecución, el contenedor debe conocer qué implementación de la `IOrderService` interfaz debe crear una instancia, antes de que puede crear una instancia de un `ProfileViewModel` objeto. Esto implica:

-   El contenedor que decida cómo va a crear una instancia de un objeto que implementa el `IOrderService` interfaz. Esto se conoce como *registro*.
-   El contenedor de crear instancias del objeto que implementa el `IOrderService` interfaz y el `ProfileViewModel` objeto. Esto se conoce como *resolución*.

Finalmente, la aplicación finalizará utilizando el `ProfileViewModel` y estará disponibles para la recolección. En este momento, el recolector de elementos no utilizados debe desechar el `IOrderService` si otras clases no comparten la misma instancia de la instancia.

> [!TIP]
> Escribir código independiente del contenedor. Siempre intenta escribir código independiente del contenedor para desacoplar la aplicación desde el contenedor de dependencia específica que se va a usar.

## <a name="registration"></a>Registro

Antes de que pueden insertar dependencias en un objeto, los tipos de las dependencias deben registrarse primero con el contenedor. Registra un tipo normalmente implica pasar el contenedor de una interfaz y un tipo concreto que implementa la interfaz.

Hay dos maneras de registrar los tipos y objetos en el contenedor a través de código:

-   Registrar un tipo o asignación con el contenedor. Cuando sea necesario, el contenedor creará una instancia del tipo especificado.
-   Registrar un objeto existente en el contenedor como un singleton. Cuando sea necesario, el contenedor devolverá una referencia al objeto existente.

> [!TIP]
> Contenedores de inserción de dependencia no son siempre adecuados. Inserción de dependencias introduce una complejidad adicional y los requisitos que podrían no ser útil o adecuado para aplicaciones pequeñas. Si una clase no tiene ninguna dependencia, o no es una dependencia para otros tipos, es posible que no sentido para colocarlo en el contenedor. Además, si una clase tiene un único conjunto de dependencias que son fundamentales para el tipo y no cambia nunca, no tendría sentido para colocarlo en el contenedor.

El registro de tipos que requieren la inserción de dependencias se debe realizar en un único método en una aplicación, y este método se debe invocar al principio de ciclo de vida de la aplicación para asegurarse de que la aplicación es consciente de las dependencias entre sus clases. En la aplicación móvil de eShopOnContainers se realiza mediante el `ViewModelLocator` clase, qué compilaciones el `IContainer` de objetos y es la única clase en la aplicación que contiene una referencia a ese objeto. El ejemplo de código siguiente muestra cómo la aplicación móvil de eShopOnContainers declara el `IContainer` objeto en el `ViewModelLocator` clase:

```csharp
private static IContainer _container;
```

Tipos y las instancias registradas en el `RegisterDependencies` método en el `ViewModelLocator` clase. Esto se logra creando primero un `ContainerBuilder` instancia, que se muestra en el ejemplo de código siguiente:

```csharp
var builder = new ContainerBuilder();
```

Tipos e instancias, a continuación, se registran con el `ContainerBuilder` objeto y el ejemplo de código siguiente se muestra la forma más común de registro del tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

El `RegisterType` método se muestra a continuación, asigna un tipo de interfaz a un tipo concreto. Indica al contenedor para crear instancias de un `RequestProvider` objeto cuando se crea una instancia de un objeto que requiere una inserción de un `IRequestProvider` a través de un constructor.

También se pueden registrar tipos concretos directamente sin una asignación de un tipo de interfaz, como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Cuando el `ProfileViewModel` tipo se resuelve, el contenedor insertará sus dependencias necesarias.

Autofac también permite el registro de la instancia, donde es responsable de mantener una referencia a una instancia singleton de un tipo de contenedor. Por ejemplo, en el ejemplo de código siguiente se muestra cómo la aplicación móvil de eShopOnContainers registra el tipo concreto para usar cuando un `ProfileViewModel` instancia requiere un `IOrderService` instancia:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

El `RegisterType` método se muestra a continuación, asigna un tipo de interfaz a un tipo concreto. El `SingleInstance` método configura el registro para que todos los objetos dependientes recibe la misma instancia compartida. Por lo tanto, una sola `OrderService` instancia existirá en el contenedor, que es compartido por los objetos que requieren una inserción de un `IOrderService` a través de un constructor.

También se puede realizar el registro de la instancia con el `RegisterInstance` método, que se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

El `RegisterInstance` crea un nuevo método que se muestra aquí `OrderMockService` de instancia y lo registra con el contenedor. Por lo tanto, una sola `OrderMockService` instancia existe en el contenedor, que es compartido por los objetos que requieren una inserción de un `IOrderService` a través de un constructor.

Tras el registro de tipo y la instancia, el `IContainer` objeto debe compilarse, que se muestra en el ejemplo de código siguiente:

```csharp
_container = builder.Build();
```

Invocar el `Build` método en el `ContainerBuilder` instancia crea un nuevo contenedor de inyección de dependencia que contiene los registros que se han realizado.

> [!TIP]
> Considere la posibilidad de un `IContainer` como inmutable. Mientras Autofac proporciona un `Update` método para actualizar registros en un contenedor existente, llamar a este método debe evitarse siempre que sea posible. Existen riesgos para modificar un contenedor una vez que se han compilado, especialmente si se ha utilizado el contenedor. Para obtener más información, consulte [considere la posibilidad de un contenedor como inmutable](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) en readthedocs.io.

<a name="resolution" />

## <a name="resolution"></a>Resolución

Una vez registrado un tipo, puede resolver o insertado como una dependencia. Cuando se resuelve un tipo y el contenedor debe crear una nueva instancia, todas las dependencias inserta en la instancia.

Por lo general, cuando se resuelve un tipo, se produce uno de tres cosas:

1.  Si no se ha registrado el tipo, el contenedor inicia una excepción.
1.  Si el tipo se ha registrado como un singleton, el contenedor devuelve la instancia de singleton. Si se trata de la primera vez que el tipo se denomina para, el contenedor crea si es necesario y mantiene una referencia a él.
1.  Si aún no se ha registrado el tipo como un singleton, el contenedor devuelve una nueva instancia y no mantiene una referencia a él.

El siguiente ejemplo de código muestra cómo el `RequestProvider` se puede resolver el tipo que se registró previamente con Autofac:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

En este ejemplo, se pide Autofac para resolver el tipo concreto para el `IRequestProvider` tipo, junto con las dependencias. Normalmente, el `Resolve` método se llama cuando se necesita una instancia de un tipo específico. Para obtener información acerca de cómo controlar la duración de objetos resueltos, consulte [administrar la vigencia de objetos puede resolver](#managing_the_lifetime_of_resolved_objects).

El ejemplo de código siguiente muestra cómo la aplicación móvil de eShopOnContainers crea instancias de tipos de modelo de vista y sus dependencias:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

En este ejemplo, se le pide Autofac para resolver el tipo de modelo de vista para un modelo de vista solicitado y el contenedor también resolverá cualquier dependencia. Al resolver el `ProfileViewModel` es de tipo, la dependencia para resolver un `IOrderService` objeto. Por lo tanto, primero crea Autofac una `OrderService` de objetos y, a continuación, se pasa al constructor de la `ProfileViewModel` clase. Para obtener más información acerca de cómo la aplicación móvil de eShopOnContainers crea la vista de modelos y los asocia a las vistas, consulte [creación automática de un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> Registrar y resolver los tipos con un contenedor tienen un costo por uso del contenedor de la reflexión para crear cada tipo, especialmente si se está reconstruyendo dependencias para cada navegación de páginas en la aplicación de rendimiento. Si hay muchas o pocas dependencias, puede aumentar significativamente el costo de la creación.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Administrar la duración de objetos resueltos

Después de registrar un tipo, el comportamiento predeterminado de Autofac es crear una nueva instancia del tipo registrado cada vez que el tipo que se resuelva o cuando el mecanismo de dependencia inyecta instancias de otras clases. En este escenario, el contenedor no contiene una referencia al objeto resuelto. Sin embargo, al registrar una instancia, es el comportamiento predeterminado de Autofac administrar la vigencia del objeto como un singleton. Por lo tanto, la instancia permanece en el ámbito mientras el contenedor está en ámbito y se elimina cuando el contenedor se sale del ámbito y se recolectan o cuando el código elimina explícitamente el contenedor.

Un ámbito de la instancia de Autofac puede utilizarse para especificar el comportamiento de singleton para un objeto que Autofac crea a partir de un tipo registrado. Ámbitos de instancia de Autofac administran la duración de los objetos que crea una instancia del contenedor. El ámbito de la instancia predeterminada para el `RegisterType` método es el `InstancePerDependency` ámbito. Sin embargo, el `SingleInstance` ámbito se puede usar con el `RegisterType` método, para que el contenedor se crea o se devuelve una instancia singleton de un tipo al llamar a la `Resolve` método. El ejemplo de código siguiente muestra cómo se indica Autofac para crear una instancia singleton de la `NavigationService` clase:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La primera vez que el `INavigationService` interfaz se ha resuelta, el contenedor crea un nuevo `NavigationService` de objetos y mantiene una referencia a él. En las resoluciones subsiguientes de la `INavigationService` interfaz, el contenedor devuelve una referencia a la `NavigationService` objeto que se creó anteriormente.

> [!NOTE]
> El ámbito SingleInstance elimina los objetos creados cuando se elimina el contenedor.

Autofac incluye los ámbitos de instancia adicional. Para obtener más información, consulte [ámbito la instancia](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) en readthedocs.io.

## <a name="summary"></a>Resumen

Inserción de dependencias permite la separación de tipos concretos desde el código que dependa de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y las asignaciones entre tipos e interfaces abstractos y los tipos concretos que implementan o amplían estos tipos.

Autofac facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que normalmente se encuentran en contenedores de inserción de dependencia, incluidos los métodos para registrar asignaciones de tipos e instancias de objeto, resuelva los objetos, administración la duración de los objetos e inyectar objetos dependientes en constructores de objetos que se resuelve.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
