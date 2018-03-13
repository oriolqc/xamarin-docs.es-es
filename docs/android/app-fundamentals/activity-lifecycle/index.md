---
title: Ciclo de vida de la actividad
description: "Las actividades son un bloque de creación fundamental de las aplicaciones de Android y pueden existir en un número de estados diferentes. El ciclo de vida de actividad comienza con la creación de instancias y termina con la destrucción e incluye muchos Estados en medio. Cuando una actividad cambia de estado, se llama al método de evento adecuado del ciclo de vida, le notifica la actividad de cambio de estado inminente y lo que le permite ejecutar el código para adaptarse a ese cambio. Este artículo examina el ciclo de vida de las actividades y explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado como parte de una aplicación con comportamiento correcto y confiable."
ms.topic: article
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 335e63ce5a36cbd0172744a35c82920853b82e5c
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

_Las actividades son un bloque de creación fundamental de las aplicaciones de Android y pueden existir en un número de estados diferentes. El ciclo de vida de actividad comienza con la creación de instancias y termina con la destrucción e incluye muchos Estados en medio. Cuando una actividad cambia de estado, se llama al método de evento adecuado del ciclo de vida, le notifica la actividad de cambio de estado inminente y lo que le permite ejecutar el código para adaptarse a ese cambio. Este artículo examina el ciclo de vida de las actividades y explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado como parte de una aplicación con comportamiento correcto y confiable._

## <a name="activity-lifecycle-overview"></a>Información general sobre el ciclo de vida de actividad

Las actividades son un concepto de programación inusual específico para Android. En el desarrollo de aplicaciones tradicionales normalmente es un método estático main, que se ejecuta para iniciar la aplicación. Con Android, sin embargo, cosas son diferentes; Las aplicaciones de Android pueden iniciarse a través de cualquier actividad registrado dentro de una aplicación. En la práctica, la mayoría de las aplicaciones solo tendrá una actividad específica que se especifica como el punto de entrada de la aplicación. Sin embargo, si se bloquea una aplicación, o se termina por el sistema operativo, el sistema operativo puede intentar reiniciar la aplicación en la última actividad abierta o cualquier otro lugar dentro de la pila de la actividad anterior.
Además, el sistema operativo puede pausar actividades cuando no están activas y recuperar ellos si se está quedando sin memoria. Una consideración cuidadosa debe realizarse para permitir que la aplicación restaurar correctamente su estado en caso de que una actividad se reinicia, especialmente si eso depende de la actividad en los datos de actividades anteriores.

El ciclo de vida de actividad se implementa como una colección de llamadas de métodos el sistema operativo durante el ciclo de vida de una actividad. Estos métodos permiten a los desarrolladores implementar la funcionalidad necesaria satisfacer los requisitos de administración de recursos y el estado de sus aplicaciones.

Es muy importante para el desarrollador de la aplicación analizar los requisitos de cada actividad para determinar qué métodos expuestos por el ciclo de vida de actividad deben implementarse. Error al hacer esto puede producir inestabilidad en la aplicación, se bloquea, inundación de recursos y posiblemente incluso subyacente inestabilidad del sistema operativo.

En este capítulo examina el ciclo de vida de actividad en detalle, incluido:

-  Estados de actividad
-  Métodos de ciclo de vida
-  Conservar el estado de una aplicación


En esta sección también incluye una [tutorial](~/android/app-fundamentals/activity-lifecycle/saving-state.md) que proporcionan ejemplos prácticos sobre cómo guardar el estado de forma eficaz durante el ciclo de vida de actividad. Al final de este capítulo debe tener una comprensión del ciclo de vida de actividad y cómo admitir en una aplicación de Android.

## <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

El ciclo de vida de actividad Android consta de una colección de métodos expuestos en la clase de actividad que se proporcionan al desarrollador de un marco de trabajo de administración de recursos. Este marco de trabajo permite a los desarrolladores cumplir los requisitos de administración de estado único de cada actividad dentro de una aplicación y controlar correctamente la administración de recursos.

