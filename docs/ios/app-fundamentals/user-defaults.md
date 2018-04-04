---
title: Trabajar con valores predeterminados del usuario
description: Este artículo explica cómo trabajar con NSUserDefault para guardar la configuración predeterminada en una aplicación o extensión de iOS de Xamarin.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: aa28e7d5636b06c8ab1e46457537431b5d1c7f1a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-user-defaults"></a>Trabajar con valores predeterminados del usuario

_Este artículo explica cómo trabajar con NSUserDefault para guardar la configuración predeterminada en una extensión o Xamarin.iOS App._


La `NSUserDefaults` clase proporciona una manera de iOS aplicaciones y extensiones para interactuar mediante programación con el sistema tiene como valor predeterminado de todo el sistema. Mediante el sistema tiene como valor predeterminado, el usuario puede configurar el comportamiento de una aplicación o aplicar un estilo para satisfacer sus preferencias (en función del diseño de la aplicación). Por ejemplo, para presentar datos en vs métricas Imperial medidas o seleccionar un tema de la interfaz de usuario determinada.

Cuando se usa con grupos de aplicaciones, `NSUserDefaults` también proporciona una manera para la comunicación entre aplicaciones (o extensiones) dentro de un grupo determinado.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Acerca de los valores predeterminados del usuario

Como se indicó anteriormente, valores predeterminados del usuario (`NSUserDefaults`) puede agregar a una aplicación (o extensión) y se utiliza para proporcionar opciones configurables que el usuario final puede modificar para ajustar la apariencia o el funcionamiento de la aplicación en tiempo de ejecución.

Cuando la aplicación se ejecuta en primer lugar, `NSUserDefaults` lee las claves y valores de base de datos de usuario tiene como valor predeterminado a la aplicación y almacena en memoria caché en la memoria para evitar la apertura y lectura de la base de datos cada vez que se requiere un valor. 

> [!IMPORTANT]
> Apple ya no recomienda la llamada de desarrollador la `Synchronize` método para sincronizar la memoria caché en memoria con la base de datos directamente. En su lugar, se le automáticamente llamará a intervalos periódicos para mantener sincronizados con la base de datos de los valores predeterminados de un usuario la memoria caché en memoria.

La `NSUserDefaults` clase contiene varios métodos útiles para leer y escribir valores de preferencia para los tipos de datos comunes como: cadena, integer, float, boolean y las direcciones URL. Otros tipos de datos se pueden archivar mediante `NSData`, de lectura o escritura a la base de datos de usuario tiene como valor predeterminado. Para obtener más información, vea de Apple [preferencias y Guía de programación de configuración](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i).

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Obtener acceso a la instancia compartida NSUserDefaults 

La instancia de tiene como valor predeterminado de usuario compartido proporciona acceso a los valores predeterminados del usuario para el usuario actual del dispositivo. Si no existe el objeto compartido tiene como valor predeterminado, se puede crear la primera vez que se tiene acceso y se inicializa con la información siguiente:

- Un `NSArgumentDomain` que consta de los valores predeterminados que se analizan desde la aplicación actual.
- Dominio del identificador del paquete de la aplicación.
- Un `NSGlobalDomain` que consta de los valores predeterminados compartidos por todas las aplicaciones.
- Un dominio independiente para cada idioma preferido del usuario.
- Un `NSRegistrationDomain` con un conjunto de valores predeterminados temporales que se puede modificar la aplicación para asegurarse de que las búsquedas siempre son correctas.

Para obtener acceso a la instancia de tiene como valor predeterminado de usuario compartido, utilice el código siguiente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Acceso a una instancia de NSUserDefaults de grupo de aplicaciones

Como se indicó anteriormente, mediante el uso de grupos de aplicaciones, `NSUserDefaults` puede utilizarse para la comunicación entre aplicaciones (o extensiones) dentro de un grupo determinado. En primer lugar, debe asegurarse de que el grupo de aplicaciones y los identificadores de aplicación necesarios están configurados correctamente en el **certificados, identificadores & perfiles** sección [iOS centro de desarrollo de](https://developer.apple.com/devcenter/ios/) y se han instalado en el entorno de desarrollo.

A continuación, necesitan tener uno de los identificadores de aplicación válido creado anteriormente, los proyectos de aplicación y la extensión y el `Entitlements.plist` archivo debe incluirse en el grupo de aplicaciones con los grupos de aplicación habilitado y especificado.

Con todo esto en su lugar, el compartido aplicación grupo valores predeterminados del usuario puede tener acceso utilizando el código siguiente:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Donde `group.com.xamarin.todaysharing` es el grupo de aplicaciones creado en **certificados, identificadores & perfiles** que desea obtener acceso. Para obtener más información, consulte el [las capacidades de grupo de aplicaciones de](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentación.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Leer los valores predeterminados

Después de que hayan obtenido acceso a la base de datos de predeterminado deseado de usuario, puede leer valores de los valores predeterminados con pares clave/valor y varios métodos de comodidad según el tipo de datos que se va a leer:

- `ArrayForKey` -Devuelve una matriz de `NSObjects` para el valor de clave especificado.
- `BoolForKey` -Devuelve un valor booleano para la clave dada.
- `DataForKey` -Devuelve un `NSData` objeto con la clave dada.
- `DictionaryForKey` -Devuelve un `NSDictionary` con la clave dada.
- `DoubleForKey` -Devuelve un valor double con la clave dada.
- `FloatForKey` -Devuelve un valor de tipo float para la clave dada.
- `IntForKey` -Devuelve un valor entero para la clave dada.
- `StringArrayForKey` -Devuelve una matriz de `String` objetos desde el valor de clave especificado.
- `StringForKey` -Devuelve un valor de cadena para la clave dada.
- `URLForKey` -Devuelve un `NSUrl` valor con la clave dada.

Por ejemplo, el código siguiente podría leer un valor booleano de los valores predeterminados del usuario:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Escribir valores predeterminados

Igual que la lectura de los valores anteriores, después de que tiene acceso el usuario predeterminado base de datos deseada, puede escribir valores en los valores predeterminados con pares clave/valor y varios métodos de comodidad según el tipo de datos que se escriben:

- `SetBool` -Escribe el valor booleano especificado en la clave dada.
- `SetDouble` -Escribe el valor de tipo double determinado a la clave especificada.
- `SetFloat` -Escribe el valor de punto flotante dada en la clave dada.
- `SetString` -Escribe el valor de cadena especificada en la clave dada.
- `SetURL` -Escribe la dirección URL dada (`NSUrl`) valor para la clave dada.

Por ejemplo, el código siguiente podría escribir un valor booleano para los valores predeterminados del usuario:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Cuando la aplicación se ejecuta en primer lugar, `NSUserDefaults` lee las claves y valores de base de datos de usuario tiene como valor predeterminado a la aplicación y almacena en memoria caché en la memoria para evitar la apertura y lectura de la base de datos cada vez que se requiere un valor.



<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto el `NSUserDefaults` clase y cómo se puede utilizar para proporcionar un conjunto de opciones que el usuario final puede usar para configurar su Xamarin.iOS App. Además, tratan con grupos de aplicaciones para la comunicación entre una extensión y su aplicación primaria o entre aplicaciones en un grupo.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Las preferencias y configuración de guía de programación](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
