---
title: Sugerencias para la solución de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 961f9f38687790343f225d95c74e00e98f594c28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773870"
---
# <a name="troubleshooting-tips"></a>Sugerencias para la solución de problemas


## <a name="getting-diagnostic-information"></a>Obteniendo información de diagnóstico

Xamarin.Android tiene unos pocos lugares para buscar al realizar el seguimiento de varios errores.
Se incluyen los siguientes:

1.  Resultados de diagnóstico MSBuild.
2.  Registros de implementación de dispositivo.
3.  Resultados del registro de depuración de Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Resultado del diagnóstico de MSBuild

MSBuild diagnóstico puede contener información adicional relacionada con la creación de paquetes y puede contener información de implementación de paquete.

Para habilitar el resultado de diagnóstico de MSBuild en Visual Studio:

1.  Haga clic en **Herramientas > Opciones...**
2.  En la vista de árbol de la izquierda, seleccione **proyectos y soluciones > compilar y ejecutar**
3.  En el panel derecho, establezca la lista desplegable nivel de detalle de la salida de MSBuild compilación para diagnóstico
4.  Haga clic en **Aceptar**.
5.  Limpie y recompile el paquete.
6.  Resultados de diagnóstico está visible dentro del panel de salida.


Para habilitar el resultado del diagnóstico de MSBuild en Visual Studio para Mac/OS X:

1.  Haga clic en **Visual Studio para Mac > Preferencias...**
2.  En la vista de árbol de la izquierda, seleccione **proyectos > compilar**
3.  En el panel derecho, establezca el nivel de detalle de registro lista desplegable para diagnóstico
4.  Haga clic en **Aceptar**.
5.  Reinicie Visual Studio para Mac.
6.  Limpie y recompile el paquete.
7.  Resultados de diagnóstico sea visible en el panel de errores (**Vista > rellena > errores** ), haciendo clic en el botón de salida de compilación.




## <a name="device-deployment-logs"></a>Registros de implementación de dispositivo

Para habilitar el registro de implementación de dispositivos en Visual Studio:

1.  **Herramientas > Opciones...**>
2.  En la vista de árbol de la izquierda, seleccione **Xamarin > configuración de Android**
3.  En el panel derecho, habilitar [X] **el registro de depuración de extensión (escribe monodroid.log en el escritorio)** casilla de verificación.
4.  Mensajes de registro se escriben en el archivo monodroid.log en el escritorio.


Visual Studio para Mac siempre escribe registros de implementación de dispositivo. Buscándolas es ligeramente más difícil; un *AndroidUtils* archivo de registro se crea para cada día y hora en que se produzca una implementación, por ejemplo: **AndroidTools-2012-10-24_12-35-45.log**.

-  En Windows, los archivos de registro se escriben en `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
-  En OS X, los archivos de registro se escriben en `$HOME/Library/Logs/XamarinStudio-{VERSION}`.




## <a name="android-debug-log-output"></a>Resultados del registro de depuración de Android

Android escribirá muchos mensajes a la [el registro de depuración Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin.Android utiliza propiedades de sistema Android para controlar la generación de mensajes adicionales en el registro de depuración de Android. Se pueden establecer propiedades del sistema Android mediante el *setprop* comando dentro de la [Android depuración Bridge (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Propiedades del sistema se leen durante el inicio del proceso y, por tanto, debe establecer antes de que se inicia la aplicación o la aplicación debe reiniciarse después de que se cambian las propiedades del sistema.



### <a name="xamarinandroid-system-properties"></a>Propiedades del sistema de Xamarin.Android

Xamarin.Android admite las siguientes propiedades del sistema:

-   *Debug.mono.Debug*: si una cadena no vacía, esto equivale a `*mono-debug*`.

-   *Debug.mono.env*: una canalización separada ('*|*') lista de variables de entorno que se va a exportar durante el inicio de la aplicación, *antes de* mono se ha inicializado. Esto permite establecer variables de entorno que el registro mono control.

    - *Tenga en cuenta*: puesto que el valor es '*|*'-separados, el valor debe tener un nivel adicional de comillas, como el \` *adb shell* \` comando quitará un conjunto de comillas.

    - *Tenga en cuenta*: valores de propiedad del sistema Android no pueden tener más de 92 caracteres de longitud.

    - Ejemplo:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *Debug.mono.log*: separados por comas ('*,*') lista de componentes que debe imprimir los mensajes adicionales en el registro de depuración de Android. De forma predeterminada, se establece nada. Los componentes incluyen:

    -   *todos los*: todos los mensajes de impresión
    -   *GC*: mensajes relacionados con la impresión GC.
    -   *gref*: imprimir los mensajes de asignación y desasignación de referencia (débil, global).
    -   *lref*: imprimir los mensajes de asignación y desasignación de referencia local.

    *Tenga en cuenta*: se trata de *muy* detallado. No habilite a menos que sea realmente necesario.

-   *Debug.mono.Trace*: permite establecer el [mono--seguimiento](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` configuración.



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android no se puede resolver System.ValueTuple

