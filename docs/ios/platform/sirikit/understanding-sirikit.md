---
title: Descripción de los conceptos de SiriKit
description: Este documento describe los conceptos básicos necesarios para trabajar con SiriKit, una aplicación de Xamarin.iOS. Por ejemplo, se tratan las intenciones y extensiones de interfaz de usuario de Intents, permisos SiriKit, diseñar una gran experiencia y mucho más.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: b72246968c9b321329e56fd51eaaa98a1625922e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123081"
---
# <a name="understanding-sirikit-concepts"></a>Descripción de los conceptos de SiriKit

_En este artículo se trata los conceptos clave que será necesarios para trabajar con SiriKit, una aplicación de Xamarin.iOS._


Nuevo iOS 10, SiriKit permite que una aplicación de Xamarin.iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS. Esta funcionalidad se proporciona en la extensión de la aplicación de uno o más con el nuevo **intenciones** y **Intents UI** marcos de trabajo.

SiriKit permite que una aplicación de iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS mediante extensiones de aplicación y el nuevo **intenciones** y **Intents UI** marcos de trabajo.

Siri funciona con el concepto de **dominios**, sabe de grupos de acciones para las tareas relacionadas. Cada interacción de la aplicación con Siri debe estar en uno de sus dominios de servicio conocido como sigue:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administración de sesiones de ejercicios.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir los pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera adecuado **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

## <a name="the-intents-and-intents-ui-extensions"></a>Las intenciones y extensiones de interfaz de usuario de intenciones

Siri y la aplicación mapas interactúan con los servicios de la aplicación a través de dos tipos diferentes de extensiones de aplicación:

- **Extensión de intents** -proporciona Siri y asignaciones con la aplicación del contenido y realiza las tareas necesarias para cumplir cualquier admitidos calidades.
- **Extensión de interfaz de usuario de intents** -proporciona una interfaz de usuario personalizado que se mostrará para el contenido de la aplicación dentro de Siri o de mapas.

La aplicación debe proporcionar una extensión Intents para admitir SiriKit y es responsable de proporcionar información que Siri y mapas pueden presentar al usuario y para controlar las intenciones.

Creación de una extensión de interfaz de usuario de Intents es opcional, ya que Siri normalmente controla toda la interacción del usuario y tiene una interfaz de usuario estándar e integrado para presentar la información de cada uno de los dominios admitidos. Al proporcionar una extensión de interfaz de usuario de Intents, la aplicación puede utilizar el **intención de interfaz de usuario** framework para presentar un personalizadas y enriquecidas interfaz de usuario que incluye la aplicación de la personalización de marca y la información adicional.

## <a name="siri-and-the-maps-app-role"></a>Siri y el rol de aplicación de mapas

Las solicitudes de voz del usuario son procesado y analizado semánticamente por Siri que dichas solicitudes se convierte en intenciones procesables que puede administrar las extensiones de la intención del idioma.

Mapas usa las extensiones de intención de la aplicación para mostrar información en la interfaz de asignación en respuesta a acciones del usuario. Como solicitar cercanas restaurantes u obtener las revisiones de restaurante de la aplicación.

Siri y mapas administración todas las interacciones del usuario y mostrar los resultados mediante la interfaz de sistema estándar. La función de las extensiones de aplicación es principalmente para proporcionar los datos que se muestran. Opcionalmente, la aplicación puede proporcionar una extensión de interfaz de usuario de Intents y presentar una interfaz de usuario personalizada para mejorar la interfaz del sistema de forma predeterminada.

## <a name="interacting-with-siri-via-sirikit"></a>Interactuar con Siri a través de SiriKit

En esta sección se presenta una visión general de cómo SiriKit permite al usuario interactuar con la aplicación de uso de Siri. Para este ejemplo, vamos a usar la aplicación MonkeyChat falsa:

