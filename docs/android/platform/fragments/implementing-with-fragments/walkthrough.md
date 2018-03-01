---
title: Tutorial
ms.topic: article
ms.prod: xamarin
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 7441fbe1fc686dc4fa5cb67cbfc5ae6353f32c93
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough"></a>Tutorial

En los pasos siguientes, se crea una aplicación básica con fragmentos. El primer paso es crear un nuevo Xamarin.Android para proyecto de Android.

## <a name="1-create-a-project"></a>1. Crear un proyecto

Crear un nuevo proyecto de Xamarin.Android denominado **FragmentSample**. El **mínimo Android** versión debe establecerse para Android 3.1 o posterior, como se muestra en la imagen siguiente:

[![Configuración de la versión mínima Android](walkthrough-images/00.png)](walkthrough-images/00.png)


## <a name="2-create-the-mainactivity"></a>2. Crear la actividad principal

A continuación, necesitamos crear el `MainActivity` clase. Se trata de la actividad de inicio de la aplicación. Esta actividad alojará fragmentos de uno o ambos, dependiendo del tamaño de pantalla. `MainActivity` se hace esto si carga el archivo de diseño que sea adecuado para el dispositivo.

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

> [!NOTE]
> `Note:` Las subclases de fragmento no deben tener un público predeterminado ningún constructor de argumento.

## <a name="3-create-the-layout-files"></a>3. Crear los archivos de diseño

Los dos tamaños de pantalla diferentes, requieren dos archivos de un diseño diferente. Así que vamos a crear una nueva carpeta, **recursos/diseño-Large**y crear un nuevo diseño denominado **activity_main.axml**. También podrá cambiar el archivo de diseño de forma predeterminada como **Resources/Layout/activity_main.axml**. Después de estos cambios, las carpetas de diseño deben ser similar a la captura de pantalla siguiente:

[![Captura de pantalla de carpetas de diseño en el IDE](walkthrough-images/01.png)](walkthrough-images/01.png)


Todos los dispositivos se cargarán y usar el archivo de diseño en **recursos/diseño**.
Es un diseño muy simple que muestra un `TitlesFragment`:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_width="fill_parent"
           android:layout_height="fill_parent" />
</LinearLayout>
```

Para dispositivos que tienen una pantalla de gran tamaño, Android cargará el archivo de diseño en **recursos/diseño-grandes**. El contenido del diseño para tabletas es como sigue:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_weight="1"
           android:layout_width="0px"
           android:layout_height="match_parent" />
 <FrameLayout android:id="@+id/details"
              android:layout_weight="1"
              android:layout_width="0px"
              android:layout_height="match_parent" />
</LinearLayout>
```

El archivo de diseño para las pantallas más grandes es ligeramente diferente. No sólo es el `TitlesFragment` muestra en este archivo de diseño, pero un `FrameLayout` se agrega junto al fragmento. En las pantallas más grandes, el `DetailsFragment` se agrega mediante programación a `MainActivity` cuando el usuario selecciona un objetivo. Más adelante le explicaremos con más detalle cómo hacerlo.

3.2 Android introdujo una nueva manera de especificar diseños de pantalla. Estos calificadores nueva especifican la cantidad de espacio que se debe al diseño, en lugar del tamaño de la pantalla. Si esta aplicación se va a ejecutar solo en Android 3.2 o posterior, se crearía un **recursos/diseño-sw600dp** carpeta (en lugar de la carpeta **recursos/diseño-Large**) para el archivo de diseño  **activity_main.axml**. Este archivo de recursos se cargarán todos los dispositivos que tienen un ancho de pantalla mínima de 600 independiente de la densidad de píxeles. Sin embargo, como esta aplicación se establece en destino Android 3.1 o posterior, usa el calificador de recursos anterior.

## <a name="4-create-the-titlesfragment"></a>4. Crear el TitlesFragment

`TitlesFragment` se muestran los títulos de los distintos objetivos, así que vamos a agregar un nuevo fragmento al proyecto denominado `TitlesFragment`:

[![Agregar un nuevo fragmento al proyecto TitlesFragment](walkthrough-images/02.png)](walkthrough-images/02.png)

