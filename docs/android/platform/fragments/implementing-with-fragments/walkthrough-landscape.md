---
title: 'Guía detallada sobre los fragmentos: parte 2'
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 7ec8ad6ce428107d2255dd07c7e69c9e77780c09
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026317"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Tutorial de fragmentos &ndash; horizontal

El [fragmentos tutorial &ndash; parte 1](./walkthrough.md) se ha mostrado cómo crear y usar los fragmentos en una aplicación de Android que tenga como destino las pantallas más pequeñas en un teléfono. El siguiente paso en este tutorial es para modificar la aplicación para aprovechar el espacio horizontal adicional en Tablet PC &ndash; habrá una actividad que siempre será la lista de objetivos (el `TitlesFragment`) y `PlayQuoteFragment` se agregarán dinámicamente a la actividad en respuesta a una selección realizada por el usuario:

[![Aplicación que se ejecuta en Tablet PC](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Los teléfonos que se ejecutan en modo horizontal también se beneficiarán de esta mejora:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Actualización de la aplicación para controlar la orientación horizontal

Las siguientes modificaciones se basan en el trabajo realizado en el [fragmentos Walkthrough - teléfono](./walkthrough.md)

1. Crear un diseño alternativo para mostrar tanto el `TitlesFragment` y `PlayQuoteFragment`.
1. Actualización `TitlesFragment` para detectar si el dispositivo muestra ambos fragmentos simultáneamente y cambiar el comportamiento en consecuencia.
1. Actualización `PlayQuoteActivity` cerrar cuando el dispositivo está en modo horizontal.

## <a name="1-create-an-alternate-layout"></a>1. Crear un diseño alternativo

Cuando se crea la actividad principal en un dispositivo Android, Android decidirá qué diseño se carga según la orientación del dispositivo. De forma predeterminada, Android proporcionará la **Resources/layout/activity_main.axml** archivo de diseño. Para los dispositivos que se cargan en modo horizontal Android proporcionará la **Resources/layout-land/activity_main.axml** archivo de diseño. La guía sobre [recursos de Android](/xamarin/android/app-fundamentals/resources-in-android) contiene más detalles sobre cómo Android decide qué recurso de archivos para cargar para una aplicación.

Crear un diseño alternativo que tenga como destino **horizontal** orientación siguiendo los pasos descritos en la [diseños alternativos](/xamarin/android/user-interface/android-designer/alternative-layout-views) guía. Esto debe agregar un nuevo archivo de recursos de diseño para el proyecto, **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Diseño alternativo en el Explorador de soluciones](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Diseño alternativo en el panel de solución](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Después de crear el diseño alternativo, modifique el origen del archivo **Resources/layout-land/activity_main.axml** para que coincida con este código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

La vista de la raíz de la actividad tiene el identificador de recurso `two_fragments_layout` y tiene dos vistas secundarias, un `fragment` y un `FrameLayout`. Mientras el `fragment` es cargados estáticamente el `FrameLayout` actúa como un "marcador de posición" que se reemplazará en tiempo de ejecución por el `PlayQuoteFragment`. Cada vez que se selecciona un juego nuevo en el `TitlesFragment`, `playquote_container` se actualizará con una nueva instancia de la `PlayQuoteFragment`.

Cada una de las vistas secundarias ocupará todo el alto de su elemento primario. El ancho de cada vista secundaria se controla mediante el `android:layout_weight` y `android:layout_width` atributos. En este ejemplo, cada vista secundaria ocupará el 50% del ancho de proporcionar por el elemento primario. Consulte [documentos de Google en el LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) para obtener más información acerca de _peso diseño_.

## <a name="2-changes-to-titlesfragment"></a>2. Cambios realizados en TitlesFragment

Una vez creado el diseño alternativo, es necesario actualizar `TitlesFragment`. Cuando la aplicación muestra a continuación, los dos fragmentos en una actividad, `TitlesFragment` debe cargar el `PlayQuoteFragment` en la actividad primaria. En caso contrario, `TitlesFragment` debe iniciar el `PlayQuoteActivity` qué host la `PlayQuoteFragment`. Una marca booleana que le ayudarán a `TitlesFragment` determinar el comportamiento que debe usar. Esta marca se inicializarán en el `OnActivityCreated` método.

En primer lugar, agregue una variable de instancia en la parte superior de la `TitlesFragment` clase:

```csharp
bool showingTwoFragments;
```

A continuación, agregue el siguiente fragmento de código para `OnActivityCreated` para inicializar la variable: 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

Si el dispositivo se está ejecutando en modo horizontal, el `FrameLayout` con el identificador de recurso `playquote_container` estará visible en la pantalla, por lo que `showingTwoFragments` se inicializarán en `true`. Si el dispositivo se está ejecutando en modo vertical, a continuación, `playquote_container` no estará en la pantalla, por lo que `showingTwoFragments` será `false`.

El `ShowPlayQuote` método será necesario cambiar cómo se muestra una oferta &ndash; en un fragmento o iniciar una nueva actividad.  Actualización de la `ShowPlayQuote` método para cargar un fragmento cuando se muestran dos fragmentos, de lo contrario debe iniciar una actividad:

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Si el usuario ha seleccionado un juego en el que es diferente del que se muestra actualmente en `PlayQuoteFragment`, a continuación, un nuevo `PlayQuoteFragment` se crea y se reemplazará el contenido de la `playquote_container` dentro del contexto de un `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Código completo de TitlesFragment

Después de completar todos los cambios anteriores a `TitlesFragment`, la clase completa debe coincidir con este código:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }


        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. Cambios realizados en PlayQuoteActivity

Hay un detalle final que se encargue de: `PlayQuoteActivity` no es necesario cuando el dispositivo está en modo horizontal. Si el dispositivo está en modo horizontal el `PlayQuoteActivity` no debería estar visible. Actualización de la `OnCreate` método de `PlayQuoteActivity` para que se va a cerrar propio. Este código es la versión final de `PlayQuoteActivity.OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

Esta modificación agrega una comprobación para la orientación del dispositivo. Si está en modo horizontal, a continuación, `PlayQuoteActivity` cerrará a sí mismo.

## <a name="4-run-the-application"></a>4. Ejecutar la aplicación

Una vez que estos cambios están completos, ejecute la aplicación, girar el dispositivo para el modo (si es necesario) horizontal y, a continuación, seleccione un objetivo. La oferta debe mostrarse en la misma pantalla de la lista de objetivos:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Aplicación que se ejecuta en una tableta Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
