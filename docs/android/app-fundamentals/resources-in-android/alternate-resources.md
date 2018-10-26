---
title: Recursos alternativos
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 0384d96ddc96f8d0b16a42f691305f26ea25881d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108761"
---
# <a name="alternate-resources"></a>Recursos alternativos

Recursos alternativos son aquellos recursos que tienen como destino un dispositivo específico o una configuración de tiempo de ejecución, como el idioma actual, el tamaño de pantalla en particular o la densidad de píxeles. Si Android puede coincidir con un recurso que es más específico para un determinado dispositivo o una configuración que el recurso predeterminado, ese recurso se usará en su lugar. Si no encuentra un recurso alternativo que coincida con la configuración actual, se cargarán los recursos predeterminados. Cómo Android decide qué recursos se utilizarán en una aplicación se tratarán con más detalle a continuación, en la sección ubicación de recursos

Recursos alternativos se organizan como un subdirectorio dentro de la carpeta de recursos según el tipo de recurso, al igual que los recursos predeterminados. El nombre del subdirectorio recursos alternativo está en el formulario: _ResourceType_-_calificador_

*Calificador* es un nombre que identifica una configuración de dispositivo específico.
Puede haber más de un calificador en un nombre, cada uno de ellos separados por un guión. Por ejemplo, la captura de pantalla siguiente muestra un proyecto simple que tiene recursos alternativos para varias configuraciones, como la configuración regional, densidad de pantalla, orientación y tamaño de pantalla:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

Las siguientes reglas se aplican al agregar calificadores a un tipo de recurso:

1. Puede haber más de un calificador, con cada calificador separado por un guión.

2. Los calificadores que es posible especificados una sola vez.

3. Calificadores deben estar en el orden en que aparecen en la tabla siguiente.

Los calificadores posibles se muestran a continuación como referencia:

