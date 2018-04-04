---
title: Trabajar con el manifiesto de Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 18817063900437baa625d8572f0ae28fec77be1e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-the-android-manifest"></a>Trabajar con el manifiesto de Android


## <a name="overview"></a>Información general

**AndroidManifest.xml** es un archivo eficaces en la plataforma Android que le permite describir la funcionalidad y los requisitos de la aplicación para Android. Sin embargo, no es fácil trabajar con él. Xamarin.Android ayuda a minimizar esta dificultad, ya que permite agregar atributos personalizados para las clases, que se utilizará para generar automáticamente el manifiesto para usted. Nuestro objetivo es que 99% de nuestros usuarios nunca debería ser necesario modificar manualmente **AndroidManifest.xml**. 

**AndroidManifest.xml** se genera como parte del proceso de compilación y el XML se encuentra en **Properties/AndroidManifest.xml** se combina con XML que se genera a partir de atributos personalizados. El cuadro combinado **AndroidManifest.xml** reside en el **obj** subdirectorio; por ejemplo, se encuentra en **obj/Debug/android/AndroidManifest.xml** para las compilaciones de depuración . El proceso de combinación es trivial: usa atributos personalizados en el código para generar elementos XML, y *inserta* esos elementos en **AndroidManifest.xml**. 



## <a name="the-basics"></a>Conceptos básicos