### <a name="activity-states"></a>Estados de actividad

El sistema operativo Android arbitra las actividades según su estado. Esto ayuda a Android a identificar las actividades que ya no están en uso, lo que permite el sistema operativo reclamar memoria y recursos. El siguiente diagrama muestra los Estados de que una actividad puede atravesar durante su duración:

[![Diagrama de Estados de actividades](images/image1-sml.png)](images/image1.png#lightbox)

Estos estados se pueden dividir en 4 grupos principales como sigue:

1.  *Activa o se está ejecutando* &ndash; actividades se considera activo o ejecutando si están en primer plano, también conocidas como la parte superior de la pila de actividad. Esto se considera la actividad de mayor prioridad en Android así como por ejemplo, solo se eliminará por el sistema operativo en situaciones extremas, tal como si la actividad intenta usar más memoria que está disponible en el dispositivo ya que esto podría provocar la interfaz de usuario deje de responder.

1.  *En pausa* &ndash; cuando el dispositivo pasa al modo de suspensión o si una actividad está siendo visible pero parcialmente oculto por una actividad nuevo, no son de tamaño completo o transparente, la actividad se considera en pausa. En pausa actividades son mantiene la conexión, es decir, mantener toda la información de estado y miembro y permanecen conectados en el Administrador de ventanas. Esto se considera que la segunda actividad de prioridad más alta en Android y, por tanto, solo se eliminará por el sistema operativo si la terminación de esta actividad cumplirá los requisitos de recursos necesarios para mantener la actividad de activo o en ejecución estable y capacidad de respuesta.

1.  *Detenido/Backgrounded* &ndash; las actividades que se ocultan completamente por otra actividad se consideran detenido o en segundo plano.
    Actividades detenidas siguen intentando conservar su información de estado y miembro mientras se consideran actividades posibles, pero se ha detenido la prioridad más baja de los tres estados y, por lo tanto, el sistema operativo elimine las actividades en este estado primero para satisfacer el recurso requisitos de actividades de mayor prioridad.

1.  *Reiniciar* &ndash; es posible para una actividad que está en alguna parte de pausado a detenido en el ciclo de vida va a quitar de la memoria por Android. Si el usuario se desplaza hacia la actividad debe reiniciarse, restaura a su estado guardado previamente y, a continuación, se muestra al usuario.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Recreación de actividad en respuesta a los cambios de configuración

Para hacer que la situación más complicado, Android produce una llave inglesa más en la combinación de los cambios de configuración. Cambios de configuración son actividad rápido destrucción/volver-creation ciclos que se producen cuando cambia la configuración de una actividad, por ejemplo, cuando el dispositivo esté [girado](~/android/app-fundamentals/handling-rotation.md) (y la actividad debe volver a compilarse en horizontal o vertical modo), cuando se muestra el teclado (y la actividad se presenta con una oportunidad a su tamaño), o cuando el dispositivo se coloca en un área, entre otros.

Cambios de configuración hacen que se siga los mismos cambios de estado de la actividad que se producirían durante detener y reiniciar una actividad. Sin embargo, para asegurarse de que una aplicación se siente con capacidad de respuesta y realiza también durante los cambios de configuración, es importante que estos se controlen tan pronto como sea posible. Por este motivo, el sistema Android tiene una API específica que puede usarse para conservar el estado durante los cambios de configuración.
Hablaremos sobre esto más adelante en el [administrar estado a lo largo del ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) sección.

### <a name="activity-lifecycle-methods"></a>Métodos de ciclo de vida de actividad

El SDK de Android y, por extensión, el marco de trabajo de Xamarin.Android proporcionan un modelo eficaz para administrar el estado de las actividades dentro de una aplicación. Cuando cambia el estado de una actividad, se notifica la actividad por el sistema operativo, que llama a métodos específicos de esa actividad. El diagrama siguiente muestra estos métodos en relación con el ciclo de vida de actividad:

[![Diagrama de flujo de actividad del ciclo de vida](images/image2-sml.png)](images/image2.png#lightbox)

Como desarrollador, puede controlar los cambios de estado mediante invalidar estos métodos dentro de una actividad. Es importante tener en cuenta, sin embargo, que todos los métodos del ciclo de vida se llaman en el subproceso de interfaz de usuario y bloquearán el sistema operativo realice el siguiente elemento de trabajo de la interfaz de usuario, como ocultar la actividad actual, mostrar una nueva actividad, etcetera. Por lo tanto, el código de estos métodos debe ser tan breve como sea posible para hacer que una aplicación se sienta buen rendimiento. Las tareas de larga duración se deberían ejecutar en un subproceso en segundo plano.

Examinemos cada uno de estos métodos de ciclo de vida y su uso:

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) es el primer método se llama cuando se crea una actividad.
`OnCreate` siempre se invalida para llevar a cabo las inicializaciones de inicio que pueden ser necesario para una actividad, como:

-  Creación de vistas
-  Inicializar variables
-  Enlazar datos estáticos a listas


`OnCreate` toma una [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/) parámetro, que es un diccionario para almacenar y pasar información de estado y objetos entre actividades si el paquete no es null, esto indica la actividad se está reiniciando y debe restaurar su estado desde el instancia anterior. El código siguiente muestra cómo recuperar los valores de la agrupación:

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

Una vez `OnCreate` ha finalizado, llamará Android `OnStart`.

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) siempre es llamado por el sistema después de `OnCreate` haya finalizado. Las actividades pueden reemplazar este método si es necesario realizar ningún derecho de determinadas tareas antes de que una actividad sea visible como actualizar los valores actuales de las vistas dentro de la actividad. Android llamará `OnResume` inmediatamente después de este método.

