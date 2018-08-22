---
title: Enlace de datos y la clave y valor de codificación en Xamarin.Mac
description: En este artículo se describe el uso de codificación y observación para permitir el enlace de datos a los elementos de interfaz de usuario de Interface Builder de Xcode de pares clave-valor de pares clave-valor.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 0adb8cda71ca8803c535679da2aecf00f3fa46a5
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251000"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Enlace de datos y la clave y valor de codificación en Xamarin.Mac

_En este artículo se describe el uso de codificación y observación para permitir el enlace de datos a los elementos de interfaz de usuario de Interface Builder de Xcode de pares clave-valor de pares clave-valor._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tener acceso a la misma codificación de clave-valor y técnicas de enlace de datos que un desarrollador que trabaja *Objective-C* y *Xcode* does. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Xcode _Interface Builder_ para enlazar datos con elementos de interfaz de usuario en lugar de escribir código.

Mediante el uso de pares clave-valor de codificación y las técnicas de enlace en la aplicación de Xamarin.Mac de datos, puede reducir significativamente la cantidad de código que debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de respaldo (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicaciones más flexibles diseño.

[![Un ejemplo de la aplicación en ejecución](databinding-images/intro01.png "un ejemplo de la aplicación en ejecución")](databinding-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos de trabajar con pares clave-valor de codificación y el enlace de datos en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` atributos se utiliza para conectar las clases de C# para objetos de Objective-C y la interfaz de usuario de elementos.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>¿Qué es la clave y valor de codificación

Codificación de clave-valor (KVC) es un mecanismo para tener acceso a propiedades de un objeto indirectamente, con claves (cadenas con formato especial) para identificar las propiedades en lugar de obtener acceso a ellos a través de las variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de pares clave-valor codificación compatible con los descriptores de acceso en la aplicación de Xamarin.Mac, obtendrá acceso a otras características de macOS (antes conocido como OS X) como pares clave-valor observación (KVO), enlace de datos, Core Data, enlaces de cacao y scriptability.

Mediante el uso de pares clave-valor de codificación y las técnicas de enlace en la aplicación de Xamarin.Mac de datos, puede reducir significativamente la cantidad de código que debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de respaldo (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicaciones más flexibles diseño. 

Por ejemplo, echemos un vistazo a la siguiente definición de clase de un objeto conforme KVC:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

En primer lugar, el `[Register("PersonModel")]` registra la clase de atributo y los expone a Objective-C. A continuación, debe heredar de la clase `NSObject` (o una subclase que hereda de `NSObject`), esto agrega varias base de método que permiten a la clase sea KVC compatible. A continuación, el `[Export("Name")]` atributo expone la `Name` propiedad y define el valor de clave que se usará después para tener acceso a la propiedad a través de técnicas KVC y KVO. 

Por último, para poder ser pares clave-valor observado cambia al valor de la propiedad, el descriptor de acceso debe incluir los cambios en su valor en `WillChangeValue` y `DidChangeValue` llamadas a métodos (especificar la misma clave que el `Export` atributo).  Por ejemplo:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Este paso es _muy_ importante para el enlace de datos en Xcode del generador de interfaz (como veremos más adelante en este artículo).

Para obtener más información, consulte Apple [Guía de programación de codificación de pares clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html).

### <a name="keys-and-key-paths"></a>Las claves y las rutas de acceso de claves

Un _clave_ es una cadena que identifica una propiedad específica de un objeto. Normalmente, una clave corresponde al nombre de un método de descriptor de acceso en un valor de clave compatibles con objeto de codificación. Deben usar las claves de codificación ASCII, suelen empezar por una letra minúscula y no puede contener espacios en blanco. Por lo que proporciona el ejemplo anterior, `Name` sería un valor de clave de `Name` propiedad de la `PersonModel` clase. La clave y el nombre de la propiedad que exponen no tienen que ser el mismo, sin embargo, en la mayoría de los casos están.

Un _Key Path_ es una cadena de punto, separados por las claves usadas para especificar una jerarquía de propiedades del objeto para atravesar. La propiedad de la primera clave de la secuencia es en relación con el receptor, y cada clave posteriores se evalúa en relación con el valor de la propiedad anterior. En la misma manera se utiliza la notación de puntos para atravesar un objeto y sus propiedades en una clase de C#.

Por ejemplo, si se ha expandido la `PersonModel` clase y agregan `Child` propiedad:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

La ruta de acceso clave para el nombre del niño sería `self.Child.Name` o simplemente `Child.Name` (según cómo se estaba utilizando el valor de clave).

### <a name="getting-values-using-key-value-coding"></a>Obtención de valores con codificación de clave y valor

El `ValueForKey` método devuelve el valor de la clave especificada (como un `NSString`), relativo a la instancia de la clase KVC recibe la solicitud. Por ejemplo, si `Person` es una instancia de la `PersonModel` clase definida anteriormente:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Esto podría devolver el valor de la `Name` propiedad para esa instancia de `PersonModel`. 

### <a name="setting-values-using-key-value-coding"></a>Valores de configuración mediante la clave y valor de codificación

De forma similar, el `SetValueForKey` establezca el valor de la clave especificada (como un `NSString`), relativo a la instancia de la clase KVC recibe la solicitud. Nuevamente, mediante una instancia de la `PersonModel` clase, como se muestra a continuación:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Debería cambiar el valor de la `Name` propiedad `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Cambia el valor de observación

Mediante la observación de pares clave-valor (KVO), puede adjuntar un observador a una clave específica de una clase conforme KVC y notificará cualquier momento en que se modifica el valor para esa clave (mediante técnicas KVC o acceder directamente a la propiedad especificada en el código de C#). Por ejemplo:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

Ahora, en cualquier momento el `Name` propiedad de la `Person` instancia de la `PersonModel` se modifica la clase, el nuevo valor se escribe en la consola. 

Para obtener más información, consulte Apple [Introducción a la Guía de programación de la observación de pares clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Enlace de datos

Las siguientes secciones se mostrará cómo puede usar una clave y valor de codificación y observar la clase conforme de pares clave-valor para enlazar datos a los elementos de interfaz de usuario en el generador de interfaz de Xcode, en lugar de leer y escribir valores con código C#. De esta manera separe la _modelo de datos_ desde las vistas que se usan para mostrarlos, haciendo que la aplicación de Xamarin.Mac más flexibles y fáciles de mantener. También en gran medida disminuir la cantidad de código que tiene que escribir.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definir el modelo de datos

Antes de poder enlazar datos de un elemento de interfaz de usuario en el generador de interfaz, debe tener una clase conforme KVC/KVO definida en la aplicación de Xamarin.Mac para que actúe como el _modelo de datos_ para el enlace. El modelo de datos proporciona todos los datos que se mostrará en la interfaz de usuario y se recibe ninguna modificación en los datos que el usuario realiza en la interfaz de usuario mientras se ejecuta la aplicación.

Por ejemplo, si estuviera escribiendo una aplicación que administra un grupo de empleados, podría usar la clase siguiente para definir el modelo de datos:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

La mayoría de las características de esta clase se trataron en la [¿qué es la codificación de pares clave-valor](#What_is_Key-Value_Coding) sección anterior. Sin embargo, echemos un vistazo a algunos elementos específicos y algunas consideraciones adicionales que se realizaron para permitir que esta clase para que actúe como un modelo de datos para **controladoras de matriz** y **árbol controladores** (lo que vamos a usar más adelante a los datos enlazar **vistas de árbol**, **vistas de esquema** y **las vistas de colección**).

En primer lugar, porque un empleado podría ser un administrador, hemos usado un `NSArray` (específicamente un `NSMutableArray` por lo que se pueden modificar los valores) para permitir que los empleados que se administraba estar asociados a ellos:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Dos cosas a tener en cuenta aquí:

1. Hemos usado un `NSMutableArray` en lugar de una matriz de C# o colección, puesto que se trata como un requisito para enlazar datos a controles de AppKit estándar **vistas de tabla**, **vistas de esquema** y **colecciones** .
2. La matriz de empleados se expone su conversión a un `NSArray` para datos con fines de enlace y puede cambiar su C# con el formato nombre, `People`, a uno que espera el enlace de datos, `personModelArray` en el formulario **{class_name} matriz** (tenga en cuenta que el primer carácter se ha convertido en minúsculas).

A continuación, necesitamos agregar algunos especialmente nombre métodos públicos para admitir **controladoras de matriz** y **árbol controladores**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Estas permiten que los controladores solicitar y modificar los datos que mostrar. Como el expuesto `NSArray` superiores, estos tienen una convención de nomenclatura muy específica (que difiere de las convenciones de nomenclatura C# típicas):

- `addObject:` : Agrega un objeto a la matriz.
- `insertObject:in{class_name}ArrayAtIndex:` : Indica dónde `{class_name}` es el nombre de la clase. Este método inserta un objeto en la matriz en un índice determinado.
- `removeObjectFrom{class_name}ArrayAtIndex:` : Indica dónde `{class_name}` es el nombre de la clase. Este método quita el objeto de la matriz en un índice determinado.
- `set{class_name}Array:` : Indica dónde `{class_name}` es el nombre de la clase. Este método permite reemplazar el acarreo existente por uno nuevo.

Dentro de estos métodos, nos hemos ajustado los cambios realizados en la matriz en `WillChangeValue` y `DidChangeValue` mensajes para el cumplimiento de KVO.

Por último, desde el `Icon` propiedad se basa en el valor de la `isManager` propiedad, cambia a la `isManager` propiedad podría no reflejarse en el `Icon` para los elementos de interfaz de usuario de datos enlazados (durante KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
``` 

Para corregir eso, usamos el código siguiente:

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Tenga en cuenta que, además de su propia clave, el `isManager` descriptor de acceso también está enviando el `WillChangeValue` y `DidChangeValue` mensajes para el `Icon` modo verá también el cambio de clave.

Vamos a usar el `PersonModel` modelo de datos en el resto de este artículo.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Enlace de datos simple

Con nuestro modelo de datos definido, echemos un vistazo a un ejemplo sencillo de enlace de datos en Interface Builder de Xcode. Por ejemplo, vamos a agregar un formulario a nuestra aplicación de Xamarin.Mac se puede usar para editar el `PersonModel` que definimos anteriormente. Vamos a agregar unos cuantos campos de texto y una casilla de verificación para mostrar y editar las propiedades de nuestro modelo.

En primer lugar, vamos a agregar un nuevo **View Controller** a nuestro **Main.storyboard** de archivos en el generador de interfaz y el nombre de su clase `SimpleViewController`: 

[![Agregar un nuevo controlador de vista](databinding-images/simple01.png "agregando un nuevo controlador de vista")](databinding-images/simple01-large.png#lightbox)

A continuación, vuelva a Visual Studio para Mac, edite el **SimpleViewController.cs** archivo (que se agregó automáticamente a nuestro proyecto) y exponer una instancia de la `PersonModel` nos pondremos nuestro formulario de enlace de datos. Agregue el código siguiente:

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

A continuación cuando se carga la vista, vamos a crear una instancia de nuestra `PersonModel` y rellenarlo con este código:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Ahora debemos crear nuestro formulario, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz. Diseño del formulario para un aspecto similar al siguiente:

[![Editar el guión gráfico en Xcode](databinding-images/simple02.png "editar el guión gráfico en Xcode")](databinding-images/simple02-large.png#lightbox)

Para enlazar el formulario para el `PersonModel` que se exponen a través de la `Person` clave, haga lo siguiente:

1. Seleccione el **nombre empleado** campo de texto y cambie a la **Bindings Inspector**.
2. Compruebe el **enlazar a** cuadro y seleccione **controlador de vista Simple** en la lista desplegable. A continuación escriba `self.Person.Name` para el **Key Path**: 

    [![Escriba la ruta de acceso clave](databinding-images/simple03.png "escribir la ruta de acceso de clave")](databinding-images/simple03-large.png#lightbox)
3. Seleccione el **Occupation** campo de texto y compruebe el **enlazar a** cuadro y seleccione **controlador de vista Simple** en la lista desplegable. A continuación escriba `self.Person.Occupation` para el **Key Path**:  

    [![Escriba la ruta de acceso clave](databinding-images/simple04.png "escribir la ruta de acceso de clave")](databinding-images/simple04-large.png#lightbox)
4. Seleccione el **empleado es un director** Checkbox y compruebe el **enlazar a** cuadro y seleccione **controlador de vista Simple** en la lista desplegable. A continuación escriba `self.Person.isManager` para el **Key Path**:  

    [![Escriba la ruta de acceso clave](databinding-images/simple05.png "escribir la ruta de acceso de clave")](databinding-images/simple05-large.png#lightbox)
5. Seleccione el **número de empleados administrado** campo de texto y compruebe el **enlazar a** cuadro y seleccione **controlador de vista Simple** en la lista desplegable. A continuación escriba `self.Person.NumberOfEmployees` para el **Key Path**:  

    [![Escriba la ruta de acceso clave](databinding-images/simple06.png "escribir la ruta de acceso de clave")](databinding-images/simple06-large.png#lightbox)
6. Si el empleado no es un administrador, desea ocultar la etiqueta de Managed número de empleados y el campo de texto.
7. Seleccione el **número de empleados administrado** etiqueta, expanda el **Hidden** control situado y compruebe el **enlazar a** cuadro y seleccione **controlador de vista Simple** en la lista desplegable. A continuación escriba `self.Person.isManager` para el **Key Path**:  

    [![Escriba la ruta de acceso clave](databinding-images/simple07.png "escribir la ruta de acceso de clave")](databinding-images/simple07-large.png#lightbox)
8. Seleccione `NSNegateBoolean` desde el **valor transformador** desplegable:  

    ![Selección de la transformación clave NSNegateBoolean](databinding-images/simple08.png "seleccionando la transformación clave NSNegateBoolean")
9. Esto indica que el enlace de datos que se ocultará la etiqueta si el valor de la `isManager` propiedad es `false`.
10. Repita los pasos 7 y 8 para el **número de empleados administrado** campo de texto.
11. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Si ejecuta la aplicación, los valores de la `Person` propiedad rellenará automáticamente el formulario:

[![Mostrando un formulario rellenan automáticamente](databinding-images/simple09.png "que muestra un formulario rellenan automáticamente")](databinding-images/simple09-large.png#lightbox)

Los cambios que los usuarios se realiza en el formulario se escribirán en el `Person` propiedad en el controlador de vista. Por ejemplo, anular la selección **empleado es un administrador de** actualizaciones el `Person` instancia de nuestro `PersonModel` y **número de empleados administrado** etiqueta y un campo de texto se ocultan automáticamente (a través de enlace de datos):

[![Ocultar el número de empleados para que no sean administradores](databinding-images/simple10.png "ocultar el número de empleados para que no son administradores")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Enlace de datos de vista de tabla

Ahora que tenemos los aspectos básicos del enlace de datos fuera de la vista, echemos un vistazo a una tarea más compleja de enlace de datos mediante el uso de un _controlador de la matriz_ y enlace de datos a una vista de tabla. Para obtener más información sobre cómo trabajar con vistas de tabla, vea nuestra [vistas de tabla](~/mac/user-interface/table-view.md) documentación.

En primer lugar, vamos a agregar un nuevo **View Controller** a nuestro **Main.storyboard** de archivos en el generador de interfaz y el nombre de su clase `TableViewController`:

[![Agregar un nuevo controlador de vista](databinding-images/table01.png "agregando un nuevo controlador de vista")](databinding-images/table01-large.png#lightbox)

A continuación, vamos a editar el **TableViewController.cs** archivo (que se agregó automáticamente a nuestro proyecto) y exponer una matriz (`NSArray`) de `PersonModel` nos pondremos nuestro formulario de enlace de datos de clases. Agregue el código siguiente:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Tal como se hizo en el `PersonModel` clase anteriormente en el [definir su modelo de datos](#Defining_your_Data_Model) sección, nos hemos expone cuatro métodos públicos especialmente denominados para que el controlador de la matriz y leer y escribir datos de nuestra colección de `PersonModels`.

A continuación cuando se carga la vista, es necesario rellenar la matriz con este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Ahora es necesario crear la vista de tabla, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz. Diseño de la tabla a un aspecto similar al siguiente:

[![Diseñar una nueva vista de tabla](databinding-images/table02.png "disponer de una nueva vista de tabla")](databinding-images/table02-large.png#lightbox)

Necesitamos agregar un **controlador de la matriz** para proporcionar datos enlazados a nuestra tabla, haga lo siguiente:

1. Arrastre un **controlador de matriz** desde el **Inspector de biblioteca** hasta la **Editor de la interfaz**:  

    ![Seleccionar un controlador de la matriz de la biblioteca](databinding-images/table03.png "seleccionando un controlador de la matriz de la biblioteca")
2. Seleccione **controlador de la matriz** en el **jerarquía de la interfaz** y cambie a la **Inspector de atributos**:  

    [![Seleccionar el Inspector de atributos](databinding-images/table04.png "seleccionando el Inspector de atributos")](databinding-images/table04-large.png#lightbox)
3. Escriba `PersonModel` para el **nombre de la clase**, haga clic en el **Plus** botón y agregue tres claves. Denomínelos `Name`, `Occupation` y `isManager`:  

    ![Agregar las rutas de acceso de claves necesarios](databinding-images/table05.png "agregar las rutas de acceso de claves necesarios")
4. Esto indica que el controlador de la matriz lo que se está administrando una matriz de, y qué propiedades debe exponer (a través de claves).
5. Cambie a la **Bindings Inspector** y en **matriz contenido** seleccione **enlazar a** y **controlador de vista de tabla**. Escriba un **ruta de la clave del modelo** de `self.personModelArray`:  

    ![Escriba una ruta de acceso clave](databinding-images/table06.png "escribir una ruta de acceso")
6. Esto enlaza el controlador de la matriz a la matriz de `PersonModels` que se exponen en nuestro controlador de vista.

Ahora, debemos enlazar la vista de tabla para el controlador de la matriz, realice lo siguiente:

1. Seleccione la vista de tabla y el **enlace Inspector**:  

    [![Seleccionar el Inspector de enlace](databinding-images/table07.png "seleccionando el Inspector de enlace")](databinding-images/table07-large.png#lightbox)
2. En el **contenido de la tabla** control situado, seleccione **enlazar a** y **controlador de la matriz**. Escriba `arrangedObjects` para el **controlador clave** campo:  

    ![Definir la clave del controlador](databinding-images/table08.png "definir la clave del controlador")
3. Seleccione el **celda de vista de tabla** bajo el **empleado** columna. En el **Bindings Inspector** bajo el **valor** control situado, seleccione **enlazar a** y **vista de la celda de tabla**. Escriba `objectValue.Name` para el **ruta de la clave del modelo**:  

    [![Establecer la ruta de acceso de clave de modelo](databinding-images/table09.png "establecer la ruta de acceso de clave de modelo")](databinding-images/table09-large.png#lightbox)
4. `objectValue` es el actual `PersonModel` en la matriz está administrada mediante el controlador de la matriz.
5. Seleccione el **celda de vista de tabla** bajo el **ocupación** columna. En el **Bindings Inspector** bajo el **valor** control situado, seleccione **enlazar a** y **vista de la celda de tabla**. Escriba `objectValue.Occupation` para el **ruta de la clave del modelo**:  

    [![Establecer la ruta de acceso de clave de modelo](databinding-images/table10.png "establecer la ruta de acceso de clave de modelo")](databinding-images/table10-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Si se ejecuta la aplicación, se rellenará la tabla con la matriz de `PersonModels`:

[![Ejecutar la aplicación](databinding-images/table11.png "ejecutando la aplicación")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Enlace de datos de vista de esquema

enlace de datos en una vista de esquema es muy similar al enlace en una vista de tabla. La principal diferencia es que vamos a usar un **controlador árbol** en lugar de un **controlador de la matriz** para proporcionar los datos enlazados a la vista de esquema. Para obtener más información sobre cómo trabajar con vistas de esquema, vea nuestra [vistas de esquema](~/mac/user-interface/outline-view.md) documentación.

En primer lugar, vamos a agregar un nuevo **View Controller** a nuestro **Main.storyboard** de archivos en el generador de interfaz y el nombre de su clase `OutlineViewController`: 

[![Agregar un nuevo controlador de vista](databinding-images/outline01.png "agregando un nuevo controlador de vista")](databinding-images/outline01-large.png#lightbox)

A continuación, vamos a editar el **OutlineViewController.cs** archivo (que se agregó automáticamente a nuestro proyecto) y exponer una matriz (`NSArray`) de `PersonModel` nos pondremos nuestro formulario de enlace de datos de clases. Agregue el código siguiente:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Tal como se hizo en el `PersonModel` clase anteriormente en el [definir su modelo de datos](#Defining_your_Data_Model) sección, nos hemos expone cuatro métodos públicos especialmente denominados para que el controlador de árbol y leer y escribir datos de nuestra colección de `PersonModels`.

A continuación cuando se carga la vista, es necesario rellenar la matriz con este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Ahora es necesario crear la vista de esquema, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz. Diseño de la tabla a un aspecto similar al siguiente:

[![Creación de la vista de esquema](databinding-images/outline02.png "crear la vista de esquema")](databinding-images/outline02-large.png#lightbox)

Necesitamos agregar un **controlador árbol** para proporcionar datos enlazados a nuestro esquema, haga lo siguiente:

1. Arrastre un **árbol controlador** desde el **Inspector de biblioteca** hasta la **Editor de la interfaz**:  

    ![Al seleccionar un controlador de árbol de la biblioteca](databinding-images/outline03.png "al seleccionar un controlador de árbol de la biblioteca")
2. Seleccione **controlador árbol** en el **jerarquía de la interfaz** y cambie a la **Inspector de atributos**:  

    [![Seleccionar el Inspector de atributos](databinding-images/outline04.png "seleccionando el Inspector de atributos")](databinding-images/outline04-large.png#lightbox)
3. Escriba `PersonModel` para el **nombre de la clase**, haga clic en el **Plus** botón y agregue tres claves. Denomínelos `Name`, `Occupation` y `isManager`:  

    ![Agregar las rutas de acceso de claves necesarios](databinding-images/outline05.png "agregar las rutas de acceso de claves necesarios")
4. Esto indica que el controlador de árbol de lo que se está administrando una matriz de, y qué propiedades debe exponer (a través de claves).
5. En el **controlador árbol** sección, escriba `personModelArray` para **elementos secundarios**, escriba `NumberOfEmployees` bajo el **recuento** y escriba `isEmployee` en  **Hoja**:  

    ![Establecer las rutas de acceso de claves de controlador de árbol](databinding-images/outline05.png "establecer las rutas de acceso de claves de controlador de árbol")
6. Esto indica que el controlador de árbol a dónde se encuentra a cualquier elemento secundario de nodos, son cuántos nodos secundarios existe y si el nodo actual tiene nodos secundarios.
7. Cambie a la **Bindings Inspector** y en **matriz contenido** seleccione **enlazar a** y **propietario del archivo**. Escriba un **ruta de la clave del modelo** de `self.personModelArray`:  

    ![Edición de la ruta de acceso clave](databinding-images/outline06.png "editar la ruta de acceso de clave")
8. Esto enlaza el controlador de árbol a la matriz de `PersonModels` que se exponen en nuestro controlador de vista.

Ahora es necesario enlazar la vista de esquema con el controlador de árbol, haga lo siguiente:

1. Seleccione la vista de esquema y, en el **enlace Inspector** seleccione:  

    [![Seleccionar el Inspector de enlace](databinding-images/outline07.png "seleccionando el Inspector de enlace")](databinding-images/outline07-large.png#lightbox)
2. En el **contenido de la vista de esquema** control situado, seleccione **enlazar a** y **controlador árbol**. Escriba `arrangedObjects` para el **controlador clave** campo:  

    ![Configuración de la clave del controlador](databinding-images/outline08.png "establecimiento de la clave del controlador")
3. Seleccione el **celda de vista de tabla** bajo el **empleado** columna. En el **Bindings Inspector** bajo el **valor** control situado, seleccione **enlazar a** y **vista de la celda de tabla**. Escriba `objectValue.Name` para el **ruta de la clave del modelo**:  

    [![Escriba la ruta de clave del modelo](databinding-images/outline09.png "escribir la ruta de acceso de clave de modelo")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` es el actual `PersonModel` en la matriz está administrada por el controlador de árbol.
5. Seleccione el **celda de vista de tabla** bajo el **ocupación** columna. En el **Bindings Inspector** bajo el **valor** control situado, seleccione **enlazar a** y **vista de la celda de tabla**. Escriba `objectValue.Occupation` para el **ruta de la clave del modelo**:  

    [![Escriba la ruta de clave del modelo](databinding-images/outline10.png "escribir la ruta de acceso de clave de modelo")](databinding-images/outline10-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Si se ejecuta la aplicación, el esquema se rellenará con la matriz de `PersonModels`:

[![Ejecutar la aplicación](databinding-images/outline11.png "ejecutando la aplicación")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Enlace de datos de vista de colección

Enlace de datos con una vista de colección es muy similar a enlazar con una vista de tabla, como un controlador de la matriz se usa para proporcionar datos para la colección. Puesto que la vista de colección no tiene un formato de presentación preestablecido, más trabajo es necesario para proporcionar comentarios de interacción de usuario y realizar el seguimiento de la selección del usuario.

> [!IMPORTANT]
> Debido a un problema en Xcode 7 y Mac OS 10.11 (y versiones posteriores), las vistas de colección son no puede utilizarse dentro de los archivos de guión gráfico (.storyboard). Como resultado, deberá seguir usando archivos .xib para definir las vistas de colección para las aplicaciones de Xamarin.Mac. Consulte nuestra [las vistas de colección](~/mac/user-interface/collection-view.md) documentación para obtener más información.

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`: 

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Depuración de bloqueos nativos

Cometer un error en los enlaces de datos puede dar lugar a un _bloqueos nativos_ en código no administrado y hacer que la aplicación de Xamarin.Mac deje de funcionar por completo con un `SIGABRT` error:

[![Ejemplo de un cuadro de diálogo de bloqueo nativo](databinding-images/debug01.png "ejemplo de un cuadro de diálogo de bloqueo nativo")](databinding-images/debug01-large.png#lightbox)

Normalmente, hay cuatro causas principales de bloqueos nativos durante el enlace de datos:

1. El modelo de datos no se hereda de `NSObject` o una subclase de `NSObject`.
2. No se expuso la propiedad de Objective-C mediante la `[Export("key-name")]` atributo.
3. No ajusta los cambios al valor del descriptor de acceso de `WillChangeValue` y `DidChangeValue` llamadas a métodos (especificar la misma clave que el `Export` atributo).
4. Tiene una clave mal escrita o es incorrecta en el **enlace Inspector** en Interface Builder.

### <a name="decoding-a-crash"></a>Descodificar un bloqueo

Vamos a provocar un bloqueo nativo en el enlace de datos por lo que podemos mostramos cómo buscar y corregirlo. En el generador de interfaz, vamos a cambiar el enlace de la primera etiqueta en el ejemplo de la vista de colección de `Name` a `Title`:

[![Edición de la clave de enlace](databinding-images/debug02.png "editar la clave de enlace")](databinding-images/debug02-large.png#lightbox)

Vamos a guardar el cambio, cambie a Visual Studio para Mac se sincronicen con Xcode y ejecutar la aplicación. Cuando se muestre la vista de colección, la aplicación se bloqueará momentáneamente con un `SIGABRT` error (como se muestra en el **resultado de la aplicación** en Visual Studio para Mac) ya que el `PersonModel` no expone una propiedad con la clave `Title`:

[![Ejemplo de un error de enlace](databinding-images/debug03.png "ejemplo de un error de enlace")](databinding-images/debug03-large.png#lightbox)

Si se desplaza a la parte superior del error en la **resultado de la aplicación** podemos ver la clave para solucionar el problema:

[![Buscar el problema en el registro de errores](databinding-images/debug04.png "buscar el problema en el registro de errores")](databinding-images/debug04-large.png#lightbox)

Esta línea nos indica que la clave `Title` no existe en el objeto que se está enlazando a. Si cambiamos el enlace hacia `Name` en Interface Builder, guardar, sincronización, vuelva a compilar y ejecutar, la aplicación se ejecutará según lo esperado sin ningún problema.

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con el enlace de datos y la codificación de pares clave-valor en una aplicación de Xamarin.Mac. En primer lugar, examinamos expone una clase de C# y Objective-C con codificación de clave-valor (KVC) y pares clave-valor observando (KVO). A continuación, se ha mostrado cómo usar una clase conforme KVO y enlazar datos a los elementos de interfaz de usuario de Interface Builder de Xcode. Por último, se ha mostrado de enlace de datos complejos mediante **controladoras de matriz** y **árbol controladores**.


## <a name="related-links"></a>Vínculos relacionados

- [Guión gráfico de MacDatabinding (ejemplo)](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding Xib (ejemplo)](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controles estándar](~/mac/user-interface/standard-controls.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Vistas de colección](~/mac/user-interface/collection-view.md)
- [Guía de programación de codificación de pares clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introducción a la Guía de programación de observación de pares clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introducción a los enlaces de cacao temas de programación](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introducción a la referencia de enlaces de Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Directrices de interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
