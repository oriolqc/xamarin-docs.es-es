---
title: Trabajar con el manifiesto de Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 5e354f8271257ab21a855bdf5d576ce3062fadc7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957399"
---
# <a name="working-with-the-android-manifest"></a>Trabajar con el manifiesto de Android


## <a name="overview"></a>Información general

**AndroidManifest.xml** es un archivo muy útil en la plataforma Android que le permite describir la funcionalidad y requisitos de la aplicación para Android. Sin embargo, no es fácil trabajar con él. Xamarin.Android ayuda a minimizar esta dificultad, ya que permite agregar atributos personalizados para sus clases, que se usará para generar automáticamente el manifiesto para usted. Nuestro objetivo es que nunca debería debe modificar manualmente el 99% de nuestros usuarios **AndroidManifest.xml**. 

**AndroidManifest.xml** se genera como parte de proceso de compilación y el XML que se encuentra dentro de **Properties/Androidmanifest.XML** se combina con XML que se genera a partir de los atributos personalizados. El resultado combinado **AndroidManifest.xml** reside en el **obj** subdirectorio; por ejemplo, reside en **obj/Debug/android/AndroidManifest.xml** para compilaciones de depuración . El proceso de combinación es trivial: lo usa atributos personalizados dentro del código para generar elementos XML, y *inserta* esos elementos en **AndroidManifest.xml**. 



## <a name="the-basics"></a>Conceptos básicos

