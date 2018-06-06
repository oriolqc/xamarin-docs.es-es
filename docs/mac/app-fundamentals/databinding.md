---
title: Enlace de datos y el valor de clave de codificación en Xamarin.Mac
description: Este artículo incluye el uso de codificación y observar para permitir el enlace de datos a los elementos de interfaz de usuario en el generador de interfaz de Xcode de clave y valor de clave y valor.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 88567e47f488a94fcf7334584a678c9689b83306
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792143"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Enlace de datos y el valor de clave de codificación en Xamarin.Mac

_Este artículo incluye el uso de codificación y observar para permitir el enlace de datos a los elementos de interfaz de usuario en el generador de interfaz de Xcode de clave y valor de clave y valor._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tendrá acceso a la misma clave y valor de codificación y técnicas de enlace de datos que un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para enlazar datos con elementos de interfaz de usuario en lugar de escribir código.

Mediante el uso de clave y valor de codificación y técnicas de enlace de la aplicación Xamarin.Mac de datos, puede reducir considerablemente la cantidad de código que se debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de copia de seguridad (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicación más flexible diseño.

[![Un ejemplo de la aplicación en ejecución](databinding-images/intro01.png "muestra un ejemplo de la aplicación en ejecución")](databinding-images/intro01-large.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con valores de clave de codificación y enlace de datos en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` atributos se utiliza para conectarlo las clases de C# a los objetos de Objective-C y la interfaz de usuario de elementos.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>¿Qué es la clave y valor de codificación

Valor de clave de codificación (KVC) es un mecanismo para tener acceso a propiedades de un objeto indirectamente, mediante claves (cadenas con formato especial) para identificar las propiedades en lugar de obtener acceso a ellos a través de las variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de clave y valor codificación compatible con los descriptores de acceso en la aplicación Xamarin.Mac, obtendrá acceso a otras características de macOS (anteriormente conocido como OS X) como la observación de clave y valor (KVO), enlace de datos, datos básicos, enlaces de cacao y scriptability.

Mediante el uso de clave y valor de codificación y técnicas de enlace de la aplicación Xamarin.Mac de datos, puede reducir considerablemente la cantidad de código que se debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de copia de seguridad (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicación más flexible diseño. 

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

En primer lugar, el `[Register("PersonModel")]` registra la clase de atributo y lo expone al objetivo de C. A continuación, la clase debe heredar de `NSObject` (o una subclase que hereda de `NSObject`), esto agrega varias base de método que permiten a la clase como KVC compatible. Después, el `[Export("Name")]` atributo expone la `Name` propiedad y define el valor de clave que se usará más adelante para obtener acceso a la propiedad a través de técnicas KVC y KVO. 

Por último, para que pueda ser observado clave-valor cambia al valor de la propiedad, el descriptor de acceso debe contener los cambios en su valor en `WillChangeValue` y `DidChangeValue` llamadas al método (especificar la misma clave que el `Export` atributo).  Por ejemplo:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Este paso es _muy_ importante para el enlace de datos en Xcode del generador de interfaz (como veremos más adelante en este artículo).

Para obtener más información, vea de Apple [Guía de programación de la codificación en la clave y valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html).

### <a name="keys-and-key-paths"></a>Las claves y las rutas de acceso de claves

A _clave_ es una cadena que identifica una propiedad específica de un objeto. Normalmente, una clave corresponde al nombre de un método de descriptor de acceso en un codificación compatible con objeto de clave y valor. Las claves deben usar la codificación ASCII, por lo general comienzan con una letra minúscula y no puede contener espacios en blanco. Por lo que proporciona el ejemplo anterior, `Name` sería un valor de clave de `Name` propiedad de la `PersonModel` clase. La clave y el nombre de la propiedad que exponen no tienen que ser el mismo, sin embargo, en la mayoría de los casos están.

A _ruta de acceso de clave_ es una cadena de punto separados claves que se utilizan para especificar una jerarquía de propiedades del objeto para atravesar. La propiedad de la primera clave de la secuencia está relacionada con el receptor, y cada clave posterior se evalúa en relación con el valor de la propiedad anterior. En la misma forma se utiliza la notación de puntos para atravesar un objeto y sus propiedades en una clase de C#.

Por ejemplo, si expande el `PersonModel` clase y agregan `Child` propiedad:

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

La ruta de acceso de clave para el nombre del niño sería `self.Child.Name` o simplemente `Child.Name` (según cómo se estaba utilizando el valor de clave).

### <a name="getting-values-using-key-value-coding"></a>Obtener valores utilizando la clave y valor de codificación

El `ValueForKey` método devuelve el valor de la clave especificada (como un `NSString`), relativa a la instancia de la clase KVC recibir la solicitud. Por ejemplo, si `Person` es una instancia de la `PersonModel` clase definida anteriormente:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Esto devolverá el valor de la `Name` propiedad para esa instancia de `PersonModel`. 

### <a name="setting-values-using-key-value-coding"></a>Valores de configuración mediante la clave y valor de codificación

De forma similar, el `SetValueForKey` establecer el valor de la clave especificada (como un `NSString`), relativa a la instancia de la clase KVC recibir la solicitud. De nuevo, mediante una instancia de la `PersonModel` de la clase, tal y como se muestra a continuación:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Debería cambiar el valor de la `Name` propiedad `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Obedezca a cambios de valor

Mediante la observación de clave y valor (KVO), puede adjuntar un observador a una clave específica de una clase conforme a KVC y recibir una notificación siempre que se modifica el valor para esa clave (si se usa técnicas KVC o tengan acceso directo a la propiedad especificada en el código de C#). Por ejemplo:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

Ahora, en cualquier momento la `Name` propiedad de la `Person` instancia de la `PersonModel` clase se modifica, el nuevo valor se escribe en la consola. 

Para obtener más información, vea de Apple [Introducción a la Guía de programación de observación de clave y valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Enlace de datos

Las secciones siguientes muestran cómo puede usar una clave y valor de codificación y clave-valor observar la clase conforme a enlazar los datos a los elementos de interfaz de usuario en el generador de interfaz de Xcode, en lugar de leer y escribir valores mediante código C#. De esta manera de separar la _modelo de datos_ de las vistas que se usan para mostrarlos, hacer que la aplicación Xamarin.Mac más flexibles y fáciles de mantener. También en gran medida disminuir la cantidad de código que tiene que escribir.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definir el modelo de datos

Antes de que pueda enlazar datos de un elemento de interfaz de usuario en el generador de interfaz, debe tener una clase conforme a KVC/KVO definida en la aplicación Xamarin.Mac para que actúe como el _modelo de datos_ para el enlace. El modelo de datos proporciona todos los datos que se mostrará en la interfaz de usuario y recibe ninguna modificación en los datos que el usuario realice en la interfaz de usuario mientras se ejecuta la aplicación.

Por ejemplo, si estuviera escribiendo una aplicación que administra un grupo de empleados, podría utilizar la siguiente clase para definir el modelo de datos:

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

La mayoría de las características de esta clase se trata en la [¿qué es la clave y valor de codificación](#What_is_Key-Value_Coding) sección anterior. Sin embargo, echemos un vistazo a unos cuantos elementos específicos y algunas consideraciones adicionales que se realizaron para permitir que esta clase para que actúe como un modelo de datos para **controladores de la matriz** y **árbol controladores** (que se usará más adelante para datos enlazar **vistas de árbol**, **vistas de esquema** y **vistas de colección**).

En primer lugar, porque un empleado puede ser un administrador, hemos usado un `NSArray` (específicamente un `NSMutableArray` por lo que se pueden modificar los valores) para permitir que los empleados que administran para adjuntarlo a ellos:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Tenga en cuenta aquí dos cosas:

1. Utilizamos un `NSMutableArray` en lugar de un estándar C# matriz o colección puesto que se trata como un requisito para enlazar datos a controles de AppKit **vistas de tabla**, **vistas de esquema** y **colecciones** .
2. La matriz de empleados se expone al convertir a un `NSArray` para datos con fines de enlace y cambiar su C# con el formato nombre, `People`, a uno que espera que el enlace de datos, `personModelArray` en el formulario **{class_name} matriz** (tenga en cuenta que el primer carácter se ha convertido en minúsculas).

A continuación, necesitamos agregar algunos especialmente nombre métodos públicos para admitir **controladores de la matriz** y **árbol controladores**:

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

Estas permiten que los controladores solicitar y modificar los datos que se muestren. Como el expuesto `NSArray` los casos anteriores, tienen una convención de nomenclatura muy específica (que difiere de las convenciones de nomenclatura C# típicas):

- `addObject:` : Agrega un objeto a la matriz.
- `insertObject:in{class_name}ArrayAtIndex:` -Donde `{class_name}` es el nombre de la clase. Este método inserta un objeto en la matriz en un índice determinado.
- `removeObjectFrom{class_name}ArrayAtIndex:` -Donde `{class_name}` es el nombre de la clase. Este método quita el objeto en la matriz en un índice determinado.
- `set{class_name}Array:` -Donde `{class_name}` es el nombre de la clase. Este método permite reemplazar el aporte existente por uno nuevo.

Dentro de estos métodos, nos hemos ajustado los cambios en la matriz en `WillChangeValue` y `DidChangeValue` mensajes para el cumplimiento de KVO.

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

Para corregir eso, se utiliza el código siguiente:

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

Tenga en cuenta que además su propia clave, el `isManager` descriptor de acceso también está enviando el `WillChangeValue` y `DidChangeValue` los mensajes para ver el `Icon` modo verá también el cambio de clave.

Usaremos el `PersonModel` modelo de datos en el resto de este artículo.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Enlace de datos simple

Con nuestro modelo de datos definido, echemos un vistazo a un ejemplo sencillo de enlace de datos en el generador de interfaz de Xcode. Por ejemplo, vamos a agregar un formulario a nuestra aplicación Xamarin.Mac que puede utilizar para editar el `PersonModel` que hemos definido anteriormente. Vamos a agregar algunos campos de texto y una casilla de verificación para mostrar y editar las propiedades de nuestro modelo.

En primer lugar, vamos a agregar un nuevo **View Controller** a nuestro **Main.storyboard** de archivos en el generador de interfaz y el nombre de su clase `SimpleViewController`: 

[![Agregar un nuevo controlador de vista](databinding-images/simple01.png "agregando un nuevo controlador de vista")](databinding-images/simple01-large.png#lightbox)

A continuación, vuelva a Visual Studio para Mac, edite el **SimpleViewController.cs** archivo (que se agregó automáticamente a nuestro proyecto) y exponer una instancia de la `PersonModel` que estaremos nuestro formulario de enlace de datos. Agregue el código siguiente:

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

A continuación cuando se carga la vista, vamos a crear una instancia de nuestro `PersonModel` y rellenarlo con este código:

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

Ahora debemos crear nuestro formulario, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz. Diseño del formulario para el siguiente aspecto:

[![Editar el guión gráfico en Xcode](databinding-images/simple02.png "editar el guión gráfico en Xcode.")](databinding-images/simple02-large.png#lightbox)

Para enlazar el formulario para el `PersonModel` que se expone a través de la `Person` clave, haga lo siguiente:

1. Seleccione el **nombre de empleado** campo de texto y cambie a la **enlaces Inspector**.
2. Compruebe el **enlazar a** cuadro y seleccione **Simple View Controller** en la lista desplegable. A continuación escriba `self.Person.Name` para el **ruta de la clave**: 

    [![Escriba la ruta de acceso de clave](databinding-images/simple03.png "escribir la ruta de acceso de clave")](databinding-images/simple03-large.png#lightbox)
3. Seleccione el **ocupación** campo de texto y compruebe el **enlazar a** cuadro y seleccione **Simple View Controller** en la lista desplegable. A continuación escriba `self.Person.Occupation` para el **ruta de la clave**:  

    [![Escriba la ruta de acceso de clave](databinding-images/simple04.png "escribir la ruta de acceso de clave")](databinding-images/simple04-large.png#lightbox)
4. Seleccione el **empleado es un administrador de** casilla de verificación y comprobar la **enlazar a** cuadro y seleccione **Simple View Controller** en la lista desplegable. A continuación escriba `self.Person.isManager` para el **ruta de la clave**:  

    [![Escriba la ruta de acceso de clave](databinding-images/simple05.png "escribir la ruta de acceso de clave")](databinding-images/simple05-large.png#lightbox)
5. Seleccione el **número de empleados administrado** campo de texto y compruebe el **enlazar a** cuadro y seleccione **Simple View Controller** en la lista desplegable. A continuación escriba `self.Person.NumberOfEmployees` para el **ruta de la clave**:  

    [![Escriba la ruta de acceso de clave](databinding-images/simple06.png "escribir la ruta de acceso de clave")](databinding-images/simple06-large.png#lightbox)
6. Si el empleado no es un administrador, desea ocultar la etiqueta de administrado número de empleados y el campo de texto.
7. Seleccione el **número de empleados administrado** etiqueta, expanda la **Hidden** turndown y compruebe el **enlazar a** cuadro y seleccione **Simple View Controller** en la lista desplegable. A continuación escriba `self.Person.isManager` para el **ruta de la clave**:  

    [![Escriba la ruta de acceso de clave](databinding-images/simple07.png "escribir la ruta de acceso de clave")](databinding-images/simple07-large.png#lightbox)
8. Seleccione `NSNegateBoolean` desde el **valor transformador** desplegable:  

    ![Selección de la transformación de clave de NSNegateBoolean](databinding-images/simple08.png "seleccionando la transformación de clave NSNegateBoolean")
9. Esto indica que el enlace de datos que se ocultará la etiqueta si el valor de la `isManager` propiedad es `false`.
10. Repita los pasos 7 y 8 para el **número de empleados administrado** campo de texto.
11. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Si se ejecuta la aplicación, los valores de la `Person` propiedad rellenará automáticamente nuestro formulario:

[![Mostrar un formulario rellena automáticamente](databinding-images/simple09.png "que muestra un formulario rellena automáticamente")](databinding-images/simple09-large.png#lightbox)

Los cambios que los usuarios se realiza en el formulario se escribirá en el `Person` propiedad en el controlador de vista. Por ejemplo, si **empleado es un administrador de** actualizaciones el `Person` instancia de nuestro `PersonModel` y **número de empleados administrado** etiqueta y un campo de texto se ocultan automáticamente (a través de enlace de datos):

[![Ocultar el número de empleados para no administradores](databinding-images/simple10.png "ocultar el número de empleados para no administradores")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Enlace de datos de vista de tabla

Ahora que tenemos los conceptos básicos del enlace de datos fuera de la vista, echemos un vistazo a una tarea más compleja de enlace de datos mediante el uso de un _controlador de la matriz_ y enlace de datos a una vista de tabla. Para obtener más información sobre cómo trabajar con vistas de tabla, vea nuestra [vistas de la tabla](~/mac/user-interface/table-view.md) documentación.

En primer lugar, vamos a agregar un nuevo **View Controller** a nuestro **Main.storyboard** de archivos en el generador de interfaz y el nombre de su clase `TableViewController`:

[![Agregar un nuevo controlador de vista](databinding-images/table01.png "agregando un nuevo controlador de vista")](databinding-images/table01-large.png#lightbox)

A continuación, editar la **TableViewController.cs** archivo (que se agregó automáticamente a nuestro proyecto) y exponer una matriz (`NSArray`) de `PersonModel` clases que estaremos nuestro formulario de enlace de datos. Agregue el código siguiente:

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

Al igual que hicimos en la `PersonModel` clase anteriormente en el [definir el modelo de datos](#Defining_your_Data_Model) sección, nos hemos expone cuatro métodos públicos especialmente con nombre para que el controlador de la matriz y leer y escribir datos de la colección de `PersonModels`.

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

Ahora necesitamos crear la vista de tabla, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz. Diseño de la tabla que se va a buscar algo parecido a lo siguiente:

[![Diseñar una nueva vista de tabla](databinding-images/table02.png "diseñar una nueva vista de tabla")](databinding-images/table02-large.png#lightbox)

Tenemos que agregar una **controlador de la matriz** para proporcionar datos enlazados con la tabla, haga lo siguiente:

1. Arrastre un **controlador de matriz** desde el **biblioteca Inspector** en el **Editor de la interfaz**:  

    ![Seleccionar un controlador de la matriz de la biblioteca](databinding-images/table03.png "seleccionar un controlador de la matriz de la biblioteca")
2. Seleccione **controlador de la matriz** en el **jerarquía de interfaz** y cambie a la **Inspector de atributo**:  

    [![Al seleccionar el Inspector de atributos](databinding-images/table04.png "seleccionando el Inspector de atributos")](databinding-images/table04-large.png#lightbox)
3. Escriba `PersonModel` para el **nombre de la clase**, haga clic en el **más** botón y agregue tres claves. Denomínelos `Name`, `Occupation` y `isManager`:  

    ![Agregar las rutas de acceso de claves necesarios](databinding-images/table05.png "agregar las rutas de acceso de claves necesarios")
4. Esto indica que el controlador de la matriz lo que está administrando una matriz de, y qué propiedades debe exponer (a través de claves).
5. Cambie a la **enlaces Inspector** y en **matriz contenido** seleccione **enlazar a** y **controlador de vista de tabla**. Escriba un **ruta de la clave del modelo** de `self.personModelArray`:  

    ![Escriba una ruta de acceso de clave](databinding-images/table06.png "escribir una ruta de acceso de clave")
6. Esto vincula el controlador de la matriz a la matriz de `PersonModels` que se exponen en nuestro controlador de vista.

Ahora necesitamos enlazar la vista de tabla para el controlador de la matriz, haga lo siguiente:

1. Seleccione la vista de tabla y la **enlace Inspector**:  

    [![Al seleccionar el Inspector de enlace](databinding-images/table07.png "seleccionando el Inspector de enlace")](databinding-images/table07-large.png#lightbox)
2. En el **contenido de la tabla** turndown, seleccione **enlazar a** y **controlador de la matriz**. Escriba `arrangedObjects` para el **controlador clave** campo:  

    ![Definir la clave de controlador](databinding-images/table08.png "definir la clave de controlador")
3. Seleccione el **celda de la vista de tabla** en el **empleado** columna. En el **enlaces Inspector** en el **valor** turndown, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Name` para el **ruta de la clave del modelo**:  

    [![Establecer la ruta de acceso de clave de modelo](databinding-images/table09.png "establecer la ruta de acceso de clave de modelo")](databinding-images/table09-large.png#lightbox)
4. `objectValue` es la actual `PersonModel` en la matriz está administrada por el controlador de la matriz.
5. Seleccione el **celda de la vista de tabla** en el **ocupación** columna. En el **enlaces Inspector** en el **valor** turndown, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Occupation` para el **ruta de la clave del modelo**:  

    [![Establecer la ruta de acceso de clave de modelo](databinding-images/table10.png "establecer la ruta de acceso de clave de modelo")](databinding-images/table10-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Si se ejecuta la aplicación, la tabla se llenará con nuestra matriz de `PersonModels`:

[![Ejecutar la aplicación](databinding-images/table11.png "ejecutando la aplicación")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Enlace de datos de vista de esquema

enlace de datos en una vista de esquema es muy similar a enlazar en una vista de tabla. La principal diferencia es que se usará un **controlador árbol** en lugar de un **controlador de la matriz** para proporcionar los datos enlazados a la vista de esquema. Para obtener más información sobre cómo trabajar con vistas de esquema, vea nuestra [vistas de esquema](~/mac/user-interface/outline-view.md) documentación.

En primer lugar, vamos a agregar un nuevo **View Controller** a nuestro **Main.storyboard** de archivos en el generador de interfaz y el nombre de su clase `OutlineViewController`: 

[![Agregar un nuevo controlador de vista](databinding-images/outline01.png "agregando un nuevo controlador de vista")](databinding-images/outline01-large.png#lightbox)

A continuación, editar la **OutlineViewController.cs** archivo (que se agregó automáticamente a nuestro proyecto) y exponer una matriz (`NSArray`) de `PersonModel` clases que estaremos nuestro formulario de enlace de datos. Agregue el código siguiente:

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

Al igual que hicimos en la `PersonModel` clase anteriormente en el [definir el modelo de datos](#Defining_your_Data_Model) sección, nos hemos expone cuatro métodos públicos especialmente con nombre para que el controlador de árbol y leer y escribir datos de la colección de `PersonModels`.

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

Ahora debemos crear la vista de esquema, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz. Diseño de la tabla que se va a buscar algo parecido a lo siguiente:

[![Crear la vista de esquema](databinding-images/outline02.png "crear la vista de esquema")](databinding-images/outline02-large.png#lightbox)

Tenemos que agregar una **controlador árbol** para proporcionar datos enlazados a nuestro esquema, haga lo siguiente:

1. Arrastre un **árbol controlador** desde el **biblioteca Inspector** en el **Editor de la interfaz**:  

    ![Seleccionar un controlador de árbol de la biblioteca](databinding-images/outline03.png "seleccionar un controlador de árbol de la biblioteca")
2. Seleccione **controlador árbol** en el **jerarquía de interfaz** y cambie a la **Inspector de atributo**:  

    [![Al seleccionar el Inspector de atributo](databinding-images/outline04.png "seleccionando el Inspector de atributo")](databinding-images/outline04-large.png#lightbox)
3. Escriba `PersonModel` para el **nombre de la clase**, haga clic en el **más** botón y agregue tres claves. Denomínelos `Name`, `Occupation` y `isManager`:  

    ![Agregar las rutas de acceso de claves necesarios](databinding-images/outline05.png "agregar las rutas de acceso de claves necesarios")
4. Esto indica que el controlador de árbol lo que está administrando una matriz de, y qué propiedades debe exponer (a través de claves).
5. En el **controlador árbol** sección, escriba `personModelArray` para **elementos secundarios**, escriba `NumberOfEmployees` en el **recuento** y escriba `isEmployee` en  **Hoja**:  

    ![Establecer las rutas de acceso de claves de controlador de árbol](databinding-images/outline05.png "establecer las rutas de acceso de claves de controlador de árbol")
6. Esto indica que el controlador de árbol a dónde encontrar a todos los nodos, están cuántos nodos secundarios existe y si el nodo actual tiene nodos secundarios.
7. Cambie a la **enlaces Inspector** y en **matriz contenido** seleccione **enlazar a** y **propietario del archivo**. Escriba un **ruta de la clave del modelo** de `self.personModelArray`:  

    ![Edición de la ruta de acceso de clave](databinding-images/outline06.png "modificar la ruta de acceso de clave")
8. Esto vincula el controlador de árbol a la matriz de `PersonModels` que se exponen en nuestro controlador de vista.

Ahora necesitamos enlazar la vista de esquema con el controlador de árbol, haga lo siguiente:

1. Seleccione la vista de esquema y en el **enlace Inspector** seleccione:  

    [![Al seleccionar el Inspector de enlace](databinding-images/outline07.png "seleccionando el Inspector de enlace")](databinding-images/outline07-large.png#lightbox)
2. En el **contenido de la vista de esquema** turndown, seleccione **enlazar a** y **controlador árbol**. Escriba `arrangedObjects` para el **controlador clave** campo:  

    ![Configuración de la clave de controlador](databinding-images/outline08.png "configuración de la clave de controlador")
3. Seleccione el **celda de la vista de tabla** en el **empleado** columna. En el **enlaces Inspector** en el **valor** turndown, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Name` para el **ruta de la clave del modelo**:  

    [![Escriba la ruta de acceso de clave de modelo](databinding-images/outline09.png "escribir la ruta de acceso de clave de modelo")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` es la actual `PersonModel` en la matriz está administrada por el controlador de árbol.
5. Seleccione el **celda de la vista de tabla** en el **ocupación** columna. En el **enlaces Inspector** en el **valor** turndown, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Occupation` para el **ruta de la clave del modelo**:  

    [![Escriba la ruta de acceso de clave de modelo](databinding-images/outline10.png "escribir la ruta de acceso de clave de modelo")](databinding-images/outline10-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Si se ejecuta la aplicación, el esquema se rellenará con nuestra matriz de `PersonModels`:

[![Ejecutar la aplicación](databinding-images/outline11.png "ejecutando la aplicación")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Enlace de datos de vista de colección

Enlace de datos con una vista de colección es muy similar a enlazar con una vista de tabla, como un controlador de la matriz se utiliza para proporcionar datos para la colección. Puesto que la vista de colección no tiene un formato de presentación de valores preestablecidos, es necesario proporcionar comentarios de interacción de usuario y realizar un seguimiento de la selección del usuario más trabajo.

> [!IMPORTANT]
> Debido a un problema en Xcode 7 y macOS 10.11 (y versiones posteriores), las vistas de colección son no puede utilizarse dentro de los archivos de un guión gráfico (.storyboard). Como resultado, debe seguir utilizando archivos .xib para definir las vistas de colección para las aplicaciones de Xamarin.Mac. Visite nuestro [vistas de colección](~/mac/user-interface/collection-view.md) documentación para obtener más información.

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

## <a name="debugging-native-crashes"></a>Depurar los bloqueos nativo

Cometer un error en los enlaces de datos puede dar lugar a un _bloquearse nativo_ en código no administrado y hacer que la aplicación Xamarin.Mac deje de funcionar por completo con un `SIGABRT` error:

[![Ejemplo de un cuadro de diálogo de bloqueo nativo](databinding-images/debug01.png "ejemplo de un cuadro de diálogo de bloqueo nativo")](databinding-images/debug01-large.png#lightbox)

Normalmente hay cuatro causas principales de nativo bloqueos durante el enlace de datos:

1. El modelo de datos no hereda de `NSObject` o una subclase de `NSObject`.
2. No exponen en Objective C utilizando la propiedad el `[Export("key-name")]` atributo.
3. No ajusta el cambio en los valores del descriptor de acceso de `WillChangeValue` y `DidChangeValue` llamadas al método (especificar la misma clave que el `Export` atributo).
4. Tiene una clave mal escrita o es incorrecta en el **enlace Inspector** en el generador de interfaz.

### <a name="decoding-a-crash"></a>Descodificar un bloqueo

Vamos a provoca un bloqueo nativo en el enlace de datos por lo que podemos mostramos cómo buscar y corregirlo. En el generador de interfaz, vamos a cambiar el enlace de la primera etiqueta en el ejemplo de vista de colección de `Name` a `Title`:

[![Editar la clave de enlace](databinding-images/debug02.png "editar la clave de enlace")](databinding-images/debug02-large.png#lightbox)

Vamos a guardar el cambio, vuelva a Visual Studio para Mac para la sincronización con Xcode y ejecutar la aplicación. Cuando se muestra la vista de colección, la aplicación se bloqueará en breve con un `SIGABRT` error (como se muestra en el **salida de la aplicación** en Visual Studio para Mac) ya que el `PersonModel` no expone una propiedad con la clave `Title`:

[![Ejemplo de un error de enlace](databinding-images/debug03.png "ejemplo de un error de enlace")](databinding-images/debug03-large.png#lightbox)

Si se desplaza a la parte superior del error en la **salida de la aplicación** podemos ver la clave para solucionar el problema:

[![Buscar el problema en el registro de errores](databinding-images/debug04.png "buscar el problema en el registro de errores")](databinding-images/debug04-large.png#lightbox)

Esta línea nos indica que la clave `Title` no existe en el objeto que se está enlazando a. Si se cambia el enlace hacia `Name` en el generador de interfaz, guardar, sincronización, vuelva a generar y ejecutar, la aplicación se ejecutará como se esperaba sin ningún problema.

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con enlace de datos y el valor de clave de escribir código en una aplicación Xamarin.Mac. En primer lugar, examinando expone una clase de C# para Objective-C mediante la clave y valor de codificación (KVC) y (KVO) de observación de clave y valor. A continuación, ha mostrado cómo usar una clase conforme a KVO y enlazar datos a los elementos de interfaz de usuario en el generador de interfaz de Xcode. Por último, se presentó el enlace de datos complejo mediante **controladores de la matriz** y **árbol controladores**.


## <a name="related-links"></a>Vínculos relacionados

- [Guión gráfico de MacDatabinding (ejemplo)](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding XIBs (ejemplo)](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controles estándar](~/mac/user-interface/standard-controls.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Vistas de colección](~/mac/user-interface/collection-view.md)
- [Guía de programación de codificación de clave y valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introducción a la Guía de programación de observación de clave y valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introducción a los enlaces de cacao temas de programación](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introducción a la referencia de enlaces de cacao](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
