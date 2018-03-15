---
title: "Resumen del capítulo 23. Desencadenadores y comportamientos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: ddb76e00cfe1c19a9d31dc3e53b80a2be0697dbc
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumen del capítulo 23. Desencadenadores y comportamientos

Desencadenadores y los comportamientos son similares, ya que ambos están diseñados para usarse en archivos XAML para simplificar las interacciones de elemento más allá de la utilización de enlaces de datos y para ampliar la funcionalidad de los elementos XAML. Desencadenadores y comportamientos casi siempre se utilizan con objetos de interfaz de usuario visual.

Para admitir los desencadenadores y los comportamientos, ambos `VisualElement` y `Style` admiten dos propiedades de colección:

- [`VisualElement.Triggers`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) y [ `Style.Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Triggers/) de tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) y [ `Style.Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Behaviors/) de tipo `IList<Behavior>`

## <a name="triggers"></a>Desencadenadores

Un desencadenador es una condición (un cambio de propiedad o el desencadenamiento de un evento) da como resultado una respuesta (otro cambio de propiedad o ejecutar algún código). El `Triggers` propiedad de `VisualElement` y `Style` es de tipo `IList<TriggersBase>`. [`TriggerBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerBase/) es una clase abstracta de la que derivan cuatro clases selladas:

- [`Trigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) para las respuestas según los cambios de propiedad
- [`EventTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/) para las respuestas en función de activación de eventos
- [`DataTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) para las respuestas en función de los enlaces de datos
- [`MultiTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) para las respuestas en función de varios desencadenadores

El desencadenador siempre se establece en el elemento cuya propiedad se está cambiando el desencadenador.

### <a name="the-simplest-trigger"></a>El desencadenador más sencillo

El [ `Trigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) comprueba si hay un cambio de un valor de propiedad de clase y responde mediante la configuración de otra propiedad del mismo elemento.

`Trigger` define tres propiedades:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Property/) de tipo `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Value/) de tipo `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Setters/) de tipo `IList<SetterBase>`, la propiedad de contenido de `Trigger`

Además, `Trigger` requiere que la siguiente propiedad se hereda de `TriggerBase` establecerse:

- [`TargetType`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.TargetType/) para indicar el tipo de elemento en el que el `Trigger` está conectado

El `Property` y `Value` conforman la condición y el `Setters` colección es la respuesta. Cuando el functoid `Property` tiene el valor indicado por `Value`, la `Setter` objetos en el `Setters` se aplica la colección. Cuando el `Property` tiene un valor diferente, se quitan los establecedores. `Setter` define dos propiedades que son las mismas que las primeras dos propiedades de `Trigger`:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) de tipo `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/) de tipo `Object`

El [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) ejemplo muestra cómo un `Trigger` aplicado a un `Entry` puede aumentar el tamaño de la `Entry` a través de la `Scale` propiedad cuando la `IsFocused`propiedad de la `Entry` es `true`.

Aunque no es muy común, el `Trigger` se puede establecer en el código, como el [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) muestra.

El [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) ejemplo muestra cómo el `Trigger` se puede establecer un `Style` para aplicar a varios `Entry` elementos.

### <a name="trigger-actions-and-animations"></a>Las acciones del desencadenador y las animaciones

También es posible ejecutar un poco código basado en un desencadenador. Este código puede ser una animación que se dirige a una propiedad. Una manera común es usar un [ `EventTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/), que define dos propiedades:

- [`Event`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Event/) de tipo `string`, el nombre de un evento
- [`Actions`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Actions/) de tipo `IList<TriggerAction>`, una lista de acciones para que se ejecute en respuesta.