Este error se produce debido a una incompatibilidad con Visual Studio.

- **Visual Studio 2017 Update 1** (versión 15.1 o anterior) sólo es compatible con la **System.ValueTuple NuGet 4.3.0** (o anterior).

- **Actualización 2 de Visual Studio de 2017** (versión más reciente o 15.2) solo es compatible con la **System.ValueTuple NuGet 4.3.1** (o posterior).

Elija System.ValueTuple NuGet correcta que se corresponde con la instalación de Visual Studio de 2017.


## <a name="gc-messages"></a>Mensajes de GC

Mensajes de componentes de GC pueden verse estableciendo la propiedad del sistema debug.mono.log en un valor que contiene el catálogo global.

Se generan mensajes de GC siempre que el GC se ejecuta y proporciona información sobre la cantidad de trabajo el catálogo global ha:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Información adicional de GC como información de tiempo puede generarse estableciendo la `MONO_LOG_LEVEL` variable de entorno `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Esto dará como resultado mensajes Mono adicionales de (una gran cantidad de), incluidos estos tres consecuencias:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

En el `GC_BRIDGE` mensaje, `num-objects` es el número de objetos de puente está considerando esta fase, y `num_hash_entries` es el número de objetos procesados durante esta invocación del código de puente.

En el `GC_MINOR` y `GC_MAJOR` mensajes, `total` es la cantidad de tiempo mientras está en pausa el mundo (no hay ningún subproceso se ejecuta), mientras que `bridge` es la cantidad de tiempo que se tarda en el puente de procesamiento de código (que se ocupa de la máquina virtual Java). Es el mundo *no* en pausa mientras se realiza el procesamiento de puentes.

 *En general*, cuanto mayor sea el valor de `num_hash_entries`, el más tiempo que el `bridge` tarda colecciones y cuanto mayor sea el `total` será el tiempo invertido en recopilar.



## <a name="global-reference-messages"></a>Mensajes de referencia global

Para habilitar la referencia Global loggig (GREF), el registro, el *debug.mono.log* debe contener la propiedad del sistema *gref*, p. ej.:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android usa Android referencias globales para proporcionar las asignaciones entre las instancias de Java y las instancias administradas asociadas, como al invocar un método de Java que una instancia de Java debe proporcionarse con Java.

Por desgracia, emuladores de Android solo permiten 2000 referencias globales que existen en un momento. Hardware tiene un límite de cantidad mayor de 52000 referencias globales. El límite inferior puede causar problemas al ejecutar aplicaciones en el emulador, así que saber *donde* el procedían de instancia de puede ser muy útil.

 *Tenga en cuenta*: el recuento de referencias global es interno Xamarin.Android y no es así (y no puede ser) incluyen referencias globales suscritas por otras bibliotecas nativas cargados en el proceso. Utilice el recuento de referencias globales como una estimación.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Hay cuatro mensajes de consecuencia:

-  Creación de referencia global: estas son las líneas que empiezan por *+ g +* y proporcionará un seguimiento de pila para la ruta de acceso del código creando.
-  Destrucción de referencia global: estas son las líneas que empiezan por *- g-* y puede proporcionar un seguimiento de pila para la ruta de acceso de código que elimine la referencia global. Si se va a eliminar el catálogo global de la gref, no se proporcionará ningún seguimiento de pila.
-  Creación de referencia de débil global: estas son las líneas que empiezan por *+ w +* .
-  Débil globales hacen referencia a la destrucción: estas son las líneas que empiezan por *-w-* .


En todos los mensajes, el *grefc* valor es el recuento de referencias globales que haya creado Xamarin.Android, mientras que la *grefwc* valor es el recuento de referencias débiles de globales creados por Xamarin.Android. El *controlar* o *obj identificador* valor es el valor del identificador JNI y el carácter posterior a la ' */*' es el tipo de valor de identificador:   */l* como referencia local, */G* para referencias globales, y */W* para referencias globales débiles.

Como parte del proceso de GC, referencias globales (+ g +) se convierten en referencias débiles de globales (causando a + w + y - g-), se ha iniciado un GC del lado de Java y, a continuación, la referencia débil global se comprueba para ver si se recopilaron. Si no mantiene la conexión, se crea un nuevo gref alrededor de la referencia débil (+ g +, -w-), en caso contrario, se destruye la referencia débil (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Instancia de Java se crea y se ajusta por un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Se está realizando un GC...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>Objeto todavía está activo, como identificador! = null
## <a name="wref-turned-back-into-a-gref"></a>Wref activarse otra vez en un gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>Objeto está inactivo, como identificador == null
## <a name="wref-is-freed-no-new-gref-created"></a>Wref es liberado, no hay nuevo gref creado

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Hay una arruga "interesante" aquí: en destinos ejecuta Android anteriores a la 4.0, el valor de gref es igual a la dirección del objeto de Java en la memoria del runtime Android. (Es decir, el catálogo global es un no mover, conservador, recopilador, y es repartir de referencias directas a esos objetos.) Por lo tanto después un g +, + w +, - g-, + g +, - w-sequence, el gref resultante tendrá el mismo valor que el valor original de gref. Esto hace que grepping a través de registros sea bastante sencilla.

Android 4.0, sin embargo, tiene un recopilador móvil y ya no se distribuye referencias directas a Android en tiempo de ejecución objetos de máquina virtual. Por lo tanto, después de un g +, + w +, - g-, + g +, - w secuencia, el valor de gref *serán diferentes*. Si el objeto sobrevive varios catálogos globales, se desplazará por varios valores de gref, lo que es más difícil determinar dónde realmente se ha asignado una instancia de.

### <a name="querying-programmatically"></a>Consultar mediante programación

Puede consultar el GREF y WREF recuentos consultando la `JniRuntime` objeto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` -Recuento de referencias global

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -Recuento de referencias débil



