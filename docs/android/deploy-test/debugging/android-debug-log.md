---
title: "Registro de depuración de Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 99b9ed9e3c71766f483f7b00996137aae7a247d1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="android-debug-log"></a>Registro de depuración de Android

## <a name="android-debug-log-overview"></a>Información general sobre el registro de depuración de Android

Un truco muy común que emplean los desarrolladores para depurar sus aplicaciones es realizar llamadas a `Console.WriteLine`. Pero en una plataforma móvil como Android no hay consola. Los dispositivos Android proporcionan un registro que puede usar al escribir aplicaciones. Este registro se conoce a veces como "logcat" debido al comando escrito para recuperarlo.

## <a name="accessing-from-visual-studio"></a>Acceso desde Visual Studio

En Visual Studio 2015 y posteriores, los paneles de registro de iOS y Android están unificados.

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 y 2017

La nueva ventana de herramienta Registro de dispositivos para Visual Studio muestra los registros de dispositivos iOS y Android. Se puede mostrar mediante la ejecución de cualquiera de los siguientes comandos: 

-   **Ver > Otras ventanas > Registro de dispositivos**
-   **Herramientas -> Android -> Registro de dispositivos**
-   **Barra de herramientas de Android -> Registro de dispositivos**

Cuando aparezca la ventana de herramientas, el dispositivo físico puede seleccionarse en el cuadro combinado de dispositivos. Cuando se selecciona el dispositivo, comienza automáticamente a agregar las entradas de registro de una aplicación en ejecución en la tabla. Cambiar entre dispositivos detendrá e iniciará el registro de dispositivos. Para que los dispositivos aparezcan en el cuadro combinado, se debe cargar un proyecto Android. Si el dispositivo no aparece en el cuadro combinado, compruebe primero si está disponible en la lista desplegable Start Debug (Empezar a depurar). 

Esta ventana de herramientas proporciona: una tabla de entradas de registro, un cuadro combinado de selección de dispositivos, una forma de borrar las entradas de registro, un cuadro de búsqueda y los botones reproducir, detener y pausar. 


<a name="Accessing_from_the_Command_Line" />

## <a name="accessing-from-the-command-line"></a>Acceso desde la línea de comandos

Otra opción para ver el registro de depuración es a través de la línea de comandos. Abra una ventana de consola y navegue hasta la carpeta de herramientas de la plataforma del SDK de Android (que, por ejemplo, puede tener la ruta **C:\android-sdk-windows\platform-tools**). 

Si solo hay un dispositivo conectado, se puede ver el registro con:

```shell
$ adb logcat
```

Si está conectado a más de un dispositivo, se debe identificar el dispositivo. Por ejemplo, `adb -d logcat` muestra el registro del único dispositivo físico conectado, mientras que `adb -e logcat` muestra el registro del único emulador que se ejecuta. 

Puede encontrar más comandos simplemente ejecutando **adb**.

<a name="Writing_to_the_Debug_Log" />


## <a name="writing-to-the-debug-log"></a>Escribir en el registro de depuración

Los mensajes pueden escribirse en el registro de depuración usando los métodos de la clase [Android.Util.Log](https://developer.xamarin.com/api/type/Android.Util.Log/): 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Esto produce:

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

<a name="Interesting_Messages" />

## <a name="interesting-messages"></a>Mensajes interesantes

Al leer el registro y especialmente al proporcionar fragmentos de registro a otros usuarios (ya que proporcionar el archivo de registro completo (1) resultaría una exageración y (2) no sería legible), la línea para comenzar *más importante* es una línea similar a esta:

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

En concreto, una línea que coincida con la expresión regular:

```shell
^I.*ActivityManager.*Starting: Intent
```

y que contenga el nombre del paquete de aplicación. Esta es la línea que corresponde al inicio de una actividad, y la *mayoría* de los mensajes siguientes (pero no todos) deberían estar relacionados con la aplicación. 

En concreto, cada mensaje que contenga el identificador de proceso (pid) del proceso que genera el mensaje. En el mensaje de `ActivityManager` anterior, el proceso `12944` generó el mensaje. Para determinar qué proceso es el proceso de la aplicación que se está depurando, busque el mensaje mono.MonoRuntimeProvider: 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Este mensaje procede del proceso que se inició y todos los mensajes siguientes que contienen el mismo pid proceden del mismo proceso. 
