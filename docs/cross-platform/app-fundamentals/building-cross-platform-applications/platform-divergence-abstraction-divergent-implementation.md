---
title: 'Parte 4: trabajar con varias plataformas'
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8de540e736bee32c9ee98476bcdc3c5b1100f0f4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4: trabajar con varias plataformas

## <a name="handling-platform-divergence-amp-features"></a>Control de plataforma divergencia &amp; características

Divergencia no es un problema de 'multiplataforma'; dispositivos en la plataforma 'mismo' tienen capacidades distintas (especialmente la amplia variedad de dispositivos Android que están disponibles). La más obvia y básico son el tamaño de pantalla, pero otros atributos del dispositivo pueden variar y requieren una aplicación para comprobar determinados capacidades y se comportan de manera diferente basándose en su presencia (o ausencia).

Esto significa que todas las aplicaciones deben ocuparse de degradación correcta de la funcionalidad, o bien presentar un conjunto de características poco atractivo, más bajo de denominador común. Integración profunda de Xamarin con los SDK nativo de cada plataforma permiten a las aplicaciones aprovechar la funcionalidad específica de la plataforma, por lo que tiene sentido diseñar aplicaciones para utilizar estas características.

Consulte la documentación de capacidades de la plataforma para obtener información general de la diferencia entre las plataformas en la funcionalidad.

 <a name="Examples_of_Platform_Divergence" />


### <a name="examples-of-platform-divergence"></a>Ejemplos de divergencia de plataforma

 <a name="Fundamental_elements_that_exist_across_platforms" />


#### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementos fundamentales que existen entre plataformas

Hay algunas características de las aplicaciones móviles que son universales.
Estos son conceptos de nivel superiores que son generalmente ciertas para todos los dispositivos y, por tanto, pueden formar la base de diseño de la aplicación:

-  Selección de características a través de las pestañas o menús
-  Listas de datos y el desplazamiento
-  Solo vistas de datos
-  Edición solo vistas de datos
-  Navegar hacia atrás


Al diseñar el flujo de alto nivel de pantalla puede basar una experiencia de usuario común en estos conceptos.

 <a name="platform-specific_attributes" />


#### <a name="platform-specific-attributes"></a>atributos específicos de la plataforma

Además de los elementos básicos que existen en todas las plataformas, debe corregir las diferencias de plataforma clave en el diseño. Debe tener en cuenta estas diferencias (y escribir código específicamente para controlar):

-   **Tamaños de pantalla** – algunas plataformas (por ejemplo, iOS y las versiones anteriores de Windows Phone) han estandarizado tamaños de pantalla que son relativamente simples como destino. Dispositivos Android tienen una gran variedad de dimensiones de la pantalla, que requieren más esfuerzo para admitir en su aplicación.
-   **Metáforas relacionadas con la navegación** – difieren entre plataformas (p. ej. botón de hardware "Atrás", control de interfaz de usuario de panorámica) y en plataformas (Android 2 y 4, iPhone vs iPad).
-   **Teclados** – dispositivos Android algunas tienen teclados físicos, mientras que otros sólo tienen un teclado de software. Código que detecta cuándo un teclado de software es ocultar parte de la pantalla debe tener en cuenta estas diferencias.
-   **Entrada táctil y movimientos** – varían en sistemas operativos admitidos para el reconocimiento de gestos, especialmente en las versiones anteriores de cada sistema operativo. Las versiones anteriores de Android muy tienen compatibilidad limitada para las operaciones de entrada táctil, lo que significa que solo se admiten dispositivos más antiguos puede requerir código independiente
-   **Notificaciones de inserción** : hay capacidades/implementaciones diferentes en cada plataforma (p. ej. Iconos dinámicos en Windows).


 <a name="Device-specific_features" />


#### <a name="device-specific-features"></a>Características específicas del dispositivo

Determinar lo que deben ser las características mínimas necesarias para la aplicación; o bien, cuando decida qué características adicionales para aprovechar las ventajas de en cada plataforma. Le pedirá el código para detectar características y funcionalidad o deshabilitar ofrecen alternativas (p. ej. para permitir que el usuario escriba una ubicación o elija una asignación podría ser una alternativa a la ubicación geográfica):

