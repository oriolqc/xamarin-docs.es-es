---
title: "Introducción al uso de Android"
description: "Con la introducción de Android desgaste de Google, ya no está obligado a solo los teléfonos y tabletas en cuanto a desarrollar excelentes aplicaciones Android. Compatibilidad con del Xamarin.Android desgaste Android hace posible para que pueda ejecutar código de C# en la muñeca! Esta introducción proporciona una introducción básica a desgaste Android, describe sus características clave y ofrece una visión general de las características disponibles en 2.0 desgaste Android. Se muestran algunos de los dispositivos Android desgaste más populares y proporciona vínculos a documentación de Google Android desgaste fundamental para obtener más información."
ms.topic: article
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 4f6cfe61b5f91fb6703fdcdd20513ce6bc2dc161
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-android-wear"></a>Introducción al uso de Android

_Con la introducción de Android desgaste de Google, ya no está obligado a solo los teléfonos y tabletas en cuanto a desarrollar excelentes aplicaciones Android. Compatibilidad con del Xamarin.Android desgaste Android hace posible para que pueda ejecutar código de C# en la muñeca! Esta introducción proporciona una introducción básica a desgaste Android, describe sus características clave y ofrece una visión general de las características disponibles en 2.0 desgaste Android. Se muestran algunos de los dispositivos Android desgaste más populares y proporciona vínculos a documentación de Google Android desgaste fundamental para obtener más información._


## <a name="overview"></a>Información general

Desgaste Android se ejecuta en una variedad de dispositivos, incluidos la primera generación Motorola 360, inspección de G del LG y el engranaje de Samsung vida. Una segunda generación, incluidos los SmartWatch 3 de Sony, también se ha liberado con capacidades adicionales incluidos GPS integrada y la reproducción de música sin conexión. Para Android 2.0 desgaste, Google ha asociado con LG para dos inspecciones nuevo: el deporte de inspección LG y el estilo de inspección LG.

![Dispositivos Android de desgaste 2.0](intro-to-wear-images/hero-image.png "ejemplo desgaste 2.0 los dispositivos Android")

Admite Xamarin.Android 5.0 y versiones posteriores admite desgaste Android a través de nuestro 4.4W Android (API de 20) y controles de interfaz de usuario específica de uso de un paquete de NuGet que agrega adicionales. Xamarin.Android 5.0 y versiones posteriores también incluye funcionalidad para empaquetar las aplicaciones de uso. Paquetes de NuGet también están disponibles para Android 2.0 desgaste tal como se describe más adelante en esta guía.


## <a name="android-wear-basics"></a>Conceptos básicos del uso de Android

Desgaste Android tiene un paradigma de interfaz de usuario que difiere de la mano de aplicaciones de Android. La primera oleada de las aplicaciones de uso se diseñaron para extender un complemento de aplicación mano de algún modo, pero el desgaste Android 2.0 a partir de, aplicaciones de uso pueden utilizar de manera autónoma. Al implementar una aplicación de uso, se empaqueta con una aplicación de mano complementario. Dado que usan más aplicaciones dependen de una aplicación complementaria de mano, necesitan una manera de comunicarse con aplicaciones de mano. Las siguientes secciones describen estos escenarios de uso y describen las características esenciales de desgaste Android. 



### <a name="usage-scenarios"></a>Escenarios de uso

La primera versión de Android desgaste se basan principalmente en extender las aplicaciones de mano actuales con notificaciones mejoradas y sincronización de datos entre la aplicación de mano y la aplicación wearable. Por lo tanto, estos escenarios son relativamente fáciles de implementar.


#### <a name="wearable-notifications"></a>Notificaciones de wearable