En tiempo de compilación, se analizan ensamblados para que no sean de`abstract` clases que derivan de [actividad](https://developer.xamarin.com/api/type/Android.App.Activity/) y tener la [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) atributo declarado en ellos. A continuación, usa estas clases y atributos para generar el manifiesto. Por ejemplo, considere el siguiente código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Esto da como resultado nada que se generan en **AndroidManifest.xml**. Si desea que un `<activity/>` elemento va a generar, deberá usar el [`[Activity]`](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) 
atributo personalizado: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este ejemplo hace que el siguiente fragmento de xml que se agregarán a **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

El `[Activity]` atributo no tiene ningún efecto `abstract` tipos; `abstract` se omiten los tipos.



### <a name="activity-name"></a>Nombre de actividad

A partir de Xamarin.Android 5.1, el nombre de tipo de una actividad se basa en el MD5SUM del nombre completo de ensamblado del tipo que se va a exportar. Esto permite que el mismo nombre completo que se proporcionará en dos ensamblados diferentes y no recibe un error de empaquetado. (Antes de Xamarin.Android 5.1, el nombre de tipo predeterminado de la actividad se creó desde el espacio de nombres en minúsculas y el nombre de clase.) 

Si desea invalidar este comportamiento predeterminado y especificar explícitamente el nombre de la actividad, use el [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) propiedad: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento de xml:

```xml
<activity android:name="awesome.demo.activity" />
```

*Tenga en cuenta*: se debe utilizar el `Name` propiedad solo por motivos de compatibilidad con versiones anteriores, por lo tanto, cambiar el nombre puede ralentizar búsqueda del tipo en tiempo de ejecución. Si tiene código heredado que se espera que el nombre de tipo predeterminado de la actividad se base en el espacio de nombres en minúsculas y el nombre de clase, vea [Android nomenclatura de contenedor CCW](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) para sugerencias sobre cómo mantener la compatibilidad. 


### <a name="activity-title-bar"></a>Barra de título de la actividad

De forma predeterminada, Android ofrece a su aplicación en una barra de título cuando se ejecuta. El valor utilizado para esto es [ `/manifest/application/activity/@android:label` ](https://developer.android.com/guide/topics/manifest/activity-element.html#label). En la mayoría de los casos, este valor será distinto del nombre de su clase. Para especificar la etiqueta de la aplicación en la barra de título, use el [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) propiedad.
Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento de xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Se puede iniciar desde el selector de la aplicación

De forma predeterminada, la actividad no se mostrará en la pantalla de iniciador de aplicaciones de Android. Esto es porque probablemente habrá muchas actividades en la aplicación y no desea incluir un icono para cada uno de ellos. Para especificar cuál de ellos debe ser se puede iniciar desde el iniciador de aplicaciones, use el [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) propiedad. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento de xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>Icono de actividad

De forma predeterminada, la actividad se proporcionará el icono del iniciador de forma predeterminada proporcionado por el sistema. Para usar un icono personalizado, primero agregue su **.png** a **/drawable a recursos**, establece su acción de compilación en **AndroidResource**, a continuación, use el [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) propiedad para especificar el icono que se utiliza. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento de xml:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>Permisos

Al agregar permisos para el manifiesto de Android (como se describe en [agregar permisos al manifiesto de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), estos permisos se registran en **Properties/Androidmanifest.XML**. Por ejemplo, si establece la `INTERNET` permiso, se agrega el siguiente elemento a **Properties/Androidmanifest.XML**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Las compilaciones de depuración establecen automáticamente algunos permisos para realizar la depuración sea más sencilla (como `INTERNET` y `READ_EXTERNAL_STORAGE`) &ndash; se establecen estas opciones solo en generado **obj/Debug/android/AndroidManifest.xml** y no son se muestra como habilitado en el **permisos necesarios** configuración. 

Por ejemplo, si examina el archivo de manifiesto generado en **obj/Debug/android/AndroidManifest.xml**, es posible que vea los siguientes agregaron los elementos de permiso: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

En el lanzamiento de versión del manifiesto de compilación (en **obj/Debug/android/AndroidManifest.xml**), estos permisos son *no* configurada automáticamente. Si encuentra que el cambio a una versión de lanzamiento hace que la aplicación pierde un permiso que estaba disponible en la compilación de depuración, compruebe que ha establecido explícitamente este permiso el **permisos necesarios** configuración de la aplicación (consulte  **Compilar > aplicación de Android** en Visual Studio para Mac; vea **Propiedades > manifiesto de Android** en Visual Studio). 




## <a name="advanced-features"></a>Características avanzadas


### <a name="intent-actions-and-features"></a>Las características y las acciones preventivas

El manifiesto de Android proporciona una manera de describir las capacidades de la actividad. Esto se realiza mediante [intenciones](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) y el [`[IntentFilter]`](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) 
atributo personalizado. Puede especificar qué acciones son apropiadas para su actividad con el [`IntentFilter`](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) 
constructor y qué categorías son adecuadas con el [`Categories`](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) 
propiedad. Debe proporcionarse al menos una actividad (razón por la cual las actividades se proporcionan en el constructor). `[IntentFilter]` se puede proporcionar varias veces, y cada uso da lugar a otro `<intent-filter/>` elemento dentro de la `<activity/>`. Por ejemplo:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento de xml:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>Elemento de la aplicación

El manifiesto de Android también proporciona una manera de declarar propiedades para toda la aplicación. Esto se realiza mediante el `<application>` elemento y su equivalente, el [aplicación](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) atributo personalizado. Tenga en cuenta que estas son opciones de toda la aplicación (todo el ensamblado) en lugar de configuración por actividad. Normalmente, se declaran `<application>` propiedades para toda la aplicación y, a continuación, anular estos valores (según sea necesario) en una base por actividad. 

Por ejemplo, la siguiente `Application` se agrega al atributo **AssemblyInfo.cs** para indicar que la aplicación puede ser depurada, que es su nombre legible por el usuario **My App**, y que usa el `Theme.Light` estilo como el tema predeterminado para todas las actividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Esta declaración hace que se generen en el siguiente fragmento XML **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
En este ejemplo, todas las actividades de la aplicación usará de forma predeterminada el `Theme.Light` estilo. Si establece el tema de la actividad en `Theme.Dialog`, solo que usará la actividad la `Theme.Dialog` estilo mientras todas las demás actividades en la aplicación usará de forma predeterminada el `Theme.Light` estilo como se establece en el `<application>` elemento. 

El `Application` elemento no es la única manera de configurar `<application>` atributos. Como alternativa, puede insertar atributos directamente en el `<application>` elemento de **Properties/Androidmanifest.XML**. Estos valores se combinan en la última `<application>` elemento que se encuentra en **obj/Debug/android/AndroidManifest.xml**. Tenga en cuenta que el contenido de **Properties/Androidmanifest.XML** prevalecen sobre los datos proporcionados por los atributos personalizados. 

Hay muchos atributos de toda la aplicación que se pueden configurar en el `<application>` elemento; para obtener más información sobre estas opciones, consulte el [propiedades públicas](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) sección de [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : Genera un [/manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html) fragmento XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : Genera un [/manifiesto/aplicación](https://developer.android.com/guide/topics/manifest/application-element.html) fragmento XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : Genera un [/manifiesto o la instrumentación](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) fragmento XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : Genera un [//intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) fragmento XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : Genera un [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) fragmento XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : Genera un [//permission](https://developer.android.com/guide/topics/manifest/permission-element.html) fragmento XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : Genera un [//permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) fragmento XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : Genera un [//permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) fragmento XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : Genera un [/manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) fragmento XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : Genera un [/manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) fragmento XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : Genera un [/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) fragmento XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : Genera un [/manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) fragmento XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : Genera un [/manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html) fragmento XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : Genera un [/manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) fragmento XML

