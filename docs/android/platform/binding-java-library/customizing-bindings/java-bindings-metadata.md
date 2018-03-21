---
title: Java Bindings Metadata
description: "Código de C# en Xamarin.Android llama a bibliotecas de Java a través de enlaces, que son un mecanismo que resume los detalles de bajo nivel que se especifican en Java JNI (interfaz nativa). Xamarin.Android proporciona una herramienta que genera estos enlaces. Este conjunto de herramientas permite el control del desarrollador cómo se crea un enlace con los metadatos, que permite que los procedimientos, como modificar los espacios de nombres y cambiar el nombre de los miembros. Este documento se describe el funcionamiento de metadatos, se resumen los atributos que los metadatos es compatible con y se explica cómo resolver problemas de enlace mediante la modificación de estos metadatos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 70f17b6bc8dc991534cdf4dd065c813aa0e27e96
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2018
---
# <a name="java-bindings-metadata"></a>Java Bindings Metadata

_Código de C# en Xamarin.Android llama a bibliotecas de Java a través de enlaces, que son un mecanismo que resume los detalles de bajo nivel que se especifican en Java JNI (interfaz nativa). Xamarin.Android proporciona una herramienta que genera estos enlaces. Este conjunto de herramientas permite el control del desarrollador cómo se crea un enlace con los metadatos, que permite que los procedimientos, como modificar los espacios de nombres y cambiar el nombre de los miembros. Este documento se describe el funcionamiento de metadatos, se resumen los atributos que los metadatos es compatible con y se explica cómo resolver problemas de enlace mediante la modificación de estos metadatos._


## <a name="overview"></a>Información general

Un Xamarin.Android **Java enlace biblioteca** intenta automatizar gran parte del trabajo necesario para enlazar una biblioteca de Android existente con la Ayuda de una herramienta conocida como la _generador de enlaces_. Al enlazar una biblioteca de Java, Xamarin.Android inspeccionará las clases de Java y generar una lista de todos los paquetes, tipos y miembros que se va a enlazar. Esta lista de API se almacena en un archivo XML que se pueden encontrar en  **\{proyecto directory}\obj\Release\api.xml** para un **versión** de compilación y en  **\{proyecto Directory}\obj\Debug\api.XML** para un **depurar** de compilación.

