---
title: Localización de interfaz de usuario de aplicación
description: Este documento describe los conceptos de desarrollo multiplataforma de internacionalización y localización y examina cómo que afecten al diseño de la aplicación.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: a1218d836aad827390d9f5e70de189a869b7c6b8
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830998"
---
# <a name="localization"></a>Localización

Esta guía presentan los conceptos de *internacionalización* y *localización* y vínculos para obtener instrucciones sobre cómo generar aplicaciones móviles de Xamarin con estos conceptos.

Si desea pasar directamente a los detalles técnicos de localización de aplicaciones de Xamarin, comience con uno de estos artículos de procedimientos específicos de la plataforma:

- [**Xamarin.Forms** ](~/xamarin-forms/app-fundamentals/localization/index.md) localización entre plataformas utilizando los archivos RESX.
- [**Xamarin.iOS** ](~/ios/app-fundamentals/localization/index.md) localización de plataforma nativa.
- [**Xamarin.Android** ](~/android/app-fundamentals/localization.md) localización de plataforma nativa.

## <a name="i18n-and-l10n"></a>I18N y L10n

*Internacionalización* es el proceso de hacer que su código capaz de mostrar diferentes idiomas y adaptar su presentación para diferentes configuraciones regionales (por ejemplo, el formato de número y fecha). Esto se conoce también como *globalización*.

*Localización* es el paso siguiente: creación de recursos (por ejemplo, cadenas e imágenes) para cada idioma y la unión de ellos con la aplicación internationalize.

Internacionalización que se suele abreviar como i18n: forma abreviada de 18 letras entre "i" y "n". Localización del mismo modo se ha reducido a L10n: para 10 letras entre "L" y "n".

## <a name="overview"></a>Información general

Este documento presenta los conceptos asociados con la internacionalización y localización, y cómo se aplican al desarrollo de aplicaciones móviles en general.
Al diseñar y crear una aplicación, lo que podría tener previamente codificado de forma rígida pero que debe parametrizar para localización incluyen:

- Diseños de pantalla y el texto,
- Iconos y colores, y gráficos
- Archivos de vídeo y sonidos,
- Texto dinámico y el formato de texto (por ejemplo, números, divisa y fechas)
- Los cambios de diseño para los idiomas de derecha a izquierda (RTL), y
- Ordenación de datos.

Con independencia de qué plataformas móviles, la aplicación tiene como destino estas sugerencias le ayudará a compilar una aplicación localizada de alta calidad.


## <a name="design-considerations"></a>Consideraciones de diseño

Diseñar una aplicación para que sea posible de adaptar su contenido se denomina internacionalización. Realizar internacionalización correctamente es más que simplemente permitir para otro idioma cadenas que se cargue en tiempo de ejecución: una aplicación bien diseñada debe permitir para que todos los recursos que se puede cambiar según el idioma y configuración regional (como imágenes, sonidos y vídeos) y se puede adaptar formato y diseño para hacer frente a diferentes cadenas de tamaño.

En esta sección se describe algunas consideraciones de diseño para tener en cuenta al compilar una aplicación internacionalizada.

### <a name="layouts-and-string-length"></a>Los diseños y longitud de cadena

Las cadenas de chino y japonés pueden ser muy breves: a veces uno o dos caracteres pueden ser lo suficientemente significativas para una etiqueta de campo de entrada.

Las cadenas de alemán (por ejemplo) pueden ser muy largos; a veces, una palabra relativamente corta en inglés pasa a ser muy larga en otros lenguajes: ya sea cada vez recortada o en otro inesperadamente el reflujo de su diseño.

Comparar las longitudes de cadena para algunos elementos en la pantalla principal de iOS en inglés, alemán y japonés:

