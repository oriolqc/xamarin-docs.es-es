---
title: 'Tutorial: Crear una aplicación mediante la API de reflexión'
description: Además de la API de elementos, MonoTouch.Dialog (MT D) también incluye una API de reflexión basada en atributos. Crear pantallas con Monte hace que la API de reflexión D. tan fácil como decorando clases con atributos. Este artículo proporciona un tutorial que muestra cómo crear una aplicación utilizando la API de reflexión.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e56eaeccb2e09d9f1ad84245bf41e2a4bf1b56f1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough-creating-an-application-using-the-reflection-api"></a>Tutorial: Crear una aplicación mediante la API de reflexión

_Además de la API de elementos, MonoTouch.Dialog (MT D) también incluye una API de reflexión basada en atributos. Crear pantallas con Monte hace que la API de reflexión D. tan fácil como decorando clases con atributos. Este artículo proporciona un tutorial que muestra cómo crear una aplicación utilizando la API de reflexión._


El Monte API de reflexión de D permite que las clases se decoran con atributos que monte D se usa para crear pantallas automáticamente. La API de reflexión proporciona un enlace entre estas clases y lo que se muestra en pantalla. Aunque esta API no proporciona el control específico encargada de la API de elementos, reduce la complejidad creando automáticamente a la jerarquía de elementos en función de la decoración de clase.

 <a name="Getting_Started_with_the_Reflection_API" />


## <a name="getting-started-with-the-reflection-api"></a>Introducción a la API de reflexión

Utilizar la API de reflexión es tan simple como:

1.  Crear una clase decorada con MT Atributos de D.
1.  Crear un `BindingContext` instancia pasando una instancia de la clase anterior. 
1.  Crear un `DialogViewController` , pasándole el `BindingContext’s` `RootElement` . 


Veamos un ejemplo para ilustrar cómo se usa la API de reflexión. En este ejemplo, vamos a crear una pantalla de entrada de datos simple, tal y como se muestra a continuación:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "En este ejemplo, vamos a crear una pantalla de entrada de datos simple como se muestra aquí")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

 <a name="Creating_a_Class_with_MT.D_Attributes" />


## <a name="creating-a-class-with-mtd-attributes"></a>Crear una clase con MT Atributos de D

Lo primero que debemos usar la API de reflexión es una clase decorada con atributos. Estos atributos se usará en MT D. internamente para crear objetos de la API de elementos. Por ejemplo, considere la siguiente definición de clase:

```csharp
public class Expense
{
        [Section("Expense Entry")]

        [Entry("Enter expense name")]
        public string Name;
        
        [Section("Expense Details")]
  
        [Caption("Description")]
        [Entry]
        public string Details;
        
        [Checkbox]
        public bool IsApproved = true;
}
```

El `SectionAttribute` dará como resultado en secciones de la `UITableView` va a crear, con el argumento de cadena que se usa para rellenar el encabezado de la sección. Una vez que se declara una sección, se incluirán todos los campos que hay a continuación en esa sección, hasta que se declara otra sección.
El tipo de elemento de la interfaz de usuario creado para el campo dependerá del tipo del campo y el Monte Atributo de D representándola.

Por ejemplo, el `Name` campo es un `string` y se decora con un `EntryAttribute`. Esto da como resultado una fila que se agrega a la tabla con un campo de entrada de texto y el título especificado. De forma similar, el `IsApproved` campo es un `bool` con un `CheckboxAttribute`, da lugar a una fila de la tabla con un control checkbox a la derecha de la celda de tabla. MONTE D utiliza el nombre del campo, agrega automáticamente un espacio, para crear el título en este caso, puesto que no se especifica en un atributo.

 <a name="Adding_the_BindingContext" />


## <a name="adding-the-bindingcontext"></a>Agregar BindingContext

Para usar el `Expense` (clase), debemos crear un `BindingContext`. A `BindingContext` es una clase que se enlazará los datos de la clase con atributos para crear la jerarquía de elementos. Para crear uno, simplemente se inicializarlo y pase una instancia de la clase con atributos al constructor.

Por ejemplo, para agregar la interfaz de usuario que se declara mediante el atributo en el `Expense` de clases, incluya el siguiente código en el `FinishedLaunching` método de la `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Lo único que queda por hacer para crear la interfaz de usuario es agregar la `BindingContext` a la `DialogViewController` y establézcalo como el `RootViewController` de la ventana, tal y como se muestra a continuación:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
   
        window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        var expense = new Expense ();
        var bctx = new BindingContext (null, expense, "Create a task");
        var dvc = new DialogViewController (bctx.Root);
            
        window.RootViewController = dvc;
        window.MakeKeyAndVisible ();
            
        return true;
}
```

Ejecutar la aplicación ahora da como resultado la pantalla mostrada anteriormente que se va a mostrar.

 <a name="Adding_a_UINavigationController" />


### <a name="adding-a-uinavigationcontroller"></a>Agregar un UINavigationController

Tenga en cuenta sin embargo que el título "crea una tarea" que se pasa a la `BindingContext` no se muestra. Esto es porque el `DialogViewController` es no forma parte de un `UINavigatonController`. Vamos a cambiar el código para agregar una `UINavigationController` como la ventana `RootViewController,` y agregue el `DialogViewController` como la raíz de la `UINavigationController` tal y como se muestra a continuación:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Ahora cuando se ejecuta la aplicación, el título aparece en la `UINavigationController’s` la captura de pantalla siguiente muestra la barra de navegación:

 [![](reflection-api-walkthrough-images/02-create-task.png "Cuando se ejecuta la aplicación, el título aparece ahora en la barra de navegación de UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Mediante la inclusión de un `UINavigationController`, ahora podemos realizar ventajas de otras características de MT Para los que es necesaria navegación d. Por ejemplo, podemos agregar una enumeración a la `Expense` clase para definir la categoría de los gastos y monte D. creará una pantalla de selección automáticamente. Para mostrar, modificar el `Expense` clase para que incluya un `ExpenseCategory` campo como sigue:

```csharp
public enum Category
{
        Travel,
        Lodging,
        Books
}
        
public class Expense
{
        …

        [Caption("Category")]
        public Category ExpenseCategory;
}
```

Ejecutar la aplicación ahora resulta en una nueva fila en la tabla de la categoría tal como se muestra:

 [![](reflection-api-walkthrough-images/03-set-details.png "Ejecutar la aplicación ahora da como resultado una nueva fila en la tabla de la categoría tal como se muestra")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Al seleccionar la fila provoca la aplicación navegar a una nueva pantalla con filas que corresponden a la enumeración, tal y como se muestra a continuación:

 [![](reflection-api-walkthrough-images/04-set-category.png "Al seleccionar la fila provoca la aplicación que vaya a una nueva pantalla con filas que corresponden a la enumeración")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Resumen

Este artículo presenta un tutorial sobre la API de reflexión. También hemos mostrado cómo agregar atributos a una clase para controlar lo que se muestra. También se explica cómo utilizar un `BindingContext` para enlazar los datos de una clase a la jerarquía de elementos que se crea, y cómo usar MT D. con un `UINavigationController`.


## <a name="related-links"></a>Vínculos relacionados

- [MTDReflectionWalkthrough (ejemplo)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Presentación en pantalla - Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introducción al cuadro de diálogo de MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial de elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de clase UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
