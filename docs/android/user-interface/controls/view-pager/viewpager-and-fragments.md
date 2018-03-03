---
title: ViewPager con fragmentos
description: "ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo izquierdo y derecho con el paso a través de páginas de datos. Esta guía explica cómo implementar una interfaz de usuario swipeable con ViewPager, utilizar fragmentos como las páginas de datos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 9b200bd335ea65bf46de00d2dc7382b7f838716b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="viewpager-with-fragments"></a>ViewPager con fragmentos

_ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo izquierdo y derecho con el paso a través de páginas de datos. Esta guía explica cómo implementar una interfaz de usuario swipeable con ViewPager, utilizar fragmentos como las páginas de datos._

<a name="overview" />
 
## <a name="overview"></a>Información general

`ViewPager` se utiliza a menudo junto con fragmentos para que resulte más fácil de administrar el ciclo de vida de cada página en la `ViewPager`. En este tutorial, `ViewPager` se utiliza para crear una llama a una aplicación **FlashCardPager** que presenta una serie de problemas de matemáticas en tarjetas flash. Cada tarjeta flash se implementa como un fragmento. El usuario lectores crédito izquierdo y derecho a través de las tarjetas de flash y puntea en un problema de matemáticas para revelar su respuesta. Esta aplicación crea una `Fragment` instancia para cada tarjeta flash e implementa un adaptador derivado de `FragmentPagerAdapter`. En [Viewpager y vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), la mayoría del trabajo se ha realizado en `MainActivity` métodos del ciclo de vida. En **FlashCardPager**, se realizará la mayoría del trabajo un `Fragment` en uno de sus métodos del ciclo de vida. 

Esta guía no abarca los conceptos básicos de fragmentos de &ndash; si no todavía está familiarizado con fragmentos de Xamarin.Android, consulte [fragmentos](~/android/platform/fragments/index.md) que le ayudarán a empezar a trabajar con fragmentos. 