[![](understanding-sirikit-images/monkeychat01.png "El icono de MonkeyChat")](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene su propio libro contacto de amigos del usuario, cada uno asociado con un nombre de pantalla (por ejemplo, Bobo, por ejemplo) y permite al usuario enviar chats de texto a cada uno de ellos por su nombre de la pantalla.

Hay muchas maneras de que el usuario podría iniciar una interacción con la aplicación, dado que diferentes personas podrían realiza la misma solicitud de muchas formas diferentes.

Por ejemplo, si el usuario desea enviar un mensaje a su amigo Bobo, podría tener la conversación con Siri siguiente:

_Usuario: Hola Siri, enviar un mensaje MonkeyChat._<br />
_Siri: A quien?_<br />
_Usuario: Bobo._<br />
_Siri: ¿Qué desea decir al Bobo?_<br />
_Usuario: Envíe Bananas más._<br />

Otra persona podría realizar la misma solicitud con una conversación diferentes:

_Usuario: Enviar un mensaje a Bobo en MonkeyChat._<br />
_Siri: ¿Qué desea decir al Bobo?_<br />
_Usuario: Envíe bananas más._<br />

Y otros usuarios puedan realizar una solicitud aun más corta:

_Usuario: MonkeyChat Bobo envíe bananas más._<br />
_Siri: Bueno, enviar mensaje envía bananas más a Bobo en Monkeychat._<br />

O incluso hacer que la misma solicitud en un idioma diferente:

_Usuario: MonkeyChat Bobo ningún crédito si revisión plaît envoyer plus de bananes._<br />
_Siri: Oui, revisión plaît envoyer de Envoi (envío) mensaje ningún crédito si más de bananes à Bobo Surname Monkeychat._<br />

Aún otro usuario podría ser muy detallado en la conversación:

_Usuario: Hola Siri, puede vuelva hágame un favor e iniciar la aplicación MonkeyChat para enviar un texto con el mensaje envíe bananas más._<br />
_Siri: A quien?_<br />
_Usuario: Mi mejor pal Bobo._<br />

Además, hay muchas maneras de Siri podría responder a una solicitud, algunos en función de cómo se realizó la solicitud:

- **Si mantiene el botón** -Siri proporcionará más respuestas visuales con comentarios verbales limitado.
- **Por "¡Hola Siri"** -Siri se más verbales y proporcionar respuestas visuales menos.

Siri también está optimizado para satisfacer las necesidades de accesibilidad del usuario e interactuar y responder según esas necesidades.

No importa cómo se realiza una solicitud o cómo Siri responde a la solicitud, Siri controla la conversación con el usuario y la aplicación (a través de sus extensiones) proporciona la funcionalidad.

Cuando el usuario realiza una solicitud de Siri verbal, estos son los pasos que seguirá Siri:

[![](understanding-sirikit-images/monkeychat02.png "Los pasos que seguirá Siri")](understanding-sirikit-images/monkeychat02.png#lightbox)

1. En primer lugar, Siri toma el audio del usuario **voz** y lo convierte en texto.
2. A continuación, el texto se convierte en un **intención**, una estructura de la representación de la solicitud del usuario.
3. Según la intención, tardará Siri **acción** para realizar la solicitud del usuario.
4. Por último, presentará Siri **respuestas** (visual y verbales) al usuario según la acción realizada.

Hay tres maneras principales que la aplicación puede tomar parte en la conversación del usuario con Siri:

[![](understanding-sirikit-images/monkeychat03.png "Los tres métodos principales que la aplicación puede tomar parte en la conversación de los usuarios con Siri")](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabulario** -se trata cómo la aplicación indica Siri las palabras que necesita conocer para interactuar con ella.
2. **Lógica de aplicación** : estas son las acciones y las respuestas que tardará la aplicación basado en dado intenciones.
3. **Interfaz de usuario** -esta es la interfaz de usuario personalizada opcional que la aplicación puede proporcionar sus respuestas en.

### <a name="example"></a>Ejemplo

Dado que la información anterior, examine cómo interactúan con la aplicación MonkeyChat la conversación siguiente:

_Usuario: Hola Siri, enviar un mensaje a Bobo en MonkeyChat._<br />
_Siri: ¿Qué desea decir al Bobo?_<br />
_Usuario: Envíe bananas más._<br />

La primera función que toma la aplicación en la conversación es entender Siri de voz del usuario:

[![](understanding-sirikit-images/monkeychat04.png "Ayudar a Siri a comprender la voz de los usuarios")](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri no tiene el nombre "Bobo" en su base de datos, pero de la aplicación y ha compartido esta información con Siri a través de su vocabulario. La aplicación también ayuda reconocer que Bobo es un destinatario, ya que los especificados a Siri como Siri un *póngase en contacto con*.

Siri sabe que más se requiere para enviar un mensaje que solo un destinatario, por lo que comprobará con la extensión de la aplicación para ver si un mensaje necesita contenido rápidamente. Dado que hace MonkeyChat, Siri responderá al usuario con la pregunta: *"¿Qué desea decir al Bobo?"*

En el ejemplo anterior, el usuario ha respondido, *"Envíe más banana"*, que Siri empaquetará en un modo estructurado **intención**:

[![](understanding-sirikit-images/monkeychat05.png "Siri empaquetará la respuesta del usuario en un intento estructurado")](understanding-sirikit-images/monkeychat05.png#lightbox)

La intención estructurada contendrá la siguiente información:

- **Dominio:** mensajes
- **Propósito:** sendMessage
- **Destinatarios:** Bobo
- **Contenido:** envíe más plátano

Cada dominio tiene como conjunto de saber *acciones* que pueden realizarse dentro de ellos y basado en el dominio y la acción, cero a muchos parámetros podrían estar incluidos en la intención se envía a la aplicación.

La intención, a continuación, se envía a la extensión de la aplicación para su procesamiento. Como resultado de la intención de procesamiento, la aplicación generará un **IntentResponse** que se incorporará con la intención e incluir parámetros que describen lo que hizo la aplicación con la intención.

Cada IntentResponse también incluirá un **código de respuesta** que indica que Siri si la aplicación pudiera completar la solicitud o no. Algunos dominios tienen códigos de respuesta de error muy específico que también se pueden enviar.

Por último, incluirá el IntentResponse un `NSUserActivity` (como los que se usan para admitir la mano Off). El `NSUserActivity` se usará para iniciar la aplicación si la respuesta requiere que deje el entorno de Siri y ejecute la aplicación para completarla. 

Siri compilará automáticamente una adecuada `NSUserActivity` para iniciar la aplicación y la camioneta de carga donde el usuario se dejó en el entorno de Siri. Sin embargo, la aplicación puede proporcionar su propio `NSUserActivity` con personalizar información, si es necesario.

Después de la aplicación ha procesado la intención y devolvió una respuesta a Siri, a continuación, muestra los resultados al usuario (verbalmente y visualmente):

[![](understanding-sirikit-images/monkeychat06.png "Los resultados presentados al usuario tanto verbalmente y visualmente")](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri tiene respuesta integrada varias Interfaces de usuario para cada uno de los dominios disponibles para la aplicación. Sin embargo, puesto que MonkeyChat ha proporcionado una extensión opcional de intención de la interfaz de usuario, se usa para presentar los resultados de la conversación para el usuario en el ejemplo anterior.

## <a name="the-intent-lifecycle"></a>El ciclo de vida de intención

Hay tres tareas principales que debe realizar cuando se trabaja con las intenciones la extensión de la aplicación:

[![](understanding-sirikit-images/monkeychat07.png "El ciclo de vida de intención")](understanding-sirikit-images/monkeychat07.png#lightbox)

1. La aplicación debe **resolver** todos los parámetros en un evento. Como resultado, la aplicación llamará resuelva varias veces (una vez por cada parámetro) y, a veces varias veces en el mismo parámetro hasta que la aplicación y el usuario está de acuerdo en lo que se solicita.
2. La aplicación debe **confirmar** que puede controlar la intención solicitada y proporcione Siri sobre el resultado esperado.
3. Por último, la aplicación debe **controlar** la intención y realice los pasos para lograr el resultado solicitado.

### <a name="the-resolve-stage"></a>La fase de resolución

La fase de resolución ayudará a Siri a comprender los valores que el usuario ha proporcionado y garantiza que lo que el usuario realmente significaba es lo que ocurrirá cuando se procesa la intención de la aplicación. 

Esta fase también proporciona una oportunidad para que la aplicación influir en el comportamiento de Siri durante la conversación con el usuario. Para ello, la aplicación proporcionará un **resolución respuesta**. Hay una serie de respuesta predefinido a los distintos tipos de datos que Siri entiende.

La más común será la respuesta de la resolución de la aplicación **éxito**, lo que significa que la aplicación coincide con el elemento específico de datos de un parámetro (por ejemplo, el nombre de pantalla del usuario) a un fragmento de información que conoce.

Puede que en ocasiones cuando la aplicación necesita para confirmar que una determinada solicitud coincide con la pieza de información conocida sobre correcto. En estos casos, enviará un **ConfirmationRequired** respuesta a una pregunta Sí o no al usuario como *"Envío de mensajes a Bobo gran?"*

Puede haber otros casos donde la aplicación necesitará al usuario elegir entre una breve lista de opciones. En este caso, la aplicación proporcionará un **desambiguación** respuesta con una lista de opciones de dos y diez para el usuario elegir, como: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri controlará el usuario que realiza la selección, verbalmente o mediante la interacción con la UI de Siri y el resultado se enviarán a la aplicación.

En otros casos, puede que no exista información suficiente para que la aplicación resolver el parámetro o es posible que haya demasiadas coincidencias resolver mediante la anulación de ambigüedades (por ejemplo, los 80 usuarios con Bobo en su nombre). En estos casos, la aplicación enviará un **NeedsMoreDetails** Siri y respuesta solicitará al usuario que sea más específico.

Si el usuario no proporciona un valor que es necesario para procesar la intención, puede enviar un **NeedsValue** respuesta tener Siri preguntar al usuario para el valor.

Si la aplicación no es compatible con un valor que el usuario ha proporcionado para un parámetro concreto, puede enviar el **UnsupportedWithReason** respuesta para proporcionar un motivo por qué no se admite el valor. Siri, a continuación, le pedirá al usuario para un valor completamente nuevo y ellos el motivo también es necesario.

Por último, utilice el **NotRequired** respuesta para indicar a Siri que la aplicación no requiere un valor para un parámetro dado. Si el usuario proporciona uno de todos modos, simplemente se omitirá por Siri.

### <a name="the-confirm-stage"></a>La fase de confirmación

La fase de confirmación tiene dos propósitos:

- Para indicar el resultado esperado de controlar una intención que Siri puede saber lo que se va a suceder al usuario de Siri.
- Proporciona una comprobación de oportunidad ninguno de los Estados necesario la aplicación que tenga que completar la solicitud presentada por el usuario, como tener suficiente dinero en el banco para realizar el pago solicitado.

La aplicación proporcionará un **intención respuesta** desde el paso de confirmación, que debe rellenarse con como mucha información de la aplicación tiene disponible para Siri pueda comunicarse lo eficazmente con el usuario.

Según el tipo de acción y de dominio, Siri puede pedir al usuario confirmación, como antes de enviar un pago o transporte de reserva.

### <a name="the-handle-stage"></a>La fase de identificador

La fase de controlar es la parte más importante del trabajo con una intención porque es el punto donde la aplicación satisface la solicitud del usuario mediante la realización de la tarea que se ha solicitado realizar.

Al igual que en la fase de confirmación, la aplicación debe proporcionar toda la información sobre el resultado como sea posible para que Siri puede asociar al usuario. A veces, esta información aparecerá visualmente u otras veces Siri se hablará simplemente al usuario. 

Puede haber ocasiones cuando la aplicación puede requerir más tiempo en procesar una solicitud determinada, por ejemplo, los retrasos en la llamada de red o si una persona necesita para satisfacer la solicitud (por ejemplo, completar y enviar un pedido o conducir un automóvil a la ubicación del usuario). Cuando Siri está esperando una respuesta de la aplicación, mostrará una interfaz de usuario espera al usuario que le indica que la aplicación está procesando la solicitud.

Idealmente, la aplicación debe proporcionar una respuesta a Siri en dos o tres segundos, como máximo. Si la aplicación sabe que una respuesta determinada se va a tardar más en procesarse, es necesario enviar una **InProgress** Siri con el código de respuesta. Siri, a continuación, se informará al usuario que la aplicación está procesando la solicitud en segundo plano y se seguirá hacerlo incluso si dejan el entorno de Siri.

## <a name="adding-sirikit-to-the-app"></a>Adición de SiriKit a la aplicación

Con SiriKit en iOS 10, Apple ha creado dos nuevos puntos de extensión:

- **Extensión de intents** : proporciona Siri con el contenido de la aplicación y realiza las tareas necesarias para cumplir cualquier admitidos calidades.
- **Extensión de interfaz de usuario de intents** -proporciona una interfaz de usuario personalizado que se mostrará para el contenido de las aplicaciones dentro de Siri. 

También hay una API para proporcionar las palabras y frases a Siri para ayudar en el reconocimiento en forma de:

- **Aplicación vocabulario** -palabras y frases que son comunes a todos los usuarios de la aplicación.
- **Usuario vocabulario** -palabras y frases que son únicas para un usuario de aplicación determinada.

## <a name="the-intents-extension"></a>La extensión de Intents

La extensión Intents es responsable de controlar las interacciones principales entre la aplicación y Siri como sigue:

[![](understanding-sirikit-images/intents01.png "La extensión de Intents")](understanding-sirikit-images/intents01.png#lightbox)

La extensión de intención puede admitir uno o varios intentos, depende del desarrollador para decidir cómo desean implementar SiriKit en la aplicación. El desarrollador también podría agregar una extensión de intención independiente para cada intento de tener controlarse.  Es decir, Apple solicita que el desarrollador limite el número de extensiones de intención para que Siri no tenga abiertas en la aplicación, que requieren más memoria y tiempo para controlar varios procesos.

El desarrollador también debe ser consciente de que la extensión de intención se ejecutará en segundo plano mientras Siri está activa. Esto permite que Siri transportar activamente en una conversación con el usuario mientras se continúa comunicándose con la extensión para procesar la información acerca de la solicitud.

## <a name="privacy-and-security-considerations"></a>Consideraciones de seguridad y privacidad

Apple ha tomado todas las medidas necesarias para asegurarse de que un usuario privado información es segura cuando se trabaja con Siri y por lo tanto, hay varias interacciones que el usuario deberá haber iniciado sesión en el dispositivo iOS. Por ejemplo, cuando se solicita un andar o realizar un pago.

Además, existen comportamientos específicos que la aplicación que desee limitar al usuario que se va a iniciar sesión en el dispositivo. Para estas situaciones, la aplicación puede solicitar la **restringir durante un bloqueo** comportamiento. Esto se realiza a través de una configuración en el `Info.plist` archivo.

El marco de autenticación Local está disponible para la extensión de intención, por lo que la aplicación puede pedir al usuario información de autenticación adicional, incluso si el dispositivo ya está desbloqueado.

Por último, está disponible para la extensión de intención de Apple Pay, por lo que la aplicación puede completar una transacción con Apple Pay y la hoja integrada de Apple Pay aparecerá encima de la interfaz de Siri.

Además, Apple quiere asegurarse de que los usuarios sabe cuándo va a enviar información a una aplicación de terceros 3rd y por lo tanto, el usuario **debe** diga el nombre específico de la aplicación (como se especifica en el nombre de presentación del lote de la aplicación) al realizar una solicitud.

Apple ha diseñado Siri para llevar a cabo las conversaciones naturales y fluidas con el usuario y por este motivo, el nombre del lote de la aplicación se puede usar en muchas partes de la oración, siempre que quepa forma natural en la solicitud del usuario.

Una de las cosas comunes que llevará a cabo los usuarios es "verbify" nombre de la aplicación, en otras palabras, toma el nombre de la aplicación y su uso como un verbo en una solicitud. Por ejemplo, *"MonkeyChat Bobo fueran bananas excelente."*

## <a name="the-intents-ui-extension"></a>La extensión de interfaz de usuario de Intents

La extensión de interfaz de usuario de Intents presenta la oportunidad de aportar la interfaz de usuario de la aplicación y personalización de marca en la experiencia de Siri y que los usuarios sientan conectado a la aplicación. Con esta extensión, la aplicación puede adoptar los marca como visual y otra información en la transcripción.

[![](understanding-sirikit-images/intents02.png "Ejemplo de salida de extensión de interfaz de usuario de Intents")](understanding-sirikit-images/intents02.png#lightbox)

La extensión de interfaz de usuario de Intents siempre devolverá un `UIViewController` y la aplicación puede agregarlo que quiera en el controlador de vista, por ejemplo, que muestra información adicional que va más allá de la respuesta inicial. La interfaz de usuario de Intents también puede actualizar el usuario con el estado de un evento de ejecución prolongada, como cuánto tiempo tardará un viaje en coche para llegar a su ubicación de uso compartido.

La extensión de interfaz de usuario de Intents siempre se muestran junto con otro contenido Siri como el icono de la aplicación y el nombre en la parte superior de la interfaz de usuario o, según la intención, es posible que se muestran los botones (por ejemplo, envío o cancelar) en la parte inferior.

Hay unas pocas instancias donde la aplicación puede reemplazar la información que se muestra al usuario de forma predeterminada como la mensajería de Siri o donde la aplicación puede reemplazar la experiencia predeterminada con una adaptada a la aplicación se asigna.

> [!IMPORTANT]
> Aunque es posible agregar elementos interactivos como `UIButtons` o `UITextFields` a la extensión de interfaz de usuario de intención `UIViewController`, estos están prohibidos estrictamente como la interfaz de usuario de la intención de no interactivo y el usuario no podrá interactuar con ellos.

Es totalmente opcional para la aplicación proporcione una extensión de interfaz de usuario de la intención, puesto que Siri contiene un conjunto predeterminado de la interfaz de usuario para cada tipo de intención. Además, las interfaces de la interfaz de usuario de Intents solo están disponibles para ciertos intenciones que Apple considerará sería útiles para el usuario.

## <a name="adding-sirikit-vocabulary"></a>Adición de vocabulario de SiriKit

La parte final de la implementación de SiriKit reside dentro de la aplicación, ya que proporciona el vocabulario necesario. Muchas aplicaciones tienen maneras exclusivas de describir la información al usuario y que el usuario proporcionará información a la aplicación de manera exclusiva.

Por este motivo, Siri requiere asistencia de la aplicación para entender las palabras y frases únicas para la aplicación. Algunas de estas frases formará parte de la aplicación para que todos los usuarios conocerán y entenderlas. Aún otros serán exclusivos para un usuario determinado de la aplicación.

### <a name="app-specific-vocabulary"></a>Vocabulario específico de la aplicación

El vocabulario específico de aplicación define las palabras y frases específicas que se conocerá a todos los usuarios de la aplicación, como nombres de entrenamiento o tipos de vehículos. Dado que son parte de la aplicación, se definen en un `AppIntentVocabulary.plist` archivo como parte de la agrupación de la aplicación principal. Además, se deben localizar estas palabras y frases.

Hay varias partes a un vocabulario `AppIntentVocabulary.plist` archivo:

- **Aplicación de ejemplo usa** -proporcionan un conjunto de casos de uso comunes para las solicitudes que el usuario puede hacer de la aplicación. Por ejemplo: *"Iniciar un entrenamiento con MonkeyFit".*
- **Los parámetros** -proporcionan un conjunto de tipos de parámetros estándar que no son específicos de la aplicación. Por ejemplo, nombres de entrenamiento de la aplicación MonkeyFit. Se componen de:
    - **Frase** -permite que la aplicación definir términos únicos para la aplicación. Por ejemplo: el tipo de entrenamiento "Bananarific" de la aplicación MonkeyFit. 
    - **Pronunciación** -ofrece sugerencias de pronunciación a Siri como una simple ortografía fonética de una frase exacta. Por ejemplo, "ba nana ri FICA".
    - **Ejemplo** -proporciona un ejemplo del uso de la frase determinada en la aplicación. Por ejemplo, *"Iniciar unas Bananarific en MonkeyFit"*.

Para obtener más información, consulte Apple [referencia del formato de archivo de aplicación vocabulario](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

### <a name="user-specific-vocabulary"></a>Vocabulario específico del usuario

El vocabulario específico del usuario que va a proporcionar palabras o frases que son únicas para los usuarios individuales de la aplicación. Estos se proporcionará en tiempo de ejecución de la aplicación principal (no en las extensiones de aplicación) como un conjunto ordenado de los términos que se ordenan en una prioridad de uso más importante para los usuarios, con los términos más importantes al principio de la lista.

Eche un vistazo al ejemplo de la aplicación MonkeyChat presentada más arriba. MonkeyChat mantiene una lista de todos los contactos del usuario, que van a enviar a Siri mediante el vocabulario específico del usuario. También mantiene una lista de los 10 contactos más reciente que el usuario ha mensajes y tiene un conjunto de contactos como favorito para cada usuario. En este ejemplo, los contactos favoritos deben ser al comienzo de nuestra vocabulario específico de usuario, seguido de los contactos recientes, a continuación, el resto de los contactos del usuario.

Se admiten los siguientes tipos de información mediante vocabulario específico del usuario:

- Póngase en contacto con los nombres.
- Nombres de entrenamiento.
- Nombres de álbumes de fotos.
- Palabras clave de la foto.

Si la aplicación se basa en la libreta de direcciones de iOS, la aplicación no tendrá que realizar ninguna acción, como esta información ya está disponible para Siri. La aplicación sólo necesita proporcionar los nombres de contacto si la aplicación tiene su propia base de datos única de contactos.

Al diseñar el vocabulario, sólo proporcionan los valores necesarios que los usuarios conocen e interesan. Evite proporcionar información como números de teléfono o direcciones de correo electrónico.

La aplicación también debe actualizar con prontitud Siri cuando cambia el vocabulario específico del usuario. Los usuarios están acostumbrados a solicitar información desde el momento en que se ha agregado a su dispositivo iOS de Siri. Por ejemplo, si el usuario agrega un nuevo contacto en la aplicación, enviar esa información a Siri tan pronto como el usuario lo guarda.

Más importante aún, la aplicación _debe_ eliminar información desde el vocabulario de Siri con prontitud puesto que un usuario podría convertirse en alterar si elimina un fragmento de información pero Siri se siguen reconociendo horas o días más tarde.

> [!IMPORTANT]
> La aplicación debe quitar todos el vocabulario específico del usuario de Siri si el usuario decide restablecer la aplicación o si se cierre de sesión.

## <a name="sirikit-permissions"></a>Permisos de SiriKit

La última pieza del SiriKit se centra en torno a los permisos. Al igual que con otras características de iOS (por ejemplo, fotografías, la cámara o contactos), los usuarios tienen que conceder el permiso explícito para la aplicación para comunicarse con Siri.

La aplicación es capaz de proporcionar una cadena que define qué información proporcionará a Siri y proporcionar una razón para ¿por qué el usuario debe conceder este acceso. 

Apple sugiere que la aplicación debe solicitar permiso al usuario para usar el tiempo de Siri primero que el usuario abre la aplicación haya actualizado a iOS 10. Se trata de forma que los usuarios saber acerca de la integración de Siri y pueden uso aprobado previamente antes de realizar su primera solicitud.

## <a name="sirikit-and-maps"></a>SiriKit y mapas

SiriKit es una parte integral de iOS y hace uso de marco intenciones más grande que se agregó a iOS 10. El marco de trabajo de intenciones se diseñó para compartir las acciones comunes y compartidas e intenciones con otras partes del sistema.

El marco de intenciones va más allá de simplemente Siri integración y proporciona otras características como la integración de contactos donde la aplicación puede convertirse en la telefonía predeterminada o una aplicación de mensajería para contactos específicos. Las intenciones también proporcionan una integración profunda con CallKit para proporcionar a los usuarios la mejor experiencia posible de VOIP.

La aplicación de mapas en iOS 10 ha agregado características como el uso compartido de andar donde el usuario puede reservar un viaje directamente dentro de la interfaz de usuario de mapas. SiriKit proporciona un punto de extensión común con mapas para andar intenciones de uso compartido (y otros) se pueden compartir entre Siri y mapas. 

Esto significa que si la aplicación ha adoptado las extensiones de SiriKit, también obtendrá la integración de mapas de forma gratuita. 

## <a name="designing-a-great-siri-experience"></a>Diseñar una excelente experiencia de Siri

Diseñar una experiencia de usuario excelente al integrar una aplicación en Siri es diferente a diseñar una interfaz de usuario excelente aplicación. A diferencia de situaciones normales, donde el usuario está interactuando con la aplicación directamente en pantalla, al usar Siri allí son muchas veces cuando ninguna interfaz visual es visible en absoluto. Por ejemplo, cuando el usuario ha iniciado la conversación con *"¡Hola Siri"*.

### <a name="how-siri-helps-the-developer"></a>Cómo Siri le ayuda a los desarrolladores

Al diseñar las interacciones de una aplicación con Siri, compilará la aplicación un *interfaz de conversación*, lo que significa que el contexto se deriva de la conversación que Siri está experimentando con el usuario en nombre de la aplicación.

En ausencia de una referencia visual, el usuario debe realizar un seguimiento de la información que se presenta en su cabeza. Por este motivo, Siri presenta la información mínima operativo necesaria para llevar a cabo la tarea se desea realizar el usuario.

La interfaz de conversación está formada por las preguntas y respuestas del usuario y la Siri durante la conversación. Por lo que es importante pensar en cómo Siri plantea algunas preguntas y responde al diseñar esta interfaz.

Tome como ejemplo el usuario que crea un mensaje, Siri podría responder la pregunta siguiente *"Listo para enviarlo?"* . El usuario podría responder de muchas maneras diferentes, como *"Enviar"*, *"Cancelar"* o incluso algo totalmente no relacionados con esta pregunta. Independientemente de cómo la conversación se desarrolla, Siri controlarla para la aplicación y solo envía la información relevante cuando se encuentre disponible.

Hay varias maneras de que un usuario podría iniciar una conversación con Siri:

- En el laboratorio seleccionando el dispositivo, al presionar el botón de inicio. En esta situación Siri presentará interfaces visuales más y menos respuestas verbales.
- Diciendo: *"¡Hola Siri"* e iniciar una conversación manos libres. En esta situación Siri será menos visual y más verbal.
- Uso de características de accesibilidad, como bluetooth había habilitado audífonos donde se va a adaptar la interfaz de usuario para un usuario con necesidades especiales.
- Usar Auto Play donde el usuario necesita para mantener su atención centrábamos en garantizar manteniendo las distracciones al mínimo.

### <a name="how-the-developer-helps-siri"></a>Cómo ayuda al desarrollador a Siri

Al integrar una aplicación con Siri, el desarrollador debe probar con frecuencia esta integración y garantizar que están realizando muchas solicitudes diferentes solicita que el mismo fragmento de información o una tarea de tantas maneras como sea posible.

Puesto que no hay dos personas piensa similares, es fundamental que el desarrollador obtiene a los evaluadores de beta diferentes tantos como sea posible para ayudar a ajustar la integración de Siri. Pueden pedir a los usuarios información o realizar solicitudes de maneras el desarrollador nunca aunque de y este ajuste puede ayudar a asegurarse de que el grupo más amplio de usuarios tienen una gran experiencia con su aplicación con Siri.

Prueba en distintas situaciones y entornos. Iniciar las conversaciones con Siri en todas las formas posibles para asegurarte de que estas conversaciones fluido y naturales. Probar en ubicaciones donde el usuario sería más probable es que se usa la aplicación, como en un gimnasio atestado.

Asegúrese de que la aplicación proporciona toda la información que necesita Siri para representar correctamente la solicitud y el resultado al usuario. Esto es especialmente cierto cuando se usa Siri en una situación de manos libres.

### <a name="siri-design-guidelines"></a>Instrucciones de diseño de Siri

Recuerde siempre que Siri está teniendo una conversación con el usuario en nombre de la aplicación. El programador se desea seguro de que esta conversación permanece como fluido y natural como sea posible.

Como lo harían con cualquier conversación importante, el desarrollador debe asegurarse de lo siguiente:

- Que la aplicación está preparada para la conversación.
- Que la aplicación escucha exactamente lo que el usuario está intentando realizar.
- Que la aplicación solicita las preguntas adecuadas en el momento adecuado.
- Que la aplicación responde a la solicitud con la información que busca el usuario.

#### <a name="preparing-for-the-conversation"></a>Preparación para la conversación

Lo primero que hay que recordar es que los usuarios de la aplicación no se van a ser exactamente igual que el desarrollador. Puede provenir de distintos fondos, hablan idiomas diferentes o tiene necesidades especiales cuando se trabaja con la aplicación.

Además, dado que el desarrollador diseñado y había compilado la aplicación, profunda y tienen un conocimiento profundo de la aplicación y su funcionamiento interno y funciones que no tenga un usuario típico. Por lo que el desarrollador podría preguntarse solicitud de Siri forma diferente a un usuario normal.

Esto es por eso es fundamental contar como muchas personas diferentes como sea posible interactúan con la aplicación a través de Siri. Los usuarios pueden realizar solicitudes de la aplicación a través de Siri que nunca se pensó que el desarrollador de o de maneras que no tienen en cuenta el desarrollador.

#### <a name="ensure-the-app-is-a-good-listener"></a>Asegúrese de que la aplicación es un agente de escucha buena

El programador debe asegurarse de que la aplicación es un agente de escucha buena y está obteniendo los detalles de la conversación que cumplen las expectativas del usuario. Pero también es posible que no haya proporcionado toda la información que la aplicación necesita para llevar a cabo la tarea solicitada.

Hay varias maneras de que la aplicación podría controlar esta situación:

- **Elegir una buena opción predeterminada para el valor que falta** : por ejemplo, una aplicación de uso compartido de conducción podría predeterminado para la ubicación del usuario actual si no especifican que deseaban seleccionarse de.
- **Asegúrese de fundamentada** -con información específica que la aplicación ha recopilado en el usuario, la aplicación podría ser capaz de marca y fundamentada sobre la información que falta, como rellenar un número de teléfono móvil que falta de información de contacto del usuario. Sin embargo, debe tener cuidado para evitar sorpresas incorrectas, como una selección de la opción más cara, etcetera.
- **Símbolo del sistema para obtener más información** -la aplicación puede tener Siri preguntar al usuario para el valor que falta. Sin embargo, la clave aquí es mantener las conversaciones simple y, en el punto. Los usuarios se convertirá rápidamente en frustrado si tienen que responder varias preguntas para lograr su solicitud.
- **Controlar correctamente información errónea** -el usuario podría proporcionar un valor que no estaba esperando la aplicación o que no se puede controlar en el contexto especificado. Asegúrese de que la aplicación está relacionado con el usuario de forma que hace que sea claro y fácil corregir esta situación.

Cuando la aplicación se presenta con un valor único que está en peligro, la mejor manera de controlar esto es que Siri solicitar confirmación al usuario. Por ejemplo, *"¿Quiso decir Bobo gran?"* , que pueden responder a con un simple responda Sí o no.

Cuando hay una situación donde varias posibles opciones pueden ser correctas para un solo valor, la anulación de ambigüedades es el método de control preferido. En esta situación Siri puede pedir al usuario con hasta diez opciones posibles para elegir. Por ejemplo:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Si aún está en la pregunta, tener Siri pedir al usuario que proporcione una respuesta completamente nueva y más específica para un determinado valor.

#### <a name="request-final-confirmation"></a>Solicitar confirmación Final

Antes de que la aplicación realiza la tarea para satisfacer la solicitud del usuario, comprobará Siri con la extensión de la aplicación para asegurarse de que todo está en su lugar. ¿Por ejemplo, el usuario tiene suficiente dinero en su cuenta para realizar el pago solicitado?

Además, la aplicación debe asegurarse de que proporciona toda la información posible para Siri para que pueda presentar al usuario y confirmar que la tarea va a realizar cumple sus expectativas.

Una vez que el usuario ha confirmado la solicitud y la aplicación que haya realizado se, la aplicación necesita para asegurarse de que ha proporcionado todos los resultados a Siri para relacionarlos al usuario nuevo.

#### <a name="responding-to-the-request"></a>Responder a la solicitud

Siri tiene varias Interfaces de usuario integrados para cada uno de los dominios y las acciones que conoce. Sin embargo, en su caso, la aplicación puede proporcionar una extensión de interfaz de usuario personalizada de intención para enriquecer la experiencia del usuario presentando la personalización de marca de la aplicación y la interfaz de usuario o la información que estaba presente en la solicitud.

Es decir, retención se debe usar al diseñar interfaces personalizadas de Siri. Normalmente, el usuario desea obtener una tarea específica que se realiza tan pronto como sea posible y no desea que se puede sobrecargar con información innecesaria.

También debe tener cuidado para asegurarse de que la interfaz de usuario personalizada examina y responde correctamente en todos los dispositivos iOS diferentes y las orientaciones que el usuario podría tener o se usa el dispositivo.

Cuando proceda, usar la API de SiriKit para ocultar cualquier información redundante ya está presente en el valor predeterminado de Siri UI. Aún asegurarse de que la aplicación todavía proporciona la información a Siri por lo que pueden presentar verbalmente en un situaciones en las manos libres.

Puede haber situaciones donde Siri iniciará la aplicación para satisfacer la solicitud del usuario, como presentar las fotos que el usuario ha solicitado. En estas situaciones, no se sorprenda al usuario. Mostrar la información esperada sin necesidad de pasos intermedios o intervenciones del usuario necesaria. Mostrar la información o realizar una tarea que no espera el usuario.

### <a name="polishing-the-design"></a>Pulido el diseño

Hay varios pasos que Apple sugerir para refinar el diseño de las Interfaces de conversación. En primer lugar, se está proporcionando claro y concisos, vocabulario y utilice ejemplos de casos a Siri.

Una de las maneras en que un usuario detecta la aplicación es mediante la realización de una conversación con Siri y le pregunta, *"Qué puede hacer?"* Siri mostrará varias cosas que puede hacer, incluida la aplicación del desarrollador y el héroe de ejemplo casos de uso que ha proporcionado a través de su `plist` archivo.

Cómo escribir casos de uso de buen ejemplo:

- Asegúrese de que los ejemplos incluyen el nombre de la aplicación.
- Mantenga el ejemplo corto y a punto.
- Proporcionan diversos ejemplos para cada uno de los intentos que admite la aplicación.
- Dé prioridad a las intenciones y los ejemplos dentro de ellos en función de los casos de uso más común para la aplicación.
- Asegúrese de que la aplicación proporciona ejemplos localizados.
- Asegúrese de que cada ejemplo presentado funciona según lo previsto dentro de la aplicación.
- Evitar las direcciones en los ejemplos, por lo que no incluya el texto como Siri *"¡Hola Siri..."*
- Evite cualquier rigor innecesarios como *","* o *"thank you"*.

Tómese un tiempo adecuado para explorar y experimentar en cómo la aplicación puede dar forma a la conversación que Siri está experimentando con el usuario en su nombre. Asegúrese de hablar con los usuarios típicos a lo largo del proceso, como sus interacciones con y las expectativas de la aplicación podrían cambiar con el tiempo.

Recuerde siempre probar la aplicación en distintas situaciones y todos los distintos métodos para invocar una conversación con Siri. Prueba en ubicaciones del mundo real del usuario podría usar la aplicación, fuera de la oficina y el departamento de soporte técnico.

Procurar que las conversaciones con Siri (en nombre de la aplicación) sea dinámica de fluidos, natural y "sentir justo". 

## <a name="summary"></a>Resumen

Este artículo ha tratado los conceptos básicos necesarios para usar SiriKit y se muestra que puede interactuar con las aplicaciones de Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de Framework de intenciones](https://developer.apple.com/reference/intents)
- [Referencia de Framework de la interfaz de usuario de intenciones](https://developer.apple.com/reference/intentsui)
