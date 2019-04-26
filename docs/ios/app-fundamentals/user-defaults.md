---
title: Trabajar con valores predeterminados del usuario en Xamarin.iOS
description: En este artículo se explica cómo trabajar con NSUserDefaults para guardar la configuración predeterminada en una aplicación Xamarin iOS o la extensión. Describe NSUserDefaults un alto nivel y se explica cómo leer y escribir valores.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 688db534d6c99a8fadb7535f0532f9c1e9564707
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153392"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Trabajar con valores predeterminados del usuario en Xamarin.iOS

_En este artículo se explica cómo trabajar con NSUserDefault para guardar la configuración predeterminada en una extensión o Xamarin.iOS App._


La `NSUserDefaults` clase proporciona una manera para iOS, aplicaciones y extensiones para interactuar mediante programación con el sistema tiene como valor predeterminado de todo el sistema. Mediante el sistema tiene como valor predeterminado, el usuario puede configurar el comportamiento de una aplicación o aplicar estilos acordes con sus preferencias (según el diseño de la aplicación). Por ejemplo, para presentar los datos de métrica vs Imperial medidas o seleccionar un tema de la interfaz de usuario determinado.

Cuando se utiliza con grupos de aplicaciones, `NSUserDefaults` también proporciona una manera para la comunicación entre aplicaciones (o extensiones) dentro de un grupo determinado.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Acerca de los valores predeterminados del usuario

Como se indicó anteriormente, valores predeterminados del usuario (`NSUserDefaults`) se pueden agregar a una aplicación (o extensión) y usa para proporcionar opciones configurables que el usuario final puede modificar para ajustar el aspecto o la operación de la aplicación en tiempo de ejecución.

Cuando la aplicación se ejecuta en primer lugar, `NSUserDefaults` lee las claves y valores de los valores predeterminados de la base de datos del usuario de la aplicación y se almacena en memoria caché en memoria para evitar la apertura y lectura de la base de datos cada vez que se requiere un valor. 

> [!IMPORTANT]
> Apple recomienda que ya no, la llamada de desarrollador la `Synchronize` método para sincronizar la memoria caché en memoria con la base de datos directamente. En su lugar, se le automáticamente llamará a intervalos periódicos para mantener sincronizados con la base de datos de los valores predeterminados de un usuario la memoria caché en memoria.

La `NSUserDefaults` clase contiene varios métodos útiles para leer y escribir valores de preferencia para los tipos de datos comunes, como: cadena, entero, float, boolean y direcciones URL. Otros tipos de datos se pueden archivar con `NSData`, leen o escriben en la base de datos de usuario tiene como valor predeterminado. Para obtener más información, consulte Apple [las preferencias y la Guía de programación de configuración](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i).

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Obtener acceso a la instancia de valores NSUserDefaults compartidos 

La instancia tiene como valor predeterminado de usuario compartido proporciona acceso a los valores predeterminados del usuario para el usuario actual del dispositivo. Si no existe el objeto compartido tiene como valor predeterminado, se crea la primera vez se tiene acceso y se inicializa con la siguiente información:

- Un `NSArgumentDomain` que consta de los valores predeterminados que se analiza desde la aplicación actual.
- Dominio del identificador de paquete de la aplicación.
- Un `NSGlobalDomain` que consta de los valores predeterminados compartidos por todas las aplicaciones.
- Un dominio independiente para cada uno de los lenguajes preferidos del usuario.
- Un `NSRegistrationDomain` con un conjunto de valores predeterminados temporales que se puede modificar la aplicación para asegurarse de que las búsquedas siempre son correctas.

Para obtener acceso a la instancia tiene como valor predeterminado de usuario compartida, use el código siguiente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Acceso a una instancia de NSUserDefaults del grupo de aplicaciones

Como se indicó anteriormente, mediante el uso de grupos de aplicaciones, `NSUserDefaults` puede utilizarse para la comunicación entre aplicaciones (o extensiones) dentro de un grupo determinado. En primer lugar, debe asegurarse de que el grupo de aplicaciones y los identificadores de aplicación necesarios se han configurado correctamente en el **certificados, identificadores y perfiles** sección [centro de desarrollo de iOS](https://developer.apple.com/devcenter/ios/) y se han instalado en el entorno de desarrollo.

A continuación, deben tener uno de los identificadores de aplicación válido que ha creado anteriormente, los proyectos de aplicación y la extensión y el `Entitlements.plist` archivo debe incluirse en el lote de aplicaciones con los grupos de aplicación habilitado y especificado.

Con todo esto en su lugar, el compartido aplicación de grupo de valores predeterminados del usuario se puede acceder mediante el siguiente código:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Donde `group.com.xamarin.todaysharing` se crea el grupo de aplicaciones en **certificados, identificadores y perfiles** que desea tener acceso. Para obtener más información, consulte el [las capacidades de grupos de aplicación](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentación.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Leer los valores predeterminados

Una vez que han tenido acceso a la base de datos de predeterminado deseada de usuario, puede leer los valores de los valores predeterminados con pares clave/valor y varios métodos útiles según el tipo de datos que se va a leer:

- `ArrayForKey` -Devuelve una matriz de `NSObjects` para el valor de clave especificado.
- `BoolForKey` -Devuelve un valor booleano para la clave especificada.
- `DataForKey` -Devuelve un `NSData` objeto para la clave especificada.
- `DictionaryForKey` -Devuelve un `NSDictionary` para la clave especificada.
- `DoubleForKey` -Devuelve un valor double para la clave especificada.
- `FloatForKey` -Devuelve un valor float para la clave especificada.
- `IntForKey` -Devuelve un valor entero para la clave especificada.
- `StringArrayForKey` -Devuelve una matriz de `String` objetos desde el valor de clave especificado.
- `StringForKey` -Devuelve un valor de cadena para la clave especificada.
- `URLForKey` -Devuelve un `NSUrl` valor para la clave especificada.

Por ejemplo, el código siguiente sería un valor booleano de los valores predeterminados del usuario:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Escribir los valores predeterminados

Al igual que leer los valores indicados anteriormente, una vez que ha tenido acceso el usuario predeterminado base de datos deseada, puede escribir valores en los valores predeterminados con pares clave/valor y varios métodos útiles según el tipo de datos que se escriben:

- `SetBool` -Escribe el valor booleano especificado en la clave especificada.
- `SetDouble` -Escribe el valor doble especificado en la clave especificada.
- `SetFloat` -Escribe el valor de punto flotante dada a la clave especificada.
- `SetString` -Escribe el valor de cadena especificada en la clave especificada.
- `SetURL` -Escribe la dirección URL especificada (`NSUrl`) valor a la clave especificada.

Por ejemplo, el código siguiente escribiría un valor booleano a los valores predeterminados del usuario:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Cuando la aplicación se ejecuta en primer lugar, `NSUserDefaults` lee las claves y valores de los valores predeterminados de la base de datos del usuario de la aplicación y se almacena en memoria caché en memoria para evitar la apertura y lectura de la base de datos cada vez que se requiere un valor.



<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto el `NSUserDefaults` clase y cómo se puede usar para proporcionar un conjunto de opciones que el usuario final puede usar para configurar su App Xamarin.iOS. Además, se ha descrito con grupos de aplicaciones para la comunicación entre una extensión y su aplicación primaria o entre aplicaciones en un grupo.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Guía de programación de configuraciones y preferencias](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
