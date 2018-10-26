---
title: Generador de perfiles de Xamarin
description: Esta guía explora las características clave de la Profiler de Xamarin. Aspecto que en los generadores de perfiles, generación de perfiles y cuándo debe usarse y en un flujo de trabajo estándar para generar perfiles de aplicaciones de Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: 237ee1a39907f9ebf0eb88db9fff1fbdab691f5e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112603"
---
# <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

_Esta guía explora las características clave de la Profiler de Xamarin. Aspecto que en los generadores de perfiles, generación de perfiles y cuándo debe usarse y en un flujo de trabajo estándar para generar perfiles de aplicaciones de Xamarin._

Éxito de la aplicación depende de la experiencia del usuario final. Como desarrollador podría han implementado algunas características realmente impresionantes en su aplicación, pero si la aplicación es lenta o lleno de bloqueos, el usuario es probable que permitirán deshacerme de él.

Históricamente, Mono cuenta con un eficaz generador de perfiles de línea de comandos para recopilar información sobre los programas que se ejecutan en el tiempo de ejecución Mono, denominada la [generador de perfiles de registro Mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/). El una interfaz gráfica para el generador de perfiles de registro de Mono y admite la generación de perfiles de Android, iOS, tvOS y las aplicaciones de Mac en Mac y Android, iOS y tvOS aplicaciones en Windows de Xamarin Profiler.

El Profiler Xamarin tiene una serie de instrumentos disponibles para la generación de perfiles, las asignaciones, los ciclos y tiempo Profiler. Esta guía explora lo que miden dichos instrumentos y cómo analizan la aplicación y explica el significado de los datos presentados en cada pantalla.

Esta guía examina los escenarios comunes de generación de perfiles y el generador de perfiles presenta como una herramienta para ayudar a analizar y optimizar aplicaciones de iOS y Android.

## <a name="download-and-install"></a>Descargue e instale

> [!NOTE]
> Deberá ser un [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) suscriptor para desbloquear esta característica en cualquier proyecto de Visual Studio Enterprise en Windows o Visual Studio para Mac en un equipo Mac.

El Profiler Xamarin es una aplicación independiente y se integra con Visual Studio para Mac y Visual Studio habilitar la generación de perfiles desde el IDE.

Descargue el paquete de instalación para su plataforma:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Una vez descargado, inicie el instalador para agregar al Profiler de Xamarin en el sistema.

## <a name="profilers-and-profiling"></a>Los generadores de perfiles y generación de perfiles

Generación de perfiles es un paso importante y a menudo se pasa por alto en el desarrollo de aplicaciones. Generación de perfiles es una forma de **análisis dinámico programa** -analiza el programa mientras está en ejecución y en uso. Un generador de perfiles es una herramienta de minería de datos que recopila información acerca de la complejidad de tiempo, el uso de determinados métodos y la memoria está asignada. Un generador de perfiles le permite explorar en profundidad y analizar estas métricas para identificar las áreas problemáticas en el código.

Al diseñar y desarrollar una aplicación, es importante que no optimice prematuramente; es decir, dedicar tiempo a desarrollar el código en áreas que rara vez tendrá acceso. Se trata de la capacidad de generación de perfiles. Un generador de perfiles proporciona visión general más frecuente partes del código base, y ayuda a buscar las áreas donde debe gastar realizar mejoras de tiempo. Los desarrolladores deben tener cuidado para entender donde la mayoría del tiempo se dedica a la aplicación y el uso de memoria por la aplicación.

Generación de perfiles es útil para todos los tipos de desarrollo, pero es especialmente fundamental en el desarrollo de aplicaciones móvil. Código no optimizado es mucho más evidente en plataformas móviles que en equipos de escritorio y el éxito de la aplicación depende de código optimizado y maravillosa que se ejecuta de forma eficaz.

## <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

