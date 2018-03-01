---
title: Crear un fragmento
ms.topic: article
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: c1dd3495b0d7f76197126094cfd10e50d0ca760d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-fragment"></a>Crear un fragmento

Para crear un fragmento, una clase debe heredar de `Android.App.Fragment` y, a continuación, reemplace el `OnCreateView` método. `OnCreateView` se llamará a la actividad de hospedaje cuando es el momento de colocar el fragmento en la pantalla y devolverá un `View`. Una típica `OnCreateView` creará `View` por lo que infla un archivo de diseño y, a continuación, conectarlo a un contenedor primario. Características del contenedor son importantes, como Android aplicará los parámetros de diseño del elemento primario a la interfaz de usuario del fragmento. Esto se ilustra en el siguiente ejemplo:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

El código anterior aumentar la vista `Resource.Layout.Example_Fragment`y agréguela como una vista secundaria para el `ViewGroup` contenedor.


> [!NOTE]
> **Nota:** subclases de fragmento no deben tener un valor predeterminado público ningún constructor de argumento.

## <a name="adding-a-fragment-to-an-activity"></a>Agregar un fragmento a una actividad

Hay dos maneras de que un fragmento puede hospedar dentro de una actividad:

-   **Mediante declaración** &ndash; fragmentos se pueden usar de forma declarativa en `.axml` archivos de diseño mediante el uso de la `<Fragment>` etiqueta.

-   **Mediante programación** &ndash; fragmentos también se pueden crear instancias dinámicamente mediante el uso de la `FragmentManager` API de la clase.

Uso mediante programación a través de la `FragmentManager` clase se explicará más adelante en esta guía.

### <a name="using-a-fragment-declaratively"></a>Utilizar mediante declaración un fragmento

Agregar un fragmento dentro del diseño, debe usar el `<fragment>` etiqueta y, a continuación, identifica el fragmento proporcionando cualquiera el `class` atributo o la `android:name` atributo. El fragmento de código siguiente muestra cómo utilizar el `class` attribute para declarar un `fragment`:

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

Cuando se crea la actividad, Android se crear una instancia de cada fragmento especificado en el archivo de diseño e inserte la vista que se crea a partir `OnCreateView` en lugar de la `Fragment` elemento.
Los fragmentos que se agregan mediante declaración a una actividad son estáticos y permanecerán en la actividad hasta que se destruye; no es posible reemplazar o quitar este tipo de fragmento durante la duración de la actividad a la que está adjuntada dinámicamente.

Cada fragmento se debe asignar un identificador único:

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

Android no permite caracteres en mayúsculas en los nombres de paquete; producirá una excepción al intentar aumentar la vista si un nombre de paquete contiene un carácter en mayúsculas. Sin embargo, Xamarin.Android es más permisivo con los errores y toleran caracteres en mayúsculas en el espacio de nombres.

Por ejemplo, ambos de los siguientes fragmentos de código funcionará con Xamarin.Android. Sin embargo, el segundo fragmento de código hará un `android.view.InflateException` que se produzca una aplicación Android puro basados en Java.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

O

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo de vida de fragmento

Fragmentos tienen su propio ciclo de vida que es algo independiente de, pero todavía afectados, el [ciclo de vida de la actividad de hospedaje](~/android/app-fundamentals/activity-lifecycle/index.md).
Por ejemplo, cuando una actividad se detiene, se pausan todos sus fragmentos asociados. El siguiente diagrama muestra el ciclo de vida del fragmento.

