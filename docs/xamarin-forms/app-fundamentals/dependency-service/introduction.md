---
title: Introducción a DependencyService
description: En este artículo se explica cómo funciona la clase DependencyService de Xamarin.Forms para acceder a características nativas de la plataforma.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675021"
---
# <a name="introduction-to-dependencyservice"></a>Introducción a DependencyService

## <a name="overview"></a>Información general

[`DependencyService`](xref:Xamarin.Forms.DependencyService) permite a las aplicaciones llamar a funciones específicas de una plataforma desde el código compartido. Esta funcionalidad permite que las aplicaciones de Xamarin.Forms hagan todo aquello que puede hacer una aplicación nativa.

`DependencyService` es un localizador de servicios. En la práctica, se define una interfaz y `DependencyService` busca la implementación correcta de esa interfaz en los diversos proyectos de plataforma.

> [!NOTE]
> De forma predeterminada, [`DependencyService`](xref:Xamarin.Forms.DependencyService) solo resuelve implementaciones de plataforma con constructores sin parámetros. Pero se puede incorporar a Xamarin.Forms un método de resolución de dependencias que use un contenedor de inserción de dependencias o métodos de generador para resolver implementaciones de plataforma. Este enfoque puede usarse para resolver las implementaciones de plataforma que tienen constructores con parámetros. Para obtener más información, vea [Resolución de dependencias en Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Funcionamiento de DependencyService

Las aplicaciones de Xamarin.Forms necesitan cuatro componentes para usar `DependencyService`:

- **Interfaz** &ndash; una interfaz define la funcionalidad requerida en el código compartido.
- **Implementación por plataforma** &ndash; las clases que implementan la interfaz deben agregarse a cada proyecto de plataforma.
- **Registro** &ndash; cada clase de implementación debe registrarse con `DependencyService` a través de un atributo de metadatos. El registro permite a `DependencyService` buscar la clase de implementación y proporcionarla en lugar de la interfaz en tiempo de ejecución.
- **Llamada a DependencyService** &ndash; el código compartido debe llamar de forma explícita a `DependencyService` para solicitar las implementaciones de la interfaz.

Tenga en cuenta que se deben proporcionar implementaciones para cada proyecto de plataforma de la solución. Los proyectos de plataforma sin implementaciones experimentan un error en tiempo de ejecución.

En el diagrama siguiente se explica la estructura de la aplicación:

![](introduction-images/overview-diagram.png "Estructura de la aplicación DependencyService")

### <a name="interface"></a>Interfaz

La interfaz diseñada define cómo se interactúa con la funcionalidad específica de la plataforma. Sea cuidadoso en caso de estar desarrollando un componente para compartirlo como componente o paquete NuGet. El diseño de la API puede crear o romper un paquete. En el ejemplo siguiente se especifica una interfaz sencilla para texto oral que ofrece flexibilidad a la hora de especificar las palabras que se van a decir pero permite personalizar la implementación para cada plataforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementación por plataforma

Una vez que se ha diseñado una interfaz adecuada, se debe implementar en el proyecto para cada plataforma de destino. Por ejemplo, la siguiente clase implementa la interfaz `ITextToSpeech` en iOS:

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

Cada implementación de la interfaz debe registrarse con `DependencyService` con un atributo de metadatos. El código siguiente registra la implementación para iOS:

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

Observe que el registro se realiza en el nivel de espacio de nombres, no en el de clase.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilación .NET Native de Plataforma universal de Windows

Los proyectos de UWP que usan la opción de compilación .NET Native deben seguir una [configuración ligeramente diferente](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) al inicializar Xamarin.Forms. La compilación .NET Native también requiere un registro ligeramente diferente para los servicios de dependencia.

En el archivo **App.xaml.cs**, registre manualmente cada servicio de dependencia definido en el proyecto de UWP con el método `Register<T>`, como se muestra a continuación:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Observe que el registro manual mediante `Register<T>` solo es eficaz en las compilaciones de versión mediante compilación .NET Native. Si se omite esta línea, las compilaciones de depuración siguen funcionando, pero las compilaciones de versión no cargan el servicio de dependencia.

### <a name="call-to-dependencyservice"></a>Llamada a DependencyService

Una vez que el proyecto se ha configurado con una interfaz común e implementaciones para cada plataforma, use `DependencyService` para obtener la implementación correcta en tiempo de ejecución:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` encuentra la implementación correcta de interfaz `T`.

### <a name="solution-structure"></a>Estructura de solución

A continuación se muestra la [solución UsingDependencyService de ejemplo](https://developer.xamarin.com/samples/UsingDependencyService/) para iOS y Android, con los cambios de código descritos anteriormente resaltados.

 [![Solución de iOS y Android](introduction-images/solution-sml.png "Estructura de la solución de ejemplo DependencyService")](introduction-images/solution.png#lightbox "DependencyService Sample Solution Structure")

> [!NOTE]
> **Debe** proporcionar una implementación en cada proyecto de plataforma. Si no se registra ninguna implementación de interfaz, `DependencyService` no puede resolver el método `Get<T>()` en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DependencyService](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
