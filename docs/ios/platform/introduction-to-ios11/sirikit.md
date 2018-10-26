---
title: Actualizaciones de SiriKit en iOS 11
description: Este documento describe cómo trabajar con SiriKit en iOS 11. En concreto, examina cómo trabajar con tareas y notas y cómo proporcionar nombres alternativos para una aplicación.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/07/2017
ms.openlocfilehash: 7e895dc2865880ec2789a40f8cdf047a20f8693b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111043"
---
# <a name="sirikit-updates-in-ios-11"></a>Actualizaciones de SiriKit en iOS 11

SiriKit se introdujo en iOS 10, con un número de dominios del servicio (incluidos los ejercicios de manejo de reserva y la realización de llamadas). Hacer referencia a la [SiriKit sección](~/ios/platform/sirikit/index.md) para los conceptos de SiriKit y cómo implementar SiriKit en su aplicación.

![Demostración de lista de tareas de Siri](sirikit-images/sirikit.png)

SiriKit en iOS 11 agrega estos dominios intención nuevos y actualizados:

- [**Enumera y notas de la** ](#listsnotes) : nuevo! Proporciona una API para las aplicaciones para procesar tareas y notas.
- **Códigos de visuales** : nuevo! Siri puede mostrar los códigos QR para compartir información de contacto, o bien participar en transacciones de pago.
- **Los pagos** : Agregar búsqueda y transferencia de intenciones para las interacciones de pago.
- **Reservas de viaje** : se ha agregado cancelar las intenciones de andar y comentarios.

Entre las otras características nuevas se incluyen estas:

- [**Los nombres de aplicación alternativo** ](#alternativenames) : proporciona los alias que ayudan a los clientes saber Siri para tener como destino la aplicación, ya que ofrece los nombres de/pronunciaciones alternativas.
- **Iniciando el programa de ejercicios físicos** : proporciona la capacidad de iniciar un entrenamiento en segundo plano.

A continuación se explican algunas de estas características. Para obtener más detalles sobre los demás, consulte [la documentación de Apple SiriKit](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Listas y notas

El nuevo dominio de las listas y notas proporciona una API para las aplicaciones para procesar tareas y notas a través de solicitudes de voz Siri.

**Tareas**

- Tiene un título y un estado de finalización.
- Opcionalmente, incluir una fecha límite y una ubicación.

**Notas**

- Tiene un título y un campo de contenido.

Notas y tareas se pueden organizar en grupos. El resto de esta sección describe cómo implementar este nuevo dominio con SiriKit, utilizando el [TasksNotes SiriKit ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/).

### <a name="how-to-process-a-sirikit-request"></a>Cómo procesar una solicitud de SiriKit

Procesar una solicitud de SiriKit siguiendo estos pasos:

1. **Resolver** : validar los parámetros y solicitar más información del usuario (si es necesario).
2. **Confirmar** : validación Final y que se puede procesar la solicitud de comprobación.
3. **Controlar** : realizar la operación (actualización de datos o realizar operaciones de red).

Los dos primeros pasos son opcionales (aunque se recomienda), y el último paso es necesario.
Hay instrucciones más detalladas en el [SiriKit sección](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Resolver y confirme los métodos

Estos métodos opcionales permiten que el código realice la validación, seleccione los valores predeterminados o solicitar información adicional del usuario.

Por ejemplo, para el `IINCreateTaskListIntent` interfaz, el método requerido es `HandleCreateTaskList`. Hay cuatro métodos opcionales que proporcionan más control sobre la interacción de Siri:

- `ResolveTitle` : Valida el título, establece un título predeterminado (si procede) o indica que no se requieren los datos.
- `ResolveTaskTitles` : Valida la lista de tareas que se habla por el usuario.
- `ResolveGroupName` : Valida el nombre del grupo, elige un grupo predeterminado o indica que no se requieren los datos.
- `ConfirmCreateTaskList` : Valida que el código puede realizar la operación solicitada, pero no realizar (solo la `Handle*` métodos deben modificar datos).

### <a name="handle-the-intent"></a>Controlar la intención

Hay seis intenciones en el dominio de las listas y notas, tres de las tareas y tres para las notas.
Los métodos que deben implementar para controlar estos intentos son:

- Las tareas:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Notas:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Cada método tiene un tipo en intención específico pasado a él, que contiene toda la información que se ha analizado Siri de la solicitud del usuario (y posiblemente se actualizan en el `Resolve*` y `Confirm*` métodos).
La aplicación debe analizar los datos proporcionados, a continuación, realizar algunas acciones para almacenar o procesar en caso contrario, los datos y devolver un resultado que Siri habla y muestra al usuario.

### <a name="response-codes"></a>Códigos de respuesta

Necesario `Handle*` opcionales y `Confirm*` métodos indican un código de respuesta estableciendo un valor en el objeto que pasa a su controlador de finalización. Las respuestas que proceden de la `INCreateTaskListIntentResponseCode` enumeración:

- `Ready` – Devuelve Durante la fase de confirmación (ie. desde una `Confirm*` método, pero no desde un `Handle*` método).
- `InProgress` : Se usa para tareas de larga ejecución (por ejemplo, una operación de red o servidor).
- `Success` – Responde con los detalles de la operación correcta (solo desde un `Handle*` método).
- `Failure` : Significa que se produjo un error y no se pudo completar la operación.
- `RequiringAppLaunch` : No puede procesar la intención, pero es posible en la aplicación de la operación.
- `Unspecified` : No utilice: mensaje de error se mostrará al usuario.

Más información sobre estos métodos y las respuestas en Apple [SiriKit enumera y notas de la documentación de](https://developer.apple.com/documentation/sirikit/lists_and_notes).

### <a name="implementing-lists-and-notes"></a>Implementación de las listas y notas

El [TasksNotes SiriKit ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/) se creó mediante los pasos siguientes para agregar compatibilidad con SiriKit a una aplicación iOS vacía.

En primer lugar, para agregar compatibilidad con SiriKit, siga estos pasos para la aplicación de iOS:

1. Graduación **SiriKit** en **Entitlements.plist**.
2. Agregar el **privacidad: descripción de uso de Siri** clave a **Info.plist**, junto con un mensaje para los clientes.
3. Llame a la `INPreferences.RequestSiriAuthorization` método en la aplicación para pedir al usuario que permite las interacciones de Siri.
4. Agregue SiriKit Id. de la aplicación en el Portal para desarrolladores y volver a crear los perfiles de aprovisionamiento para incluir los nuevos derechos.

A continuación, agregue un nuevo proyecto de extensión a la aplicación para controlar las solicitudes de Siri:

1. Haga doble clic en la solución y elija **Agregar > Agregar nuevo proyecto...** .
2. Elija la **iOS > extensión > extensión Intents** plantilla.
3. Se agregarán dos nuevos proyectos: intención y IntentUI. Personalizar la interfaz de usuario es opcional, por lo que el ejemplo solo incluye el código en el **intención** proyecto.

El proyecto de extensión es donde se procesarán todas las solicitudes de SiriKit. Como una extensión independiente, no lo tiene automáticamente ninguna forma de comunicarse con la aplicación principal, esto generalmente se resuelve mediante la implementación de almacenamiento de archivos compartidos con grupos de aplicaciones.

#### <a name="configure-the-intenthandler"></a>Configurar la IntentHandler

El `IntentHandler` clase es el punto de entrada para las solicitudes de Siri: cada intención se pasa a la `GetHandler` método, que devuelve un objeto que puede atender la solicitud.

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

La clase debe heredar de `INExtension`, y dado que el ejemplo se va a listas de control y las intenciones de notas, también implementa `IINNotebookDomainHandling`.

> [!NOTE]
> - Hay una convención en .NET para las interfaces sean precedidos con una letra mayúscula `I`, lo que Xamarin se adhiere a cuando los protocolos de enlace desde el SDK de iOS.
> - Xamarin también conserva los nombres de tipo de iOS y Apple usa los dos primeros caracteres en nombres de tipo para reflejar el marco de trabajo que pertenece un tipo.
> - Para el `Intents` framework, los tipos tienen el prefijo `IN*` (p ej. `INExtension`), pero estos son _no_ interfaces.
> - También sigue ese protocolos (que se convierten en las interfaces de C#) terminará con dos `I`s, como `IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>Control de intenciones

Cada intención (Agregar tarea, establecer el atributo de tarea, etcetera) se implementa en un único método, similar a la que se muestra a continuación. El método debe realizar tres funciones principales:

1. **Procesar la intención** : los datos por parte de Siri está disponible en un `intent` específico del tipo de la intención del objeto. Puede que la aplicación haya validado esos datos mediante opcional `Resolve*` métodos.
2. **Validar y actualizar el almacén de datos** : guardar datos en el sistema de archivos (con grupos de aplicaciones para que también puede acceder a la aplicación principal de iOS) o a través de una solicitud de red.
3. **Proporcionar una respuesta** : Use el `completion` controlador para enviar una respuesta al Siri a lectura/Mostrar al usuario:

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

Tenga en cuenta que `null` se pasa como segundo parámetro a la respuesta: este es el parámetro de actividad de usuario y, si no se proporciona, se usará un valor predeterminado.
Puede establecer un tipo de actividad personalizada que admita la aplicación de iOS a través siempre y cuando la `NSUserActivityTypes` clave **Info.plist**. A continuación, puede controlar este caso, cuando se abre la aplicación y realizar operaciones concretas (por ejemplo, de apertura para un controlador de vista relevante y carga los datos de la operación de Siri).

El ejemplo también codifica el `Success` resultado, pero en escenarios reales, se deben agregar los informes de errores adecuado.

### <a name="test-phrases"></a>Frases de prueba

Las siguientes frases de prueba deberían funcionar en la aplicación de ejemplo:

- "Hacer una lista de víveres con Apple, banana y peras en TasksNotes"
- "Agregar tarea sesión WWDC en TasksNotes"
- "Agregar tarea sesión WWDC a la lista de cursos en TasksNotes"
- "Marcar Asista a sesión WWDC como completadas en TasksNotes"
- "En TasksNotes Recordármelo a comprar un iphone cuando llegue a casa"
- "¡Marca compre iPhone como completado en TasksNotes"
- "Recordarme deje de inicio a las 8 a.m. en TasksNotes"

![Crear un nuevo ejemplo de lista](sirikit-images/createtasklist-sml.png) ![Conjunto de tareas como ejemplo completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> El 11 de iOS Simulator admite pruebas con Siri (a diferencia de las versiones anteriores).
>
> Si las pruebas en dispositivos reales, no olvide configurar el identificador de la aplicación y perfiles de aprovisionamiento para SiriKit soporte técnico.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nombres alternativos

Esta nueva característica de iOS 11 significa que puede configurar nombres alternativos para la aplicación ayudar a los usuarios se activan correctamente con Siri. Agregue las claves siguientes a la **Info.plist** archivo del proyecto de aplicación de iOS:

![Info.plist que muestra los valores y claves de nombre alternativo de aplicación](sirikit-images/alternative-names.png)

Con el conjunto de nombres alternativos de aplicación, las frases siguientes también funciona para la aplicación de ejemplo (que realmente se denomina **TasksNotes**):

- "Hacer una lista de víveres con Apple, banana y peras en _MonkeyNotes_"
- "Agregar sesión WWDC en la tarea _MonkeyTodo_"


## <a name="troubleshooting"></a>Solución de problemas

Algunos errores que pueden surgir al ejecutar el ejemplo o agregar SiriKit a sus propias aplicaciones:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Excepción de C de objetivo.  Nombre: NSInternalInconsistencyException razón: uso de la clase < INPreferences: 0x60400082ff00 > desde una aplicación requiere el derecho com.apple.developer.siri. ¿Ha habilitado la capacidad de Siri en el proyecto de Xcode?_

- SiriKit está activada en **Entitlements.plist**.
- **Entitlements.plist** está configurado en el **opciones de proyecto > compilar > firma de lote de iOS**.

  [![Opciones de proyecto que muestra que los derechos se estableció correctamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (para la implementación de dispositivo) Id. de aplicación tiene SiriKit habilitado y descarga el perfil de aprovisionamiento.


## <a name="related-links"></a>Vínculos relacionados

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Ejemplo de SiriKit TasksNotes](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [Novedades en SiriKit (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/214/)
