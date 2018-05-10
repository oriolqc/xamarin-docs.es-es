---
title: Generador de perfiles de Xamarin
description: Esta guía explora las características clave del generador de perfiles de Xamarin. Busca en los generadores de perfiles, generación de perfiles y cuándo debe utilizarse y en un flujo de trabajo estándar de generar perfiles de aplicaciones de Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 7e28e2513b74faa884fb7bc3d7194f1b02844734
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

_Esta guía explora las características clave del generador de perfiles de Xamarin. Busca en los generadores de perfiles, generación de perfiles y cuándo debe utilizarse y en un flujo de trabajo estándar de generar perfiles de aplicaciones de Xamarin._

Éxito de la aplicación depende de la experiencia del usuario final. Como desarrollador puede que haya implementado algunas características realmente maravillosa en su aplicación, pero si la aplicación es lenta o completa de bloqueos, el usuario probablemente se deshacerse de él.

Históricamente, Mono ha incluido un generador de perfiles de línea de comandos eficaz para recopilar información sobre los programas que se ejecutan en el tiempo de ejecución Mono llama el [el generador de perfiles de registro Mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/). El generador de perfiles de Xamarin una interfaz gráfica para el generador de perfiles de registro Mono y admite la generación de perfiles tvOS aplicaciones en Windows, iOS, tvOS y aplicaciones Mac en Mac y Android, iOS y Android.

El generador de perfiles de Xamarin tiene un número de instrumentos disponibles para la generación de perfiles, las asignaciones y ciclos, el generador de perfiles de tiempo. Esta guía explora lo que dichos instrumentos de medida y cómo analizar la aplicación y explica el significado de los datos presentados en cada pantalla.

Esta guía examina los escenarios comunes de generación de perfiles y el generador de perfiles presenta como una herramienta para ayudar a analizar y optimizar aplicaciones de iOS y Android.

## <a name="contents"></a>Contenido