El Profiler Xamarin proporciona a los desarrolladores una manera de generar perfiles de aplicaciones desde dentro de Visual Studio para Mac o Visual Studio. El generador de perfiles recopila y muestra información acerca de la aplicación, que, a continuación, se puede usar el programador para analizar el comportamiento de la aplicación. Hay una serie de diferentes maneras de generar perfiles de una aplicación con Profiler de Xamarin, es decir, la generación de perfiles de memoria y muestreo estadístico. Estos se llevan a cabo a través de las asignaciones y tiempo Profiler instruments, respectivamente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Actualmente, el Profiler de Xamarin puede utilizarse para probar aplicaciones de Xamarin.iOS, Xamarin.Android y Xamarin.Mac en Mac (a través de Visual Studio para Mac). El generador de perfiles es un proceso independiente desde el IDE y, por lo tanto, además de iniciar desde Visual Studio para Mac, se puede usar como una aplicación independiente para examinar .exe y `.mlpd` archivos que se han producido desde la [delgeneradordeperfilesderegistrodemono](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Actualmente, el Profiler de Xamarin puede usarse para probar aplicaciones de Xamarin.Android en Windows (a través de Visual Studio y Visual Studio para Mac). El generador de perfiles es un proceso independiente desde el IDE y, por lo tanto, además de iniciar desde Visual Studio, se puede usar como una aplicación independiente para examinar .exe y `.mlpd` archivos que se han producido desde la [generador de perfiles de registro mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Compatibilidad con Profiler

Compatibilidad con el Profiler Xamarin está disponible en las siguientes plataformas:

 - Visual Studio para Mac (Mac OS, con licencia de Enterprise)
    - Android
        - Dispositivo y el emulador
    - iOS
        - Simulador y dispositivo
    - tvOS (no se admite el instrumento de tiempo)
        - Simulador y dispositivo
    - Mac

 - Visual Studio (solo **Enterprise** versión)
    - Android
        - Dispositivo y el emulador
    - iOS [Experimental]
        - Simulador y dispositivo
    - tvOS
        - Simulador y dispositivo

Tenga en cuenta que puede **sólo** perfil **depurar** configuraciones.

## <a name="profiler-basics"></a>Conceptos básicos de Profiler

Esta sección presenta las partes de la Profiler Xamarin y tours sus características.

### <a name="allow-profiling-in-your-app"></a>Permitir la generación de perfiles en la aplicación

Antes de correctamente puede generar perfiles de la aplicación, deberá permitir la generación de perfiles en las opciones de proyecto de la aplicación.

 - iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Compilar > depuración de iOS > Habilitar generación de perfiles**

  ![](images/ios-options-mac.png "cuadro de diálogo de opciones en Visual Studio para Mac de iOS")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Propiedades > compilación de iOS > Habilitar generación de perfiles**

  ![](images/ios-project-options-vs.png "cuadro de diálogo de opciones en Visual Studio de iOS")

-----

 - Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Compilar > depuración de Android > habilitar la instrumentación de desarrollador**

  ![Cuadro de diálogo de opciones de Android en Visual Studio para Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Compilar > depuración de Android > habilitar la instrumentación de desarrollador**

  ![Cuadro de diálogo de opciones de Android en Visual Studio para Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Iniciando el Profiler

El Profiler de Xamarin se puede iniciar desde el IDE cuando se está generando perfiles para su aplicación Android o iOS, o como una aplicación independiente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Iniciar desde Visual Studio para Mac

1. En primer lugar, asegúrese de que la aplicación de carga en Visual Studio para Mac y seleccione la configuración de depuración (valor predeterminado).
2. Vaya a **ejecutar > Iniciar generación de perfiles**en Visual Studio para Mac, o **analizar > Xamarin Profiler** en Visual Studio, para abrir el Profiler, como se muestra en el diagrama siguiente:

  ![](images/start-profiling-xs.png "Iniciando al Profiler desde Visual Studio para Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Iniciar desde Visual Studio

1.  En primer lugar, asegúrese de que la aplicación de carga en Visual Studio y seleccione la configuración de depuración (valor predeterminado), según lo especificado anteriormente.
2.  Vaya a **analizar > Xamarin Profiler** en Visual Studio, para abrir el Profiler, como se muestra en el diagrama siguiente:

![Iniciando al Profiler desde Visual Studio](images/start-profiling-vs.png)

-----

Si no aparecen los elementos de menú, consulte el [Guía de solución](~/tools/profiler/troubleshooting.md).

Esto inicia al Profiler y generación de perfiles de la aplicación inicia automáticamente.

El Profiler puede usarse para medir el rendimiento y la memoria. Esto consigue a través de Profiler de tiempo y asignaciones instrumentos que exploramos detalladamente en la sección siguiente.

#### <a name="saving-and-loading-profiler-sessions"></a>Guardar y cargar las sesiones de Profiler

Para guardar una sesión de generación de perfiles en cualquier momento, elija **archivo > Guardar como...**  desde la barra de menús de Profiler. Esto guarda el archivo en _mlpd_ formato, un formato muy comprimido especial para generar perfiles de datos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Después de haber instalado el Profiler de Xamarin puede encontrarse en la carpeta de aplicaciones como se muestra en la captura de pantalla siguiente:

![](images/applications.png "Abrir Profiler independiente de Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Una vez se ha instalado la aplicación Xamarin Profiler puede encontrarse en el directorio de aplicación:

![](images/applications-vs.png "Abrir Profiler independiente de Windows ")

-----

Puede cargar *.mlpd* archivos en el Profiler, abra la aplicación independiente, seleccione **elegir destino** y cargar el archivo.

Para obtener más información, consulte [generar archivos .mlpd ](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Características de Profiler

El Profiler de Xamarin se compone de cinco secciones, como se muestra a continuación:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](images/profiler-mac-sml.png "Secciones de Profiler en Visual Studio para Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "Secciones de Profiler en Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barra de herramientas** – ubicado en la parte superior del generador de perfiles, esto ofrece opciones para iniciar o detener la generación de perfiles, seleccione un proceso de destino, ver el tiempo de ejecución de la aplicación y seleccione las vistas de división que componen la aplicación del generador de perfiles.
- **Instrumentar lista** : enumera todos los instrumentos cargados para la sesión de generación de perfiles.
- **Trazar el gráfico** – estos gráficos se relacionan con horizontalmente los instrumentos pertinentes en la lista de instrumentación. Un control deslizante (se muestra debajo de Profiler de tiempo) puede utilizarse para cambiar la escala.
- **Instrumentar el área de detalles** -contiene datos que se muestran en la vista seleccionada del instrumento actual. Veremos estas vistas con más detalle en la sección siguiente.
- **Vista de inspector** : este archivo contiene secciones que se pueden seleccionar el control segmentado. Las secciones son dependientes en el instrumento seleccionado e incluye: opciones de configuración, las estadísticas, información de seguimiento de la pila y la ruta de acceso a las raíces.

### <a name="allocations"></a>Asignaciones

El instrumento de asignaciones proporciona información detallada acerca de los objetos en la aplicación tal como se crean y se recolectan.

En la parte superior del generador de perfiles es el gráfico de las asignaciones, que muestra la cantidad de memoria asignada a intervalos regulares durante la generación de perfiles. Actualmente el gráfico de las asignaciones es el número total de asignaciones y no el tamaño del montón en ese momento dado. En cierto sentido, nunca se desplazará hacia abajo, solo aumentará. Esto incluye los objetos asignados en la pila. Según la versión en tiempo de ejecución que se usa, el gráfico puede parecer diferente, incluso para la misma aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](images/allocations1.png "Las asignaciones instrumentos")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "Las asignaciones instrumentos")](images/allocations1-vs.png#lightbox)

-----

Hay diferentes vistas de datos en el acto de asignaciones, que permiten a los desarrolladores analizar cómo su aplicación está utilizando y liberar memoria. Estas vistas se describen a continuación:

 -   **Las asignaciones de** : se muestra una lista de todas las asignaciones y las agrupa por nombre de clase. Esto proporciona una excelente introducción de clases y métodos que se va a usar, con qué frecuencia se utilizan y el tamaño de las clases utilizadas colectivo. Hacer doble clic en una clase, se mostrará la memoria asignada: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/allocations3.png "Pestaña asignaciones")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "Pestaña asignaciones")](images/allocations2-vs.png#lightbox)

-----

Para las asignaciones de la vista del Inspector proporciona opciones de filtrado y agrupación de objetos, lo que proporciona las estadísticas en la memoria asignada y las asignaciones principales, así como las vistas de seguimiento de pila y la ruta de acceso a raíz.

 -   **Árbol de llamadas** : Esto muestra el árbol de llamadas completa de todos los subprocesos en la aplicación e incluye información acerca de la memoria asignada en cada nodo. Cuando se selecciona un elemento en la lista, aparecerán todos los nodos del mismo nivel gris. Puede expandir el árbol o haga doble clic en el elemento que se va a profundizar en él. Cuando se muestra esta vista de datos, la vista de inspector de configuración de información puede utilizarse para cambiar la forma en que se presenta. Actualmente hay dos opciones:
    1.  **Invierte el árbol de llamadas** : se considera que el seguimiento de pila de arriba a abajo. Esto es una opción de vista práctico ya que indica los métodos más profundo donde dedica a la CPU el tiempo.
    2.  **Separado por subproceso** : esta opción organiza el árbol de llamadas por subproceso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/allocations2.png "Pestaña árbol de llamadas")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "Pestaña árbol de llamadas")](images/allocations3-vs.png#lightbox)

-----

 -   **Las instantáneas** : este panel muestra información acerca de las instantáneas de memoria. Para generar estos durante la generación de perfiles de una aplicación en directo, haga clic en el _cámara_ botón en la barra de herramientas en cada momento en que gustaría ver qué memoria se conservan y se suelta. A continuación, puede hacer clic en cada instantánea para explorar lo que sucede en segundo plano. Tenga en cuenta que las instantáneas se pueden realizar cuando se activa una aplicación de generación de perfiles. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/allocations4.png "Pestaña de instantáneas")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "Pestaña de instantáneas")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Profiler de tiempo

