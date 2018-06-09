---
title: Resumen de capítulo 18. MVVM
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 4a9e8221828ddd49c10dc4f14dc996acc167ae2f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240436"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumen de capítulo 18. MVVM

Una de las mejores formas de diseñar una aplicación consiste en separar la interfaz de usuario desde el código subyacente, que a veces se denomina la *lógica de negocios*. Hay varias técnicas, pero que se adapta para entornos basados en XAML se conoce como Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Relaciones mutuas MVVM

Una aplicación de MVVM consta de tres capas:

- El modelo proporciona los datos subyacentes, a veces a través de archivos o tiene acceso web
- La vista es la capa de presentación o de la interfaz de usuario, que generalmente se implementa en XAML
- El modelo de vista conecta el modelo y la vista

El modelo está escrito en el modelo de vista y el modelo de vista está escrito en la vista. Estas tres capas suelen conectan entre sí mediante los mecanismos siguientes:

![Vista, ViewModel y vista](images/ch18fg03.png "MVVM")

En muchos programas más pequeños (y otros incluso mayores), a menudo el modelo está ausente o su funcionalidad está integrada en el modelo de vista.

## <a name="viewmodels-and-data-binding"></a>ViewModels y enlace de datos

Para participar en enlaces de datos, un modelo de vista debe ser capaz de notificar a la vista cuando ha cambiado una propiedad del modelo de vista. El modelo de vista hace implementando el [ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) de la interfaz en el `System.ComponentModel` espacio de nombres. Esto forma parte de .NET en lugar de Xamarin.Forms. (Generalmente ViewModels intenta mantener la independencia de la plataforma.)

El `INotifyPropertyChanged` interfaz declara un evento único denominado [ `PropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) que indica la propiedad que ha cambiado.

### <a name="a-viewmodel-clock"></a>Un reloj ViewModel

El [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca define una propiedad de tipo `DateTime` que los cambios se basan en un temporizador. La clase implementa `INotifyPropertyChanged` y se activa la `PropertyChanged` evento cada vez que la `DateTime` cambios de propiedad.

El [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) ejemplo crea una instancia de este modelo de vista y usa enlaces de datos para el modelo de vista para mostrar la información de hora y fecha de actualización.

### <a name="interactive-properties-in-a-viewmodel"></a>Propiedades interactivos en un modelo de vista

Las propiedades en un modelo de vista pueden ser más interactivas, como se muestra en el [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) (clase), que forma parte de la [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) ejemplo. Los enlaces de datos proporcionan valores multiplicando y multiplicador de dos `Slider` elementos y mostrar el producto con un `Label`. Sin embargo, puede realizar una amplia cambios a esta interfaz de usuario de XAML sin cambios en el modelo de vista o en el archivo de código subyacente.

### <a name="a-color-viewmodel"></a>Un modelo de vista de Color

El [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca integra los modelos de color RGB y HSL. Se muestra en el [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) ejemplo:

[![Captura de pantalla triple de TK](images/ch18fg08-small.png "HSL Color modelo")](images/ch18fg08-large.png#lightbox "HSL modelo de Color")

### <a name="streamlining-the-viewmodel"></a>Optimizar el modelo de vista

Es posible simplificar el código de ViewModels será definiendo un `OnPropertyChanged` método mediante el [ `CallerMemberName` ](https://developer.xamarin.com/api/type/System.Runtime.CompilerServices.CallerMemberNameAttribute/) atributo, que obtiene automáticamente el nombre de propiedad que realiza la llamada. El [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca hace esto y proporciona una clase base para ViewModels.

## <a name="the-command-interface"></a>La interfaz de comandos

MVVM funciona con enlaces de datos y enlaces de datos trabajar con las propiedades, por lo que parece ser deficientes en cuanto a control MVVM un `Clicked` eventos de un `Button` o un `Tapped` eventos de un `TapGestureRecognizer`. Para permitir que ViewModels controlar estos eventos, es compatible con Xamarin.Forms el *interfaz de comandos*.

La interfaz de comandos se manifiesta en la `Button` con dos propiedades públicas:

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) de tipo [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) (definido en el `System.Windows.Input` espacio de nombres)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) de tipo `Object`

Para admitir la interfaz de comandos, un modelo de vista debe definir una propiedad de tipo `ICommand` es decir, a continuación, datos enlazados a la `Command` propiedad de la `Button`. El `ICommand` interfaz declara dos métodos y un evento:

- Un [ `Execute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.Execute/p/System.Object/) método con un argumento de tipo `object`
- A [ `CanExecute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.CanExecute/p/System.Object/) método con un argumento de tipo `object` que devuelve `bool`
- A [ `CanExecuteChanged` ](https://developer.xamarin.com/api/event/System.Windows.Input.ICommand.CanExecuteChanged/) eventos

Internamente, un ViewModel establece cada propiedad de tipo `ICommand` a una instancia de una clase que implementa el `ICommand` interfaz. A través del enlace de datos, el `Button` llama inicialmente el `CanExecute` método y se deshabilita si el método devuelve `false`. También establece un controlador para el `CanExecuteChanged` eventos y llamadas `CanExecute` cada vez que se desencadena. Si el `Button` es habilitado, llama a la `Execute` método cada vez que la `Button` se hace clic en.

Es posible que tenga algunos ViewModels anteriores a Xamarin.Forms y ya estos podrían admitir la interfaz de comandos. Nueva ViewModels destinadas a usarse solo con Xamarin.Forms, Xamarin.Forms proporciona un [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) clase y un [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) de clases que implementan la `ICommand` interfaz. El tipo genérico es el tipo de argumento para el `Execute` y `CanExecute` métodos.

### <a name="simple-method-executions"></a>Ejecuciones de método simple

El [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) ejemplo muestra cómo usar la interfaz de comandos en un modelo de vista. El [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) clase define dos propiedades de tipo `ICommand` y también define dos propiedades privadas que se pasa a la más sencilla de [ `Command` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/). El programa contiene enlaces de datos de este modelo de vista para la `Command` propiedades de dos `Button` elementos.

El `Button` poder reemplazar fácilmente elementos con `TapGestureRecognizer` objetos en XAML sin realizar ningún cambio en el código.

### <a name="a-calculator-almost"></a>Una calculadora casi

El [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) ejemplo hace uso de ambos el `Execute` y `CanExecute` métodos de `ICommand`. Usa un [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) biblioteca. El modelo de vista contiene seis propiedades de tipo `ICommand`. Estos se inicializan desde la [ `Command` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/) y [ `Command` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/) de `Command` y [ `Command<T>` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/) de `Command<T>`. Las teclas numéricas de la máquina de sumar se enlazan a la propiedad que se inicializa con `Command<T>`y un `string` argumento pasado a `Execute` y `CanExecute` identifica la clave determinada.

## <a name="viewmodels-and-the-application-lifecycle"></a>El ciclo de vida de la aplicación y ViewModels

El `AdderViewModel` utilizados en el **AddingMachine** ejemplo también define dos métodos denominados `SaveState` y `RestoreState`. Se llaman a estos métodos de la aplicación cuando pasa al modo de suspensión y cuando se inicie de nuevo.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 18 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Ejemplos de capítulo 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
