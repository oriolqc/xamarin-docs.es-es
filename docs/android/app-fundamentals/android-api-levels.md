---
title: Descripción de los niveles de API de Android
description: Xamarin.Android tiene varias configuraciones de nivel de API de Android que determinan la compatibilidad de la aplicación con varias versiones de Android. Esta guía explica lo que significan estas opciones, cómo configurarlos y qué efecto tienen en su aplicación en tiempo de ejecución.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 8f284fefd260764c6f09d78d2518bfd115782cd2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="understanding-android-api-levels"></a>Descripción de los niveles de API de Android

_Xamarin.Android tiene varias configuraciones de nivel de API de Android que determinan la compatibilidad de la aplicación con varias versiones de Android. Esta guía explica lo que significan estas opciones, cómo configurarlos y qué efecto tienen en su aplicación en tiempo de ejecución._


## <a name="quick-start"></a>Inicio rápido

Xamarin.Android expone tres configuraciones de proyecto de nivel de API de Android:

-   [Marco de destino](#framework) &ndash; especifica que desea utilizar para generar la aplicación de .NET framework. Este nivel de API se usa en *compilar* tiempo por Xamarin.Android.

-   [Versión mínima de Android](#minimum) &ndash; especifica la versión de Android más antigua que quiere que su aplicación para admitir. Este nivel de API se usa en *ejecutar* tiempo Android.

-   [Versión de Android como destino](#target) &ndash; especifica la versión de Android que la aplicación está diseñada para ejecutarse en. Este nivel de API se usa en *ejecutar* tiempo Android.

Para poder configurar un nivel de API para el proyecto, debe instalar los componentes de la plataforma SDK para ese nivel de API. Para obtener más información sobre cómo descargar e instalar los componentes del SDK de Android, consulte [instalación del SDK de Android](~/android/get-started/installation/android-sdk.md).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Normalmente, los tres niveles de API de Xamarin.Android se establecen en el mismo valor. En el **aplicación** , establezca **compilar con la versión de Android (.NET Framework de destino)** a la última versión de API estable (o, como mínimo, a la versión de Android que tiene todas las características que necesita).
En la siguiente captura de pantalla, la plataforma de destino se establece en **7.1 Android (API nivel 25 - nueces)**:

[![Valores predeterminados de la versión de Framework a compilar con la versión Android como destino](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

En el **manifiesto Android** , establezca la versión de Android como mínimo **uso compilar con la versión SDK** y establezca la versión de Android de destino en el mismo valor que la versión de .NET Framework de destino (en la siguiente captura de pantalla, la plataforma Android de destino se establece en **7.1 Android (nueces)**):

[![Versiones de como mínimo y Android de destino establecen en la versión de .NET Framework de destino](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Si desea mantener la compatibilidad con versiones anteriores con una versión anterior de Android, establezca **versión mínimo Android al destino** a la versión más antigua de Android que quiere que su aplicación para admitir. (Tenga en cuenta que el 14 de nivel de API es el nivel de API mínimo necesario para [servicios Google Play y soporte técnico de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) La configuración del ejemplo siguiente es compatible con versiones de Android de 14 de nivel de API a través de nivel de API 25:

[![Compile con el nivel de API 25 nueces, versión Android mínimo establecido en el nivel de API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Normalmente, los tres niveles de API de Xamarin.Android se establecen en el mismo valor. Establecer **.NET framework de destino** a la última versión de API estable (o, como mínimo, a la versión de Android que tiene todas las características que necesita). Para establecer el **.NET framework de destino**, vaya a **generar > General** en el **Project Options**. En la siguiente captura de pantalla, la plataforma de destino se establece en **usar más reciente instalado platform (8.0)**:

[![El valor predeterminado para la plataforma de uso más reciente instalado .NET framework de destino](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

La configuración de versión mínimo y Android de destino puede encontrarse en **generar > aplicación Android** en **Project Options**. Establezca la versión mínima Android en **automático: versión de framework de destino de uso** y establezca la versión de Android de destino en el mismo valor que la versión de .NET Framework de destino. En la siguiente captura de pantalla, la plataforma Android de destino se establece en **8.0 Android (nivel de API 26)** para que coincida con la configuración de .NET Framework de destino anterior:

[![Establecer los niveles de destino y un marco en Opciones de proyecto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Si desea mantener la compatibilidad con versiones anteriores con una versión anterior de Android, cambiar **versión mínimo Android** a la versión más antigua de Android que quiere que su aplicación para admitir. Tenga en cuenta que el 14 de nivel de API es el nivel de API mínimo necesario para [servicios Google Play y soporte técnico de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Por ejemplo, la siguiente configuración es compatible con versiones de Android tan pronto como 14 de nivel de API:

[![Como mínimo y las versiones de destino establecidas en automático - usar la versión de framework de destino](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


Si la aplicación admite varias versiones de Android, el código debe incluir las comprobaciones en tiempo de ejecución para asegurarse de que la aplicación funciona con la configuración de versión mínimo Android (vea [en tiempo de ejecución busca versiones de Android](#runtimechecks) a continuación para obtener más información). Si está consumiendo o crear una biblioteca, consulte [niveles de API y las bibliotecas](#libraries) a continuación para ver recomendaciones para configurar la API de nivel configuración para las bibliotecas.



## <a name="android-versions-and-api-levels"></a>Versiones de Android y niveles de API

A medida que evoluciona la plataforma Android y se publiquen nuevas versiones de Android, cada versión de Android se asigna un identificador entero único, denominado el *nivel de API*. Por lo tanto, cada versión de Android corresponde a un solo nivel de API de Android. Dado que los usuarios instalan aplicaciones en las versiones anteriores, así como más recientes de Android, aplicaciones Android reales deben diseñarse para trabajar con varios niveles de API de Android.


### <a name="android-versions"></a>Versiones de Android

Cada versión de Android pasa por varios nombres de:

-   La versión de Android, como **7.1 Android**
-   Un nombre, un código como _nueces_
-   Nivel de una API correspondiente, como **25 de nivel de API**

Un nombre de código Android pueden corresponder a varias versiones y niveles de API (como se muestra en la lista siguiente), pero cada versión de Android corresponde exactamente a un nivel de API.

Además, se define Xamarin.Android *códigos de versión de compilación* que se asignan a los niveles de API de Android detectados. En la lista siguiente puede ayudarle a traducir entre el nivel de API, versión de Android, nombre de código y Xamarin.Android código de la versión de compilación.

-   **27 de API (Android 8.1)** &ndash; _Oreos_, que se lanzó diciembre de 2017. Compilar código de versión `Android.OS.BuildVersionCodes.OMr1`

-   **26 de API (Android 8.0)** &ndash; _Oreos_, que se lanzó agosto de 2017. Compilar código de versión `Android.OS.BuildVersionCodes.O`

-   **25 de API (Android 7.1)** &ndash; _nueces_, que se lanzó de 2016 de diciembre. Compilar código de versión `Android.OS.BuildVersionCodes.NMr1`

-   **24 de API (Android 7.0)** &ndash; _nueces_, que se lanzó agosto de 2016. Compilar código de versión `Android.OS.BuildVersionCodes.N`

-   **API 23 (Android 6.0)** &ndash; _Marshmallow_, que se lanzó agosto de 2015. Compilar código de versión `Android.OS.BuildVersionCodes.M`

-   **22 de API (Android 5.1)** &ndash; _círculo_, lanzamiento de marzo de 2015. Compilar código de versión `Android.OS.BuildVersionCodes.LollipopMr1`

-   **API 21 (Android 5.0)** &ndash; _círculo_, que se lanzó noviembre de 2014. Compilar código de versión `Android.OS.BuildVersionCodes.Lollipop`

-   **20 de API (Android 4.4W)** &ndash; _Kitkat inspección_, que se lanzó junio de 2014. Compilar código de versión `Android.OS.BuildVersionCodes.KitKatWatch`

-   **API 19 (Android 4.4)** &ndash; _Kitkat_, que se lanzó octubre de 2013. Compilar código de versión `Android.OS.BuildVersionCodes.KitKat`

-   **18 de API (Android 4.3)** &ndash; _jalea Bean_, que se lanzó julio de 2013. Compilar código de versión `Android.OS.BuildVersionCodes.JellyBeanMr2`

-   **17 de API (Android 4.2-4.2.2)** &ndash; _jalea Bean_, que se lanzó noviembre de 2012. Compilar código de versión `Android.OS.BuildVersionCodes.JellyBeanMr1`

-   **16 de API (Android 4.1-4.1.1)** &ndash; _jalea Bean_, que se lanzó junio de 2012. Compilar código de versión `Android.OS.BuildVersionCodes.JellyBean`

-   **API 15 (Android 4.0.3-4.0.4)** &ndash; _helado Sandwich_, que se lanzó diciembre de 2011. Compilar código de versión `Android.OS.BuildVersionCodes.IceCreamSandwichMr1`

-   **14 de API (Android 4.0-4.0.2)** &ndash; _helado Sandwich_, que se lanzó octubre de 2011. Compilar código de versión `Android.OS.BuildVersionCodes.IceCreamSandwich`

-   **13 de API (Android 3.2)** &ndash; _panal_, que se lanzó de junio de 2011. Compilar código de versión `Android.OS.BuildVersionCodes.HoneyCombMr2`

-   **12 de API (Android 3.1)** &ndash; _panal_, que se lanzó mayo de 2011. Compilar código de versión `Android.OS.BuildVersionCodes.HoneyCombMr1`

-   **11 de API (Android 3.0. x)** &ndash; _panal_, que se lanzó febrero de 2011. Compilar código de versión `Android.OS.BuildVersionCodes.HoneyComb`

-   **API de 10 (Android 2.3.3-2.3.4)** &ndash; _Monigote_, que se lanzó febrero de 2011. Compilar código de versión `Android.OS.BuildVersionCodes.GingerBreadMr1`

-   **API de 9 (Android 2.3-2.3.2)** &ndash; _Monigote_, que se lanzó noviembre de 2010. Compilar código de versión `Android.OS.BuildVersionCodes.GingerBread`

-   **API de 8 (Android 2.2)** &ndash; _Froyo_, que se lanzó junio de 2010. Compilar código de versión `Android.OS.BuildVersionCodes.Froyo`

-   **7 de API (Android 2.1)** &ndash; _Eclair_, que se lanzó enero de 2010. Compilar código de versión `Android.OS.BuildVersionCodes.EclairMr1`

-   **6 de API (Android 2.0.1)** &ndash; _Eclair_, que se lanzó diciembre de 2009. Compilar código de versión `Android.OS.BuildVersionCodes.Eclair01`

-   **API 5 (Android 2.0)** &ndash; _Eclair_, que se lanzó de noviembre de 2009. Compilar código de versión `Android.OS.BuildVersionCodes.Eclair`

-   **API de 4 (Android 1.6)** &ndash; _anillo_, que se lanzó de septiembre de 2009. Compilar código de versión `Android.OS.BuildVersionCodes.Donut`

-   **API de 3 (Android 1.5)** &ndash; _para magdalenas glaseadas_, que se lanzó mayo de 2009. Compilar código de versión `Android.OS.BuildVersionCodes.Cupcake`

-   **API 2 (Android 1.1)** &ndash; _Base_, lanzamiento de febrero de 2009. Compilar código de versión `Android.OS.BuildVersionCodes.Base11`

-   **API de 1 (Android 1.0)** &ndash; _Base_, lanzamiento de octubre de 2008. Compilar código de versión `Android.OS.BuildVersionCodes.Base`


Tal y como se indica esta lista, se publican nuevas versiones de Android con frecuencia &ndash; a veces varias versiones por año. Como resultado, el universo de dispositivos Android que podría ejecutar la aplicación incluye una amplia variedad de versiones de Android anteriores y más recientes. ¿Cómo puede garantizar que la aplicación se ejecutará de forma coherente y confiable en muchas versiones diferentes de Android? Niveles de API de Android pueden ayudarle a administrar este problema.


### <a name="android-api-levels"></a>Niveles de API de Android

Cada dispositivo Android se ejecuta en exactamente *una* nivel de API &ndash; este nivel de API se garantiza que sea única para cada versión de la plataforma Android. El nivel de API identifica de forma precisa la versión del conjunto de API que la aplicación puede llamar a; identifica la combinación de elementos del manifiesto, los permisos, etc. código con como desarrollador. Sistema de Android de niveles de API ayuda Android determinar si una aplicación es compatible con una imagen de sistema Android antes de instalar la aplicación en un dispositivo.

Cuando se compila una aplicación, contiene la siguiente información de nivel de API:

-   El *destino* nivel de API de Android que la aplicación se ha compilado.

-   El *mínimo* nivel de API de Android que debe tener un dispositivo Android para ejecutar la aplicación. 

Esta configuración se utiliza para asegurarse de que la funcionalidad necesaria para ejecutar correctamente la aplicación está disponible en el dispositivo Android en tiempo de instalación. De lo contrario, la aplicación se bloqueó su ejecución en ese dispositivo. Por ejemplo, si el nivel de API de un dispositivo Android es inferior al mínimo nivel de API que se especifica para la aplicación, el dispositivo Android impedirá el usuario de instalación de la aplicación.


## <a name="project-api-level-settings"></a>Configuración del proyecto de nivel de API

En las siguientes secciones se explican cómo usar el Administrador de SDK para preparar el entorno de desarrollo para los niveles de API que desee como destino, seguido de una explicación detallada de cómo configurar *.NET Framework de destino*, *mínimo Versión de Android*, y *versión de destino Android* configuración en Xamarin.Android.


### <a name="android-sdk-platforms"></a>Plataformas SDK de Android

Para poder seleccionar un nivel de destino o la API de mínimo en Xamarin.Android, debe instalar la versión de la plataforma de SDK de Android que corresponde a ese nivel de API. El intervalo de opciones disponibles para .NET Framework de destino, como mínimo Android versión y versión de Android de destino se limita a las versiones del intervalo de Android SDK que ha instalado. Puede usar el Administrador de SDK para comprobar que se instalan las versiones necesarias de Android SDK, y se puede usar para agregar los nuevos niveles de API que necesita para la aplicación. Si no está familiarizado con cómo instalar niveles de API, consulte [instalación del SDK de Android](~/android/get-started/installation/android-sdk.md).

<a name="framework" />

### <a name="target-framework"></a>Plataforma de destino

El *.NET Framework de destino* (también conocido como `compileSdkVersion`) es la versión de un marco concreto Android (nivel de API) que se compila la aplicación de en tiempo de compilación. Esta configuración especifica qué API de la aplicación *espera* que se usará cuando se ejecuta, pero no tiene ningún efecto en el que las API están realmente disponibles para la aplicación cuando se instala. Como resultado, cambiar la configuración de .NET Framework de destino no cambia el comportamiento de tiempo de ejecución.

La plataforma de destino identifica qué versiones de la biblioteca su aplicación se vincula con &ndash; Esto determina qué API que puede usar en la aplicación. Por ejemplo, si desea utilizar el [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método que se introdujo en el círculo 5.0 Android, debe establecer la plataforma de destino en **API nivel 21 (círculo)** o una versión posterior. Si establece .NET Framework de destino del proyecto a una API de nivel como **nivel de API 19 (KitKat)** e intenta llamar a la `SetCategory` método en el código, obtendrá un error de compilación.

Se recomienda que siempre compile con el *más reciente* versión de .NET Framework de destino disponible. Si lo hace, proporciona mensajes de advertencia útiles para las API en desuso que podrían ser llamadas por el código. Mediante la última versión de .NET Framework de destino es especialmente importante cuando se usa en las últimas versiones de la biblioteca de compatibilidad con &ndash; cada biblioteca espera que la aplicación sea compiladas nivel mínimo de la biblioteca de compatibilidad de API o mayor. 

> [!NOTE]
> A partir de agosto de 2018, la consola de Google Play requerirá que el nivel de API 26 (Android 8.0) de destino de nuevas aplicaciones o superior.
Las aplicaciones existentes se requerirá a nivel de API 26 o superior a partir de noviembre de 2018 de destino. Para obtener más información, consulte [mejora del rendimiento en Google Play para años proceder y seguridad de la aplicación](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para obtener acceso a la configuración de .NET Framework de destino en Visual Studio, abra las propiedades del proyecto en **el Explorador de soluciones** y seleccione la **aplicación** página:

[![Página de propiedades de proyecto la aplicación](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Establece la plataforma de destino mediante la selección de un nivel de API en el menú desplegable bajo **compilar con la versión Android** como se indicó anteriormente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para obtener acceso a la configuración de .NET Framework de destino en Visual Studio para Mac, haga clic en el nombre del proyecto y seleccione **opciones**; este abre la **Project Options** cuadro de diálogo. En este cuadro de diálogo, vaya a **generar > General** tal y como se muestra aquí:

[![Crear la sección General de la página de opciones de proyecto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Establece la plataforma de destino mediante la selección de un nivel de API en el menú desplegable situado a la derecha del **.NET framework de destino** como se indicó anteriormente.

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Versión mínima de Android

El *versión mínimo Android* (también conocido como `minSdkVersion`) es la versión más antigua del SO Android (es decir, el menor nivel de API) que puede instalar y ejecutar la aplicación. De forma predeterminada, una aplicación solo puede instalar en dispositivos que coinciden con la configuración de .NET Framework de destino o superior; Si el valor de versión mínimo Android es *inferior* a la configuración de .NET Framework de destino, también puede ejecutar la aplicación en versiones anteriores de Android. Por ejemplo, si establece la plataforma de destino en **7.1 Android (nueces)** y establezca la versión mínima Android en **Android 4.0.3 (helado Sandwich)**, la aplicación puede instalarse en cualquier plataforma de nivel de API 15 a nivel de API 25, ambos inclusive.

Aunque la aplicación puede generar e instalar en este intervalo de plataformas correctamente, esto no garantiza que será correctamente *ejecutar* en todas estas plataformas. Por ejemplo, si la aplicación se instala en **Android 5.0 (círculo)** y el código llama a una API que solo está disponible en **7.1 Android (nueces)** y versiones más recientes, la aplicación obtendrá un error en tiempo de ejecución y posiblemente de bloqueo. Por lo tanto, debe asegurarse de su código &ndash; en tiempo de ejecución &ndash; que llama a las API son compatibles con el dispositivo Android que se está ejecutando en. En otras palabras, el código debe incluir las comprobaciones en tiempo de ejecución explícita para asegurarse de que la aplicación usa las API más recientes solo en dispositivos que sean lo suficientemente recientes como para admitirlas.
[En tiempo de ejecución busca versiones de Android](#runtimechecks), más adelante en esta guía se explica cómo agregar estas comprobaciones en tiempo de ejecución en el código.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para obtener acceso a la configuración de versión mínimo Android en Visual Studio, abra las propiedades del proyecto en **el Explorador de soluciones** y seleccione la **manifiesto Android** página. En el menú desplegable bajo **versión mínimo Android** puede seleccionar la versión Android mínimo para la aplicación:

[![Android mínimo para la opción de destino establecida en compilar con la versión SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Si selecciona **uso compilar con la versión SDK**, la versión mínima Android será el mismo que la configuración de .NET Framework de destino.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para obtener acceso a la configuración de .NET Framework de destino en Visual Studio para Mac, haga clic en el nombre del proyecto y seleccione **opciones**; este abre la **Project Options** cuadro de diálogo. Vaya a **generar > aplicación Android**.
Mediante el menú desplegable a la derecha del **versión mínimo Android**, puede establecer la versión de Android mínimo para la aplicación:

[![Versión mínima de Android establecido en automático - versión de framework de destino de uso](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Si selecciona **automática &ndash; usar la versión de framework de destino**, la versión mínima Android será el mismo que la configuración de .NET Framework de destino.

-----


<a name="target" />

### <a name="target-android-version"></a>Versión de destino de Android

El *destino Android versión* (también conocido como `targetSdkVersion`) es la API de nivel de los dispositivos Android donde la aplicación espera para que se ejecute. Android usa este valor para determinar si desea habilitar los comportamientos de compatibilidad &ndash; Esto garantiza que la aplicación continúa funcionando de la manera esperada. Android usa la configuración de versión de Android de destino de la aplicación para averiguar qué cambios de comportamiento pueden aplicarse a la aplicación sin que ello interrumpa (Esto es cómo Android proporciona compatibilidad con versiones posteriores).

La plataforma de destino y la versión de destino Android, al tiempo que tiene nombres muy parecidos, no son lo mismo. La configuración de .NET Framework de destino comunica información de nivel de API de destino a Xamarin.Android para su uso en *tiempo de compilación*, mientras que la versión de destino Android comunica información de nivel de API de destino para Android para su uso en  *tiempo de ejecución* (cuando la aplicación está instalado y ejecutándose en un dispositivo).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para obtener acceso a esta configuración en Visual Studio, abra las propiedades del proyecto en **el Explorador de soluciones** y seleccione la **manifiesto Android** página. En el menú desplegable bajo **versión de destino Android** puede seleccionar la versión de Android de destino para la aplicación:

[![Versión de Android de destino establecida en compilar con la versión SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

Se recomienda establecer explícitamente la versión de Android de destino a la versión más reciente de Android que usa para probar la aplicación. Idealmente, debe establecerse para la versión más reciente del SDK de Android &ndash; Esto le permite usar las API de nuevo antes de trabajar a través de los cambios de comportamiento. Para la mayoría de los desarrolladores, se *no* recomienda establecer la versión de destino Android en **uso compilar con la versión SDK**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para obtener acceso a la configuración de .NET Framework de destino en Visual Studio para Mac, haga clic en el nombre del proyecto y seleccione **opciones**; este abre la **Project Options** cuadro de diálogo. Vaya a **generar > aplicación Android**.
Mediante el menú desplegable a la derecha del **versión de destino Android**, puede establecer la versión de Android de destino para la aplicación:

[![Versión de Android de destino establecido en automático - versión de framework de destino de uso](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

Se recomienda establecer explícitamente la versión de Android de destino a la versión más reciente de Android que usa para probar la aplicación. Idealmente, debe establecerse para la versión de SDK de Android más reciente disponible &ndash; Esto le permite usar las API de nuevo antes de trabajar a través de los cambios de comportamiento. Para la mayoría de los desarrolladores, no se recomienda establecer la versión de destino Android en **automático: versión de framework de destino de uso**.

-----

En general, la versión de Android de destino debe estar limitada por la versión Android mínima y la plataforma de destino. Es decir:

**Versión mínima de Android < = versión de Android de destino < = .NET Framework de destino**

Para obtener más información acerca de los niveles SDK, vea el desarrollador Android [sdk utiliza](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) documentación.


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>En tiempo de ejecución busca versiones de Android

Cuando se publica cada versión de Android, la API del marco se actualiza para proporcionar nuevos o la funcionalidad de reemplazo. Con pocas excepciones, la funcionalidad de API de versiones anteriores de Android se transmite en versiones más recientes de Android sin modificaciones. Como resultado, si la aplicación se ejecuta en un determinado nivel de API de Android, normalmente podrá ejecutar en un nivel de API de Android posterior sin modificaciones. Pero ¿qué ocurre si también desea ejecutar la aplicación en versiones anteriores de Android?

Si selecciona una versión de Android mínimo *inferior* a la configuración de .NET Framework de destino, en algunas API no puede estar disponible para la aplicación en tiempo de ejecución. Sin embargo, todavía puede ejecutar la aplicación en un dispositivo anterior, pero con funcionalidad reducida. Para cada API que no está disponible en las plataformas Android corresponde a la configuración de versión mínimo Android, el código debe comprobar explícitamente el valor de la `Android.OS.Build.VERSION.SdkInt` propiedad para determinar el nivel de API de la plataforma que se está ejecutando la aplicación. Si el nivel de API es *inferior* a la versión Android mínimo que es compatible con la API que desea llamar, a continuación, el código tiene que encontrar una manera para funcionar correctamente sin realizar esta llamada API.

Por ejemplo, imaginemos que desea usar el [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método para clasificar una notificación cuando se ejecuta en **Android 5.0 el círculo** (y versiones posteriores), pero todavía queremos que nuestra aplicación ejecutar en versiones anteriores de Android como **Android 4.1 jalea Bean** (donde `SetCategory` no está disponible). Que hace referencia a la tabla de la versión de Android al principio de esta guía, vemos que el código de la versión de compilación para **Android 5.0 el círculo** es `Android.OS.BuildVersionCodes.Lollipop`. Compatibilidad con versiones anteriores de Android where `SetCategory` es no disponible, el código puede detectar el nivel de API en tiempo de ejecución y llamar condicionalmente `SetCategory` sólo cuando el nivel de API es mayor o igual que el código de versión de compilación de círculo:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

En este ejemplo, de .NET Framework de nuestra aplicación de destino se establece en **Android 5.0 (API nivel 21)** y su versión mínimo Android está establecida en **Android 4.1 (API nivel 16)**. Dado que `SetCategory` está disponible en el nivel de API `Android.OS.BuildVersionCodes.Lollipop` y versiones posteriores, llamará a este código de ejemplo `SetCategory` sólo cuando está realmente disponible &ndash; hará lo siguiente *no* intenta llamar a `SetCategory` cuando la API nivel es 16, 17, 18, 19 y 20. La funcionalidad se reduce en estas versiones de Android anteriormente solo en la medida que las notificaciones no están ordenadas correctamente (debido a no se clasifican por tipo), aunque todavía se publican las notificaciones para alertar al usuario. Nuestra aplicación todavía funciona, pero su funcionalidad se reduce ligeramente.

En general, la comprobación de la versión de compilación ayuda a su código decidir en tiempo de ejecución entre hacer algo la nueva manera frente a la manera antigua. Por ejemplo:

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

No hay ninguna regla rápida y simple que explica cómo reducir o modificar la funcionalidad de la aplicación cuando se ejecuta en versiones anteriores de Android que faltan una o varias API. En algunos casos (como en la `SetCategory` ejemplo anterior), basta con omitir simplemente la llamada API cuando no está disponible. Sin embargo, en otros casos, puede que necesite implementar una funcionalidad alternativa para saber cuándo `Android.OS.Build.VERSION.SdkInt` se detecta que para ser inferior a la API de nivel que necesita la aplicación para presentar su experiencia óptima.

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>Niveles de API y bibliotecas

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Cuando se crea un proyecto de biblioteca de Xamarin.Android (por ejemplo, una biblioteca de clases o en una biblioteca de enlaces), puede establecer solo la configuración de .NET Framework de destino &ndash; la versión mínima Android y la configuración de la versión Android de destino no está disponible. Eso es porque no hay ningún **manifiesto Android** página:

[![Solo está disponible la compilación mediante la opción de versión de Android](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Al crear un proyecto de biblioteca de Xamarin.Android, no hay ningún **aplicación Android** página donde puede configurar la versión mínima Android y la versión de destino Android &ndash; la versión de Android como mínimo y de destino Configuración de la versión de Android no está disponible.
Eso es porque no hay ningún **generar > aplicación Android** página):

[![Generar página General sin opciones de versión mínimo y de destino](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

La versión mínimo Android y la configuración de versión de Android de destino no está disponibles porque la biblioteca resultante no es una aplicación independiente &ndash; la biblioteca se puede ejecutar en cualquier versión de Android, dependiendo de la aplicación que se incluye con. Se puede especificar cómo la biblioteca será *compilado*, pero no puede predecir qué nivel de API de plataforma se ejecutará la biblioteca en. Con esto en mente, las siguientes prácticas recomendadas deberían tenerse en cuenta al consumir o crear bibliotecas:

-   **Al utilizar una biblioteca de Android** &ndash; si una biblioteca de Android que consume en la aplicación, asegúrese de establecer de .NET Framework la aplicación de destino si se establece en una API nivel decir *al menos tan alto como* el destino Configuración de marco de trabajo de la biblioteca.

-   **Cuando se crea una biblioteca Android** &ndash; si va a crear una biblioteca de Android para su uso por otras aplicaciones, asegúrese de establecer su configuración de .NET Framework de destino en el nivel de API mínimo que necesita para compilar.

Se recomiendan estas prácticas recomendadas para ayudar a evitar la situación donde una biblioteca intenta llamar a una API que no está disponible en tiempo de ejecución (que puede provocar que la aplicación se bloquee). Si es un desarrollador de biblioteca, debe procurar restringir el uso de llamadas de API a un subconjunto pequeño y bien establecida de la superficie total de API. Esto ayuda a asegurarse de que se puede utilizar la biblioteca de forma segura en las versiones de un intervalo de Android más amplia.


## <a name="summary"></a>Resumen

Esta guía explica cómo se usan los niveles de API de Android para administrar la compatibilidad de aplicaciones en las distintas versiones de Android. Proporcionan pasos detallados para configurar el Xamarin.Android *.NET Framework de destino*, *versión mínimo Android*, y *versión de destino Android* configuración del proyecto. Proporcionan instrucciones para usar el Administrador de Android SDK para instalar paquetes SDK, incluidos ejemplos de cómo escribir código para tratar con distintos niveles de API en tiempo de ejecución y se ha explicado cómo administrar los niveles de API al crear o consumir bibliotecas Android. También proporciona una lista completa que relaciona los niveles de API a los números de versión de Android (por ejemplo, Android 4.4), nombres de versión de Android (por ejemplo, Kitkat) y códigos de versión de compilación Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Herramientas de SDK CLI cambia](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Seleccionar su compileSdkVersion, minSdkVersion y targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [¿Cuál es el nivel de API?](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Nombres, etiquetas y los números de compilación](https://source.android.com/source/build-numbers)
