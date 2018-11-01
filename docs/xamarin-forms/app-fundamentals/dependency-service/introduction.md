---
title: Introducción a DependencyService
description: En este artículo se explica cómo funciona la clase Xamarin.Forms DependencyService para acceder a características de plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675021"
---
# <a name="introduction-to-dependencyservice"></a>Introducción a DependencyService

## <a name="overview"></a>Información general

[`DependencyService`](xref:Xamarin.Forms.DependencyService) permite que las aplicaciones llamar a funciones específicas de plataforma desde código compartido. Esta funcionalidad permite que las aplicaciones de Xamarin.Forms todo lo que puede hacer una aplicación nativa hacer.

`DependencyService` es un localizador de servicio. En la práctica, se define una interfaz y `DependencyService` busca la correcta implementación de la interfaz de los diversos proyectos de plataforma.

> [!NOTE]
> De forma predeterminada, el [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) solucionará solo las implementaciones de plataforma que tienen constructores sin parámetros. Sin embargo, un método de resolución de dependencia puede insertarse en Xamarin.Forms que usa un contenedor de inserción de dependencia o métodos de generador para resolver las implementaciones de plataforma. Este enfoque puede utilizarse para resolver las implementaciones de plataforma que tienen constructores con parámetros. Para obtener más información, consulte [resolución de dependencias en Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Funcionamiento de DependencyService

Las aplicaciones de Xamarin.Forms necesitan cuatro componentes que se utilizará `DependencyService`:

- **Interfaz** &ndash; se define la funcionalidad requerida por una interfaz en código compartido.
- **Implementación por plataforma** &ndash; las clases que implementan la interfaz deben agregarse a cada proyecto de la plataforma.
- **Registro** &ndash; cada clase de implementación debe registrarse con `DependencyService` a través de un atributo de metadatos. Habilita el registro `DependencyService` para buscar la clase de implementación y proporcionar en lugar de la interfaz en tiempo de ejecución.
- **La llamada a DependencyService** &ndash; comparten el código debe llamar explícitamente a `DependencyService` para solicitar las implementaciones de la interfaz.

Tenga en cuenta que se deben proporcionar implementaciones para cada proyecto de la plataforma en la solución. Proyectos de la plataforma sin implementaciones se producirá un error en tiempo de ejecución.

Se explica la estructura de la aplicación en el diagrama siguiente:

![](introduction-images/overview-diagram.png "Estructura de la aplicación de DependencyService")

### <a name="interface"></a>Interfaz

La interfaz de diseño que definirá cómo interactuar con una funcionalidad específica de la plataforma. Tenga cuidado si está desarrollando un componente compartido como un componente o paquete de NuGet. Diseño de API puede crear o interrumpir un paquete. El ejemplo siguiente especifica una interfaz sencilla para hablar el texto que permite flexibilidad para especificar las palabras que se hablará, pero deja la implementación para personalizarse para cada plataforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementación por plataforma

Una vez que se ha diseñado una interfaz adecuada, se debe implementar esa interfaz en el proyecto para cada plataforma de destino. Por ejemplo, la siguiente clase implementa la `ITextToSpeech` interfaz en iOS:

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>Registro

Cada implementación de la interfaz debe estar registrada con `DependencyService` con un atributo de metadatos. El código siguiente registra la implementación para iOS:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

En resumen, la implementación específica de la plataforma tiene este aspecto:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

Nota: que se realiza el registro en el nivel de espacio de nombres, no en el nivel de clase.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilación nativa de .NET de plataforma de Windows universal

Los proyectos UWP que usan la opción de compilación .NET Native deben seguir un [configuración ligeramente diferentes](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) al inicializar Xamarin.Forms. Compilación nativa de .NET también requiere el registro ligeramente diferente para los servicios de dependencia.

En el **App.xaml.cs** de archivos, registrar manualmente cada servicio de dependencia definida en el proyecto UWP con el `Register<T>` método tal y como se muestra a continuación:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Nota: el registro manual mediante `Register<T>` es eficaz solo en la versión compilaciones con compilación nativa. NET. Si se omite esta línea, las compilaciones de depuración seguirá funcionando, pero compilaciones de versión se producirá un error al cargar el servicio de dependencia.

### <a name="call-to-dependencyservice"></a>La llamada a DependencyService

Una vez que el proyecto se configuró con una interfaz común e implementaciones para cada plataforma, use `DependencyService` para obtener una implementación correcta en tiempo de ejecución:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` Encontrará la correcta implementación de interfaz `T`.

### <a name="solution-structure"></a>Estructura de la solución

El [UsingDependencyService solución de ejemplo](https://developer.xamarin.com/samples/UsingDependencyService/) es que se muestra a continuación para iOS y Android, con los cambios de código descritos anteriormente resaltado.

 [![iOS y Android solución](introduction-images/solution-sml.png "DependencyService estructura de la solución de ejemplo")](introduction-images/solution.png#lightbox "DependencyService estructura de la solución de ejemplo")

> [!NOTE]
> Le **debe** proporcionar una implementación en cada proyecto de la plataforma. Si no se registra ninguna implementación de interfaz, el `DependencyService` será no se puede resolver el `Get<T>()` método en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
