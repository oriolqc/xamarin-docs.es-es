---
title: Ciclo de vida de la actividad
description: Las actividades son un pilar fundamental de las aplicaciones de Android y pueden existir en un número de estados diferentes. El ciclo de vida de la actividad comienza con la creación de instancias y termina con la destrucción e incluye muchos Estados entre ellos. Cuando una actividad cambia de estado, se llama al método de evento adecuado del ciclo de vida, notificar a la actividad de la inminente cambio de estado y lo que le permite ejecutar el código para adaptarse a ese cambio. En este artículo examina el ciclo de vida de las actividades y se explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado para formar parte de una aplicación confiable y se comporten correctamente.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 3592a3027469cb9997d973db53d636ddea9e679d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110893"
---
# <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

_Las actividades son un pilar fundamental de las aplicaciones de Android y pueden existir en un número de estados diferentes. El ciclo de vida de la actividad comienza con la creación de instancias y termina con la destrucción e incluye muchos Estados entre ellos. Cuando una actividad cambia de estado, se llama al método de evento adecuado del ciclo de vida, notificar a la actividad de la inminente cambio de estado y lo que le permite ejecutar el código para adaptarse a ese cambio. En este artículo examina el ciclo de vida de las actividades y se explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado para formar parte de una aplicación confiable y se comporten correctamente._

## <a name="activity-lifecycle-overview"></a>Información general sobre el ciclo de vida de actividad

Las actividades son un concepto de programación inusual específico de Android. Desarrollo de aplicaciones tradicionales normalmente es un método main estático, que se ejecuta para iniciar la aplicación. Con Android, sin embargo, las cosas son diferentes; Las aplicaciones de Android se pueden iniciar a través de cualquier actividad dentro de una aplicación registrada. En la práctica, la mayoría de las aplicaciones solo tendrá una actividad específica que se especifica como el punto de entrada de la aplicación. Sin embargo, si se bloquea una aplicación, o se termina por el sistema operativo, el sistema operativo puede intentar reiniciar la aplicación en la última actividad abierta o cualquier otro lugar dentro de la pila de la actividad anterior.
Además, el sistema operativo puede pausar las actividades cuando no están activas y reclamar ellos si está quedando sin memoria. Una consideración cuidadosa debe realizarse para permitir que la aplicación restaurar correctamente su estado en caso de que una actividad se reinicia, especialmente si eso depende de la actividad en los datos de las actividades anteriores.

El ciclo de vida de actividad se implementa como una colección de llamadas de métodos, el sistema operativo durante el ciclo de vida de una actividad. Estos métodos permiten a los desarrolladores implementar la funcionalidad necesaria satisfacer los requisitos de administración de recursos y el estado de sus aplicaciones.

Es muy importante para el desarrollador de aplicaciones analizar los requisitos de cada actividad para determinar qué métodos expuestos por el ciclo de vida de actividad deben implementarse. Error al hacer esto puede provocar inestabilidad en la aplicación, se bloquea, sobredimensionamiento de recursos y posiblemente incluso subyacente inestabilidad del sistema operativo.

Este capítulo analizan el ciclo de vida de actividad en detalle, incluido:

-  Estados de actividad
-  Métodos de ciclo de vida
-  Conservar el estado de una aplicación


En esta sección también incluye un [tutorial](~/android/app-fundamentals/activity-lifecycle/saving-state.md) que proporcionan ejemplos prácticos sobre cómo guardar el estado de manera eficaz durante el ciclo de vida de actividad. Al final de este capítulo debe comprender el ciclo de vida de actividad y cómo se admiten en una aplicación Android.

## <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

El ciclo de vida de la actividad de Android consta de una colección de métodos expuestos dentro de la clase de actividad que proporcionan al desarrollador de un marco de administración de recursos. Este marco de trabajo permite a los desarrolladores cumplir los requisitos de administración de estado único de cada actividad dentro de una aplicación y controlar correctamente la administración de recursos.

