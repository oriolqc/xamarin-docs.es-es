---
title: ViewPager con fragmentos
description: ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo hacia izquierda y derecha para ir avanzando por las páginas de datos. Esta guía explica cómo implementar una interfaz de usuario swipeable con ViewPager, usa fragmentos como las páginas de datos.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1b6e1c8ce91eaad46e779527c5ba12e2187cad24
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528434"
---
# <a name="viewpager-with-fragments"></a>ViewPager con fragmentos

_ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo hacia izquierda y derecha para ir avanzando por las páginas de datos. Esta guía explica cómo implementar una interfaz de usuario swipeable con ViewPager, usa fragmentos como las páginas de datos._

 
## <a name="overview"></a>Información general

`ViewPager` a menudo se usa junto con fragmentos para que resulte más fácil de administrar el ciclo de vida de cada página en el `ViewPager`. En este tutorial, `ViewPager` se utiliza para crear una aplicación llamada **FlashCardPager** que presenta una serie de problemas matemáticos en tarjetas flash. Cada tarjeta flash se implementa como un fragmento. El usuario pase el dedo left y right a través de las tarjetas flash y pulsa en un problema de matemáticas para revelar su respuesta. Esta aplicación crea un `Fragment` instancia para cada tarjeta flash e implementa un adaptador se deriva `FragmentPagerAdapter`. En [Viewpager y vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), la mayoría del trabajo se ha realizado en `MainActivity` métodos del ciclo de vida. En **FlashCardPager**, se realizarán la mayoría del trabajo mediante un `Fragment` en uno de sus métodos de ciclo de vida. 

Esta guía no trata los conceptos básicos de los fragmentos &ndash; si no todavía está familiarizado con los fragmentos en Xamarin.Android, consulte [fragmentos](~/android/platform/fragments/index.md) que le ayudarán a empezar a trabajar con fragmentos. 



## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android denominado **FlashCardPager**. A continuación, inicie el Administrador de paquetes de NuGet (para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: incluidos unos NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Buscar e instalar el **Xamarin.Android.Support.v4** del paquete como se explica en [Viewpager y vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Agregar un origen de datos de ejemplo

En **FlashCardPager**, el origen de datos es una baraja de cartas de flash representado por la `FlashCardDeck` clase; estos datos de origen proporciona el `ViewPager` con el contenido del elemento. `FlashCardDeck` contiene una colección de listas para usar de problemas de matemáticas y respuestas. El `FlashCardDeck` constructor no requiere ningún argumento: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La colección de tarjetas flash en `FlashCardDeck` se organiza de modo que cada tarjeta flash pueden tener acceso a un indizador. Por ejemplo, la siguiente línea de código recupera el cuarto problema tarjeta flash en la baraja: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Esta línea de código recupera la respuesta correspondiente al problema anterior:

```csharp
string answer = flashCardDeck[3].Answer;
```

