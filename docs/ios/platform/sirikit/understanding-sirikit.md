---
title: Descripción de los conceptos de SiriKit
description: Este documento describe los conceptos básicos necesarios para trabajar con SiriKit en una aplicación de Xamarin.iOS. Por ejemplo, se trata del color y extensiones de interfaz de usuario de intentos, SiriKit permisos, diseñar una gran experiencia y mucho más.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 62b612f2e2725e5856a39e1d4d3fc1288282167a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788935"
---
# <a name="understanding-sirikit-concepts"></a>Descripción de los conceptos de SiriKit

_Este artículo tratan los conceptos clave que será necesarios para trabajar con SiriKit en una aplicación de Xamarin.iOS._


Nuevo en 10 de iOS, SiriKit permite que una aplicación de Xamarin.iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas en un dispositivo iOS. Esta funcionalidad se proporciona en uno o más extensión de la aplicación con el nuevo **intentos** y **calidades de interfaz de usuario** marcos de trabajo.

SiriKit permite que una aplicación iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS mediante extensiones de aplicaciones y a los nuevos **intentos** y **calidades de interfaz de usuario** marcos de trabajo.

Siri funciona con el concepto de **dominios**, grupos de conocer las acciones para las tareas relacionadas. Cada interacción con la aplicación con Siri debe estar en uno de sus dominios de servicio conocido, como se indica a continuación:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administrar entrenamientos.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de la aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera la correspondiente **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

## <a name="the-intents-and-intents-ui-extensions"></a>Las extensiones de interfaz de usuario de intentos y propósitos

Siri y la aplicación de mapas interactúan con los servicios de la aplicación a través de dos tipos diferentes de extensiones de aplicación:

- **Extensión de calidades** -Siri proporciona y se asigna a la aplicación del contenido y realiza las tareas necesarias para cumplir los intentos admitidos.
- **Extensión de la interfaz de usuario de calidades** -proporciona una interfaz de usuario personalizada que se mostrará para el contenido de la aplicación dentro de Siri o asignaciones.

La aplicación debe proporcionar una extensión de intentos para admitir SiriKit y es responsable de proporcionar información que Siri y mapas pueden presentar al usuario y para controlar los intentos.

Crear una extensión de la interfaz de usuario de intentos es opcional, ya que Siri normalmente controla toda la interacción del usuario y tiene una interfaz de usuario estándar e integrada para presentar la información en cada uno de los dominios admitidos. Si proporciona una extensión de la interfaz de usuario del color, puede utilizar la aplicación la **intención UI** framework para presentar un amplio, personalizado interfaz de usuario que incluye la aplicación de la personalización de marca y la información adicional.

## <a name="siri-and-the-maps-app-role"></a>Siri y el rol de aplicación de mapas

Las solicitudes del usuario hablado son procesan y analizan semánticamente por Siri que dichas solicitudes se convierte en intentos procesables que puede controlar las extensiones de la intención del idioma.

Mapas usa las extensiones de intención de la aplicación para mostrar información en la interfaz de mapa en respuesta a las acciones del usuario. Como solicitar cercano restaurantes u obtener las revisiones restaurante de la aplicación.

Siri y mapas administración todas las interacciones del usuario y mostrar los resultados mediante la interfaz de sistema estándar. El rol de las extensiones de aplicación es principalmente proporcionar los datos que se muestran. Opcionalmente, la aplicación puede proporcionar una extensión de la interfaz de usuario de intentos y presentar una interfaz de usuario personalizada para mejorar la interfaz de sistema de forma predeterminada.

## <a name="interacting-with-siri-via-sirikit"></a>Interactuar con Siri a través de SiriKit

Esta sección presentará información general sobre cómo SiriKit permite al usuario interactuar con la aplicación mediante Siri. Para este ejemplo, usaremos la aplicación MonkeyChat falsa:

