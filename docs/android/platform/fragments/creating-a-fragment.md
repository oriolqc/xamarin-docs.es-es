---
title: Creación de un fragmento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 339de4930242e35c40b034af2ce6ba47fe1543af
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122470"
---
# <a name="creating-a-fragment"></a>Creación de un fragmento

Para crear un fragmento, una clase debe heredar de `Android.App.Fragment` y, a continuación, reemplace el `OnCreateView` método. `OnCreateView` se llamará a la actividad de hospedaje cuando se ponga el fragmento en la pantalla y se devolverá un `View`. Una típica `OnCreateView` creará `View` por lo que infla un archivo de diseño y, a continuación, conectarlo a un contenedor primario. Características del contenedor son importantes ya que Android aplicará los parámetros de diseño del elemento primario a la interfaz de usuario del fragmento. Esto se ilustra en el siguiente ejemplo:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

El código anterior infla la vista `Resource.Layout.Example_Fragment`y se agrega como una vista secundaria a la `ViewGroup` contenedor.


> [!NOTE]
> Subclases del fragmento no deben tener ningún constructor de argumento de un público predeterminado.

## <a name="adding-a-fragment-to-an-activity"></a>Agregar un fragmento a una actividad

Hay dos maneras de que un fragmento puede hospedarse dentro de una actividad:

-   **Mediante declaración** &ndash; fragmentos pueden utilizarse mediante declaración en `.axml` archivos de diseño mediante el uso de la `<Fragment>` etiqueta.

-   **Mediante programación** &ndash; fragmentos también se pueden crear instancias dinámicamente mediante el uso de la `FragmentManager` API de la clase.

Uso mediante programación a través de la `FragmentManager` clase se tratará más adelante en esta guía.

### <a name="using-a-fragment-declaratively"></a>Con un fragmento de forma declarativa

