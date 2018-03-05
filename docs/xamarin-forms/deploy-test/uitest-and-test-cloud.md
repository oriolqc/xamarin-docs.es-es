---
title: Automatizar las pruebas de Xamarin.Forms con Xamarin.UITest y App Center
description: El componente UITest de Xamarin puede usarse con Xamarin.Forms para escribir pruebas de UI que se ejecuten en la nube en cientos de dispositivos.
ms.topic: article
ms.prod: xamarin
ms.assetid: b674db3d-c526-4e31-a9f4-b6d6528ce7a9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/31/2016
ms.openlocfilehash: 78788524c1afdda127762049018ca769926f729e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="automate-xamarinforms-testing-with-xamarinuitest-and-app-center"></a>Automatizar las pruebas de Xamarin.Forms con Xamarin.UITest y App Center

_El componente UITest de Xamarin puede usarse con Xamarin.Forms para escribir pruebas de UI que se ejecuten en la nube en cientos de dispositivos._

## <a name="overview"></a>Información general

**[App Center Test](/appcenter/test-cloud/)** permite a los desarrolladores escribir pruebas de interfaz de usuario automatizadas para aplicaciones iOS y Android. Con algunos ajustes menores, se pueden probar aplicaciones de Xamarin.Forms con Xamarin.UITest, incluido el uso compartido del mismo código de prueba. Este artículo presenta sugerencias específicas para conseguir que Xamarin.UITest funcione con Xamarin.Forms.

Esta guía supone que está familiarizado con Xamarin.UITest. Para familiarizarse con Xamarin.UITest, se recomiendan las siguientes guías:

- [Introducción a App Center Test](/appcenter/test-cloud/)
- [Introducción a UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)

Una vez que se ha agregado un proyecto de UITest a una solución de Xamarin.Forms, los pasos para escribir y ejecutar las pruebas para una aplicación de Xamarin.Forms son los mismos que para una aplicación de Xamarin.Android o Xamarin.iOS.

## <a name="requirements"></a>Requisitos

Consulte [Xamarin.UITest](/appcenter/test-cloud/uitest/) para confirmar que el proyecto está listo para las pruebas de IU automatizadas.

## <a name="adding-uitest-support-to-xamarinforms-apps"></a>Agregar compatibilidad con UITest a aplicaciones de Xamarin.Forms

UITest automatiza la interfaz de usuario activando controles en la pantalla y ejecutando entradas allí donde un usuario interactuaría normalmente con la aplicación. Para habilitar pruebas que pueden *pulsar un botón* o *escribir texto en un cuadro*, el código de prueba necesita una forma de identificar los controles de la pantalla.

Para que el código de UITest pueda hacer referencia a los controles, cada control necesita un identificador único. En Xamarin.Forms, la forma recomendada de establecer este identificador es mediante la propiedad `AutomationId`, como se muestra a continuación:

```csharp
var b = new Button {
    Text = "Click me",
    AutomationId = "MyButton"
};
var l = new Label {
    Text = "Hello, Xamarin.Forms!",
    AutomationId = "MyLabel"
};
```

La propiedad `AutomationId` también se puede establecer en XAML:

```xaml
<Button x:Name="b" AutomationId="MyButton" Text="Click me"/>
<Label x:Name="l" AutomationId="MyLabel" Text="Hello, Xamarin.Forms!" />
```

Se debe agregar un `AutomationId` único a todos los controles que sean necesarios para las pruebas (como botones, entradas de texto y etiquetas cuyo valor es posible que se necesite consultar).

> [!NOTE]
> Tenga en cuenta que se producirá una excepción `InvalidOperationException` si se trata de establecer la propiedad `AutomationId` de un elemento `Element` más de una vez.

### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