En tiempo de compilación, se examinan el ensamblados no son`abstract` clases que derivan de [actividad](https://developer.xamarin.com/api/type/Android.App.Activity/) y tener la [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) atributo declarado en ellos. A continuación, usa estas clases y atributos para compilar el manifiesto. Por ejemplo, considere el siguiente código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Esto da como resultado nada que se generan en **AndroidManifest.xml**. Si desea que un `<activity/>` elemento debe generarse, debe usar el [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) atributos personalizados: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este ejemplo produce el siguiente fragmento de xml para agregarse a **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

El `[Activity]` atributo no tiene ningún efecto `abstract` tipos; `abstract` se omiten los tipos.



### <a name="activity-name"></a>Nombre de actividad

A partir de Xamarin.Android 5.1, el nombre de tipo de una actividad se basa en el MD5SUM del nombre completo de ensamblado del tipo que se va a exportar. Esto permite que el mismo nombre completo que se proporcionará en ensamblados diferentes y no recibe un error de empaquetado. (Antes Xamarin.Android 5.1, el nombre del tipo predeterminado de la actividad se creó desde el espacio de nombres minúscula y el nombre de clase.) 

Si desea invalidar este comportamiento predeterminado y especificar explícitamente el nombre de la actividad, use la [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) propiedad: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml:

```xml
<activity android:name="awesome.demo.activity" />
```

*Tenga en cuenta*: debe usar el `Name` propiedad solo por razones de compatibilidad con versiones anteriores, como por ejemplo, cambiar el nombre puede ralentizar la búsqueda de tipos en tiempo de ejecución. Si tiene código heredado que se espera que el nombre del tipo predeterminado de la actividad se basen en el espacio de nombres minúscula y el nombre de clase, vea [Android nombres de contenedor CCW](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) para obtener sugerencias sobre el mantenimiento de compatibilidad. 


### <a name="activity-title-bar"></a>Barra de título de la actividad

De forma predeterminada, Android asigna a la aplicación una barra de título cuando se ejecuta. El valor utilizado para esto es [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). En la mayoría de los casos, este valor será distinto del nombre de su clase. Para especificar la etiqueta de la aplicación en la barra de título, use la [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) propiedad.
Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Puede iniciar desde el selector de la aplicación

De forma predeterminada, la actividad no se mostrará en la pantalla de iniciador de aplicación de Android. Esto es porque es posible que haya muchas actividades en la aplicación y no desea incluir un icono para cada uno de ellos. Para especificar cuál debe ser se puede iniciar desde el iniciador de la aplicación, use la [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) propiedad. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml:

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

De forma predeterminada, la actividad se proporcionará el icono del iniciador predeterminado proporcionado por el sistema. Para utilizar un icono personalizado, primero agregue su **.png** a **/pueden dibujar los recursos**, establezca su acción de compilación **AndroidResource**, a continuación, utilice el [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) propiedad para especificar el icono que se utiliza. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml:

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

Al agregar permisos para el manifiesto de Android (como se describe en [agregar permisos al manifiesto de Android](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest/)), estos permisos se registran en **Properties/AndroidManifest.xml**. Por ejemplo, si establece la `INTERNET` permiso, se agrega el siguiente elemento a **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Compilaciones de depuración establecen automáticamente algunos permisos para realizar la depuración más fácil (como `INTERNET` y `READ_EXTERNAL_STORAGE`) &ndash; estas opciones se establecen únicamente en los botones generados **obj/Debug/android/AndroidManifest.xml** y no son se muestra como habilitado en el **los permisos necesarios** configuración. 

Por ejemplo, si examina el archivo de manifiesto generado en **obj/Debug/android/AndroidManifest.xml**, es posible que vea elementos de permiso de agregar lo siguiente: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

En la versión de versión del manifiesto de compilación (en **obj/Debug/android/AndroidManifest.xml**), estos permisos son *no* configurada automáticamente. Si encuentra que el cambio a una versión de lanzamiento hace que la aplicación pierda un permiso que estaba disponible en la versión de depuración, compruebe que ha establecido explícitamente este permiso el **los permisos necesarios** opciones de la aplicación (consulte  **Compilar > aplicación Android** en Visual Studio para Mac; vea **Propiedades > manifiesto Android** en Visual Studio). 




## <a name="advanced-features"></a>Características avanzadas


### <a name="intent-actions-and-features"></a>Funciones y acciones de intención

El manifiesto de Android proporciona una manera de describir las funciones de la actividad. Esto se realiza mediante [intentos](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) y [ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) atributo personalizado. Puede especificar qué acciones son apropiadas para su actividad con el [ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) constructor, y qué categorías apropiadas con la [ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) propiedad. Se debe proporcionar al menos una actividad (que es la razón por la que se muestran las actividades en el constructor). `[IntentFilter]` se puede proporcionar varias veces, y cada vez que se produce en otro `<intent-filter/>` elemento dentro de la `<activity/>`. Por ejemplo:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml:

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

El manifiesto de Android también proporciona una manera de declarar propiedades para toda la aplicación. Esto se realiza mediante la `<application>` elemento y su homólogo, el [aplicación](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) atributo personalizado. Tenga en cuenta que se trata de configuración de toda la aplicación (todo el ensamblado) en lugar de valores por actividad. Normalmente, se declara `<application>` propiedades para toda la aplicación y, a continuación, invalidar estos valores (según sea necesario) en una base por actividad. 

Por ejemplo, la siguiente `Application` atributo se agrega a **AssemblyInfo.cs** para indicar que la aplicación puede ser depurada, que es el nombre legible para el usuario **My App**, y que usa el `Theme.Light` estilo como el tema predeterminado para todas las actividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Esta declaración hace que el siguiente fragmento XML que se generarán en **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
En este ejemplo, todas las actividades de la aplicación predeterminado será el `Theme.Light` estilo. Si establece el tema de la actividad en `Theme.Dialog`, solo que usará la actividad la `Theme.Dialog` estilo mientras todas las demás actividades en la aplicación tendrá como valor predeterminado el `Theme.Light` estilo como se establece en el `<application>` elemento. 

El `Application` elemento no es la única manera de configurar `<application>` atributos. Como alternativa, puede insertar atributos directamente en el `<application>` elemento de **Properties/AndroidManifest.xml**. Estos valores se combinan en la última `<application>` elemento que se encuentra en **obj/Debug/android/AndroidManifest.xml**. Tenga en cuenta que el contenido de **Properties/AndroidManifest.xml** prevalecen sobre los datos proporcionados por los atributos personalizados. 

Hay muchos atributos de toda la aplicación que se pueden configurar en el `<application>` elemento; para obtener más información acerca de estas opciones, consulte la [propiedades públicas](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) sección de [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : genera un [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) fragmento XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : genera un [/manifiesto/aplicación](http://developer.android.com/guide/topics/manifest/application-element.html) fragmento XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : genera un [/manifiesto/Instrumental](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) fragmento XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : genera un [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) fragmento XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : genera un [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) fragmento XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : genera un [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) fragmento XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : genera un [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) fragmento XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : genera un [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) fragmento XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : genera un [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) fragmento XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : genera un [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) fragmento XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : genera un [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) fragmento XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : genera un [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) fragmento XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : genera un [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) fragmento XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : genera un [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) fragmento XML