Después de `TitlesFragment` se ha agregado, debemos cambiamos la clase para que herede de `Android.App.ListFragment`. `ListFragment` es un tipo de fragmento especializadas que incluye funcionalidad de la lista.
`TitlesFragment` También invalidarán `OnActivityCreated` (otro método de ciclo de vida de fragmento) y proporcione un `Adapter` que `ListFragment` va a usar para rellenar la lista:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
   base.OnActivityCreated(savedInstanceState);
   var adapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemChecked, Shakespeare.Titles);
   ListAdapter = adapter;
   if (savedInstanceState != null)
   {
       _currentPlayId = savedInstanceState.GetInt("current_play_id", 0);
   }
   var detailsFrame = Activity.FindViewById<View>(Resource.Id.details);
   _isDualPane = detailsFrame != null && detailsFrame.Visibility == ViewStates.Visible;
   if (_isDualPane)
   {
       ListView.ChoiceMode = (int) ChoiceMode.Single;
       ShowDetails(_currentPlayId);
   }
}
```

Como se mencionó anteriormente, nuestra aplicación tiene dos diseños para `MainActivity`. El código en `OnActivityCreated` detecta la presencia de la `FrameLayout` y determina qué archivo de diseño se ha cargado. Si el `FrameLayout` existe en el diseño de la `_isDualPane` marca se establece en `true`. El `_isDualPane` marca se utiliza en otra parte de la actividad, específicamente el `ShowDetails` método. El `ShowDetails` método se tratarán con más detalle a continuación.

`TitlesFragment` es una lista y debe responder a las selecciones del usuario en la lista. Para ello, `TitlesFragment` invalidará el método `OnListItemClick`. Dentro de `OnListItemClick`, un nuevo `DetailsFragment` se creará y se muestra en el `FrameLayout`, mediante programación. El código relevante dentro de `TitlesFragment` es:

```csharp
public override void OnListItemClick(ListView l, View v, int position, long id)
{
   ShowDetails(position);
}
private void ShowDetails(int playId)
{
   _currentPlayId = playId;
   if (_isDualPane)
   {
       // We can display everything in place with fragments.
       // Have the list highlight this item and show the data.
       ListView.SetItemChecked(playId, true);
       // Check what fragment is shown, replace if needed.
       var details = FragmentManager.FindFragmentById(Resource.Id.details) as DetailsFragment;
       if (details == null || details.ShownPlayId != playId)
       {
           // Make new fragment to show this selection.
           details = DetailsFragment.NewInstance(playId);
           // Execute a transaction, replacing any existing
           // fragment with this one inside the frame.
           var ft = FragmentManager.BeginTransaction();
           ft.Replace(Resource.Id.details, details);
           ft.SetTransition(FragmentTransaction.TransitFragmentFade);
           ft.Commit();
       }
   }
   else
   {
       // Otherwise we need to launch a new Activity to display
       // the dialog fragment with selected text.
       var intent = new Intent();
       intent.SetClass(Activity, typeof (DetailsActivity));
       intent.PutExtra("current_play_id", playId);
       StartActivity(intent);
   }
}
```

El código determina desde el dispositivo cómo dar formato y mostrar la oferta de la reproducción seleccionada. En el caso de tabletas, la `_isDualPane` marca se establecerá en `true`, por lo que se mostrará junto a la oferta el `TitlesFragment`. Si la reproducción seleccionada `id` ya no aparece, a continuación, un nuevo `DetailsFragment` se crea y, a continuación, se cargan en el `FrameLayout` en la actividad. Para otros dispositivos que no tienen una pantalla grande &ndash; teléfonos, por ejemplo &ndash; `isDualPane` se establecerá en `false` para un nuevo `DetailsActivity` se iniciará.

<a name="5. Create_the_DetailsActivity" />

## <a name="5-create-the-detailsactivity"></a>5. Crear el DetailsActivity

El `DetailsActivity` muestra el `DetailsFragment` para dispositivos más pequeños. Para ver esto, primero vamos a agregar una nueva actividad al proyecto denominado `DetailsActivity`. `DetailsActivity` es una actividad muy simple. Creará y, a continuación, hospedar un nuevo `DetailsFragment` para la reproducción `id` que envió:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("current_play_id", 0);

       var details = DetailsFragment.NewInstance(index); // DetailsFragment.NewInstance is a factory method to create a Details Fragment
       var fragmentTransaction = FragmentManager.BeginTransaction();
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

Observe que no se ha cargado ningún archivo de diseño para `DetailsActivity`. En su lugar, `DetailsFragment` se carga en la vista de la raíz de la actividad. Esta vista raíz tiene el identificador especial `Android.Resource.Id.Content`. Un nuevo `DetailFragment` se crea y, a continuación, se agrega a esta vista raíz dentro de un `FragmentTransaction` creado por la actividad `FragmentManager`.

<a name="6. Create_the_DetailsFragment" />

## <a name="6-create-the-detailsfragment"></a>6. Crear el DetailsFragment

Ahora, vamos a agregar otro fragmento a la aplicación denominada `DetailsFragment`. Este fragmento mostrará una oferta de la reproducción seleccionada. El código siguiente muestra la sección completa `DetailsFragment`:

```csharp
internal class DetailsFragment : Fragment
{
   public static DetailsFragment NewInstance(int playId)
   {
       var detailsFrag = new DetailsFragment {Arguments = new Bundle()};
       detailsFrag.Arguments.PutInt("current_play_id", playId);
       return detailsFrag;
   }
   public int ShownPlayId
   {
       get { return Arguments.GetInt("current_play_id", 0); }
   }
   public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
   {
       if (container == null)
       {
           // Currently in a layout without a container, so no reason to create our view.
           return null;
       }
       var scroller = new ScrollView(Activity);
       var text = new TextView(Activity);
       var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
       text.SetPadding(padding, padding, padding, padding);
       text.TextSize = 24;
       text.Text = Shakespeare.Dialogue[ShownPlayId];
       scroller.AddView(text);
       return scroller;
   }
}
```

En orden para `DetailsFragment` para que funcione correctamente, debe tener el índice de la reproducción en el que está seleccionado en el `TitlesFragment`. Hay muchas formas de proporcionar este valor a `DetailsFragment`; en este ejemplo, la reproducción `Id` se coloca en un paquete y que se almacena el paquete a la propiedad argumentos de una instancia de la `DetailsFragment`. La propiedad `ShownPlayId` se proporciona por comodidad &ndash; se usará en instancias de `DetailsFragment` para recuperar ese valor de la agrupación.

`OnCreateView` se llama cuando el fragmento se debe dibujar su interfaz de usuario y debe devolver un `Android.Views.View` objeto. En la mayoría de los casos, se trata de un `View` exagerar desde un archivo de diseño existente. En el caso del ejemplo anterior, el fragmento compilará mediante programación la vista que se usará para mostrar.

¡Enhorabuena! Ahora ha creado una aplicación que utiliza fragmentos para simplificar el desarrollo a través de factores de forma.

En el [próxima sección](supporting-pre-honeycomb.md), vamos a ampliar esta aplicación para que funcione en dispositivos Android preliminar 3.0.