- [Descargue e instale](#Download_and_Install)
- [Los generadores de perfiles y la generación de perfiles](#Profilers_and_Profiling)
- [Xamarin Profiler](#Xamarin_Profiler)
- [Compatibilidad del generador de perfiles](#Profiler_Support)
- [Conceptos básicos del generador de perfiles](#Profiler_Basics)
    - [Permitir que la aplicación de generación de perfiles](#Allowing_Profiling_in_your_App)
    - [Iniciar el generador de perfiles](#Launching_the_Profiler)
        - [Iniciar desde Visual Studio para Mac](#Launching_from_Xamarin_Studio)
        - [Iniciar desde Visual Studio](#Launching_from_Visual_Studio)
        - [Guardar y cargar las sesiones del generador de perfiles](#Saving_and_Loading_Profiler_Sessions)
        - [Instrumentos y características del generador de perfiles](#Profiler_Features)
    - [Asignaciones](#Allocations)
    - [Generador de perfiles de tiempo](#Time_Profiler)
    - [Ciclos](#Cycles)
- [Generar perfiles de aplicaciones](#Profiling_Applications)
- [Resumen](#Summary)

## <a name="download-and-install"></a>Descargue e instale

> [!NOTE]
> **Nota:** debe ser un [Visual Studio Enterprise](https://www.visualstudio.com/vs/compare/) suscriptor para desbloquear esta característica en cualquier proyecto de Visual Studio Enterprise en Windows o Visual Studio para Mac en un equipo Mac.

El generador de perfiles de Xamarin es una aplicación independiente y se integra con Visual Studio para Mac y Visual Studio habilitar la generación de perfiles desde el IDE.

### <a name="download"></a>Descargar

Descargue el paquete de instalación para su plataforma:

- [**MacOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Una vez descargado, inicie el instalador para agregar el generador de perfiles de Xamarin en el sistema.


## <a name="profilers-and-profiling"></a>Los generadores de perfiles y la generación de perfiles

Generación de perfiles es un paso importante y a menudo se pasa por alto en el desarrollo de aplicaciones. Generación de perfiles es una forma de **análisis dinámico programa** -analiza el programa mientras está en ejecución y en uso. Un generador de perfiles es una herramienta de minería de datos que recopila información acerca de la complejidad del tiempo, el uso de determinados métodos y la memoria está asignada. Un generador de perfiles le permite explorar en profundidad y analizar estas métricas para identificar áreas con problemas en el código.

Al diseñar y desarrollar una aplicación, es importante que no optimice prematuramente; es decir, invierte más del tiempo desarrollando el código en áreas que rara vez tendrá acceso. Se trata de la potencia de generación de perfiles. Un generador de perfiles proporciona una visión general más frecuente partes del código base, y le ayuda a localizar las áreas donde se deben emplear en mejoras de realización de hora. Los desarrolladores deberían tener cuidado para entender donde la mayoría de las veces se invirtió en la aplicación y uso de memoria por la aplicación.

Generación de perfiles es útil para todos los tipos de desarrollo, pero resulta especialmente fundamental en el desarrollo de aplicaciones móvil. Código no optimizado es mucho más evidente en plataformas móviles que en equipos de escritorio y el éxito de la aplicación depende de código maravillosa y optimizado que se ejecuta de forma eficaz.

## <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

El generador de perfiles de Xamarin proporciona a los desarrolladores una manera de generar perfiles de aplicaciones desde dentro de Visual Studio para Mac o en Visual Studio. El generador de perfiles recopila y muestra información acerca de la aplicación, que, a continuación, se puede usar el programador para analizar el comportamiento de la aplicación. Hay varias maneras de generar perfiles de una aplicación con el generador de perfiles de Xamarin, es decir, la generación de perfiles de memoria y muestreo estadístico. Estos se llevan a cabo a través de las asignaciones y el generador de perfiles de tiempo instrumenta respectivamente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Actualmente, el generador de perfiles de Xamarin puede utilizarse para probar aplicaciones de Xamarin.iOS y Xamarin.Android, Xamarin.Mac en Mac (a través de Visual Studio para Mac). El generador de perfiles es un proceso independiente desde el IDE y, por lo tanto, además de poder iniciar desde Visual Studio para Mac, se puede utilizar como una aplicación independiente para examinar .exe y `.mlpd` archivos que se han producido desde la [generador de perfiles de registro mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Actualmente, el generador de perfiles de Xamarin puede utilizarse para probar aplicaciones de Xamarin.Android en Windows (a través de Visual Studio y Visual Studio para Mac). El generador de perfiles es un proceso independiente desde el IDE y, por lo tanto, además de poder iniciar desde Visual Studio, se puede utilizar como una aplicación independiente para examinar .exe y `.mlpd` archivos que se han producido desde la [el generador de perfiles de registro mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Compatibilidad del generador de perfiles

Compatibilidad con el generador de perfiles de Xamarin está disponible en las siguientes plataformas:

 - Visual Studio para Mac (macOS, con una licencia Enterprise)
    - Android
        - Dispositivo y el emulador
    - iOS
        - Dispositivo y del simulador
    - tvOS (no se admite la instrumentación de tiempo)
        - Dispositivo y del simulador
    - Mac

 - Visual Studio (solo **Enterprise** versión)
    - Android
        - Dispositivo y el emulador
    - iOS [Experimental]
        - Dispositivo y del simulador
    - tvOS
        - Dispositivo y del simulador

Tenga en cuenta que también puede **sólo** perfil **depurar** configuraciones.

## <a name="profiler-basics"></a>Conceptos básicos del generador de perfiles

Esta sección presenta las partes del generador de perfiles de Xamarin y tours sus características.

### <a name="allow-profiling-in-your-app"></a>Permitir la generación de perfiles de la aplicación

Antes de correctamente puede generar perfiles de la aplicación, debe permitir la generación de perfiles en las opciones de proyecto de la aplicación.

 - iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  **Compilar > iOS depuración > Habilitar generación de perfiles**

  ![](images/ios-options-mac.png "cuadro de diálogo de opciones en Visual Studio para Mac de iOS")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **Propiedades > iOS compilación > Habilitar generación de perfiles**

  ![](images/ios-project-options-vs.png "cuadro de diálogo de opciones en Visual Studio de iOS")

-----

 - Android:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  **Compilar > depuración Android > habilitar la instrumentación de desarrollador**

  ![Cuadro de diálogo de opciones de Android en Visual Studio para Mac](images/android-project-options.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **Compilar > depuración Android > habilitar la instrumentación de desarrollador**

  ![Cuadro de diálogo de opciones de Android en Visual Studio para Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Iniciar el generador de perfiles

El generador de perfiles de Xamarin se puede iniciar desde el IDE cuando está creando perfiles de su aplicación Android o iOS, o como una aplicación independiente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

#### <a name="launching-from-visual-studio-for-mac"></a>Iniciar desde Visual Studio para Mac

1. En primer lugar, asegúrese de que tiene la aplicación cargada en Visual Studio para Mac y seleccione la configuración de depuración (valor predeterminado).
2. Vaya a **ejecutar > Iniciar generación de perfiles**en Visual Studio para Mac, o **analizar > Xamarin Profiler** en Visual Studio, para abrir el generador de perfiles, como se muestra en el diagrama siguiente:

  ![](images/start-profiling-xs.png "Iniciar el generador de perfiles de Visual Studio para Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="launching-from-visual-studio"></a>Iniciar desde Visual Studio

1.  En primer lugar, asegúrese de que tiene la aplicación cargada en Visual Studio y seleccione la configuración de depuración (valor predeterminado), según lo especificado anteriormente.
2.  Vaya a **analizar > Xamarin Profiler** en Visual Studio, para abrir el generador de perfiles, como se muestra en el diagrama siguiente:

![Iniciar el generador de perfiles de Visual Studio](images/start-profiling-vs.png)

-----

Si no aparecen los elementos de menú, consulte la [Guía de solución de problemas](~/tools/profiler/troubleshooting.md).

Esto inicia el generador de perfiles e inicia automáticamente la generación de perfiles de la aplicación.

El generador de perfiles puede utilizarse para medir el rendimiento y memoria. Esto consigue a través de las asignaciones y el generador de perfiles de tiempo de instrumentos, que a continuación se explican con detalle en la sección siguiente.

#### <a name="saving-and-loading-profiler-sessions"></a>Guardar y cargar las sesiones del generador de perfiles

Para guardar una sesión de generación de perfiles en cualquier momento, elija **archivo > Guardar como...**  desde la barra de menús del generador de perfiles. Esta opción guarda el archivo en _mlpd_ formato, un formato muy comprimido especial para generar perfiles de datos.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Después de que se ha instalado el generador de perfiles de Xamarin puede encontrarse en la carpeta de aplicaciones, como se muestra en la captura de pantalla siguiente:

![](images/applications.png "Abra el generador de perfiles independiente de Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Después de que se ha instalado la aplicación del generador de perfiles de Xamarin puede encontrarse en el directorio de la aplicación:

![](images/applications-vs.png "Abrir el generador de perfiles independiente de Windows ")

-----

Puede cargar *.mlpd* archivos en el generador de perfiles, abra la aplicación independiente, seleccione **elegir destino** y cargar el archivo.

Para obtener más información, consulte [generar archivos de .mlpd ](~/tools/profiler/troubleshooting.md#gen_mlpd).


## <a name="profiler-features"></a>Características del generador de perfiles

El generador de perfiles de Xamarin se compone de cinco secciones, como se muestra a continuación:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](images/profiler-mac-sml.png "Secciones de generador de perfiles de Visual Studio para Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/profiler-vs.png "Secciones de generador de perfiles de Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barra de herramientas** – situado en la parte superior del generador de perfiles, esto ofrece opciones para iniciar o detener la generación de perfiles, seleccione un proceso de destino, ver el tiempo de ejecución de la aplicación y seleccione las vistas de división que componen la aplicación del generador de perfiles.
- **Instrumentar lista** : enumera todos los instrumentos cargados para la sesión de generación de perfiles.
- **Trazar gráfico** : estos gráficos se relacionan con horizontalmente los instrumentos pertinentes en la lista de instrumentación. Un control deslizante (se muestra debajo de generador de perfiles de tiempo) puede utilizarse para cambiar la escala.
- **Área de detalle de instrumentar** -contiene datos que se muestran la vista seleccionada del instrumento actual. Veremos estas vistas con más detalle en la sección siguiente.
- **Vista de inspector** – contiene secciones que se pueden seleccionar el control segmentados. Las secciones son dependientes de los instrumentos seleccionada e incluye: opciones de configuración, estadísticas, información de seguimiento de la pila y la ruta de acceso a las raíces.

### <a name="allocations"></a>Asignaciones

El instrumento de asignaciones proporciona información detallada acerca de los objetos de la aplicación tal y como se crea y se recolectan.

En la parte superior del generador de perfiles es el gráfico de asignaciones, que muestra la cantidad de memoria asignada a intervalos regulares durante la generación de perfiles. Actualmente el gráfico de las asignaciones es el número total de asignaciones y no el tamaño del montón en ese momento en el tiempo. En un sentido, nunca irá hacia abajo, siempre aumentará. Esto incluye los objetos asignados en la pila. Según la versión de tiempo de ejecución que se usa, el gráfico puede parecer diferente, incluso para la misma aplicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](images/allocations1.png "Instrumento de asignaciones")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/allocations1-vs.png "Instrumento de asignaciones")](images/allocations1-vs.png#lightbox)

-----

Hay diferentes vistas de datos en el acto de asignaciones, que permiten a los desarrolladores a analizar cómo su aplicación está utilizando y liberar memoria. A continuación se describen estas vistas:

 -   **Las asignaciones de** : se muestra una lista de todas las asignaciones y los agrupa por nombre de clase. Esto ofrece una excelente introducción de las clases y métodos que se va a utilizar, con qué frecuencia se utilizan y el tamaño colectivo de las clases utilizadas. Doble clic en una clase mostrará el la memoria asignada: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/allocations3.png "Ficha de asignaciones")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations2-vs.png "Ficha de asignaciones")](images/allocations2-vs.png#lightbox)

-----

La vista de Inspector para las asignaciones proporciona opciones de filtrado y agrupación de objetos, lo que proporciona las estadísticas en la memoria asignada y las asignaciones superiores, así como vistas de seguimiento de la pila y la ruta de acceso al nodo raíz.

 -   **Árbol de llamadas de** : Esto muestra el árbol de llamadas completa de todos los subprocesos en la aplicación e incluye información sobre la memoria asignada en cada nodo. Cuando se selecciona un elemento en la lista, aparecerán todos los nodos del mismo nivel gris. Puede expandir el árbol o haga doble clic en el elemento que se va a profundizar en él. Cuando se muestra esta vista de datos, la vista de inspector de configuración de presentación se puede utilizar para cambiar la manera en que se presentan. Actualmente hay dos opciones:
    1.  **Invierte el árbol de llamadas** : se considera que el seguimiento de pila de arriba a abajo. Esta es una vista cómoda opción tal y como indican a los métodos más profundo donde gastos a la CPU el tiempo.
    2.  **Independiente por subproceso** : esta opción organiza el árbol de llamadas por subproceso.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/allocations2.png "Pestaña árbol de llamadas")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations3-vs.png "Pestaña árbol de llamadas")](images/allocations3-vs.png#lightbox)

-----

 -   **Las instantáneas** – este panel muestra información acerca de las instantáneas de memoria. Para generar estos durante la generación de perfiles de una aplicación en directo, haga clic en el _cámara_ botón en la barra de herramientas en todos los puntos que le gustaría que la memoria se conservan y se liberan. A continuación, hacer clic en cada instantánea para explorar lo que sucede en segundo plano. Tenga en cuenta que las instantáneas se pueden realizar cuando se activa una aplicación de generación de perfiles. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/allocations4.png "Pestaña de instantáneas")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations4-vs.png "Pestaña de instantáneas")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Generador de perfiles de tiempo

El instrumento de generador de perfiles de tiempo mide exactamente cuánto tiempo se dedica a cada método de una aplicación. La aplicación está en pausa a intervalos regulares y se ejecuta un seguimiento de pila en cada subproceso activo. Cada fila del área de detalle de instrumento muestra la ruta de acceso de ejecución que se ha seguido.

El diagrama de caja, tal y como se muestra en la captura de pantalla siguiente, muestra el número de muestras recibidas por la aplicación mientras se ejecuta:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Instrumentación del generador de perfiles de tiempo](images/time1.png)](images/time1.png#lightbox) 

[![Instrumento de generador de perfiles de tiempo: lista de ejemplos](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Instrumentación del generador de perfiles de tiempo](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Instrumento de generador de perfiles de tiempo: lista de ejemplos](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----


- **Árbol de llamadas de** – muestra de tiempo empleado en cada método:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/time2.png "Hora de instrumentación del generador de perfiles: árbol de llamadas")](images/time2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/time2-vs.png "Hora de instrumentación del generador de perfiles: árbol de llamadas")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Ciclos

Mediante el uso de código C# y F # administrado, puede ser bastante habitual y Lamentablemente muy fácil crear referencias a objetos que nunca se eliminará. Esta intrument permite identificar los objetos y mostrar los ciclos que se hace referencia en la aplicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Los ciclos de instrumentación](images/cycles-vs.png)](images/time1-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Generar perfiles de aplicaciones

Actualmente, se pueden generar perfiles solo las configuraciones de depuración predeterminado.

Si se generan perfiles de una aplicación con cualquier otra configuración, aparecerá el cuadro de diálogo de mensaje siguiente:


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cuadro de diálogo de Error de generación de perfiles](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/image1vs.png "Cuadro de diálogo de Error de generación de perfiles")](images/image1vs.png#lightbox) 

-----


Seleccione **actualización** para continuar.

<!---
## Profiling Android Applications


Due to the recent inclusion of the profiling libraries into any new Android project template, you will find that when profiling any legacy applications you are greeted with the message dialog above.

You will need to enable this to make sure that the profiling libraries are included in your Android application, for debug builds. This should not be checked for release builds as it creates overhead.


## Profiling iOS Applications

### Profiling tvOS

## Profiling Mac Applications
-->

### <a name="sgen-garbage-collector-and-profiling"></a>El recolector de elementos no utilizados de SGen y generación de perfiles

El [SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/) recolector de elementos no utilizados se utiliza para todas las plataformas de Xamarin.

SGen es un GC de generaciones, que asigna los objetos de una aplicación en tres montones: guardería, principales montón y el espacio de objeto grande. Esto permite la ejecución más rápida de recolección de elementos. SGen es actualmente el GC de forma predeterminada para Xamarin.Android y aplicaciones Xamarin.iOS unificado.

Aplicación de Xamarin.iOS mediante la API clásico usa el GC Boehm – un recolector de elementos no generaciones, conservador. Como es conservador, es menos probable que libera la memoria disponible, lo que puede provocar resultados incorrectos cuando se utiliza el generador de perfiles. Por este motivo, el instrumento de las asignaciones no se puede usar con el recolector de elementos no utilizados Boehm.

Mientras se le pedirá un cuadro de diálogo de mensaje si la aplicación usa el GC Boehm, Xamarin no recomienda cambiar las aplicaciones existentes de iOS que utilice Boehm a SGen sin investigación cuidado y realizar pruebas minuciosas. Xamarin también no recomienda volver a SGen para generación de perfiles y, a continuación, cambiar, como estos resultados no proporcionarán pruebas comparativas precisas del uso de memoria.

Para obtener más información sobre la administración de memoria, consulte el [memoria y prácticas recomendadas de rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md) guía.

## <a name="summary"></a>Resumen

En esta guía se examinando la generación de perfiles es y cómo esto supone una mejora para el desarrollador. A continuación, presentamos el generador de perfiles de Xamarin, proporciona algunas historial y la información en su funcionamiento. Por último se viajado por las características del generador de perfiles de Xamarin y se han explorado instrumentos de generador de perfiles de tiempo y asignaciones.


## <a name="related-links"></a>Vínculos relacionados

- [Prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de la versión](https://developer.xamarin.com/releases/profiler/preview/)