Dado que los detalles de implementación `FlashCardDeck` no son relevantes para familiarizarse con `ViewPager`, el `FlashCardDeck` código no aparece aquí.
El código fuente para `FlashCardDeck` está disponible en [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Descargue este archivo de origen (o copie y pegue el código en un nuevo **FlashCardDeck.cs** archivo) y agréguelo al proyecto.



## <a name="create-a-viewpager-layout"></a>Crear un diseño ViewPager

Abra **Resources/layout/Main.axml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Este XML define un `ViewPager` que ocupa toda la pantalla. Tenga en cuenta que debe usar el nombre completo **android.support.v4.view.ViewPager** porque `ViewPager` se empaqueta en una biblioteca de soporte técnico. `ViewPager` solo está disponible desde el [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); no está disponible en el SDK de Android.


## <a name="set-up-viewpager"></a>Configurar ViewPager

Editar **MainActivity.cs** y agregue la siguiente `using` instrucciones:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Cambiar el `MainActivity` declaración de clase para que se deriva de `AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` se deriva de`FragmentActivity` (en lugar de `Activity`) porque `FragmentActivity` sabe cómo administrar la compatibilidad de los fragmentos. Reemplace el método `OnCreate` con el código siguiente: 

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

Al compilar y ejecutar este código, debería ver una pantalla similar a la captura de pantalla siguiente: 

[![Aplicación de captura de pantalla de FlashCardPager con ViewPager vacía](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

En este momento, el `ViewPager` está vacío porque falta los fragmentos que se usan rellenar el `ViewPager`, y que carezca de un adaptador para la creación de estos fragmentos de los datos en **FlashCardDeck**. 

En las secciones siguientes, un `FlashCardFragment` es crear para implementar la funcionalidad de cada tarjeta flash y un `FragmentPagerAdapter` se crea para conectar el `ViewPager` a los fragmentos creados a partir de datos en el `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Crear el fragmento

Cada tarjeta flash se administrarán mediante un fragmento de la interfaz de usuario denominado `FlashCardFragment`. `FlashCardFragment`de la vista mostrará la información incluida con una sola tarjeta de flash. Cada instancia de `FlashCardFragment` se hospedará en la `ViewPager`. 
`FlashCardFragment`de vista consistirá en un `TextView` que muestra el texto del problema de tarjetas flash. Esta vista implementará un controlador de eventos que usa un `Toast` para mostrar la respuesta cuando el usuario pulsa la pregunta de tarjetas flash. 



### <a name="create-the-flashcardfragment-layout"></a>Crear el diseño FlashCardFragment

Antes de `FlashCardFragment` puede ser implementado, debe definir su diseño. Este diseño es un diseño de contenedor de fragmento para un único fragmento. Agregar un nuevo diseño de Android a **y diseño de los recursos** llamado **flashcard_layout.axml**. Abra **Resources/layout/flashcard_layout.axml** y reemplace su contenido con el código siguiente: 

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

Este diseño define un fragmento de la tarjeta de flash único; cada fragmento se compone de un `TextView` que muestra un problema de matemáticas con una fuente grande (100sp). Este texto está centrado verticalmente y horizontalmente en la tarjeta flash. 



### <a name="create-the-initial-flashcardfragment-class"></a>Crear la clase FlashCardFragment inicial

Agregue un nuevo archivo denominado **FlashCardFragment.cs** y reemplace su contenido con el código siguiente:

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

Este código crea el código auxiliar del esenciales `Fragment` definición que se usará para mostrar una tarjeta de flash. Tenga en cuenta que `FlashCardFragment` se deriva de la versión de la biblioteca de soporte técnico de `Fragment` definido en `Android.Support.V4.App.Fragment`. El constructor está vacío para que la `newInstance` se usa el método de generador para crear un nuevo `FlashCardFragment` en lugar de un constructor. 

El `OnCreateView` método del ciclo de vida crea y configura el `TextView`. Aumenta el diseño para el fragmento `TextView` y devuelve la inflación `TextView` al llamador. `LayoutInflater` y `ViewGroup` se pasan a `OnCreateView` para que puede aumentar el diseño. El `savedInstanceState` paquete contiene datos que `OnCreateView` usa para volver a crear el `TextView` desde un estado guardado. 

Vista del fragmento se inflan explícitamente por la llamada a `inflater.Inflate`. El `container` argumento es el elemento primario de la vista y el `false` marca indica la inflater abstenerse de agregar la vista aumentada al elemento primario de la vista (se agregarán cuando `ViewPager` del adaptador de la llamada `GetItem` método más adelante en este tutorial). 



### <a name="add-state-code-to-flashcardfragment"></a>Agregar código de estado a FlashCardFragment

Al igual que una actividad, un fragmento no tiene un `Bundle` que usa para guardar y recuperar su estado. En **FlashCardPager**, este `Bundle` se usa para guardar la pregunta y respuesta texto de la tarjeta de flash asociada. En **FlashCardFragment.cs**, agregue la siguiente `Bundle` claves a la parte superior de la `FlashCardFragment` definición de clase: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificar el `newInstance` método de fábrica, por lo que TI crea un `Bundle` de objetos y usa las claves anteriores para almacenar la pregunta pasa y responder a texto en el fragmento de una vez que se crea una instancia: 

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

Modifique el método de ciclo de vida de fragmento `OnCreateView` para recuperar esta información desde el paquete en el pasado y cargar el texto de pregunta en el `TextBox`: 

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

El `answer` variable no se usa aquí, pero que se usará más adelante cuando el código del controlador de eventos se agrega a este archivo. 


## <a name="create-the-adapter"></a>Crear el adaptador

`ViewPager` usa un objeto de controlador de adaptador que se encuentra entre el `ViewPager` y el origen de datos (vea la ilustración en la ViewPager [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter) artículo). Para obtener acceso a estos datos, `ViewPager` requiere que proporcione un adaptador personalizado derivado de `PagerAdapter`. Puesto que este ejemplo usa fragmentos, utiliza un `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` se deriva de `PagerAdapter`. 
`FragmentPagerAdapter` representa cada página como un `Fragment` que se mantiene en el Administrador de fragmentos de forma persistente siempre y cuando el usuario puede volver a la página. Como los deslizamientos de usuario a través de páginas de la `ViewPager`, el `FragmentPagerAdapter` extrae información del origen de datos y lo usa para crear `Fragment`s para el `ViewPager` para mostrar. 

Cuando se implementa un `FragmentPagerAdapter`, debe reemplazar lo siguiente:

-   **Recuento de** &ndash; propiedad de solo lectura que devuelve el número de vistas (páginas) disponibles.

-   **GetItem** &ndash; devuelve el fragmento que se va a mostrar para la página especificada.

Agregue un nuevo archivo denominado **FlashCardDeckAdapter.cs** y reemplace su contenido con el código siguiente:

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



### <a name="implement-the-adapter-constructor"></a>Implemente el Constructor del adaptador

Cuando crea una instancia de la aplicación la `FlashCardDeckAdapter`, proporciona una referencia al administrador de fragmentos y con instancias `FlashCardDeck`. Agregue la siguiente variable de miembro a la parte superior de la `FlashCardDeckAdapter` clase **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Agregue la siguiente línea de código para el `FlashCardDeckAdapter` constructor: 

```csharp
this.flashCardDeck = flashCards;
```

Esta línea de código almacena el `FlashCardDeck` de instancia que el `FlashCardDeckAdapter` va a usar. 



### <a name="implement-count"></a>Recuento de implementación

El `Count` implementación es relativamente sencilla: devuelve el número de tarjetas flash en la baraja de cartas de flash. Reemplaza `Count` por el código siguiente: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


El `NumCards` propiedad de `FlashCardDeck` devuelve el número de tarjetas flash (número de fragmentos) en el conjunto de datos. 



### <a name="implement-getitem"></a>Implementar GetItem

El `GetItem` método devuelve el fragmento de la posición especificada. Cuando `GetItem` se llama a una posición en la baraja de cartas de flash, devuelve un `FlashCardFragment` configurado para mostrar el problema de la tarjeta de flash en esa posición. Reemplace el método `GetItem` con el código siguiente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Este código hace lo siguiente:

1.  Busca la cadena de problemas de matemáticas en la `FlashCardDeck` cubierta para la posición especificada. 

2.  Busca la cadena de respuesta en el `FlashCardDeck` cubierta para la posición especificada. 

3.  Las llamadas del `FlashCardFragment` método de generador `newInstance`, pasando las cadenas de problema y la respuesta de la tarjeta flash. 

4.  Crea y devuelve una nueva tarjeta de flash `Fragment` que contiene el texto de pregunta y respuesta para esa posición. 

Cuando el `ViewPager` representa el `Fragment` en `position`, muestra la `TextBox` que contiene la cadena de problemas de matemáticas que reside en `position` en la baraja de cartas de flash. 



## <a name="add-the-adapter-to-the-viewpager"></a>Agregar el adaptador a la ViewPager

Ahora que la `FlashCardDeckAdapter` está implementado, es el momento de agregarlo a la `ViewPager`. En **MainActivity.cs**, agregue la siguiente línea de código al final de la `OnCreate` método:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Este código crea instancias el `FlashCardDeckAdapter`, pasando el `SupportFragmentManager` en el primer argumento. (El `SupportFragmentManager` propiedad de FragmentActivity se utiliza para obtener una referencia a la `FragmentManager` &ndash; para obtener más información sobre la `FragmentManager`, consulte [administrar fragmentos](~/android/platform/fragments/managing-fragments.md).) 

La implementación básica está completa ahora &ndash; compilar y ejecutar la aplicación.
Debería ver la primera imagen de la baraja de cartas de flash aparecen en la pantalla, tal como se muestra a la izquierda en la captura de pantalla siguiente. Deslice el dedo hacia izquierdo para ver más tarjetas flash, a continuación, derecha de pasar el dedo para desplazarse por la baraja de cartas de flash:

[![Capturas de pantalla de ejemplo de aplicación FlashCardPager sin indicadores de buscapersonas](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Agregar un indicador de buscapersonas

Este mínimo `ViewPager` implementación muestra cada tarjeta de flash en la baraja, pero no se proporciona ninguna indicación de dónde esté el usuario dentro de la baraja. El siguiente paso es agregar un `PagerTabStrip`. El `PagerTabStrip` informa al usuario en cuanto a qué problema número, se muestra y proporciona el contexto de navegación mostrando una sugerencia de las tarjetas flash anteriores y siguiente. 

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

[![Primer plano de PagerTabStrip sin texto](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Mostrar un título

Para agregar un título para cada pestaña de página, implemente el `GetPageTitleFormatted` método en el adaptador. `ViewPager` las llamadas `GetPageTitleFormatted` (si se implementa) para obtener la cadena de título que describe la página en la posición especificada. Agregue el método siguiente a la `FlashCardDeckAdapter` clase **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Este código convierte la posición en la baraja de cartas de flash en un número del problema. La cadena resultante se convierte en un Java `String` que se devuelve a la `ViewPager`. Al ejecutar la aplicación con este nuevo método, cada página muestra el número de problema en el `PagerTabStrip`: 

[![Capturas de pantalla de FlashCardPager con el número de problema que aparece encima de cada página](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Puede deslizar y hacia atrás para ver el número de problema en la baraja de cartas de flash en el que se muestra en la parte superior de cada tarjeta flash. 



## <a name="handle-user-input"></a>Controlar la entrada de usuario

**FlashCardPager** presenta una serie de basado en el fragmento de tarjetas flash en un `ViewPager`, pero todavía no tiene una manera de mostrar la respuesta para cada problema. En esta sección, se agrega un controlador de eventos para el `FlashCardFragment` para mostrar la respuesta cuando el usuario puntea en el texto del problema de tarjetas flash. 

Abra **FlashCardFragment.cs** y agregue el código siguiente al final de la `OnCreateView` método justo antes de la vista se devuelve al llamador: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Esto `Click` controlador de eventos muestra la respuesta en una notificación del sistema que aparece cuando el usuario pulsa el `TextBox`. El `answer` variable se inicializó anteriormente cuando se ha leído la información de estado desde el paquete que se pasó a `OnCreateView`. Compilar y ejecutar la aplicación, puntee en el texto del problema en cada tarjeta flash para ver la respuesta: 

[![Aplicación de capturas de pantalla de FlashCardPager notificaciones del sistema cuando se pulsa problema matemático](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

El **FlashCardPager** presentados en este tutorial usa un `MainActivity` deriva `FragmentActivity`, pero también se puede derivar `MainActivity` desde `AppCompatActivity` (que también proporciona compatibilidad para la administración de fragmentos). Para ver una `AppCompatActivity` ejemplo, vea [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) en la Galería de ejemplos. 



## <a name="summary"></a>Resumen

Este tutorial proporciona un ejemplo paso a paso de cómo crear un basic `ViewPager`-según la aplicación con `Fragment`s. Presentaba un origen de datos de ejemplo que contiene la tarjeta flash preguntas y respuestas, un `ViewPager` diseño para mostrar las tarjetas flash y un `FragmentPagerAdapter` subclase que conecta el `ViewPager` al origen de datos. Para ayudar al usuario navegar por las tarjetas flash, se incluyen instrucciones que explican cómo agregar un `PagerTabStrip` para mostrar el número de problema en la parte superior de cada página. Por último, se agregan el código de control de eventos para mostrar la respuesta cuando el usuario puntea en un problema con la tarjeta de flash. 



## <a name="related-links"></a>Vínculos relacionados

- [FlashCardPager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