![Ubicación del archivo api.xml en la carpeta obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

El generador de enlaces utilizará el **api.xml** archivo como guía para generar las clases de contenedor de C# necesarias. El contenido de este archivo XML es una variación de Google _Android Open Source Project_ formato.
El fragmento de código siguiente es un ejemplo del contenido de **api.xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

En este ejemplo, **api.xml** declara una clase en el `android` paquete denominado `Manifest` que abarca el `java.lang.Object`.

En muchos casos, asistencia humano es necesaria para que la API de Java se sienta más ".NET como" o para corregir los problemas que impiden que se compile el ensamblado de enlace. Por ejemplo, puede que sea necesario cambiar los nombres de paquete de Java a espacios de nombres. NET, cambiar el nombre de una clase o cambiar el tipo de valor devuelto de un método.

Estos cambios no se consiguen mediante la modificación de **api.xml** directamente.
En su lugar, los cambios se registran en los archivos XML especiales que se proporcionan con la plantilla de biblioteca de enlace de Java. Cuando se compila el ensamblado de enlace Xamarin.Android, el generador de enlaces se verán influido por estos archivos de asignación al crear el ensamblado de enlace

Estos archivos de asignación XML pueden encontrarse en el **transforma** carpeta del proyecto:

-   **MetaData.xml** &ndash; permite que los cambios que se realizan en la API final, como cambiar el espacio de nombres del enlace generado. 

-   **EnumFields.xml** &ndash; contiene la asignación entre Java `int` constantes y C# `enums` . 

-   **EnumMethods.xml** &ndash; permite cambiar los parámetros de método y tipos devueltos desde Java `int` constantes en C# `enums` . 

El **MetaData.xml** archivo es más importante de estos archivos ya que permite cambios de propósito generales para el enlace, como:

-   Cambiar el nombre de espacios de nombres, clases, métodos o campos para que siguen convenciones. NET. 

-   Quitar los espacios de nombres, clases, métodos o campos que no son necesarios. 

-   Pasar clases a diferentes espacios de nombres. 

-   Agregar las clases de soporte técnico adicional para realizar el diseño del enlace siguen patrones de .NET framework. 

Permite mover explicar **Metadata.xml** con más detalle.


## <a name="metadataxml-transform-file"></a>Archivo de transformación de Metadata.Xml

Como ya hemos aprendido, el archivo **Metadata.xml** es utilizada por el generador de enlaces para influir en la creación del ensamblado de enlace.
El formato de metadatos utiliza [XPath](https://www.w3.org/TR/xpath/) sintaxis y es casi idéntica a la *metadatos GAPI* se describe en [metadatos GAPI](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) guía. Esta implementación es prácticamente una implementación completa de XPath 1.0 y, por tanto, es compatible con elementos en el estándar 1.0. Este archivo es un XPath según un mecanismo eficaz para cambiar, agregar, ocultar o mover cualquier elemento o atributo en el archivo de API. Todos los elementos de regla en las especificaciones de metadatos incluyen un atributo de ruta de acceso para identificar el nodo al que se aplicará la regla. Las reglas se aplican en el orden siguiente:

* **Agregar nodo** &ndash; anexa un nodo secundario al nodo especificado por el atributo de ruta de acceso.
* **attr** &ndash; establece el valor de un atributo del elemento especificado por el atributo de ruta de acceso.
* **Eliminar nodo** &ndash; quita nodos que coinciden con un expresión XPath especificada.

El siguiente es un ejemplo de un **Metadata.xml** archivo:

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

A continuación enumeran algunos de los elementos de XPath más comúnmente usados para la API de Java:

-   `interface` &ndash; Se utiliza para buscar una interfaz de Java. Por ejemplo, `/interface[@name='AuthListener']`.

-   `class` &ndash; Se utiliza para buscar una clase. Por ejemplo, `/class[@name='MapView']`.

-   `method` &ndash; Se utiliza para buscar un método en una interfaz o clase de Java. Por ejemplo, `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Identificar un parámetro para un método. P. ej. `/parameter[@name='p0']`



### <a name="adding-types"></a>Adición de tipos

El `add-node` elemento indicará el proyecto de enlace de Xamarin.Android para agregar una nueva clase de contenedor para **api.xml**. Por ejemplo, el siguiente fragmento dirigirá el generador de enlace para crear una clase con un constructor y un único campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Eliminación de tipos

Es posible indicar el generador de enlaces Xamarin.Android para omitir un tipo de Java y no enlazarla. Esto se realiza agregando un `remove-node` elemento XML en el **metadata.xml** archivo:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Cambiar el nombre de los miembros

Cambiar el nombre de los miembros no se puede realizar mediante la edición de la **api.xml** archivo porque Xamarin.Android requiere que los nombres originales de Java JNI (interfaz nativa). Por lo tanto, la `//class/@name` no se puede modificar el atributo; si es así, el enlace no funcionará.

Considere el caso donde desea cambiar el nombre de un tipo `android.Manifest`.
Para ello, podemos probar que editar directamente **api.xml** y cambiar el nombre de la clase de este modo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Esto provocará que el generador de enlaces de crear el siguiente código de C# para la clase de contenedor:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Tenga en cuenta que la clase de contenedor ha cambiado a `NewName`, mientras que el tipo de Java original sigue siendo `Manifest`. Ya no es posible que la clase de enlace Xamarin.Android tener acceso a los métodos en `android.Manifest`; la clase de contenedor está enlazada a un tipo de Java inexistente.

Para cambiar correctamente el nombre administrado de un tipo (o un método) ajustada, es necesario establecer la `managedName` atributo tal como se muestra en este ejemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Cambiar el nombre de `EventArg` clases contenedoras

Cuando el generador de enlace de Xamarin.Android identifica un `onXXX` método establecedor para una _tipo de agente de escucha_, un evento de C# y `EventArgs` subclase se generarán admitir .NET sabor API para el agente de escucha basados en Java patrón. Por ejemplo, considere la siguiente clase de Java y el método:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android se quitará el prefijo `on` desde el método de establecedor y en su lugar use `2DSignNextManuever` como base para el nombre de la `EventArgs` subclase. La subclase tendrá un nombre parecido al:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

No es un nombre de clase de C# válido. Para corregir este problema, el autor del enlace debe utilizar el `argsType` de atributo y especifique un nombre C# válido para el `EventArgs` subclase:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Atributos admitidos

Las secciones siguientes describen algunos de los atributos para transformar las API de Java.

### <a name="argstype"></a>argsType

Este atributo se coloca en los métodos de establecedor por nombrar la `EventArg` subclase que se generará para admitir los agentes de escucha de Java. Esto se describe con más detalle a continuación en la sección [cambiar el nombre de estas clases contenedoras EventArg](#Renaming_EventArg_Wrapper_Classes) más adelante en esta guía.

### <a name="eventname"></a>eventName

Especifica un nombre de un evento. Si está vacío, desactiva la generación de eventos.
Esto se describe con más detalle en el título de sección [cambiar el nombre de estas clases contenedoras EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Esto se utiliza para cambiar el nombre de un paquete, la clase, el método o el parámetro. Por ejemplo, para cambiar el nombre de la clase Java `MyClass` a `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

En el ejemplo siguiente se muestra una expresión XPath para cambiar el nombre del método `java.lang.object.toString` a `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` se utiliza para cambiar el tipo de valor devuelto de un método. En algunos casos el generador de enlaces incorrectamente deducirá el tipo de valor devuelto de un método de Java, lo que dará lugar a un error en tiempo de compilación. Una posible solución en esta situación es cambiar el tipo de valor devuelto del método.

Por ejemplo, el generador de enlaces considera que el método de Java `de.neom.neoreadersdk.resolution.compareTo()` debe devolver un `int`, que da como resultado el mensaje de error **Error CS0535: ' Alemania. Neom.Neoreadersdk.Resolution' no implementa el miembro de interfaz 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**. El fragmento de código siguiente muestra cómo cambiar el tipo de valor devuelto del método C# generado desde un `int` a una `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Cambia el tipo de valor devuelto de un método. Esto no cambia el atributo de valor devuelto (como los cambios para devolver atributos pueden causar cambios incompatibles en la firma JNI). En el ejemplo siguiente, el tipo devuelto de la `append` método se cambia de `SpannableStringBuilder` a `IAppendable` (Recuerde que C# no admite los tipos de valor devueltos covariantes):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>protegido

Herramientas que ofuscar las bibliotecas de Java pueden interferir con el generador de enlace de Xamarin.Android y su capacidad para generar clases contenedoras de C#. Características de clases ofuscadas incluyen: * incluye el nombre de clase un  **$** , es decir, **un .class $** * el nombre de clase está completamente en peligro de caracteres en minúsculas, es decir,  **a.Class**

Este fragmento de código es un ejemplo de cómo generar un tipo de C# "sin ofuscado":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>
          propertyName

Este atributo se puede usar para cambiar el nombre de una propiedad administrada.

Un caso de uso especializado `propertyName` implica la situación donde una clase de Java tiene solo un método de captador para un campo. En esta situación, el generador de enlace sería aconsejable crear una propiedad de solo escritura, algo que no se recomienda en. NET. El siguiente fragmento muestra cómo las propiedades. NET "quitar" estableciendo la `propertyName` en una cadena vacía:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Tenga en cuenta que los métodos establecedor y captador se creará igualmente por el generador de enlaces.

### <a name="sender"></a>
          sender

Especifica que el parámetro de un método debe ser el `sender` parámetro cuando el método se asigna a un evento. El valor puede ser `true` o `false`. Por ejemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidad

Este atributo se utiliza para cambiar la visibilidad de una clase, un método o una propiedad. Por ejemplo, es posible que sea necesario promover un `protected` método Java para que corresponde el contenedor de C# es `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml y EnumMethods.xml

Hay casos donde las bibliotecas Android utilizan constantes enteras para representar los Estados que se pasan a las propiedades o métodos de las bibliotecas. En muchos casos, es útil enlazar estas constantes de tipo entero a las enumeraciones en C#. Para facilitar esta asignación, utilice la **EnumFields.xml** y **EnumMethods.xml** archivos del proyecto de enlace. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definir una enumeración con EnumFields.xml

El **EnumFields.xml** archivo contiene la asignación entre Java `int` constantes y C# `enums`. Veamos el siguiente ejemplo de una enumeración de C# que se va a crear para un conjunto de `int` constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aquí le hemos llevado la clase Java `SKRealReachSettings` y define una enumeración de C# llamada `SKMeasurementUnit` en el espacio de nombres `Skobbler.Ngx.Map.RealReach`. El `field` entradas define el nombre de la constante de Java (ejemplo `UNIT_SECOND`), el nombre de la entrada de enumeración (ejemplo `Second`) y el valor de entero representado por ambas entidades (ejemplo `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definir métodos de captador o establecedor mediante EnumMethods.xml

El **EnumMethods.xml** archivo permite cambiar los parámetros de método y tipos devueltos desde Java `int` constantes en C# `enums`. En otras palabras, se asigna la lectura y escritura de las enumeraciones de C# (definidos en el **EnumFields.xml** archivo) para Java `int` constante `get` y `set` métodos.

Dada la `SKRealReachSettings` enum definidos anteriormente, la siguiente **EnumMethods.xml** archivo tendría que definir el captador o establecedor de esta enumeración:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La primera `method` línea asigna el valor devuelto de la Java `getMeasurementUnit` método para el `SKMeasurementUnit` enum. El segundo `method` línea asigna el primer parámetro de la `setMeasurementUnit` a la misma enumeración.

Con todos estos cambios en su lugar, puede utilizar el código de seguimiento en Xamarin.Android para establecer el `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Resumen

En este artículo se describe cómo Xamarin.Android usa metadatos para transformar una definición de la API de la *Google* *formato AOSP*. Después de cubrir los cambios que son posibles con *Metadata.xml*, examinan las limitaciones que se encuentran al cambiar el nombre de los miembros y presenta la lista de atributos admitidos de XML, que describe cuándo se debe utilizar cada atributo.



## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [GAPI Metadata](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