[![](understanding-sirikit-images/monkeychat01.png "El icono de MonkeyChat")](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene su propio libro contacto de amigos del usuario, cada uno asociado con un nombre de pantalla (por ejemplo, Bobo por ejemplo) y permite al usuario enviar chats de texto a cada uno de ellos por su nombre de pantalla.

Hay muchas maneras de que el usuario puede iniciar una interacción con la aplicación, ya que cada persona podría provocar que la misma solicitud de muchas formas diferentes.

Por ejemplo, si el usuario desea enviar un mensaje a su amigo Bobo, podría tener la siguiente conversación con Siri:

_Usuario: Hola Siri, enviar un mensaje MonkeyChat._<br />
_Siri: A quien?_<br />
_Usuario: Bobo._<br />
_Siri: ¿Qué desea decir a Bobo?_<br />
_Usuario: Envíe plátano más._<br />

Otra persona podría provocar que la misma solicitud con una conversación diferentes:

_Usuario: Enviar un mensaje a Bobo en MonkeyChat._<br />
_Siri: ¿Qué desea decir a Bobo?_<br />
_Usuario: Envíe plátano más._<br />

Y otros usuarios puedan realizar una solicitud incluso más corta:

_Usuario: MonkeyChat Bobo envíe plátano más._<br />
_Siri: Correcto, enviar mensaje envíe más plátano a Bobo en Monkeychat._<br />

O incluso tener la misma solicitud en un idioma diferente:

_Usuario: MonkeyChat Bobo ningún crédito si revisión plaît envoyer más de bananes._<br />
_Siri: Oui, revisión plaît envoyer de Envoi (envío) mensaje ningún crédito si más de bananes à Bobo sur Monkeychat._<br />

Aún otro usuario podría ser muy detallado de su conversación:

_Usuario: Hola Siri, puede, no me un favor e inicie la aplicación MonkeyChat para enviar un texto con el mensaje envíe plátano más._<br />
_Siri: A quien?_<br />
_Usuario: Mi mejor pal Bobo._<br />

Además, hay muchas maneras que Siri podría responder a una solicitud, algunos en función de cómo se realizó la solicitud:

- **Manteniendo el botón Inicio** -Siri proporcionará más respuestas visuales con comentarios verbales limitado.
- **Por "¡Hola Siri"** -Siri se más verbales y proporcionar respuestas visuales menos.

Siri también está diseñado para satisfacer las necesidades de accesibilidad del usuario e interactuar y responda basándose en esas necesidades.

Con independencia de cómo se realiza una solicitud y cómo Siri responde a la solicitud, Siri controla la conversación con el usuario y la aplicación (a través de sus extensiones) proporciona la funcionalidad.

Cuando el usuario realiza una solicitud de Siri verbal, estos son los pasos que seguirá Siri:

[![](understanding-sirikit-images/monkeychat02.png "Los pasos que seguirá Siri")](understanding-sirikit-images/monkeychat02.png#lightbox)

1. En primer lugar, Siri toma el audio del usuario **voz** y lo convierte en texto.
2. A continuación, el texto se convierte en una **intención**, una estructura de representación de la solicitud del usuario.
3. Según el propósito, tardará Siri **acción** para realizar la solicitud del usuario.
4. Por último, se presentará Siri **respuestas** (visual y verbales) para el usuario en función de la acción realizada.

Hay tres maneras principales que la aplicación puede formar parte de la conversación del usuario con Siri:

[![](understanding-sirikit-images/monkeychat03.png "Los tres métodos principales que la aplicación puede tomar parte en la conversación de los usuarios con Siri")](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabulario** -trata cómo la aplicación indica Siri las palabras que necesita saber para interactuar con él.
2. **Lógica de aplicación** : estas son las acciones y las respuestas que se llevará a la aplicación según las proporcionadas intentos.
3. **Interfaz de usuario** -se trata de la interfaz de usuario personalizada opcional que la aplicación puede permitir que sus respuestas en.

### <a name="example"></a>Ejemplo

Dada la anterior información, examine la interacción de la conversación siguiente con la aplicación MonkeyChat:

_Usuario: Hola Siri, enviar un mensaje a Bobo en MonkeyChat._<br />
_Siri: ¿Qué desea decir a Bobo?_<br />
_Usuario: Envíe plátano más._<br />

La primera función que toma la aplicación de la conversación consiste en conocer Siri la voz del usuario:

[![](understanding-sirikit-images/monkeychat04.png "Ayudar a Siri a comprender la voz de los usuarios")](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri no tiene el nombre "Bobo" en su base de datos, pero la aplicación funciona y compartió esta información con Siri a través de su vocabulario. La aplicación también ayuda a Siri a reconocer que Bobo es un destinatario, ya que les ha especificado para Siri como un *póngase en contacto con*.

Siri sabe que más se necesita para enviar un mensaje que solo un destinatario, por lo que comprobará con la extensión de la aplicación para ver si un mensaje necesita contenido rápidamente. Puesto que se hace MonkeyChat, Siri responderá al usuario con la pregunta: *"¿Qué desea decir a Bobo?"*

En el ejemplo anterior, el usuario ha respondido, *"Envíe más plátano"*, que Siri se agrupar en una estructura **intención**:

[![](understanding-sirikit-images/monkeychat05.png "Siri agrupe la respuesta del usuario en un intento de estructurado")](understanding-sirikit-images/monkeychat05.png#lightbox)

La intención estructurada contendrá la siguiente información:

- **Dominio:** mensajes
- **Intención:** sendMessage
- **Destinatario:** Bobo
- **Contenido:** envíe más plátano

Cada dominio tiene como conjunto de saber *acciones* que pueden realizarse dentro de ellos y basado en el dominio y la acción, cero a muchos parámetros podrían estar incluidos en el intento se envía a la aplicación.

La intención, a continuación, se envía a la extensión de la aplicación para su procesamiento. Como resultado de la intención de procesamiento, la aplicación generará un **IntentResponse** que se incorporará con la intención e incluir parámetros que describen lo hizo en la aplicación de la aplicación con la intención.

Cada IntentResponse también incluirá un **código de respuesta** que indica que Siri si la aplicación fue capaz de completar la solicitud o no. Algunos dominios tienen códigos de respuesta de error muy específico que también se pueden enviar.

Por último, se incluirá el IntentResponse un `NSUserActivity` (como las que se usan para admitir la mano Off). El `NSUserActivity` se usará para iniciar la aplicación si la respuesta les obliga a abandonar el entorno de Siri y entrar en la aplicación para completarla. 

Siri generará una adecuada `NSUserActivity` para iniciar la aplicación y recogida donde el usuario se dejó en el entorno de Siri. Sin embargo, la aplicación puede proporcionar su propio `NSUserActivity` con personalizar información, si es necesario.

Después de que la aplicación ha procesado el intento y devolvió una respuesta a Siri, a continuación, muestra los resultados al usuario (verbalmente y visualmente):

[![](understanding-sirikit-images/monkeychat06.png "Los resultados que se presentan al usuario tanto verbalmente y visualmente")](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri tiene respuesta integrada varias Interfaces de usuario para cada uno de los dominios disponibles para la aplicación. Sin embargo, puesto que MonkeyChat ha proporcionado una extensión opcional de la interfaz de usuario de intención, se usa para presentar los resultados de la conversación para el usuario en el ejemplo anterior.

## <a name="the-intent-lifecycle"></a>El ciclo de vida intención

Hay tres tareas principales que la extensión de la aplicación necesitará realizar cuando se trabaja con propósitos:

[![](understanding-sirikit-images/monkeychat07.png "El ciclo de vida intención")](understanding-sirikit-images/monkeychat07.png#lightbox)

1. La aplicación debe **resolver** cada parámetro en un evento. Como resultado, la aplicación llamará resuelva varias veces (una vez por cada parámetro) y, a veces varias veces en el mismo parámetro hasta que la aplicación y el usuario de acuerdo en lo que se solicita.
2. La aplicación debe **confirmar** que puede controlar la intención solicitada y explíquenos Siri el resultado esperado.
3. Por último, la aplicación debe **controlar** la intención y realice los pasos para lograr el resultado solicitado.

### <a name="the-resolve-stage"></a>La fase de resolución

La fase de resolución ayuda a Siri a conocer los valores que el usuario ha proporcionado y se asegura de que lo que el usuario realmente diseñado es lo que ocurrirá cuando se procesa la intención de la aplicación. 

Esta fase también proporciona una oportunidad para que la aplicación influir en el comportamiento del Siri durante la conversación con el usuario. Para ello, la aplicación proporcionará un **respuesta con la solución**. Hay una serie de respuesta predefinido a los distintos tipos de datos que Siri entienda.

La más común será la respuesta de resolución de la aplicación **correcto**, lo que significa que la aplicación coincide con el elemento específico de datos de un parámetro (por ejemplo, el nombre de pantalla del usuario) a un fragmento de información que conoce.

Puede haber ocasiones cuando la aplicación necesita para confirmar que una determinada solicitud coincide con la parte correcta de la información que conoce. En estos casos, enviará un **ConfirmationRequired** respuesta para hacer una pregunta Sí o no al usuario como *"Enviar un mensaje a Bobo la gran?"*

Puede haber otros casos donde la aplicación necesitará al usuario elegir entre una breve lista de opciones. En este caso, la aplicación proporcionará un **desambiguación** respuesta con una lista de opciones de dos y diez para el usuario que puede elegir como: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri controlará el usuario que realiza la selección, verbalmente o mediante la interacción con la UI Siri y el resultado se enviarán a la aplicación.

En otros casos, podría no ser suficiente información para que la aplicación resolver el parámetro o puede haber demasiadas coincidencias que resolver utilizando desambiguación (por ejemplo, los 80 usuarios con Bobo en su nombre). En estos casos, la aplicación enviará un **NeedsMoreDetails** respuesta y Siri pedirá al usuario que sea más específico.

Si el usuario no proporciona un valor que es necesario para la intención de proceso, puede enviar un **NeedsValue** respuesta si desea que Siri preguntar al usuario el valor.

Si la aplicación no es compatible con un valor que el usuario ha proporcionado para un parámetro concreto, puede enviar el **UnsupportedWithReason** respuesta para proporcionar un motivo por qué no se admite el valor. Siri, a continuación, se pedirá al usuario para un valor completamente nuevo y debido a la razón es necesario.

Por último, utilice la **NotRequired** respuesta para indicar que Siri que la aplicación no requiere un valor para un parámetro dado. Si el usuario proporciona una todos modos, sencillamente se ignoran por Siri.

### <a name="the-confirm-stage"></a>La fase de confirmación

La fase de confirmación tiene dos propósitos:

- Para indicar el resultado esperado de control de un intento para que Siri puede indicar que el usuario lo que se va a ocurrir de Siri.
- Proporciona una comprobación de oportunidad en cualquier estado requiere la aplicación que tenga que completar la solicitud presentada por el usuario, como tener suficiente dinero en el banco para realizar el pago solicitado.

La aplicación proporcionará un **intención respuesta** desde el paso de confirmación, que se debe rellenar con tal y como se proporciona mucha información de la aplicación tiene disponibles, por lo que Siri puede comunicarla eficazmente con el usuario.

Según el tipo de acción y de dominio, Siri puede pedir al usuario confirmación, como antes de enviar un pago o transporte de reserva.

### <a name="the-handle-stage"></a>La fase de identificador

La fase de controlar es la parte más importante sobre cómo trabajar con un intento porque es el punto donde la aplicación satisface la solicitud del usuario mediante la realización de la tarea que se solicitó hacer.

Al igual que lo hiciera en la fase de confirmación, la aplicación debe proporcionar toda la información sobre el resultado como sea posible para que Siri puede asociar al usuario. A veces, esta información se presentará visualmente o simplemente otros Siri hable al usuario. 

Puede haber ocasiones cuando la aplicación puede requerir más tiempo en procesar una solicitud dada, por ejemplo, retrasos de llamada de red o si necesita una persona en vivo para satisfacer la solicitud (por ejemplo, completar y enviar un pedido o un automóvil que conduce a la ubicación del usuario). Cuando Siri está esperando una respuesta de la aplicación, se mostrará una interfaz de usuario espera al usuario que les indica que la aplicación está procesando la solicitud.

Idealmente, la aplicación debe proporcionar una respuesta a Siri en dos o tres segundos, como máximo. Si la aplicación conoce que una respuesta determinada se va a tardar más en procesarse, que necesita para enviar un **InProgress** código de respuesta que Siri. Siri, a continuación, se informará al usuario que la aplicación está procesando la solicitud en segundo plano y continuará hacerlo incluso si deja el entorno de Siri.

## <a name="adding-sirikit-to-the-app"></a>Agregando SiriKit a la aplicación

Con SiriKit en iOS 10, Apple ha creado dos nuevos puntos de extensión:

- **Extensión de calidades** : proporciona Siri con el contenido de la aplicación y realiza las tareas necesarias para cumplir los intentos admitidos.
- **Extensión de la interfaz de usuario de calidades** -proporciona una interfaz de usuario personalizada que se mostrará para el contenido de las aplicaciones dentro de Siri. 

También es una API para proporcionar palabras y frases a Siri para ayudar en el reconocimiento en forma de:

- **Aplicación vocabulario** -palabras y frases que son comunes a todos los usuarios de la aplicación.
- **Vocabulario del usuario** -palabras y frases que son únicos para un usuario de aplicación determinada.

## <a name="the-intents-extension"></a>La extensión de intentos

La extensión de intentos es responsable de controlar las interacciones principales entre la aplicación y Siri como se indica a continuación:

[![](understanding-sirikit-images/intents01.png "La extensión de intentos")](understanding-sirikit-images/intents01.png#lightbox)

La extensión de intención puede admitir uno o varios intentos, depende del desarrollador para decidir cómo desean implementar SiriKit en la aplicación. El desarrollador también puede agregar una extensión de intención independiente para cada intento de tener que controlar.  Es decir, Apple solicita que el desarrollador de limitar el número de intento de extensiones para que Siri no tiene varios procesos abierta en la aplicación, que requiere más memoria y tiempo para controlar.

El desarrollador también debe ser consciente de que la extensión de intención se ejecutará en segundo plano mientras Siri está activa. Esto permite Siri transportar activamente en una conversación con el usuario mientras continúa comunicándose con la extensión que se va a procesar la información sobre la solicitud.

## <a name="privacy-and-security-considerations"></a>Consideraciones de seguridad y privacidad

Apple ha tomado todas las medidas necesarias para asegurarse de que un usuario privado información es segura cuando se trabaja con Siri y por lo tanto, hay varias interacciones que requieren que el usuario iniciar sesión en el dispositivo iOS. Por ejemplo, cuando se solicita una conducción o realizar un pago.

Además, existen comportamientos específicos que la aplicación que desee limitar al usuario que se va a iniciar sesión en el dispositivo. En estas situaciones, la aplicación puede solicitar la **restringir durante un bloqueo** comportamiento. Esto se realiza mediante una configuración en el `Info.plist` archivo.

El marco de trabajo de autenticación Local está disponible para la extensión de intención, por lo que la aplicación puede pedir al usuario información de autenticación adicional, incluso si el dispositivo ya está desbloqueado.

Por último, está disponible para la extensión de intención de Apple Pay, por lo que la aplicación puede completar una transacción con Apple Pay y aparecerá la hoja de Apple Pay integrada sobre la interfaz Siri.

Además, Apple quiere asegurarse de que los usuarios sabe cuándo va a enviar información a una aplicación de entidad 3rd y por lo tanto, el usuario **debe** dice el nombre específico de la aplicación (como se especifica en el nombre para mostrar de la aplicación agrupación) al realizar una solicitud.

Apple ha diseñado Siri para llevar a cabo conversaciones naturales y fluidas con el usuario y por este motivo, el nombre del paquete de la aplicación puede usarse en muchas partes de la oración, siempre que quepa forma natural en la solicitud del usuario.

Una de las acciones comunes que llevará a cabo los usuarios es "verbify" nombre de la aplicación, en otras palabras, toma el nombre de la aplicación y su uso como un verbo en una solicitud. Por ejemplo, *"MonkeyChat Bobo fueran plátano excelente."*

## <a name="the-intents-ui-extension"></a>La extensión de la interfaz de usuario de intentos

La extensión de la interfaz de usuario de calidades presenta la oportunidad de poner la aplicación en la interfaz de usuario y personalización de marca en la experiencia de Siri y hacer que los usuarios se sienta conectado a la aplicación. Con esta extensión, puede hacer que la aplicación de la marca, así como información visual y otra en la transcripción.

[![](understanding-sirikit-images/intents02.png "Salida de extensión de interfaz de usuario de intentos de ejemplo")](understanding-sirikit-images/intents02.png#lightbox)

La extensión de la interfaz de usuario de calidades siempre devolverá un `UIViewController` y la aplicación puede agregar cualquier elemento que le guste dentro el controlador de vista como la que se muestra información adicional que vaya más allá de la respuesta inicial. La interfaz de usuario del color también puede actualizar el usuario con el estado de un evento de larga duración, como cuánto tiempo tardará transporte automóvil para llegar a su ubicación de uso compartido.

La extensión de la interfaz de usuario de intentos se siempre se muestran junto con otro contenido Siri como el icono de la aplicación y el nombre en la parte superior de la interfaz de usuario o, según el propósito, se pueden mostrar botones (por ejemplo, envío o cancelar) en la parte inferior.

Hay algunos casos donde la aplicación puede reemplazar la información que Siri muestra al usuario de forma predeterminada como mensajería o asigna donde la aplicación puede reemplazar la experiencia predeterminada con uno personalizado para la aplicación.

> [!IMPORTANT]
> Aunque es posible agregar elementos interactivos como `UIButtons` o `UITextFields` a la extensión de la interfaz de usuario de intención `UIViewController`, estos se prohíben estrictamente como la interfaz de usuario de intención en no interactivo y el usuario no podrá interactuar con ellos.

Es totalmente opcional para la aplicación proporciona una extensión de la interfaz de usuario de intención puesto que Siri contiene un conjunto predeterminado de interfaz de usuario para cada tipo de intención. Además, las interfaces de la interfaz de usuario de calidades solo están disponibles para ciertos propósitos que se considere a Apple sería útiles para el usuario.

## <a name="adding-sirikit-vocabulary"></a>Agregar SiriKit vocabulario

El último fragmento de la implementación de SiriKit se encuentra dentro de la aplicación proporcionando el vocabulario necesario. Muchas aplicaciones tienen formas únicas de describir la información para el usuario y que el usuario proporcionará información a la aplicación de manera exclusiva.

Por este motivo, Siri requiere asistencia de la aplicación para entender las palabras y frases únicos para la aplicación. Algunas de estas frases va a formar parte de la aplicación para que todos los usuarios se conoce y entenderlos. Todavía otros serán exclusivos para un usuario determinado de la aplicación.

### <a name="app-specific-vocabulary"></a>Vocabulario específica de la aplicación

El vocabulario específico de la aplicación define las palabras y frases específicas que se conocerá a todos los usuarios de la aplicación, como tipos de vehículos o nombres de entrenamiento. Dado que estos son parte de la aplicación, se definen en un `AppIntentVocabulary.plist` archivo como parte de la agrupación de la aplicación principal. Además, se deben localizar estas palabras y frases.

Hay varias partes a un vocabulario `AppIntentVocabulary.plist` archivo:

- **Aplicación de ejemplo usa** -proporcionan un conjunto de casos de uso común para las solicitudes que puede realizar el usuario de la aplicación. Por ejemplo: *"Iniciar un entrenamiento con MonkeyFit".*
- **Parámetros** -proporcionan un conjunto de tipos de parámetro estándar no son específicos de la aplicación. Por ejemplo, nombres de entrenamiento de la aplicación MonkeyFit. Estas constan de:
    - **Frase** -permite a la aplicación definir términos únicos para la aplicación. Por ejemplo: el tipo de entrenamiento "Bananarific" de la aplicación MonkeyFit. 
    - **Pronunciación** -proporciona sugerencias de pronunciación a Siri como un simple fonética para una determinada frase. Por ejemplo, "ba nana ri FICA".
    - **Ejemplo** -proporciona un ejemplo del uso de la frase determinada en la aplicación. Por ejemplo, *"Iniciar un Bananarific en MonkeyFit"*.

Para obtener más información, vea de Apple [referencia del formato de archivo de aplicación vocabulario](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

### <a name="user-specific-vocabulary"></a>Vocabulario específico de usuario

El vocabulario específica del usuario se va a proporcionar palabras o frases que son únicos a usuarios individuales de la aplicación. Estos se proporcionarán en tiempo de ejecución de la aplicación principal (no en las extensiones de aplicación) como un conjunto ordenado de términos que se ordenan en una prioridad de uso más importante para los usuarios, con los términos más importantes al principio de la lista.

Eche un vistazo en el ejemplo de la aplicación MonkeyChat presentado anteriormente. MonkeyChat mantiene una lista de todos los contactos del usuario, que van a enviar a Siri mediante el vocabulario específica del usuario. También mantiene una lista de los 10 contactos más reciente que el usuario ha mensajes y tiene un conjunto de contactos de favorito para cada usuario. En este ejemplo, los contactos favoritos deben ser al principio de nuestro vocabulario específico de usuario, seguido de los contactos recientes, a continuación, el resto de los contactos del usuario.

Se admiten los siguientes tipos de información por vocabulario específico de usuario:

- Póngase en contacto con los nombres.
- Nombres de entrenamiento.
- Nombres de álbumes de fotos.
- Palabras clave de la foto.

Si la aplicación se basa en la libreta de direcciones de iOS, la aplicación no tendrá que realizar ninguna acción porque esta información ya está disponible para Siri. La aplicación solo debe proporcionar nombres de los contactos si la aplicación tiene su propia base de datos única de contactos.

Al diseñar el vocabulario, solo proporcionan los valores es necesarios que los usuarios conoce y le importa. Evite proporcionar información como números de teléfono o direcciones de correo electrónico.

La aplicación también debe actualizar con prontitud Siri cuando cambia el vocabulario específico de usuario. Los usuarios acostumbrados a solicitar información de Siri el momento en que se ha agregado a su dispositivo iOS. Por ejemplo, si el usuario agrega un nuevo contacto en la aplicación, enviar esa información a Siri tan pronto como el usuario lo guarda.

Lo que es más importante, la aplicación _debe_ eliminar información desde el vocabulario Siri rápidamente ya que podría ser alterar un usuario cuando elimina una parte de la información pero Siri se siguen reconociendo, horas o días más tarde.

> [!IMPORTANT]
> La aplicación debe quitar todos del vocabulario específico de usuario de Siri si el usuario decide restablecer la aplicación o si se cierre de sesión.

## <a name="sirikit-permissions"></a>Permisos de SiriKit

La última pieza del SiriKit se centra en los permisos. Al igual que con otras características de operaciones de E/s (por ejemplo, fotos, cámara o contactos), los usuarios deben conceder permiso explícito para la aplicación para comunicarse con Siri.

La aplicación es capaz de proporcionar una cadena que define qué información proporcionará a Siri y dar el motivo en cuanto a por qué el usuario debe conceder este acceso. 

Apple sugiere que la aplicación debe solicitar permiso del usuario para usar la hora de la primera Siri que el usuario abre la aplicación después de que se actualicen a iOS 10. Se trata de forma que los usuarios saber acerca de la integración de Siri y pueden previamente aprobado uso antes de realizar su primera solicitud.

## <a name="sirikit-and-maps"></a>SiriKit y mapas

SiriKit es una parte integral de iOS y hace uso de un marco intentos más grande agregado a iOS 10. El marco de trabajo de intentos se diseñó para compartir las acciones comunes y compartidas e intentos con otras partes del sistema.

El marco de trabajo de intentos va más allá de la integración de Siri simplemente y proporciona otras características como la integración de contactos donde la aplicación puede convertirse en la telefonía predeterminada o una aplicación de mensajería para contactos específicos. Intentos también proporcionan una integración profunda con CallKit para proporcionar a los usuarios la mejor experiencia posible de VOIP.

La aplicación de mapas en iOS 10 ha agregado características como el uso compartido de transporte donde el usuario puede reservar una conducción directamente dentro de la interfaz de usuario de mapas. SiriKit proporciona un punto de extensión común a los mapas para que intentos de uso compartido (y otros) de carrera se pueden compartir entre Siri y mapas. 

Esto significa que si la aplicación ha adoptado las extensiones SiriKit, también obtendrá la integración de mapas de forma gratuita. 

## <a name="designing-a-great-siri-experience"></a>Diseñar una excelente experiencia de Siri

Diseñar una experiencia óptima al usuario cuando se integra una aplicación en Siri es diferente a diseñar una interfaz de usuario de excelente aplicación. A diferencia de normales situaciones donde el usuario está interactuando con la aplicación directamente en pantalla, al usar Siri allí son muchas veces cuando ninguna interfaz visual está visible en absoluto. Por ejemplo, cuando el usuario ha iniciado la conversación con *"¡Hola Siri"*.

### <a name="how-siri-helps-the-developer"></a>Cómo Siri ayuda al programador

Al diseñar las interacciones de una aplicación con Siri, la aplicación va a crear un *interfaz conversacional*, lo que significa que el contexto se deriva de la conversación cuyo Siri con el usuario en nombre de la aplicación.

En ausencia de una referencia visual, el usuario debe realizar un seguimiento de la información que se presentan en su encabezado. Por este motivo, Siri presenta la información mínima necesarios para llevar a cabo la tarea se desea realizar el usuario.

La interfaz de conversación está formada por las preguntas y respuestas del usuario y el Siri durante la conversación. Por lo que es importante pensar acerca de cómo Siri plantea algunas preguntas y responde al diseñar esta interfaz.

Considere el ejemplo siguiente del usuario que crea un mensaje, Siri puede responder a la pregunta, *"Listo para su envío?"* . El usuario puede responder de muchas maneras diferentes, como *"Enviar"*, *"Cancelar"* o incluso algo totalmente no relacionados a esta pregunta. Con independencia de cómo se reproduce la conversación, Siri controlará para la aplicación y solo envía la información relevante cuando se encuentre disponible.

Hay varias maneras de que un usuario puede iniciar una conversación con Siri:

- Recoger el dispositivo, al presionar el botón de inicio. En esta situación Siri presentará interfaces visuales más y menos verbales respuestas.
- Diciendo *"¡Hola Siri"* e iniciar una conversación de manos libres. En esta situación Siri será menos visual y más verbales.
- Mediante las características de accesibilidad, como bluetooth habilitado dispositivos de audición donde se va a adaptar la interfaz de usuario para un usuario con necesidades especiales.
- Usar automóvil reproducir donde el usuario debe mantener su atención enfocada en impulsar manteniendo distracciones al mínimo.

### <a name="how-the-developer-helps-siri"></a>Cómo ayuda al programador a Siri

Cuando se integra una aplicación con Siri, el desarrollador debe probar esta integración a menudo y asegúrese de que están realizando muchas solicitudes diferentes solicitando el mismo fragmento de información o una tarea de maneras diferentes tantos como sea posible.

Puesto que no hay dos personas reflexión similares, es fundamental que el desarrollador obtenga a tantos evaluadores beta diferentes como sea posible para ayudar a ajustar la integración de Siri. Los usuarios pueden solicitar la información o hacer solicitudes de maneras el programador nunca aunque de y este ajuste puede ayudar a asegurarse de que el grupo más amplio de usuarios tienen una gran experiencia con su aplicación con Siri.

Probar en situaciones diferentes y entornos. Iniciar las conversaciones con Siri en todas las formas posibles para asegurarse de que esas conversaciones permanecerán fluido y naturales. Pruebas en los lugares donde el usuario más probable es que se utilizara la aplicación, como en un gimnasio llena.

Asegúrese de que la aplicación proporciona toda la información que necesita Siri representar correctamente la solicitud y el resultado al usuario. Esto es especialmente cierto cuando el uso de Siri en una situación de manos libres.

### <a name="siri-design-guidelines"></a>Instrucciones de diseño de Siri

Recuerde siempre que Siri tiene una conversación con el usuario en el nombre de la aplicación. El programador se desea seguro de que esta conversación permanece como fluido y natural como sea posible.

Tal y como lo harían con cualquier conversación importante, el desarrollador debe asegurarse de lo siguiente:

- Que la aplicación esté preparada para la conversación.
- Que la aplicación escucha exactamente lo que el usuario está intentando realizar.
- Que la aplicación solicita las preguntas adecuadas en el momento adecuado.
- Que la aplicación responde a la solicitud con la información que está buscando el usuario.

#### <a name="preparing-for-the-conversation"></a>Preparación de la conversación

Lo primero que hay que recordar es que los usuarios de la aplicación no van a ser exactamente igual que el desarrollador. Puede proceder de diferentes fondos, hablan idiomas diferentes o tiene necesidades especiales cuando se trabaja con la aplicación.

Además, dado que el desarrollador diseñado y había compilado la aplicación, profundas, tienen un conocimiento profundo de la aplicación y sus mecanismos internos y las funciones que no tenga un usuario típico. Por lo que el programador podría solicitar solicitud de Siri de forma diferente a un usuario normal.

Este es el motivo por el que es esencial tener muchas personas diferentes como sea posible interactúan con la aplicación a través de Siri. Los usuarios pueden realizar las solicitudes de la aplicación a través de Siri que el desarrollador que nunca ha pensado de o de formas que no tienen en cuenta el programador.

#### <a name="ensure-the-app-is-a-good-listener"></a>Asegúrese de que la aplicación es un buen oyente

El desarrollador debe asegurarse de que la aplicación es un agente de escucha buena y obtiene los detalles de la conversación que cumplen las expectativas del usuario. Pero también es posible que no haya proporcionado toda la información que necesita la aplicación para llevar a cabo la tarea solicitada.

Hay varias maneras de que la aplicación puede controlar esta situación:

- **Elegir una buena opción predeterminada para el valor que falta** : por ejemplo, una aplicación de uso compartido de conducción podría predeterminado para la ubicación del usuario actual si no especifican que deseaban recogerán desde.
- **Hacer una conjetura** -con información específica que la aplicación ha recopilado en el usuario, la aplicación podría ser capaz de marca y conjetura en la información que falta, como rellenar un número de teléfono móvil que falta de información de contacto del usuario. Sin embargo, debe tener cuidado para evitar las sorpresas incorrectas, como una selección de la opción más cara, etcetera.
- **Símbolo del sistema para obtener más información** -la aplicación puede tener Siri preguntar al usuario el valor que falta. Sin embargo, la clave aquí es mantener las conversaciones simples y hasta el punto. Los usuarios se convertirá rápidamente en frustrado si tienen para responder a varias preguntas para lograr su solicitud.
- **Administrar información errónea correctamente** -el usuario puede proporcionar un valor que no se esperaba la aplicación o que no se puede administrar en el contexto especificado. Asegúrese de que la aplicación está relacionada con el usuario de una manera que hace más clara y facilita la corregir esta situación.

Cuando la aplicación se presenta con un valor único que está en peligro, la mejor manera de controlar esto es que Siri solicitar confirmación al usuario. Por ejemplo, *"¿Quiso decir Bobo la gran?"* , que puede responder a con una respuesta Sí o no sencilla.

Cuando se produce una situación donde varias opciones posibles pueden ser correctas para un valor único, la anulación de ambigüedades es el método preferido del control. En esta situación Siri puede pedir al usuario con hasta diez opciones posibles que puede elegir. Por ejemplo:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Si todavía en cuestión, tener Siri solicitar al usuario que proporcione una respuesta completamente nueva, más específica para un valor determinado.

#### <a name="request-final-confirmation"></a>Solicitar confirmación Final

Antes de que la aplicación realice realmente la tarea para satisfacer la solicitud del usuario, Siri comprobará con la extensión de la aplicación para asegurarse de que todo está en su lugar. ¿Por ejemplo, el usuario tiene suficiente dinero en su cuenta para realizar el pago solicitado?

Además, la aplicación debe asegurarse de que ofrece toda la información posible para Siri para que pueda presentar al usuario y confirmar que la tarea va a realizar cumple sus expectativas.

Una vez que el usuario ha confirmado la solicitud y la aplicación ha realizado las necesidades de la aplicación para asegurarse de que ha proporcionado todos los resultados hacia Siri por lo que puede asociar al usuario nuevo.

#### <a name="responding-to-the-request"></a>Responde a la solicitud

Siri tiene varias Interfaces de usuario integrada para cada uno de los dominios y las acciones que conocen. Sin embargo, en su caso, la aplicación puede proporcionar una extensión de interfaz de usuario personalizada de intención para enriquecer la experiencia del usuario al presentar información de personalización de la aplicación y la interfaz de usuario o más información que estaba presente en la solicitud.

Es decir, retención debe usarse al diseñar interfaces personalizadas para Siri. Normalmente, el usuario desea obtener una tarea específica que se realiza tan pronto como sea posible y no desea volver a sobrecargarse con información innecesaria.

También debe tener cuidado para asegurarse de que la interfaz de usuario personalizada busca y responde correctamente en todos los dispositivos iOS diferentes y orientaciones que el usuario podría tener o usar el dispositivo.

Cuando proceda, usar la API SiriKit para ocultar cualquier información redundante ya está presente en el valor predeterminado Siri UI. Asegúrese de que la aplicación todavía está proporcionando la información que Siri por lo que puede presentar verbalmente en situaciones de manos libres todavía.

Puede haber situaciones donde Siri iniciará la aplicación para satisfacer la solicitud del usuario, como presentar las fotos que el usuario ha solicitado. En estas situaciones, no le sorprende al usuario. Mostrar la información esperada sin necesidad de pasos intermedios o aún más el requisito de interacción. No mostrar nunca información o realizar una tarea que no está esperando el usuario.

### <a name="polishing-the-design"></a>Pulir el diseño

Hay varios pasos que Apple sugieren para refinar el diseño de las Interfaces de conversación. En primer lugar, se está proporcionando claras y concisos vocabulario y el uso mayúsculas ejemplos para Siri.

Una de las maneras en que un usuario detecta la aplicación consiste en iniciar una conversación con Siri y solicitar, *"Qué puede hacer?"* Siri mostrará varias cosas que puede hacer, incluida la aplicación del desarrollador y el héroe de ejemplo casos de uso que se proporcionan a través de su `plist` archivo.

Cómo escribir casos de uso de buen ejemplo:

- Asegúrese de que los ejemplos incluyen el nombre de la aplicación.
- Mantenga el ejemplo a corto y a punto.
- Proporcionan diversos ejemplos para cada uno de los tipos de color que admite la aplicación.
- Dé prioridad a los tipos del color y los ejemplos dentro de ellos en función de los casos de uso más común para la aplicación.
- Asegúrese de que la aplicación proporcione ejemplos localizados.
- Asegúrese de que cada ejemplo presentado funciona según lo previsto dentro de la aplicación.
- Evitar direcciones Siri en los ejemplos, por lo que no incluya el texto como *"¡Hola Siri..."*
- Evite cualquier rigor innecesarios como *","* o *"agradecimiento"*.

Tomar el momento adecuado para explorar y experimentar en cómo la aplicación puede dar forma a la conversación cuyo Siri con el usuario en su nombre. Asegúrese de hablar con los usuarios típicos durante el proceso, como sus interacciones con y las expectativas de la aplicación pueden cambiar con el tiempo.

Recuerde siempre probar la aplicación en diferentes situaciones y todos los métodos diferentes para invocar una conversación con Siri. Prueba en ubicaciones del mundo real del usuario puede usar la aplicación, fuera de la oficina y el departamento de soporte.

Procurar que las conversaciones con Siri (en nombre de la aplicación) sean fluido, natural y "sentirán simplemente". 

## <a name="summary"></a>Resumen

En este artículo se trata los conceptos básicos necesarios para usar SiriKit y se muestra que puede interactuar con las aplicaciones de Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas en un dispositivo iOS.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de marco de intentos](https://developer.apple.com/reference/intents)
- [Referencia de marco de interfaz de usuario de intentos](https://developer.apple.com/reference/intentsui)
