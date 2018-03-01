---
title: SiriKit
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/07/2017
ms.openlocfilehash: 0240dd5e381694a31ba9ebb12dd166ca0ef54750
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="sirikit"></a>SiriKit

SiriKit se introdujo en iOS 10, con un número de dominios del servicio (incluido entrenamientos, transporte de reserva y realizar llamadas). Hacer referencia a la [SiriKit sección](~/ios/platform/sirikit/index.md) para SiriKit conceptos y cómo implementar SiriKit en la aplicación.

![Demostración de lista de tareas de Siri](sirikit-images/sirikit.png)

SiriKit en iOS 11 agrega estos dominios intención nuevos y actualizados:

- [**Enumera y notas de la** ](#listsnotes) : nuevo! Proporciona una API para aplicaciones para procesar las tareas y notas.
- **Códigos de visuales** : nuevo! Siri puede mostrar códigos QR para compartir información de contacto o participar en transacciones de pago.
- **Pagos** : agregar propósitos de búsqueda y la transferencia para las interacciones de pago.
- **Invalidar reserva** : agregado cancelar los intentos de viaje y comentarios.

Otras nuevas características incluyen:

- [**Nombres de aplicaciones alternativo** ](#alternativenames) – proporciona alias que ayudan a los clientes indican Siri para tener como destino de la aplicación, ya que ofrece nombres/pronunciaciones alternativas.
- **A partir de entrenamientos** : proporciona la capacidad de iniciar un entrenamiento en segundo plano.

A continuación se explican algunas de estas características. Para obtener más detalles sobre los demás, consulte [documentación de Apple SiriKit](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Listas y notas

El nuevo dominio de listas y notas proporciona una API para aplicaciones para procesar las tareas y notas a través de solicitudes de voz Siri.

**Tareas**

- Tiene un título y el estado de finalización.
- Opcionalmente, incluye una fecha límite y una ubicación.

**Notas**

- Tiene un título y un campo de contenido.

Notas y tareas se pueden organizar en grupos. El resto de esta sección describe cómo implementar este nuevo dominio con SiriKit, usando la [TasksNotes SiriKit ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/).

### <a name="how-to-process-a-sirikit-request"></a>Cómo procesar una solicitud de SiriKit

Procesar una solicitud de SiriKit siguiendo estos pasos:

1. **Resolver** : validar parámetros y solicitar más información del usuario (si es necesario).
2. **Confirmar** : validación Final y que se puede procesar la solicitud de comprobación.
3. **Controlar** : realizar la operación (actualización de datos o realizar operaciones de red).

Los dos primeros pasos son opcionales (aunque se recomienda), y el último paso es necesario.
Hay instrucciones más detalladas en el [SiriKit sección](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Resolver y confirme métodos

Estos métodos opcionales permiten que el código se efectúen la validación, seleccione los valores predeterminados o información adicional de la solicitud del usuario.

Por ejemplo, para la `IINCreateTaskListIntent` interfaz, el método requerido es `HandleCreateTaskList`. Hay cuatro métodos opcionales que proporcionan más control sobre la interacción de Siri:

- `ResolveTitle` : Valida el título, establece un título predeterminado (si procede) o indica que los datos no son necesarios.
- `ResolveTaskTitles` : Valida la lista de tareas pronunciado por el usuario.
- `ResolveGroupName` : Valida el nombre del grupo, elija un grupo predeterminado o indica que los datos no son necesarios.
- `ConfirmCreateTaskList` : Valida que el código puede realizar la operación solicitada, pero no la realiza (solo el `Handle*` métodos deben modificar los datos).

### <a name="handle-the-intent"></a>Identificador de la intención

Hay seis intentos en el dominio de listas y notas, tres para las tareas y tres para las notas.
Los métodos que debe implementar para administrar estos intentos son:

- Las tareas:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Notas:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Cada método tiene un tipo de intención específico pasado a él, que contiene toda la información que Siri ha analizado de la solicitud del usuario (y posiblemente se actualizan en el `Resolve*` y `Confirm*` métodos).
La aplicación debe analizar los datos proporcionados, a continuación, realizar algunas acciones para almacenar o procesar de otra forma los datos y devuelven un resultado que Siri habla y muestra al usuario.

### <a name="response-codes"></a>Códigos de respuesta

Requerido `Handle*` y opcionales `Confirm*` métodos indican un código de respuesta estableciendo un valor en el objeto que se pasan a su controlador de finalización. Las respuestas proceden de la `INCreateTaskListIntentResponseCode` enumeración:

- `Ready` : Devuelve Durante la fase de confirmación (ie. desde un `Confirm*` método, pero no desde un `Handle*` método).
- `InProgress` : Se usa para tareas de larga duración (por ejemplo, una operación de red/servidor).
- `Success` – Responde con los detalles de la operación correcta (solo desde un `Handle*` método).
- `Failure` : Significa que se produjo un error y no se pudo completar la operación.
- `RequiringAppLaunch` : No se puede procesar con la intención, pero es posible en la aplicación de la operación.
- `Unspecified` : No use: mensaje de error se mostrará al usuario.

Obtener más información sobre estos métodos y las respuestas de Apple [SiriKit enumera y notas de la documentación de](https://developer.apple.com/documentation/sirikit/lists_and_notes).

### <a name="implementing-lists-and-notes"></a>Implementación de listas y notas

El [TasksNotes SiriKit ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/) se creó mediante los pasos siguientes para agregar compatibilidad con SiriKit a una aplicación de iOS en blanco.

En primer lugar, para agregar compatibilidad de SiriKit, siga estos pasos para la aplicación de iOS:

1. Graduación **SiriKit** en **Entitlements.plist**.
2. Agregar el **privacidad: descripción de uso de Siri** clave a **Info.plist**, junto con un mensaje para sus clientes.
3. Llame a la `INPreferences.RequestSiriAuthorization` método en la aplicación, para pedir al usuario que permita las interacciones de Siri.
4. Agregar SiriKit a su ID de aplicación en el Portal para desarrolladores y volver a crear los perfiles de aprovisionamiento para incluir los derechos de nuevo.

A continuación, agregue un nuevo proyecto de extensión a la aplicación para controlar las solicitudes de Siri:

1. Haga doble clic en la solución y elija **Agregar > Agregar nuevo proyecto...** .
2. Elija la **iOS > extensión > extensión intentos** plantilla.
3. Se agregarán dos nuevos proyectos: intención y IntentUI. Personalizar la interfaz de usuario es opcional, por lo que el ejemplo solo incluye el código en el **intención** proyecto.

El proyecto de extensión es donde se procesarán todas las solicitudes de SiriKit. Como una extensión independiente, no tienen automáticamente cualquier medio para comunicarse con la aplicación principal: Esto normalmente se resuelve mediante la implementación de almacenamiento de archivos compartidos con grupos de aplicaciones.

#### <a name="configure-the-intenthandler"></a>Configurar la IntentHandler

El `IntentHandler` clase es el punto de entrada para las solicitudes Siri: cada intento se pasa a la `GetHandler` método, que devuelve un objeto que puede atender la solicitud.

El código siguiente muestra una implementación sencilla:

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

La clase debe heredar de `INExtension`, y dado que el ejemplo se va a listas de control y notas de intentos, también implementa `IINNotebookDomainHandling`.

> [!NOTE]
> **Nota sobre la nomenclatura:** hay una convención en .NET para las interfaces para llevar un prefijo con una letra mayúscula `I`, que Xamarin se adhiere a cuando se enlaza protocolos desde el SDK de iOS.
>
> Xamarin también conserva los nombres de tipo de iOS y Apple utiliza los dos primeros caracteres en nombres de tipo para reflejar el marco de trabajo que un tipo al que pertenece.
>
> Para el `Intents` framework, los tipos tienen el prefijo `IN*` (p. ej. `INExtension`) pero son _no_ interfaces.
> También sigue protocolos (que se convierten en interfaces de C#) acaban con dos `I`s, como `IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>Control del color

Cada intento (Agregar tarea, establezca el atributo de tarea, etcetera) se implementa en un único método similar al que se muestra a continuación. El método debe realizar tres funciones principales:

1. **Procesar la intención** : los datos analizados por Siri debe ponerse a disposición en un `intent` específica del tipo de la intención de objeto. Puede que la aplicación haya validado datos con opcional `Resolve*` métodos.
2. **Validar y actualizar el almacén de datos** : guardar datos en el sistema de archivos (usar grupos de aplicaciones para que también puede acceder a la aplicación de iOS principal), o a través de una solicitud de red.
3. **Proporcionar una respuesta** : Use la `completion` controlador para enviar una respuesta al Siri a lectura/Mostrar al usuario:

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

Tenga en cuenta que `null` se pasa como segundo parámetro a la respuesta: este es el parámetro de actividad de usuario y cuando no se proporciona, se usará un valor predeterminado.
Puede establecer un tipo de actividad personalizados siempre y cuando la aplicación de iOS es compatible con él a través de la `NSUserActivityTypes` clave en **Info.plist**. A continuación, puede controlar este caso, cuando se abre la aplicación y realizar operaciones concretas (por ejemplo, abrir a un controlador de vista relevante y cargar los datos de la operación de Siri).

El ejemplo también codifica el `Success` resultado, pero en escenarios reales, se deben agregar los informes de errores adecuado.

### <a name="test-phrases"></a>Frases de prueba

Las siguientes frases de prueba deben funcionar en la aplicación de ejemplo:

- "Elabora una lista de ultramarinos con manzanas y plátano, peras de TasksNotes"
- "Agregar tarea WWDC en TasksNotes"
- "Agregar tarea WWDC a lista de entrenamiento en TasksNotes"
- "Marca asistir a WWDC como completadas en TasksNotes"
- "En TasksNotes Recordármelo comprar un iphone cuando llegue a casa"
- "Marca de comprar iPhone como completado en TasksNotes"
- "Recordármelo dejar inicio a las 8 a.m. en TasksNotes"

![Crear un nuevo ejemplo de lista](sirikit-images/createtasklist-sml.png) ![Conjunto de tareas como ejemplo completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> El 11 de iOS Simulator admite pruebas con Siri (a diferencia de las versiones anteriores).
>
> Si las pruebas en dispositivos reales, no olvide configurar su ID de aplicación y el aprovisionamiento de perfiles para la compatibilidad con SiriKit.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nombres alternativos

Esta nueva característica de iOS 11 significa que se pueden configurar nombres alternativos para la aplicación ayudar a los usuarios desencadenar de correctamente con Siri. Agregue las claves siguientes a la **Info.plist** archivo del proyecto de aplicación de iOS:

![Info.plist que muestra los valores y claves de nombre de aplicación alternativo](sirikit-images/alternative-names.png)

En el conjunto de nombres alternativos de aplicación, las siguientes frases también funcionará para la aplicación de ejemplo (que se denomina realmente **TasksNotes**):

- "Crear una lista de ultramarinos con manzanas y plátano, peras en _MonkeyNotes_"
- "Agregar tareas WWDC en _MonkeyTodo_"


## <a name="troubleshooting"></a>Solución de problemas

Algunos errores que pueden surgir al ejecutar el ejemplo o agregar SiriKit a sus propias aplicaciones:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Excepción de C de objetivo.  Nombre: NSInternalInconsistencyException razón: uso de la clase < INPreferences: 0x60400082ff00 > desde una aplicación requiere la com.apple.developer.siri de derechos. ¿Ha habilitado la capacidad de Siri en el proyecto de Xcode?_

- SiriKit esté marcada en **Entitlements.plist**.
- **Entitlements.plist** está configurado en el **Project Options > compilar > agrupación de firma de iOS**.

  [![Opciones de proyecto que muestra que los derechos ha configurado correctamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png)

- (para la implementación de dispositivo) Identificador de la aplicación tiene SiriKit habilitado y perfil de aprovisionamiento descargado.


## <a name="related-links"></a>Vínculos relacionados

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Ejemplo de SiriKit TasksNotes](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [What's New en SiriKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/214/)
