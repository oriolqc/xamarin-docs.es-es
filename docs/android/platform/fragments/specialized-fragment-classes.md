---
title: Clases especializadas fragmento
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: e7b4349ee2664a94ef6dff3c6a58d5f8f97682a1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="specialized-fragment-classes"></a>Clases especializadas fragmento

La API de fragmentos proporciona otras subclases que encapsulan algunas de las funciones más comunes que se encuentran en aplicaciones. Estas subclases son:

-   **ListFragment** &ndash; este fragmento se utiliza para mostrar una lista de elementos enlazados a un origen de datos como una matriz o un cursor.

-   **DialogFragment** &ndash; este fragmento se utiliza como un contenedor alrededor de un cuadro de diálogo. El fragmento mostrará el cuadro de diálogo sobre su actividad.

-   **PreferenceFragment** &ndash; este fragmento se utiliza para mostrar los objetos de preferencias como listas.


<a name="The_ListFragment" />

## <a name="the-listfragment"></a>El ListFragment

El `ListFragment` es muy similar en concepto y funcionalidad a la `ListActivity`; es un contenedor que hospeda un `ListView` en un fragmento. La imagen siguiente muestra un `ListFragment` ejecutando en un Tablet PC y un teléfono:

[![Capturas de pantalla de ListFragment en un Tablet PC y en un teléfono](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png)

<a name="Binding_Data_With_The_ListAdapter" />

### <a name="binding-data-with-the-listadapter"></a>Enlazar datos con el ListAdapter

El `ListFragment` clase ya proporciona un diseño predeterminado, por lo que no es necesario reemplazar `OnCreateView` para mostrar el contenido de la `ListFragment`. El `ListView` está enlazado a datos mediante el uso de un `ListAdapter` implementación. En el ejemplo siguiente se muestra cómo se podría hacer esto mediante el uso de una matriz de cadenas simple:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

Al establecer el `ListAdapter`, es importante que use la `ListFragment.ListAdapter` propiedad y no el `ListView.ListAdapter` propiedad. Usar `ListView.ListAdapter` hará que el código de inicialización importante va a omitir.

<a name="Responding_to_User_Selection" />


### <a name="responding-to-user-selection"></a>Responder a la selección del usuario

Para responder a las selecciones del usuario, una aplicación debe invalidar la `OnListItemClick` método. En el ejemplo siguiente se muestra una posibilidad de este tipo:

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

En el código anterior, cuando el usuario selecciona un elemento en el `ListFragment`, se muestra un fragmento nuevo en la actividad de hospedaje, que muestra más detalles sobre el elemento que se ha seleccionado.

<a name="DialogFragment" />


## <a name="dialogfragment"></a>DialogFragment

El *DialogFragment* es un fragmento que se usa para mostrar un objeto de cuadro de diálogo dentro de un fragmento que se va a flotar encima de la ventana de la actividad. Está diseñado para sustituir el cuadro de diálogo administrado API (a partir de 3.0 Android). Captura de pantalla siguiente muestra un ejemplo de un `DialogFragment`:

[![Captura de pantalla de DialogFragment mostrar Agregar cuadro de edición de vehículo nuevo](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png)

Un `DialogFragment` garantiza que el estado entre el fragmento y el cuadro de diálogo siguen siendo coherente. Todas las interacciones y control del objeto de cuadro de diálogo se deben aplicar a través de la `DialogFragment` API y no se pueden realizar con llamadas directas en el objeto de cuadro de diálogo. El `DialogFragment` API proporciona cada instancia con un `Show()` método que se usa para mostrar un fragmento. Hay dos maneras de deshacerse de un fragmento:

-  Llame a `DialogFragment.Dismiss()` en el `DialogFragment` instancia. 

-  Mostrar otra `DialogFragment`.

Para crear un `DialogFragment`, una clase hereda de `Android.App.DialogFragment,` y, a continuación, reemplaza uno de los dos métodos siguientes:

- **OnCreateView** &ndash; Esto crea y devuelve una vista.

- **OnCreateDialog** &ndash; este modo crea un cuadro de diálogo personalizado. Normalmente se utiliza para mostrar un *AlertDialog*. Al reemplazar este método, no es necesario reemplazar `OnCreateView` .


<a name="A_Simple_DialogFragment" />

### <a name="a-simple-dialogfragment"></a>Un Simple DialogFragment

Captura de pantalla siguiente muestra un sencillo `DialogFragment` que tiene un `TextView` y dos `Button`s:

[![DialogFragment de ejemplo con un control TextView y dos botones](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png)

El `TextView` mostrará el número de veces que el usuario hace clic en un botón en el `DialogFragment`, mientras que al hacer clic en el botón otras se cerrará el fragmento. El código de `DialogFragment` es:

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```

<a name="Displaying_a_Fragment" />

### <a name="displaying-a-fragment"></a>Mostrar un fragmento

Al igual que todos los fragmentos, un `DialogFragment` se muestra en el contexto de un `FragmentTransaction`.

El `Show()` método en un `DialogFragment` toma una `FragmentTransaction` y un `string` como entrada. El cuadro de diálogo se agregará a la actividad y el `FragmentTransaction` confirmada.

El código siguiente muestra una posible manera de una actividad puede usar el `Show()` método para mostrar un `DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

<a name="Dismissing_a_Fragment" />

### <a name="dismissing-a-fragment"></a>Descartando un fragmento

Al llamar a `Dismiss()` en una instancia de un `DialogFragment` hace que un fragmento que se va a quitar de la actividad y confirma la transacción.
Se llamará a los métodos de ciclo de vida estándar de fragmento que implica la destrucción de un fragmento.

<a name="Alert_Dialog" />

### <a name="alert-dialog"></a>Cuadro de diálogo Alerta

En lugar de reemplazar `OnCreateView`, `DialogFragment` en su lugar, puede reemplazar `OnCreateDialog`. Esto permite que una aplicación crear un [AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/) que está administrado por un fragmento. El código siguiente es un ejemplo que utiliza el `AlertDialog.Builder` para crear un `Dialog`:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```

 <a name="PreferenceFragment" />


## <a name="preferencefragment"></a>PreferenceFragment

Para ayudar a administrar las preferencias, proporciona la API de fragmentos de la `PreferenceFragment` subclase. El `PreferenceFragment` es similar a la [PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/
) &ndash; mostrará una jerarquía de las preferencias para el usuario en un fragmento. Cuando el usuario interactúa con las preferencias, se guardará automáticamente en [SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html).
En Android 3.0 o superior aplicaciones, use el `PreferenceFragment` para tratar con las preferencias de aplicaciones. En la siguiente imagen muestra un ejemplo de un `PreferenceFragment`:

[![En el ejemplo se PreferencesFragment con las preferencias de inicio, cuadro de diálogo y en línea](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png)

<a name="Create_A_Preference_Fragment_from_a_Resource" />

### <a name="create-a-preference-fragment-from-a-resource"></a>Crear un fragmento de preferencia de un recurso

La preferencia de fragmento se puede exagerar desde un archivo de recursos XML mediante el uso de la [PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/) método. Un lugar lógico para llamar a este método en el ciclo de vida del fragmento estaría en el `OnCreate` método.

El `PreferenceFragment` que se muestran anteriormente se creó mediante la carga de un recurso desde XML. El archivo de recursos es:

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

El código de la preferencia de fragmento es como sigue:

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```

 <a name="Querying_Activities_to_Create_a_Preference_Fragment" />


### <a name="querying-activities-to-create-a-preference-fragment"></a>Consultar las actividades para crear un fragmento de preferencia

Otra técnica para crear un `PreferenceFragment` implica consultar las actividades. Cada actividad puede usar el [metadatos\_clave\_preferencias](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/) atributo que señala a un archivo de recursos XML. En Xamarin.Android, para ello, etiquetar una actividad con el `MetaDataAttribute`y, a continuación, especifique el archivo de recursos que usar. El `PreferenceFragment` clase proporciona el método [AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) que se puede utilizar para consultar una actividad para encontrar este recurso XML y aumentar una jerarquía de preferencia para él.

Se proporciona un ejemplo de este proceso en el siguiente fragmento de código, que utiliza `AddPreferencesFromIntent` para crear un `PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android mirará en la clase `MyActivityWithPreference`. La clase debe ser complementada con el `MetaDataAttribute,` tal como se muestra en el fragmento de código siguiente:

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

El `MetaDataAttribute` declara un archivo de recursos XML que el `PreferenceFragment` va a usar para aumentar la jerarquía de preferencias. Si el `MetatDataAttribute` no se proporciona, a continuación, se producirá una excepción en tiempo de ejecución. Cuando se ejecuta este código, la `PreferenceFragment` aparece como se muestra en la captura de pantalla siguiente:

[![Captura de pantalla de aplicación de ejemplo con PreferenceFragment muestra](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)
