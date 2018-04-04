---
title: Parte 1. Introducción a XAML
description: En una aplicación de Xamarin.Forms, XAML se usa principalmente para definir el contenido visual de una página. Un archivo XAML siempre está asociado a un archivo de código C# que proporciona compatibilidad de código para el marcado. Juntos, estos dos archivos contribuyen a una nueva definición de clase que incluye vistas secundarias y la inicialización de la propiedad. En el archivo XAML, se hace referencia a las clases y propiedades con atributos y elementos XML y se establecen los vínculos entre el código y marcado.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: f1d347cc37ab065e09cb2627ee2122797280f3c1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introducción a XAML

_En una aplicación de Xamarin.Forms, XAML se usa principalmente para definir el contenido visual de una página. Un archivo XAML siempre está asociado a un archivo de código C# que proporciona compatibilidad de código para el marcado. Juntos, estos dos archivos contribuyen a una nueva definición de clase que incluye vistas secundarias y la inicialización de la propiedad. En el archivo XAML, se hace referencia a las clases y propiedades con atributos y elementos XML y se establecen los vínculos entre el código y marcado._

## <a name="creating-the-solution"></a>Crear la solución

Para comenzar a editar el primer archivo XAML, utilice Visual Studio o Visual Studio para Mac para crear una nueva solución de Xamarin.Forms. (Seleccione la pestaña en la parte superior de esta página correspondientes a su entorno).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En Windows, use Visual Studio para seleccionar **archivo > Nuevo > proyecto** en el menú. En el **nuevo proyecto** cuadro de diálogo, seleccione **Visual C# > multiplataforma** a la izquierda y, a continuación, **aplicación de plataforma cruzada (Xamarin.Forms o nativo)** de la lista en el centro. 

![](get-started-with-xaml-images/win/newprojectdialog.png "Cuadro de diálogo nuevo proyecto")

Seleccione una ubicación para la solución, asígnele un nombre de **XamlSamples** (o lo que prefiere) y presione **Aceptar**.

En la siguiente pantalla, seleccione la **aplicación vacía** plantilla, el **Xamarin.Forms** tecnología de interfaz de usuario y el **biblioteca de clases portables (PCL)** estrategia para compartir el código:

![](get-started-with-xaml-images/win/newcrossplatformapp.png "Cuadro de diálogo nueva aplicación")

Press **OK**. 

Se crean cuatro proyectos en la solución: el **XamlSamples** biblioteca de clases portables (PCL), **XamlSamples.Android**, **XamlSamples.iOS**y las ventanas Universal Solución de plataforma, **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En Visual Studio para Mac, seleccione **archivo > nueva solución** en el menú. En el **nuevo proyecto** cuadro de diálogo, seleccione **multiplataforma > aplicación** a la izquierda, y **aplicación de formularios en blanco** (*no* **aplicación de formularios** ) de la lista de plantillas:

![](get-started-with-xaml-images/mac/newprojectdialog1.png "Nuevo cuadro de diálogo de proyecto 1")

Presione **siguiente**.

En el cuadro de diálogo siguiente, asigne al proyecto un nombre de **XamlSamples** (o lo que prefiere). Asegúrese de que el **biblioteca de clases Portable de uso** botón de radio está seleccionado y que **uso XAML para los archivos de la interfaz de usuario** está activada:

![](get-started-with-xaml-images/mac/newprojectdialog2.png "Cuadro de diálogo de proyecto nuevo 2")

Presione **siguiente**. 

En el siguiente cuadro de diálogo, puede seleccionar una ubicación para el proyecto:

![](get-started-with-xaml-images/mac/newprojectdialog3.png "Cuadro de diálogo de proyecto nuevo 3")

Presione **crear**

Se crean tres proyectos en la solución: el **XamlSamples** biblioteca de clases portables (PCL), **XamlSamples.Android**, y **XamlSamples.iOS**. 

-----

Después de crear el **XamlSamples** solución, es posible que desee probar el entorno de desarrollo seleccionando los diversos proyectos de plataforma como proyecto de inicio de la solución y crear e implementar la aplicación simple crean por la plantilla de proyecto en dispositivos reales o emuladores de teléfono.

A menos que necesite escribir código específico de la plataforma, el recurso compartido **XamlSamples** proyecto PCL es donde se va a pasar prácticamente todo el tiempo de programación. Estos artículos no se ir fuera de ese proyecto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomía de un archivo XAML

En el **XamlSamples** biblioteca de clases portables forman un par de archivos con los siguientes nombres:

