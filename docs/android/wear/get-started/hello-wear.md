---
title: Hola, desgaste
description: Crear su primera aplicación Android desgaste y ejecutarlo en un dispositivo o emulador de uso. Este tutorial proporciona instrucciones paso a paso para crear un proyecto pequeño desgaste Android que administra los clics de botón y muestra un contador de hacer clic en el dispositivo de uso. Se explica cómo depurar la aplicación mediante un emulador desgaste o un dispositivo de uso que está conectado a través de Bluetooth a un teléfono Android. También proporciona un conjunto de sugerencias de depuración para desgaste Android.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 17c12c4ec818c21d6697932315874ea4f63e6109
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="hello-wear"></a>Hola, desgaste

_Crear su primera aplicación Android desgaste y ejecutarlo en un dispositivo o emulador de uso. Este tutorial proporciona instrucciones paso a paso para crear un proyecto pequeño desgaste Android que administra los clics de botón y muestra un contador de hacer clic en el dispositivo de uso. Se explica cómo depurar la aplicación mediante un emulador desgaste o un dispositivo de uso que está conectado a través de Bluetooth a un teléfono Android. También proporciona un conjunto de sugerencias de depuración para desgaste Android._

![Captura de pantalla de la aplicación de uso se complete en este tutorial](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>La primera aplicación de uso

Siga estos pasos para crear su primera aplicación de uso de Xamarin.Android:

### <a name="1-create-a-new-android-project"></a>1. Cree un nuevo proyecto de Android

Crear un nuevo **aplicación Android desgaste**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Crear una nueva aplicación Android desgaste en el cuadro de diálogo nuevo proyecto](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Crear una nueva aplicación Android desgaste en el cuadro de diálogo nueva solución](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Esta plantilla se incluye automáticamente el **Xamarin Android Wearable biblioteca** NuGet (y dependencias) por lo que tendrá acceso a los widgets de uso específicos. Si no ve la plantilla de desgaste, revise la [instalación y configuración](~/android/wear/get-started/installation.md) guía para comprobar que ha instalado un SDK de Android compatibles. 

### <a name="2-choose-the-correct-target-framework"></a>2. Elija el valor correcto **.NET Framework de destino**

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Asegúrese de que **mínimo Android al destino** está establecido en **Android 5.0 (círculo)** o una versión posterior: 

[![Establecer la plataforma de destino para Android 5.0 en Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Asegúrese de que la plataforma de destino se establece en **Android 5.0 (círculo)** o una versión posterior:

[![Establecer la plataforma de destino para Android 5.0 en Visual Studio para Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Para obtener más información acerca de cómo establecer la plataforma de destino, vea [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Editar la **Main.axml** diseño

Configurar el diseño para que contenga un `TextView` y un `Button` del ejemplo: 

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

### <a name="4-edit-the-mainactivitycs-source"></a>4. Editar la **MainActivity.cs** origen

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

El siguiente paso es configurar un emulador o dispositivo para implementar y ejecutar la aplicación. Si no está familiarizado con el proceso de implementar y ejecutar aplicaciones de Xamarin.Android en general, vea el [Hello, Android Quickstart](~/android/get-started/hello-android/hello-android-quickstart.md).

Si no tiene un dispositivo Android desgaste como un Smartwatch desgaste Android, puede ejecutar la aplicación en un emulador. Para obtener información sobre la depuración de aplicaciones de uso en un emulador, consulte [depurar Android desgaste en un emulador](~/android/wear/deploy-test/debug-on-emulator.md).

Si tiene un dispositivo Android desgaste como un Smartwatch desgaste Android, puede ejecutar la aplicación en el dispositivo en lugar de utilizar un emulador. Para obtener más información sobre la depuración en un dispositivo de uso, consulte [depurar en un dispositivo desgaste](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Ejecutar la aplicación de uso de Android

El dispositivo Android desgaste debe aparecer en el menú desplegable de dispositivo. Asegúrese de elegir el dispositivo Android desgaste correcto o AVD antes de iniciar la depuración. Después de seleccionar el dispositivo, haga clic en el botón Reproducir para implementar la aplicación en el emulador o dispositivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Elegir un AVD desgaste en el menú del dispositivo de Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Elegir un AVD desgaste en Visual Studio para el menú del dispositivo de Mac](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Es posible que vea una **tan sólo un minuto...**  mensaje (o alguna otra pantalla intersticial) al principio: 

![Ver el emulador muestra tan sólo un minuto...](hello-wear-images/please-wait.png)

Si está utilizando un emulador de inspección, puede tardar varios minutos en iniciarse la aplicación. Cuando usas Bluetooth, tarda más tiempo para implementar la aplicación a como lo haría a través de USB. (Por ejemplo, tarda aproximadamente 5 minutos para implementar esta aplicación en un Watch G LG que está conectado de Bluetooth en un teléfono Nexus 5.)

Después de que la aplicación se implementa correctamente, la pantalla del dispositivo desgaste debe mostrar una pantalla similar al siguiente:

[![Pantalla inicial de la aplicación de uso](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Pulse la **¡CLICK ME!** botón en el dispositivo de desgaste y vea el incremento del recuento con cada derivación:

[![Captura de pantalla de desgaste aplicación después de 3 clics](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Pasos siguientes

Extraer del repositorio el [desgaste ejemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) incluidas desgaste Android las aplicaciones con aplicaciones de Phone complementario.

Cuando esté preparado para distribuir la aplicación, consulte [trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Vínculos relacionados

- [Haga clic en mi aplicación (ejemplo)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