La manera más sencilla para admitir Android desgaste es aprovechar las ventajas de la naturaleza compartida de notificaciones entre los equipos de mano y el dispositivo wearable. Mediante el uso de la API de notificación de v4 de soporte técnico y el `WearableExtender` clase (disponible en la [Xamarin Android admite biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), puede aprovechar las características nativas de la plataforma, como tarjetas de estilo de bandeja de entrada o de entrada de voz. El [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) ejemplo proporciona código de ejemplo que muestra cómo enviar una lista de notificaciones a un dispositivo Android desgaste. 



#### <a name="companion-applications"></a>Asistente para aplicaciones

Otra estrategia consiste en crear una aplicación completa que se ejecuta de forma nativa en el dispositivo wearable y pares en una aplicación de mano complementario. Un buen ejemplo de este enfoque es la [prueba](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) aplicación de ejemplo que muestra cómo crear una prueba que se ejecuta en un dispositivo de mano y plantea algunas preguntas de prueba en el dispositivo wearable. 



### <a name="user-interface"></a>Interfaz de usuario

El modelo de navegación principal para desgaste es una serie de tarjetas organizadas verticalmente. Cada una de estas tarjetas puede tener acciones asociadas que se disponen en capa en la misma fila. El `GridViewPager` clase proporciona esta funcionalidad, se adhiere al mismo concepto de adaptador como `ListView`. Normalmente se asocian los `GridViewPager` con un `FragmentGridPagerAdaptor` (o `GridPagerAdaptor`) que permite representar cada fila y columna de celdas como una `Fragment`: 

[![Desgaste navegación](intro-to-wear-images/2d-picker-sml.png "desgaste navegación")](intro-to-wear-images/2d-picker.png#lightbox)

Desgaste también hace uso de los botones de acción que se componen de una gran coloreados círculo con texto pequeño descripción aparecen debajo de él (como se muestra anteriormente).  El [FilterUserControl](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) ejemplo muestra cómo utilizar `GridViewPager` y `GridPagerAdapter` en una aplicación de uso.

Android 2.0 desgaste agrega un cajón de navegación, un cajón de acción y botones de acción en línea a la interfaz de usuario de uso. Para obtener más información sobre los elementos de interfaz de usuario de Android 2.0 desgaste, consulte el Android [Anatomía](https://www.google.com/design/spec-wear/system-overview/anatomy.html) tema. 



### <a name="communications"></a>Comunicaciones

Desgaste Android proporciona comunicación diferentes dos API para facilitar las comunicaciones entre aplicaciones de mano complementaria y wearable: 

**API de datos** &ndash; esta API es similar a un almacén de datos sincronizados entre el dispositivo wearable y el dispositivo de mano. Android se encarga de propagar los cambios entre wearable y mano cuando resulta óptimo para hacerlo. Cuando el transportable está fuera del intervalo, pone en cola sincronización para una hora posterior. El punto de entrada principal para esta API es `WearableClass.DataApi`. Para obtener más información acerca de esta API, consulte el Android [sincronizar elementos de datos](https://developer.android.com/training/wearables/data-layer/data-items.html) tema. 

**API de mensaje** &ndash; esta API permite usar una ruta de acceso de las comunicaciones de nivel inferior: una carga pequeña se envía unidireccional sin sincronización entre las aplicaciones de equipos de mano y wearable.
El punto de entrada principal para esta API es `WearableClass.MessageApi`.
Para obtener más información acerca de esta API, consulte el Android [enviar y recibir mensajes](https://developer.android.com/training/wearables/data-layer/messages.html) tema.

Puede elegir registrar devoluciones de llamada para recibir los mensajes a través de cada una de las interfaces de agente de escucha de la API o, alternativamente, implemente un servicio en la aplicación que se deriva de `WearableListenerService`.
Este servicio se crearán instancias automáticamente por desgaste Android.
El [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) ejemplo muestra cómo implementar un `WearableListenerService`.



### <a name="deployment"></a>Implementación

Cada aplicación wearable se implementa con su propio archivo APK incrustado dentro de la aplicación principal APK. Este paquete se controla automáticamente en Xamarin.Android 5.0 y versiones posteriores, pero debe realizarse manualmente para versiones de Xamarin.Android anteriores a la versión 5.0. 
[Trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md) explica la implementación con más detalle. 



## <a name="going-further"></a>Ampliar conocimientos 

Es la mejor manera de familiarizarse con Android usan compilar y probar su primera aplicación. En la lista siguiente proporciona un orden de lectura recomendada para ayudarle a ponerse en marcha rápidamente:

1.  [Configuración e instalación](~/android/wear/get-started/installation.md) proporciona instrucciones detalladas para instalar y configurar el entorno de desarrollo para compilar aplicaciones de uso de Xamarin.Android. 

2.  Después de haber instalado los paquetes necesarios y configurado un dispositivo o emulador, consulte [¡Hola, desgaste](~/android/wear/get-started/hello-wear.md) para obtener instrucciones paso a paso que explican cómo crear un proyecto de Android desgaste pequeño botón identificadores hace clic en y se muestra un Haga clic en el contador en el dispositivo de uso. 

3.  [Implementación y pruebas](~/android/wear/deploy-test/index.md) proporciona más información sobre cómo configurar e implementar emuladores y dispositivos, incluidas las instrucciones sobre cómo implementar la aplicación en un dispositivo de uso a través de Bluetooth.

4.  [Trabajar con tamaños de pantalla](~/android/wear/screen-sizes.md) explica cómo obtener una vista previa y optimizar su interfaz de usuario para los distintos tamaños de pantalla disponibles en los dispositivos de uso. 

5.  [Trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md) describe los pasos para empaquetar manualmente aplicaciones de uso para la distribución en Google Play.

Después de haber creado su primera aplicación de uso, puede intentar crear una pantalla de watch personalizado para desgaste Android. 
[Crear una pantalla Watch](~/android/wear/platform/creating-a-watchface.md) proporciona instrucciones paso a paso y código de ejemplo para desarrollar un eliminada servicio de cara de inspección digital, seguido por el código más que mejora a una pantalla de estilo de señales analógicas watch con características adicionales. 



## <a name="android-wear-20"></a>Android desgaste 2.0

Android 2.0 desgaste presenta una variedad de nuevas características y funcionalidades, como *complicaciones*, curvado diseños, los alimentadores de navegación y la acción y las notificaciones ampliadas. Además, desgaste 2.0 hace posible para crear aplicaciones independientes que funcionan independientemente de aplicaciones de la mano. El nuevo *movimientos muñeca* capacidad permite mancos interacciones con la aplicación. Las siguientes secciones resaltan estas características y se proporcionan vínculos para ayudarle a empezar a trabajar con su uso en la aplicación.



### <a name="install-wear-20-packages"></a>Instalación desgaste 2.0 paquetes

Para compilar una aplicación de desgaste 2.0 con Xamarin.Android, debe agregar el **Xamarin.Android.Wear v2.0** paquete al proyecto (haga clic en el **ficha Examinar**):

[![V2.0 Xamarin.Android.Wear](intro-to-wear-images/wear-nuget-2.0-sml.png "instalar NuGet v2.0 Xamarin.Android.Wear")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Este paquete de NuGet contiene enlaces para las bibliotecas de Android Wearable de soporte técnico y desgaste Compat.

Además **Xamarin.Android.Wear**, le recomendamos que instale el **Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "instalar Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Características clave de desgaste 2.0

Android 2.0 desgaste es la actualización más importante al desgaste Android desde su lanzamiento inicial en 2014. En las siguientes secciones se resaltan las características claves de Android 2.0 desgaste y se proporcionan vínculos para ayudar a empezar a usar estas nuevas características de la aplicación. 


#### <a name="complications"></a>Complicaciones

*Complicaciones* es inspección pequeño widgets de cara que puede ver un vistazo sin tener que deslice el dedo de la pantalla del reloj. Complicaciones son similares a los widgets del panel de estilo de escritorio; muestran información como el tiempo, duración de la batería, los eventos de calendario y las estadísticas de aplicación de idoneidad: 

![Ejemplo de las complicaciones](intro-to-wear-images/complications.png "ejemplo complicaciones")

Para obtener más información acerca de las complicaciones, consulte el Android [inspección cara complicaciones](https://developer.android.com/wear/preview/features/complications.html) tema. 



#### <a name="navigation-and-action-drawers"></a>Alimentadores de acción y navegación 

Se incluyen dos alimentadores nuevo en desgaste 2.0. El *el cajón de navegación*, que aparece en la parte superior de la pantalla, permite a los usuarios navegar entre las vistas de la aplicación (como se muestra en la izquierda, bajo). El *el cajón de acción*, que aparece en la parte inferior de la pantalla (como se muestra en la parte derecha), permite a los usuarios elegir entre una lista de acciones. 

![Alimentadores de acción y navegación](intro-to-wear-images/drawers.png "alimentadores de acción y navegación")

Para obtener más información acerca de estos dos alimentadores interactivos nueva, vea el Android [desgaste navegación y acciones](https://developer.android.com/wear/preview/features/ui-nav-actions.html) tema. 



#### <a name="curved-layouts"></a>Diseños de curvados 

Desgaste 2.0 presenta nuevas características para mostrando los diseños de curvados en dispositivos de desgaste redondear. En concreto, la nueva `WearableRecyclerView` clase se ha optimizado para mostrar una lista de elementos verticales en pantallas round: 

![Ejemplo de diseño de curvado](intro-to-wear-images/curved-layout.png "ejemplo curvada diseño")

`WearableRecyclerView` extiende la `RecyclerView` clase para admitir la curvados diseños y los gestos de desplazamiento circulares. Para obtener más información, vea el Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) la documentación de API. 



#### <a name="standalone-apps"></a>Aplicaciones independientes 

Aplicaciones de Android desgaste 2.0 pueden trabajar independientemente de aplicaciones de la mano. Esto significa que, por ejemplo, una inspección inteligente puede continuar ofrecer una funcionalidad completa, incluso si el dispositivo de mano complementario se activa o desactiva lejos del dispositivo wearable. Para obtener más información sobre esta característica, vea el Android [aplicaciones independientes](https://developer.android.com/wear/preview/features/standalone-apps.html) tema.



#### <a name="wrist-gestures"></a>Muñeca movimientos 

Movimientos muñeca le permite a los usuarios interactuar con la aplicación sin usar la pantalla táctil &ndash; los usuarios puedan responder a la aplicación con una sola mano. Se admiten dos movimientos muñeca: 

-   Muñeca gesto out
-   Muñeca gesto en

Para obtener más información, vea el Android [muñeca movimientos](https://developer.android.com/wear/preview/features/gestures.html) tema. 


Hay muchas más características de desgaste 2.0 como acciones en línea, respuesta inteligente, entrada remoto, las notificaciones ampliadas y un nuevo modo de protocolo de puente para las notificaciones. Para obtener más información sobre las nuevas características de desgaste 2.0, consulte el Android [Introducción a la API](https://developer.android.com/wear/preview/api-overview.html). 



## <a name="devices"></a>Dispositivos

Estos son algunos ejemplos de los dispositivos que se pueden ejecutar desgaste Android:

* [Motorola 360](https://moto360.motorola.com/)
* [LG G inspección](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G inspección R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Icono de engranaje de Samsung en vivo](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASU ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>Información adicional

Compruebe la documentación de desgaste Android de Google:

* [Acerca de desgaste Android](http://www.android.com/wear/)
* [Diseño de aplicaciones de uso de Android](https://developer.android.com/design/wear/index.html)
* [biblioteca de Android.support.wearable ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android desgaste 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>Resumen

Esta introducción proporciona una visión general de desgaste Android. Que se describen las características básicas de Android desgaste e incluye una visión general de las características introducidas en 2.0 desgaste Android. Proporcionan vínculos a leer esencial para ayudar a los desarrolladores a empezar a trabajar con el desarrollo de Xamarin.Android desgaste y muestran ejemplos de algunos de los dispositivos Android usan actualmente en el mercado.


## <a name="related-links"></a>Vínculos relacionados

- [Instalación y configuración](~/android/wear/get-started/installation.md)
- [Introducción](~/android/wear/get-started/index.md)