Agregar un fragmento dentro del diseño requiere el uso de la `<fragment>` etiqueta y, a continuación, que identifica el fragmento al proporcionar ya sea el `class` atributo o la `android:name` atributo. El fragmento de código siguiente muestra cómo usar el `class` atributos para declarar un `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Este fragmento de código siguiente muestra cómo declarar un `fragment` utilizando el `android:name` atributo para identificar la clase de fragmento:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Cuando se crea la actividad, Android se crear una instancia de cada fragmento especificado en el archivo de diseño e insertar la vista que se crea a partir `OnCreateView` en lugar de la `Fragment` elemento.
Los fragmentos que se agregan mediante declaración a una actividad son estáticos y permanecerán en la actividad hasta que se destruye; no es posible dinámicamente, reemplazar o quitar este tipo de fragmento durante la vigencia de la actividad a la que está conectado.

Debe tener asignado un identificador único a cada fragmento:

-  **Android: id** &ndash; igual que con otros elementos de interfaz de usuario en un archivo de diseño, esto es un identificador único.

-  **Android: etiqueta** &ndash; este atributo es una cadena única.

Si se usa ninguno de los dos métodos anteriores, el fragmento asumirá el identificador de la vista del contenedor. En el ejemplo siguiente donde ninguno `android:id` ni `android:tag` es siempre Android asignará el Id. de `fragment_container` al fragmento:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Caso de nombre de paquete

Android no permite caracteres en mayúsculas en los nombres de paquete; producirá una excepción al intentar aumentar la vista si un nombre de paquete contiene un carácter en mayúscula. Sin embargo, es más permisivo con Xamarin.Android y toleran la pérdida de caracteres en mayúsculas en el espacio de nombres.

Por ejemplo, ambos de los siguientes fragmentos de código que funcionará con Xamarin.Android. Sin embargo, el segundo fragmento de código provocará un `android.view.InflateException` es iniciada por una aplicación Android basada en Java pura.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

O

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo de vida de fragmento

Fragmentos tienen su propio ciclo de vida de alguna medida, independientes, pero todavía se verá afectado por, la [ciclo de vida de la actividad de hospedaje](~/android/app-fundamentals/activity-lifecycle/index.md).
Por ejemplo, cuando se detiene una actividad, se interrumpen todos sus fragmentos asociados. El siguiente diagrama muestra el ciclo de vida del fragmento.

[![Diagrama de flujo que ilustra el ciclo de vida de fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de creación de fragmento

La lista siguiente muestra el flujo de las varias devoluciones de llamada en el ciclo de vida de un fragmento tal como se está creando:

-   **`OnInflate()`** &ndash; Se llama cuando se crea el fragmento como parte de un diseño de vista. Esto se puede llamar inmediatamente después de crea mediante declaración el fragmento de un archivo de diseño XML. El fragmento no está asociado a su actividad, pero el **actividad**, **agrupación**, y **AttributeSet** desde la vista de jerarquía se pasan como parámetros. Este método está concebida para analizar el **AttributeSet** y para guardar los atributos que podría utilizarse más adelante en el fragmento.

-   **`OnAttach()`** &ndash; Se llama después de que el fragmento está asociado con la actividad. Este es el primer método que se ejecutará cuando el fragmento está listo para usarse. En general, fragmentos deben implementar un constructor o reemplace el constructor predeterminado. En este método se deben inicializar los componentes que son necesarios para el fragmento.

-   **`OnCreate()`** &ndash; Llamado por la actividad para crear el fragmento. Cuando se llama a este método, la jerarquía de vistas de la actividad de hospedaje puede no ser completamente instancias, por lo que el fragmento no debe confiar en cualquier parte de la jerarquía de vistas de la actividad hasta más adelante en el ciclo de vida del fragmento. Por ejemplo, no utilice este método para realizar ajustes en la interfaz de usuario de la aplicación ni modificaciones. Se trata de la primera hora a la que el fragmento puede comenzar a recopilar los datos que necesita. El fragmento se está ejecutando en el subproceso de interfaz de usuario en este momento, por lo que evitar cualquier procesamiento largo o procesar en un subproceso en segundo plano. Este método se puede omitir si **SetRetainInstance(true)** se llama.
    Esta alternativa se describirán más detalladamente a continuación.

-   **`OnCreateView()`** &ndash; Crea la vista para el fragmento.
    Este método se llama una vez la actividad **OnCreate()** método esté completado. En este momento, es seguro interactuar con la jerarquía de vistas de la actividad. Este método debe devolver la vista que se usará en el fragmento.

-   **`OnActivityCreated()`** &ndash; Se llama después de **Activity.OnCreate** se ha completado la actividad de hospedaje.
    Cambios finales a la interfaz de usuario deben realizarse en este momento.

-   **`OnStart()`** &ndash; Se llama después de que se ha reanudado la actividad contenedora. Esto hace que el fragmento sea visible al usuario. En muchos casos, el fragmento contendrá código que podría estar en el **OnStart()** método de una actividad.

-   **`OnResume()`** &ndash; Este es el último método se llama antes de que el usuario puede interactuar con el fragmento. Un ejemplo del tipo de código que debe realizarse en este método podría ser habilitar características de un dispositivo que el usuario puede interactuar con, por ejemplo, la cámara que los servicios de ubicación. Sin embargo, servicios como los siguientes pueden causar drain excesiva de la batería, y una aplicación debe minimizar su uso para conservar la duración de la batería.


### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destrucción de fragmento

La lista siguiente explica los métodos de ciclo de vida que se denominan tal como se está destruyendo un fragmento:

-   **`OnPause()`** &ndash; El usuario ya no es capaz de interactuar con el fragmento. Esta situación se produce porque otra operación fragmento está modificando este fragmento o la actividad de hospedaje está en pausa. Es posible que la actividad de este fragmento de hospedaje podría seguir estando visible, es decir, la actividad en el foco es parcialmente transparente o no ocupa la pantalla completa. Cuando este método se activa, es la primera indicación de que el usuario está terminando el fragmento. El fragmento debe guardar los cambios.

-   **`OnStop()`** &ndash; El fragmento ya no es visible. Se puede detener la actividad de host o una operación de fragmento está modificando en la actividad. Esta devolución de llamada tiene la misma finalidad que **Activity.OnStop**.

-   **`OnDestroyView()`** &ndash; Se llama a este método para limpiar los recursos asociados a la vista. Se llama cuando se ha destruido la vista asociada con el fragmento.

-   **`OnDestroy()`** &ndash; Este método se llama cuando el fragmento ya no está en uso. Sigue asociado con la actividad, pero ya no es funcional en el fragmento. Este método debe liberar todos los recursos que están en uso por el fragmento, como un [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) que podría utilizarse para una cámara. Este método se puede omitir si **SetRetainInstance(true)** se llama. Esta alternativa se describirán más detalladamente a continuación.

-   **`OnDetach()`** &ndash; Este método se llama justo antes de que el fragmento ya no está asociado con la actividad. Ya no existe la jerarquía de vistas del fragmento y se deben liberar todos los recursos utilizados por el fragmento de en este momento.


### <a name="using-setretaininstance"></a>Uso de SetRetainInstance

Es posible que un fragmento especificar que se debe no completamente destruirse si la actividad se vuelve a crear. El `Fragment` clase proporciona el método `SetRetainInstance` para este propósito. Si `true` se pasa a este método, a continuación, cuando se reinicia la actividad, se usará la misma instancia del fragmento. Si esto sucede, entonces todos los métodos de devolución de llamada se invocará, excepto el `OnCreate` y `OnDestroy` las devoluciones de llamada del ciclo de vida. Este proceso se ilustra en el diagrama de ciclo de vida que se muestra arriba (por las líneas de puntos verde).


## <a name="fragment-state-management"></a>Administración de Estados de fragmento

Fragmentos de mayo guardar y restaurar su estado durante el ciclo de vida del fragmento mediante una instancia de un `Bundle`. La agrupación permite que un fragmento guardar los datos como pares clave/valor y es útil para datos simples que no requieren la cantidad de memoria. Un fragmento puede guardar su estado con una llamada a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Cuando se crea una nueva instancia de un fragmento, el estado guardado en el `Bundle` estará disponible para la nueva instancia a través de la `OnCreate`, `OnCreateView`, y `OnActivityCreated` métodos de la nueva instancia.
El ejemplo siguiente muestra cómo recuperar el valor `current_choice` desde el `Bundle`:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

Reemplazar `OnSaveInstanceState` es un mecanismo adecuado para guardar los datos transitorios en un fragmento en todos los cambios de orientación, como el `current_choice` valor en el ejemplo anterior. Sin embargo, la implementación predeterminada de `OnSaveInstanceState` se encarga de guardar los datos transitorios en la interfaz de usuario para cada vista que tiene un identificador asignado. Por ejemplo, un vistazo a una aplicación que tenga un `EditText` elemento definido en XML como sigue:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Puesto que la `EditText` control tiene un `id` asignado, el fragmento guarda automáticamente los datos en el widget cuando `OnSaveInstanceState` se llama.


### <a name="bundle-limitations"></a>Limitaciones de agrupación

Aunque el uso de `OnSaveInstanceState` hace que resulte fácil guardar datos transitorios, uso de este método tiene algunas limitaciones:

-  Si el fragmento no se agrega a la pila de retroceso, entonces no se restaurará su estado cuando el usuario presiona el **volver** botón.

-  Cuando se usa la agrupación para guardar los datos, esos datos se serializan. Esto puede provocar retrasos en el procesamiento.


## <a name="contributing-to-the-menu"></a>En el menú de contribución

Fragmentos pueden contribuir con elementos al menú de su actividad de hospedaje.
Una actividad controla los elementos de menú en primer lugar. Si la actividad no tiene un controlador, a continuación, el evento se transferirán al fragmento, que, a continuación, la controlará.

Para agregar elementos al menú de la actividad, un fragmento debe hacer dos cosas.
En primer lugar, el fragmento debe implementar el método `OnCreateOptionsMenu` y colocar sus elementos en el menú, como se muestra en el código siguiente:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

El menú en el fragmento de código anterior se aumenta desde el siguiente código XML, ubicado en el archivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

A continuación, debe llamar en el fragmento de `SetHasOptionsMenu(true)`. La llamada a este método se anuncia a Android que el fragmento tiene elementos de menú para contribuir en el menú de opciones. A menos que se realiza la llamada a este método, los elementos de menú para el fragmento no se agregará al menú de la opción de la actividad. Esto se hace normalmente en el método de ciclo de vida `OnCreate()`, tal y como se muestra en el siguiente fragmento de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

La siguiente pantalla muestra el aspecto que tendría este menú:

[![Captura de pantalla de ejemplo de aplicación de Mis viajes mostrar elementos de menú](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