-   **Cámara** – funcionalidad difiere en todos los dispositivos: algunos dispositivos no tienen una cámara, otros tienen ambas cámaras frontal y trasero. Algunas cámaras son capaces de grabación de vídeo.
-   **Ubicación geográfica y asignaciones** : compatibilidad para la ubicación del GPS o Wi-Fi no está presente en todos los dispositivos. Las aplicaciones también deben cumplir los requisitos para los distintos niveles de precisión que es compatible con cada método.
-   **Acelerómetro, giroscopio y brújula** : estas características se encuentran a menudo en solo un conjunto de dispositivos en cada plataforma, por lo que casi siempre necesitan aplicaciones proporcionar una reserva cuando no se admite el hardware.
-   **Twitter y Facebook** : solo 'integrada' en iOS5 y iOS6 respectivamente. En versiones anteriores y otras plataformas, deberá proporcionar sus propias funciones de autenticación y comunicarse directamente con cada una API de servicios.
-   **Un nivel próximo al campo de las comunicaciones (NFC)** : solo en (algunos) dispositivos Android (en el momento de escribir este artículo).


 <a name="Dealing_with_Platform_Divergence" />


### <a name="dealing-with-platform-divergence"></a>Trabajar con divergencia de plataforma

Existen dos enfoques diferentes para admitir varias plataformas desde el mismo código base, cada uno con su propio conjunto de ventajas y desventajas.

-   **Abstracción de plataforma** : patrón de fachada de negocios, proporciona un acceso unificado en las distintas plataformas y abstrae las implementaciones de plataforma en particular en una API unificada.
-   **Implementación divergente** : características de invocación de plataforma concreta a través de implementaciones divergentes mediante herramientas de arquitectura como interfaces y la herencia o la compilación condicional.


 <a name="Platform_Abstraction" />


## <a name="platform-abstraction"></a>Abstracción de plataforma

 <a name="Class_Abstraction" />


### <a name="class-abstraction"></a>Abstracción de clase

Mediante interfaces o clases base definidas en el código compartido e implementa o amplía en proyectos específicos de plataforma. Escribir y ampliar el código compartido con abstracciones de clase especialmente es adecuada para las bibliotecas de clases portables porque tienen un subconjunto limitado del marco de trabajo disponible para ellos y no pueden contener directivas de compilador para admitir las bifurcaciones de código específico de la plataforma.

 <a name="Interfaces" />


#### <a name="interfaces"></a>Interfaces

Usar interfaces, podrá implementar las clases específicas de plataforma que todavía se pueden pasar a las bibliotecas compartidas para aprovechar el código común.

La interfaz se define en el código compartido y pasó a la biblioteca compartida como un parámetro o una propiedad.

Las aplicaciones específicas de la plataforma pueden, a continuación, implemente la interfaz y seguir aprovechando código compartido ' procesarla '.

 **Ventajas**

La implementación puede contener código específico de la plataforma y hacer referencia a bibliotecas externas específica de la plataforma.

 **Disadvantages**

Tener que crear y pasar las implementaciones en el código compartido. Si se usa la interfaz de profundidad en el código compartido, a continuación, acabarán que se va a pasar a través de varios parámetros de método o en caso contrario, se trasladará a través de la cadena de llamadas. Si el código compartido usa una gran cantidad de interfaces diferentes, a continuación, debe todos se crea y establecer en el código compartido en alguna parte.

 <a name="Inheritance" />


#### <a name="inheritance"></a>Herencia

El código compartido podría implementar clases abstractas o virtuales que se pudieron ampliar en uno o más proyectos para plataformas específicas. Esto es similar al uso de interfaces pero con un comportamiento determinado ya implementado. No hay puntos de vista diferentes en interfaces o herencia sean una mejor opción de diseño: en concreto ya que C# sólo permite herencia simple puede dictar la manera en que se pueden diseñar las API en el futuro. Usar la herencia con precaución.

Las ventajas y desventajas de las interfaces se aplican por igual a la herencia, con la ventaja adicional de que la clase base puede contener algún código de implementación (quizás una completa plataforma independiente implementación que se puede extender si lo desea).

<a name="Xamarin.Forms" />

### <a name="xamarinforms"></a>Xamarin.Forms

Consulte la [Xamarin.Forms](~/xamarin-forms/get-started/index.md) documentación.


### <a name="plug-in-cross-platform-functionality"></a>Funcionalidad de Plug-in multiplataforma

También puede ampliar aplicaciones multiplataforma de una manera coherente mediante complementos.