### <a name="activity-states"></a>Estados de actividad

El sistema operativo Android arbitra las actividades según su estado. Esto ayuda a Android a identificar las actividades que ya no están en uso, lo que permite el sistema operativo reclamar memoria y recursos. El siguiente diagrama muestra los Estados que se puede pasar una actividad durante su vigencia:

[![Diagrama de Estados de actividad](images/image1-sml.png)](images/image1.png#lightbox)

Estos estados se pueden dividir en 4 grupos principales como sigue:

1.  *Activa o en ejecución* &ndash; actividades se considera activo o ejecutando si se encuentran en primer plano, también conocidas como la parte superior de la pila de la actividad. Esto se considera la actividad de mayor prioridad en Android y, por lo tanto sólo se terminará por el sistema operativo en situaciones extremas, tal como si la actividad intenta usar más memoria que está disponible en el dispositivo ya que esto podría causar la interfaz de usuario deje de responder.

1.  *En pausa* &ndash; cuando el dispositivo entra en suspensión, o una actividad es estando visible pero parcialmente oculto por una actividad nueva, que no son de tamaño completo o transparente, la actividad se considera en pausa. Las actividades en pausa son activos, es decir, mantener toda la información de estado y de miembro y permanecerá adjuntos para el Administrador de ventanas. Esto se considera que la segunda actividad de prioridad más alta en Android y, por tanto, solo se terminará por el sistema operativo si la eliminación de esta actividad cumplirá los requisitos de recursos necesarios para mantener la actividad de activo o en ejecución estable y con capacidad de respuesta.

1.  *Detenido/Backgrounded* &ndash; se consideran las actividades que están completamente ocultos, por otra actividad detenida o en segundo plano.
    Actividades detenidas siguen intentan conservar su información de estado y de miembro para siempre se consideran actividades posibles, pero se detuvo la prioridad más baja de los tres estados y, por lo tanto, el sistema operativo terminará las actividades en este estado en primer lugar para satisfacer el recurso requisitos de las actividades de mayor prioridad.

1.  *Reiniciar* &ndash; es posible que una actividad que está en cualquier parte desde pausó en detenido en el ciclo de vida que va a quitar de la memoria por Android. Si el usuario navega a la actividad debe reiniciarse, puede restaurar a su estado guardado previamente y, a continuación, se muestra al usuario.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Volver a crear en respuesta a cambios de configuración de actividad

Para complicar más complicado, Android produce una llave inglesa más en la combinación de los cambios de configuración. Los cambios de configuración son actividad rápido destrucción/volver-creation ciclos que se producen cuando cambia la configuración de una actividad, como cuando el dispositivo esté [girado](~/android/app-fundamentals/handling-rotation.md) (y la actividad debe volver a compilarse en horizontal o vertical modo), cuando se muestra el teclado (y la actividad se presenta una oportunidad de tamaño él mismo), o cuando el dispositivo se encuentre en una base, entre otros.

Cambios de configuración provocan sigue los mismos cambios de estado de actividad que se producirían durante detener y reiniciar una actividad. Sin embargo, para asegurarse de que una aplicación se siente con capacidad de respuesta y lleva a cabo correctamente durante los cambios de configuración, es importante que se administran tan pronto como sea posible. Por este motivo, Android tiene una API específica que puede usarse para conservar el estado durante los cambios de configuración.
Hablaremos sobre esto más adelante en el [administración de estado en todo el ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) sección.

### <a name="activity-lifecycle-methods"></a>Métodos de ciclo de vida de actividad

El SDK de Android y, por extensión, el marco de trabajo de Xamarin.Android proporciona un modelo eficaz para administrar el estado de actividades dentro de una aplicación. Cuando cambia el estado de la actividad, se notifica la actividad por el sistema operativo, que llama a métodos específicos de esa actividad. El siguiente diagrama ilustra estos métodos en relación con el ciclo de vida de actividad:

[![Diagrama de flujo de ciclo de vida de actividad](images/image2-sml.png)](images/image2.png#lightbox)

Como desarrollador, puede invalidar estos métodos dentro de una actividad para gestionar los cambios de estado. Es importante tener en cuenta, sin embargo, que todos los métodos de ciclo de vida se llaman en el subproceso de interfaz de usuario y bloquearán el sistema operativo realice el siguiente elemento de trabajo de la interfaz de usuario, como ocultar la actividad actual, mostrar una nueva actividad, etcetera. Por lo tanto, el código de estos métodos debe ser tan breve como sea posible para que una aplicación se sienta bien realizar. Las tareas de ejecución prolongada deben ejecutarse en un subproceso en segundo plano.

Examinemos cada uno de estos métodos de ciclo de vida y su uso:

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) es el primer método se llama cuando se crea una actividad.
`OnCreate` siempre se reemplaza para llevar a cabo las inicializaciones de inicio que pueden ser necesario para una actividad, como:

-  Creación de vistas
-  Inicialización de variables
-  Enlace de datos estáticos a las listas


`OnCreate` toma un [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/) parámetro, que es un diccionario para almacenar y pasar información de estado y los objetos entre actividades si el paquete no es null, esto indica la actividad se está reiniciando y debe restaurar su estado desde el instancia anterior. El código siguiente muestra cómo recuperar los valores de la agrupación:

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

Una vez `OnCreate` ha finalizado, llamará a Android `OnStart`.

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) siempre se llama por el sistema después de `OnCreate` haya finalizado. Actividades pueden invalidar este método si es necesario realizar ningún derecho de tareas específicas antes de una actividad esté visible como actualizar los valores actuales de las vistas dentro de la actividad. Android llamará `OnResume` inmediatamente después de este método.

