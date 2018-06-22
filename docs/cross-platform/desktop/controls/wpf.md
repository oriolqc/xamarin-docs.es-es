---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'Frente a WPF Xamarin.Forms: Similitudes y diferencias'
description: Este documento se compara y contrasta WPF a Xamarin.Forms. Se trata de plantillas de control, XAML, infraestructura de enlaces, plantillas de datos, ItemsControl, UserControl, exploración y navegación a direcciones URL.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 232ddd5ea06891c70125adc9b8bf77f3e857dbd6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781974"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>Frente a WPF Xamarin.Forms: Similitudes y diferencias

## <a name="control-templates"></a>Plantillas de control

WPF admite el concepto de *plantillas de Control* que proporcionan las instrucciones de visualización para un control (`Button`, `ListBox`, etcetera.). Como se mencionó anteriormente, Xamarin.Forms utiliza hormigón _representación_ clases para este que interactúan con la plataforma nativa (iOS, Android, etc.) para visualizar el control.

Sin embargo, Xamarin.Forms _does_ tiene un `ControlTemplate` tipo: se usa para la creación de temas `Page` objetos. Proporciona una definición para un `Page` que proporciona coherencia del contenido, pero permite al usuario de la página Cambiar colores, fuentes, etc. e incluso agregar elementos para que sea único para la aplicación.

