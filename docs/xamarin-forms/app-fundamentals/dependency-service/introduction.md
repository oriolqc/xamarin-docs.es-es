---
title: Introducción a DependencyService
description: Comprender el funcionamiento de DependencyService a las características de la plataforma nativa de acceso
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 01953d55a104a70b0451c9b796c732254afb081e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-dependencyservice"></a>Introducción a DependencyService

## <a name="overview"></a>Información general

[`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) permite que las aplicaciones llamar a la funcionalidad específica de la plataforma desde código compartido. Esta funcionalidad permite que las aplicaciones de Xamarin.Forms todo lo que puede hacer una aplicación nativa hacer.

`DependencyService` es una resolución de dependencia. En la práctica, se define una interfaz y `DependencyService` busca la implementación correcta de esa interfaz de los diversos proyectos de plataforma.

## <a name="how-dependencyservice-works"></a>Cómo funciona DependencyService

Aplicaciones de Xamarin.Forms necesitan tres componentes que se utilizará `DependencyService`:

- **Interfaz** &ndash; la funcionalidad necesaria se define mediante una interfaz en el código compartido.
- **Por la implementación de la plataforma** &ndash; las clases que implementan la interfaz se deben agregar a cada proyecto de la plataforma.
- **Registro** &ndash; cada clase de implementación debe registrarse con `DependencyService` a través de un atributo de metadatos. Habilita el registro `DependencyService` para encontrar la clase de implementación y suministrarlo en lugar de la interfaz en tiempo de ejecución.
- **La llamada a DependencyService** &ndash; compartido código debe llamar explícitamente a `DependencyService` que ponerse en contacto para las implementaciones de la interfaz.

Tenga en cuenta que se deben proporcionar implementaciones para cada proyecto de la plataforma en la solución. Proyectos de plataforma sin implementaciones se producirá un error en tiempo de ejecución.

Se explica la estructura de la aplicación en el diagrama siguiente:

![](introduction-images/overview-diagram.png "Estructura de aplicación DependencyService")

### <a name="interface"></a>Interfaz

La interfaz de diseño definirá cómo interactuar con una funcionalidad específica de la plataforma. Tenga cuidado si está desarrollando un componente compartido como un componente o paquete de Nuget. Diseño de la API puede hacer o interrumpir un paquete. En el ejemplo siguiente se especifica una interfaz sencilla para hablar de texto que permite flexibilidad para especificar las palabras que se pronuncian pero deja la implementación se personalice para cada plataforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementación por plataforma

Una vez que se ha diseñado una interfaz adecuada, se debe implementar esa interfaz en el proyecto para cada plataforma de destino. Por ejemplo, las clases siguientes implementan la `ITextToSpeech` interfaz en Windows Phone:

```csharp
namespace TextToSpeech.WinPhone
{
  public class TextToSpeechImplementation : ITextToSpeech
  {
      public TextToSpeechImplementation() {}

      public async void Speak(string text)
      {
          SpeechSynthesizer synth = new SpeechSynthesizer();
          await synth.SpeakTextAsync(text);
      }
  }
}
```

Tenga en cuenta que cada implementación debe tener un constructor (sin parámetros) predeterminado en orden para `DependencyService` que puedan crear instancias de él. La interfaz no se pueden definir constructores sin parámetros.

### <a name="registration"></a>Registro

Cada implementación de la interfaz debe registrarse con `DependencyService` con un atributo de metadatos. El código siguiente registra la implementación para Windows Phone:

```csharp
using TextToSpeech.WinPhone;

[assembly: Xamarin.Forms.Dependency (typeof (TextToSpeechImplementation))]
namespace TextToSpeech.WinPhone {
  ...
```

Reunión de todo, la implementación específica de la plataforma que se ve así:

```csharp
[assembly: Xamarin.Forms.Dependency (typeof (TextToSpeechImplementation))]
namespace TextToSpeech.WinPhone {
  public class TextToSpeechImplementation : ITextToSpeech
  {
      public TextToSpeechImplementation() {}

      public async void Speak(string text)
      {
          SpeechSynthesizer synth = new SpeechSynthesizer();
          await synth.SpeakTextAsync(text);
      }
  }
}
```

Nota: que se realiza el registro en el nivel de espacio de nombres, no en el nivel de clase.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilación nativa de Windows universal plataforma .NET

Los proyectos UWP que usan la opción de compilación de .NET Native deben seguir un [configuración ligeramente diferentes](~/xamarin-forms/platform/windows/installation/universal.md#target-invocation-exception) al inicializar Xamarin.Forms. Compilación nativa de .NET también requiere registro ligeramente diferente para los servicios de dependencia.

En el **App.xaml.cs** de archivos, registre manualmente cada servicio de dependencia definida en el proyecto UWP usando el `Register<T>` método, tal y como se muestra a continuación:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Nota: el registro manual mediante `Register<T>` es sólo es efectivo en versión compilaciones con compilación nativa. NET. Si se omite esta línea, compilaciones de depuración seguirá funcionando, pero versiones de lanzamiento se producirá un error al cargar el servicio de dependencia.

### <a name="call-to-dependencyservice"></a>La llamada a DependencyService

Una vez que el proyecto se ha configurado con una interfaz común y las implementaciones para cada plataforma, use `DependencyService` para obtener la implementación correcta en tiempo de ejecución:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` Encontrará la implementación correcta de la interfaz `T`.

### <a name="solution-structure"></a>Estructura de la solución

El [UsingDependencyService solución de ejemplo](https://developer.xamarin.com/samples/UsingDependencyService/) es se muestra a continuación para iOS y Android, con los cambios de código descritos anteriormente resaltado.

 [![iOS y Android solución](introduction-images/solution-sml.png "DependencyService estructura de la solución de ejemplo")](introduction-images/solution.png#lightbox "DependencyService estructura de la solución de ejemplo")

> [!NOTE]
> Se **debe** proporcionar una implementación en cada proyecto de la plataforma. Si se registró ninguna implementación de interfaz, la `DependencyService` podrá resolver el `Get<T>()` método en tiempo de ejecución.


## <a name="related-links"></a>Vínculos relacionados

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