#### <a name="onresume"></a>OnResume

Las llamadas al sistema [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) cuando la actividad está lista para empezar a interactuar con el usuario.
Las actividades deben invalidar este método para realizar tareas como:

-  Refuerza las velocidades de fotogramas (una tarea común de creación de juegos)
-  A partir de las animaciones
-  Escucha de las actualizaciones GPS
-  Mostrar alertas pertinentes y los cuadros de diálogo
-  Conectar controladores de eventos externos


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

`OnResume` es importante porque cualquier operación que realiza en `OnPause` debe deshacerse en `OnResume`, ya que es el único método de ciclo de vida que siempre se ejecutará después `OnPause` cuando lleva la actividad de vida a.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) se llama cuando el sistema está a punto de colocar la actividad en segundo plano o cuando la actividad parcialmente queda oculta. Las actividades deben invalidar este método si es necesario:

-   Confirmar los cambios no guardados en los datos persistentes

-   Destruir o limpiar los otros objetos que consumen recursos

-   Disminuir las velocidades de fotogramas y animaciones pausar

-   Anular el registro de controladores de eventos externos o controladores de notificación (es decir, aquellos que están vinculados a un servicio). Esto se debe hacer para evitar pérdidas de memoria de la actividad.

-   Del mismo modo, si la actividad ha mostrado cuadros de diálogos ni las alertas, deben limpiarse con el `.Dismiss()` método.

Por ejemplo, el siguiente fragmento de código liberará la cámara, como la actividad no se puede hacer uso de ella mientras está en pausa:

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

Hay dos métodos posibles del ciclo de vida que se llamará después `OnPause`:

1.  `OnResume` se llamará si la actividad que se va a devolver al primer plano.
1.  `OnStop` se llamará si se ha realizado la actividad en segundo plano.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) se llama cuando la actividad ya no es visible para el usuario. Esto sucede cuando se produce uno de los siguientes:

-  Una nueva actividad se está iniciando y abarca de esta actividad.
-  Una actividad existente es que se incorporó en primer plano.
-  La actividad se está destruyendo.


