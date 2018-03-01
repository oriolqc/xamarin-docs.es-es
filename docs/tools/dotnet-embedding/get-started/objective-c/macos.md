---
title: "Introducción a macOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 5e2f14e7b29f85e838563914089743f56239d7bb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-macos"></a>Introducción a macOS


## <a name="what-you-will-need"></a>Lo que necesita

* Siga las instrucciones que aparecen en nuestro [introducción con Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía.

* Un ensamblado de .NET para usar con **Embeddinator 4000**.

* Una aplicación de cacao macOS

Continúe después de haber seguido las instrucciones que aparecen en nuestro [introducción con Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía. Si ya tiene un ensamblado .NET puede omitir directamente en **Embeddinator-4000 utilizando** sección.

## <a name="creating-a-net-assembly"></a>Crear un ensamblado .NET

Para generar un ensamblado de .NET deberá abrir [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) y crear un nuevo **proyecto de biblioteca de .NET** de manera práctica *archivo > nueva solución > otros > .NET > biblioteca*. Haga clic en siguiente y proporcione *tiempo* como el *nombre del proyecto*y haga clic en *crear*.

Siga los pasos siguientes:

1. Eliminar el **MyClass.cs** archivo y la **propiedades** carpeta.

2. Haga clic con el botón secundario en *proyecto tiempo > Agregar > nuevo archivo.*

3. Seleccione *clase vacía* y usar **XAMWeatherFetcher** como el nombre, a continuación, haga clic en nuevo.

4. Reemplace el contenido de *XAMWeatherFetcher.cs* con el código siguiente:

```csharp
using System;
using System.Json;
using System.Net;

public class XAMWeatherFetcher {

    static string urlTemplate = @"https://query.yahooapis.com/v1/public/yql?q=select%20item.condition%20from%20weather.forecast%20where%20woeid%20in%20(select%20woeid%20from%20geo.places(1)%20where%20text%3D%22{0}%2C%20{1}%22)&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys";
    public string City { get; private set; }
    public string State { get; private set; }

    public XAMWeatherFetcher (string city, string state)
    {
        City = city;
        State = state;
    }

    public XAMWeatherResult GetWeather ()
    {
        try {
            using (var wc = new WebClient ()) {
                var url = string.Format (urlTemplate, City, State);
                var str = wc.DownloadString (url);
                var json = JsonValue.Parse (str)["query"]["results"]["channel"]["item"]["condition"];
                var result = new XAMWeatherResult (json["temp"], json["text"]);
                return result;
            }
        }
        catch (Exception ex) {
            // Log some of the exception messages
            Console.WriteLine (ex.Message);
            Console.WriteLine (ex.InnerException?.Message);
            Console.WriteLine (ex.InnerException?.InnerException?.Message);

            return null;
        }

    }
}

public class XAMWeatherResult {
    public string Temp { get; private set; }
    public string Text { get; private set; }

    public XAMWeatherResult (string temp, string text)
    {
        Temp = temp;
        Text = text;
    }
}
```

Observará que `Using System.Json;` produce un error; para solucionar esto es necesario para hacer lo siguiente:

1. Haga doble clic en el **referencias** carpeta.

2. Haga clic en el **paquetes** ficha.

3. Comprobar **System.Json**.

4. Click **Ok**.

Una vez que los pasos anteriores se realiza lo que debemos hacer es compilación nuestro ensamblado. NET, haga clic en *menú Generar > compilar todos los* o ⌘ + b. Compilación correcta debería aparecer el mensaje en la barra de estado superior.

Ahora, haga clic en *tiempo* nodo del proyecto y seleccione *revelar en Finder*. En Finder, vaya a la *bin/Debug* carpeta; interna que encuentre **Weather.dll.**

## <a name="using-embeddinator-4000"></a>Usar Embeddinator 4000

Si instaló Embeddinator-4000 mediante el instalador de pkg e inicia una nueva sesión de terminal después de la instalación, puede usar el **objcgen** comando (en caso contrario, puede usar su ruta de acceso absoluta: `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`); **objcgen** es la herramienta que necesitamos para generar una biblioteca nativa de un ensamblado. NET.

Abra terminal, `cd` en la carpeta que contiene Weather.dll y ejecute **objcgen** con los argumentos que se muestra a continuación:

```shell
cd /Users/Alex/Projects/Weather/Weather/bin/Debug

objcgen --debug --outdir=output -c Weather.dll
```

Todo lo que necesita se colocarán en el **salida** directorio junto a *Weather.dll*. Si tiene su propio ensamblado. NET, reemplace *Weather.dll* con él y siga los mismos pasos de la sección.

## <a name="using-the-generated-output-in-an-xcode-project"></a>Usar el resultado generado en un proyecto de Xcode

Abra Xcode y cree un nuevo **macOS cacao aplicación** y asígnele el nombre **MyWeather**. Haga clic con el botón secundario en el *nodo del proyecto MyWeather*, seleccione *agregar archivos a "MyWeather"*, navegue hasta la **salida** directorio creado por *Embeddinator 4000* y agregue los siguientes archivos:

* Bindings.h
* embeddinator.h
* glib.h
* mono support.h
* mono_embeddinator.h
* objc-support.h
* libWeather.dylib
* Weather.dll

Asegúrese de que **copiar elementos si es necesario** está activada en el panel de opciones del cuadro de diálogo de archivo.

Ahora necesitamos para asegurarse de que **libWeather.dylib** y **Weather.dll** obtener en el grupo de aplicaciones:

* Haga clic en *nodo del proyecto MyWeather*.
* Seleccione *fases de compilación* ficha.
* Agregue un nuevo *fase de copia de archivos*.
* En *destino* seleccione **marcos** y agregue **libWeather.dylib**.
* Agregue un nuevo *fase de copia de archivos*.
* En *destino* seleccione **ejecutables**, agregar **Weather.dll** y asegúrese de que *código de inicio de sesión copia* está activada.

Ahora, abra **ViewController.m** y reemplace su contenido con:

```objective-c
#import "ViewController.h"
#import "bindings.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    XAMWeatherFetcher * fetcher = [[XAMWeatherFetcher alloc] initWithCity:@"Boston" state:@"MA"];
    XAMWeatherResult * weather = [fetcher getWeather];

    NSString * result;
    if (weather)
        result = [NSString stringWithFormat:@"%@ °F - %@", weather.temp, weather.text];
    else
        result = @"An error occured";

    NSTextField * textField = [NSTextField labelWithString:result];
    [self.view addSubview:textField];
}

@end
```

Por último, ejecute el proyecto de Xcode, y se mostrará a algo parecido a esto:

![Ejecución de ejemplo MyWeather](macos-images/weather-from-csharp-macos.png)

Un ejemplo más completo y con mayor capacidad [aquí](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
