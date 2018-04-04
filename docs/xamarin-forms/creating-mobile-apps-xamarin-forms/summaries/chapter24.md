---
title: Resumen de capítulo 24. Navegación de páginas
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9d1a226a4532b745fddee28e943562fb51d34e65
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumen de capítulo 24. Navegación de páginas

Muchas aplicaciones constan de varias páginas, entre los que el usuario se desplaza. La aplicación siempre tiene un *principal* página o *principal* página, y a partir de ahí, el usuario navega a otras páginas, que se mantienen en una pila para navegar hacia atrás. Opciones de navegación adicionales se tratan en [ **capítulo 25. Página variedades**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modales y no modales

`VisualElement` define un [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de tipo [ `INavigation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INavigation/), que incluye los dos métodos siguientes para navegar a una nueva página:

- [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)
- [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/)

Ambos métodos aceptan un `Page` instancia como un argumento y devuelve un `Task` objeto. Los dos métodos siguientes navegue a la página anterior:

- [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync()/)
- [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/)

Si la interfaz de usuario tiene su propio **volver** botón (como los teléfonos Android y Windows) no es necesaria para la aplicación llamar a estos métodos.

Aunque estos métodos están disponibles desde cualquier `VisualElement`, por lo general, se les llama desde la `Navigation` propiedad del elemento actual `Page` instancia.

Las aplicaciones utilizan generalmente páginas modales cuando el usuario tiene que proporcionar cierta información en la página antes de volver a la página anterior. A veces se denominan páginas que no son modales no modales o *jerárquica*. Nada en la propia página distingue como modal o no modal; se rige en su lugar el método utilizado para navegar hasta él. Para trabajar con todas las plataformas, una página modal debe proporcionar su propia interfaz de usuario para navegar a la página anterior.

El [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) ejemplo permite explorar la diferencia entre las páginas modales y no modales. Cualquier aplicación que utiliza la navegación de página debe pasar su página de inicio para la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) constructor, por lo general en el programa `App` clase. Una bonificación es que ya no se necesita establecer un `Padding` en la página de iOS.

Descubrirá que no modal páginas, la página de [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) propiedad se muestra. IOS, Android y las plataformas de Tablet PC y el escritorio de Windows proporcionan un elemento de interfaz de usuario para volver a la página anterior. De curso, Android y Windows dispositivos phone tienen un estándar **volver** botón para volver atrás.

Para las páginas modales, la página `Title` no se muestra, y no se proporciona ningún elemento de interfaz de usuario para volver a la página anterior. Aunque puede usar el estándar de teléfono Android y Windows **volver** botón para volver a la página anterior, la página modal en las otras plataformas debe proporcionar su propio mecanismo para volver atrás.

### <a name="animated-page-transitions"></a>Transiciones de página animado

Se proporcionan versiones alternativas de los diversos métodos de navegación con un segundo argumento booleano que se establece en `true` si desea que la transición de página para incluir una animación:

- [PushAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PushModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PopAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync/p/System.Boolean/)
- [PopModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync/p/System.Boolean/)

Sin embargo, los métodos de navegación de páginas estándar incluyen la animación de forma predeterminada, por lo que éstos sólo son valiosos para navegar a una página concreta de inicio (como se describe al final de este capítulo) o al proporcionar su propia animación de entrada (como se describe en [ **Chapter22. Animación**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variaciones visuales y funcionales

`NavigationPage` incluye dos propiedades que se pueden establecer al crear una instancia de la clase en su `App` método:

- [`BarBackgroundColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)
- [`BarTextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarTextColor/)

`NavigationPage` También incluye cuatro propiedades enlazables adjuntas que afectan a la página correspondiente en el que se establecen:

- [`SetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasBackButton/p/Xamarin.Forms.Page/System.Boolean/) y [`GetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasBackButton/p/Xamarin.Forms.Page/)
- [`SetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasNavigationBar/p/Xamarin.Forms.BindableObject/System.Boolean/) y [`GetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasNavigationBar/p/Xamarin.Forms.BindableObject/)
- [`SetBackButtonTitle`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetBackButtonTitle/p/Xamarin.Forms.BindableObject/System.String/) y [ `GetBackButtonTitle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetBackButtonTitle/p/Xamarin.Forms.BindableObject/) en solo iOS
- [`SetTitleIcon`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetTitleIcon/p/Xamarin.Forms.BindableObject/Xamarin.Forms.FileImageSource/) y [ `GetTitleIcon` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetTitleIcon/p/Xamarin.Forms.BindableObject/) en iOS y Android únicamente

### <a name="exploring-the-mechanics"></a>Explorar la mecánica

Los métodos de navegación de página son todos los asincrónicos y se debe utilizar con `await`. La finalización no indica que navegación de una página se ha completado, pero solo que es seguro examinar la pila de navegación de páginas.

Cuando se desplaza una página a otra, la primera página obtiene generalmente una llamada a su [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/) método y la segunda página obtiene una llamada a su [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) método. De forma similar, cuando se devuelve una página a otra, la primera página obtiene una llamada a su `OnDisappearing` método y la segunda página normalmente obtiene una llamada a su `OnAppearing` método. El orden de estas llamadas (y la finalización de los métodos asincrónicos que invoca el panel de navegación) es depende de la plataforma. El uso de la palabra "general" de las dos instrucciones anteriores es debido al desplazamiento de página modal Android, en el que no se producen estas llamadas al método.

Además, las llamadas a la `OnAppearing` y `OnDisappearing` métodos no indican necesariamente la navegación en páginas.

El `INavigation` interfaz incluye dos propiedades de la colección que permiten examinar la pila de navegación:

- [`NavigationStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) de tipo `IReadOnlyList<Page>` para la pila no modal
- [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) de tipo `IReadOnlyList<Page>` para la pila modal

Que es más seguro tener acceso a estas pilas de la `Navigation` propiedad de la `NavigationPage` (que debe ser el `App` la clase [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propiedad). Solo es seguro examinar estas pilas después de han completado los métodos de navegación de página asincrónica. El [ `CurrentPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.CurrentPage/) propiedad de la `NavigationPage` no indica la página actual si la página actual es una página modal, pero indica en su lugar la última página no modal.

El [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) ejemplo le permite explorar la navegación en páginas y las pilas y los tipos válidos de navegación de página:

- Una página no modal puede navegar a otra página no modal o una página modal
- Una página modal sólo puede navegar hasta otra página modal

### <a name="enforcing-modality"></a>Modalidad de la implantación

Una aplicación utiliza una página modal cuando sea necesario obtener la información del usuario. El usuario debe estar prohibido devuelvan a la página anterior hasta que dicha información se proporcione. En iOS, resulta sencillo proporcionar un **volver** botón y habilitarlo sólo cuando el usuario ha terminado con la página. Pero para dispositivos de Android y Windows phone, la aplicación debe invalidar la [ `OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed()/) método y devuelven `true` si programa ha controlado la **volver** botón, como se muestra en el [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) ejemplo.

El [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) ejemplo muestra cómo funciona en un escenario MVVM.

## <a name="navigation-variations"></a>Variaciones de navegación

Si una determinada página modal se puede navegar a varias veces, debe conservar información para que el usuario puede editar la información en lugar de escribirlo todo en nuevo. Puede controlar esto, pero se mantiene la instancia concreta de la página modal, pero un enfoque más adecuado (especialmente en iOS) conserva la información de un modelo de vista.

### <a name="making-a-navigation-menu"></a>Hacer que un menú de navegación

El [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) ejemplo muestra cómo utilizar un `TableView` para enumerar los elementos de menú. Cada elemento está asociado con un `Type` objeto para una página determinada. Cuando se selecciona ese elemento, el programa crea una instancia de la página y navega a ella.

[![Captura de pantalla triple de tipo de vista de galería](images/ch24fg21-small.png "elementos de menú de listado de TableView")](images/ch24fg21-large.png#lightbox "TableView enumerar elementos de menú")

El [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) ejemplo es un poco distinto que el menú contiene instancias de cada página en lugar de tipos. Esto ayuda a conservar la información de cada página, pero deben crear instancias de todas las páginas al inicio del programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) muestra varias funciones definidas por `INavigation` que permiten manipular la pila de navegación de un modo estructurado:

- [`RemovePage`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage/p/Xamarin.Forms.Page/)
- [`InsertPageBefore`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore/p/Xamarin.Forms.Page/Xamarin.Forms.Page/)
- [`PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync()/) y [ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync/p/System.Boolean/) con animación opcional

### <a name="dynamic-page-generation"></a>Generación de páginas dinámicas

El [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) ejemplo muestra la creación de una página en tiempo de ejecución en función de los proporcionados por el usuario.

## <a name="patterns-of-data-transfer"></a>Patrones de transferencia de datos

A menudo es necesario compartir datos entre páginas &mdash; para transferir datos a una página navegar y de una página devolver los datos a la página que lo invocó. Hay varias técnicas para hacerlo.

### <a name="constructor-arguments"></a>Argumentos de constructor

Cuando se desplace a una nueva página, es posible crear una instancia de la clase de página con un argumento de constructor que permite a la página para inicializarse a sí misma. El [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) ejemplo se muestra cómo hacerlo. También es posible que la página navegada a tener su `BindingContext` establecido por la página que navega a ella.

### <a name="properties-and-method-calls"></a>Propiedades y llamadas a métodos

Los ejemplos de transferencia de datos restantes exploran el problema de pasar información entre páginas cuando se desplaza una página a otra página y viceversa. En estas conversaciones, el *principal* página navega a la *información* página y debe transferir información inicializado para la *información* página. El *información* página obtiene información adicional del usuario y la información que se transfiere la *principal* página.

El *principal* página puedan acceder fácilmente a los métodos públicos y las propiedades en el *información* página tan pronto como se crea una instancia de esa página. El *información* página también puede tener acceso a métodos públicos y las propiedades en el *principal* página, pero la elección de un buen momento para esto puede ser problemático. El [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) ejemplo realiza esto su `OnDisappearing` invalidar. Una desventaja es que la *información* necesita conocer el tipo de página el *principal* página.

### <a name="messagingcenter"></a>MessagingCenter

El Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase proporciona otra manera de dos páginas para comunicarse entre sí. Los mensajes se identifican mediante una cadena de texto y pueden ir acompañados de cualquier objeto.

Un programa que desea recibir mensajes de un tipo determinado debe suscribirse a ellas mediante [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender,TArgs%7D/p/System.Object/System.String/System.Action%7BTSender,TArgs%7D/TSender/) y especificar una función de devolución de llamada. Más adelante puede cancelar la suscripción mediante una llamada a [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/). La función de devolución de llamada recibe los mensajes enviados desde el tipo especificado con el nombre especificado que se envían a través de la [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método.

El [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programa muestra cómo transferir datos mediante el centro de mensajería, pero a este método precisa que la *información* página conoce el tipo de la *doméstica* página.

### <a name="events"></a>Eventos

El evento es un enfoque consagrado para una clase enviar información a otra clase sin conocer el tipo de la clase. En el [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) ejemplo la *información* clase define un evento que activa cuando la información esté lista. Sin embargo, no hay ningún lugar conveniente para la *principal* página desconectar el controlador de eventos.

### <a name="the-app-class-intermediary"></a>El intermediario de clase de aplicación

El [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) ejemplo muestra cómo obtener acceso a propiedades definidas en el `App` clase ambos el *principal* página y la *información*página. Se trata de una buena solución, pero la sección siguiente describe algo mejor.

### <a name="switching-to-a-viewmodel"></a>Cambiar a un modelo de vista

El uso de un modelo de vista para la información que permite la *principal* página y la *información* página compartir la instancia de la clase de información. Esto se muestra en el [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) ejemplo.

### <a name="saving-and-restoring-page-state"></a>Guardar y restaurar el estado de la página

El `App` intermediario de clase o el enfoque ViewModel es ideal cuando la aplicación debe guardar información si el programa entra en modo de suspensión mientras el *información* página está activa. El [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) ejemplo se muestra cómo hacerlo.

## <a name="saving-and-restoring-the-navigation-stack"></a>Guardar y restaurar la pila de navegación

Por lo general, un programa de varias páginas que se pasa al modo de suspensión debe navegar a la misma página cuando se restaura. Esto significa que este tipo de programa debería guardar el contenido de la pila de navegación. Esta sección muestra cómo automatizar este proceso en una clase diseñada para este propósito. Esta clase también llama a las páginas individuales para que puedan guardar y restaurar su estado de página.

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define una interfaz denominada [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que las clases pueden implementar para guardar y restaurar los elementos en el `Properties`diccionario.

El [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) clase en el **Xamarin.FormsBook.Toolkit** biblioteca deriva de `Application`. A continuación, puede derivar su `App` clase `MultiPageRestorableApp` y realizar tareas de mantenimiento.

El [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) muestra el uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Un resultado similar a una aplicación de la vida real

El [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) ejemplo también hace uso de `MultiPageRestorableApp` y permite la modificación e introducción de notas que se guardan en el `Properties` diccionario.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 24 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Ejemplos de capítulo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