#### <a name="onresume"></a>OnResume

Las llamadas del sistema [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) cuando la actividad está lista para empezar a interactuar con el usuario.
Las actividades deben invalidar este método para realizar tareas como:

-  Refuerza velocidades de fotogramas (una tarea común en juego edificio)
-  A partir de las animaciones
-  Realizar escuchas para las actualizaciones GPS
-  Mostrar las alertas pertinentes o los cuadros de diálogo
-  Conecte los controladores de eventos externos


Por ejemplo, el fragmento de código siguiente muestra cómo inicializar la cámara:

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume` es importante porque cualquier operación que realiza en `OnPause` deben deshacerse en `OnResume`, ya que es el único método de ciclo de vida que se garantiza que se ejecutarán después `OnPause` al poner la actividad vida a.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) se llama cuando el sistema se va a colocar la actividad en segundo plano o cuando la actividad parcialmente queda oculta. Las actividades deben invalidar este método si necesitan:

-   Confirmar los cambios no guardados en datos persistentes

-   Destruir o limpiar de otros objetos que consumen recursos

-   Rampa descendente velocidades de fotogramas y animaciones pausar

-   Anular el registro de controladores de eventos externos o controladores de notificación (es decir, aquellas que están vinculadas a un servicio). Esto se debe hacer para evitar pérdidas de memoria de la actividad.

-   Del mismo modo, si la actividad muestra cuadros de diálogos ni las alertas, se deben limpiar con el `.Dismiss()` método.

Por ejemplo, el siguiente fragmento de código suministrará la cámara, cuando no se puede realizar la actividad utilizarlo mientras está en pausa:

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

Existen dos métodos de ciclo de vida posibles que se invocará después de `OnPause`:

1.  `OnResume` se llamará si la actividad es que se devuelvan al primer plano.
1.  `OnStop` se llamará si se va a dibujar la actividad en segundo plano.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) se llama cuando la actividad ya no es visible para el usuario. Esto sucede cuando se produce una de las siguientes acciones:

-  Una nueva actividad se está iniciando y está cubriendo esta actividad.
-  Una actividad existente se está poniendo en primer plano.
-  La actividad se está destruyendo.


`OnStop` no siempre se puede llamar en situaciones de escasez de memoria, como cuando necesite de recursos y no se correctamente en segundo plano la actividad Android. Por esta razón, es mejor no se basan en `OnStop` esté llamando al preparar una actividad a la destrucción. Los métodos del ciclo de vida siguientes que se pueden llamar después de que éste será `OnDestroy` si la actividad se ha desechado, o `OnRestart` si la actividad devuelve para interactuar con el usuario.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) es el método final que se llama en una instancia de actividad antes de se destruye y los quita por completo de la memoria. En situaciones extremas Android puede detener el proceso de aplicación que hospeda la actividad, lo que dará lugar `OnDestroy` no que se va a invocar. Mayoría de las actividades no implemente este método porque la mayoría limpia y apagado se ha realizado la `OnPause` y `OnStop` métodos. El `OnDestroy` normalmente se invalida el método para limpiar de larga ejecución recursos que podría perder recursos. Un ejemplo de esto podría ser subprocesos en segundo plano que se iniciaron en `OnCreate`.

No habrá ningún método de ciclo de vida se llama después de la actividad se ha destruido.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) se llama después de la actividad se ha detenido, antes de que se inicie de nuevo. Un buen ejemplo de esto sería cuando el usuario presiona el botón Inicio, mientras que en una actividad en la aplicación. Cuando esto sucede `OnPause` y, a continuación, `OnStop` se denominan métodos, y se mueve al fondo de la actividad pero no se destruye. Si el usuario, a continuación, para restaurar la aplicación mediante el Administrador de tareas o una aplicación similar, Android llamará el `OnRestart` método de la actividad.

No existen directrices generales para qué tipo de lógica debe implementarse en `OnRestart`. Esto es porque `OnStart` siempre se invoca independientemente de si se está creando la actividad o se reinicia, por lo que todos los recursos requeridos por la actividad deben inicializarse en `OnStart`, en lugar de `OnRestart`.

El siguiente método de ciclo de vida que se llama después de `OnRestart` será `OnStart`.

### <a name="back-vs-home"></a>Realice una copia de vs. Página principal

Muchos dispositivos Android tienen dos botones distintos: un botón "Atrás" y un botón "Inicio". Un ejemplo de esto puede verse en la siguiente captura de pantalla de Android 4.0.3:

[![Botones Atrás y principal](images/image4-sml.png)](images/image4.png#lightbox)

Hay una ligera diferencia entre los dos botones, aunque parece que tienen el mismo efecto de colocar una aplicación en segundo plano. Cuando un usuario hace clic en el botón Atrás, está indicando Android que han terminado con la actividad. Android destruirá la actividad. En cambio, cuando el usuario hace clic en el botón Inicio la actividad simplemente se coloca en segundo plano &ndash; Android no elimina la actividad.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Administrar el estado a lo largo del ciclo de vida

Cuando una actividad se detuvo o se destruya el sistema proporciona una oportunidad de guardar el estado de la actividad para rehidratación más adelante.
Este estado guardado se conoce como estado de la instancia. Android proporciona tres opciones para almacenar el estado de instancia durante el ciclo de vida de actividad:

1. Almacenar valores primitivos en una `Dictionary` conocido como un [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que Android a usar para guardar el estado.

1. Crear una clase personalizada que contendrán valores complejos como mapas de bits. Android usará esta clase personalizada para guardar el estado.

1. Sortear el ciclo de vida de cambio de configuración y que asume la responsabilidad completa para mantener el estado de la actividad.


Esta guía incluye las dos primeras opciones.



### <a name="bundle-state"></a>Estado de agrupación

La opción para guardar el estado de la instancia principal consiste en usar un objeto de diccionario clave-valor conocido como un [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
Recuerde que cuando se crea una actividad que la `OnCreate` método se pasa un paquete como un parámetro, este paquete puede utilizarse para restaurar el estado de la instancia. No se recomienda usar un lote de datos más complejos que no rápidamente o serializar fácilmente para clave-valor pares (por ejemplo, mapas de bits); en su lugar, debe utilizarse para valores simples como cadenas.

Una actividad proporciona métodos para ayudar a guardar y recuperar el estado de la instancia en el paquete:

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; se invoca Android cuando no se está destruyendo la actividad. Las actividades pueden implementar este método si necesita conservar los elementos de estado de clave/valor.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; Esto se llama después de la `OnCreate` método haya finalizado y proporciona otra oportunidad de una actividad restaurar su estado una vez completada la inicialización.

El siguiente diagrama muestra cómo se utilizan estos métodos:

[![Diagrama de flujo de Estados de agrupación](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) se llamará cuando se está deteniendo la actividad. Recibirá un parámetro de paquete que la actividad puede almacenar su estado en. Cuando un dispositivo produce un cambio de configuración, puede usar una actividad el `Bundle` objeto que se pasa a conservar el estado de actividad invalidando `OnSaveInstanceState`. Por ejemplo, considere el siguiente código:

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

El código anterior incrementa un número entero denominado `c` cuando un botón denominado `incrementCounter` se hace clic, mostrar el resultado en un `TextView` denominado `output`. Cuando se produce un cambio de configuración: por ejemplo, cuando se gira el dispositivo: si el código anterior pierde el valor de `c` porque el `bundle` sería `null`, tal y como se muestra en la ilustración siguiente:

[![Pantalla no indica el valor anterior](images/07-sml.png)](images/07.png#lightbox)

Para conservar el valor de `c` en este ejemplo, puede reemplazar la actividad `OnSaveInstanceState`, guardar el valor en el paquete tal y como se muestra a continuación:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Ahora cuando se gira el dispositivo a una orientación de nuevo, el entero se guarda en la agrupación y se recupera con la línea:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> Es importante siempre llamada a la implementación base de `OnSaveInstanceState` para que también se puede guardar el estado de la jerarquía de vista.



##### <a name="view-state"></a>Estado de vista

Reemplazar `OnSaveInstanceState` es un mecanismo adecuado para guardar datos transitorios en una actividad en todos los cambios de orientación, por ejemplo, el contador en el ejemplo anterior. Sin embargo, la implementación predeterminada de `OnSaveInstanceState` se encargará de guardar datos transitorios en la interfaz de usuario para cada vista, siempre y cuando cada vista tiene un identificador asignado. Por ejemplo, suponga que una aplicación ha un `EditText` elemento definido en XML como se indica a continuación:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Puesto que la `EditText` control tiene un `id` asignados, cuando el usuario escribe algunos datos y gira el dispositivo, los datos se siguen mostrando, tal y como se muestra a continuación:

[![Se conservan los datos en modo horizontal](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) llamará después `OnStart`. Proporciona la oportunidad de restaurar cualquier estado previamente guardado en un paquete durante la anterior una actividad `OnSaveInstanceState`. Se trata de la misma agrupación que se proporciona a `OnCreate`, sin embargo.

El código siguiente muestra cómo se puede restaurar estado de `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Este método no existe para proporcionar cierta flexibilidad alrededor cuando se debe restaurar el estado. A veces resulta más adecuado esperar hasta que se realizan todas las inicializaciones antes de restaurar el estado de la instancia. Además, podría desear sólo una subclase de una actividad existente restaurar ciertos valores de estado de la instancia. En muchos casos, no es necesario reemplazar `OnRestoreInstanceState`, ya que la mayoría de las actividades puede restaurar el estado mediante el paquete proporcionado para `OnCreate`.