- **App.XAML**, el archivo XAML; y
- **App.Xaml.cs**, C# *código subyacente* archivo asociado con el archivo XAML.

Debe hacer clic en la flecha situada junto a **App.xaml** para ver el archivo de código subyacente. 

Ambos **App.xaml** y **App.xaml.cs** contribuyen a una clase denominada `App` que se deriva de `Application`. La mayoría de otras clases con archivos XAML contribuyen a una clase que deriva de `ContentPage`; esos archivos usan XAML para definir el contenido visual de una página completa. Esto es así de los otros dos archivos en el **XamlSamples** proyecto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- **MainPage.xaml**, el archivo XAML; y
- **MainPage.xaml.cs**, el archivo de código subyacente de C#.

El **MainPage.xaml** archivo tiene el siguiente aspecto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <Label Text="Welcome to Xamarin Forms!" 
           VerticalOptions="Center" 
           HorizontalOptions="Center" />

</ContentPage>
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

- **XamlSamplesPage.xaml**, el archivo XAML; y
- **XamlSamplesPage.xaml.cs**, el archivo de código subyacente de C#.

El **XamlSamplesPage.xaml** archivo tiene el siguiente aspecto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" 
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
             xmlns:local="clr-namespace:XamlSamples" 
             x:Class="XamlSamples.XamlSamplesPage">

    <Label Text="Welcome to Xamarin Forms!" 
           VerticalOptions="Center" 
           HorizontalOptions="Center" />

</ContentPage>
```

-----

El espacio de nombres XML dos ( `xmlns`) declaraciones hacen referencia a identificadores URI, la primera aparentemente en el sitio de web de Xamarin y el segundo de Microsoft. No pierda tiempo qué esos punto URI para la comprobación. No hay nada no existe. Son simplemente los URI que pertenecen a Microsoft y Xamarin y, básicamente funcionan como identificadores de versión.

La primera declaración de espacio de nombres XML significa que etiquetas definidas en el archivo XAML no tiene ningún prefijo hace referencia a las clases de Xamarin.Forms, por ejemplo `ContentPage`. La segunda declaración de espacio de nombres define un prefijo de `x`. Se utiliza para varios elementos y atributos que son intrínsecos de XAML en sí mismo y que son compatibles con otras implementaciones de XAML. Sin embargo, estos elementos y atributos son ligeramente diferentes según el año incrustado en el URI. Xamarin.Forms es compatible con la especificación de XAML 2009, pero no todas del mismo.

El `local` declaración de espacio de nombres permite obtener acceso a otras clases del proyecto PCL.

Al final de la primera etiqueta, el `x` prefijo se utiliza para un atributo denominado `Class`. Dado que el uso de este `x` prefijo es prácticamente universal para el espacio de nombres XAML, atributos XAML como `Class` casi siempre se conocen como `x:Class`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El `x:Class` atributo especifica un nombre de clase .NET completo: el `MainPage` clase en el `XamlSamples` espacio de nombres. Esto significa que este archivo XAML define una nueva clase denominada `MainPage` en el `XamlSamples` espacio de nombres que se deriva de `ContentPage`: la etiqueta en el que el `x:Class` atributo aparece.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El `x:Class` atributo especifica un nombre de clase .NET completo: el `XamlSamplesPage` clase en el `XamlSamples` espacio de nombres. Esto significa que este archivo XAML define una nueva clase denominada `XamlSamplesPage` en el `XamlSamples` espacio de nombres que se deriva de `ContentPage`: la etiqueta en el que el `x:Class` atributo aparece.

-----

El `x:Class` atributo solo puede aparecer en el elemento raíz de un archivo XAML para definir una clase derivada de C#. Ésta es la clase solo nueva definida en el archivo XAML. Todo lo demás que aparece en el archivo XAML se en su lugar, basta con crear instancias de clases existentes e inicializa.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El **MainPage.xaml.cs** archivo tiene el siguiente aspecto (además de sin usar `using` directivas):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

El `MainPage` clase se deriva de `ContentPage`, pero tenga en cuenta el `partial` definición de clase. Esto sugiere que debería haber otra definición de clase parcial para `MainPage`, pero ¿dónde está? Y ¿qué es que `InitializeComponent` método? 

Cuando Visual Studio compila el proyecto, analiza el archivo XAML para generar un archivo de código de C#. Si observa el **XamlSamples\XamlSamples\obj\Debug** directory, encontrará un archivo denominado **XamlSamples.MainPage.xaml.g.cs**. "g" son las siglas de generadas. Se trata de la otra definición de clase parcial de `MainPage` que contiene la definición de la `InitializeComponent` método invocado desde el `MainPage` constructor. Estos dos parcial `MainPage` definiciones de clase, a continuación, se compilan juntos. Dependiendo de si se compila el código XAML o no, el archivo XAML o en un formato binario del archivo XAML se incrusta en el archivo ejecutable.

En tiempo de ejecución de código en las llamadas de proyecto de plataforma en particular una `LoadApplication` método, pasándole una nueva instancia de la `App` clase en la PCL. El `App` crea una instancia de constructor de clase `MainPage`. Llama al constructor de la clase `InitializeComponent`, que llama a la `LoadFromXaml` método que extrae el archivo XAML (o su binario compilado) de la PCL. `LoadFromXaml` Inicializa todos los objetos definidos en el archivo XAML, conecta todos juntos en las relaciones de elementos primarios y secundarios, adjunta los controladores de eventos definidos en el código a los eventos que se establece en el archivo XAML y establece el árbol de objetos resultante como el contenido de la página.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El **XamlSamplesPage.xaml.cs** archivo tiene el siguiente aspecto:

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class XamlSamplesPage : ContentPage
    {
        public XamlSamplesPage()
        {
            InitializeComponent();
        }
    }
}
```

