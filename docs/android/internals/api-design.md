---
title: Principios de diseño de la API de Xamarin.Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 53348e15d1ecc74f50cacdd422da5c80af802d1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110724"
---
# <a name="xamarinandroid-api-design-principles"></a>Principios de diseño de la API de Xamarin.Android


## <a name="overview"></a>Información general

El núcleo de bibliotecas de clases Base que forman parte de Mono, además de Xamarin.Android se suministra con enlaces para distintas API de Android permitir a los desarrolladores crear aplicaciones nativas de Android con Mono.

En el núcleo de Xamarin.Android existe es un motor de interoperabilidad ese mundo puentes de C# con el mundo de Java y ofrece a los desarrolladores con acceso a las API de Java desde C# o en otros lenguajes. NET.


## <a name="design-principles"></a>Principios de diseño

Estos son algunos de nuestros principios de diseño para el enlace de Xamarin.Android

-  Se ajustan a la [instrucciones de diseño de .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

-  Permiten a los desarrolladores a las clases de Java de subclase.

-  Subclase debe funcionar con construcciones de C# estándar.

-  Derivar una clase existente.

-  Llame al constructor base a cadena.

-  Invalidar los métodos debe realizarse con el sistema de invalidación de C#.

-  Realizar tareas comunes de Java sencillas y tareas de disco duras Java posibles.

-  Exponer propiedades JavaBean como propiedades de C#.

-  Exponer una API fuertemente tipada:

    - Aumentar la seguridad de tipos.

    - Minimizar los errores de tiempo de ejecución.

    - Obtener IDE intellisense de tipos de valor devuelto.

    - Permite la documentación del IDE de menú emergente.

-  Recomendamos la exploración en el IDE de la API:

    - Usar alternativas de Framework Classlib de Java de minimizar la exposición.

    - Exponer a los delegados de C# (las expresiones lambda, métodos anónimos y System.Delegate) en lugar de interfaces de método único cuando adecuado y es aplicable.

    - Proporcionan un mecanismo para llamar a las bibliotecas de Java arbitrarias ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Ensamblados

Xamarin.Android incluye una serie de ensamblados que conforman el *MonoMobile perfil*. El [ensamblados](~/cross-platform/internals/available-assemblies.md) página encontrará más información.

Los enlaces a la plataforma Android se encuentran en el `Mono.Android.dll` ensamblado. Este ensamblado contiene todo el enlace para el consumo de API de Android y comunicarse con el tiempo de ejecución Android máquina virtual.


## <a name="binding-design"></a>Diseño de enlace


### <a name="collections"></a>Colecciones

Las API de Android usan las colecciones java.util ampliamente para proporcionar listas, conjuntos y asignaciones. Se exponen estos elementos mediante el [System.Collections.Generic](xref:System.Collections.Generic) interfaces en el enlace. Las asignaciones fundamentales son:

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html) se asigna al tipo de sistema [ICollection<T>](xref:System.Collections.Generic.ICollection`1), clase de aplicación auxiliar [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html) se asigna al tipo de sistema [IList<T>](xref:System.Collections.Generic.IList`1), clase de aplicación auxiliar [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](http://developer.android.com/reference/java/util/Map.html) se asigna al tipo de sistema [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), clase de aplicación auxiliar [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html) se asigna al tipo de sistema [ICollection<T>](xref:System.Collections.Generic.ICollection`1), clase de aplicación auxiliar [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Hemos proporcionado clases auxiliares para facilitar más rápido copyless el cálculo de referencias de estos tipos. Cuando sea posible, se recomienda usar estas proporcionan colecciones en lugar de la implementación del marco proporcionado, al igual que [ `List<T>` ](xref:System.Collections.Generic.List`1) o [ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2). El [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) implementaciones usan internamente una colección nativa de Java y, por tanto, no requieren copiar a y desde una colección nativa cuando se pasa a un miembro de la API de Android.

Puede pasar cualquier implementación de la interfaz a un método Android aceptar dicha interfaz, por ejemplo, pasar un `List<int>` a la [ArrayAdapter&lt;int&gt;(contexto, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)constructor. *Sin embargo*, para todas las implementaciones *excepto* para las implementaciones Android.Runtime, esto implica *copiar* la lista de la máquina virtual de Mono en el tiempo de ejecución Android máquina virtual. Puede cambiar si la lista es una versión posterior en el tiempo de ejecución de Android (por ejemplo, al invocar el [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) método), esos cambios *no* estar visible en código administrado. Si un `JavaList<int>` estaban utilizado, esos cambios serían visibles.

Reformulados, colecciones de las implementaciones que son la interfaz *no* uno de los pasos anteriores muestran **clase auxiliar**es serializar solamente [In]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```


### <a name="properties"></a>Propiedades

Métodos de Java se transforman en Propiedades, cuando corresponda:

-  El par de métodos de Java `T getFoo()` y `void setFoo(T)` se transforman en el `Foo` propiedad. Ejemplo: [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  El método Java `getFoo()` se transforma en la propiedad de solo lectura Foo. Ejemplo: [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  No se generan solo para establecer las propiedades.

-  Las propiedades son *no* generado si el tipo de propiedad sería una matriz.



### <a name="events-and-listeners"></a>Eventos y los agentes de escucha

Las API de Android se basan en Java y sus componentes siguen el patrón de Java para enlazar los agentes de escucha de eventos. Este patrón tiende a ser complicado, ya que requiere que el usuario crear una clase anónima y declarar los métodos de invalidación, por ejemplo, se trata de cómo se podrían hacer cosas en Android con Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

El código equivalente en C# con eventos sería:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Tenga en cuenta que ambos de los mecanismos anteriores están disponibles con Xamarin.Android. Puede implementar una interfaz de agente de escucha y asociarlo con View.SetOnClickListener, o puede adjuntar a un delegado que se crea a través de cualquiera de los habituales paradigmas de C# para el evento Click.

Cuando el método de devolución de llamada del agente de escucha tiene un valor devuelto void, creamos los elementos de la API según un [EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1) delegar. Se genera un evento similar al ejemplo anterior para estos tipos de agente de escucha. Sin embargo, si la devolución de llamada del agente de escucha devuelve un distinto de void y no- **booleano** valor, eventos y controladores de eventos no se utilizan. En su lugar, se genera a un delegado específico para la firma de la devolución de llamada y agregar propiedades en lugar de eventos. Es la razón tratar con el orden de invocación del delegado y devolver el control. Este enfoque refleja lo que se realiza con la API de Xamarin.iOS.

Propiedades o eventos de C# se generan automáticamente solo si el método de registro de eventos Android:

1. Tiene un `set` prefijo, por ejemplo, [ *establecer*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. Tiene un `void` tipo de valor devuelto.

1. Acepta un solo parámetro, el tipo de parámetro es una interfaz, la interfaz tiene un solo método y el nombre de la interfaz finaliza en `Listener` , por ejemplo, [View.OnClick *escucha*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Además, si el agente de escucha del método de interfaz tiene un tipo de valor devuelto de **booleano** en lugar de **void**, a continuación, el generado *EventArgs* subclase contendrá un *Handled* propiedad. El valor de la *Handled* propiedad se utiliza como el valor devuelto para la *escucha* método y el valor predeterminado es `true`.

Por ejemplo, Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) método acepta el [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) interfaz y el [View.OnKeyListener.onKey (ver, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) el método tiene un tipo de valor devuelto boolean. Xamarin.Android genera correspondiente [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) evento, que es un [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
El *KeyEventArgs* clase a su vez tiene una [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propiedad, que se utiliza como el valor devuelto para la *View.OnKeyListener.onKey()* método.

Tenemos previsto agregar sobrecargas para otros métodos y constructores para exponer la conexión basada en el delegado. Además, los agentes de escucha con varias devoluciones de llamada requieren algunos inspección adicional para determinar si la implementación de devoluciones de llamada individuales es razonable, por lo que se está convirtiendo estas que se identifican. Si no hay ningún evento correspondiente, los agentes de escucha deben usarse en C#, pero no dude en los que piensa que podría tener el uso del delegado a nuestra atención. También hemos hecho algunas conversiones de interfaces sin el sufijo "Escucha" cuando estaba claro que se beneficiarían de una alternativa de delegado.

Todas las interfaces de los agentes de escucha de implementan la [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
interfaz, debido a los detalles de implementación del enlace, por lo que las clases de agente de escucha deben implementar esta interfaz. Esto puede hacerse mediante la implementación de la interfaz del agente de escucha en una subclase de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) o cualquier otro objeto de Java, como una actividad de Android de ajustada.


### <a name="runnables"></a>Runnables

Java utiliza el [interfaz java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) interfaz para proporcionar un mecanismo de delegación. El [java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) clase es un consumidor que notables de esta interfaz. Android ha contratado a la interfaz de la API también.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) y [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) son ejemplos importantes.

El `Runnable` interfaz contiene un único método void, [run()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Se presta, por tanto, al enlace en C# como un [System.Action](xref:System.Action) delegar. Se proporcionan las sobrecargas en el enlace que acepten un `Action` parámetro para todos los miembros de la API que consume un `Runnable` en la API nativa, por ejemplo, [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) y [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

Hemos dejado el [IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) sobrecargas en su lugar en lugar de sustituirlas puesto que varios tipos implementan la interfaz y, por tanto, pueden pasarse como runnables directamente.


### <a name="inner-classes"></a>Clases internas

Java tiene dos tipos diferentes de [clases anidadas](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): estático anidar las clases y clases no estáticas.

Las clases anidadas estáticas Java son idénticas a los tipos anidados de C#.

No estáticos clases anidadas, también denominadas *clases internas*, son significativamente diferentes. Que contienen una referencia implícita a una instancia de sus tipos envolventes y no pueden contener a miembros estáticos (entre otras diferencias fuera del ámbito de esta información general).

Cuando se trata de enlace y el uso de C#, las clases anidadas estáticas se tratan como tipos anidados normales. Las clases internas, mientras tanto, tienen dos diferencias importantes:

1. La referencia implícita al tipo contenedor se debe proporcionar explícitamente como un parámetro de constructor.

1. Al heredar de una clase interna, la clase interna *debe* anidarse dentro de un tipo que hereda del tipo de contenedor de la clase base interna, y el tipo derivado debe proporcionar un constructor del mismo tipo como C# que contiene el tipo.


Por ejemplo, considere la [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) clase interna. Puesto que es una clase interna, el [WallpaperService.Engine() constructor](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) toma una referencia a un [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) instancia (compare y contraste a Java [WallpaperService.Engine ( constructor),](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) que no toma ningún parámetro).

Una derivación de ejemplo de una clase interna es CubeWallpaper.CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
        public override WallpaperService.Engine OnCreateEngine ()
        {
                return new CubeEngine (this);
        }

        class CubeEngine : WallpaperService.Engine {
                public CubeEngine (CubeWallpaper s)
                        : base (s)
                {
                }
        }
}
```

Tenga en cuenta cómo `CubeWallpaper.CubeEngine` está anidada dentro de `CubeWallpaper`, `CubeWallpaper` hereda de la clase contenedora de `WallpaperService.Engine`, y `CubeWallpaper.CubeEngine` tiene un constructor que toma el tipo declarativo-- `CubeWallpaper` en este caso, todo ello como especificada anteriormente.


### <a name="interfaces"></a>Interfaces

Las interfaces de Java pueden contener tres conjuntos de miembros, dos de los cuales causar problemas de C#:

1. Métodos

1. Tipos

1. Campos


Interfaces de Java se traducen en dos tipos:

1. Interfaz (opcional) que contiene las declaraciones de método. Esta interfaz tiene el mismo nombre que la interfaz de Java, *excepto* también tiene un ' *me* ' prefijo.

1. Una clase estática (opcional) que contiene todos los campos declarados dentro de la interfaz de Java.

Los tipos anidados son "reubicados" para ser elementos del mismo nivel de la interfaz envolvente en lugar de los tipos anidados, con el nombre de la interfaz envolvente como prefijo.

Por ejemplo, considere la [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) interfaz.
El *Parcelable* interfaz contiene métodos, tipos anidados y constantes. El *Parcelable* métodos de interfaz se colocan en el [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) interfaz.
El *Parcelable* constantes de interfaz se colocan en el [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) tipo. Anidado [android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) y [android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html) tipos no están actualmente enlazado debido a limitaciones en la compatibilidad con genéricos; Si se admitían estarían presentes como el *Android.OS.IParcelableClassLoaderCreator* y *Android.OS.IParcelableCreator* interfaces. Por ejemplo, anidada [android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) interfaz se enlaza como el [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) interfaz.


> [!NOTE]
> A partir de Xamarin.Android 1.9, las constantes de interfaz de Java son <em>duplicado</em> en un esfuerzo por simplificar la migración de Java de código. Esto ayuda a mejorar la portabilidad código Java que se basa en [proveedor android](http://developer.android.com/reference/android/provider/package-summary.html) constantes de la interfaz.

Además de los tipos anteriores, hay más de cuatro cambios:

1. Se genera un tipo con el mismo nombre que la interfaz de Java que contienen constantes.

1. Tipos que contiene las constantes de la interfaz también contienen todas las constantes que proceden de interfaces implementadas de Java.

1. Todas las clases que implementan una interfaz de Java que contiene las constantes de obtención un nuevo tipo InterfaceConsts anidado que contiene las constantes de todas las interfaces implementadas.

1. El *Consts* tipo ahora está obsoleto.


Para el *android.os.Parcelable* interfaz, esto significa que ahora habrá un [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) tipo para contener las constantes. Por ejemplo, el [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) constante se vincularán como el [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) constante en lugar de como el  *ParcelableConsts.ContentsFileDescriptor* constante.

Para las interfaces que contiene las constantes que implementan otras interfaces que contiene aún más constantes, ahora se genera la unión de todas las constantes. Por ejemplo, el [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) interfaz implementa la [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) interfaz. Sin embargo, anteriores a 1.9, el [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) tipo no tiene ninguna manera de obtener acceso a las constantes que se declaran en [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Como resultado, la expresión Java *MediaStore.Video.VideoColumns.TITLE* debe enlazarse a la expresión de C# *MediaStore.Video.MediaColumnsConsts.Title* que es difícil de detectar sin leer gran cantidad de documentación de Java. En la versión 1.9, será la expresión equivalente de C# [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Además, tenga en cuenta la [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) tipo, que implementa el Java *Parcelable* interfaz. Ya que implementa la interfaz, todas las constantes de esa interfaz son accesibles "a través de" el tipo de agrupación, por ejemplo, *Bundle.CONTENTS_FILE_DESCRIPTOR* es una expresión de Java perfectamente válida.
Anteriormente, para esta expresión para el puerto C# debe observar todas las interfaces que se implementan para ver de qué tipo el *CONTENTS_FILE_DESCRIPTOR* procede. A partir de Xamarin.Android 1.9, las clases que implementan las interfaces de Java que contienen constantes tienen un anidada *InterfaceConsts* tipo, que contendrá todas las constantes de la interfaz heredada. Esto le permitirá traducir *Bundle.CONTENTS_FILE_DESCRIPTOR* a [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Por último, los tipos con un *Consts* como sufijo *Android.OS.ParcelableConsts* está ahora obsoleto, aparte de los recién incorporados InterfaceConsts tipos anidados. Se quitará en Xamarin.Android 3.0.


## <a name="resources"></a>Recursos

Se pueden incluir imágenes, descripciones de diseño, objetos BLOB binarios y los diccionarios de cadena de la aplicación como [archivos de recursos](http://developer.android.com/guide/topics/resources/providing-resources.html).
Distintas API de Android están diseñadas para [operan en los identificadores de recursos](http://developer.android.com/guide/topics/resources/accessing-resources.html) en lugar de lidiar con imágenes, cadenas o binario blobs directamente.

Por ejemplo, una aplicación Android ejemplo que contiene un diseño de interfaz de usuario ( `main.axml`), una cadena de la tabla de internacionalización ( `strings.xml`) y algunos iconos ( `drawable-*/icon.png`) mantendría sus recursos en el directorio "Resources" de la aplicación:

    Resources/
        drawable-hdpi/
            icon.png

        drawable-ldpi/
            icon.png

        drawable-mdpi/
            icon.png

        layout/
            main.axml

        values/
            strings.xml

Nativo API de Android no funcionan directamente con los nombres de archivo, pero en su lugar, operan en los identificadores de recursos. Al compilar una aplicación Android que usa los recursos, el sistema de compilación empaquetar los recursos para la distribución y generará una clase denominada `Resource` que contiene los símbolos para cada uno de los recursos incluidos. Por ejemplo, para el diseño de los recursos anterior, esto es lo que podría exponer la clase:

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

A continuación, se usaría `Resource.Drawable.icon` para hacer referencia a la `drawable/icon.png` archivo, o `Resource.Layout.main` para hacer referencia a la `layout/main.xml` archivo, o `Resource.String.first_string` para hacer referencia a la primera cadena en el archivo de diccionario `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Constantes y enumeraciones

Nativo de API de Android tiene muchos métodos que toman o devuelven un valor int que debe asignarse a un campo constante para determinar lo que significa que el tipo int. Para usar estos métodos, el usuario es necesario consultar la documentación para ver qué constantes son valores apropiados, que es ideal.

Por ejemplo, considere la posibilidad de [Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

En estos casos, nos esforzamos por agrupar constantes relacionadas en una enumeración de .NET y reasignar el método para realizar la enumeración en su lugar.
Al hacerlo, se pueden ofrecer IntelliSense de selección de los valores posibles.

El ejemplo anterior se convierte en: [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/)).

Tenga en cuenta que esto es un proceso muy manual para averiguar qué constantes forman un conjunto y las API que consumen estas constantes. Registre los errores para cualquier uso de las constantes de la API que sería mejor expresada como una enumeración.