[![](localization-images/language-compare-sml.png "Longitud de cadena en japonés vs alemán")](localization-images/language-compare.png#lightbox)

Tenga en cuenta que **configuración** en inglés (8 caracteres) requiere 13 caracteres para la traducción de alemán, pero sólo 2 caracteres de japonés.

Diseños donde la etiqueta de presentación y el campo de entrada son side-by-side son difíciles de trabajar con cuando la longitud de la etiqueta puede variar enormemente. A menudo un diseño donde se muestra la etiqueta encima de un campo es más fácil de localizar porque todo el ancho de la pantalla está disponible para la etiqueta y la entrada.

Como norma general, si va a crear diseños fijos (especialmente los elementos en paralelo) permiten al menos un 50%, requieren más ancho que la versión en inglés de las etiquetas y el texto. Esto no solucionará todos los problemas pero le proporcionará un búfer que funcionará en muchos casos.

### <a name="input-validation"></a>Validación de entrada

Tenga cuidado de supuestos al escribir reglas de validación. Puede parecer válido para requerir un campo de texto de entrada a "require" al menos tres caracteres de inglés, puesto que una sola letra muy rara vez tiene algún significado. En chino y japonés sin embargo, un solo carácter puede ser una entrada válida y una validación de mensajes "al menos 3 caracteres is required" no tiene sentido para esos lenguajes.

Otras tareas aparentemente simples, como validación de una dirección de correo electrónico o URL del sitio Web se vuelven más complicado con los caracteres no se limitará al subconjunto de ASCII.

Escribir las reglas de validación con la internacionalización en mente, elija las reglas menos restrictivas, o escribir la lógica para que funciona de forma diferente para cada idioma.

### <a name="images-and-color"></a>Las imágenes y colores

No todas las imágenes se deben cambiar en función de la opción de idioma del usuario. Muchos de los iconos o las fotos se adecuado para todos los usuarios, no importa en qué lenguaje hablan.
Algunos recursos que tenga sentido para localizar sin embargo, como:

- Las imágenes que representan personas o ubicaciones específicas: la aplicación puede resultar más relevante para los usuarios si se muestran en las personas y ubicaciones locales.
- Iconos: algunos iconografía puede ser específicas de referencias culturales y puede hacer que la aplicación más fácil de usar mediante la localización de las imágenes para reflejar el conocimiento local.
- Colores: algunas referencias culturales comprender los colores de manera diferente: rojo puede significar advertencia en una región, pero la buena suerte en otro. Póngase en contacto con hablantes nativos al diseñar la aplicación para determinar si debe crear un mecanismo para localizar los colores.


### <a name="videos-and-sound"></a>Vídeos y sonido

Vídeos y presentan desafíos especiales sonidos al localizar una aplicación, ya que si bien es relativamente fácil de obtener las cadenas traducidas, grabar varios voiceover realiza un seguimiento o clips de vídeo pueden ser costoso y difícil.

Varias copias de archivos de vídeo y sonidos significativamente también pueden aumentar el tamaño de la aplicación (especialmente si durante la localización en un gran número de idiomas o tiene una gran cantidad de archivos multimedia). Considere la posibilidad de descargar solo los recursos de idioma necesarios después de que el usuario ha instalado la aplicación, pero esto también podría dar lugar a una mala experiencia de usuario en redes lentas.

A menudo hay varias formas de solucionar problemas de localización: lo más importante es tenerlos en cuenta por adelantado y asegúrese de que la aplicación está diseñada para encargarse de ellos.


### <a name="dates-times-numbers-and-currency"></a>Fechas, horas, números y moneda

Si usa las funciones de formato. NET, no olvide especificar la referencia cultural para que los separadores decimales se analizan correctamente (y evitar las excepciones de conversión que se produzca). Por ejemplo 1.99 y 1,99 son representaciones decimales válidas según la configuración regional.

Cuando los datos proceden de un origen conocido (es decir. desde su propio código o un servicio web que usted controla) puede codificar un identificador de referencia cultural que coincide con el formato como InvariantCulture que funcionará para el formato de idioma de inglés estándar.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Si los datos se está pasando por el usuario de la aplicación, analizarlo mediante una instancia de CultureInfo que refleja la configuración regional:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Consulte la [analizar cadenas numéricas](https://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) y [analizar cadenas de fecha y hora](https://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) artículos de MSDN para obtener más información.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Idiomas de derecha a izquierda (RTL)

Algunos lenguajes, como el árabe, hebreo y urdú (por ejemplo), se leen de derecha a izquierda.
Las aplicaciones que admiten estos idiomas deben usar diseños de pantalla que se adapten para los lectores de derecha a izquierda, por ejemplo:

- Debe ser texto alineado a la derecha.
- Las etiquetas deben aparecer a la derecha de los campos de entrada.
- Por lo general se invierte la colocación del botón predeterminado.
- Navegación jerárquica Deslizar y animación (y otras metáforas relacionadas con la navegación y las animaciones) que usar la dirección de contexto también se debe invertir.

IOS y Android admiten diseños de derecha a izquierda y la representación de fuente, con características integradas que ayudan a realizar los ajustes anteriores. Xamarin.Forms no admite actualmente automáticamente el procesamiento de derecha a izquierda.

### <a name="sorting"></a>Ordenar

Idiomas diferentes definen el criterio de ordenación de sus alfabetos de forma diferente, incluso cuando usan el mismo juego de caracteres.

Consulte la [detalle de comparación de cadenas](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) en [prácticas recomendadas para el uso de cadenas en .NET Framework](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) para obtener un ejemplo donde el criterio de ordenación afecta a la lenguaje (CultureInfo).

No es probable que las capacidades de base de datos integradas en las plataformas móviles admitirán a específico del lenguaje orden por lo que es pueden que deba implementar código adicional en la lógica de negocios.

### <a name="text-search"></a>Búsqueda de texto

Asegúrese de escribir y probar su algoritmo de búsqueda con varios idiomas en mente. Aspectos a tener en cuenta son:

- Autocompletar – si ha creado una función de Autocompletar Asegúrese de que los orígenes de sugerencias relevantes para el idioma del usuario.
- ¿Consulta correspondiente a los datos: buscará consultas escritas en un determinado idioma se ejecuta con sólo contenido escrito en dicho lenguaje o de todo el contenido de la aplicación?
- ¿Lematización: si la búsqueda se ha creado para buscar palabras similares, las raíces de word y otras optimizaciones de búsqueda es esas optimizaciones creadas para todos los idiomas que admite?
- Ordenación: asegúrese de que los resultados se ordenan correctamente (consulte más arriba).


### <a name="data-from-external-sources"></a>Datos de orígenes externos

Muchas aplicaciones descargan datos desde orígenes externos, desde Twitter y fuentes RSS a tiempo, noticias o cotizaciones bursátiles. Cuando se muestra esto para un usuario deberá considerar la posibilidad de que se mostrará una pantalla de información pertinente o es ilegible para ellos.

Hay algunas estrategias que puede usar para probar y asegúrese de que la aplicación muestra los datos relevantes para el usuario:

- Orígenes diferentes: la aplicación podría descargar los datos de un origen diferente según el idioma o la configuración regional del usuario. Los precios de noticias, clima y acciones de configuración regional es posible que más sentido que algo descargado desde una fuente de América del Norte.
- Para mostrar localizado: si va a mostrar un Twitter o una foto de fuente, debe mostrar los metadatos (por ejemplo, el tiempo necesario) en su propio lenguaje, incluso si el propio contenido permanece en el idioma original.
- Traducción: puede crear una opción de traducción en su aplicación para realizar una traducción automática de los datos entrantes. Esto podría ser automática o a discreción del usuario: asegúrese de notificar al usuario si éste está teniendo lugar, puesto que las traducciones de la máquina nunca son perfectas.

Esto también podría afectar a los vínculos externos a las pistas de audio o vídeos: al diseñar la aplicación Asegúrese de planear por adelantado abastecimiento contenido traducen o lo que garantiza que los usuarios son informados adecuadamente mediante la interfaz de usuario cuando el contenido no aparecerá en su lenguaje.


### <a name="dont-over-translate"></a>No se traducen exceso

Algunas cadenas de la aplicación no debe traducir, o al menos necesitan una atención especial el traductor. Algunos ejemplos podrían incluir:

- Las direcciones URL: si escribes una dirección URL, puede o no deberá ser ajustado por idioma. Por ejemplo, facebook.com no requiere traducción detecta automáticamente el idioma en el sitio principal. Otros sitios tienen contenido específico de la configuración regional y desea ofrecer una dirección URL diferente, por ejemplo, yahoo.com frente a yahoo.fr o yahoo.it.
- Números de teléfono, especialmente aquellas con diferentes códigos de país o números para los llamadores que hablan un lenguaje determinado.
- Detalles de contacto: direcciones y otra información pueden variar por idioma o configuración regional.
- Marcas comerciales y los nombres de producto: algunas cadenas no es necesario traducir ya que siempre se escriben en el mismo idioma.

Por último, asegúrese de incluir instrucciones detalladas para el traductor si determinadas cadenas requieren un tratamiento especial.


### <a name="formatted-text"></a>Texto con formato

Normalmente no un problema con aplicaciones móviles porque las cadenas generalmente no son de formato enriquecido. Sin embargo si se requiere texto enriquecido (por ejemplo, negrita o cursiva) en la aplicación asegurarse el traductor sabe cómo para el formato de entrada, los archivos de las cadenas almacenan correctamente y está formateado correctamente antes de que se va a mostrar al usuario (es decir. no se deje accidentalmente los códigos de formato que se presentan al usuario).



## <a name="translation-tips"></a>Sugerencias de traducción

Traducir las cadenas utilizadas por una aplicación se considera parte del proceso de localización. Normalmente, esta tarea se contrata a un servicio de traducción y realizarla multilingüe personal que no sepa la aplicación o su empresa.

Las siguientes sugerencias le ayudará a generar las cadenas que son más fáciles de traducir con precisión y, por tanto, mejorar la calidad de las aplicaciones localizadas.



### <a name="localize-complete-strings-not-words"></a>Localizar cadenas completas, no las palabras

A veces, los desarrolladores adoptan el enfoque de intenta especificar palabras aisladas o la frase "fragmentos", por lo que puede volver a utilizarlas a lo largo de la aplicación. Por ejemplo, para el texto "tiene 5 mensajes." pueden especificar las siguientes cadenas de traducción

**Incorrecta**:

```csharp
"You have"
"no"
"message"
"messages"
```

y, a continuación, intente crear la frase correcta sobre la marcha en el código mediante la concatenación de cadenas:

**Incorrecta**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Esto no es recomendable** porque no funcionará necesariamente todos los idiomas y será difícil para el traductor para entender el contexto de cada segmento corto. También lleva a la reutilización de las cadenas traducidas, lo que pueden causar problemas más adelante si se utilizan en contextos diferentes (y, a continuación, se actualizan).


### <a name="allow-for-parameter-re-ordering"></a>Permitir la reordenación de parámetros

Algunos lenguajes de programación requieren sintaxis adicional para especificar el orden de parámetros en una cadena, sin embargo, .NET ya admite el concepto de marcadores de posición numerados, por lo que

**Buena**:

```csharp
"a {0} b {1} cde {3}"
```

podría ser traducido lo siguiente (donde la posición y el orden de los marcadores de posición se puede cambiar)

```csharp
"{2} {3} f g h {0}"
```

y los tokens se ordenarán como el traductor pensado. No olvide incluir una explicación de lo que contiene cada marcador de posición cuando se envía la cadena a un traductor.


### <a name="use-multiple-strings-for-cardinality"></a>Usar varias cadenas de cardinalidad

Evitar cadenas como `"You have {0} message/s."` usar las cadenas específicas para cada estado para proporcionar una mejor experiencia de usuario:

**Buena**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

Tendrá que escribir código en la aplicación para evaluar el número que se muestran y elija la cadena adecuada. Algunas plataformas (incluidos iOS y Android) tienen características integradas para elegir automáticamente la cadena en plural mejor según las preferencias para el idioma o configuración regional actual.


### <a name="allowing-for-gender"></a>Lo que permite sexo

Alfabeto latino idiomas a veces usan palabras diferentes según el sexo del sujeto. Si la aplicación conoce sexo, debe permitir las cadenas traducidas reflejar esto.

También es el caso más obvio incluso en inglés, donde las cadenas hacen referencia a una persona concreta o un usuario de la aplicación. Por ejemplo, algunos sitios mostrar mensajes como `"Bob commented on his post"` por lo que tiene las cadenas para un género hombre, mujer y no binarias o desconocido:

**Buena**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>No reutilice las cadenas

O más concretamente, no reutilice cadenas simplemente porque son similares de la propia cadena tiene un propósito diferente o significado.

Por ejemplo: imagine que tiene un modificador on/off de la aplicación y el control de cambio es el texto de 'on' y 'off' necesario traducir. También mostrar el valor de esa configuración en otro lugar en la aplicación en una etiqueta de texto. Debe utilizar cadenas diferentes para la presentación del conmutador en comparación con el estado del conmutador (incluso si son la misma cadena en el idioma predeterminado): por ejemplo:

- "Activado": se muestra en el propio conmutador
- "Off": se muestra en el propio conmutador
- "Activado": se muestra en una etiqueta
- "Off": se muestra en una etiqueta

Esto proporciona la máxima flexibilidad para el traductor:

- Por motivos de diseño, quizás el propio conmutador utiliza minúscula "on" y "off" pero la etiqueta para mostrar utiliza mayúsculas "On" y "Off".
- Algunos lenguajes necesitar el valor del modificador se abrevia para ajustarse en el control de interfaz de usuario, mientras que la palabra completa (traducida) puede aparecer en la etiqueta.
- Como alternativa, para algunos idiomas, la representación del conmutador podría ser usar "I" y "O" cultural familiar, pero es posible que aún desea que la etiqueta para leer "On" o "Desactivar".

### <a name="translation-services"></a>Servicios de traducción

#### <a name="machine-translation"></a>Traducción automática

Para crear características de traducción en su aplicación, tenga en cuenta la [Azure Translator Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

Con fines de prueba podría utilizar una de las muchas herramientas de traducción en línea para incluir algún texto localizado en la aplicación durante el desarrollo:

- [Traductor de Bing](https://www.bing.com/translator/)
- [Google Translate](http://translate.google.com/)

Hay muchos otros disponibles. La calidad de la traducción automática por lo general no se considera bastante buena como para publicar una aplicación sin que primero se revisado y probado por traductores profesionales o hablantes nativos.

#### <a name="professional-translation"></a>Traducción profesional

También hay servicios de traducción profesional que tomará las cadenas y distribuirlos a sus propios traductores, proporcionar traducciones terminadas por una cuota.

Uno de los servicios más conocidos es [LionBridge](http://www.lionbridge.com/). Servicios profesionales más admiten todos los tipos de archivo comunes como cadenas, XML, RESX y POT/OC.


## <a name="summary"></a>Resumen

Este artículo presenta algunos de los conceptos que debe conocer antes de internacionalizar su aplicación y, a continuación, localizar los recursos y también explican cómo cambiar las preferencias de idioma para cada plataforma.

Estos conceptos se pueden aplicar a las diversas técnicas de internacionalización específicos de la plataforma y multiplataforma que son posibles con Xamarin.

Continúe leyendo los detalles técnicos para la plataforma que le interese:

- [Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md) localización entre plataformas utilizando los archivos RESX.
- [Xamarin.iOS](~/ios/app-fundamentals/localization/index.md) localización de plataforma nativa.
- [Xamarin.Android](~/android/app-fundamentals/localization.md) localización de plataforma nativa.



## <a name="related-links"></a>Vínculos relacionados

- [Información general de localización de Apple](https://developer.apple.com/internationalization/)
- [Lista de comprobación de localización de Android](https://developer.android.com/distribute/tools/localization-checklist.html)
- [Prácticas recomendadas para desarrollar aplicaciones internacionalizadas (MSDN)](https://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
