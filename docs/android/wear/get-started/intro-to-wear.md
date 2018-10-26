---
title: Introducción a Android Wear
description: Con la introducción de Google Android Wear, ya no está restringido a solo los teléfonos y tabletas en cuanto a desarrollo de excelentes aplicaciones Android. Soporte técnico de Xamarin.Android para Android Wear posibilita ejecutar C# código en la muñeca! Esta introducción proporciona una introducción básica a Android Wear, describe sus características claves y ofrece una visión general de las características disponibles en 2.0 de Android Wear. Enumera algunos de los dispositivos Android Wear más populares y proporciona vínculos a documentación de Google Android Wear esencial para obtener más información.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a35cb82f4f6d20e91f45a782c73d3ef811947c3a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117790"
---
# <a name="introduction-to-android-wear"></a>Introducción a Android Wear

_Con la introducción de Google Android Wear, ya no está restringido a solo los teléfonos y tabletas en cuanto a desarrollo de excelentes aplicaciones Android. Soporte técnico de Xamarin.Android para Android Wear posibilita ejecutar C# código en la muñeca! Esta introducción proporciona una introducción básica a Android Wear, describe sus características claves y ofrece una visión general de las características disponibles en 2.0 de Android Wear. Enumera algunos de los dispositivos Android Wear más populares y proporciona vínculos a documentación de Google Android Wear esencial para obtener más información._


## <a name="overview"></a>Información general

Android Wear se ejecuta en una variedad de dispositivos, incluidos la primera generación 360 Motorola, inspección de G del LG y el engranaje de Samsung Live. También se ha publicado una segunda generación, incluida la SmartWatch 3 de Sony, con funcionalidades adicionales, como GPS integrada y la reproducción de música sin conexión. Para Android Wear 2.0, Google ha trabajado con LG para dos inspecciones nuevo: el deporte de inspección LG y el estilo de inspección LG.

![Los dispositivos Android Wear 2.0](intro-to-wear-images/hero-image.png "ejemplo Android Wear 2.0 dispositivos")

Compatibilidad con Xamarin.Android 5.0 y versiones posteriores admite Android Wear a través de nuestro 4.4W Android (API 20) y un paquete de NuGet que agrega más controles de interfaz de usuario específica de desgaste. Xamarin.Android 5.0 y versiones posteriores incluye también funcionalidad para empaquetar las aplicaciones de desgaste. Paquetes de NuGet también están disponibles para Android Wear 2.0 como se describe más adelante en esta guía.


## <a name="android-wear-basics"></a>Conceptos básicos de Android Wear

Android Wear tiene un paradigma de interfaz de usuario que difiere de la mano de aplicaciones de Android. La primera ola de desgaste de aplicaciones se han diseñado para extender un complemento aplicación mano en alguna forma, pero a partir de Android Wear 2.0, aplicaciones de desgaste pueden ser utilizado independiente. Al implementar una aplicación Wear, está empaquetado con una aplicación complementaria de mano. Porque la mayoría Wear aplicaciones dependen de una aplicación complementaria de mano, necesita alguna manera para comunicarse con aplicaciones portátiles. Las siguientes secciones describen estos escenarios de uso y describen las características esenciales de Android Wear. 



### <a name="usage-scenarios"></a>Escenarios de uso

La primera versión de Android Wear se centra principalmente en ampliar aplicaciones portátiles actuales con las notificaciones mejoradas y sincronizar datos entre la aplicación de dispositivo de mano y la aplicación portátiles. Por lo tanto, estos escenarios son relativamente fáciles de implementar.


#### <a name="wearable-notifications"></a>Notificaciones de portátiles

