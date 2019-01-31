---
title: 'Parte 4: Trabajo con varias plataformas'
description: Este documento describe cómo controlar la divergencia de aplicación en función de la plataforma o la funcionalidad. Describe el tamaño de pantalla, metáforas relacionadas con la navegación, táctil y gestos, las notificaciones de inserción y paradigmas de interfaz, como listas y pestañas.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f4558d5172d7dcbd2e14e6b74a347dd2f1e3a234
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292134"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4: Trabajo con varias plataformas

## <a name="handling-platform-divergence-amp-features"></a>Controlar la divergencia de plataforma &amp; características

Divergencia no es un problema de 'cross-platform'; los dispositivos en la plataforma 'mismo' tienen capacidades diferentes (especialmente la amplia variedad de dispositivos Android que están disponibles). Más obvios y básicos son el tamaño de la pantalla, pero otros atributos del dispositivo pueden variar y requieren una aplicación para comprobar determinados capacidades y se comportan de manera diferente en función de su presencia (o ausencia).

Esto significa que todas las aplicaciones deben tratar con degradación correcta de funcionalidad, o bien presentar un conjunto de características poco atractivo, el mínimo común denominador. Profunda integración Xamarin con los SDK nativos de cada plataforma permiten a las aplicaciones aprovechar la funcionalidad específica de la plataforma, así que resulta más adecuado diseñar aplicaciones para usar esas características.

Consulte la documentación de funciones de la plataforma para obtener información general de qué se diferencian las plataformas en la funcionalidad.

## <a name="examples-of-platform-divergence"></a>Ejemplos de divergencia de plataforma

### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementos fundamentales que existen entre plataformas

Hay algunas características de aplicaciones móviles que son universales.
Estos son los conceptos de alto nivel que se cumple por lo general de todos los dispositivos y, por tanto, pueden formar la base de diseño de la aplicación:

-  Selección de características a través de los menús o pestañas
-  Listas de datos y el desplazamiento
-  Solo vistas de datos
-  Editar solo vistas de datos
-  Desplazarse hacia atrás

Al diseñar el flujo de alto nivel de pantalla puede basar una experiencia de usuario común en estos conceptos.

### <a name="platform-specific-attributes"></a>Atributos específicos de la plataforma

Además de los elementos básicos que existen en todas las plataformas, deberá diferencias en el diseño de la plataforma de clave de dirección. Puede que necesite tener en cuenta estas diferencias (y escribir código específicamente para controlar):

-   **Tamaños de pantalla** – algunas plataformas (por ejemplo, iOS y las versiones anteriores de Windows Phone) han estandarizado los tamaños de pantalla son relativamente sencillas para tener como destino. Dispositivos Android tienen una gran variedad de dimensiones de la pantalla, que requiere más esfuerzo para admitir en la aplicación.
-   **Metáforas relacionadas con la navegación** – difieren entre plataformas (p ej. botón de hardware 'nuevo', control de IU de panorámica) y dentro de las plataformas (Android 2 y 4, iPhone vs iPad).
-   **Teclados** – dispositivos Android algunos tienen teclados físicos mientras que otros sólo tienen un teclado de software. Código que detecta cuándo un teclado temporal oculta en parte de la pantalla debe ser sensible a estas diferencias.
-   **Tacto y gestos** : compatibilidad del sistema operativo para el reconocimiento de gestos varía, especialmente en las versiones anteriores de cada sistema operativo. Las versiones anteriores de Android muy tienen compatibilidad limitada para las operaciones de toque, lo que significa que se admiten aquellos dispositivos antiguos puede requerir código independiente
-   **Notificaciones de inserción** : hay capacidades/implementaciones diferentes en cada plataforma (p ej. Iconos dinámicos de Windows).

### <a name="device-specific-features"></a>Características específicas del dispositivo

Determinar cuál deben ser las características mínimas necesarias para la aplicación; o bien, cuando decida qué características adicionales pueden aprovechar en cada plataforma. Se requerirá código para detectar las características y funcionalidad o deshabilitar ofrecen alternativas (p ej. para permitir que el usuario escriba una ubicación o elija de una asignación podría ser una alternativa a la ubicación geográfica):

-   **Cámara** : funcionalidad difiere entre dispositivos: algunos dispositivos no tienen una cámara, otros tienen dos cámaras frontal y trasero. Algunas cámaras son capaces de grabación de vídeo.
-   **Ubicación geográfica & mapas** : soporte técnico para la ubicación de GPS o Wi-Fi no está presente en todos los dispositivos. Las aplicaciones también necesitan cumplir los requisitos para los distintos niveles de precisión que sea compatible con cada método.
-   **Acelerómetro, giroscopio y brújula** – estas características se encuentran a menudo en sólo una selección de dispositivos en cada plataforma, por lo que las aplicaciones casi siempre deben proporcionar una acción de reserva cuando no se admite el hardware.
-   **Twitter y Facebook** : solo 'integrado' en iOS5 y iOS6 respectivamente. En versiones anteriores y otras plataformas deberá proporcionar sus propias funciones de autenticación y comunicándose directamente con cada API de los servicios.
-   **Cerca de las comunicaciones de campo (NFC)** : solo en (algunos) dispositivos Android (en el momento de escribir este artículo).

## <a name="dealing-with-platform-divergence"></a>Trabajar con la divergencia de plataforma

Hay dos enfoques diferentes para admitir varias plataformas desde el mismo código base, cada uno con su propio conjunto de ventajas y desventajas.

