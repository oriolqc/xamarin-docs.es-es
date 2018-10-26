---
title: Metadatos de los enlaces de Java
description: C#código de Xamarin.Android, llama a las bibliotecas de Java a través de enlaces, que son un mecanismo que abstraen los detalles de bajo nivel que se especifican en Java Native Interface (JNI). Xamarin.Android proporciona una herramienta que genera estos enlaces. Estas herramientas permite el control del desarrollador cómo se crea un enlace mediante el uso de metadatos, que permite que los procedimientos como la modificación de los espacios de nombres y cambiar el nombre de los miembros. Este documento se describe el funcionamiento de los metadatos, se resumen los atributos que los metadatos admite y se explica cómo resolver problemas de enlace mediante la modificación de estos metadatos.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 858f1e5c0bd2af85b419bb9a1cffb7d484f3f7e4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113409"
---
# <a name="java-bindings-metadata"></a>Metadatos de los enlaces de Java

_C#código de Xamarin.Android, llama a las bibliotecas de Java a través de enlaces, que son un mecanismo que abstraen los detalles de bajo nivel que se especifican en Java Native Interface (JNI). Xamarin.Android proporciona una herramienta que genera estos enlaces. Estas herramientas permite el control del desarrollador cómo se crea un enlace mediante el uso de metadatos, que permite que los procedimientos como la modificación de los espacios de nombres y cambiar el nombre de los miembros. Este documento se describe el funcionamiento de los metadatos, se resumen los atributos que los metadatos admite y se explica cómo resolver problemas de enlace mediante la modificación de estos metadatos._


## <a name="overview"></a>Información general

Xamarin.Android **biblioteca de enlaces de Java** intenta automatizar gran parte del trabajo necesario para el enlace de una biblioteca de Android existente con la Ayuda de una herramienta conocida como la _generador de enlaces_. Al enlazar una biblioteca de Java, Xamarin.Android inspeccionará las clases de Java y generar una lista de todos los paquetes, tipos y miembros que se va a enlazar. Esta lista de API se almacena en un archivo XML que puede encontrarse en  **\{proyecto directory}\obj\Release\api.xml** para un **versión** de compilación y en  **\{proyecto Directory}\obj\Debug\api.XML** para un **depurar** de compilación.

![Ubicación del archivo en la carpeta obj/Debug api.xml](java-bindings-metadata-images/java-bindings-metadata-01.png)

Va a usar el generador de enlaces de la **api.xml** archivo como guía para generar el necesario C# clases contenedoras. El contenido de este archivo XML es una variación de Google _Android Open Source Project_ formato.
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

En muchos casos, es necesaria para que la API de Java se sienta más ". NET, como" o para corregir los problemas que impiden que el ensamblado de enlace de compilar ayuda humana. Por ejemplo, puede ser necesario cambiar los nombres de paquete de Java a espacios de nombres. NET, cambiar el nombre de una clase o cambiar el tipo de valor devuelto de un método.

Estos cambios no se consiguen mediante la modificación de **api.xml** directamente.
En su lugar, los cambios se registran en los archivos XML especiales proporcionados por la plantilla de biblioteca de enlaces de Java. Cuando se compila el ensamblado de enlace de Xamarin.Android, el generador de enlaces se verán influido por estos archivos de asignación al crear el enlace de ensamblado

Estos archivos de asignación XML pueden encontrarse en el **transforma** carpeta del proyecto:

-   **MetaData.xml** &ndash; permite que se realicen cambios en la API de final, como cambiar el espacio de nombres del enlace generado. 

-   **EnumFields.xml** &ndash; contiene la asignación entre Java `int` constantes y C# `enums` . 

-   **EnumMethods.xml** &ndash; permite cambiar los parámetros de método y los tipos de valor devuelto desde Java `int` constantes a C# `enums` . 

El **MetaData.xml** archivo es más una importación de estos archivos, ya que permite cambios de propósito generales para el enlace, como:

-   Cambiar el nombre de espacios de nombres, clases, métodos o campos para que siguen convenciones. NET. 

-   Quitar espacios de nombres, clases, métodos o campos que no son necesarios. 

-   Pasar clases a diferentes espacios de nombres. 

-   Agregar clases de soporte técnico adicional para que el diseño del enlace siguen patrones de .NET framework. 

Permite pasar a tratar **Metadata.xml** con más detalle.


## <a name="metadataxml-transform-file"></a>Archivo de transformación de Metadata.Xml