El instrumento de tiempo Profiler mide exactamente cuánto tiempo se dedica a cada método de una aplicación. La aplicación está en pausa a intervalos regulares y se ejecuta un seguimiento de pila en cada subproceso activo. Cada fila en el área de detalles del instrumento muestra la ruta de acceso de ejecución que se ha seguido.

El diagrama de caja, como se muestra en la captura de pantalla siguiente, muestra el número de muestras que recibe la aplicación mientras se ejecuta:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento de Profiler de tiempo](images/time1.png)](images/time1.png#lightbox) 

[![Tiempo Profiler instrumento: lista de ejemplos](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento de Profiler de tiempo](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Tiempo Profiler instrumento: lista de ejemplos](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Árbol de llamadas** – muestra tiempo empleado en cada método:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/time2.png "Tiempo Profiler instrumento: árbol de llamadas")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "Tiempo Profiler instrumento: árbol de llamadas")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Ciclos

Mediante el uso de C# y F# código administrado, puede ser bastante común y Lamentablemente bastante fácil crear referencias a objetos que nunca se eliminará. Este instrumento le permite identificar esos objetos y mostrar los ciclos que se hace referencia en la aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento de ciclos](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento de ciclos](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Generar perfiles de aplicaciones

Actualmente, se pueden generar perfiles solo las configuraciones de depuración predeterminado.

Si genera perfiles de una aplicación con cualquier otra configuración, aparecerá el cuadro de diálogo de mensaje siguiente:


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Cuadro de diálogo Error de generación de perfiles](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "Cuadro de diálogo Error de generación de perfiles")](images/image1vs.png#lightbox) 

-----

Seleccione **actualización** para continuar.

### <a name="sgen-garbage-collector-and-profiling"></a>El recolector de elementos no utilizados de SGen y generación de perfiles

El [SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/) recolector de elementos no utilizados se usa para todas las plataformas Xamarin.

SGen es un GC generacional, que asigna objetos de una aplicación en tres montones: vivero, montón principal y el espacio de objeto grande. Esto permite la ejecución más rápida de elementos no utilizados. SGen es actualmente el GC de forma predeterminada para Xamarin.Android y Xamarin.iOS unificada de aplicaciones.

Aplicación de Xamarin.iOS mediante la API clásica usa el GC Boehm – un recolector no generacional conservador. Aunque sea conservador, es menos probable liberar la memoria disponible, lo que puede provocar resultados incorrectos cuando se usa el generador de perfiles. Por este motivo, no se puede usar el instrumento de asignaciones con el recolector de elementos no utilizados Boehm.

Mientras se le pedirá con un cuadro de diálogo de mensaje si la aplicación usa el GC Boehm, Xamarin recomienda no cambiar la aplicación de iOS existente que use Boehm a SGen sin el cuidado de investigación y pruebas minuciosas. Xamarin también no recomienda cambiar a SGen para generación de perfiles y, a continuación, cambiar, como estos resultados no proporcionará precisas bancos de pruebas de uso de memoria.

Para obtener más información sobre la administración de memoria, consulte el [memoria y procedimientos recomendados de rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md) guía.

## <a name="summary"></a>Resumen

En esta guía explicamos qué generación de perfiles es y cómo esto supone una mejora para el desarrollador. A continuación, presentamos al Xamarin Profiler, que proporciona algunas historial y la información de su funcionamiento. Por último se viajado por las características de la Profiler Xamarin y se han explorado las asignaciones y tiempo Profiler Instruments.

## <a name="related-links"></a>Vínculos relacionados

- [Prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de la versión](https://developer.xamarin.com/releases/profiler/preview/)