-   **Abstracción de plataforma** : el patrón de fachada de negocios, proporciona un acceso unificado a través de plataformas y abstrae las implementaciones de plataforma en particular a una API unificada.
-   **Implementación divergente** : características de invocación de plataforma concreta a través de implementaciones divergentes a través de herramientas de arquitectura como interfaces y herencia o compilación condicional.

## <a name="platform-abstraction"></a>Abstracción de plataforma

### <a name="class-abstraction"></a>Abstracción de clase

Mediante interfaces o clases base definido en el código compartido e implementado o extendido en proyectos específicos de plataforma. Escribir y ampliar el código compartido con las abstracciones de clase especialmente es adecuado para las bibliotecas de clases portables porque tienen un subconjunto limitado de framework a su disposición y no puede contener las directivas de compilador para admitir las bifurcaciones de código específico de la plataforma.

#### <a name="interfaces"></a>Interfaces

Interfaces permite implementar clases específicas de la plataforma que pueden pasarse en sus bibliotecas compartidas para aprovechar el código común.

La interfaz se define en el código compartido y pasa a la biblioteca compartida como un parámetro o una propiedad.

Las aplicaciones específicas de la plataforma pueden, a continuación, implemente la interfaz y seguir aprovechando el código compartido para 'procesar'.

 **Ventajas**

La implementación puede contener código específico de plataforma e incluso hacer referencia a bibliotecas externas de específicos de la plataforma.

 **Desventajas**

Tener que crear y pasar las implementaciones en el código compartido. Si se usa la interfaz profunda en el código compartido, a continuación, termina siendo pasan a través de varios parámetros de método o si no se han transferido a través de la cadena de llamadas. Si el código compartido usa una gran cantidad de interfaces diferentes, a continuación, deben todos crearse y establecer en el código compartido en alguna parte.

#### <a name="inheritance"></a>Herencia

El código compartido podría implementar clases abstractas o virtuales que se podrían ampliar en uno o varios proyectos específicos de la plataforma. Esto es similar al uso de interfaces pero con algún comportamiento que ya ha implementado. Hay puntos de vista diferentes si las interfaces o herencia son una mejor opción de diseño: en particular porque C# solo permite herencia única puede dictar la manera en que se pueden diseñar las API en el futuro. Usar la herencia con precaución.

Las ventajas y desventajas de las interfaces se aplican igualmente a la herencia, con la ventaja adicional de que la clase base puede contener código de implementación (quizás una todo independiente de la implementación de la plataforma que se puede extender si lo desea).

## <a name="xamarinforms"></a>Xamarin.Forms

Consulte la [Xamarin.Forms](~/get-started/index.md) documentación.

### <a name="other-cross-platform-libraries"></a>Otras bibliotecas multiplataforma

Estas bibliotecas ofrecen también funcionalidad multiplataforma para C# a los desarrolladores:

- [**Xamarin.Essentials** ](~/essentials/index.md) – APIs multiplataforma para características comunes.
- [**SkiaSharp** ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) – gráficos 2D multiplataforma.

## <a name="conditional-compilation"></a>Compilación condicional

Existen algunas situaciones donde el código compartido se deberá funcionan de manera diferente en cada plataforma, posiblemente tenga acceso a clases o funciones que se comportan de manera diferente. Compilación condicional funciona mejor con proyectos de activos compartidos, donde el mismo archivo de origen se hace referencia en varios proyectos que tienen distintos símbolos definidos.

Proyectos de Xamarin siempre definen `__MOBILE__` que es true para iOS y los proyectos de aplicación de Android (tenga en cuenta el carácter de subrayado doble previa y posteriores a la corrección en estos símbolos).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```
#### <a name="ios"></a>iOS

Xamarin.iOS define `__IOS__` que puede usar para detectar los dispositivos iOS.

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

#### <a name="android"></a>Android

Código que solo se debe compilar en aplicaciones de Xamarin.Android puede usar lo siguiente

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Cada versión de API también define una nueva directiva de compilador, por lo que el código como éste le permitirá agregar características si están destinadas a las API más recientes. Cada nivel de API incluye todos los símbolos 'inferiores' nivel. Esta característica no es realmente útil para la compatibilidad con múltiples plataformas; Normalmente el `__ANDROID__` símbolo será suficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

No hay actualmente un símbolo integrado para Xamarin.Mac, pero puede agregar un proyecto de aplicación de su equipo Mac **Opciones > compilar > compilador** en el **definir símbolos** cuadro o editar el **.csproj**  archivo y agregue existe (por ejemplo `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Use `WINDOWS_UWP`. No hay ningún caracteres de subrayado que rodea la cadena, como los símbolos de la plataforma Xamarin.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Compilación condicional

Un ejemplo sencillo de caso práctico de compilación condicional es establecer la ubicación del archivo para el archivo de base de datos de SQLite. Las tres plataformas tienen requisitos ligeramente diferentes para especificar la ubicación del archivo:

-   **iOS** : Apple prefiere datos que no son de usuario se coloquen en una ubicación específica (el directorio de biblioteca), pero no hay ninguna constante del sistema para este directorio. Se requiere código específico de plataforma para compilar la ruta de acceso correcta.
-   **Android** : la ruta de acceso del sistema devuelto por `Environment.SpecialFolder.Personal` es una ubicación para almacenar el archivo de base de datos aceptable.
-   **Windows Phone** : el mecanismo de almacenamiento aislado no se permite una ruta de acceso completa que se especifique una ruta de acceso relativa y el nombre.
-   **Plataforma universal de Windows** – usa `Windows.Storage` API.

El código siguiente usa la compilación condicional para garantizar la `DatabaseFilePath` es correcto para cada plataforma:

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
