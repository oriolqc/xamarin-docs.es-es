---
title: Limitaciones
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 07353cfa3ce9bc20688b9a59c09a6e602f16dd60
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="limitations"></a>Limitaciones

Puesto que las aplicaciones en Android requieren generar tipos de proxy de Java durante el proceso de compilación, no es posible generar todo el código en tiempo de ejecución.

Éstas son las limitaciones de Xamarin.Android en comparación con el escritorio Mono:


## <a name="limited-dynamic-language-support"></a>Compatibilidad de idioma dinámico limitado

 [Contenedores CCW Android](~/android/platform/java-integration/android-callable-wrappers.md) son necesarias siempre que el tiempo de ejecución Android es necesario para llamar a código administrado. Contenedores CCW Android se generan en tiempo de compilación, en función de análisis estático de IL. El resultado neto de este: se *no* usar lenguajes dinámicos (IronPython, IronRuby, etc.) en cualquier escenario donde subclases de tipos de Java se requiere (incluidas las subclases indirecta), porque no hay ninguna manera de extraer estos tipos dinámicos en tiempo de compilación para generar los contenedores CCW Android necesarios.


## <a name="limited-java-generation-support"></a>Compatibilidad con la generación de Java limitado

[Contenedores CCW Android](~/android/platform/java-integration/android-callable-wrappers.md) se deben generar para código de Java llamar a código administrado. *De forma predeterminada*, Android contenedores CCW solo contendrá (ciertas) constructores declarados y los métodos que invalidación un método virtual de Java (es decir, tiene [ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) o implementar un método de interfaz de Java () interfaz del mismo modo tiene `Attribute`).
  
Antes de la versión 4.1, no se pudieron declarar métodos adicionales. Con la versión 4.1, [el `Export` y `ExportField` atributos personalizados se pueden utilizar para declarar métodos de Java y campos en el contenedor invocable Android](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Constructores que faltan

Constructores permanecen complicados, a menos que [ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute) se utiliza. El algoritmo para generar constructores Android RCW es un constructor de Java se emitirá si:

1. Hay una asignación de Java para todos los tipos de parámetro
2. La clase base declara el mismo constructor &ndash; esto es necesario porque el contenedor invocable Android *debe* invocar el constructor de clase base correspondiente; no pueden utilizarse argumentos predeterminados (como no hay ninguna manera fácil determinar qué valores se deben usar dentro de Java).

Por ejemplo, considere la siguiente clase:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Aunque esta parece perfectamente lógico, el contenedor invocable Android resultante *en versiones de lanzamiento* no contendrá un constructor predeterminado. Por lo tanto, si intenta iniciar este servicio (por ejemplo, [ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)), se producirá un error:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

La solución consiste en declarar un constructor predeterminado, adornar con el `ExportAttribute`y establezca el [ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```


### <a name="generic-c-classes"></a>Clases genéricas de C#

Clases genéricas de C# se admiten parcialmente. Existen las siguientes limitaciones:


-   No se pueden usar tipos genéricos `[Export]` o `[ExportField`]. Intenta hacerlo, se generará una `XA4207` error.

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

-   No pueden usar los métodos genéricos `[Export]` o `[ExportField]`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

-   `[ExportField]` no puede usarse en métodos que devuelven `void`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

-   Instancias de tipos genéricos _no debe_ crearse a partir de código de Java.
    Se pueden crear solo de forma segura desde el código administrado:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```


## <a name="partial-java-generics-support"></a>La compatibilidad con genéricos de Java parcial

La compatibilidad con elementos genéricos de enlace de Java está limitado. Especialmente, los miembros de una clase de instancia genérico que se deriva de otra clase genérica (no crea una instancia) quedan expuestos como Java.Lang.Object. Por ejemplo, [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/) método devuelve Java.Lang.Object. Esto es debido a borrados genéricos de Java.
Tenemos algunas clases que no son aplicables a esta limitación, pero se ajustarán manualmente.


## <a name="related-links"></a>Vínculos relacionados

- [Contenedores que se pueden llamar de Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
