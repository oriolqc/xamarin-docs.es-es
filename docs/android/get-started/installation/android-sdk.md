---
title: Configuración del SDK de Android para Xamarin.Android
description: Visual Studio incluye Android SDK Manager, que puede usar para descargar herramientas de Android SDK, plataformas y otros componentes necesarios para desarrollar aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/10/2018
ms.openlocfilehash: 895496f6a198f679ce08322ae48fe88e03b85629
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947275"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configuración del SDK de Android para Xamarin.Android

_Visual Studio incluye Android SDK Manager, que puede usar para descargar herramientas de Android SDK, plataformas y otros componentes necesarios para desarrollar aplicaciones de Xamarin.Android._


## <a name="overview"></a>Información general

En esta guía se explica cómo usar Android SDK Manager de Xamarin en Visual Studio y Visual Studio para Mac.

> [!NOTE]
> Esta guía solo se aplica a Visual Studio 2017 y Visual Studio para Mac.  

Android SDK Manager de Xamarin (instalado como parte de la carga de trabajo **Desarrollo para dispositivos móviles con .NET**) ayuda a descargar los componentes de Android más recientes que se necesitan para desarrollar la aplicación de Xamarin.Android. Reemplaza el SDK Manager independiente de Google, que está en desuso.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="requirements"></a>Requisitos

Para usar Android SDK Manager de Xamarin, necesita lo siguiente:

- Visual Studio 2017 (edición Community, Professional o Enterprise). Se necesita Visual Studio 2017 versión 15.7 o posterior.

- Visual Studio Tools para Xamarin versión 4.10.0 o posterior. 

Android SDK Manager de Xamarin no es compatible con Visual Studio
2015. Los usuarios de Visual Studio 2015 deben usar las herramientas de SDK Manager proporcionadas por Google en Android SDK.


El Administrador de Android SDK de Xamarin también necesita disponer de Java Development Kit, que se instala automáticamente con Xamarin.Android.
Xamarin.Android usa [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), necesario para el desarrollo en el nivel de API 24 o superior. JDK 8 también admite niveles de API anteriores al 24. Si está desarrollando contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

 
## <a name="sdk-manager"></a>SDK Manager 

Para iniciar SDK Manager en Visual Studio, haga clic en **Herramientas > Android > Android SDK Manager**:

[![Ubicación del elemento de menú Android SDK Manager](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

**Android SDK Manager de Xamarin** se abre en la pantalla **Herramientas y Android SDK**. Esta pantalla tiene dos pestañas: **Plataformas** y **Herramientas**:

[![Captura de pantalla de Android SDK Manager abierto en la pestaña Plataformas](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

La pantalla **Herramientas y Android SDK** se describe con más detalle en las secciones siguientes.


### <a name="android-sdk-location"></a>Ubicación de Android SDK

La ubicación de Android SDK está configurada en la parte superior de la pantalla **Herramientas y Android SDK**, tal como se muestra en la imagen anterior. Esta ubicación se debe configurar correctamente para que las pestañas **Plataformas** y **Herramientas** funcionen correctamente. Es posible que deba establecer la ubicación de Android SDK por alguno de los motivos siguientes:

1. SDK Manager de Xamarin no pudo encontrar Android SDK. 

2. Ha instalado Android SDK en una ubicación alternativa (no predeterminada). 

Para establecer la ubicación de Android SDK, haga clic en el botón &hellip; situado a la derecha de **Ubicación de Android SDK**. Se abrirá el cuadro de diálogo **Buscar carpeta**, que usará para navegar a la ubicación de Android SDK. En la siguiente captura de pantalla, está seleccionado Android SDK en **Archivos de programa (x86)\\Android**:

![Captura de pantalla del cuadro de diálogo de Windows Buscar carpeta para localizar Android SDK](android-sdk-images/win/05-browse-for-folder.png)

Al hacer clic en **Aceptar**, Android SDK Manager de Xamarin administrará el Android SDK instalado en la ubicación seleccionada.



### <a name="tools-tab"></a>Pestaña Herramientas

En la pestaña **Herramientas** se muestra una lista de _herramientas_ y _extras_. Use esta pestaña para instalar las herramientas de Android SDK, las herramientas de la plataforma y las herramientas de compilación.
Además, puede instalar Android Emulator, el depurador de bajo nivel (LLDB), el NDK, la aceleración de HAXM y bibliotecas de Google Play.


Por ejemplo, para descargar el paquete de Android Emulator de Google, haga clic en la marca de verificación situada junto a **Android Emulator** y, después, haga clic en el botón **Aplicar cambios**:

[![Instalación de Android Emulator desde la pestaña Herramientas](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)



Es posible que aparezca un cuadro de diálogo con el mensaje _Some components can be updated. Do you want to update them now?_ (Algunos componentes se pueden actualizar. ¿Quiere actualizarlos ahora?). Haga clic en **Sí**. Después, se muestra un cuadro de diálogo de aceptación de licencia:


![Pantalla de aceptación de licencia](android-sdk-images/win/07-license-acceptance.png)


Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Una vez finalizada la instalación, en la pestaña **Herramientas** se mostrará que se han instalado las herramientas y los extras seleccionados.



### <a name="platforms-tab"></a>Pestaña Plataformas

En la pestaña **Plataformas** se muestra una lista de versiones del SDK de la plataforma junto con otros recursos (por ejemplo, imágenes del sistema) para cada plataforma.


[![Captura de pantalla del panel Plataformas](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)


En esta pantalla se muestra la versión de Android (como **Android 7.0**), el nombre de código (**Nougat**), el nivel de API (por ejemplo, **24**) y el estado (**Instalado** si la plataforma está instalada). La pestaña **Plataformas** se usa para instalar los componentes para el nivel de API de Android que quiere establecer como destino (para más información sobre las versiones de Android y los niveles de API, vea [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)).

Si están instalados todos los componentes de una plataforma, aparece una marca de verificación junto al nombre de la plataforma. Si no están instalados todos los componentes de una plataforma, se rellena el cuadro de dicha plataforma. 


Puede expandir una plataforma para ver sus componentes (y los componentes instalados). Para ello, haga en el cuadro **+** situado a la izquierda de la plataforma.
Haga clic en **-** para contraer la lista de componentes de una plataforma.


Para agregar otra plataforma al SDK, haga clic en el cuadro situado junto a la plataforma hasta que aparezca la marca de verificación para instalar todos sus componentes. Después, haga clic en **Aplicar cambios**:


[![Ejemplo de cómo agregar componentes de Android 7.1 Nougat a Android SDK](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)


Para instalar solo el SDK, haga clic una vez en el cuadro situado junto a la plataforma. Después, puede seleccionar los componentes individuales que necesite:


[![Ejemplo de cómo agregar algunos componentes de Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)




Observe que el número de componentes que se van a instalar aparece junto al botón **Aplicar cambios**. En el ejemplo anterior, hay seis componentes listos para instalarse. Tras hacer clic en el botón **Aplicar cambios**, verá la pantalla **Aceptación de licencia**:



![Cuadro de diálogo Aceptación de licencia de la pestaña Plataforma](android-sdk-images/win/11-license-screen.png)


Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. Es posible que vea este cuadro de diálogo más de una vez si se van a instalar varios componentes. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Cuando se complete el proceso de descarga e instalación (puede tardar muchos minutos, en función de cuántos componentes deban descargarse), los componentes agregados se marcarán con una marca de verificación y a su lado se indicará **Instalado**.



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


## <a name="requirements"></a>Requisitos

Para usar Android SDK Manager de Xamarin, necesita lo siguiente:

-   Visual Studio para Mac 7.5 (o posterior).

El Administrador de Android SDK de Xamarin también necesita disponer de Java Development Kit, que se instala automáticamente con Xamarin.Android.
Xamarin.Android usa [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), necesario para el desarrollo en el nivel de API 24 o superior. JDK 8 también admite niveles de API anteriores al 24. Si está desarrollando contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

 
## <a name="sdk-manager"></a>SDK Manager 

Para iniciar SDK Manager en Visual Studio para Mac, haga clic en **Herramientas > SDK Manager**:
 
![Ubicación del elemento de menú Android SDK Manager](android-sdk-images/mac/sdkmanager-01.png )

**Android SDK Manager** se abre en la **ventana Preferencias**, que contiene tres pestañas, denominadas **Plataformas**, **Herramientas**y **Ubicaciones**:

![Captura de pantalla de Android SDK Manager abierto en la pestaña Plataformas](android-sdk-images/mac/sdkmanager-02.png)

Las pestañas de Android SDK Manager de Xamarin se describen en las secciones siguientes.


### <a name="locations-tab"></a>Pestaña Ubicaciones

La pestaña **Ubicaciones** tiene tres opciones para configurar las ubicaciones de Android SDK, Android NDK y el SDK de Java (JDK). Estas ubicaciones se deben configurar correctamente para que las pestañas **Plataformas** y **Herramientas** funcionen correctamente.

Cuando se inicia SDK Manager, determina automáticamente la ruta de acceso para cada paquete instalado e indica que se ha **Encontrado** y coloca un icono de marca de verificación verde junto a la ruta de acceso:

![Captura de pantalla de la pestaña Ubicaciones](android-sdk-images/mac/sdkmanager-03.png)

Haga clic en el botón **Restablecer valores predeterminados** para que SDK Manager busque el SDK, el NDK y el JDK en sus ubicaciones predeterminadas. 

Normalmente, usará la pestaña **Ubicaciones** para modificar la ubicación de Android SDK o del JDK de Java. No es necesario instalar el NDK para desarrollar aplicaciones de Xamarin.Android, ya que el NDK solo se usa cuando se necesita desarrollar partes de la aplicación mediante lenguajes de código nativo, como C y C++.

### <a name="tools-tab"></a>Pestaña Herramientas

En la pestaña **Herramientas** se muestra una lista de _herramientas_ y _extras_. Use esta pestaña para instalar las herramientas de Android SDK, las herramientas de la plataforma y las herramientas de compilación.
Además, puede instalar Android Emulator, el depurador de bajo nivel (LLDB), el NDK, la aceleración de HAXM y bibliotecas de Google Play.


Por ejemplo, para descargar el paquete de Android Emulator de Google, haga clic en la marca de verificación situada junto a **Android Emulator** y, después, haga clic en el botón **Instalar actualizaciones**:

![Instalación de Android Emulator desde la pestaña Herramientas](android-sdk-images/mac/sdkmanager-08.png)


Es posible que aparezca un cuadro de diálogo con el mensaje _Some components can be updated. Do you want to update them now?_ (Algunos componentes se pueden actualizar. ¿Quiere actualizarlos ahora?). Haga clic en **Sí**. Después, se muestra un cuadro de diálogo de aceptación de licencia:


![Pantalla de aceptación de licencia](android-sdk-images/mac/sdkmanager-09.png)


Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Una vez finalizada la instalación, en la pestaña **Herramientas** se mostrará que se han instalado las herramientas y los extras seleccionados.



### <a name="platforms-tab"></a>Pestaña Plataformas

En la pestaña **Plataformas** se muestra una lista de versiones del SDK de la plataforma junto con otros recursos (por ejemplo, imágenes del sistema) para cada plataforma.


![Captura de pantalla del panel Plataformas](android-sdk-images/mac/sdkmanager-11.png)


En esta pantalla se muestra la versión de Android (como **Android 7.0**), el nombre de código (**Nougat**), el nivel de API (por ejemplo, **24**) y el estado (**Instalado** si la plataforma está instalada). La pestaña **Plataformas** se usa para instalar los componentes para el nivel de API de Android que quiere establecer como destino (para más información sobre las versiones de Android y los niveles de API, vea [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)).

Si están instalados todos los componentes de una plataforma, aparece una marca de verificación junto al nombre de la plataforma. Si no están instalados todos los componentes de una plataforma, se rellena el cuadro de dicha plataforma. 


Puede expandir una plataforma para ver sus componentes (y los componentes instalados). Para ello, haga en la **flecha** situada a la izquierda de la plataforma.
Haga clic en la **flecha abajo** para contraer la lista de componentes de una plataforma.


Para agregar otra plataforma al SDK, haga clic en el cuadro situado junto a la plataforma hasta que aparezca la marca de verificación para instalar todos sus componentes. Después, haga clic en **Aplicar cambios**:


![Ejemplo de cómo agregar componentes de Android 4.4 a Android SDK](android-sdk-images/mac/sdkmanager-12.png)


Para instalar solo el SDK, haga clic una vez en el cuadro situado junto a la plataforma. Después, puede seleccionar los componentes individuales que necesite:


![Ejemplo de cómo agregar algunos componentes de Android 4.4](android-sdk-images/mac/sdkmanager-13.png)




Observe que el número de componentes que se van a instalar aparece junto al botón **Aplicar cambios**. Tras hacer clic en el botón **Aplicar cambios**, verá la pantalla **Aceptación de licencia**:



![Cuadro de diálogo Aceptación de licencia de la pestaña Plataforma](android-sdk-images/mac/sdkmanager-14.png)


Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. Es posible que vea este cuadro de diálogo más de una vez si se van a instalar varios componentes. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Cuando se complete el proceso de descarga e instalación (puede tardar muchos minutos, en función de cuántos componentes deban descargarse), los componentes agregados se marcarán con una marca de verificación y a su lado se indicará **Instalado**.

-----

 
## <a name="summary"></a>Resumen

En esta guía se explica cómo instalar y usar la herramienta Android SDK Manager de Xamarin en Visual Studio y Visual Studio para Mac.


## <a name="related-links"></a>Vínculos relacionados

- [Cambios en las Herramientas de Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