Para ejecutar pruebas en iOS, debe agregarse el [paquete Xamarin Test Cloud Agent NuGet](https://www.nuget.org/packages/Xamarin.TestCloud.Agent/) al proyecto. Una vez agregado, copie el código siguiente en el método `AppDelegate.FinishedLaunching`:

```csharp
#if ENABLE_TEST_CLOUD
// requires Xamarin Test Cloud Agent
Xamarin.Calabash.Start();
#endif
```

El ensamblado Calabash usa API de Apple no públicas, lo que hará que la App Store rechace las aplicaciones. Pero el vinculador Xamarin.iOS quitará el ensamblado Calabash del IPA final si no se le hace referencia explícitamente desde el código.

> [!NOTE]
>  Las compilaciones de versión no tienen la variable del compilador `ENABLE_TEST_CLOUD`, lo que hará que el ensamblado Calabash se quite del lote de aplicaciones. En cambio, las compilaciones de depuración tienen la directiva de compilador definida, lo que impide que el vinculador se quite del ensamblado.

La siguiente captura de pantalla muestra la variable de compilador `ENABLE_TEST_CLOUD` establecida para compilaciones de depuración:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](uitest-and-test-cloud-images/12-compiler-directive-vs.png "Opciones de compilación")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](uitest-and-test-cloud-images/11-compiler-directive-xs.png "Opciones del compilador")

-----

### <a name="android-application-project"></a>Proyecto de aplicación de Android

A diferencia de iOS, los proyectos de Android no necesitan un código de inicio especial.

## <a name="writing-uitests"></a>Escritura de UITests

Para más información sobre cómo escribir UITests, vea la [documentación de UITest](/appcenter/test-cloud/preparing-for-upload/uitest/). Los pasos siguientes son un resumen, y describen específicamente cómo se compila la [demostración de Xamarin.Forms **UsingUITest**](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/).

### <a name="use-automationid-in-the-xamarinforms-ui"></a>Usar AutomationId en la interfaz de usuario de Xamarin.Forms

Antes de poder escribir cualquier UITest, la interfaz de usuario de la aplicación de Xamarin.Forms debe permitir ejecutar scripts. Asegúrese de que todos los controles de la interfaz de usuario tienen un `AutomationId` para que se pueda hacer referencia a ellos en el código de prueba.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="adding-a-uitest-project-to-a-new-solution"></a>Agregar un proyecto de UITest a una nueva solución

Al crear un nuevo proyecto de Xamarin.Forms con Visual Studio para Mac, se puede agregar un nuevo proyecto de UITest a la solución. Para ello, seleccione **Xamarin Test Cloud: Agregar un proyecto de prueba de interfaz de usuario automatizado**:

![](uitest-and-test-cloud-images/01-new-solution-xs.png "Configure el nuevo proyecto")

La nueva solución se configurará automáticamente para ejecutar Xamarin.UITests en la aplicación de Xamarin.Forms.

-----

#### <a name="referring-to-the-automationid-in-uitests"></a>Hacer referencia a AutomationId en UITests

Al escribir UITests, el valor `AutomationId` se expone de forma distinta en cada plataforma:

- **iOS** usa el campo `id`.
- **Android** usa el campo `label`.

Para escribir UITests multiplataforma que encuentren el `AutomationId` en iOS y Android, use la consulta de prueba `Marked`:

```csharp
app.Query(c=>c.Marked("MyButton"))
```

La forma más breve `app.Query("MyButton")` también funciona.

### <a name="adding-a-uitest-project-to-an-existing-solution"></a>Agregar un proyecto de UITest a una solución existente

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio tiene una plantilla que facilita agregar un proyecto de Xamarin.UITest a una solución existente de Xamarin.Forms:

1. Haga clic con el botón derecho del ratón en la solución y seleccione **Archivo > Nuevo proyecto**.
1. Desde las plantillas **Visual C#**, seleccione la categoría **Prueba**. Seleccione la plantilla **Aplicación de prueba de interfaz de usuario > Multiplataforma**:

    ![](uitest-and-test-cloud-images/08-new-uitest-project-vs.png "Agregue nuevo proyecto")

    Esto agregará un nuevo proyecto con los paquetes de NuGet **NUnit**, **Xamarin.UITest** y **NUnitTestAdapter** a la solución:

    ![](uitest-and-test-cloud-images/09-new-uitest-project-xs.png "Administrador de paquetes NuGet")

    **NUnitTestAdapter** es un ejecutor de pruebas de terceros que permite a Visual Studio ejecutar pruebas NUnit desde Visual Studio.

    El nuevo proyecto también tiene dos clases. **AppInitializer** contiene código para ayudar a inicializar y configurar pruebas. La otra clase, **Tests**, contiene código reutilizable para ayudar a iniciar la UITest.

1. Agregue una referencia de proyecto desde el proyecto de UITest al proyecto de Xamarin.Android:

    ![](uitest-and-test-cloud-images/10-test-apps-vs.png "Administrador de referencias de proyecto")

    Esto permitirá a **NUnitTestAdapter** ejecutar las UITests para la aplicación Android desde Visual Studio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Es posible agregar manualmente un nuevo proyecto de Xamarin.UITest a una solución existente:

1. Empiece agregando un nuevo proyecto. Para ello, seleccione la solución y haga clic en **Archivo > Agregar nuevo proyecto**. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Multiplataforma > Pruebas > Xamarin Test Cloud > Aplicación de prueba de interfaz de usuario**:

    ![](uitest-and-test-cloud-images/02-new-uitest-project-xs.png "Elija una plantilla")

    Esto agregará un nuevo proyecto que ya tiene los paquetes de NuGet **NUnit** y **Xamarin.UITest** en la solución:

    ![](uitest-and-test-cloud-images/03-new-uitest-project-xs.png "Paquetes NuGet de Xamarin UITest")

    El nuevo proyecto también tiene dos clases. **AppInitializer** contiene código para ayudar a inicializar y configurar pruebas. La otra clase, **Tests**, contiene código reutilizable para ayudar a iniciar la UITest.

1. Seleccione **Vista > Paneles > Pruebas unitarias** para mostrar el panel de pruebas unitarias. Expanda **UsingUITest > UsingUITest.UITests > Aplicaciones de prueba**:

    ![](uitest-and-test-cloud-images/04-unit-test-pad-xs.png "Panel Prueba unitaria")

1. Haga clic con el botón derecho en **Aplicaciones de prueba**, haga clic en **Agregar proyecto de aplicación** y seleccione proyectos de iOS y Android en el cuadro de diálogo que aparece:

    ![](uitest-and-test-cloud-images/05-add-test-apps-xs.png "Cuadro de diálogo Aplicaciones de prueba")

    El panel **Prueba unitaria** ahora debería tener una referencia a los proyectos de iOS y Android. Esto permitirá que el ejecutor de pruebas de Visual Studio para Mac ejecute UITests localmente en los dos proyectos de Xamarin.Forms.

#### <a name="adding-uitest-to-the-ios-app"></a>Agregar UITest a la aplicación de iOS

Hay algunos cambios adicionales que deben realizarse en la aplicación de iOS antes de que funcione Xamarin.UITest:

1. Agregue el paquete NuGet **Xamarin Test Cloud Agent**. Haga clic con el botón derecho en **Paquetes**, seleccione **Agregar paquetes**, busque **Xamarin Test Cloud Agent** en NuGet y agréguelo al proyecto de Xamarin.iOS:

    ![](uitest-and-test-cloud-images/07-add-test-cloud-agent-xs.png "Agregue paquetes NuGet")

1. Edite el método `FinishedLaunching` de la clase **AppDelegate** para inicializar Xamarin Test Cloud Agent cuando se inicie la aplicación de iOS y para establecer la propiedad `AutomationId` de las vistas. El método `FinishedLaunching` debería ser similar al del ejemplo de código siguiente:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
        #if ENABLE_TEST_CLOUD
        Xamarin.Calabash.Start();
        #endif

        global::Xamarin.Forms.Forms.Init();

        LoadApplication(new App());

        return base.FinishedLaunching(app, options);
}
```

-----

Después de agregar Xamarin.UITest a la solución de Xamarin.Forms, es posible crear UITests, ejecutarlas localmente y enviarlas a Xamarin Test Cloud.

## <a name="summary"></a>Resumen

Las aplicaciones de Xamarin.Forms se pueden probar fácilmente con **Xamarin.UITest** usando un mecanismo sencillo para exponer el `AutomationId` como un identificador de vista único para la automatización de pruebas. Una vez que se ha agregado un proyecto de UITest a una solución de Xamarin.Forms, los pasos para escribir y ejecutar las pruebas para una aplicación de Xamarin.Forms son los mismos que para una aplicación de Xamarin.Android o Xamarin.iOS.

Para más información sobre cómo enviar pruebas a App Center Test, vea [Submitting UITests (Envío de UITests)](/appcenter/test-cloud/preparing-for-upload/uitest/). Para más información sobre UITest, vea la [documentación de App Center Test](/appcenter/test-cloud/).


## <a name="related-links"></a>Vínculos relacionados

- [UITestSample](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
- [App Center Test](/appcenter/test-cloud/)
- [Xamarin.UITest](/appcenter/test-cloud/uitest/)
- [NUnit](http://www.nunit.org)