Para obtener un ejemplo del proceso de guardar el estado utilizando un `Bundle`, consulte el [tutorial - estado de guardar la actividad](saving-state.md).


#### <a name="bundle-limitations"></a>Limitaciones de agrupación

Aunque `OnSaveInstanceState` resulta fácil de guardar datos transitorios, tiene algunas limitaciones:

-   No se llama en todos los casos. Por ejemplo, al presionar **inicio** o **Atrás** salir de una actividad no se producirá en `OnSaveInstanceState` que se va a llamar.

-   La agrupación se pasa a `OnSaveInstanceState` no está diseñado para los objetos grandes, como las imágenes. En el caso de objetos grandes, guardar el objeto de [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) es preferible, tal y como se describe a continuación.

-   Se serializan datos guardados mediante el uso de la agrupación, lo que puede provocar retrasos.

Estado de agrupación es útil para los datos simples que no utilizan la cantidad de memoria, mientras que *datos de la instancia de configuración no* es útil para los datos más complejas o datos que son costosas de recuperar, por ejemplo, una llamada de servicio web o un complicados consulta de base de datos. Datos de la instancia de configuración no se guardan en un objeto según sea necesario. La siguiente sección presenta `OnRetainNonConfigurationInstance` como una manera de mantener los tipos de datos más complejos a través de los cambios de configuración.


