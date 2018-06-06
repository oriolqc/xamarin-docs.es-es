---
title: Localización de interfaz de usuario de aplicación
description: Este documento describe los conceptos de multiplataforma de internacionalización y localización y examina cómo que afecten al diseño de la aplicación.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 299fe28a896c2bf2e5c420b330b8e8bde7d9dc22
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781961"
---
# <a name="localization"></a>Localización

Esta guía presentan los conceptos que subyacen *internacionalización* y *localización* y vínculos con instrucciones sobre cómo generar aplicaciones móviles de Xamarin con esos conceptos.

Si desea omitir directamente a los detalles técnicos de localizar aplicaciones Xamarin, comience con uno de estos artículos de procedimientos específicos de la plataforma:

- [**Xamarin.Forms** ](~/xamarin-forms/app-fundamentals/localization/index.md) localización entre plataformas con RESX (archivos).
- [**Xamarin.iOS** ](~/ios/app-fundamentals/localization/index.md) localización de plataforma nativa.
- [**Xamarin.Android** ](~/android/app-fundamentals/localization.md) localización de plataforma nativa.

## <a name="i18n-and-l10n"></a>I18N y L10n

*Internacionalización* es el proceso de hacer que su código capaz de mostrar distintos idiomas y adaptar su presentación para diferentes configuraciones regionales (por ejemplo, formato de número y fecha). Esto se conoce también como *globalización*.

*Localización* es el paso siguiente: creación de recursos (por ejemplo, cadenas e imágenes) para cada idioma y agrupación de ellos con la aplicación internationalize.

Internacionalización reducirla a menudo a i18n: una forma abreviada de 18 letras entre "i" y "n". De igual forma se acorta la localización a L10n: 10 letras entre "L" y "n".

## <a name="overview"></a>Información general

Este documento presentan los conceptos asociados con la internacionalización y localización, y cómo se aplican al desarrollo de aplicaciones móviles en general.
Al diseñar y crear una aplicación, lo que podría tener previamente codificado de forma rígida pero que deben parámetros de localización incluyen:

-   Diseños de pantalla y texto,
-   Iconos, gráficos y colores,
-   Archivos de vídeo y sonidos,
-   Texto dinámico y el formato de texto (por ejemplo, números, moneda y fechas),
 - Cambios de diseño para los idiomas de derecha a izquierda (RTL), y
-   Ordenación de los datos.

Con independencia de qué plataformas móviles, su aplicación está dirigida a estas sugerencias le ayudará a compilar una aplicación localizada de alta calidad.


## <a name="design-considerations"></a>Consideraciones de diseño

Diseñar una aplicación para que sea posible de adaptar su contenido se denomina internacionalización. Esto internacionalización correctamente es más que simplemente permitir para otro idioma cadenas que se va a cargar en tiempo de ejecución: debe permitir una aplicación bien diseñada para que todos los recursos que puede cambiar en función de idioma y configuración regional (incluidas imágenes, sonidos y vídeos) y se puede adaptar formato y diseño para hacer frente a otro tamaño cadenas.

Esta sección describen algunas consideraciones de diseño para tener en cuenta al compilar una aplicación de uso internacional.

### <a name="layouts-and-string-length"></a>Diseños y longitud de cadena

Cadenas de chino y japonés pueden ser muy breve: a veces, uno o dos caracteres pueden ser lo suficientemente significativas para una etiqueta de campo de entrada.

Cadenas de alemán (por ejemplo) pueden ser muy largos; a veces, una palabra relativamente corta en inglés pasa a ser muy larga en otros lenguajes: o bien pase a ser recortadas o else reajuste inesperadamente el diseño.

Comparar las longitudes de cadena de unos pocos elementos en la pantalla principal de iOS en inglés, alemán y japonés:

[![](localization-images/language-compare-sml.png "Longitud de cadena en japonés de vs alemán")](localization-images/language-compare.png#lightbox)

Tenga en cuenta que **configuración** en inglés (8 caracteres) requiere 13 caracteres para la traducción de alemán, pero sólo 2 caracteres en japonés.

Diseños donde la etiqueta de visualización y el campo de entrada son side-by-side son difíciles de trabajar con cuando la longitud de la etiqueta puede variar enormemente. A menudo un diseño que se muestra la etiqueta encima de un campo es más fácil localizar porque todo el ancho de la pantalla está disponible para la etiqueta y la entrada.

Como norma general, si está generando diseños fijos (especialmente los elementos side-by-side) permiten al menos un 50% más ancho que las cadenas de inglés requieren para las etiquetas y texto. Esto no resolver todos los problemas pero le proporcionará un búfer que funcionará en muchos casos.

### <a name="input-validation"></a>Validación de entrada

Tenga cuidado de supuestos al escribir reglas de validación. Puede parecer válido para requerir un campo de texto de entrada "requerir" al menos tres caracteres en inglés, puesto que una sola letra con poca frecuencia tiene ningún significado. En chino y japonés sin embargo, un carácter individual puede ser una entrada válida y una validación de mensajes "al menos 3 caracteres es necesario" no tiene sentido correspondientes a esos idiomas.

Otras tareas aparentemente simples, como validar una dirección de correo electrónico o URL del sitio Web se vuelven más complejos con los caracteres no se limitan al subconjunto de ASCII.

Escribir las reglas de validación con internacionalización en mente, elija las reglas menos restrictivas, o escribir la lógica para que funciona de forma diferente para cada idioma.

### <a name="images-and-color"></a>Imágenes y colores

No todas las imágenes deben cambiar en función de la elección del idioma del usuario. Muchos iconos o fotos estarán adecuado para todos los usuarios, no importa qué idioma habla.
Algunos recursos tienen sentido para localizar aunque, como:

 - Las imágenes que representan personas o ubicaciones específicas: la aplicación puede se sienten más relevante para los usuarios si muestra locales personas y ubicaciones.
 - Iconos: algunos iconography puede ser específica de la referencia cultural y hacer que la aplicación fáciles de usar mediante la localización de las imágenes para reflejar descripción local.
 - Colores: algunas referencias culturales comprender colores de forma diferente: rojo podría significar advertencia en una región, pero le deseamos buena suerte en otro. Póngase en contacto con hablantes nativos al diseñar la aplicación para determinar si se debe generar un mecanismo para localizar los colores.


### <a name="videos-and-sound"></a>Vídeos y sonido

Vídeos y presentan desafíos especiales sonidos al localizar una aplicación, porque aunque es relativamente fácil de obtener cadenas traducidas, grabación voiceover varios realiza un seguimiento o clips de vídeo pueden ser caro y difícil.

Varias copias de archivos de vídeo y sonidos significativamente también pueden aumentar el tamaño de la aplicación (sobre todo si se localiza en un gran número de idiomas o tiene un gran número de archivos multimedia). Considere la posibilidad de descargar solo los recursos de idioma necesarios una vez que el usuario ha instalado la aplicación, pero esto también podría provocar una mala experiencia de usuario en redes lentas.

A menudo hay varias formas de solucionar problemas de localización: lo más importante es tenerlos en cuenta por adelantado y asegúrese de que la aplicación está diseñada para que se ocupe de ellos.


### <a name="dates-times-numbers-and-currency"></a>Fechas, horas, números y moneda

Si usa funciones de formato. NET, no olvide especificar la referencia cultural para que los separadores decimales se analizan correctamente (y evitar excepciones de conversión que se producen). Por ejemplo 1.99 y 1,99 son representaciones decimales válidos según la configuración regional.

Cuando los datos proceden de un origen conocido (es decir. desde su propio código o un servicio web que usted controla) puede codificar un identificador de referencia cultural que coincide con el formato como InvariantCulture que funcionará para el formato de idioma de inglés estándar.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Si el usuario de la aplicación es que se va a la entrada de los datos, analizarlos con una instancia de CultureInfo que refleja su configuración regional:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Consulte la [analizar cadenas numéricas](http://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) y [analizar cadenas de fecha y hora](http://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) artículos de MSDN para obtener información adicional.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Idiomas de derecha a izquierda (RTL)

Algunos lenguajes, como el árabe y hebreo, urdú (por ejemplo), se leen de derecha a izquierda.
Aplicaciones que admiten estos idiomas deben usar diseños de pantalla que se adapten para los lectores de derecha a izquierda, por ejemplo:

 - Debe ser texto alineado a la derecha.
 - Las etiquetas aparecerán a la derecha de los campos de entrada.
 - Por lo general se invierte la colocación del botón predeterminado.
 - Navegación jerárquica Deslizar rápidamente y animación (y otras metáforas relacionadas con la navegación y las animaciones) que usar la dirección para que también se debe invertir contexto.

IOS y Android admiten diseños de derecha a izquierda y la representación de la fuente, con características integradas que le ayudan a realizar los ajustes anteriores. Xamarin.Forms no admite actualmente automáticamente la representación de derecha a izquierda.

### <a name="sorting"></a>Ordenar

Idiomas diferentes definan el criterio de ordenación de los alfabetos de forma diferente, incluso cuando utilicen el mismo juego de caracteres.

Consulte la [detalle de comparación de cadenas](http://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) en [prácticas recomendadas para el uso de cadenas en .NET Framework](http://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) para obtener un ejemplo donde idioma (CultureInfo) afecta al criterio de ordenación.

Es poco probable que las capacidades integradas de la base de datos en las plataformas móviles admitirán a ordenación específicos del idioma ordenación por lo que es podrán que deba implementar código adicional en la lógica de negocios.

### <a name="text-search"></a>Búsqueda de texto

Asegúrese de escribir y probar el algoritmo de búsqueda con varios idiomas en mente. Cosas a tener en cuenta incluyen:

 - Autocompletar – si ha compilado una función Autocompletar Asegúrese de orígenes de sugerencias relevantes para el idioma del usuario.
 - ¿La consulta correspondiente a los datos: buscará consultas escritas en un determinado idioma se ejecuta sólo contenido escrito en dicho lenguaje, o en todo el contenido de la aplicación?
 - ¿Lematización: si se ha creado la búsqueda para buscar palabras similares, las raíces de word y otras optimizaciones de búsqueda es esas optimizaciones compiladas para todos los idiomas que se admiten?
 - Ordenación: asegúrese de que los resultados se ordenan correctamente (vea más arriba).


### <a name="data-from-external-sources"></a>Datos de orígenes externos

Muchas aplicaciones descargan datos desde orígenes externos, de Twitter y fuentes RSS para el tiempo, noticias o cotizaciones. Para mostrar esto a un usuario que debe tener en cuenta la posibilidad de que se mostrará una pantalla de información pertinente o es ilegible para ellos.

Hay algunas estrategias que puede usar para probar y asegúrese de que la aplicación muestra datos relevantes para el usuario:

 - Orígenes diferentes: la aplicación podría descargar los datos desde un origen diferente según el idioma o la configuración regional del usuario. Los precios de noticias, el tiempo y las existencias de configuración regional pueden que tenga más sentido que algo descargado desde una fuente de América del Norte.
 - Mostrar localizado: si va a mostrar un Twitter o una foto de fuentes de distribución, debe mostrar los metadatos (por ejemplo, el tiempo empleado) en su propio lenguaje, incluso si el contenido en sí permanece en el idioma original.
 - Traducción: puede generar una opción de traducción en su aplicación para realizar una traducción automática de los datos entrantes. Esto podría ser automática o a discreción del usuario: asegúrese de notificar al usuario si éste está teniendo lugar, ya que no son nunca perfectas traducciones de la máquina.

Esto podría afectar también a los vínculos externos en las pistas de audio o vídeos: al diseñar la aplicación Asegúrese de planear con antelación para originar convierten contenido o para garantizar que los usuarios son informados adecuadamente mediante la interfaz de usuario al contenido no aparecerá en su idioma.


### <a name="dont-over-translate"></a>No se trasladan excesiva

Algunas cadenas de la aplicación podrían no necesario traducir, o como mínimo una atención especial en el traductor. Algunos ejemplos pueden ser:

 - Las direcciones URL: si escribes una dirección URL, puede o no es necesario que se pueden ajustar por idioma. Por ejemplo, facebook.com no requiere traducción detecta automáticamente el idioma en el sitio principal. Otros sitios tienen contenido específico de la configuración regional y desea ofrecer una dirección URL diferente, como yahoo.com frente a yahoo.fr o yahoo.it.
 - Números de teléfono, especialmente aquellos con diferentes códigos de país o números para los llamadores que hablan un idioma determinado.
 - Detalles de contacto: direcciones y otra información podrían varían según el idioma o configuración regional.
 - Marcas comerciales y nombres de producto – algunas cadenas no es necesario traducir porque siempre se escribe en el mismo idioma.

Por último, no olvide incluir instrucciones detalladas para el traductor si ciertas cadenas requieren un tratamiento especial.


### <a name="formatted-text"></a>Texto con formato

No se suele un problema con las aplicaciones móviles porque generalmente no están enriquecido da formato a cadenas. Sin embargo si se requiere texto enriquecido (como el formato de negrita o cursiva) de la aplicación asegurarse el traductor sabe cómo para el formato de entrada, los archivos de cadenas almacenan correctamente y es el formato correcto antes de mostrarlo al usuario (es decir. accidentalmente no permita que los códigos de formato se presentan al usuario).



## <a name="translation-tips"></a>Sugerencias de traducción

Traducir las cadenas utilizadas por una aplicación se considera parte del proceso de localización. Normalmente esta tarea se contrata a un servicio de traducción y se realiza multilingüe personal que puede no conocer su aplicación o su empresa.

Las sugerencias siguientes le ayudará a generar las cadenas que son más fáciles de traducir con precisión y, por tanto, mejorar la calidad de las aplicaciones localizadas.



### <a name="localize-complete-strings-not-words"></a>Localizar cadenas completas, no deben ser palabras

En ocasiones, los desarrolladores adoptan el enfoque de intentar especificar palabras o frases 'fragmentos' para que puedan volver a utilizarlas a lo largo de la aplicación. Por ejemplo, para el texto "tiene 5 mensajes." pueden especificar las siguientes cadenas de traducción

**Incorrecta**:

```csharp
"You have"
"no"
"message"
"messages"
```

y, a continuación, intente crear el corregir frase sobre la marcha en el código mediante la concatenación de cadenas:

**Incorrecta**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Esto se desaconseja** porque no funcionará necesariamente para todos los idiomas y será difícil para el traductor entender el contexto de cada segmento corto. También se provoca para volver a usar de las cadenas traducidas, lo que pueden causar problemas más adelante si se utilizan en varios contextos (y, a continuación, se actualizan).


### <a name="allow-for-parameter-re-ordering"></a>Permitir para volver a ordenar los parámetros

Algunos lenguajes de programación requieren sintaxis adicional para especificar el orden de los parámetros en una cadena, sin embargo, .NET ya es compatible con el concepto de marcadores de posición numerados, por lo que

**Buena**:

```csharp
"a {0} b {1} cde {3}"
```

se pudo traducir el siguiente (donde la posición y el orden de los marcadores de posición se cambia)

```csharp
"{2} {3} f g h {0}"
```

y los tokens se ordenarán como el traductor previsto. No olvide incluir una explicación del contenido de cada marcador de posición cuando se envía la cadena a un traductor.


### <a name="use-multiple-strings-for-cardinality"></a>Usar varias cadenas de cardinalidad

Evite las cadenas como `"You have {0} message/s."` usar las cadenas específicas para cada estado para proporcionar una mejor experiencia de usuario:

**Buena**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

Tendrá que escribir código en su aplicación para evaluar el número que se va a mostrar y elegir la cadena adecuada. Algunas plataformas (incluidos iOS y Android) tienen características integradas para seleccionar automáticamente el mejor cadena plural según las preferencias para el idioma o configuración regional actual.


### <a name="allowing-for-gender"></a>Permitir correspondientes al género

Idiomas latinas a veces usan palabras diferentes según el sexo del sujeto. Si la aplicación conoce sexo, debería permitir las cadenas traducidas reflejarlo.

También es el caso más obvio incluso en inglés, donde las cadenas hacen referencia a una persona específica o un usuario de la aplicación. Por ejemplo, algunos sitios muestran como mensajes `"Bob commented on his post"` por lo que necesita cadenas correspondientes al género masculino, mujer y no binarios o desconocido:

**Buena**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>No volver a usar las cadenas

O más concretamente, no vuelva a usar cadenas simplemente porque son parecidas cuando la propia cadena tiene un propósito diferente o significado.

Por ejemplo: imagine que tiene un conmutador de encendido/apagado de la aplicación y el control de conmutador necesita el texto de 'on' y 'off' para que se adapten. También mostrar el valor de esa configuración en otra parte de la aplicación en una etiqueta de texto. Debe usar cadenas diferentes para la presentación de conmutador en comparación con el estado del modificador (incluso si son la misma cadena en el idioma predeterminado): por ejemplo:

-   "On": se muestra en el propio modificador
-   "Off": se muestra en el propio modificador
-   "On": se muestra en una etiqueta
-   "Off": se muestra en una etiqueta

Esto proporciona una flexibilidad máxima para el traductor de:

-   Por motivos de diseño, quizás el propio modificador utiliza minúsculas "on" y "off" pero la etiqueta para mostrar usa mayúsculas "On" y "Off".
-   Algunos idiomas que tenga el valor del modificador que se puede abreviar para ajustarse en el control de interfaz de usuario, mientras que la palabra completa (traducida) puede aparecer en la etiqueta.
-   Como alternativa, para algunos idiomas de la representación de su conmutador podría ser utilizar "I" y "O" para familiaridad cultural, pero podría ser conveniente la etiqueta para leer "On" u "Off".

### <a name="translation-services"></a>Servicios de traducción

#### <a name="machine-translation"></a>Traducción automática

Para compilar características de traducción de la aplicación, tenga en cuenta el [API de Azure traductor texto](https://azure.microsoft.com/en-au/services/cognitive-services/translator-text-api/).

Con fines de prueba podría utilizar una de las muchas herramientas de traducción en línea para incluir cierto texto localizado en la aplicación durante el desarrollo:

- [Traductor de Bing](https://www.bing.com/translator/)
- [Traducir de Google](http://translate.google.com/)

Hay muchas otras disponibles. La calidad de la traducción automática no considera bastar para lanzar una aplicación sin primero que se va a revisar y probado por traductores profesionales o hablantes nativos.

#### <a name="professional-translation"></a>Traducción profesional

También hay servicios de traducción profesional que tomará las cadenas y distribuirlos a sus propios traductores, proporcionándole traducciones terminadas por una cuota.

Uno de los servicios de renombre es [LionBridge](http://www.lionbridge.com/). Servicios profesionales más admiten todos los tipos de archivo comunes como cadenas, XML, RESX y POT/pedido de compra.


## <a name="summary"></a>Resumen

En este artículo se introdujo algunos de los conceptos que debe conocer antes de internacionalizar la aplicación y, a continuación, localizar los recursos y también describe cómo cambiar las preferencias de idioma para cada plataforma.

Estos conceptos se pueden aplicar a las diversas técnicas de internacionalización específica de la plataforma y multiplataforma que son posibles con Xamarin.

Seguir leyendo detalles técnicos para la plataforma que le interesen:

- [Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md) localización entre plataformas con RESX (archivos).
- [Xamarin.iOS](~/ios/app-fundamentals/localization/index.md) localización de plataforma nativa.
- [Xamarin.Android](~/android/app-fundamentals/localization.md) localización de plataforma nativa.



## <a name="related-links"></a>Vínculos relacionados

- [Información general de localización de Apple](https://developer.apple.com/internationalization/)
- [Lista de comprobación de localización de Android](http://developer.android.com/distribute/tools/localization-checklist.html)
- [Prácticas recomendadas para desarrollar aplicaciones de uso internacional (MSDN)](http://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