Vinculado desde nuestra [github complementos](https://github.com/xamarin/plugins), la mayoría de complementos son código abierto proyectos (normalmente disponibles para la instalación a través de Nuget) que le ayudarán a implementan una variedad de funciones específicas de la plataforma de estado de la batería a la configuración con un API común de que sea fácil de usar en aplicaciones de la plataforma de Xamarin y Xamarin.Forms.


<a name="Other_Cross-Platform_Libraries" />

### <a name="other-cross-platform-libraries"></a>Otras bibliotecas multiplataforma

Hay una serie de bibliotecas de entidad 3rd disponibles que proporcionan la funcionalidad de multiplataforma:

-   **MvvmCross** -  [https://github.com/slodge/MvvmCross/](https://github.com/slodge/MvvmCross/)
-   **Vernáculo** (para la localización):  [https://github.com/rdio/vernacular/](https://github.com/rdio/vernacular/)
-   **MonoGame** (para juegos XNA):  [http://monogame.codeplex.com/](http://monogame.codeplex.com/)
-   **NGraphics** - [NGraphics](https://github.com/praeclarum/NGraphics) y su precursor [https://github.com/praeclarum/CrossGraphics](https://github.com/praeclarum/CrossGraphics)


 <a name="Divergent_Implementation" />


### <a name="divergent-implementation"></a>Implementación divergente

 <a name="Conditional_Compilation" />


#### <a name="conditional-compilation"></a>Compilación condicional

Existen algunas situaciones donde su código compartido todavía necesita funcionan de manera diferente en cada plataforma, posiblemente tiene acceso a las clases o funciones que se comporten de manera diferente. Compilación condicional funciona mejor con proyectos activos compartidos, donde se hace referencia el mismo archivo de origen en varios proyectos que tienen diferentes símbolos definidos.

Proyectos de Xamarin definen siempre `__MOBILE__` que es true para iOS y proyectos de aplicación de Android (tenga en cuenta el subrayado doble previo y posteriores a la corrección en estos símbolos).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

<a name="iOS" />

##### <a name="ios"></a>iOS

Define Xamarin.iOS `__IOS__` que puede usar para detectar dispositivos de iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

También hay símbolos específicos de inspección y TV:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

<a name="Android" />

##### <a name="android"></a>Android

Código que solo se debe compilar en aplicaciones de Xamarin.Android puede utilizar lo siguiente

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Cada versión de API también define una nueva directiva de compilador, por lo que el código parecido a esto le permitirá agregar características si tienen como destino las API más recientes. Cada nivel de API incluye todos los símbolos de nivel 'inferiores'. Esta característica no es realmente útil para admitir varias plataformas; Normalmente el `__ANDROID__` símbolo será suficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

##### <a name="mac"></a>Mac

No hay actualmente un símbolo integrado para Xamarin.Mac, pero puede agregar un proyecto de aplicación de su propio en el Mac **Opciones > compilar > compilador** en el **definir símbolos** cuadro o editar el **.csproj**  de archivos y agregar no existe (por ejemplo `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

<a name="Windows_Phone" />

##### <a name="windows-phone"></a>Windows Phone

Aplicaciones de Windows Phone define dos símbolos: `WINDOWS_PHONE` y `SILVERLIGHT` : que se puede utilizar como código a la plataforma de destino. Estos no tienen los caracteres de subrayado encerrarlas como los símbolos de la plataforma Xamarin hacer.


<a name="Using_Conditional_Compilation" />

##### <a name="using-conditional-compilation"></a>Compilación condicional

Un ejemplo sencillo de caso práctico de compilación condicional es establecer la ubicación del archivo para el archivo de base de datos de SQLite. Las tres plataformas tienen requisitos ligeramente diferentes para especificar la ubicación del archivo:

-   **iOS** : Apple preferir datos no es de usuario que se colocarán en una ubicación específica (el directorio de la biblioteca), pero no hay ninguna constante de sistema para este directorio. Código específico de la plataforma es necesario para generar la ruta de acceso correcta.
-   **Android** : la ruta de acceso de sistema devuelto por `Environment.SpecialFolder.Personal` es una ubicación para almacenar el archivo de base de datos aceptable.
-   **Windows Phone** : el mecanismo de almacenamiento aislado no permite una ruta de acceso completa que se especifique una ruta de acceso relativa y el nombre.
-   **Plataforma universal de Windows** – utiliza `Windows.Storage` API.

El código siguiente utiliza la compilación condicional para asegurarse de la `DatabaseFilePath` es correcto para cada plataforma:

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

El resultado es una clase que puede crearse y usarse en todas las plataformas, colocar el archivo de base de datos de SQLite en una ubicación diferente en cada plataforma.

