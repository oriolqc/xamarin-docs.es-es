---
title: Creación de una aplicación Xamarin.iOS mediante la API de reflexión
description: Este documento describe la MonoTouch.Dialog basada en atributos API de reflexión, lo que crea la interfaz de usuario basado en clases decoradas con atributos.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a1b77f46410ef20892485a866221bab2c872e54c
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038477"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Creación de una aplicación Xamarin.iOS mediante la API de reflexión

El destino maestro. D Reflection API permite que las clases decorado con atributos de ese destino maestro. D se usa para crear pantallas automáticamente. La API de reflexión proporciona un enlace entre estas clases y lo que se muestra en pantalla. Aunque esta API no proporciona el control específico que hace de los elementos de la API, reduce la complejidad al crear automáticamente la jerarquía de elementos en función de la decoración de clase.

## <a name="setting-up-mtd"></a>Configuración de destino maestro. D.

DESTINO MAESTRO. D. se distribuye con Xamarin.iOS. Para ello, haga doble clic en el **referencias** nodo de un Xamarin.iOS del proyecto en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia a la **MonoTouch.Dialog 1** ensamblado. A continuación, agregue `using MonoTouch.Dialog` instrucciones en el origen de código según sea necesario.

## <a name="getting-started-with-the-reflection-api"></a>Introducción a la API de reflexión

Mediante la API de reflexión es tan sencillo como:

1.  Creación de una clase decorada con el destino maestro. Atributos D.
1.  Creación de un `BindingContext` instancia, pasando una instancia de la clase anterior. 
1.  Creación de un `DialogViewController` , pasándole el `BindingContext’s` `RootElement` . 


Veamos un ejemplo para ilustrar cómo se usa la API de reflexión. En este ejemplo, vamos a crear una pantalla de entrada de datos simples, como se muestra a continuación:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "En este ejemplo, vamos a crear una pantalla de entrada de datos simples como se muestra aquí")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Crear una clase con el destino maestro. Atributos D

Lo primero que debemos usar la API de reflexión es una clase decorada con atributos. Estos atributos se usará en el destino maestro. D. internamente para crear objetos de la API de elementos. Por ejemplo, considere la siguiente definición de clase:

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

El `SectionAttribute` dará como resultado en las secciones de la `UITableView` creado, con el argumento de cadena que se usa para rellenar el encabezado de la sección. Una vez que se declara una sección, se incluirán todos los campos que le sigue en esa sección, hasta que se declara otra sección.
El tipo de elemento de la interfaz de usuario creado para el campo dependerá del tipo del campo y el destino maestro. Atributo de D representándola.

Por ejemplo, el `Name` campo es un `string` y está decorado con un `EntryAttribute`. Esto da como resultado una fila que se agrega a la tabla con un campo de entrada de texto y el título especificado. De forma similar, el `IsApproved` campo es un `bool` con un `CheckboxAttribute`, lo que en una fila de tabla con una casilla a la derecha de la celda de tabla. DESTINO MAESTRO. D. usa el nombre del campo, agrega automáticamente un espacio, para crear el título en este caso, puesto que no se especifica en un atributo.

## <a name="adding-the-bindingcontext"></a>Agregar BindingContext

Para usar el `Expense` (clase), es necesario crear un `BindingContext`. Un `BindingContext` es una clase que se enlazará los datos de la clase con atributos para crear la jerarquía de elementos. Para crear uno, simplemente crear instancias de ella y pasar una instancia de la clase con atributos al constructor.

Por ejemplo, para agregar la interfaz de usuario que se declara con el atributo en el `Expense` class, incluya el código siguiente en el `FinishedLaunching` método de la `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Todo lo que tenemos que hacer para crear la interfaz de usuario es agregar la `BindingContext` a la `DialogViewController` y establecerlo como el `RootViewController` de la ventana, tal como se muestra a continuación:

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

Se ejecuta la aplicación ahora resultados mostrada anteriormente que se muestra en la pantalla.

### <a name="adding-a-uinavigationcontroller"></a>Agregar un UINavigationController

Pero tenga en cuenta que el título "crea una tarea" que se pasa a la `BindingContext` no se muestra. Esto es porque el `DialogViewController` es no forma parte de un `UINavigatonController`. Vamos a cambiar el código para agregar un `UINavigationController` como la ventana `RootViewController,` y agregue el `DialogViewController` como la raíz de la `UINavigationController` tal como se muestra a continuación:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Ahora Cuando ejecutemos la aplicación, el título aparece en la `UINavigationController’s` la captura de pantalla siguiente muestra la barra de navegación:

 [![](reflection-api-walkthrough-images/02-create-task.png "Ahora Cuando ejecutemos la aplicación, el título aparece en la barra de navegación UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Mediante la inclusión de un `UINavigationController`, ahora nos podemos aprovechar las ventajas de otras características del destino maestro. D. para que la navegación es necesaria. Por ejemplo, podemos agregar enumeración a la `Expense` clase para definir la categoría de los gastos y el destino maestro. D. creará una pantalla de selección automáticamente. Para mostrar, modifique la `Expense` clase para que incluya un `ExpenseCategory` campo como sigue:

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

Se ejecuta la aplicación ahora resultados en una nueva fila en la tabla de la categoría tal como se muestra:

 [![](reflection-api-walkthrough-images/03-set-details.png "Ejecutar la aplicación ahora da como resultado una nueva fila en la tabla de la categoría tal como se muestra")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Al seleccionar la fila se obtienen en la aplicación vaya a una nueva pantalla con filas que corresponden a la enumeración, tal como se muestra a continuación:

 [![](reflection-api-walkthrough-images/04-set-category.png "Al seleccionar la fila se obtienen en la aplicación que vaya a una nueva pantalla con filas que corresponden a la enumeración")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Resumen

En este artículo se presenta un tutorial sobre la API de reflexión. Hemos mostrado cómo agregar atributos a una clase para controlar lo que se muestra. También analizamos cómo usar un `BindingContext` para enlazar datos desde una clase a la jerarquía de elementos que se crea, además de cómo usar destino maestro. D. con un `UINavigationController`.


## <a name="related-links"></a>Vínculos relacionados

- [MTDReflectionWalkthrough (ejemplo)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Presentación en pantalla: Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introducción al cuadro de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial sobre la API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial del elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de clase UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
