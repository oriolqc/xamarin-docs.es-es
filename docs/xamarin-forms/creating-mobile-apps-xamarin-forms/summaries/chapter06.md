---
title: Resumen del capítulo 6. Clics de botón
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 6. Clics de botón'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9c8a4e6a6c4d62722e72ff3b27251987aa201284
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240754"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumen del capítulo 6. Clics de botón

El [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) es la vista que permite al usuario iniciar un comando. A `Button` se identifican con (y, opcionalmente, una imagen como se muestra en [capítulo 13, mapas de bits](chapter13.md)). Por lo tanto, `Button` define muchas de las mismas propiedades que `Label`:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/)
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontFamily/)
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/)
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontAttributes/)
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)

`Button` También define tres propiedades que controlan la apariencia de su borde, pero la compatibilidad de estas propiedades y su independencia mutua es específico de la plataforma:

- [`BorderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderColor/) de tipo `Color`
- [`BorderWidth`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderWidth/) de tipo `Double`
- [`BorderRadius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/) de tipo `Double`

`Button` también hereda todas las propiedades de `VisualElement` y `View`, incluido `BackgroundColor`, `HorizontalOptions`, y `VerticalOptions`.

## <a name="processing-the-click"></a>Haga clic en de procesamiento

El `Button` clase define un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) evento que se desencadena cuando el usuario puntea el `Button`. El `Click` controlador es de tipo `EventHandler`. El primer argumento es el `Button` objeto genera el evento; el segundo argumento es una `EventArgs` objeto que se proporciona ninguna información adicional.

El [ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) muestra simple `Clicked` control.

## <a name="sharing-button-clicks"></a>Uso compartido de botón hace clic en

Varios `Button` vistas pueden compartir el mismo `Clicked` controlador, pero el controlador generalmente es necesario determinar qué `Button` es responsable de un evento determinado. Un enfoque consiste en almacenar los distintos `Button` objetos como campos y compruebe que está desencadenando el evento en el controlador.

El [ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) ejemplo muestra esta técnica. El programa también muestra cómo establecer el [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) propiedad de un `Button` a `false` cuando se presiona el `Button` ya no es válido. A deshabilitado `Button` no genera un `Clicked` eventos.

## <a name="anonymous-event-handlers"></a>Controladores de eventos anónimos

Es posible definir `Clicked` controladores como funciones lambda anónima, como la [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) muestra. Sin embargo, no se puede compartir controladores anónimos sin algún código de reflexión sin optimizar.

## <a name="distinguishing-views-with-ids"></a>Distinción vistas con Id.

Varios `Button` objetos también se pueden distinguir estableciendo la [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) propiedad o [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/) propiedad a un `string`. Esta propiedad se define mediante `Element` pero no se usa dentro de Xamarin.Forms. Está pensado para usarse únicamente por los programas de aplicación.

El [ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) ejemplo usa el mismo controlador de eventos para todas las claves de número 10 en un teclado numérico y distingue entre ellos con el `StyleId` propiedad:

[![Captura de pantalla triple de teclado más sencilla](images/ch06fg04-small.png "calculadora")](images/ch06fg04-large.png#lightbox "Calculadora")

## <a name="saving-transient-data"></a>Guardar datos transitorios

Muchas aplicaciones necesitan que se va a guardar los datos cuando se finaliza un programa como volver a cargar esos datos cuando se inicia el programa de nuevo. El [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) clase define varios miembros que le ayudan a su programa guardar y restaurar datos transitorios:

- El [ `Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/) propiedad es un diccionario con `string` claves y `object` elementos. El contenido del diccionario automáticamente se guarda en el almacenamiento local de aplicación antes de la finalización del programa y volver a cargar cuando se inicia el programa.
- El `Application` clase define tres métodos virtuales protegidos estándar de programa `App` invalidaciones de clase: [ `OnStart` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnStart()/), [ `OnSleep` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnSleep()/), y [ `OnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnResume()/). Se refieren a *ciclo de vida de aplicación* eventos.
- El [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) método guarda el contenido del diccionario.

No es necesario llamar a `SavePropertiesAsync`. El contenido del diccionario automáticamente se guarda antes de la finalización del programa y recuperar antes de iniciar el programa. Resulta útil durante las pruebas de programa para guardar los datos si se bloquea el programa.

También es útil:

- [`Application.Current`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Current/), una propiedad estática que devuelve el valor actual `Application` objeto que, a continuación, puede usar para obtener el `Properties` diccionario.

El primer paso es identificar todas las variables en la página que desea conservar cuando finaliza el programa. Si conoce todos los lugares donde cambian esas variables, se puede simplemente agréguelos a la `Properties` diccionario en ese momento. En el constructor de la página, puede establecer las variables de la `Properties` diccionario si la clave existe.

Un programa más amplio probablemente tendrá que tratar con eventos de ciclo de vida de la aplicación. El más importante es la `OnSleep` método. Una llamada a este método indica que el programa ha abandonado al primer plano. Quizás el usuario presionó la **inicio** situado en el dispositivo, o muestra todas las aplicaciones o se va a cerrar el teléfono. Una llamada a `OnSleep` es la única notificación que un programa recibe antes de finalizarse. El programa debe aprovechar esta oportunidad para asegurarse de que el `Properties` diccionario está actualizado.

Una llamada a `OnResume` indica que el programa no ha terminado de después de la última llamada a `OnSleep` , pero ahora se ejecuta en primer plano nuevo. El programa podría utilizar esta oportunidad para actualizar las conexiones de internet (por ejemplo).

Una llamada a `OnStart` se produce durante el inicio del programa. No es necesario esperar hasta que llama este método para tener acceso a la `Properties` diccionario porque el contenido ya se han restaurado cuando el `App` se llama al constructor.

El [ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) es muy similar al ejemplo **SimplestKeypad** excepto en que el programa usa el `OnSleep` invalidación para guardar la entrada de teclado actual, y el constructor de la página para restaurar esos datos.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 6 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Capítulo 6 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Ejemplos de capítulo 6 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