Para ello, tiene que escribir una clase que deriva de [ `TriggerAction<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/), generalmente `TriggerAction<VisualElement>`. Puede definir propiedades en esta clase. Estas son propiedades CLR sin formato en lugar de propiedades enlazables porque `TriggerAction` no derivan de `BindableObject`. Es necesario reemplazar el [ `Invoke` ](https://developer.xamarin.com/api/member/Xamarin.Forms.TriggerAction%3CT%3E.Invoke/p/T/) método al que se llama cuando se invoca la acción. El argumento es el elemento de destino.

El [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca es un ejemplo. Lo llama el `ScaleTo` propiedad que se va a animar el `Scale` propiedad de un elemento. Puesto que una de sus propiedades es de tipo `Easing`, el [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) clase le permite usar el estándar de `Easing` campos estáticos en XAML.

El [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) ejemplo muestra cómo invocar el `ScaleAction` de `EventTrigger` objetos que supervisa la `Focused` y `Unfocused` eventos.

El [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) ejemplo muestra cómo definir una función de aceleración personalizada para `ScaleAction` en un archivo de código subyacente.

También se pueden invocar acciones mediante una `Trigger` (distinguished desde `EventTrigger`). Esto requiere que se tenga en cuenta que `TriggerBase` define dos colecciones:

- [`EnterActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.EnterActions/) de tipo `IList<TriggerAction>`
- [`ExitActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.ExitActions/) de tipo `IList<TriggerAction>`

El [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) ejemplo muestra cómo utilizar estas colecciones.

### <a name="more-event-triggers"></a>Varios desencadenadores de eventos

El [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca llamadas `ScaleTo` dos veces para escalar vertical y horizontalmente. El [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) ejemplo utiliza en un estilo `EventTrigger` para proporcionar comentarios visuales cuando un `Button` está presionado. Esta animación doble también es posible usar dos acciones en la colección de tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

El [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) clase en el **Xamarin.FormsBook.Toolkit** biblioteca define una acción shiver personalizable. El [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) ejemplo muestra.

El [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) clase en el **Xamarin.FormsBook.Toolkit** biblioteca está restringida a `Entry` elementos y conjuntos de la `TextColor` propiedad rojas si el `Text` propiedad no es un `double`. El [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) ejemplo muestra.

### <a name="data-triggers"></a>Desencadenadores de datos

El [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) es similar a la `Trigger` salvo que en lugar de supervisión de una propiedad para los cambios de valor, supervisa un enlace de datos. Esto permite una propiedad de un elemento para modificar una propiedad de otro elemento.

`DataTrigger` define tres propiedades:

- [`Binding`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Binding/) de tipo `BindingBase`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Value/) de tipo `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Setters/) de tipo `IList<SetterBase>`