[![Diagrama de flujo que ilustra el ciclo de vida de fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png)


### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de creación de fragmento

La lista siguiente muestra el flujo de las varias devoluciones de llamada en el ciclo de vida de un fragmento tal y como se está creando:

-   **`OnInflate()`** &ndash; Se llama cuando se crea el fragmento como parte de un diseño de la vista. Esto se puede llamar inmediatamente después de que el fragmento se crea mediante declaración desde un archivo de diseño XML. El fragmento no está asociado a su actividad aún, pero la **actividad**, **agrupación**, y **AttributeSet** desde la vista de jerarquía se pasan como parámetros. Este método sirve para analizar el **AttributeSet** y para guardar los atributos que puede usarse más adelante en el fragmento.

-   **`OnAttach()`** &ndash; Se llama después de que el fragmento está asociado a la actividad. Este es el primer método que se ejecutará cuando el fragmento está listo para usarse. En general, fragmentos deben implementar un constructor o reemplazar el constructor predeterminado. Todos los componentes que son necesarios para el fragmento deben inicializarse en este método.

-   **`OnCreate()`** &ndash; Llama a la actividad que se va a crear el fragmento. Cuando se llama a este método, la jerarquía de vista de la actividad de hospedaje puede no ser completamente instancias, por lo que el fragmento no debe confiar en cualquier parte de la jerarquía de vista de la actividad hasta más adelante en el ciclo de vida del fragmento. Por ejemplo, no utilice este método para realizar los ajustes o hacer ajustes a la interfaz de usuario de la aplicación. Se trata de la primera hora a la que el fragmento puede empezar a recopilar los datos que necesita. El fragmento se ejecuta en el subproceso de interfaz de usuario en este momento, por lo que evite cualquier procesamiento largo o bien realizar ese procesamiento en un subproceso en segundo plano. Este método se puede omitir si **SetRetainInstance(true)** se llama.
    Esta alternativa se describirá con más detalle a continuación.

-   **`OnCreateView()`** &ndash; Crea la vista del fragmento.
    Este método se llama una vez la actividad **OnCreate()** método esté completado. En este momento, es seguro interactuar con la jerarquía de vista de la actividad. Este método debe devolver la vista que se usará en el fragmento.

-   **`OnActivityCreated()`** &ndash; Llamado después de **Activity.OnCreate** se ha completado la actividad de hospedaje.
    Ajustes finales a la interfaz de usuario deben realizarse en este momento.

-   **`OnStart()`** &ndash; Se llama después de que se ha reanudado la actividad que lo contiene. Esto hace que el fragmento visibles para el usuario. En muchos casos, el fragmento contiene código que estaría en el **OnStart()** método de una actividad.

-   **`OnResume()`** &ndash; Este es el último método que se llama antes de que el usuario puede interactuar con el fragmento. Un ejemplo del tipo de código que debe realizarse en este método se puede habilitar características de un dispositivo que el usuario puede interactuar con, por ejemplo, la cámara que los servicios de ubicación. Sin embargo, servicios como los siguientes pueden producir purga excesiva de la batería, y una aplicación debe minimizar su uso para conservar la duración de la batería.


### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destrucción de fragmento

La lista siguiente describe los métodos del ciclo de vida que se llaman, como se está destruyendo un fragmento:

-   **`OnPause()`** &ndash; El usuario ya no es capaz de interactuar con el fragmento. Esta situación se produce porque otra operación fragmento está modificando este fragmento o la actividad de hospedaje está en pausa. Es posible que la actividad que hospeda este fragmento podría seguir estando visible, es decir, la actividad en el foco es parcialmente transparente o no ocupa la pantalla completa. Cuando este método se activa, es la primera indicación de que el usuario está abandonando el fragmento. El fragmento debe guardar los cambios.

-   **`OnStop()`** &ndash; El fragmento ya no está visible. Quizás se detenga la actividad de host o una operación de fragmento está modificando en la actividad. Esta devolución de llamada tiene la misma finalidad como **Activity.OnStop**.

-   **`OnDestroyView()`** &ndash; Se llama a este método para limpiar los recursos asociados a la vista. Esto se llama cuando se destruye la vista asociada al fragmento.

-   **`OnDestroy()`** &ndash; Este método se llama cuando el fragmento ya no está en uso. Se sigue asociado a la actividad, pero el fragmento ya no es funcional. Este método debería liberar cualquier recurso que están en uso por el fragmento, como un [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) que podría utilizarse para una cámara. Este método se puede omitir si **SetRetainInstance(true)** se llama. Esta alternativa se describirá con más detalle a continuación.

-   **`OnDetach()`** &ndash; Se llama a este método justo antes de que el fragmento ya no está asociado a la actividad. La jerarquía de vista del fragmento ya no existe, y todos los recursos utilizados por el fragmento deben liberarse en este momento.


### <a name="using-setretaininstance"></a>Usar SetRetainInstance

Es posible que un fragmento especificar que se debería no completamente destruir si se vuelve a crear la actividad. El `Fragment` clase proporciona el método `SetRetainInstance` para este propósito. Si `true` se pasa a este método, cuando se reinicia la actividad, se utilizará la misma instancia del fragmento. Si esto ocurre, todos los métodos de devolución de llamada se invocará excepto la `OnCreate` y `OnDestroy` las devoluciones de llamada del ciclo de vida. Este proceso se muestra en el diagrama del ciclo de vida anterior (de las líneas de puntos verde).


## <a name="fragment-state-management"></a>Administración de Estados de fragmento

Fragmentos puede guardar y restaurar su estado durante el ciclo de vida del fragmento mediante una instancia de un `Bundle`. La agrupación permite un fragmento guardar los datos como pares clave/valor y es útil para los datos simples que no requieren la cantidad de memoria. Un fragmento puede guardar su estado con una llamada a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Cuando se crea una nueva instancia de un fragmento, el estado guardado en el `Bundle` estará disponible para la nueva instancia a través de la `OnCreate`, `OnCreateView`, y `OnActivityCreated` métodos de la nueva instancia.
El siguiente ejemplo muestra cómo recuperar el valor `current_choice` desde el `Bundle`:

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

Reemplazar `OnSaveInstanceState` es un mecanismo adecuado para guardar datos transitorios en un fragmento en todos los cambios de orientación, como la `current_choice` valor en el ejemplo anterior. Sin embargo, la implementación predeterminada de `OnSaveInstanceState` se encarga de guardar datos transitorios en la interfaz de usuario para todas las vistas que tiene un identificador asignado. Por ejemplo, un vistazo a una aplicación que tenga un `EditText` elemento definido en XML como se indica a continuación:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Puesto que la `EditText` control tiene un `id` asignado, el fragmento guarda automáticamente los datos en el widget cuando `OnSaveInstanceState` se llama.


### <a name="bundle-limitations"></a>Limitaciones de agrupación

Aunque el uso de `OnSaveInstanceState` hace fácil guardar datos transitorios, el uso de este método tiene algunas limitaciones:

-  Si el fragmento no se agrega a la pila de retroceso, su estado no se restaurará cuando el usuario presiona el **volver** botón.

-  Cuando el paquete se utiliza para guardar los datos, esos datos se serializan. Esto puede provocar retrasos de procesamiento.


## <a name="contributing-to-the-menu"></a>Que contribuyen al menú

Fragmentos pueden contribuir elementos al menú de su actividad de hospedaje.
Una actividad controla los elementos de menú en primer lugar. Si la actividad no tiene un controlador, a continuación, el evento se pasarán el fragmento, que, a continuación, la controlará.

Para agregar elementos al menú de la actividad, un fragmento debe hacer dos cosas.
En primer lugar, el fragmento debe implementar el método `OnCreateOptionsMenu` y colocar sus elementos en el menú, como se muestra en el código siguiente:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

El menú en el fragmento de código anterior se aumenta en el siguiente código XML, ubicado en el archivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

A continuación, el fragmento debe llamar a `SetHasOptionsMenu(true)`. La llamada a este método se anuncia a Android que el fragmento tiene elementos de menú para contribuir en el menú de opciones. A menos que se realiza la llamada a este método, los elementos de menú para el fragmento no se agregará al menú de opciones de la actividad. Esto se hace normalmente en el método de ciclo de vida `OnCreate()`, tal y como se muestra en el siguiente fragmento de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

La pantalla siguiente muestra el aspecto que tendría este menú:

[![Captura de pantalla de ejemplo de aplicación de Mis viajes y mostrar elementos de menú](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png)