El `XamlSamplesPage` clase se deriva de `ContentPage`, pero tenga en cuenta el `partial` definición de clase. Se sugiere que debería haber otro archivo de C# con otra definición de clase parcial para `XamlSamplesPage`, pero ¿dónde está? Y ¿qué es que `InitializeComponent` método?

Cuando Visual Studio para Mac compila el proyecto, analiza el archivo XAML para generar un archivo de código de C#. Si observa el **XamlSamples\XamlSamples\obj\Debug** directory, encontrará un archivo denominado **XamlSamples.XamlSamplesPage.xaml.g.cs**. "g" son las siglas de generadas. Se trata de la otra definición de clase parcial de `XamlSamplesPage` que contiene la definición de la `InitializeComponent` método invocado desde el `XamlSamplesPage` constructor.  Estos dos parcial `XamlSamplesPage` definiciones de clase, a continuación, se compilan juntos. Dependiendo de si se compila el código XAML o no, el archivo XAML o en un formato binario del archivo XAML se incrusta en el archivo ejecutable.

En tiempo de ejecución de código en las llamadas de proyecto de plataforma en particular una `LoadApplication` método, pasándole una nueva instancia de la `App` clase en la PCL. El `App` crea una instancia de constructor de clase `XamlSamplesPage`. Llama al constructor de la clase `InitializeComponent`, que llama a la `LoadFromXaml` método que extrae el archivo XAML (o su binario compilado) de la PCL. `LoadFromXaml` Inicializa todos los objetos definidos en el archivo XAML, conecta todos juntos en las relaciones de elementos primarios y secundarios, adjunta los controladores de eventos definidos en el código a los eventos que se establece en el archivo XAML y establece el árbol de objetos resultante como el contenido de la página.

-----

Aunque normalmente no es necesario dedicar mucho tiempo con archivos de código generados, en ocasiones, en tiempo de ejecución se producen excepciones en el código de los archivos generados, por lo que debe estar familiarizado con ellos.

Cuando se compila y ejecuta este programa, el `Label` elemento aparece en el centro de la página como sugiere el XAML. Las tres plataformas de izquierda a derecha son iOS, Android y Windows 10 Mobile:

[![](get-started-with-xaml-images/xamlsamples.png "Predeterminados de presentación de Xamarin.Forms")](get-started-with-xaml-images/xamlsamples-large.png#lightbox "mostrar Xamarin.Forms predeterminado")

Con efectos visuales más interesantes, todo lo que necesita es más interesante XAML.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="preliminaries"></a>Pasos preliminares

Para que los nombres de archivo en Visual Studio para Mac coherente con los archivos creados por Visual Studio que se ejecuta en Windows, cambie el nombre **XamlSamplesPage.xaml** a **MainPage.xaml**, y  **XamlSamplesPage.xaml.cs** a **MainPage.xaml.cs**. En el **XamlSamplesPage.xaml** de archivos, cambiar `XamlSamplesPage` a `MainPage`. En el **XamlSamplesPage.xaml.cs** de archivos, cambiar dos apariciones de `XamlSamplesPage` a `MainPage`. En el **App.xaml.cs** de archivo, cambie la instrucción

```csharp
MainPage = new XamlSamplesPage();
```

a:

```csharp
MainPage = new MainPage();
```

-----

Compruebe que el programa aún compila e implementa antes de continuar.

## <a name="adding-new-xaml-pages"></a>Agregar nuevas páginas XAML

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para agregar otros basadas en XAML `ContentPage` clases al proyecto, seleccione la **XamlSamples** PCL proyecto e invocar el **proyecto > Agregar nuevo elemento** elemento de menú. A la izquierda de la **Agregar nuevo elemento** cuadro de diálogo, seleccione **Visual C#** y **Xamarin.Forms**. En la lista seleccione **página contenido** (no **página de contenido (C#)**, que crea una página de solo código, o **vista de contenido**, que no es una página). Asigne un nombre, por ejemplo, de la página **HelloXamlPage.xaml**:

![](get-started-with-xaml-images/win/addnewitemdialog.png "Agregar nuevo elemento de cuadro de diálogo")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para agregar otros basadas en XAML `ContentPage` clases al proyecto, seleccione la **XamlSamples** PCL proyecto e invocar el **archivo > nuevo archivo** elemento de menú. A la izquierda de la **nuevo archivo** cuadro de diálogo, seleccione **formularios** a la izquierda, y **Forms contenidoPage Xaml** (no **contenidoPage Forms**, que crea una página de solo código, o **vista de contenido**, que no es una página). Asigne un nombre, por ejemplo, de la página **HelloXamlPage**:

![](get-started-with-xaml-images/mac/newfiledialog.png "Nuevo cuadro de diálogo de archivo")

-----

Se agregan dos archivos al proyecto, **HelloXamlPage.xaml** y el archivo de código subyacente **HelloXamlPage.xaml.cs**. 

## <a name="setting-page-content"></a>Contenido de la página de configuración

Editar la **HelloXamlPage.xaml** de archivos para que las etiquetas sola son aquellas de `ContentPage` y `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>
        
    </ContentPage.Content>
</ContentPage>
```

El `ContentPage.Content` etiquetas forman parte de la sintaxis de XAML única. En primer lugar, aparecen como XML no válido, pero son válidos. El período de no es un carácter especial en XML.

El `ContentPage.Content` se denominan etiquetas *elemento property* etiquetas. `Content` es una propiedad de `ContentPage`y generalmente se establece en una vista única o un diseño con vistas secundarias. Normalmente propiedades se convierten en atributos en XAML, pero sería difícil de configurar un `Content` atributo a un objeto complejo. Por esta razón, la propiedad se expresa como un elemento XML que se compone del nombre de la clase y el nombre de propiedad separados por puntos. Ahora el `Content` propiedad puede establecerse entre el `ContentPage.Content` etiquetas, similar al siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

Observe también que un `Title` se ha establecido el atributo en la etiqueta raíz.

En este momento, la relación entre las clases, propiedades y XML debe ser evidente: Xamarin.Forms de una clase (como `ContentPage` o `Label`) aparece en el archivo XAML como un elemento XML. Propiedades de esa clase, incluidos los `Title` en `ContentPage` y siete propiedades de `Label`, suelen aparecer como atributos XML.

Existen muchos métodos abreviados para establecer los valores de estas propiedades. Algunas propiedades son tipos de datos básicos: por ejemplo, el `Title` y `Text` propiedades son de tipo `String`, `Rotation` es de tipo `Double`, y `IsVisible` (que es `true` de forma predeterminada y se establece aquí sólo para ilustración) es de tipo `Boolean`.

El `HorizontalTextAlignment` propiedad es de tipo `TextAlignment`, que es una enumeración. Para una propiedad de cualquier tipo de enumeración, todo lo que necesita la fuente de alimentación es un nombre de miembro.

Sin embargo, para las propiedades de tipos más complejos, convertidores se utilizan para el análisis de XAML. Estas son clases de Xamarin.Forms que derivan de `TypeConverter`. Muchos son clases públicas pero otros no. Para este archivo XAML en particular, algunas de estas clases desempeñan un papel en segundo plano:

-  `LayoutOptionsConverter` para el `VerticalOptions` propiedad
-  `FontSizeConverter` para el `FontSize` propiedad
-  `ColorTypeConverter` para el `TextColor` propiedad

Estos convertidores rigen la sintaxis permitida de los valores de propiedad.

La `ThicknessTypeConverter` puede controlar uno, dos o cuatro números separados por comas. Si se proporciona un número, se aplica a los cuatro lados. Con dos números, el primero es left y right relleno y el segundo es la parte superior e inferior. Son cuatro números de orden izquierda, superior, derecha e inferior.

El `LayoutOptionsConverter` puede convertir los nombres de los campos estáticos públicos de la `LayoutOptions` estructura a valores de tipo `LayoutOptions`.

El `FontSizeConverter` puede controlar un `NamedSize` miembro o un tamaño de fuente numérico.

El `ColorTypeConverter` acepta los nombres de los campos estáticos públicos de la `Color` estructura o los valores RGB hexadecimales, con o sin un canal alfa, precedido por un signo de número (#). Esta es la sintaxis sin un canal alfa:

 `TextColor="#rrggbb"`

Cada una de las letras poco es un dígito hexadecimal. Le mostramos cómo un canal alfa se incluye:

 `TextColor="#aarrggbb">`

Para el canal alfa, tenga en cuenta que FF es completamente opaco y 00 es totalmente transparente.

Dos otros formatos le permiten especificar un único dígito hexadecimal para cada canal:

 `TextColor="#rgb"` `TextColor="#argb"`

En estos casos, se repite el dígito para formar el valor. Por ejemplo, #CF3 es el color RGB CC-FF-33.

## <a name="page-navigation"></a>Navegación de páginas

Al ejecutar el **XamlSamples** programa, el `MainPage` se muestra. Para ver el nuevo `HelloXamlPage` puede establecer que, como el inicio de la nueva página en el **App.xaml.cs** de archivo o vaya a la nueva página desde `MainPage`.

Para implementar la navegación, cambie primero el código de la **App.xaml.cs** constructor para que un `NavigationPage` se crea el objeto:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

En el **MainPage.xaml.cs** constructor, puede crear un sencillo `Button` y usar el controlador de eventos para navegar a `HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

Establecer el `Content` propiedad de la página reemplaza la configuración de la `Content` propiedad en el archivo XAML. Al compilar e implementar la nueva versión de este programa, aparece un botón en la pantalla. Presionarlo navega a `HelloXamlPage`. Ésta es la página resultante en el iPhone, Android y Windows 10 Mobile dispositivos:

[![](get-started-with-xaml-images/helloxaml1.png "Girar el texto de la etiqueta")](get-started-with-xaml-images/helloxaml1-large.png#lightbox "girar el texto de etiqueta")

Puede navegar hacia `MainPage` mediante la **< Atrás** botón en iOS, utilizando la flecha izquierda en la parte superior de la página o en la parte inferior del teléfono en Android, o mediante la flecha izquierda en la parte inferior de la página en Windows 10 Mobile.

Puede experimentar con el código XAML para las distintas formas representar la `Label`. Si necesita insertar caracteres Unicode en el texto, puede usar la sintaxis XML estándar. Por ejemplo, para poner el saludo en las comillas tipográficas, use:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Este es su aspecto:

[![](get-started-with-xaml-images/helloxaml2.png "Girar el texto de la etiqueta con caracteres Unicode")](get-started-with-xaml-images/helloxaml2-large.png#lightbox "girar el texto de la etiqueta con caracteres Unicode")

## <a name="xaml-and-code-interactions"></a>XAML y las interacciones del código

El **HelloXamlPage** ejemplo contiene sólo un único `Label` en la página, pero esto es muy poco habitual. La mayoría `ContentPage` derivados conjunto el `Content` ordenar de propiedad a un diseño de algunos, como un `StackLayout`. El `Children` propiedad de la `StackLayout` se define como de tipo `IList<View>` pero es realmente un objeto del tipo `ElementCollection<View>`, y que se puede rellenar colección con varias vistas u otros esquemas. En XAML, estas relaciones de elementos primarios y secundarios se establecen con la jerarquía XML normal. Este es un archivo XAML para una nueva página denominada **XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Este archivo XAML está completa sintácticamente, y este es su aspecto:

[![](get-started-with-xaml-images/xamlpluscode1.png "Varios controles en una página")](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox "varios controles en una página")

Sin embargo, es probable que observe este programa sea funcionalmente deficientes. Quizás el `Slider` debe para provocar la `Label` para mostrar el valor actual y el `Button` probablemente está diseñado para hacer algo dentro del programa.

Como verá en [parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), el trabajo de presentación de un `Slider` valor mediante un `Label` puede controlarse completamente en XAML con un enlace de datos. Pero resulta útil ver la solución de código en primer lugar. Aun así, controlar la `Button` haga clic en duda de código que requiere. Esto significa que el archivo de código subyacente para `XamlPlusCodePage` debe contener controladores para la `ValueChanged` eventos de la `Slider` y `Clicked` eventos de la `Button`. Vamos a agregarlos:

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

Estos controladores de eventos no es necesario que ser público.

En el archivo XAML, el `Slider` y `Button` etiquetas es necesario incluir atributos para el `ValueChanged` y `Clicked` eventos que hacen referencia a estos controladores:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

Tenga en cuenta que asignar a un controlador a un evento tiene la misma sintaxis que asignar un valor a una propiedad.

Si el controlador para el `ValueChanged` eventos de la `Slider` va a utilizar el `Label` para mostrar el valor actual, el controlador necesita hacer referencia a ese objeto desde el código. El `Label` requiere un nombre, que se especifica con el `x:Name` atributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

El `x` prefijo de la `x:Name` atributo indica que este atributo es intrínseco a XAML.

El nombre que asigne a la `x:Name` atributo tiene las mismas reglas que los nombres de variables de C#. Por ejemplo, debe empezar con una letra o un carácter de subrayado y no contener espacios incrustados.

Ahora el `ValueChanged` controlador de eventos puede establecer el `Label` para mostrar el nuevo `Slider` valor. El nuevo valor está disponible en los argumentos de evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

O bien, se puede obtener el controlador de la `Slider` objeto que se va a generar este evento desde el `sender` argumento y obtener el `Value` propiedad desde el que:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Cuando ejecuta el programa por primera vez el `Label` no se muestra la `Slider` valor porque el `ValueChanged` aún no se desencadena el evento. Pero cualquier manipulación de los `Slider` hace que el valor que se mostrará:

[![](get-started-with-xaml-images/xamlpluscode2.png "Valor del control deslizante muestra")](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox "valor del control deslizante muestra")

Ahora para el `Button`. Vamos a simular una respuesta a una `Clicked` eventos mediante una alerta con la `Text` del botón. El controlador de eventos puede difundir con seguridad el `sender` argumento pasado a un `Button` y, a continuación, obtener acceso a sus propiedades:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

El método se define como `async` porque el `DisplayAlert` método es asincrónico y deben ir precedida con el `await` operador, que se devuelve cuando se completa el método. Dado que este método obtiene el `Button` desencadenar el evento desde el `sender` argumento, el mismo controlador podría utilizarse para varios botones.

Ha visto que un objeto definido en XAML puede desencadenar un evento que se controla en el archivo de código subyacente y que el archivo de código subyacente puede tener acceso a un objeto definido en XAML mediante el nombre asignado a él con el `x:Name` atributo. Estos son los dos aspectos fundamentales que interactúan código y XAML.

Algunas características adicionales sobre cómo funciona el XAML puede se pueden deducir examinando recién generado **XamlPlusCode.xaml.g.cs archivo**, que ahora incluye cualquier nombre asignado a cualquier `x:Name` atributo como un campo privado. Esta es una versión simplificada de dicho archivo:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La declaración de este campo permite que la variable que se utilizará libremente en cualquier parte dentro de la `XamlPlusCodePage` archivo de clase parcial en su jurisdicción. En tiempo de ejecución, el campo se asigna después el código XAML que se ha analizado. Esto significa que la `valueLabel` campo es `null` cuando el `XamlPlusCodePage` constructor comienza pero válidos tras `InitializeComponent` se llama.

Después de `InitializeComponent` devuelve el control vuelve al constructor, los objetos visuales de la página se ha creado como si se hubiera inicializado en el código y crea una instancia. El archivo XAML ya no desempeña ningún rol en la clase. Puede manipular estos objetos en la página de ninguna manera que desee, por ejemplo, mediante la adición de vistas para la `StackLayout`, o la configuración de la `Content` propiedad de la página a otra cosa por completo. Se puede "recorrer el árbol" mediante el examen de la `Content` propiedad de la página y los elementos de la `Children` colecciones de los diseños. Puede establecer propiedades en las vistas de acceso de esta manera, o asignar dinámicamente los controladores de eventos a ellos.

No dude. Es la página y XAML es solo una herramienta para generar su contenido.

## <a name="summary"></a>Resumen

Con esta introducción, ha visto cómo un archivo XAML y el archivo de código influyen en una definición de clase, y cómo interactúan los archivos XAML y código. Pero XAML también tiene sus propias características sintácticas únicas que permiten que se pueda usar en una forma muy flexible. Puede empezar a explorar estas opciones en [parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