El [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) ejemplo requiere la [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca y establece los colores de los nombres de los alumnos a azul o Rosa en función de la `Sex` propiedad:

[![Captura de pantalla triple de colores de género](images/ch23fg04-small.png "sexo colores")](images/ch23fg04-large.png#lightbox "sexo colores")

El [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) conjuntos de ejemplo la `IsEnabled` propiedad de un `Entry` a `False` si la `Length` propiedad de la `Text` propiedad de la `Entry`es igual a 0. Tenga en cuenta que la `Text` propiedad se inicializa en una cadena vacía; de forma predeterminada es `null`y el `DataTrigger` no funcionaría correctamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinar condiciones en la MultiTrigger

El [ `MultiTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) es una colección de condiciones. Cuando se encuentran todas las `true`, a continuación, se aplican los establecedores. La clase define dos propiedades:

- [`Conditions`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Conditions/) de tipo `IList<Condition>`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Setters/) de tipo `IList<Setter>`

[`Condition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/) es una clase abstracta y tiene dos clases descendientes:

- [`PropertyCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/), que tiene [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Property/) y [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Value/) propiedades, como `Trigger`
- [`BindingCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingCondition/), que tiene [ `Binding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Binding/) y [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Value/) propiedades, como `DataTrigger`

En el [ **y las CONDICIONESGENERALES** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) ejemplo, un `BoxView` tiene un solo color cuando cuatro `Switch` elementos se activan.

El [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) ejemplo muestra cómo se puede hacer un `BoxView` un color cuando *cualquier* de cuatro `Switch` elementos están activados. Esto requiere una aplicación de la ley de Alemania Morgan y toda la lógica de reversión.

Combinación de AND y o lógica no es tan fácil y requiere normalmente invisible `Switch` elementos para los resultados intermedios. El [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) ejemplo muestra cómo un `Button` pueden habilitarse si el valor de dos `Entry` elementos tienen algún texto escrito, pero no si ambas tienen algún texto escrito.

## <a name="behaviors"></a>comportamientos

Todo lo que puede hacer con un desencadenador, también puede hacer con un comportamiento, pero comportamientos siempre requieren una clase que deriva de [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) e invalida los dos métodos siguientes:

- [`OnAttachedTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/T/)
- [`OnDetachingFrom`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/T/)

El argumento es el elemento que el comportamiento asociado. Por lo general, el `OnAttachedTo` método asocia algunos controladores de eventos, y `OnDetachingFrom` desasocia ellos. Como esta clase guarda normalmente algún estado, generalmente no se puede compartir en un `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) ejemplo es similar al **TriggerEntryValidation** salvo que usa un comportamiento &mdash; el [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="behaviors-with-properties"></a>Comportamientos con propiedades

`Behavior<T>` se deriva de `Behavior`, que deriva de `BindableObject`, por lo que se pueden definir propiedades enlazables en un comportamiento. Estas propiedades pueden estar activas en los enlaces de datos.

Esto se muestra en el [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) programa que hace uso de la [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) clase en el [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. `ValidEmailBehavior` tiene una propiedad enlazable de solo lectura y actúa como un origen de los enlaces de datos.

El [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) ejemplo usa este mismo comportamiento para mostrar otro tipo de indicador para indicar que una dirección de correo electrónico es válida.

El [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) ejemplo es una variación en el ejemplo anterior. ButtonGlide utiliza un `DataTrigger` en combinación con ese comportamiento.

### <a name="toggles-and-check-boxes"></a>Casillas de verificación y alterna

Es posible encapsular el comportamiento de un botón de alternancia de una clase como [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca y, a continuación, defina todos los objetos visuales para el control de alternancia completamente en XAML.

El [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) ejemplo usa el `ToggleBehavior` con un `DataTrigger` para usar un `Label` con dos cadenas de texto para el control de alternancia.

El [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) ejemplo amplía este concepto al cambiar entre dos `FormattedString` objetos.

El [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) clase en el **Xamarin.FormsBook.Toolkit** deriva biblioteca `ContentView`, define un `IsToggled` propiedad e incorpora un `ToggleBehavior` para el control de alternancia lógica. Así resulta más fácil definir el botón de alternancia en XAML, como se muestra en el [ **TranditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) ejemplo.

El [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) incluye un [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) clase que deriva de `ToggleBase` y utiliza un [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)clase para crear un botón de alternancia que se parezca a la Xamarin.Forms `Switch`.

A [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) en el **Xamarin.FormsBook.Toolkit** proporciona una animación que se utiliza para realizar un nivel animado en el [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)ejemplo.

### <a name="responding-to-taps"></a>Responde a las derivaciones

Una desventaja de `EventTrigger` es que no se puede adjuntar a un `TapGestureRecognizer` para responder a derivaciones. Eludir este problema es el propósito de [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) en el [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

El [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) ejemplo usa `TapBehavior` para usar la anterior `ShiverAction` para puntea `BoxView` elementos.

El [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) ejemplo muestra cómo reducir la cantidad del marcado encapsulando un `ShiverView` clase.

### <a name="radio-buttons"></a>Botones de radio

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca también tiene un [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) clase para hacer que los botones de radio que se agrupan por un `string` nombre del grupo.

El [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programa utiliza cadenas de texto para el botón de radio. El [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) ejemplo usa un `Style` para la diferencia de aspecto entre botones checked y unchecked. El [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) ejemplo usa las imágenes de conversión boxing de sus botones de opción:

[![Captura de pantalla triple de imágenes de Radio](images/ch23fg17-small.png "imágenes de botón de Radio")](images/ch23fg17-large.png#lightbox "imágenes de botón de Radio")

El [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) ejemplo dibuja tradicional botones de radio que aparece con un punto dentro de un círculo.

### <a name="fades-and-orientation"></a>Las fusiones y orientación

El ejemplo final, [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) le permite cambiar entre las tres vistas de selección de color diferente utilizando botones de radio. Las tres vistas fundido de entrada y salida con un [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

El programa también responde a los cambios de orientación entre vertical y horizontal usando un [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) en el **Xamarin.FormsBook.Toolkit** biblioteca.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 23 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Ejemplos de capítulo 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabajar con desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