## <a name="offline-activation"></a>Activación sin conexión

Si es que no se puede activar Xamarin.Android en Windows, o no se puede instalar la versión completa de Xamarin.Android en Mac OS X, consulte el [activación sin conexión](~/android/get-started/installation/index.md) página.



## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>No se puede actualizar a Indie/Business de cuenta de prueba

Si ha adquirido recientemente Xamarin.Android e inició previamente una versión de prueba de Xamarin.Android, debe completar los pasos siguientes para obtener este cambio de licencia detectado por Visual Studio para Mac o en Visual Studio.

-  Cierre Visual Studio para Mac/Visual Studio
-  Quitar todos los archivos de ~/Library/MonoAndroid en Mac o %PROGRAMDATA%\Mono Android\License\ para Windows
-  Vuelva a abrir Visual Studio para Mac/Visual Studio y crear un proyecto Xamarin.Android


Esto debería obtener activos y en funcionamiento. Si sigue teniendo problemas, puede que desee intentar un [activación sin conexión](~/android/get-started/installation/index.md) para completar la activación de la estación de trabajo.



## <a name="receiving-activation-incomplete-error-message"></a>Recibir ' mensaje de Error incompleta de activación

Este problema puede producirse al usar Xamarin.Android para Visual Studio. Para resolver este problema, envíe los registros de la siguiente ubicación para *contact@xamarin.com*.

-  Ubicación del registro: **% LocalAppData %\\Xamarin\\registros**




## <a name="receiving-error-retrieving-update-information-error-message"></a>Recibir el mensaje de Error 'Error al recuperar información de actualización'