`OnStop` no siempre se puede llamar en situaciones de escasez de memoria, como cuando tiene escasez de recursos y no en segundo plano correctamente de la actividad de Android. Por este motivo, es mejor no dependen de `OnStop` obtener llama al preparar una actividad para la destrucción. Los métodos del ciclo de vida siguientes que se pueden llamar después de que ésta será `OnDestroy` si la actividad está en desuso, o `OnRestart` si la actividad se vuelve a interactuar con el usuario.

#### <a name="ondestroy"></a>onDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) es el método final que se llama en una instancia de actividad antes de que se destruye y se quita completamente de la memoria. En situaciones extremas Android podría terminar el proceso de aplicación que hospeda la actividad, lo que dará lugar `OnDestroy` no que se va a invocar. La mayoría de las actividades no implementa este método porque la mayoría limpia y apagado se ha realizado el `OnPause` y `OnStop` métodos. El `OnDestroy` normalmente se invalida el método para limpiar prolongada ejecuta los recursos que podría perder recursos. Un ejemplo de esto podría ser subprocesos en segundo plano que se iniciaron en `OnCreate`.

No habrá ningún método de ciclo de vida se llama después de la actividad se ha destruido.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) se llama después de la actividad se ha detenido, antes de que se inicie de nuevo. Un buen ejemplo de esto sería cuando el usuario presiona el botón de inicio mientras se encuentra en una actividad en la aplicación. Cuando esto sucede `OnPause` y, a continuación, `OnStop` se llama a métodos y la actividad se mueve en segundo plano, pero no se destruye. Si el usuario, a continuación, para restaurar la aplicación mediante el Administrador de tareas o una aplicación similar, llamará Android el `OnRestart` método de la actividad.

No hay ningún directrices generales para qué tipo de lógica debe implementarse en `OnRestart`. Esto es porque `OnStart` siempre se invoca independientemente de si la actividad se crea o se reinicia, por lo que todos los recursos requeridos por la actividad deben inicializarse en `OnStart`, en lugar de `OnRestart`.

El siguiente método de ciclo de vida se llama después de `OnRestart` será `OnStart`.

### <a name="back-vs-home"></a>Realizar una copia de vs. Página principal

Muchos dispositivos Android tienen dos botones distintos: un botón "Atrás" y un botón "Inicio". Un ejemplo de esto se aprecia en la siguiente captura de pantalla de Android 4.0.3:

