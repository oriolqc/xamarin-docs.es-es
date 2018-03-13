---
title: Recursos alternativos
ms.topic: article
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 7ebbf2a9215c8472ae2f286728cb2f819e8331cb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="alternate-resources"></a>Recursos alternativos

Recursos alternativos son aquellos recursos que tienen como destino un dispositivo específico o la configuración de tiempo de ejecución, como el idioma actual, el tamaño de pantalla determinado o la densidad de píxeles. Si Android puede hacer coincidir un recurso que sea más específico para un determinado dispositivo o la configuración que el recurso de manera predeterminada, ese recurso se usará en su lugar. Si no encuentra un recurso alternativo que coincide con la configuración actual, se cargarán los recursos predeterminados. Cómo Android decide qué recursos se usará en una aplicación se tratarán con más detalle a continuación, en la sección ubicación de recursos

Recursos alternativos se organizan como un subdirectorio dentro de la carpeta de recursos según el tipo de recurso, al igual que los recursos de forma predeterminada. El nombre del subdirectorio recurso alternativo está en el formulario: _ResourceType_-_calificador_

*Calificador de* es un nombre que identifica una configuración de dispositivo específico.
Puede haber más de un calificador en un nombre, cada uno de ellos separados por un guión. Por ejemplo, la captura de pantalla siguiente muestra un proyecto simple que tiene recursos alternativos para varias configuraciones, como la configuración regional, la densidad de la pantalla, tamaño de pantalla y la orientación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recursos alternativos](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Recursos alternativos](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

Al agregar calificadores a un tipo de recurso, se aplican las siguientes reglas:

1. Puede haber más de un calificador, con cada calificador separado por un guión.

2. Los calificadores que especifique puede ser una sola vez.

3. Calificadores deben estar en el orden en que aparecen en la tabla siguiente.

Los calificadores posibles se muestran a continuación como referencia:

- **MCC y MNC** &ndash; el [código de país para teléfonos móviles](http://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) y, opcionalmente, el [código de la red móvil](http://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). La tarjeta SIM proporcionará la MCC, mientras que la red que el dispositivo está conectado a proporcionará la MNC. Aunque es posible configuraciones regionales de destino utilizando el código de país para teléfonos móviles, el enfoque recomendado es utilizar el calificador de idioma especificado más abajo. Por ejemplo, a los recursos de destino en Alemania, sería el calificador `mcc262`. Recursos de destino para T-Mobile la zona horaria del Pacífico, el calificador es `mcc310-mnc026`.
  Encontrará una lista completa de códigos de país para teléfonos móviles y de red móvil <http://mcclist.com/>.

- **Idioma** &ndash; dos letras [el código ISO 639-1 lenguaje](http://en.wikipedia.org/wiki/ISO_639-1) y, opcionalmente, seguido de dos letras [código de región ISO 3166-alpha 2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
  Si se proporcionan ambos calificadores, se separan mediante un `-r`. Por ejemplo, para las configuraciones regionales de francés de destino, a continuación, el calificador de `fr` se utiliza. Para tener como destino las configuraciones regionales francés canadiense, la `fr-rCA` lo utiliza. Para obtener una lista completa de códigos de idioma y región, consulte [códigos para la representación de nombres de idiomas](http://www.loc.gov/standards/iso639-2/php/English_list.php) y [nombres de países y elementos de código](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Ancho menor** &ndash; especifica el ancho de pantalla más pequeño que la aplicación está pensada para ejecutar en. Tratan con más detalle en [crear recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Está disponible en el nivel de API 13 (Android 3.2) y versiones posteriores. Por ejemplo, el calificador `sw320dp` se utiliza para dispositivos de destino cuyo alto y ancho es al menos 320dp.

- **Ancho disponible** &ndash; el ancho mínimo de la pantalla en el formato w*N*dp, donde *N* es el ancho de la densidad de píxeles independientes.
  Este valor puede cambiar como el usuario gira el dispositivo. Tratan con más detalle en [crear recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Está disponible en el nivel de API 13 (Android 3.2) y versiones posteriores. Ejemplo: el w720dp calificador se usa para tener como destino dispositivos que tienen un ancho de 720dp mínimos.

- **Alto disponible** &ndash; el alto mínimo de la pantalla en el formato h*N*dp, donde *N* es el alto en el punto de distribución. Este valor puede cambiar como el usuario gira el dispositivo. Tratan con más detalle en [crear recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Está disponible en el nivel de API 13 (Android 3.2) y versiones posteriores. Por ejemplo, el h720dp calificador se usa como destino dispositivos que tienen una altura de 720dp mínimos

- **Tamaño de la pantalla** &ndash; este calificador es una generalización del tamaño de pantalla que se encuentran estos recursos para. Se trata con más detalle en [crear recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Los valores posibles son `small`, `normal`, `large` y `xlarge`. Agregado en el nivel de API 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- **Aspecto de pantalla** &ndash; se basa en la relación de aspecto, no la orientación de la pantalla. Una pantalla largo es más amplia. Agregado en el nivel de API 4 (Android 1.6). Los valores posibles son largos y notlong.

- **Orientación de pantalla** &ndash; orientación vertical u horizontal de la pantalla. Esto puede cambiar durante la vigencia de una aplicación.
  Los valores posibles son `port` y `land`.

- **Acoplar el modo** &ndash; para acoplar dispositivos en un automóvil o acoplar un departamento de soporte. Agregado en el nivel de API 8 (Android 2.2. x). Los valores posibles son `car` y `desk`.

- **Modo nocturno** &ndash; o no se ejecuta la aplicación durante la noche o en el día. Esto puede cambiar durante la vigencia de una aplicación y está pensado para proporcionar a los programadores una oportunidad para usar versiones más oscuras de una interfaz por la noche. Agregado en el nivel de API 8 (Android 2.2. x). Los valores posibles son `night` y `notnight`.

- **Densidad de píxeles (PPP) de la pantalla** &ndash; el número de píxeles en un área determinado en la pantalla física. Normalmente se expresa como puntos por pulgada (PPP). Los valores posibles son:

    - `ldpi` &ndash; Pantallas de densidad baja.

    - `mdpi` &ndash; Pantallas de densidad media

    - `hdpi` &ndash; Pantallas de alta densidad

    - `xhdpi` &ndash; Pantallas de muy alta densidad

    - `nodpi` &ndash; Recursos que no se puede escalar

    - `tvdpi` &ndash; Se introdujo en el nivel de API 13 (Android 3.2) para las pantallas entre mdpi y hdpi.

- **Tipo de pantalla táctil** &ndash; especifica el tipo de pantalla táctil puede tener un dispositivo. Los valores posibles son `notouch` (ninguna pantalla táctil), `stylus` (resistente pantalla táctil adecuada para un lápiz óptico), y `finger` (una pantalla táctil).

- **Disponibilidad de teclado** &ndash; especifica qué tipo de teclado está disponible. Esto podría variar durante la duración de una aplicación &ndash; por ejemplo, cuando un usuario abre un teclado de hardware. Los valores posibles son:

    - `keysexposed` &ndash; El dispositivo tiene un teclado disponible. Si no hay ninguna actividad del teclado software habilitado, a continuación, solo se utiliza cuando se abre el teclado de hardware.

    - `keyshidden` &ndash; El dispositivo tiene un teclado de hardware, pero está oculta y no se habilita teclado de software.

    - `keyssoft` &ndash; el dispositivo tiene un teclado de software habilitado.

- **Método de entrada de texto principal** &ndash; Use para especificar qué tipos de claves de hardware están disponibles para la entrada. Los valores posibles son:

    - `nokeys` &ndash; No hay ninguna clave de hardware para la entrada.

    - `qwerty` &ndash; Hay disponible un teclado qwerty.

    - `12key` &ndash; Hay un teclado de hardware de la clave de 12


- **Disponibilidad de la clave de navegación** &ndash; para cuándo está disponible la forma de 5 o d-panel navegación (teclas de dirección). Esto puede cambiar durante la vigencia de la aplicación. Los valores posibles son:

    - `navexposed` &ndash; las teclas de navegación están disponibles para el usuario

    - `navhidden` &ndash; las teclas de navegación no están disponibles.

-  **Método de navegación principal "no-touch"** &ndash; el tipo de navegación disponible en el dispositivo. Los valores posibles son:

    - `nonav` &ndash; la funcionalidad de navegación única disponible es la pantalla táctil

    - `dpad` &ndash; un control de dirección (teclas de dirección) está disponible para la navegación

    - `trackball` &ndash; el dispositivo tiene una bola de seguimiento para la navegación

    - `wheel` &ndash; el escenario poco común donde hay uno o más direccional ruedas disponibles

-  **Versión de la plataforma (API nivel)** &ndash; nivel de la API admitida por el dispositivo en el formato v*N*, donde *N* es el nivel de API que se destina. Por ejemplo, v11 tendrán como destino un nivel de API 11 (Android 3.0) dispositivo.


Para obtener información más completa sobre recursos vea calificadores [proporcionar recursos](http://developer.android.com/guide/topics/resources/providing-resources.html) en el sitio Web de desarrolladores de Android.


## <a name="how-android-determines-what-resources-to-use"></a>Cómo Android determina los recursos que se utilizan

Es muy posible y es probable que una aplicación Android contendrá muchos recursos. Es importante entender cómo Android seleccionará los recursos de una aplicación cuando se ejecuta en un dispositivo.

Android determina los recursos base iterando por la siguiente prueba de reglas:

- **Eliminar los calificadores contradictorios** &ndash; por ejemplo, si la orientación del dispositivo es vertical, se rechazarán todos los directorios de recursos de horizontal.

- **Omitir calificadores no admitidos** &ndash; no todos los calificadores están disponibles para todos los niveles de API. Si un directorio de recursos contiene un calificador que no es compatible con el dispositivo, se omitirá dicho directorio de recursos.

- **Identificar el calificador de prioridad más alto siguiente** &ndash; que hace referencia a la tabla anterior la selección del calificador de prioridad más alto siguiente (de arriba a abajo).

- **Mantener los directorios de recursos para el calificador** &ndash; si existen los directorios de recursos que coinciden con el calificador de la tabla anterior, seleccione el calificador de prioridad más alto siguiente (de arriba a abajo).

Estas reglas también se muestran en el siguiente diagrama de flujo:

[![Diagrama de flujo de recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Cuando el sistema busca recursos específicos de la densidad y no encuentra, intentará buscar otros recursos específicos de densidad y escalarlos. Android no puede usar los recursos predeterminados necesariamente.
Por ejemplo, cuando busca un recurso de baja densidad y no está disponible, Android puede seleccionar alta densidad y versión del recurso sobre los recursos predeterminados o medio de densidad. Esto consigue porque el recurso de alta densidad y se puede escalar hacia abajo en un factor de 0,5, lo que dará como resultado en menos problemas de visibilidad que la reducción de un recurso de densidad media que requieran un factor de 0,75.

Por ejemplo, considere la posibilidad de una aplicación que tenga los siguientes directorios de recursos con estas características:

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

Y ahora se ejecuta la aplicación en un dispositivo con la siguiente configuración:

- **Configuración regional** &ndash; en-GB
- **Orientación** &ndash; puerto
- **Pantalla densidad** &ndash; hdpi
- **Tipo de pantalla táctil** &ndash; notouch
- **Método de entrada principal** &ndash; 12key

Para comenzar con, se eliminan los recursos en francés como entran en conflicto con la configuración regional de `en-GB`, lo que nos deja con:

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

A continuación, se selecciona el primer calificador de la tabla de calificadores anterior: MCC y MNC. No hay ningún directorio de recursos que contienen este calificador para que el código MCC/MNC se omite.

Se selecciona el siguiente calificador, que es el idioma. Hay recursos que coinciden con el código de idioma. Todos los directorios de recursos que no coincide con el código de idioma `en` se rechazan, por lo que la lista de recursos es ahora:

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

El calificador siguiente que se encuentra es para la orientación de pantalla, por lo que todos los directorios de recursos que no coincide con la orientación de pantalla de `port` se eliminan:

    drawable-en-port
    drawable-en-port-ldpi

A continuación figura el calificador de densidad de la pantalla, `ldpi`, que da como resultado la exclusión de un directorio de recursos más:

    drawable-en-port-ldpi

Como resultado de este proceso, Android usará los recursos pueden dibujar en el directorio de recursos `drawable-en-port-ldpi` para el dispositivo.

> [!NOTE]
> Los calificadores de tamaño de pantalla proporcionan una excepción a este proceso de selección. Es posible para Android seleccionar los recursos que están diseñados para una pantalla más pequeña que proporciona el dispositivo actual. Por ejemplo, un dispositivo de pantalla de gran tamaño puede utilizar los recursos que proporcione para una pantalla de tamaño normal. Sin embargo el inverso de esto no es cierto: el mismo dispositivo de pantalla de gran tamaño no usará los recursos proporcionados para una pantalla extra grande. Si Android no puede encontrar un conjunto de recursos que coincide con un tamaño de pantalla especificado, la aplicación se bloqueará.
