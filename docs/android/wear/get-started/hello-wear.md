---
title: Hola, Wear
description: Cree su primera aplicación Android Wear y ejecútelo en un dispositivo o emulador desgaste. Este tutorial proporciona instrucciones paso a paso para crear un pequeño proyecto de Android Wear que administra los clics de botón y muestra un contador haga clic en el dispositivo de desgaste. Explica cómo depurar la aplicación mediante un emulador de desgaste o un dispositivo Wear que está conectado a través de Bluetooth a un teléfono Android. También se proporciona un conjunto de sugerencias de depuración para Android Wear.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: a8e27063040ff91f72a1cbf932b1b277a5dee63d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277453"
---
# <a name="hello-wear"></a>Hola, Wear

_Cree su primera aplicación Android Wear y ejecútelo en un dispositivo o emulador desgaste. Este tutorial proporciona instrucciones paso a paso para crear un pequeño proyecto de Android Wear que administra los clics de botón y muestra un contador haga clic en el dispositivo de desgaste. Explica cómo depurar la aplicación mediante un emulador de desgaste o un dispositivo Wear que está conectado a través de Bluetooth a un teléfono Android. También se proporciona un conjunto de sugerencias de depuración para Android Wear._

![Captura de pantalla de la aplicación Wear realizarse en este tutorial](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>La primera aplicación Wear

Siga estos pasos para crear su primera aplicación de desgaste de Xamarin.Android:

### <a name="1-create-a-new-android-project"></a>1. Cree un nuevo proyecto de Android

Cree un nuevo **aplicación Android Wear**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Crear una nueva aplicación Android Wear en el cuadro de diálogo nuevo proyecto](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Crear una nueva aplicación Android Wear en el cuadro de diálogo nueva solución](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Esta plantilla incluye automáticamente el **biblioteca ponibles Android de Xamarin** NuGet (y las dependencias) para tener acceso a los widgets de desgaste específicos. Si no ve la plantilla de desgaste, revise el [instalación y configuración](~/android/wear/get-started/installation.md) guía para comprobar que ha instalado un SDK de Android compatible. 

### <a name="2-choose-the-correct-target-framework"></a>2. Elija el valor correcto **.NET Framework de destino**

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Asegúrese de que **Android mínima de destino** está establecido en **Android 5.0 (Lollipop)** o una versión posterior: 

[![Si se establece la plataforma de destino en Android 5.0 en Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Asegúrese de que la plataforma de destino se establece en **Android 5.0 (Lollipop)** o una versión posterior:

[![Configuración de la plataforma de destino a Android 5.0 en Visual Studio para Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Para obtener más información sobre cómo establecer la plataforma de destino, vea [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Editar el **Main.axml** diseño

Configurar el diseño para contener un `TextView` y un `Button` del ejemplo: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. Editar el **MainActivity.cs** origen

Agregue el código para incrementar un contador y lo muestra cada vez que se hace clic en el botón: 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. Programa de instalación de un dispositivo o emulador

El siguiente paso es configurar un emulador o dispositivo para implementar y ejecutar la aplicación. Si todavía no está familiarizado con el proceso de implementar y ejecutar aplicaciones de Xamarin.Android en general, vea el [Hello, Android Quickstart](~/android/get-started/hello-android/hello-android-quickstart.md).

Si no tiene un dispositivo Android Wear, como un Smartwatch Android Wear, puede ejecutar la aplicación en un emulador. Para obtener información sobre la depuración de aplicaciones de desgaste en un emulador, consulte [depurar Android Wear en un emulador](~/android/wear/deploy-test/debug-on-emulator.md).

Si tiene un dispositivo Android Wear, como un Smartwatch Android Wear, puede ejecutar la aplicación en el dispositivo en lugar de usar un emulador. Para obtener más información sobre la depuración en un dispositivo Wear, consulte [depurar en un dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Ejecute la aplicación Android Wear

El dispositivo Android Wear debe aparecer en el menú desplegable de dispositivo. Asegúrese de elegir el dispositivo Android Wear correcto o AVD antes de iniciar la depuración. Después de seleccionar el dispositivo, haga clic en el botón Reproducir para implementar la aplicación en el emulador o dispositivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Elegir un AVD Wear en el menú del dispositivo de Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Elegir un AVD Wear en Visual Studio para el menú de dispositivos de Mac](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Es posible que vea un **tan sólo un minuto...**  mensaje (o alguna otra pantalla intersticial) al principio: 

![Ver el emulador muestra sólo un minuto...](hello-wear-images/please-wait.png)

Si usa un emulador de inspección, puede tardar un tiempo en iniciarse la aplicación. Cuando usas Bluetooth, tarda más tiempo para implementar la aplicación a como lo haría a través de USB. (Por ejemplo, tarda aproximadamente 5 minutos para implementar esta aplicación en un Watch G LG que está conectado de Bluetooth a un teléfono Nexus 5.)

Después de la aplicación se implementa correctamente, la pantalla del dispositivo Wear debe mostrar una pantalla similar a lo siguiente:

[![Pantalla inicial de la aplicación Wear](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Pulse el **¡CLICK ME!** botón en el dispositivo Wear y vea con cada vez que toque el incremento del recuento:

[![Captura de pantalla de desgaste de la aplicación después de 3 clics](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Pasos siguientes

Consulte la [Wear ejemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) , incluidas las aplicaciones de Android Wear con aplicaciones de teléfono complementarias.

Cuando esté listo para distribuir la aplicación, consulte [trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Vínculos relacionados

- [Haga clic en mi aplicación (ejemplo)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