[![Botones Atrás y principal](images/image4-sml.png)](images/image4.png#lightbox)

Hay una diferencia sutil entre los dos botones, aunque parece que tienen el mismo efecto de colocar una aplicación en segundo plano. Cuando un usuario hace clic en el botón Atrás, está indicando Android que han terminado con la actividad. Android destruirá la actividad. En cambio, cuando el usuario hace clic en el botón de inicio la actividad simplemente se coloca en segundo plano &ndash; Android no terminará la actividad.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Administrar el estado a lo largo del ciclo de vida

Cuando una actividad se detuvo o se destruya el sistema proporciona una oportunidad para guardar el estado de la actividad de rehidratación más adelante.
Este estado guardado se conoce como estado de la instancia. Android proporciona tres opciones para almacenar el estado de la instancia durante el ciclo de vida de actividad:

1. Almacenar valores primitivos en un `Dictionary` conocido como un [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que Android utilizará para guardar el estado.

1. Creación de una clase personalizada que va a contener valores complejos, como los mapas de bits. Android usará esta clase personalizada para guardar el estado.

1. Sortear el ciclo de vida de cambio de configuración y asumir la responsabilidad completa para mantener el estado de la actividad.


Esta guía trata las dos primeras opciones.



### <a name="bundle-state"></a>Estado de lote

La opción para guardar el estado de la instancia principal es usar un objeto de diccionario de pares clave-valor conocido como un [agrupación](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
Recuerde que cuando se crea una actividad que el `OnCreate` método se pasa una agrupación como un parámetro, este paquete puede utilizarse para restaurar el estado de la instancia. No se recomienda usar un lote de datos más complejos que no rápidamente o se serializan fácilmente para clave-valor pares (por ejemplo, mapas de bits); en su lugar, debe usarse para valores sencillos como cadenas.

Una actividad proporciona métodos para guardar y recuperar el estado de la instancia en el paquete:

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; se invoca mediante Android cuando se está destruyendo la actividad. Las actividades pueden implementar este método si necesitan conservar los elementos de estado de clave/valor.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; Esto se llama después de la `OnCreate` método finalice y proporciona otra oportunidad para una actividad restaurar su estado una vez completada la inicialización.

El siguiente diagrama ilustra cómo se usan estos métodos:

[![Diagrama de flujo de los Estados de agrupación](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) se denominará como la actividad se está deteniendo. Recibirá un parámetro de paquete que la actividad puede almacenar su estado en. Cuando un dispositivo sufre un cambio de configuración, puede usar una actividad el `Bundle` objeto que se pasa a conservar el estado de actividad mediante la invalidación `OnSaveInstanceState`. Por ejemplo, considere el siguiente código:

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

El código anterior incrementa un número entero denominado `c` al seleccionar un botón denominado `incrementCounter` se hizo clic, mostrar el resultado en un `TextView` denominado `output`. Cuando se produce un cambio de configuración: por ejemplo, cuando se gira el dispositivo: el código anterior se perderá el valor de `c` porque el `bundle` sería `null`, como se muestra en la ilustración siguiente:

[![Pantalla no indica el valor anterior](images/07-sml.png)](images/07.png#lightbox)

Para conservar el valor de `c` en este ejemplo, puede invalidar la actividad `OnSaveInstanceState`, guardar el valor en el paquete tal como se muestra a continuación:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Ahora cuando se gira el dispositivo a una nueva orientación, el entero se guarda en el paquete y se recupera con la línea:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> Es importante siempre llamada la implementación base de `OnSaveInstanceState` para que también se puede guardar el estado de la jerarquía de vistas.



##### <a name="view-state"></a>Estado de vista

Reemplazar `OnSaveInstanceState` es un mecanismo adecuado para guardar los datos transitorios en una actividad a través de los cambios de orientación, por ejemplo, el contador en el ejemplo anterior. Sin embargo, la implementación predeterminada de `OnSaveInstanceState` se encargará de guardar los datos transitorios en la interfaz de usuario para cada vista, siempre y cuando cada vista tiene un identificador asignado. Por ejemplo, supongamos que una aplicación tiene un `EditText` elemento definido en XML como sigue:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Puesto que la `EditText` control tiene un `id` asignado, cuando el usuario escribe algunos datos y gira el dispositivo, los datos se siguen mostrando, tal como se muestra a continuación:

[![Se conservan los datos en modo horizontal](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) se llamará después `OnStart`. Proporciona la oportunidad de restaurar cualquier estado que se guardó previamente a una agrupación durante anterior de una actividad `OnSaveInstanceState`. Este es el mismo paquete que se proporciona a `OnCreate`, sin embargo.

El código siguiente muestra cómo se puede restaurar el estado en `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Este método existe para proporcionar cierta flexibilidad en torno a cuando se debe restaurar el estado. A veces es más adecuado esperar hasta que se realizan todas las inicializaciones antes de restaurar el estado de la instancia. Además, una subclase de una actividad existente solo es posible que desee restaurar ciertos valores de estado de la instancia. En muchos casos, no es necesario reemplazar `OnRestoreInstanceState`, ya que la mayoría de las actividades puede restaurar el estado mediante el paquete proporcionado a `OnCreate`.

Para obtener un ejemplo de guardar el estado mediante un `Bundle`, consulte el [Walkthrough - estado de guardar la actividad](saving-state.md).


#### <a name="bundle-limitations"></a>Limitaciones de agrupación

Aunque `OnSaveInstanceState` resulta fácil guardar los datos transitorios, tiene algunas limitaciones:

-   No se llama en todos los casos. Por ejemplo, al presionar **inicio** o **Atrás** salir de una actividad no darán como resultado `OnSaveInstanceState` que se llama.

-   La agrupación se pasó `OnSaveInstanceState` no está diseñado para los objetos grandes, como las imágenes. En el caso de objetos grandes, guardar el objeto de [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) es preferible, tal como se describe a continuación.

-   Guardado mediante la agrupación de datos serializados, lo que puede provocar retrasos.

Estado de la agrupación es útil para datos simples que no usan la cantidad de memoria, mientras que *datos de instancia sin configuración* es útil para datos o datos que son costosas de recuperar más complejas, como las llamadas de servicio web o un complicado consulta de base de datos. Datos de la instancia de configuración no se guardan en un objeto según sea necesario. La siguiente sección se presentan `OnRetainNonConfigurationInstance` como una manera de conservar los tipos de datos más complejos a través de los cambios de configuración.


### <a name="persisting-complex-data"></a>Persistencia de datos complejos

Además de almacenar datos en el paquete, Android también admite el almacenamiento de datos invalidando [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) y devolver una instancia de un `Java.Lang.Object` que contiene los datos que se va a conservar. Hay dos principales ventajas de usar `OnRetainNonConfigurationInstance` para guardar el estado:

-   El objeto devuelto desde `OnRetainNonConfigurationInstance` funciona bien con los tipos de datos más grandes y complejas porque memoria conserva este objeto.

-   El `OnRetainNonConfigurationInstance` método es llamado bajo demanda y solo cuando sea necesario. Esto resulta más económico que el uso de una caché manual.

Uso de `OnRetainNonConfigurationInstance` es adecuado para escenarios donde resulta caro recuperar los datos varias veces, como llamadas al servicio web. Por ejemplo, considere el siguiente código que busca en Twitter:

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

[![Resultados que aparecen en pantalla](images/06-sml.png)](images/06.png#lightbox)

Cuando un cambio de configuración - por ejemplo, se produce cuando un dispositivo se gira - el código repite el proceso. Para volver a usar los resultados recuperados originalmente y no hacer que las llamadas de red redundantes, innecesaria, podemos usar `OnRetainNonconfigurationInstance` para guardar los resultados, como se muestra a continuación:

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

Ahora cuando se gira el dispositivo, se recuperan los resultados originales de la `LastNonConfiguartionInstance` propiedad. En este ejemplo, los resultados están compuestos de una `string[]` que contiene los tweets. Puesto que `OnRetainNonConfigurationInstance` requiere que un `Java.Lang.Object` van a devolver el `string[]` se encapsula en una clase que cree subclases `Java.Lang.Object`, tal y como se muestra a continuación:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Por ejemplo, si intenta usar un `TextView` como el objeto devuelto desde `OnRetainNonConfigurationInstance` producirán una pérdida de la actividad, como se muestra en el código siguiente:

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

En esta sección, hemos aprendido cómo conservar los datos de estado simple con el `Bundle`, y conservar los tipos de datos más complejos con `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Resumen

El ciclo de vida de la actividad de Android proporciona un marco eficaz para la administración de estado de las actividades dentro de una aplicación, pero puede ser difícil de comprender e implementar. Este capítulo presenta los diferentes Estados que puede pasar una actividad durante su ciclo de vida, así como los métodos del ciclo de vida que están asociados con esos Estados. A continuación, se proporciona orientación sobre qué tipo de lógica debe realizarse en cada uno de estos métodos.


## <a name="related-links"></a>Vínculos relacionados

- [Control de giro](~/android/app-fundamentals/handling-rotation.md)
- [Actividad de Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
