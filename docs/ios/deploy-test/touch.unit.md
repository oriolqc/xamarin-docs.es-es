---
title: Pruebas unitarias
ms.topic: article
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: f7c743bab2a6acb3dcd57ebca207957f983e0c0f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="unit-testing"></a>Pruebas unitarias

En este documento se describe cómo se crean pruebas unitarias para los proyectos de Xamarin.iOS.
Las pruebas unitarias con Xamarin.iOS se realizan mediante el marco Touch.Unit, que incluye tanto un ejecutor de pruebas de iOS como una versión modificada de NUnit denominada [Touch.Unit](https://github.com/xamarin/Touch.Unit), que proporciona un conjunto conocido de API para escribir pruebas unitarias.

## <a name="setting-up-a-test-project"></a>Configuración de un proyecto de pruebas

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para configurar un marco de pruebas unitarias para el proyecto, lo único que tiene que hacer es agregar a la solución un proyecto de tipo **proyecto de pruebas unitarias de iOS**. Para ello, haga clic con el botón derecho en la solución y seleccione **Agregar > Agregar nuevo proyecto**. En la lista, seleccione **iOS > Pruebas > Unified API > iOS Unit Tests Project (Proyecto de pruebas unitarias de iOS)** (puede elegir C# o F#).

![](touch.unit-images/00.png "Elegir C# o F#")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para configurar un marco de pruebas unitarias para el proyecto, lo único que tiene que hacer es agregar a la solución un proyecto de tipo **proyecto de pruebas unitarias de iOS**. Para ello, haga clic con el botón derecho en la solución y seleccione **Agregar > Nuevo proyecto...** En la lista, seleccione **Visual C# > iOS > Unit Test App (iOS) (Aplicación de pruebas unitarias (iOS))**.

![](touch.unit-images/00a.png "Aplicación de prueba unitaria (iOS)")

-----

Con los pasos anteriores se crea un proyecto básico que contiene un programa básico ejecutor y que hace referencia al nuevo ensamblado MonoTouch.NUnitLite; el proyecto tendrá este aspecto:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](touch.unit-images/01.png "El proyecto en el Explorador de soluciones")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](touch.unit-images/01a.png "El proyecto en el Explorador de soluciones")

-----

La clase `AppDelegate.cs` contiene el ejecutor de pruebas y tiene el siguiente aspecto:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
        UIWindow window;
        TouchRunner runner;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
                // create a new window instance based on the screen size
                window = new UIWindow (UIScreen.MainScreen.Bounds);
                runner = new TouchRunner (window);

                // register every tests included in the main application/assembly
                runner.Add (System.Reflection.Assembly.GetExecutingAssembly ());

                window.RootViewController = new UINavigationController (runner.GetViewController ());

                // make the window visible
                window.MakeKeyAndVisible ();

                return true;
        }
}
```

## <a name="writing-some-tests"></a>Escritura de pruebas

Ahora que tiene el shell básico, debería escribir el primer conjunto de pruebas.

Las pruebas se escriben mediante la creación de clases que tienen aplicado el atributo `[TestFixture]`. Dentro de cada clase TestFixture, debe aplicar el atributo `[Test]` a cada método al que quiera que invoque el ejecutor de pruebas. Los accesorios de prueba reales pueden encontrarse en cualquier archivo del proyecto de pruebas.

Para empezar a trabajar rápidamente, seleccione **Agregar/Agregar nuevo archivo** y, luego, seleccione el grupo de Xamarin.iOS **UnitTests**. Esto agregará un archivo de esquema con una prueba correcta, otra incorrecta y una omitida, con el siguiente aspecto:

```csharp
using System;
using NUnit.Framework;

namespace Fixtures {

        [TestFixture]
        public class Tests {

                [Test]
                public void Pass ()
                {
                        Assert.True (true);
                }

                [Test]
                public void Fail ()
                {
                        Assert.False (true);
                }

                [Test]
                [Ignore ("another time")]
                public void Ignore ()
                {
                        Assert.True (false);
                }
        }
}
```

## <a name="running-your-tests"></a>Ejecución de las pruebas

Para ejecutar este proyecto en la solución, haga clic en él con el botón derecho y seleccione **Debug Item (Depurar elemento)** o **Ejecutar elemento**.

El ejecutor de pruebas permite ver qué pruebas se registran y seleccionar individualmente las pruebas que se pueden ejecutar.

[![](touch.unit-images/02.png "Lista de pruebas registradas")](touch.unit-images/02.png#lightbox) 

[![](touch.unit-images/03.png "Texto individual")](touch.unit-images/03.png#lightbox) 

[![](touch.unit-images/04.png "Resultados de ejecución")](touch.unit-images/04.png#lightbox)

Puede ejecutar accesorios de prueba individuales si selecciona el accesorio de texto en las vistas anidadas o puede ejecutar todas las pruebas con "Run Everything (Ejecutar todo)". Si ejecuta la prueba predeterminada que se supone que incluye una prueba correcta, una incorrecta y una omitida. Este es el aspecto del informe, y puede ir directamente a las pruebas incorrectas y obtener más información sobre el error:

[![](touch.unit-images/05.png "Informe de ejemplo")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/05.png "Informe de ejemplo")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/05.png "Informe de ejemplo")](touch.unit-images/05.png#lightbox)

También puede ir a la ventana Application Output (Resultado de aplicación) del IDE para ver qué pruebas se ejecutan y su estado actual.

## <a name="writing-new-tests"></a>Escritura de nuevas pruebas

NUnitLite es una versión modificada del proyecto NUnit denominado [Touch.Unit](https://github.com/xamarin/Touch.Unit). Es un marco de pruebas ligero para .NET basado en las ideas de [NUnit](http://nunit.com/) que proporciona un subconjunto de sus características.
Usa recursos mínimos y se ejecuta en plataformas de recursos restringidos como las usadas en el desarrollo móvil e incrustado. Puede [examinar la API de NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/) disponible en Xamarin.iOS. Con el esquema básico que proporciona la plantilla de pruebas unitarias, el punto de entrada principal son los métodos de la clase [Assert](https://developer.xamarin.com/api/type/NUnit.Framework.Assert/).

Además de los métodos de la clase Assert, la funcionalidad de pruebas unitarias se divide en los siguientes espacios de nombre que forman parte de NUnitLite:

-   [NUnit.Framework](https://developer.xamarin.com/api/namespace/NUnit.Framework/)
-   [NUnit.Constraints](https://developer.xamarin.com/api/namespace/NUnit.Framework.Constraints/)
-   [NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/)
-   [NUniteLite.Runner](https://developer.xamarin.com/api/namespace/NUnitLite.Runner/)


El ejecutor de pruebas unitarias específico de Xamarin.iOS se documenta aquí:

-   [NUnit.UI.TouchRunner](https://developer.xamarin.com/api/type/NUnit.UI.TouchRunner/)