De vez en cuando, se producirá un error de una actualización con este error siguiente, que a menudo se producirá durante la comprobación de actualizaciones:

Una mayoría de las veces, este error se puede resolver simplemente por el registro de la cuenta de Xamarin y, a continuación, de nuevo en el registro.

Para lograr esto, averiguar la plataforma de elección a continuación y siga los pasos:

**En Mac:**
1. Abra Visual Studio para Mac
2. Seleccione Visual Studio para Mac > cuenta...
3. Haga clic en Cerrar sesión
4. Haga clic en Inicio de sesión
5. Escriba sus credenciales
6. Compruebe si hay actualizaciones

**En PC con Visual Studio:**
1. Apertura de Visual Studio
2. Seleccionar Herramientas > cuenta de Xamarin
3. Haga clic en Cerrar sesión
4. Haga clic en Inicio de sesión
5. Escriba sus credenciales
6. Compruebe si hay actualizaciones

Si sigue apareciendo este mensaje de error, envíe un correo electrónico **contact@xamarin.com**.




## <a name="android-debug-logs"></a>Registros de depuración de Android

El [Android registros](~/android/deploy-test/debugging/android-debug-log.md) puede proporcionar contexto adicional sobre los errores en tiempo de ejecución que está viendo.



## <a name="floating-point-performance-is-terrible"></a>¡Rendimiento de punto flotante es terrible!

O bien, "mi aplicación ejecuta 10 veces más rápido con la versión de depuración que con la versión de lanzamiento!"

Xamarin.Android es compatible con varios dispositivos ABIs: *armeabi*, *armeabi v7a*, y *x86*. Dispositivo ABIs se pueden especificar en **propiedades del proyecto > pestaña de la aplicación > admite arquitecturas**.

Compilaciones de depuración utilizan un paquete de Android que proporciona todas las ABIs y, por tanto, usará la ABI más rápida para el dispositivo de destino.

Versiones de lanzamiento solo incluirá el ABIs seleccionadas en la ficha de propiedades del proyecto. Puede seleccionar más de una.

*armeabi* es el valor predeterminado de la ABI y tiene la mayor compatibilidad de dispositivo. *Sin embargo*, armeabi no es compatible con los dispositivos con varias CPU y el hardware de punto flotante, amont otras cosas. Por lo tanto, aplicaciones con el tiempo de ejecución de la versión armeabi estará unidas a un solo núcleo y va a utilizar una implementación de software float. Ambos pueden contribuir al rendimiento considerablemente más lento de la aplicación.

Si su aplicación requiere un rendimiento aceptable punto flotante (por ejemplo, juegos), debe habilitar la *armeabi v7a* ABI. Desea sólo son compatibles con el *armeabi v7a* en tiempo de ejecución, aunque esto significa que los dispositivos más antiguos que sólo admiten *armeabi* podrá ejecutar la aplicación.



## <a name="could-not-locate-android-sdk"></a>No se pudo encontrar el SDK de Android

Existen 2 descargas de Google Android SDK para Windows.
Si decide que el instalador .exe, escribirá las claves del registro que indiquen Xamarin.Android donde se instaló. Si elige el archivo .zip y descomprímalo usted mismo, Xamarin.Android no sabe dónde buscar el SDK. Puede indicar Xamarin.Android donde es el SDK de Visual Studio yendo a **Herramientas > Opciones > Xamarin > configuración de Android**:

[![Ubicación de SDK de Android en la configuración de Xamarin para Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE no muestra los dispositivos de destino

A veces, intenta implementar la aplicación en un dispositivo, pero el dispositivo que desea implementar en no se muestra en el cuadro de diálogo Seleccionar dispositivo. Esto puede ocurrir cuando decida Android Debug Bridge ir de vacaciones.

Para diagnosticar este problema, busque el [adb programa](~/android/deploy-test/debugging/android-debug-log.md), a continuación, ejecute:

```shell
adb devices
```

Si el dispositivo no está presente, debe reiniciar el servidor de Android Debug Bridge para que se puede encontrar el dispositivo:

```shell
adb kill-server
adb start-server
```

Puede impedir que el software de sincronización HTC **adb start-server** funcione correctamente. Si el **adb start-server** comando no imprimirá el puerto que se está iniciando en, salga del programa de sincronización HTC e inténtelo de reiniciar el servidor adb.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>No se pudo ejecutar el ejecutable de la tarea especificada "keytool"

Esto significa que la ruta de acceso no contiene el directorio donde se encuentra directorio de bin del SDK de Java. Compruebe que ha seguido los pasos de la [instalación](~/android/get-started/installation/index.md) guía.


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe o aresgen.exe terminado con el código 1

Para ayudarle a depurar este problema, vaya a Visual Studio y cambiar el nivel de detalle de MSBuild, para ello, seleccione: **Herramientas > Opciones > proyecto** y **soluciones > compilar** y **ejecutar > Contenido de salida de compilación de proyecto de MSBuild** y establezca este valor en **Normal**.

Volver a generar y comprobar el panel de salida de Visual Studio, que debe contener el error completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>No hay suficiente espacio de almacenamiento en el dispositivo para implementar el paquete

Esto se produce cuando no inicia el emulador desde dentro de Visual Studio. Al iniciar el emulador fuera de Visual Studio, tiene que pasar el `-partition-size 512` opciones, p. ej.

```shell
emulator -partition-size 512 -avd MonoDroid
```

Asegúrese de utilizar el nombre correcto del simulador, es decir, [el nombre que usó al configurar el simulador](~/android/get-started/installation/windows.md#device).


## <a name="installfailedinvalidapk-when-installing-a-package"></a>INSTALAR\_error\_válido\_APK al instalar un paquete

Los nombres de paquetes Android *debe* contienen un punto ('*.*'). Edite el nombre del paquete para que contenga un punto.

-   En Visual Studio:
    -   Haga clic con el proyecto > Propiedades
    -   Haga clic en la ficha manifiesto de Android a la izquierda.
    -   Actualice el campo de nombre de paquete.
        -   Si ve el mensaje &ldquo;AndroidManifest.xml No encontrado. Haga clic para agregar uno. &rdquo;, haga clic en el vínculo y, a continuación, actualice el campo de nombre de paquete.
-   En Visual Studio para Mac:
    -   Haga clic con el proyecto > Opciones.
    -   Navegue a la compilación o la sección aplicación Android.
    -   Cambie el campo de nombre de paquete para que contenga un '.'.




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>INSTALAR\_error\_falta\_SHARED\_biblioteca al instalar un paquete

Una "biblioteca compartida" en este contexto es *no* una biblioteca compartida nativo (*libfoo.so*) de archivos; en su lugar, es una biblioteca que debe instalarse por separado en el dispositivo de destino, como Google Maps.

El paquete de Android especifica qué bibliotecas compartidas son necesarios con el `<uses-library/>` elemento. Si un *requiere* biblioteca no está presente en el dispositivo de destino (por ejemplo, `//uses-library/@android:required` es *true*, que es el valor predeterminado), se producirá un error de instalación del paquete con *instalar\_ No se pudo\_falta\_SHARED\_biblioteca*.

Para determinar qué bibliotecas compartidas son necesarios, vea el *genera*
**AndroidManifest.xml** archivo (por ejemplo, **obj\\depurar\\android \\AndroidManifest.xml**) y busque el `<uses-library/>` elementos. `<uses-library/>` elementos se pueden agregar manualmente en el proyecto **propiedades\\AndroidManifest.xml** archivo y a través de la [UsesLibraryAttribute de atributo personalizado](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/).

Por ejemplo, al agregar una referencia a ensamblado *Mono.Android.GoogleMaps.dll* agregará implícitamente un `<uses-library/>` para la biblioteca compartida de Google Maps.



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>INSTALAR\_error\_actualización\_INCOMPATIBLE al instalar un paquete

Paquetes de Android tienen tres requisitos:

-   Deben contener un '.' (vea la entrada anterior)
-   Deben tener un nombre de paquete de cadena única (por lo tanto, la convención de inversa tld vista en los nombres de aplicación de Android, por ejemplo, com.android.chrome para la aplicación de Chrome)
-   Al actualizar los paquetes, el paquete debe tener la misma clave de firma.

Por lo tanto, imagínese esta situación:

1.  Generar & implementar la aplicación como una aplicación de depuración
2.  Cambiar la clave de firma, por ejemplo, para usar como una aplicación de la versión (o porque no le gusta el valor predeterminado proporcionado por depurar clave de firma)
3.  Instalar la aplicación sin eliminarlo en primer lugar, por ejemplo, depurar > Iniciar sin depurar en Visual Studio


Cuando esto sucede, se producirá un error de instalación del paquete con una instalación\_error\_actualización\_error INCOMPATIBLE, porque el nombre del paquete no cambia mientras la firma clave ¿. El [el registro de depuración Android](~/android/deploy-test/debugging/android-debug-log.md) también contendrá un mensaje similar al:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Para corregir este error, quite por completo la aplicación del dispositivo antes de volver a instalar.


## <a name="installfaileduidchanged-when-installing-a-package"></a>INSTALAR\_error\_UID\_CAMBIADO al instalar un paquete

Cuando se instala un paquete de Android, se asigna un *Id. de usuario* (UID).
*A veces*, por razones desconocidas actualmente, cuando la instalación a través de una aplicación ya instalada, se producirá un error de la instalación con `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Para solucionar este problema, *desinstalar completamente* el paquete de Android, ya sea mediante la instalación de la aplicación de interfaz gráfica de usuario del destino Android, o mediante `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**No utilice** `adb uninstall -k`, así como esto *conservar* datos de la aplicación y, por tanto, conservar el UID en conflicto en el dispositivo de destino.



## <a name="release-apps-fail-to-launch-on-device"></a>Aplicaciones de la versión no se pudo iniciar en el dispositivo

La salida de registro de depuración de Android contendrá un mensaje similar al:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Si es así, hay dos causas posibles:

1.  El .apk no proporciona una ABI que admite el dispositivo de destino.
    Por ejemplo, el .apk sólo contiene los archivos binarios de armeabi v7a y el dispositivo de destino sólo admite armeabi.

2.  Un [error Android](http://code.google.com/p/android/issues/detail?id=21670). Si éste es el caso, desinstale la aplicación, entre los dedos y volver a instalar la aplicación.

Para corregir (1), editar las propiedades y opciones del proyecto y [agregar compatibilidad para la ABI necesaria a la lista de ABIs admite](~/android/app-fundamentals/cpu-architectures.md). Para determinar qué necesita agregar la ABI, ejecute el siguiente comando adb en el dispositivo de destino:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

La salida contendrá el elemento principal (y base de datos secundaria opcional) ABIs.

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La propiedad OutPath no está establecida para el proyecto &ldquo;MyApp.csproj&rdquo;

Por lo general, esto significa que tiene un equipo de HP y la variable de entorno &ldquo;plataforma&rdquo; se ha establecido en algo como MCD o HPD. Esto está en conflicto con la propiedad de MSBuild Platform que generalmente se establece en &ldquo;cualquier CPU&rdquo; o &ldquo;x86&rdquo;. Debe quitar esta variable de entorno de su equipo para que pueda funcionar MSBuild:

-   Panel de control > sistema > avanzado > Variables de entorno

Reinicie Visual Studio o Visual Studio para Mac e intente volver a generar. Ahora, todo debería funcionar según lo esperado.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject no se puede convertir a...

Xamarin.Android 4.x no correctamente calcular las referencias de tipos genéricos anidados correctamente. Por ejemplo, considere el siguiente C\# del código mediante [SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


El problema es que Xamarin.Android calcula correctamente las referencias de tipos genéricos anidados. El `List<IDictionary<string, object>>` referencias se calculan para una [java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/), pero la `ArrayList` contiene `mono.android.runtime.JavaObject` instancias (qué referencia el `Dictionary<string, object>` instancias) en lugar de algo que implementa [java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/), da lugar a la siguiente excepción:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

La solución consiste en usar proporcionado [tipos de colección de Java](~/android/internals/api-design.md) en lugar de la `System.Collections.Generic` tipos para la &ldquo;interna&rdquo; tipos. Esto provocará en tipos de Java adecuados al calcular referencias de las instancias. (El código siguiente es más complicado que es necesario para reducir la duración de gref. Se puede simplificar para modificar el código original a través de `s/List/JavaList/g` y `s/Dictionary/JavaDictionary/g` si gref duraciones no están una preocupación.)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Este problema se solucionará en una futura versión](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).


## <a name="unexpected-nullreferenceexceptions"></a>NullReferenceException inesperado

En ocasiones, el [Android Debug Log](~/android/deploy-test/debugging/android-debug-log.md) mencionarán excepciones NullReferenceException que &ldquo;no puede suceder,&rdquo; o proceder de Mono para el código en tiempo de ejecución Android poco antes de la aplicación se agote:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

o

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Esto puede ocurrir cuando el tiempo de ejecución Android decide anular el proceso, lo cual puede suceder por una serie de motivos, incluidos alcanzando el límite GREF del destino o hacer algo &ldquo;incorrecto&rdquo; con JNI.

Para ver si éste es el caso, compruebe el registro de depuración Android un mensaje de su proceso similar para:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Anular debidos al agotamiento de referencia Global

Capa JNI de Android runtime solo admite un número limitado de referencias a objetos JNI sea válida en un momento dado en el tiempo. Cuando se supera este límite, salto de cosas.

El GREF (*referencia global*) límite es 2000 referencias en el emulador y hace referencia a ~ 52000 en hardware.

Sabrá que comience a crear demasiadas GREFs cuando vea mensajes como este en el registro de depuración Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Cuando alcance el límite GREF, se imprime un mensaje como el siguiente:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


En el ejemplo anterior (que, a propósito, procede de [errores 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) el problema es que hay demasiados Android.Graphics.Point se están creando instancias; vea [comentario \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) para obtener una lista de correcciones para Este error concreto.

Por lo general, una solución útil es encontrar qué tipo tiene demasiadas instancias asignada &ndash; Android.Graphics.Point en el volcado de memoria anterior &ndash; , a continuación, busque donde se crean en el código fuente y dispose de ellos correctamente (por lo que sus Se acorta la duración de Java-Object). Esto no siempre es adecuado (\#685215 es multiproceso, por lo que la solución trivial evita la llamada a Dispose), pero es lo primero que tener en cuenta.

Puede habilitar [GREF registro](~/android/troubleshooting/index.md) para ver cuándo se crean GREFs y cuántos existen.


## <a name="abort-due-to-jni-type-mismatch"></a>Anulación debido a una falta de coincidencia de tipo JNI

Si se desarrollar código JNI, es posible que los tipos no coinciden con correctamente, por ejemplo, si se intenta invocar `java.lang.Runnable.run` en un tipo que no implementa `java.lang.Runnable`. Cuando esto ocurre, habrá un mensaje similar al siguiente en el registro de depuración Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Compatibilidad de código dinámico

### <a name="dynamic-code-does-not-compile"></a>No se compila código dinámico

Al usar C\# dinámicos en sus aplicaciones o la biblioteca, tiene que agregar System.Core.dll, Microsoft.CSharp.dll y Mono.CSharp.dll al proyecto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>En la versión de lanzamiento, MissingMethodException se produce para código dinámico en tiempo de ejecución.

-   Es probable que el proyecto de aplicación no tiene referencias a System.Core.dll, Microsoft.CSharp.dll o Mono.CSharp.dll. Asegúrese de que se hace referencia a esos ensamblados.

    -   Tenga en cuenta que el código dinámico siempre los costos. Si necesita un código eficaz, considere la posibilidad de no usa código dinámico.

-   En la primera vista previa, esos ensamblados se han excluido, a menos que explícitamente se utilizan tipos de cada ensamblado por el código de aplicación. Para solucionar este problema, consulte: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Los proyectos compilados con bloqueo AOT + LLVM en x86 dispositivos

Al implementar una aplicación compilada con [AOT + LLVM](~/android/deploy-test/release-prep/index.md) en dispositivos x86, verá un mensaje de error de excepción similar al siguiente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Se trata de un problema conocido como se notifica en [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). La solución consiste en deshabilitar LLVM.
