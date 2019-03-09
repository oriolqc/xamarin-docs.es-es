---
title: Descripción de los niveles de API de Android
description: Xamarin.Android tiene varias configuraciones de nivel de API de Android que determinan la compatibilidad de la aplicación con varias versiones de Android. Esta guía explica lo que significan estos valores, cómo configurarlos y qué efecto tienen en su aplicación en tiempo de ejecución.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 8690be7551046a26339f58029da5f3f58e18cd15
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672591"
---
# <a name="understanding-android-api-levels"></a>Descripción de los niveles de API de Android

_Xamarin.Android tiene varias configuraciones de nivel de API de Android que determinan la compatibilidad de la aplicación con varias versiones de Android. Esta guía explica lo que significan estos valores, cómo configurarlos y qué efecto tienen en su aplicación en tiempo de ejecución._


## <a name="quick-start"></a>Inicio rápido

Xamarin.Android expone tres configuraciones de proyecto de nivel de API de Android:

-   [Marco de destino](#framework) &ndash; especifica que desea utilizar para generar la aplicación de .NET framework. Este nivel de API se usa en *compilar* tiempo xamarin.Android.

-   [Versión mínima de Android](#minimum) &ndash; especifica la versión de Android más antigua que desea que la aplicación para admitir. Este nivel de API se usa en *ejecutar* tiempo por Android.

-   [Versión de Android de destino](#target) &ndash; especifica la versión de Android que la aplicación está diseñada para ejecutarse. Este nivel de API se usa en *ejecutar* tiempo por Android.

Antes de configurar un nivel de API para el proyecto, debe instalar los componentes de platform SDK para ese nivel de API. Para obtener más información sobre cómo descargar e instalar los componentes de Android SDK, consulte [instalación de Android SDK](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partir de agosto de 2018, la consola de Google Play requerirá que nuevas aplicaciones de destino de nivel de API 26 (Android 8.0) o superior.
Las aplicaciones existentes deberán tener como destino el nivel de API 26 o superior a partir de noviembre de 2018. Para obtener más información, consulte [mejorar la seguridad de la aplicación y el rendimiento en Google Play durante años venideros](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Normalmente, los tres niveles de API de Xamarin.Android se establecen en el mismo valor. En el **aplicación** , establezca **compilar con la versión de Android (.NET Framework de destino)** a la última versión estable de API (o, como mínimo, para la versión de Android que tiene todas las características que necesita).
En la siguiente captura de pantalla, la plataforma de destino se establece en **Android 7.1 (API nivel 25 - Nougat)**:

[![Valores predeterminados de la versión de Framework a compilar con la versión Android de destino](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

En el **manifiesto de Android** página, establezca la versión Android mínima en **uso compilar con la versión SDK** y establezca la versión Android de destino en el mismo valor que la versión de .NET Framework de destino (en el siguiente captura de pantalla, la plataforma Android de destino se establece en **Android 7.1 (Nougat)**):

[![Las versiones mínima y Android de destino establecen en la versión de .NET Framework de destino](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Si desea mantener la compatibilidad con una versión anterior de Android, establezca **versión Android mínima de destino** a la versión más antigua de Android que desea que la aplicación para admitir. (Tenga en cuenta que el 14 de nivel de API es el nivel de API mínimo requerido para [Google Play services y soporte técnico de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) El siguiente ejemplo de configuración admite Android versiones de nivel de API 14 a través de nivel de API 25:

[![Compilar con el nivel de API 25 Nougat, versión Android mínima establecida en el nivel de API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, los tres niveles de API de Xamarin.Android se establecen en el mismo valor. Establecer **.NET framework de destino** a la última versión estable de API (o, como mínimo, para la versión de Android que tiene todas las características que necesita). Para establecer el **.NET framework de destino**, vaya a **compilar > General** en el **opciones de proyecto**. En la siguiente captura de pantalla, la plataforma de destino se establece en **usar la última plataforma instalada (8.0)**:

[![De forma predeterminada para usar la plataforma instalada más reciente de .NET framework de destino](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

La configuración de versión mínimo y Android de destino puede encontrarse en **compilar > aplicación de Android** en **opciones de proyecto**. Establezca la versión Android mínima en **automático: use la versión de framework de destino** y establezca la versión Android de destino en el mismo valor que la versión de .NET Framework de destino. En la siguiente captura de pantalla, la plataforma Android de destino se establece en **Android 8.0 (API nivel 26)** para que coincida con la configuración de .NET Framework de destino anterior:

[![Establecer los niveles de destino y el marco de trabajo en las opciones de proyecto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Si desea mantener la compatibilidad con una versión anterior de Android, cambiar **versión Android mínima** a la versión más antigua de Android que desea que la aplicación para admitir. Tenga en cuenta que el 14 de nivel de API es el nivel de API mínimo requerido para [Google Play services y soporte técnico de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Por ejemplo, la siguiente configuración es compatible con las versiones de Android tan pronto como el nivel de API 14:

[![Como mínimo y las versiones de destino establecidas en automático - usar la versión de framework de destino](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


Si la aplicación admite varias versiones de Android, el código debe incluir comprobaciones en tiempo de ejecución para asegurarse de que la aplicación funciona con la configuración de la versión Android mínima (consulte [comprueba en tiempo de ejecución para las versiones de Android](#runtimechecks) a continuación para obtener más información). Si va a consumir o crear una biblioteca, consulte [los niveles de API y bibliotecas](#libraries) debajo de los procedimientos recomendados en la configuración de la API de nivel configuración para las bibliotecas.



## <a name="android-versions-and-api-levels"></a>Las versiones de Android y niveles de API

A medida que evoluciona la plataforma Android y se publican nuevas versiones de Android, cada versión de Android se asigna un identificador entero único, denominado el *nivel de API*. Por lo tanto, cada versión de Android corresponde a un solo nivel de API de Android. Dado que los usuarios instalar aplicaciones en las versiones anteriores, así como más recientes de Android, Android aplicaciones del mundo real deben diseñarse para trabajar con varios niveles de API de Android.


### <a name="android-versions"></a>Versiones de Android

Cada versión de Android pasa por varios nombres:

-   La versión de Android, como **9.0 Android**
-   Un código (o postre) el nombre, como _circular_
-   Nivel de una API correspondiente, como **28 de nivel de API**

Un nombre de código Android puede corresponder a varias versiones y los niveles de API (tal y como se muestra en la tabla siguiente), pero cada versión de Android corresponde exactamente a un nivel de API.

Además, define Xamarin.Android *códigos de versión de compilación* que se asignan a los niveles de API de Android conocidos actualmente. En la tabla siguiente puede ayudarle a traducir entre el nivel de API, versión de Android, con nombre en código y código de versión de compilación de Xamarin.Android (códigos de versión de compilación se definen en el `Android.OS` espacio de nombres):

[!include[](~/android/includes/api-levels.md)]

Como se indica en esta tabla, se publican con frecuencia nuevas versiones de Android &ndash; a veces más de una versión al año. Como resultado, se incluye el universo de dispositivos Android que podría ejecutar la aplicación de una amplia variedad de versiones de Android anteriores y recientes. ¿Cómo puede garantizar que la aplicación se ejecutará de forma coherente y confiable en muchas versiones diferentes de Android? Los niveles de API de Android pueden ayudarle a administrar este problema.


### <a name="android-api-levels"></a>Niveles de API de Android

Cada dispositivo Android se ejecuta en exactamente *una* nivel de API &ndash; este nivel de API se garantiza que sea único para cada versión de la plataforma Android. El nivel de API identifica de forma precisa la versión del conjunto de API que puede llamar la aplicación. identifica la combinación de elementos del manifiesto, permisos, etc. código contra como desarrollador. Sistema de Android de los niveles de API ayuda a Android a determinar si una aplicación es compatible con una imagen de sistema Android antes de instalar la aplicación en un dispositivo.

Cuando se compila una aplicación, contiene la siguiente información de nivel de API:

-   El *destino* nivel de API de Android que se compiló la aplicación para ejecutarse en.

-   El *mínimo* nivel de API de Android que debe tener un dispositivo Android para ejecutar la aplicación. 

Estos valores se usan para garantizar que la funcionalidad necesaria para ejecutar correctamente la aplicación está disponible en el dispositivo Android en tiempo de instalación. Si no es así, la aplicación se bloqueó su ejecución en ese dispositivo. Por ejemplo, si el nivel de API de un dispositivo Android es inferior al nivel mínimo de API que especifique para la aplicación, el dispositivo Android impedirá al usuario instalar la aplicación.


## <a name="project-api-level-settings"></a>Configuración del nivel de proyecto de API

Las siguientes secciones explican cómo usar SDK Manager para preparar el entorno de desarrollo para los niveles de API que desee establecer como destino, seguido de una explicación detallada de cómo configurar *.NET Framework de destino*, *mínimo Versión de Android*, y *versión Android de destino* configuración en Xamarin.Android.


### <a name="android-sdk-platforms"></a>Plataformas de Android SDK

Para poder seleccionar un nivel de API mínima o de destino en Xamarin.Android, debe instalar la versión de la plataforma Android SDK que corresponde a ese nivel de API. El intervalo de opciones disponibles para .NET Framework de destino, la versión Android mínima y versión Android de destino se limita a las versiones de intervalo de Android SDK que ha instalado. Puede usar el Administrador de SDK para comprobar que se instalan las versiones necesarias de Android SDK, y puede usarlo para agregar los nuevos niveles de API que necesita para la aplicación. Si no está familiarizado con la instalación de los niveles de API, consulte [instalación de Android SDK](~/android/get-started/installation/android-sdk.md).

<a name="framework" />

### <a name="target-framework"></a>Versión de .NET Framework de destino

El *.NET Framework de destino* (también conocido como `compileSdkVersion`) es la versión de la plataforma Android específica (nivel de API) que se compila la aplicación de en tiempo de compilación. Esta configuración especifica las API que la aplicación *espera* que se usará cuando se ejecuta, pero no tiene ningún efecto en el que las API están realmente disponibles para la aplicación cuando se instala. Como resultado, cambiar la configuración de .NET Framework de destino no cambia el comportamiento de tiempo de ejecución.

La plataforma de destino identifica qué versiones de la biblioteca está vinculado a la aplicación contra &ndash; esta configuración determina qué API que puede usar en la aplicación. Por ejemplo, si desea usar el [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método que se introdujo en Android Lollipop 5.0, debe establecer la plataforma de destino en **API nivel 21 (Lollipop)** o una versión posterior. Si establece de .NET Framework su proyecto de destino a una API nivel como **API nivel 19 (KitKat)** y tratar de llamar a la `SetCategory` método en el código, obtendrá un error de compilación.

Se recomienda que siempre compilar con la *más reciente* versión de .NET Framework de destino disponible. Si lo hace, proporciona mensajes de advertencia útiles para cualquier API en desuso que podría llamarse mediante su código. Con la última versión de .NET Framework de destino es especialmente importante cuando se usa en las últimas versiones de la biblioteca de soporte técnico &ndash; cada biblioteca espera que su aplicación esté compilado en el nivel de API mínimo de la biblioteca de soporte técnico o superior. 


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para obtener acceso a la configuración de .NET Framework de destino en Visual Studio, abra las propiedades del proyecto en **el Explorador de soluciones** y seleccione el **aplicación** página:

[![Página de aplicación de las propiedades del proyecto](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Establezca la plataforma de destino seleccionando un nivel de API en el menú desplegable bajo **compilar con la versión Android** como se indicó anteriormente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para obtener acceso a la configuración de .NET Framework de destino en Visual Studio para Mac, haga clic en el nombre del proyecto y seleccione **opciones**; este se abre el **opciones de proyecto** cuadro de diálogo. En este cuadro de diálogo, vaya a **compilar > General** como se muestra aquí:

[![Sección General de la página de opciones de proyecto de compilación](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Establezca la plataforma de destino seleccionando un nivel de API en el menú desplegable situado a la derecha del **.NET framework de destino** como se indicó anteriormente.

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Versión mínima de Android

El *versión Android mínima* (también conocido como `minSdkVersion`) es la versión más antigua del SO Android (es decir, el menor nivel de API) que puede instalar y ejecutar la aplicación. De forma predeterminada, una aplicación solo se puede instalar en dispositivos que coincide con la configuración de .NET Framework de destino o superior; Si la configuración de la versión Android mínima es *inferior* a la configuración de .NET Framework de destino, también puede ejecutar la aplicación en versiones anteriores de Android. Por ejemplo, si establece la plataforma de destino en **Android 7.1 (Nougat)** y establezca la versión Android mínima en **Android 4.0.3 (Ice Cream Sandwich)**, la aplicación puede instalarse en cualquier plataforma de nivel de API 15 a nivel de API 25, ambos inclusive.

Aunque la aplicación puede generar e instalar en esta amplia variedad de plataformas correctamente, esto no garantiza que realizará correctamente *ejecutar* en todas estas plataformas. Por ejemplo, si la aplicación se instala en **Android 5.0 (Lollipop)** y el código llama a una API que solo está disponible en **Android 7.1 (Nougat)** y versiones más recientes, obtendrá un error en tiempo de ejecución y posiblemente bloquearse la aplicación. Por lo tanto, el código debe garantizar &ndash; en tiempo de ejecución &ndash; que llama a solamente aquellas API que son compatibles con el dispositivo Android que se ejecuta en. En otras palabras, el código debe incluir comprobaciones en tiempo de ejecución explícita para asegurarse de que la aplicación usa las API más recientes solo en dispositivos que son lo suficientemente recientes como para admitirlos.
[En tiempo de ejecución busca versiones de Android](#runtimechecks), más adelante en esta guía se explica cómo agregar estas comprobaciones en tiempo de ejecución en el código.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para obtener acceso a la configuración de la versión Android mínima en Visual Studio, abra las propiedades del proyecto en **el Explorador de soluciones** y seleccione el **manifiesto de Android** página. En el menú desplegable bajo **versión Android mínima** puede seleccionar la versión Android mínima para la aplicación:

[![Android mínimo para el destino conjunto de opciones para compilar con la versión SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Si selecciona **uso compilar con la versión SDK**, la versión Android mínima será el mismo que la configuración de .NET Framework de destino.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para obtener acceso a la versión Android mínima en Visual Studio para Mac, haga clic en el nombre del proyecto y seleccione **opciones**; este se abre el **opciones de proyecto** cuadro de diálogo. Vaya a **compilar > aplicación de Android**.
Mediante el menú desplegable a la derecha del **versión Android mínima**, puede establecer la versión Android mínima de la aplicación:

[![Versión mínima de Android se establece en automático: use la versión de framework de destino](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Si selecciona **automática &ndash; usar la versión de target framework**, la versión Android mínima será el mismo que la configuración de .NET Framework de destino.

-----


<a name="target" />

### <a name="target-android-version"></a>Versión de Android de destino

El *versión Android de destino* (también conocido como `targetSdkVersion`) es la API de nivel del dispositivo Android que espera ejecutar la aplicación. Android usa esta configuración para determinar si deben habilitarse comportamientos de compatibilidad &ndash; Esto garantiza que la aplicación continúa funcionando según lo previsto. Android usa la configuración de la versión Android de destino de la aplicación para averiguar qué cambios de comportamiento se pueden aplicar a la aplicación sin que ello interrumpa (Esto es cómo Android proporciona compatibilidad con versiones posteriores).

La plataforma de destino y la versión Android de destino, al tiempo que tiene nombres muy similares, no son lo mismo. La configuración de .NET Framework de destino comunica la información de nivel de API de destino para Xamarin.Android para su uso en *tiempo de compilación*, mientras que la versión Android de destino comunica la información de nivel de API de destino a Android para su uso en  *tiempo de ejecución* (cuando la aplicación está instalado y ejecutándose en un dispositivo).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para obtener acceso a esta configuración en Visual Studio, abra las propiedades del proyecto en **el Explorador de soluciones** y seleccione el **manifiesto de Android** página. En el menú desplegable bajo **versión Android de destino** puede seleccionar la versión Android de destino para la aplicación:

[![Versión de Android de destino establecido en compilar con la versión SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

Se recomienda que establezca explícitamente la versión Android de destino a la versión más reciente de Android que se usa para probar la aplicación. Idealmente, debe establecerse para la versión más reciente del SDK de Android &ndash; Esto le permite usar nuevas API antes de trabajar a través de los cambios de comportamiento. Para la mayoría de los desarrolladores, nos *no* recomienda establecer la versión Android de destino en **uso compilar con la versión SDK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para obtener acceso a esta configuración en Visual Studio para Mac, haga clic en el nombre del proyecto y seleccione **opciones**; este se abre el **opciones de proyecto** cuadro de diálogo. Vaya a **compilar > aplicación de Android**. Mediante el menú desplegable a la derecha del **versión Android de destino**, puede establecer la versión Android de destino para la aplicación:

[![Versión de Android de destino establecido en automático: use la versión de framework de destino](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

Se recomienda que establezca explícitamente la versión Android de destino a la versión más reciente de Android que se usa para probar la aplicación. Idealmente, debe establecerse para la versión de Android SDK más reciente disponible &ndash; Esto le permite usar nuevas API antes de trabajar a través de los cambios de comportamiento. Para la mayoría de los desarrolladores, no se recomienda establecer la versión Android de destino en **automático: use la versión de framework de destino**.

-----

En general, la versión de Android de destino deben estar limitada por la versión mínima de Android y la plataforma de destino. Es decir:

**Versión mínima de Android < = versión de Android de destino < = .NET Framework de destino**

Para obtener más información acerca de los niveles SDK, consulte el desarrollador Android [usa sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) documentación.


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>En tiempo de ejecución busca versiones de Android

Como cada versión nueva de Android, el marco de API se actualiza para proporcionar nuevas o la funcionalidad de reemplazo. Con pocas excepciones, funcionalidad de API de versiones anteriores de Android se mantiene en las versiones más recientes de Android sin modificaciones. Como resultado, si la aplicación se ejecuta en un nivel determinado de la API de Android, normalmente podrá ejecutar en un nivel de API de Android más adelante sin modificaciones. Pero ¿qué ocurre si también desea ejecutar la aplicación en versiones anteriores de Android?

Si selecciona una versión de Android mínima *inferior* a la configuración de .NET Framework de destino, algunas API no puede estar disponible para la aplicación en tiempo de ejecución. Sin embargo, todavía puede ejecutar la aplicación, en un dispositivo anterior, pero con funcionalidad reducida. Para cada API que no está disponible en las plataformas Android correspondiente a la configuración de la versión Android mínima, el código debe comprobar explícitamente el valor de la `Android.OS.Build.VERSION.SdkInt` propiedad para determinar el nivel de API de la plataforma de la aplicación se ejecuta en. Si el nivel de API es *inferior* a la versión Android mínima que es compatible con la API que desea llamar y, después, el código tiene que encontrar una manera para funcionar correctamente sin realizar esta llamada API.

Por ejemplo, supongamos que queremos usar el [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método para clasificar una notificación cuando se ejecuta en **Android 5.0 Lollipop** (y versiones posteriores), pero todavía queremos que nuestra aplicación para ejecutar en versiones anteriores de Android como **Android 4.1 Jelly Bean** (donde `SetCategory` no está disponible). Que hace referencia a la tabla de la versión de Android al principio de esta guía, vemos que el código de versión de compilación para **Android 5.0 Lollipop** es `Android.OS.BuildVersionCodes.Lollipop`. Compatibilidad con versiones anteriores de Android dónde `SetCategory` es no está disponible, el código puede detectar el nivel de API en tiempo de ejecución y llamar condicionalmente `SetCategory` solo cuando el nivel de API es mayor o igual que el código de versión de compilación de círculo:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

En este ejemplo, de .NET Framework nuestra aplicación de destino se establece en **Android 5.0 (API nivel 21)** y su versión Android mínima se establece en **Android 4.1 (nivel de API 16)**. Dado que `SetCategory` está disponible en el nivel de API `Android.OS.BuildVersionCodes.Lollipop` y versiones posteriores, llamará a este código de ejemplo `SetCategory` solo cuando está realmente disponible &ndash; lo hará *no* intenta llamar a `SetCategory` cuando la API nivel es 16, 17, 18, 19 o 20. La funcionalidad se reduce en estas versiones de Android anteriormente solo en la medida que las notificaciones no están ordenadas correctamente (porque no se clasifican por tipo), aunque todavía se publican las notificaciones para alertar al usuario. Nuestra aplicación sigue funcionando, pero su funcionalidad disminuye un poco.

En general, la comprobación de la versión de compilación ayuda a su código decidir en tiempo de ejecución entre haciendo algo en la nueva forma frente a la manera antigua. Por ejemplo:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

No hay ninguna regla rápida y sencilla que se explica cómo reducir o modificar la funcionalidad de la aplicación cuando se ejecuta en versiones anteriores de Android que carecen de una o varias API. En algunos casos (como en el `SetCategory` ejemplo anterior), basta con omitir la llamada de API cuando no está disponible. Sin embargo, en otros casos, es posible que deba implementar funcionalidad alternativa para cuando `Android.OS.Build.VERSION.SdkInt` se detecta sea inferior a la API de nivel que la aplicación necesita para presentar su experiencia óptima.

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>Las bibliotecas y los niveles de API

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Cuando se crea un proyecto de biblioteca de Xamarin.Android (por ejemplo, una biblioteca de clases o una biblioteca de enlaces), puede configurar solo la configuración de .NET Framework de destino &ndash; la versión Android mínima y la configuración de la versión Android de destino no está disponible. Eso es porque no hay ningún **manifiesto de Android** página:

[![Solo está disponible la compilación mediante la opción de versión de Android](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Al crear un proyecto de biblioteca de Xamarin.Android, no hay ningún **Aplicaciónandroid** página donde puede configurar la versión Android mínima y la versión Android de destino &ndash; la versión Android mínima y destino Configuración de la versión de Android no está disponible.
Eso es porque no hay ningún **compilar > aplicación de Android** página:

[![Página General sin opciones de versión mínimo y de destino de compilación](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

La versión Android mínima y la configuración de versión de Android de destino no está disponibles porque la biblioteca resultante no es una aplicación independiente &ndash; la biblioteca se puede ejecutar en cualquier versión de Android, dependiendo de la aplicación que está empaquetado con. Puede especificar cómo la biblioteca es ser *compilado*, pero no puede predecir qué nivel de API de plataforma se ejecutará la biblioteca en. Teniendo esto en mente, se deben observar las siguientes prácticas recomendadas cuando se consume o creación de bibliotecas:

-   **Al utilizar una biblioteca de Android** &ndash; si consume una biblioteca de Android en la aplicación, asegúrese de establecer marco de destino de la aplicación configuración a una API de nivel que se *al menos tan alto como* el destino Configuración del marco de trabajo de la biblioteca.

-   **Al crear una biblioteca de Android** &ndash; si va a crear una biblioteca de Android para su uso por otras aplicaciones, asegúrese de establecer su configuración de .NET Framework de destino en el nivel mínimo de API que necesita para compilar.

Se recomiendan estas prácticas recomendadas para ayudar a evitar la situación donde una biblioteca intenta llamar a una API que no está disponible en tiempo de ejecución (que puede provocar que la aplicación se bloquee). Si es un desarrollador de biblioteca, debe procurar restringir el uso de las llamadas de API a un subconjunto pequeño y bien establecida de la superficie total de API. Esto ayuda a asegurarse de que la biblioteca puede utilizarse de forma segura a través de un versiones más amplio de intervalo de Android.


## <a name="summary"></a>Resumen

Esta guía explica cómo se usan los niveles de API de Android para administrar la compatibilidad de aplicaciones en las distintas versiones de Android. Proporcionan pasos detallados para configurar el Xamarin.Android *.NET Framework de destino*, *versión Android mínima*, y *versión Android de destino* configuración del proyecto. Proporcionan instrucciones para usar Android SDK Manager para instalar los paquetes del SDK incluye ejemplos de cómo escribir código para tratar con distintos niveles de API en tiempo de ejecución y se explica cómo administrar los niveles de API al crear o consumo de bibliotecas de Android. También proporciona una lista completa que se relaciona con los niveles de API para los números de versión de Android (como Android 4.4), los nombres de versión de Android (como Kitkat) y códigos de versión de compilación de Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Cambia las herramientas de CLI de SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Selección su compileSdkVersion, minSdkVersion y targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [¿Qué es el nivel de API?](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Nombres, etiquetas y los números de compilación](https://source.android.com/source/build-numbers)