- **MCC y MNC** &ndash; el [código de país móvil](http://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) y, opcionalmente, el [código de la red móvil](http://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). La tarjeta SIM proporcionará la MCC, mientras que la red que está conectado el dispositivo que proporcionará el MNC. Aunque es posible que las configuraciones regionales de destino mediante el código de país móvil, el enfoque recomendado es usar el calificador de idioma especificado a continuación. Por ejemplo, para los recursos de destino va a Alemania, sería el calificador `mcc262`. A los recursos de destino de T-Mobile en Estados Unidos, el calificador es `mcc310-mnc026`.
  Para obtener una lista completa de códigos de país móvil y de red móvil consulte <http://mcc-mnc.com/>.

- **Lenguaje** &ndash; dos letras [código ISO 639-1 lenguaje](http://en.wikipedia.org/wiki/ISO_639-1) y, opcionalmente, seguido de dos letras [código de región ISO 3166-alpha-2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
  Si se proporcionan los calificadores de ambos, se separan mediante un `-r`. Por ejemplo, para configuraciones regionales de francés de destino, a continuación, el calificador de `fr` se utiliza. Para establecer como destino las configuraciones regionales francés canadiense, la `fr-rCA` se utilizaría. Para obtener una lista completa de códigos de idioma y región, consulte [códigos para la representación de los nombres de lenguajes](http://www.loc.gov/standards/iso639-2/php/English_list.php) y [elementos de código y los nombres de país](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Ancho mínimo** &ndash; especifica el ancho de pantalla más pequeño que la aplicación está diseñada para ejecutarse en. Tratan con más detalle en [creando recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3.2) y versiones posteriores. Por ejemplo, el calificador `sw320dp` se usa para los dispositivos de destino cuyo alto y ancho sea al menos 320dp.

- **Ancho disponible** &ndash; el ancho mínimo de la pantalla en el formato de w*N*dp, donde *N* es el ancho en la densidad de píxeles independientes.
  Puede cambiar este valor como el usuario gira el dispositivo. Tratan con más detalle en [creando recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en nivel de API 13 (Android 3.2) y versiones posteriores. Ejemplo: el w720dp calificador se usa como destino dispositivos que tienen un ancho de 720dp mínimos.

- **Alto disponible** &ndash; el alto mínimo de la pantalla en el formato de h*N*dp, donde *N* es el alto en dp. Puede cambiar este valor como el usuario gira el dispositivo. Tratan con más detalle en [creando recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en nivel de API 13 (Android 3.2) y versiones posteriores. Por ejemplo, el h720dp calificador se usa como destino dispositivos que tienen una altura de 720dp mínimos

- **Tamaño de pantalla** &ndash; este calificador es una generalización que estos recursos son para el tamaño de pantalla. Se trata en detalle en [creando recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Los valores posibles son `small`, `normal`, `large` y `xlarge`. Agregado en el nivel de API 9 (Android 2.3 o Android 2.3.1/Android 2.3.2)

- **Aspecto de pantalla** &ndash; se basa en la relación de aspecto, no la orientación de pantalla. Una pantalla larga es más amplia. Agregado en el nivel de API 4 (Android 1.6). Los valores posibles son largos y notlong.

- **Orientación de pantalla** &ndash; orientación vertical u horizontal de la pantalla. Esto puede cambiar durante la vigencia de una aplicación.
  Los valores posibles son `port` y `land`.

- **Modo de acoplamiento** &ndash; para acoplar los dispositivos en un automóvil o acoplar un departamento de soporte técnico. Se agregó en el nivel de API 8 (Android 2.2. x). Los valores posibles son `car` y `desk`.

- **Modo nocturno** &ndash; o no se ejecuta la aplicación durante la noche o en el día. Esto puede cambiar durante la vigencia de una aplicación y está pensado para proporcionar a los desarrolladores una oportunidad de usar las versiones de una interfaz más oscuras durante la noche. Se agregó en el nivel de API 8 (Android 2.2. x). Los valores posibles son `night` y `notnight`.

- **Densidad de píxeles (PPP) de la pantalla** &ndash; el número de píxeles en un área determinada en la pantalla física. Normalmente, expresado en puntos por pulgada (PPP). Los valores posibles son:

    - `ldpi` &ndash; Pantallas de baja densidad.

    - `mdpi` &ndash; Pantallas de densidad media

    - `hdpi` &ndash; Pantallas de alta densidad

    - `xhdpi` &ndash; Pantallas de densidad extra alta

    - `nodpi` &ndash; Recursos que no se escala

    - `tvdpi` &ndash; Se introdujo en el nivel de API 13 (Android 3.2) para las pantallas entre mdpi y hdpi.

- **Tipo de pantalla táctil** &ndash; especifica el tipo de pantalla táctil que un dispositivo puede tener. Los valores posibles son `notouch` (sin pantalla táctil), `stylus` (electromagnético pantalla táctil adecuada para un lápiz óptico), y `finger` (una pantalla táctil).

- **Disponibilidad de teclado** &ndash; especifica qué tipo de teclado está disponible. Esto podría cambiar durante la vigencia de una aplicación &ndash; por ejemplo, cuando un usuario abre un teclado de hardware. Los valores posibles son:

    - `keysexposed` &ndash; El dispositivo tiene un teclado disponible. Si no hay ningún teclado software habilitado, a continuación, sólo se utiliza cuando se abre el teclado de hardware.

    - `keyshidden` &ndash; El dispositivo tiene un teclado de hardware, pero está oculta y no se habilita teclado de software.

    - `keyssoft` &ndash; el dispositivo tiene un teclado de software habilitado.

- **Método de entrada de texto principal** &ndash; utilizar para especificar qué tipos de claves de hardware están disponibles para la entrada. Los valores posibles son:

    - `nokeys` &ndash; No hay ninguna clave de hardware para la entrada.

    - `qwerty` &ndash; Hay disponible un teclado qwerty.

    - `12key` &ndash; Hay un teclado de hardware de 12 teclas


- **Disponibilidad de la clave de navegación** &ndash; cuando 5-way o cruceta navegación (teclas de dirección) está disponible. Esto puede cambiar durante la vigencia de la aplicación. Los valores posibles son:

    - `navexposed` &ndash; las teclas de navegación están disponibles para el usuario

    - `navhidden` &ndash; las teclas de navegación no están disponibles.

-  **Método de navegación principal "no-touch"** &ndash; el tipo de navegación disponible en el dispositivo. Los valores posibles son:

    - `nonav` &ndash; el método de navegación única disponible es la pantalla táctil

    - `dpad` &ndash; un control de dirección (teclas de dirección) está disponible para la navegación

    - `trackball` &ndash; el dispositivo tiene una bola de seguimiento para la navegación

    - `wheel` &ndash; el escenario poco común donde hay uno o más direccional ruedas disponibles

-  **Versión de la plataforma (nivel de API)** &ndash; nivel de la API compatible con el dispositivo en el formato v*N*, donde *N* es el nivel de API que se ha elegido. Por ejemplo, v11 destinará un nivel de API 11 (Android 3.0) dispositivo.


Para obtener información más completa sobre recursos vea calificadores [proporcionar recursos](http://developer.android.com/guide/topics/resources/providing-resources.html) en el sitio Web para desarrolladores de Android.


## <a name="how-android-determines-what-resources-to-use"></a>Cómo Android determina qué recursos que se usarán

Es muy probable que una aplicación de Android contiene muchos recursos y posible. Es importante comprender cómo Android seleccionará los recursos de una aplicación cuando se ejecuta en un dispositivo.

Android determina los recursos base iterando por la prueba de las reglas siguiente:

- **Eliminar calificadores contradictorias** &ndash; por ejemplo, si la orientación del dispositivo es vertical, se rechazará todos los directorios de recursos horizontal.

- **Omitir calificadores no admitidas** &ndash; no todos los calificadores están disponibles para todos los niveles de API. Si un directorio de recursos contiene un calificador que no es compatible con el dispositivo, se omitirá dicho directorio de recursos.

- **Identificar el calificador de prioridad más alto siguiente** &ndash; que hace referencia a la tabla anterior la selección del calificador de prioridad más alto siguiente (de arriba a abajo).

- **Mantener los directorios de recursos para el calificador** &ndash; si existen los directorios de recursos que coinciden con el calificador de la tabla anterior, seleccione el calificador de prioridad más alto siguiente (de arriba a abajo).

Estas reglas también se muestran en el siguiente diagrama:

[![Diagrama de flujo de recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Cuando el sistema busca recursos específicos de la densidad y no los encuentra, intentará buscar otros recursos específicos de densidad y escalarlos. Android no puede usar necesariamente los recursos predeterminados.
Por ejemplo, cuando busca un recurso de baja densidad y no está disponible, Android puede seleccionar versión de alta densidad de los recursos a través de los recursos predeterminados o densidad Media. Para hacerlo porque el recurso de alta densidad se puede escalar hacia abajo por un factor de 0,5, lo que dará lugar a reducir verticalmente un recurso de densidad media, lo que requeriría un factor de 0,75 menos problemas de visibilidad.

Por ejemplo, considere la posibilidad de una aplicación que tiene los siguientes directorios de recursos drawable:

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

Y ahora se ejecuta la aplicación en un dispositivo con la siguiente configuración:

- **Configuración regional** &ndash; en GB
- **Orientación** &ndash; puerto
- **Densidad de pantalla** &ndash; hdpi
- **Tipo de pantalla táctil** &ndash; notouch
- **Método de entrada principal** &ndash; 12key

Para empezar, se eliminan los recursos en francés como entran en conflicto con la configuración regional de `en-GB`, lo que nos deja con:

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

A continuación, se selecciona el calificador de la primera de la tabla de calificadores anterior: MCC y MNC. No hay ningún directorio de recursos que contienen este calificador para que el código MCC/MNC se omite.

El siguiente calificador está seleccionado, que es el idioma. Hay recursos que coinciden con el código de idioma. Todos los directorios de recursos que no coinciden con el código de idioma de `en` se rechazan, para que la lista de recursos es ahora:

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

Es el siguiente calificador que está presente para la orientación de pantalla, por lo que todos los directorios de recursos que no coinciden con la orientación de pantalla de `port` se eliminan:

    drawable-en-port
    drawable-en-port-ldpi

A continuación es el calificador de densidad de la pantalla, `ldpi`, que da como resultado la exclusión de un directorio de recursos más:

    drawable-en-port-ldpi

Como resultado de este proceso, Android usará los recursos pueden dibujables en el directorio de recursos `drawable-en-port-ldpi` para el dispositivo.

> [!NOTE]
> Los calificadores de tamaño de pantalla proporcionan una excepción a este proceso de selección. Es posible para Android seleccionar los recursos que están diseñados para una pantalla más pequeña que proporciona el dispositivo actual. Por ejemplo, un dispositivo de pantalla de gran tamaño puede usar los recursos se proporcionan para una pantalla de tamaño normal. Sin embargo la inversa de este no es cierto: el mismo dispositivo de pantalla de gran tamaño no usará los recursos proporcionados para una pantalla extra grande. Si Android no puede encontrar un conjunto de recursos que coincide con un tamaño de pantalla dadas, la aplicación se bloqueará.
