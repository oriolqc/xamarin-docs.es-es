---
title: Resumen del capítulo 6. Clics de botón
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 6. Clics de botón'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334723"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumen del capítulo 6. Clics de botón

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

El [ `Button` ](xref:Xamarin.Forms.Button) es la vista que permite al usuario iniciar un comando. Un `Button` identificado por texto (y, opcionalmente, una imagen como se muestra en [capítulo 13, mapas de bits](chapter13.md)). Por lo tanto, `Button` define muchas de las mismas propiedades que `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` También define tres propiedades que controlan la apariencia de su borde, pero la compatibilidad de estas propiedades y su independencia mutua es específico de la plataforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) de tipo `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) de tipo `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) de tipo `Double`

`Button` también hereda todas las propiedades de `VisualElement` y `View`, incluidos `BackgroundColor`, `HorizontalOptions`, y `VerticalOptions`.

## <a name="processing-the-click"></a>Haga clic en de procesamiento

El `Button` clase define un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento que se desencadena cuando el usuario pulsa el `Button`. El `Click` controlador es de tipo `EventHandler`. El primer argumento es el `Button` objeto genera el evento; el segundo argumento es una `EventArgs` objeto que se proporciona ninguna información adicional.

El [ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) muestra simple `Clicked` de control.

## <a name="sharing-button-clicks"></a>Compartir botón hace clic en

Varios `Button` vistas pueden compartir el mismo `Clicked` controlador, pero el controlador generalmente se necesita para determinar qué `Button` es responsable de un evento determinado. Un enfoque consiste en almacenar los distintos `Button` objetos como campos y compruebe que está desencadenando el evento en el controlador.

El [ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) muestra esta técnica. El programa también muestra cómo establecer el [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad de un `Button` a `false` cuando se presiona el `Button` ya no es válido. Deshabilitado `Button` no genera un `Clicked` eventos.

## <a name="anonymous-event-handlers"></a>Controladores de eventos anónimos

Es posible definir `Clicked` controladores como funciones lambda anónima, como el [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) muestra. Sin embargo, no se puede compartir controladores anónimos sin algún código de reflexión desordenada.

## <a name="distinguishing-views-with-ids"></a>Distinguir las vistas con Id.

Varios `Button` también se pueden distinguir objetos estableciendo el [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propiedad o [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) propiedad a un `string`. Esta propiedad se define mediante `Element` pero no se usa dentro de Xamarin.Forms. Se está diseñado para usarse únicamente por los programas de aplicación.

El [ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) ejemplo utiliza el mismo controlador de eventos para todas las teclas numéricas 10 en un teclado numérico y distingue entre ellos con el `StyleId` propiedad:

[![Captura de pantalla de triple del teclado más sencilla](images/ch06fg04-small.png "calculadora")](images/ch06fg04-large.png#lightbox "Calculadora")

## <a name="saving-transient-data"></a>Guardar datos transitorios

Muchas aplicaciones necesitan para guardar los datos cuando se finaliza un programa y volver a cargar esos datos cuando el programa se inicie de nuevo. El [ `Application` ](xref:Xamarin.Forms.Application) clase define varios miembros que ayudan a su programa guardar y restaurar los datos transitorios:

- El [ `Properties` ](xref:Xamarin.Forms.Application.Properties) propiedad es un diccionario con `string` claves y `object` elementos. El contenido del diccionario automáticamente está guardado en almacenamiento local para la aplicación antes de finalizar el programa y vuelve a cargar cuando se inicia el programa.
- El `Application` clase define tres métodos virtuales protegidos estándar de programa `App` clase invalidaciones: [ `OnStart` ](xref:Xamarin.Forms.Application.OnStart), [ `OnSleep` ](xref:Xamarin.Forms.Application.OnSleep), y [ `OnResume` ](xref:Xamarin.Forms.Application.OnResume). Hacen referencia a *ciclo de vida de aplicación* eventos.
- El [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) método guarda el contenido del diccionario.

No es necesario llamar a `SavePropertiesAsync`. El contenido del diccionario se guarda antes de finalizar el programa automáticamente y se recuperan antes de iniciar el programa. Es útil durante las pruebas de programa para guardar los datos si el programa se bloquea.

También es útil:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), una propiedad estática que devuelve el valor actual `Application` objeto que, a continuación, puede usar para obtener el `Properties` diccionario.

El primer paso es identificar todas las variables en la página que desea conservar cuando finaliza el programa. Si conoce todos los lugares donde cambian esas variables, se puede simplemente agréguelos a la `Properties` diccionario en ese momento. En el constructor de la página, puede establecer las variables de la `Properties` si existe la clave de diccionario.

Un programa más grande probablemente tendrá que tratar con eventos de ciclo de vida de aplicación. Lo más importante es la `OnSleep` método. Una llamada a este método indica que el programa ha dejado de primer plano. Quizás el usuario ha presionado el **inicio** situado en el dispositivo, o muestra todas las aplicaciones o se está cerrando el teléfono. Una llamada a `OnSleep` es la única notificación que un programa recibe antes de finalizarse. El programa debe aprovechar esta oportunidad para asegurarse de que el `Properties` diccionario está actualizado.

Una llamada a `OnResume` indica que el programa no finalizó después de la última llamada a `OnSleep` , pero ahora se ejecuta en primer plano nuevo. El programa podría utilizar esta oportunidad para actualizar las conexiones a internet (por ejemplo).

Una llamada a `OnStart` se produce durante el inicio del programa. No es necesario esperar hasta que llame este método para tener acceso a la `Properties` diccionario porque el contenido ya se han restaurado cuando el `App` se llama al constructor.

El [ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) es muy similar al ejemplo **SimplestKeypad** , salvo que el programa usa el `OnSleep` invalidación para guardar la entrada de teclado actual, y el constructor de la página para restaurar esos datos.

> [!NOTE]
> Otro enfoque para guardar la configuración del programa proporcionado por el Xamarin.Essentials [preferencias](~/essentials/preferences.md) clase.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 6, texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Ejemplos de capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Capítulo 6 F# ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
