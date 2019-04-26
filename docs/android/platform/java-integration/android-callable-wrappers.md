---
title: Contenedores que se pueden llamar de Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 7edbdaa5a690a641523cb5baad7909ed01992aa5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61090909"
---
# <a name="android-callable-wrappers"></a>Contenedores que se pueden llamar de Android

Android contenedores RCW (ACWs) son necesarios siempre que el tiempo de ejecución Android invoca a código administrado. Estos contenedores son necesarios porque no hay ninguna manera de registrar las clases con arte (el tiempo de ejecución de Android) en tiempo de ejecución. (En concreto, el [DefineClass() JNI función](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) no es compatible con el tiempo de ejecución de Android.} Contenedores RCW Android, por tanto, compensar la falta de compatibilidad de registro de tipo en tiempo de ejecución. 

*Cada vez que* código Android es necesario ejecutar una `virtual` o método que es la interfaz `overridden` o se implementa en código administrado, Xamarin.Android debe proporcionar un proxy de Java para que este método se envía al tipo administrado adecuado. Estos tipos de proxy Java son código de Java que tiene el "mismo" clase base y la lista de interfaces de Java como el tipo administrado, la implementación de los constructores mismos y declarar cualquier clase base invalidado y los métodos de interfaz. 

Android contenedores RCW generados por el **monodroid.exe** de programa durante la [proceso de compilación](~/android/deploy-test/building-apps/build-process.md): se generan para todos los tipos que heredan (directa o indirectamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Nomenclatura de contenedor CCW Android

Los nombres de paquete para contenedores RCW Android se basan en el MD5SUM del nombre completo de ensamblado del tipo que se va a exportar. Esta técnica nomenclatura hace posible para el mismo nombre de tipo completo para que estén disponibles mediante distintos ensamblados sin introducir un error de empaquetado. 

Debido a este esquema de nomenclatura MD5SUM, no puede tener acceso directamente a sus tipos por su nombre. Por ejemplo, la siguiente `adb` comando no funcionará porque el nombre de tipo `my.ActivityType` no se genera de forma predeterminada: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Además, pueden aparecer errores similar al siguiente si intenta hacer referencia a un tipo por nombre:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Si se *hacer* requieren acceso a los tipos por su nombre, puede declarar un nombre para ese tipo en una declaración de atributo. Por ejemplo, este es el código que declara una actividad con el nombre completo `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

El `ActivityAttribute.Name` propiedad puede establecerse para declarar explícitamente el nombre de esta actividad: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Después de agrega el valor de esta propiedad, `my.ActivityType` puede tener acceso por nombre de código externo y de `adb` secuencias de comandos. El `Name` atributo puede establecerse para muchos tipos diferentes incluidos `Activity`, `Application`, `Service`, `BroadcastReceiver`, y `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

Denominación de ACW basada en MD5SUM se introdujo en Xamarin.Android 5.0. Para obtener más información sobre la nomenclatura de atributo, vea [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Implementar interfaces

Hay veces cuando es posible que necesite para implementar una interfaz de Android, como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Puesto que todas las clases de Android e interfaz amplía el [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaz, la pregunta que surge es: ¿cómo se implementa `IJavaObject`? 

Se respondió la pregunta anterior: la razón por la que todos los tipos de Android deben implementar `IJavaObject` es para que Xamarin.Android tiene un contenedor CCW Android para proporcionar a Android, es decir, un proxy de Java para el tipo especificado. Puesto que **monodroid.exe** solo busca `Java.Lang.Object` subclases y `Java.Lang.Object` implementa `IJavaObject,` la respuesta es obvia: subclase `Java.Lang.Object`: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```


## <a name="implementation-details"></a>Detalles de implementación

*El resto de esta página proporciona los detalles de implementación sujeta a cambios sin previo aviso* (y se presenta aquí solo porque los desarrolladores estarán curiosidad sobre lo que está ocurriendo). 

Por ejemplo, dada la siguiente C# origen:

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

El **mandroid.exe** programa generará el siguiente contenedor invocable Android: 

```java
package mono.samples.helloWorld;

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Tenga en cuenta que se conserva la clase base, y `native` se proporcionan declaraciones de método para cada método que se invalide en código administrado. 