<a name="start" />

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Crear un nuevo proyecto Android denominado **FlashCardPager**. A continuación, inicie el Administrador de paquetes de NuGet (para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: incluidos unos NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Buscar e instalar el **Xamarin.Android.Support.v4** paquete tal como se describe en [Viewpager y vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 


<a name="datasource" />

## <a name="add-an-example-data-source"></a>Agregar un origen de datos de ejemplo

En **FlashCardPager**, el origen de datos es una baraja de cartas de flash representado por la `FlashCardDeck` clase; estos datos de origen proporciona el `ViewPager` con el contenido del elemento. `FlashCardDeck` contiene una colección de problemas de matemáticas y respuestas listos para su uso. El `FlashCardDeck` constructor no requiere argumentos: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La colección de tarjetas de flash en `FlashCardDeck` está organizada de manera que un indizador que se puede tener acceso a cada tarjeta flash. Por ejemplo, la siguiente línea de código recupera el cuarto problema con la tarjeta de flash en la baraja: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Esta línea de código recupera la respuesta correspondiente para el problema anterior:

```csharp
string answer = flashCardDeck[3].Answer;
```

Dado que los detalles de implementación `FlashCardDeck` no son relevantes para familiarizarse con `ViewPager`, la `FlashCardDeck` código no aparece aquí.
El código fuente `FlashCardDeck` está disponible en [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Descargar este archivo de origen (o copie y pegue el código en un nuevo **FlashCardDeck.cs** archivo) y agréguelo al proyecto.


<a name="layout" />

## <a name="create-a-viewpager-layout"></a>Crear un diseño ViewPager

Abra **Resources/layout/Main.axml** y reemplazar su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Este código XML define un `ViewPager` que ocupa toda la pantalla. Tenga en cuenta que debe usar el nombre completo **android.support.v4.view.ViewPager** porque `ViewPager` se empaqueta en una biblioteca de compatibilidad. `ViewPager` solo está disponible desde el [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); no está disponible en el SDK de Android.


<a name="setup" />

## <a name="set-up-viewpager"></a>Configurar ViewPager

Editar **MainActivity.cs** y agregue el siguiente `using` instrucciones:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Cambiar el `MainActivity` declaración de clase para que se deriva de `AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` se deriva de`FragmentActivity` (en lugar de `Activity`) porque `FragmentActivity` sabe cómo administrar la compatibilidad con los fragmentos. Reemplace el método `OnCreate` con el código siguiente: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Este código hace lo siguiente:

1.  Establece la vista de la **Main.axml** recursos de diseño.

2.  Recupera una referencia a la `ViewPager` del diseño.

3.  Crea una instancia de un nuevo `FlashCardDeck` como origen de datos.

Al compilar y ejecutar este código, debería ver una presentación similar a la captura de pantalla siguiente: 

[![Aplicación de captura de pantalla de FlashCardPager con ViewPager vacía](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png)

En este momento, el `ViewPager` está vacío porque carezca de los fragmentos que se usan rellenar el `ViewPager`, y le falta un adaptador para la creación de estos fragmentos de datos en **FlashCardDeck**. 

En las siguientes secciones, una `FlashCardFragment` es crear para implementar la funcionalidad de cada tarjeta flash y un `FragmentPagerAdapter` se crea para conectar el `ViewPager` a los fragmentos creados a partir de datos en el `FlashCardDeck`. 


<a name="fragment" />

## <a name="create-the-fragment"></a>Crear el fragmento

Cada tarjeta flash se administrará con un fragmento de la interfaz de usuario llama a `FlashCardFragment`. `FlashCardFragment`de vista muestra la información incluida con una sola tarjeta de flash. Cada instancia de `FlashCardFragment` se hospedará en la `ViewPager`. 
`FlashCardFragment`de la vista se compondrá de un `TextView` que muestra el texto de problema de la tarjeta flash. Esta vista va a implementar un controlador de eventos que utiliza un `Toast` para mostrar la respuesta cuando el usuario puntea la pregunta de tarjeta flash. 


<a name="layout" />

### <a name="create-the-flashcardfragment-layout"></a>Crear el diseño de FlashCardFragment

Antes de `FlashCardFragment` puede ser implementado, debe definir su diseño. Este diseño es un diseño de contenedor de fragmento para un único fragmento. Agregar un nuevo diseño Android a **recursos/diseño** llama **flashcard_layout.axml**. Abra **Resources/layout/flashcard_layout.axml** y reemplazar su contenido con el código siguiente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

Este diseño define un fragmento sola tarjeta de flash; cada fragmento se compone de un `TextView` que muestra un problema de matemáticas con una fuente grande (100sp). Este texto se centra verticalmente y horizontalmente en la tarjeta flash. 


<a name="fcfclass" />

### <a name="create-the-initial-flashcardfragment-class"></a>Crear la clase FlashCardFragment inicial

Agregar un nuevo archivo denominado **FlashCardFragment.cs** y reemplazar su contenido con el código siguiente:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

Este código crea el código auxiliar del esenciales `Fragment` definición que se usará para mostrar una tarjeta flash. Tenga en cuenta que `FlashCardFragment` se deriva de la versión de la biblioteca de compatibilidad de `Fragment` definido en `Android.Support.V4.App.Fragment`. El constructor está vacío para que la `newInstance` se utiliza el método de generador para crear un nuevo `FlashCardFragment` en lugar de un constructor. 

El `OnCreateView` del ciclo de vida método crea y configura el `TextView`. Aumenta el diseño para el fragmento `TextView` y devuelve la inflación `TextView` al llamador. `LayoutInflater` y `ViewGroup` se pasan a `OnCreateView` para que puede aumentar el diseño. El `savedInstanceState` agrupación contiene datos que `OnCreateView` se utiliza para recrear el `TextView` desde un estado guardado. 

Vista del fragmento se aumenta explícitamente en la llamada a `inflater.Inflate`. El `container` argumento es el elemento primario de la vista y el `false` marca indica la inflater abstenerse de agregar la vista aumentada al elemento primario de la vista (se agregará al `ViewPager` del adaptador de la llamada `GetItem` método más adelante en este tutorial). 


<a name="state" />

### <a name="add-state-code-to-flashcardfragment"></a>Agregar código de estado a FlashCardFragment

Al igual que una actividad, un fragmento no tiene un `Bundle` que usa para guardar y recuperar su estado. En **FlashCardPager**, esta `Bundle` se utiliza para guardar la pregunta y respuesta texto de la tarjeta flash asociada. En **FlashCardFragment.cs**, agregue el siguiente `Bundle` claves en la parte superior de la `FlashCardFragment` definición de clase: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificar el `newInstance` método de generador, por lo que TI crea un `Bundle` de objetos y utiliza las claves anteriores para almacenar la pregunta pasada y responder a texto en el fragmento después de que se crea una instancia: 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

Modifique el método de ciclo de vida de fragmento `OnCreateView` para recuperar esta información de la agrupación en el pasado y cargar el texto de la pregunta en el `TextBox`: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

El `answer` variable no se utiliza aquí, pero se utilizará más adelante cuando el código del controlador de eventos se agrega a este archivo. 


<a name="adapter" />

## <a name="create-the-adapter"></a>Crear el adaptador

`ViewPager` se usa un objeto de controlador de adaptador que se encuentra entre la `ViewPager` y el origen de datos (vea la ilustración en la ViewPager [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter) artículo). Para obtener acceso a estos datos, `ViewPager` requiere que proporcione un adaptador personalizado derivado de `PagerAdapter`. Dado que este ejemplo utiliza fragmentos, utiliza un `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` se deriva de `PagerAdapter`. 
`FragmentPagerAdapter` representa cada página como un `Fragment` que se mantiene en el Administrador de fragmentos de forma persistente como el usuario puede volver a la página. Como los lectores de crédito del usuario a través de páginas de la `ViewPager`, `FragmentPagerAdapter` extrae la información del origen de datos y lo utiliza para crear `Fragment`para ver si el `ViewPager` para mostrar. 

Cuando se implementa un `FragmentPagerAdapter`, es necesario reemplazar los siguientes:

-   **Recuento de** &ndash; propiedad de solo lectura que devuelve el número de vistas (páginas) disponibles.

-   **GetItem** &ndash; devuelve el fragmento que se va a mostrar para la página especificada.

Agregar un nuevo archivo denominado **FlashCardDeckAdapter.cs** y reemplazar su contenido con el código siguiente:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

Este código crea el código auxiliar del esenciales `FragmentPagerAdapter` implementación. En las secciones siguientes, cada uno de estos métodos se reemplaza con el código de trabajo. El propósito del constructor es el Administrador de fragmentos de pasar el `FlashCardDeckAdapter`del constructor de clase base. 


<a name="ctor" />

### <a name="implement-the-adapter-constructor"></a>Implemente el Constructor del adaptador

Cuando crea una instancia de la aplicación la `FlashCardDeckAdapter`, proporciona una referencia al administrador de fragmentos y una instancia `FlashCardDeck`. Agregue la siguiente variable de miembro a la parte superior de la `FlashCardDeckAdapter` clase **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Agregue la siguiente línea de código para el `FlashCardDeckAdapter` constructor: 

```csharp
this.flashCardDeck = flashCards;
```

Esta línea de código almacena la `FlashCardDeck` instancia que la `FlashCardDeckAdapter` va a usar. 


<a name="count" />

### <a name="implement-count"></a>Recuento de implementar

El `Count` implementación es relativamente sencilla: devuelve el número de tarjetas flash de la baraja de flash. Reemplaza `Count` por el código siguiente: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


El `NumCards` propiedad de `FlashCardDeck` devuelve el número de tarjetas de flash (número de fragmentos) en el conjunto de datos. 


<a name="getitem" />

### <a name="implement-getitem"></a>Implementar GetItem

El `GetItem` método devuelve el fragmento asociado a la posición especificada. Cuando `GetItem` llaman a una posición en la baraja de flash, devuelve un `FlashCardFragment` configurar para que muestre el problema con la tarjeta flash en esa posición. Reemplace el método `GetItem` con el código siguiente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Este código hace lo siguiente:

1.  Busca la cadena de problemas de matemáticas en el `FlashCardDeck` baraja para la posición especificada. 

2.  Busca la cadena de respuesta en el `FlashCardDeck` baraja para la posición especificada. 

3.  Llamadas a la `FlashCardFragment` método generador `newInstance`, pasando las cadenas de problema y la respuesta de la tarjeta flash. 

4.  Crea y devuelve una nueva tarjeta de flash `Fragment` que contiene el texto de la pregunta y respuesta para esa posición. 

Cuando el `ViewPager` representa el `Fragment` en `position`, muestra la `TextBox` que contiene la cadena de problemas de matemáticas que residen en `position` en la baraja de flash. 


<a name="addadapter" />

## <a name="add-the-adapter-to-the-viewpager"></a>Agregar el adaptador a la ViewPager

Ahora que el `FlashCardDeckAdapter` está implementado, es el momento de agregarlo a la `ViewPager`. En **MainActivity.cs**, agregue la siguiente línea de código al final de la `OnCreate` método:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Este código crea instancias el `FlashCardDeckAdapter`, pasando el `SupportFragmentManager` en el primer argumento. (El `SupportFragmentManager` propiedad de FragmentActivity se utiliza para obtener una referencia a la `FragmentManager` &ndash; para obtener más información sobre la `FragmentManager`, consulte [administrar fragmentos](~/android/platform/fragments/managing-fragments.md).) 

La implementación básica ahora está completa &ndash; compilar y ejecutar la aplicación.
Debería ver la primera imagen de la baraja flash aparecen en la pantalla, tal como se muestra a la izquierda en la captura de pantalla siguiente. Deslice el dedo izquierdo para ver más cartas de flash, a continuación, deslice el dedo posibilidad de retroceder a través de la baraja de flash:

[![Capturas de pantalla de ejemplo de aplicación de FlashCardPager sin indicadores de buscapersonas](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png)


<a name="pagetabstrip" />

## <a name="add-a-pager-indicator"></a>Agregar un indicador de buscapersonas

Este mínimo `ViewPager` implementación muestra cada tarjeta flash en la baraja, pero no proporciona ninguna indicación de que el usuario está dentro de la baraja. El paso siguiente consiste en Agregar un `PagerTabStrip`. El `PagerTabStrip` informa al usuario en cuanto a qué problema número, se muestra y proporciona el contexto de navegación mostrando una sugerencia de las tarjetas de flash anteriores y siguiente. 

Abra **Resources/layout/Main.axml** y agregue un `PagerTabStrip` al diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

  <android.support.v4.view.PagerTabStrip
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:layout_gravity="top"
      android:paddingBottom="10dp"
      android:paddingTop="10dp"
      android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

Al compilar y ejecutar la aplicación, debería ver el vacío `PagerTabStrip` aparece en la parte superior de cada tarjeta flash: 

[![Vista de cerca de PagerTabStrip sin texto](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png)


<a name="title" />

### <a name="display-a-title"></a>Mostrar un título

Para agregar un título para cada pestaña de página, implemente el `GetPageTitleFormatted` método en el adaptador. `ViewPager` llamadas `GetPageTitleFormatted` (si se implementa) para obtener la cadena de título que describe la página en la posición especificada. Agregue el método siguiente a la `FlashCardDeckAdapter` clase **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Este código convierte la posición en la baraja flash en un número de problema. La cadena resultante se convierte en un Java `String` que se devuelve a la `ViewPager`. Cuando se ejecuta la aplicación con este nuevo método, cada página muestra el número de problema en el `PagerTabStrip`: 

[![Capturas de pantalla de FlashCardPager con el número de problema que aparece por encima de cada página](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png)

Puede deslizar rápidamente y hacia atrás para ver el número de problema de la baraja tarjeta flash que se muestra en la parte superior de cada tarjeta flash. 


<a name="userinput" />

## <a name="handle-user-input"></a>Controlar proporcionados por el usuario

**FlashCardPager** presenta una serie de las tarjetas basadas en el fragmento de flash en un `ViewPager`, pero todavía no tiene una forma de mostrar la respuesta para cada problema. En esta sección, se agrega un controlador de eventos para el `FlashCardFragment` para mostrar la respuesta cuando el usuario puntea en el texto de problema de la tarjeta flash. 

Abra **FlashCardFragment.cs** y agregue el código siguiente al final de la `OnCreateView` método justo antes de que la vista se devuelve al autor de llamada: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Esto `Click` controlador de eventos muestra la respuesta en una notificación del sistema que aparece cuando el usuario puntea el `TextBox`. El `answer` variable se ha inicializado anteriormente cuando lee información de estado de la agrupación que se pasó a `OnCreateView`. Compilar y ejecutar la aplicación, a continuación, puntee en el texto del problema en cada tarjeta flash para ver la respuesta: 

[![Aplicación de capturas de pantalla de FlashCardPager notificaciones del sistema al que se derivan problemas de matemáticas](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png)

El **FlashCardPager** en este tutorial se presentan usa un `MainActivity` deriva `FragmentActivity`, pero también puede derivar `MainActivity` de `AppCompatActivity` (que también proporciona compatibilidad para la administración de fragmentos). Para ver una `AppCompatActivity` ejemplo, vea [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) en la Galería de ejemplos. 


<a name="summary" />

## <a name="summary"></a>Resumen

Este tutorial proporciona un ejemplo paso a paso de cómo compilar un basic `ViewPager`-según la aplicación con `Fragment`s. Presenta un origen de datos de ejemplo que contiene la tarjeta flash preguntas y respuestas, un `ViewPager` diseño para mostrar las tarjetas flash y un `FragmentPagerAdapter` subclase que se conecta el `ViewPager` al origen de datos. Para ayudar al usuario navegar por las tarjetas de flash, se incluyen instrucciones que explican cómo agregar un `PagerTabStrip` para mostrar el número de problema en la parte superior de cada página. Por último, el código de control de eventos se agregan para mostrar la respuesta cuando el usuario puntea en un problema con la tarjeta de flash. 



## <a name="related-links"></a>Vínculos relacionados

- [FlashCardPager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