Como ya hemos aprendido, el archivo **Metadata.xml** es utilizada por el generador de enlaces para influir en la creación del ensamblado de enlace.
Usa el formato de metadatos [XPath](https://www.w3.org/TR/xpath/) sintaxis y es casi idéntica a la *metadatos GAPI* se describe en [metadatos GAPI](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) guía. Esta implementación es casi una implementación completa de XPath 1.0 y, por tanto, es compatible con los elementos en el estándar 1.0. Este archivo es un mecanismo eficaz de XPath en función a cambiar, agregar, ocultar o mover cualquier elemento o atributo en el archivo de la API. Todos los elementos de regla en la especificación de los metadatos incluyen un atributo de ruta de acceso para identificar el nodo al que se aplicará la regla. Las reglas se aplican en el orden siguiente:

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

A continuación enumeran algunos de los elementos más usados de XPath para la API de Java:

-   `interface` &ndash; Se utiliza para buscar una interfaz de Java. Por ejemplo, `/interface[@name='AuthListener']`.

-   `class` &ndash; Se utiliza para buscar una clase. Por ejemplo, `/class[@name='MapView']`.

-   `method` &ndash; Se utiliza para localizar un método en una interfaz o clase de Java. Por ejemplo, `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Identificar un parámetro para un método. P. ej. `/parameter[@name='p0']`



### <a name="adding-types"></a>Adición de tipos

El `add-node` elemento indicará el proyecto Xamarin.Android de enlace para agregar una nueva clase de contenedor para **api.xml**. Por ejemplo, el siguiente fragmento de código dirigirá el generador de enlace para crear una clase con un constructor y un solo campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Eliminación de tipos

Es posible indicar el generador de enlaces de Xamarin.Android para omitir un tipo de Java y no enlazarlo. Esto se hace agregando un `remove-node` elemento XML a la **metadata.xml** archivo:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Cambiar el nombre de los miembros

Cambiar el nombre de los miembros no se puede realizar modificando directamente el **api.xml** archivo porque Xamarin.Android requiere que el nombre original de Java Native Interface (JNI). Por lo tanto, el `//class/@name` no se puede modificar el atributo; si es así, el enlace no funcionará.

Considere el caso donde queremos cambiar el nombre de un tipo, `android.Manifest`.
Para lograr esto, podemos probar que editar directamente **api.xml** y cambiar el nombre de la clase de este modo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Esto dará como resultado el generador de enlaces de crear los siguientes C# código para la clase de contenedor:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Tenga en cuenta que la clase contenedora se ha cambiado a `NewName`, mientras que el tipo de Java original sigue siendo `Manifest`. Ya no es posible que la clase de enlace de Xamarin.Android acceder a los métodos en `android.Manifest`; la clase contenedora se enlaza a un tipo de Java que no existe.

Para cambiar correctamente el nombre administrado de un tipo ajustado (o método), es necesario establecer el `managedName` atributo tal como se muestra en este ejemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Cambiar el nombre de `EventArg` clases contenedoras

Cuando el generador de enlace de Xamarin.Android identifica un `onXXX` método establecedor para un _tipo de agente de escucha_, un C# eventos y `EventArgs` subclase se generarán admitir .NET sabor API basado en Java patrón de agente de escucha. Por ejemplo, considere la siguiente clase de Java y el método:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android se quitará el prefijo `on` desde el método de establecedor y en su lugar use `2DSignNextManuever` como base para el nombre de la `EventArgs` subclase. La subclase se denominará algo parecido a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Esto no es un legal C# nombre de clase. Para corregir este problema, el autor de enlace debe utilizar el `argsType` de atributo y proporcionar válido C# nombre para el `EventArgs` subclase:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Atributos compatibles

Las secciones siguientes describen algunos de los atributos para transformar las API de Java.

### <a name="argstype"></a>argsType

Este atributo se coloca en los métodos de establecedor para el nombre del `EventArg` subclase que se generará para admitir los agentes de escucha de Java. Esto se describe con más detalle a continuación, en la sección [cambiar el nombre de clases contenedoras de EventArg](#Renaming_EventArg_Wrapper_Classes) más adelante en esta guía.

### <a name="eventname"></a>eventName

Especifica un nombre para un evento. Si está vacío, desactiva la generación de eventos.
Esto se describe con más detalle en el título de sección [cambiar el nombre de clases contenedoras de EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Esto se usa para cambiar el nombre de un paquete, clase, método o parámetro. Por ejemplo, para cambiar el nombre de la clase Java `MyClass` a `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

En el ejemplo siguiente se muestra una expresión XPath para cambiar el nombre del método `java.lang.object.toString` a `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>Tipo administrado

`managedType` se utiliza para cambiar el tipo de valor devuelto de un método. En algunas situaciones el generador de enlaces incorrectamente deducirá el tipo de valor devuelto de un método de Java, lo que dará lugar a un error en tiempo de compilación. Una posible solución en esta situación consiste en cambiar el tipo de valor devuelto del método.

Por ejemplo, el generador de enlaces que considera que el método Java `de.neom.neoreadersdk.resolution.compareTo()` debe devolver un `int`, que da como resultado el mensaje de error **Error CS0535: ' DE. Neom.Neoreadersdk.Resolution' no implementa el miembro de interfaz 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**. El fragmento de código siguiente muestra cómo cambiar el tipo de valor devuelto de generado C# método desde un `int` a un `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Cambia el tipo de valor devuelto de un método. Esto no cambia el atributo de valor devuelto (como los cambios para devolver los atributos pueden causar cambios incompatibles con la firma JNI). En el ejemplo siguiente, el tipo de devolución de la `append` se cambia el método de `SpannableStringBuilder` a `IAppendable` (Recuerde que C# no admite tipos de valor devuelto covariante):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>ofuscado

Las herramientas que ofuscar las bibliotecas de Java pueden interferir con el generador de enlace de Xamarin.Android y su capacidad para generar C# clases contenedoras. Características de ofuscación clases incluyen: * incluye el nombre de clase un **$**, es decir, **un .class $** * el nombre de clase completamente consta de caracteres en minúsculas, es decir,  **a.Class**

Este fragmento de código es un ejemplo de cómo generar un "sin ofuscado" C# tipo:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Este atributo se puede usar para cambiar el nombre de una propiedad administrada.

Un caso de uso especializado `propertyName` implica la situación donde una clase de Java tiene solo un método Get para un campo. En esta situación, el generador de enlace querría crear una propiedad de solo escritura, algo que no es recomendable en. NET. El siguiente fragmento muestra cómo las propiedades. NET "quitar" estableciendo la `propertyName` en una cadena vacía:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Tenga en cuenta que los métodos establecedor y captador se seguirá creando el generador de enlaces.

### <a name="sender"></a>sender

Especifica que el parámetro de un método debe ser el `sender` parámetro cuando el método se asigna a un evento. El valor puede ser `true` o `false`. Por ejemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidad

Este atributo se utiliza para cambiar la visibilidad de una clase, método o propiedad. Por ejemplo, puede ser necesaria para promover un `protected` método de Java, por lo que TI correspondiente del C# contenedor es `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml y EnumMethods.xml

Hay casos donde las bibliotecas de Android utilizan constantes enteras para representar los Estados que se pasan a las propiedades o métodos de las bibliotecas. En muchos casos, es útil enlazar estas constantes enteras con enumeraciones en C#. Para facilitar esta asignación, utilice el **EnumFields.xml** y **EnumMethods.xml** archivos del proyecto de enlace. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definir una enumeración con EnumFields.xml

El **EnumFields.xml** archivo contiene la asignación entre Java `int` constantes y C# `enums`. Veamos el siguiente ejemplo de un C# enum que se va a crear para un conjunto de `int` constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aquí hemos tomado la clase Java `SKRealReachSettings` y define un C# enum llamado `SKMeasurementUnit` en el espacio de nombres `Skobbler.Ngx.Map.RealReach`. El `field` entradas define el nombre de la constante de Java (ejemplo `UNIT_SECOND`), el nombre de la entrada de enumeración (ejemplo `Second`) y el valor entero representado por ambas entidades (ejemplo `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definir métodos de captador o establecedor usando EnumMethods.xml

El **EnumMethods.xml** archivo permite cambiar los parámetros de método y los tipos de valor devuelto desde Java `int` constantes a C# `enums`. En otras palabras, se asigna la lectura y escritura de C# enumeraciones (definido en el **EnumFields.xml** archivo) para Java `int` constante `get` y `set` métodos.

Dada la `SKRealReachSettings` enum definidos anteriormente, la siguiente **EnumMethods.xml** archivo definiría el captador o establecedor de esta enumeración:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La primera `method` línea asigna el valor devuelto de Java `getMeasurementUnit` método a la `SKMeasurementUnit` enum. El segundo `method` línea asigna el primer parámetro de la `setMeasurementUnit` a la misma enumeración.

Con todos estos cambios en su lugar, puede usar el código siguiente en Xamarin.Android para establecer el `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Resumen

En este artículo se describe cómo Xamarin.Android usa metadatos para transformar una definición de API desde el *Google* *formato AOSP*. Después de cubrir los cambios que son posibles mediante *Metadata.xml*, examinan las limitaciones que se encuentran al cambiar el nombre de los miembros y lo presenta la lista de atributos admitidos de XML, que describe cuándo se debe utilizar cada atributo.



## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadatos GAPI](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