### <a name="persisting-complex-data"></a>Conservar datos complejos

Además de almacenar datos en el paquete, Android también permiten guardar datos invalidando [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) y devolver una instancia de un `Java.Lang.Object` que contiene los datos que se va a conservar. Hay dos ventajas principales del uso de `OnRetainNonConfigurationInstance` para guardar el estado:

-   El objeto devuelto desde `OnRetainNonConfigurationInstance` funciona bien con tipos de datos más grandes y complejos porque memoria conserva este objeto.

-   El `OnRetainNonConfigurationInstance` método se llama a petición y sólo cuando sea necesario. Esto resulta más económico que el uso de una caché manual.

Usar `OnRetainNonConfigurationInstance` es adecuado para escenarios donde resulta económico recuperar los datos varias veces, como en llamadas al servicio web. Por ejemplo, considere el siguiente código que busca en Twitter:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

Este código recupera los resultados de la web con formato JSON, analiza y, a continuación, muestra los resultados en una lista, como se muestra en la captura de pantalla siguiente:

[![Resultados que se muestran en pantalla](images/06-sml.png)](images/06.png#lightbox)

Cuando ocurre un cambio de configuración: por ejemplo, cuando un dispositivo se gira - el código repite el proceso. Para volver a usar los resultados recuperados originalmente y no hacer llamadas de red innecesaria y redundante, podemos usar `OnRetainNonconfigurationInstance` para guardar los resultados, tal y como se muestra a continuación:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

Ahora cuando se gira el dispositivo, se recuperan los resultados originales de la `LastNonConfiguartionInstance` propiedad. En este ejemplo, los resultados están compuestos de una `string[]` que contiene tweets. Puesto que `OnRetainNonConfigurationInstance` requiere que un `Java.Lang.Object` devolverá el `string[]` se encapsula en una clase que cree subclases `Java.Lang.Object`, tal y como se muestra a continuación:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Por ejemplo, al intentar utilizar un `TextView` como el objeto devuelto desde `OnRetainNonConfigurationInstance` producirá la pérdida de la actividad, como se muestra en el código siguiente:

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

En esta sección, hemos visto cómo conservar los datos de estado simple con el `Bundle`, y conservar los tipos de datos más complejos con `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Resumen

El ciclo de vida de actividad Android proporciona un marco eficaz para la administración de estado de las actividades dentro de una aplicación, pero puede ser difícil de comprender e implementar. En este capítulo se introdujo los diferentes Estados que puede recorrer una actividad durante su duración, así como los métodos del ciclo de vida que están asociados a esos Estados. A continuación, se proporciona orientación sobre qué tipo de lógica debe realizarse en cada uno de estos métodos.


## <a name="related-links"></a>Vínculos relacionados

- [Control de giro](~/android/app-fundamentals/handling-rotation.md)
- [Activity de Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