Usos comunes para esto son cosas como cuadros de diálogo de autenticación, mensajes y para proporcionar una apariencia y funcionamiento página normalizado, pero definibles mediante temas que se pueden personalizar dentro de la aplicación. Como parte de este soporte, se utilizan muchos controles de WPF con el nombre conocidos:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Pero es importante saber que estos están _no_ que sirve al mismo propósito de Xamarin.Forms. Para obtener más información sobre esta característica, visite la [página de documentación de](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML se usa como el lenguaje de marcado declarativo para WPF y Xamarin.Forms. En su mayor parte, la sintaxis es idéntica: la principal diferencia es que los objetos que se definen/crean los gráficos de XAML.

- Xamarin.Forms es compatible con la [especificación de XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); así resulta más fácil definir los datos como `string`s, `int`s, etc., así como definir los tipos genéricos y pasar argumentos a los constructores.

- Actualmente no hay ninguna manera de cargar dinámicamente XAML como WPF se puede hacer con `XamlReader`. Puede obtener la misma funcionalidad básica con un [paquete NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) aunque.

### <a name="markup-extensions"></a>Extensiones de marcado

Xamarin.Forms admite la extensión XAML a través de las extensiones de marcado, como WPF. De fábrica, tiene los mismos bloques de creación básicos:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Además, incluye `{x:Reference}` de la especificación de XAML 2009 y un `{TemplateBinding}` extensión de marcado que se utiliza para la versión especializada de `ControlTemplate` compatibles con Xamarin.Forms.

> [!WARNING]
> El `ControlTemplate` soporte técnico no es lo mismo - aunque tenga el mismo nombre.

Xamarin.Forms es compatible con las extensiones de marcado personalizado también - pero la implementación es ligeramente diferente. En WPF, debe derivar de `MarkupExtension` -una clase base abstracta. En Xamarin.Forms, que se reemplaza por una interfaz `IMarkupExtension` o `IMarkupExtension<T>` que es más flexible.

Al igual que WPF, es el único método requiere un `ProvideValue` método para devolver el valor de la extensión de marcado.

## <a name="binding-infrastructure"></a>Infraestructura de enlaces

Uno de los conceptos principales que se realiza a través de es una infraestructura de enlace de datos para conectarse propiedades visuales a las propiedades de datos. NET. Esto permite que los patrones arquitectónicos como MVVM. El diseño básico es idéntico, ya que tener una clase base enlazable [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), en WPF es el [DependencyObject](https://msdn.microsoft.com/en-us/library/system.windows.dependencyobject(v=vs.110).aspx) clase. Esta clase base se utiliza como el antecesor de raíz para todos los objetos que vaya a participar como destinos de enlace de datos. Las clases derivadas, exponen [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) objetos que actúan como el almacenamiento de respaldo para los valores de propiedad (se definen como [DependencyProperty](https://msdn.microsoft.com/library/system.windows.dependencyproperty(v=vs.110).aspx) objetos de WPF).

### <a name="defining-bindable-properties"></a>Definir propiedades enlazables

La definición de una propiedad enlazable de Xamarin.Forms es igual que WPF:
1. El objeto debe derivarse de `BindableObject`.
2. Debe haber un campo estático público del tipo `BindableProperty` declarado para definir la clave de almacenamiento de respaldo para la propiedad.
3. Debe haber un contenedor de propiedad de instancia pública que usa `GetValue` y `SetValue` para recuperar y cambiar el valor de propiedades.

Para obtener un ejemplo completo, vea [propiedades enlazables en Xamarin.Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propiedades adjuntas

Las propiedades adjuntas son un subconjunto de la propiedad enlazable y funcionan del mismo modo que lo hacen en WPF. La principal diferencia es que el contenedor de propiedad se omite en este caso y se reemplaza con un conjunto de métodos get/set estático en la clase propietaria. Vea [adjunta propiedades de Xamarin.Forms](~/xamarin-forms/xaml/attached-properties.md) para obtener más información.

### <a name="using-the-binding-engine"></a>Usar el motor de enlace

El proceso para utilizar el motor de enlace es el mismo como en WPF. Puede utilizarse en el código subyacente mediante la creación de un `Binding` objeto asociado a un objeto de origen (cualquier tipo. NET) y un valor de propiedad opcional (Si omite, trata el objeto de origen como la propiedad en Sí: al igual que WPF). A continuación, puede usar `SetBinding` en cualquier `BindableObject` para asociar el enlace a un `BindableProperty`.

Como alternativa, puede definir la relación de enlace en XAML con el `BindingExtension`. Tiene los mismos valores básicos como la extensión en WPF.

La compatibilidad de enlace y el motor son más similares a la implementación de Silverlight que WPF. Hay varias características que faltan que no se implementaron en Xamarin.Forms:

- No hay compatibilidad para las características siguientes de enlaces:
    - BindingGroupName
    - BindsDirectlyToSource
    - IsAsync
    - MultiBinding
    - NotifyOnSourceUpdated
    - NotifyOnTargetUpdated
    - NotifyOnValidationError
    - UpdateSourceTrigger
    - UpdateSourceExceptionFilter
    - ValidatesOnDataErrors
    - ValidatesOnExceptions
    - Colección de ValidationRules
    - XPath
    - XmlNamespaceManager
- `Binding.Mode` no admite `OneTime`, en su lugar, use `OneWay`.

#### <a name="relativesource"></a>RelativeSource

No hay compatibilidad para `RelativeSource` enlaces. En WPF, permiten enlazar a otros elementos visuales que se define en XAML. En Xamarin.Forms, esta misma capacidad puede lograrse mediante el `{x:Reference}` extensión de marcado. Por ejemplo, suponiendo que tenemos un control con el nombre "otherControl" que tiene una propiedad de texto, podemos enlazar a él como el siguiente:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

Puede utilizarse la misma capacidad para el `{RelativeSource Self}` característica. Sin embargo no hay compatibilidad para la localización de antecesores por tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contexto de enlace

En WPF, puede definir un `DataContext` valor de propiedad que reprents el origen de enlace predeterminado. Si no se define el origen para un enlace, se utiliza este valor de propiedad. El valor se hereda a lo largo del árbol visual, lo que le permite definir en un nivel superior y, a continuación, se usa por grupos secundarios.

En Xamarin.Forms, esta misma función está disponible, pero el nombre de propiedad es `BindingContext`.

#### <a name="value-converters"></a>Convertidores de valores

Convertidores de valores son totalmente compatibles con Xamarin.Forms - igual que WPF. Se utiliza la misma forma de interfaz, pero Xamarin.Forms tiene la interfaz definida en el `Xamarin.Forms` espacio de nombres.

### <a name="model-view-viewmodel"></a>Model-View-ViewMode

MVVM es totalmente compatible con WPF y Xamarin.Forms.

WPF incluye integrada en `RoutedCommand` que se usa a veces; Xamarin.Forms no tiene comandos soporte integrado más allá de la `ICommand` definición de la interfaz. Puede incluir una variedad de marcos MVVM para agregar las clases base necesarias para implementar MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged y INotifyCollectionChanged

Ambas interfaces son totalmente compatibles con enlaces de Xamarin.Forms. A diferencia de muchos marcos basadas en XAML, las notificaciones de cambio de propiedad se pueden generar en subprocesos en segundo plano de Xamarin.Forms (al igual que WPF) y el motor de enlace correctamente pasará al subproceso de interfaz de usuario.

Además, admiten ambos entornos `SynchronziationContext` y `async` / `await` para realizar el cálculo de referencias de subproceso adecuado. WPF incluye el `Dispatcher` clase en todos los elementos visuales, Xamarin.Forms tiene un método estático `Device.BeginInvokeOnMainThread` que también se puede utilizar (aunque `SynchronizationContext` es preferible para codificar multiplataforma).

- Xamarin.Forms incluye un `ObservableCollection<T>` que es compatible con las notificaciones de cambio de colección.
- Puede usar `BindingBase.EnableCollectionSynchronization` para habilitar las actualizaciones entre subprocesos para una colección. La API es ligeramente diferente de la variación de WPF, [comprobar los documentos para obtener detalles de uso](https://developer.xamarin.com/api/member/Xamarin.Forms.BindingBase.EnableCollectionSynchronization/).

## <a name="data-templates"></a>Plantillas de datos

Se admiten plantillas de datos de Xamarin.Forms para personalizar la representación de un `ListView` fila (celda). A diferencia de WPF que se puede usar `DataTemplate`para ver si cualquier control orientada a servicios de contenido, Xamarin.Forms actualmente solo usa para `ListView`. La definición de plantilla pueden definir en línea (asignado a la `ItemTemplate` propiedad), o como un recurso en un `ResourceDictionary`.

Además, no son bastante flexibles como su equivalente WPF.

1. El elemento raíz de la `DataTemplate` debe _siempre_ ser un `ViewCell` objeto.
2. Desencadenadores de datos son totalmente compatibles con una plantilla de datos, pero debe incluir un `DataType` propiedad que indica el tipo de la propiedad que está asociado el desencadenador.
3. `DataTemplateSelector` También es compatible, pero se deriva de `DataTemplate` y es, por tanto, solo asignados directamente a la `ItemTemplate` propiedad (frente a `ItemTemplateSelector` en WPF).

## <a name="itemscontrol"></a>ItemsControl

No hay ningún equivelent integrado a un `ItemsControl` de Xamarin.Forms; pero no hay un [uno personalizado para Xamarin.Forms disponible aquí](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controles de usuario

En WPF, `UserControl`se utilizan para proporcionar una sección de la interfaz de usuario que está asociado el comportamiento. En Xamarin.Forms, usamos el `ContentView` para el mismo propósito. Ambos admiten un enlace y su inclusión en XAML.

## <a name="navigation"></a>Navegación

WPF incluye un poco usados `NavigationService` que se puede utilizar para proporcionar una característica de navegación de "explorador-like". Mayoría de las aplicaciones no se interponga a este sin embargo y en su lugar utilizan diferentes `Window` elementos o diferentes secciones de la ventana para mostrar los datos.

En los dispositivos de teléfono, diferentes _pantallas_ son a menudo la solución y por lo que Xamarin.Forms incluye compatibilidad con varias formas de exploración:

| Estilo de navegación | Tipo de página. |
|--- |--- |
|Basada en pilas (inserción/extracción)|NavigationPage|
|Maestro y detalles|MasterDetailPage|
|Tabulaciones|TabbedPage|
|Deslice el dedo izquierda/derecha|CarouselView|

El `NavigationPage` es el enfoque más común, y cada página tiene un `Navigation` propiedad que se puede usar para insertar o extraer páginas activar y desactivar la pila de navegación. Se trata de la equivelent más cercano a la `NavigationService` se encuentra en WPF.

### <a name="url-navigation"></a>Navegación a direcciones URL

WPF es una tecnología orientada a servicios de escritorio y puede aceptar parámetros de línea de comandos para dirigir el comportamiento de inicio. Puede usar Xamarin.Forms [vinculación de URL profunda](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) para saltar a una página de inicio.