La manera más sencilla para admitir Android Wear es aprovechar la naturaleza compartida de notificaciones entre el equipo de mano y el dispositivo portátil. Mediante el uso de la API de notificación de v4 de soporte técnico y el `WearableExtender` clase (disponible en el [Xamarin Android Support Library](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), puede aprovechar las características nativas de la plataforma, como tarjetas de estilo de la Bandeja de entrada o de entrada de voz. El [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) ejemplo proporciona código de ejemplo que muestra cómo enviar una lista de notificaciones a un dispositivo Android Wear. 



#### <a name="companion-applications"></a>Aplicaciones complementarias

Otra estrategia consiste en crear una aplicación completa que se ejecuta de forma nativa en el dispositivo portátil y se empareja con una aplicación complementaria de mano. Un buen ejemplo de este enfoque es el [cuestionario](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) aplicación de ejemplo que muestra cómo crear una prueba que se ejecuta en un dispositivo de mano y solicita las preguntas en el dispositivo portátil. 



### <a name="user-interface"></a>Interfaz de usuario

El modelo de navegación principal de desgaste es una serie de las tarjetas que se organizan verticalmente. Cada una de estas tarjetas puede tener acciones asociadas que se superponen horizontalmente en la misma fila. El `GridViewPager` clase proporciona esta funcionalidad; cumple el mismo concepto de adaptador que `ListView`. Normalmente se asocian los `GridViewPager` con un `FragmentGridPagerAdaptor` (o `GridPagerAdaptor`) que le permite representar celdas de cada fila y columna como un `Fragment`: 

[![Wear navegación](intro-to-wear-images/2d-picker-sml.png "Wear navegación")](intro-to-wear-images/2d-picker.png#lightbox)

Wear también hace uso de botones de acción que se componen de una gran coloreados círculo con texto pequeño descripción debajo de ella (como se muestra anteriormente).  El [FilterUserControl](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) ejemplo muestra cómo usar `GridViewPager` y `GridPagerAdapter` en una aplicación de desgaste.

2.0 de Android Wear agrega un cajón de navegación, un cajón de acción y botones de acción en línea a la interfaz de usuario de desgaste. Para obtener más información sobre los elementos de interfaz de usuario de 2.0 de Android Wear, vea el Android [Anatomía](https://www.google.com/design/spec-wear/system-overview/anatomy.html) tema. 



### <a name="communications"></a>Comunicaciones

Android Wear proporciona dos API para facilitar las comunicaciones entre aplicaciones portátiles y aplicaciones de dispositivo de mano complementarias de comunicación diferentes: 

**API de datos** &ndash; esta API es similar a un almacén de datos sincronizados entre el dispositivo portátil y el dispositivo de mano. Android se encarga de propagar los cambios entre el dispositivo de mano y portátiles cuando resulta óptimo para hacerlo. Cuando el ponible está fuera del intervalo, pone en cola la sincronización para una fecha posterior. El punto de entrada principal para esta API es `WearableClass.DataApi`. Para obtener más información acerca de esta API, consulte el Android [elementos de datos de sincronización](https://developer.android.com/training/wearables/data-layer/data-items.html) tema. 

**API de mensajes** &ndash; esta API permite usar una ruta de acceso de comunicación de un nivel inferior: una pequeña carga se envía un sentido sin sincronización entre las aplicaciones de dispositivo de mano y portátiles.
El punto de entrada principal para esta API es `WearableClass.MessageApi`.
Para obtener más información acerca de esta API, consulte el Android [enviar y recibir mensajes](https://developer.android.com/training/wearables/data-layer/messages.html) tema.

Puede elegir registrar devoluciones de llamada para recibir esos mensajes a través de cada una de las interfaces de API del agente de escucha o, también puede implementar un servicio en la aplicación que se deriva de `WearableListenerService`.
Este servicio se creará automáticamente una instancia por Android Wear.
El [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) ejemplo ilustra cómo implementar un `WearableListenerService`.



### <a name="deployment"></a>Implementación

Cada aplicación portátiles se implementa con su propio archivo APK incrustado dentro de la aplicación principal APK. Este paquete se controla automáticamente en Xamarin.Android 5.0 y versiones posteriores, pero debe realizarse manualmente para las versiones anteriores a la versión 5.0 de Xamarin.Android. 
[Trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md) explica la implementación con más detalle. 



## <a name="going-further"></a>Ampliar conocimientos 

Es la mejor manera de familiarizarse con Android Wear compilar y probar su primera aplicación. En la lista siguiente proporciona un orden de lectura recomendada para ayudarle a ponerse en marcha rápidamente:

1.  [Configuración e instalación](~/android/wear/get-started/installation.md) se proporcionan instrucciones detalladas para instalar y configurar el entorno de desarrollo para compilar aplicaciones de Xamarin.Android desgaste. 

2.  Después de que tiene instalados los paquetes necesarios y configurar un dispositivo o emulador, consulte [Hola, Wear](~/android/wear/get-started/hello-wear.md) para obtener instrucciones paso a paso que explican cómo crear un pequeño proyecto de Android Wear ese botón identificadores hace clic en y se muestra un Haga clic en el contador en el dispositivo de desgaste. 

3.  [Implementación y pruebas](~/android/wear/deploy-test/index.md) proporciona más información sobre cómo configurar e implementar en emuladores y dispositivos, incluidas las instrucciones sobre cómo implementar la aplicación en un dispositivo Wear a través de Bluetooth.

4.  [Trabajar con tamaños de pantalla](~/android/wear/screen-sizes.md) explica cómo obtener una vista previa y optimizar su interfaz de usuario para los distintos tamaños de pantalla disponibles en los dispositivos de desgaste. 

5.  [Trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md) describe los pasos para empaquetado manual de desgaste de aplicaciones para su distribución en Google Play.

Después de haber creado su primera aplicación Wear, desea intentar compilar una esfera del reloj personalizado para Android Wear. 
[Creación de una esfera del reloj](~/android/wear/platform/creating-a-watchface.md) proporciona instrucciones paso a paso y el código de ejemplo para desarrollar un eliminados servicio cara de inspección digital, seguida de más de código que se mejora a un reloj analógico de estilo con características adicionales. 



## <a name="android-wear-20"></a>Android Wear 2.0

2.0 de Android Wear presenta una variedad de nuevas características y funcionalidades, tales como *complicaciones*, curvados diseños, cajones de navegación y la acción y las notificaciones expandidas. Además, Wear 2.0 hace posible para crear aplicaciones independientes que funcionan independientemente de las aplicaciones de dispositivo de mano. El nuevo *gestos muñeca* funcionalidad permite una sola mano interacciones con la aplicación. Las siguientes secciones resaltan estas características y proporcionan vínculos para ayudarle a empezar a trabajar con ellos en la aplicación.



### <a name="install-wear-20-packages"></a>Instalación Wear 2.0 paquetes

Para compilar una aplicación Wear 2.0 con Xamarin.Android, debe agregar el **Xamarin.Android.Wear v2.0** paquete al proyecto (haga clic en el **pestaña Examinar**):

[![V2.0 Xamarin.Android.Wear](intro-to-wear-images/wear-nuget-2.0-sml.png "instalar NuGet Xamarin.Android.Wear v2.0")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Este paquete de NuGet contiene enlaces para las bibliotecas portátiles de compatibilidad con Android y Wear Compat.

Además **Xamarin.Android.Wear**, le recomendamos que instale el **Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "instalar Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Características clave de desgaste 2.0

2.0 de Android Wear es la mayor actualización para Android Wear desde su lanzamiento inicial en 2014. Las secciones siguientes resaltan las características clave de 2.0 de Android Wear, y se proporcionan vínculos para ayudar a empezar a usar estas nuevas características en la aplicación. 


#### <a name="complications"></a>Complicaciones

*Complicaciones* es pequeña inspección widgets caras que ve un vistazo sin tener que pasar el dedo de la pantalla del reloj. Complicaciones son similares a los widgets del panel de estilo de escritorio; muestran información como el meteorológicos, duración de la batería, los eventos de calendario y las estadísticas de aplicación de idoneidad: 

![Ejemplo de complicaciones](intro-to-wear-images/complications.png "ejemplo complicaciones")

Para obtener más información sobre problemas, consulte el Android [complicaciones de inspección cara](https://developer.android.com/wear/preview/features/complications.html) tema. 



#### <a name="navigation-and-action-drawers"></a>Alimentadores de acción y navegación 

Se incluyen dos alimentadores nuevo en Wear 2.0. El *cajón de navegación*, que aparece en la parte superior de la pantalla, permite a los usuarios navegar entre las vistas de la aplicación (como se muestra en la izquierda, bajo). El *cajón de acción*, que aparece en la parte inferior de la pantalla (como se muestra en la parte derecha), permite a los usuarios elegir entre una lista de acciones. 

![Alimentadores de acción y navegación](intro-to-wear-images/drawers.png "alimentadores de acción y navegación")

Para obtener más información acerca de estos dos alimentadores interactivos nuevo, consulte el Android [Wear navegación y acciones](https://developer.android.com/wear/preview/features/ui-nav-actions.html) tema. 



#### <a name="curved-layouts"></a>Diseños de curvos 

Desgaste 2.0 presenta nuevas características para mostrar los diseños de curvos en dispositivos de desgaste de redondeo. En concreto, el nuevo `WearableRecyclerView` clase está optimizada para mostrar una lista de elementos verticales en pantallas de redondeo: 

![Ejemplo de diseño curvado](intro-to-wear-images/curved-layout.png "ejemplo curvados diseño")

`WearableRecyclerView` extiende la `RecyclerView` clase para admitir los diseños de curvos y gestos de desplazamiento circulares. Para obtener más información, vea el Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) documentación de API. 



#### <a name="standalone-apps"></a>Aplicaciones independientes 

Aplicaciones de Android Wear 2.0 pueden trabajar independientemente de las aplicaciones de dispositivo de mano. Esto significa que, por ejemplo, una inspección inteligente puede continuar ofrecer una funcionalidad completa, incluso si el dispositivo complementario portátil está apagado o lejos de los dispositivos portátiles. Para obtener más información sobre esta característica, consulte el Android [las aplicaciones independientes](https://developer.android.com/wear/preview/features/standalone-apps.html) tema.



#### <a name="wrist-gestures"></a>Gestos de muñeca 

Gestos muñeca permiten a los usuarios interactuar con la aplicación sin usar la pantalla táctil &ndash; los usuarios puedan responder a la aplicación con una única mano. Se admiten dos movimientos muñeca: 

-   Muñeca gesto horizontal
-   Muñeca gesto en

Para obtener más información, vea el Android [muñeca movimientos](https://developer.android.com/wear/preview/features/gestures.html) tema. 


Hay muchas más características de desgaste 2.0 como acciones insertadas, respuesta inteligente, entrada remoto, las notificaciones expandidas y un nuevo modo de puente para las notificaciones. Para obtener más información sobre las nuevas características de desgaste 2.0, consulte el Android [Introducción a la API](https://developer.android.com/wear/preview/api-overview.html). 



## <a name="devices"></a>Dispositivos

Estos son algunos ejemplos de los dispositivos que se pueden ejecutar Android Wear:

* [Motorola 360](https://moto360.motorola.com/)
* [LG G inspección](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G inspección R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung engranaje en vivo](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>Información adicional

Revise la documentación de Google Android Wear:

* [Acerca de Android Wear](http://www.android.com/wear/)
* [Diseño de aplicaciones para Android Wear](https://developer.android.com/design/wear/index.html)
* [biblioteca Android.support.wearable ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android Wear 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>Resumen

Esta introducción proporciona una visión general de Android Wear. Se describen las características básicas de Android Wear y se incluye una visión general de las características introducidas en Android 2.0 Wear. Proporcionan vínculos a la lectura esencial para ayudar a los desarrolladores a empezar a trabajar con el desarrollo de desgaste de Xamarin.Android y muestran ejemplos de algunos de los dispositivos Android Wear actualmente en el mercado.


## <a name="related-links"></a>Vínculos relacionados

- [Instalación y configuración](~/android/wear/get-started/installation.md)
- [Introducción](~/android/wear/get-started/index.md)
