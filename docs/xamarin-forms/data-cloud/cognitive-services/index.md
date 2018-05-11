---
title: Agregar inteligencia mediante servicios cognitivos
description: Microsoft Services cognitivos son un conjunto de API, SDK y los servicios disponibles para los desarrolladores para hacer que sus aplicaciones más inteligentes agregando características tales como el reconocimiento facial, reconocimiento de voz y comprensión de lenguaje. Este artículo proporciona una introducción a la aplicación de ejemplo que muestra cómo invocar algunas de las API de servicio cognitivas de Microsoft.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 86253e42db7da2da6eb8b03e2d4a4b3c943b7e17
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="adding-intelligence-with-cognitive-services"></a>Agregar inteligencia mediante servicios cognitivos

_Microsoft Services cognitivos son un conjunto de API, SDK y los servicios disponibles para los desarrolladores para hacer que sus aplicaciones más inteligentes agregando características tales como el reconocimiento facial, reconocimiento de voz y comprensión de lenguaje. Este artículo proporciona una introducción a la aplicación de ejemplo que muestra cómo invocar algunas de las API de servicio cognitivas de Microsoft._

## <a name="overview"></a>Información general

El ejemplo que lo acompañan es una aplicación de lista de tareas que proporciona funcionalidad para:

- Ver una lista de tareas.
- Agregar y editar las tareas a través del teclado de pantalla, o bien al realizar el reconocimiento de voz con la API de voz de Microsoft. Para obtener más información acerca de cómo realizar el reconocimiento de voz, consulte [el reconocimiento de voz mediante la API de voz de Microsoft](speech-recognition.md).
- Escribir tareas de comprobación mediante la API de Bing corrector comprobar. Para obtener más información, consulte [ortográfica mediante la API de Bing corrector comprobar](spell-check.md).
- Traducir las tareas de inglés a alemán mediante la API de traductor. Para obtener más información, consulte [traducción de texto mediante la API de traductor](text-translation.md).
- Eliminar tareas.
- Establece el estado de una tarea a 'done'.
- Velocidad de la aplicación con reconocimiento de emociones, mediante la API de cara. Para obtener más información, consulte [reconocimiento emociones mediante la API de cara](emotion-recognition.md).

Las tareas se almacenan en una base de datos local de SQLite. Para obtener más información sobre el uso de una base de datos local de SQLite, consulte [trabajar con una base de datos Local](~/xamarin-forms/app-fundamentals/databases.md).

El `TodoListPage` se muestra cuando se inicia la aplicación. Esta página muestra una lista de las tareas que se almacenan en la base de datos local y permite al usuario crear una nueva tarea o para clasificar la solicitud:

![](images/sample-application-1.png "TodoListPage")

Se pueden crear nuevos elementos, haga clic en el *+* botón, que se desplaza a la `TodoItemPage`. Esta página también se puede navegar a seleccionando una tarea:

![](images/sample-application-2.png "TodoItemPage")

El `TodoItemPage` permite que las tareas que va a crear, editar, comprobará el corrector, traducir, se guardan y se eliminan. El reconocimiento de voz puede utilizarse para crear o editar una tarea. Esto se logra presionando el botón de micrófono para iniciar la grabación y presionando el botón mismo una segunda vez para detener la grabación, que envía la grabación a la API de reconocimiento de voz de Bing.

Haga clic en el botón de emoticones en el `TodoListPage` navega a la `RateAppPage`, que se usa para realizar el reconocimiento de la expresión en una imagen de una expresión facial:

![](images/sample-application-3.png "RateAppPage")

El `RateAppPage` permite al usuario tomar una foto de su fuente, que se envía a la API de cara con la emoción devuelta que se muestren.

## <a name="understanding-the-application-anatomy"></a>Descripción de la Anatomía de aplicación

El proyecto de biblioteca de clases portables (PCL) para la aplicación de ejemplo consta de cinco carpetas principales:

|Carpeta|Propósito|
|--- |--- |
|Modelos|Contiene las clases del modelo de datos para la aplicación. Esto incluye la `TodoItem` (clase), que modela un único elemento de datos que usa la aplicación. La carpeta también incluye clases que se usan para respuestas en JSON modelo procedentes de distintas API de servicio cognitivas de Microsoft.|
|Repositorios|Contiene el `ITodoItemRepository` interfaz y `TodoItemRepository` clase que se utilizan para realizar operaciones de base de datos.|
|Servicios|Contiene las interfaces y clases que se usan para tener acceso a diferentes cognitivos servicio API de Microsoft, junto con interfaces que se usan por el `DependencyService` clase para buscar las clases que implementan las interfaces en los proyectos de plataforma.|
|Utils|Contiene el `Timer` (clase), que es utilizado por el `AuthenticationService` clase renovar un token de acceso JWT cada 9 minutos.|
|Vistas|Contiene las páginas de la aplicación.|

El proyecto PCL también contiene algunos archivos importantes:

|Archivo|Propósito|
|--- |--- |
|Constants.cs|El `Constants` (clase), que especifica las claves de API y los puntos de conexión de las API del servicio de cognitivas de Microsoft que se invocan. Las constantes de clave de API es necesario actualizar para tener acceso a las API de servicio cognitivos diferentes.|
|App.xaml.cs|El `App` clase es responsable de crear instancias de ambos la primera página que se mostrará de la aplicación en cada plataforma, y la `TodoManager` clase que se utiliza para invocar las operaciones de base de datos.|

### <a name="nuget-packages"></a>Paquetes NuGet

La aplicación de ejemplo utiliza los siguientes paquetes de NuGet:

- `Newtonsoft.Json` : proporciona un marco JSON para. NET.
- `PCLStorage` : proporciona un conjunto de las API de E/S de archivos local de multiplataforma.
- `sqlite-net-pcl` : proporciona almacenamiento de base de datos de SQLite.
- `Xam.Plugin.Media` : proporciona las API de selección y toma de fotografía de multiplataforma.

Además, estos paquetes de NuGet también instalan sus propias dependencias.

### <a name="modeling-the-data"></a>Modelar los datos

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos que se muestran y se almacena en la base de datos local de SQLite. En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

El `ID` propiedad se utiliza para identificar de forma única cada `TodoItem` una instancia y se decoran con atributos de SQLite que hacen que la propiedad de una clave principal de incremento automático de la base de datos.

### <a name="invoking-database-operations"></a>Invocar operaciones de base de datos

El `TodoItemRepository` clase implementa las operaciones de base de datos y una instancia de la clase puede tener acceso mediante el `App.TodoManager` propiedad. La `TodoItemRepository` clase proporciona los métodos siguientes para invocar operaciones de base de datos:

- **GetAllItemsAsync** : recupera todos los elementos de la base de datos local de SQLite.
- **GetItemAsync** : recupera un elemento especificado de la base de datos local de SQLite.
- **SaveItemAsync** : crea o actualiza un elemento en la base de datos local de SQLite.
- **DeleteItemAsync** : elimina el elemento especificado de la base de datos local de SQLite.

### <a name="platform-project-implementations"></a>Implementaciones de proyecto de plataforma

El `Services` carpeta en el proyecto PCL contiene la `IFileHelper` y `IAudioRecorderService` interfaces que se usan por el `DependencyService` clase para buscar las clases que implementan las interfaces en los proyectos de plataforma.

El `IFileHelper` interfaz está implementada por la `FileHelper` clase en cada proyecto de la plataforma. Esta clase está formada por un único método, `GetLocalFilePath`, que devuelve una ruta de acceso de archivo local para almacenar la base de datos de SQLite.

El `IAudioRecorderService` interfaz está implementada por la `AudioRecorderService` clase en cada proyecto de la plataforma. Esta clase está formada por `StartRecording`, `StopRecording`y da soporte a métodos, que usa las API de plataforma para grabar audio desde el micrófono del dispositivo y almacenan como un archivo wav. En iOS, el `AudioRecorderService` usa el `AVFoundation` API para grabar audio. En Android, el `AudioRecordService` usa el `AudioRecord` API para grabar audio. En la plataforma Universal de Windows (UWP), el `AudioRecorderService` usa el `AudioGraph` API para grabar audio.

### <a name="invoking-cognitive-services"></a>Invocar los servicios cognitivos

La aplicación de ejemplo invoca los servicios de Microsoft cognitivos siguientes:

- API de voz de Microsoft. Para obtener más información, consulte [el reconocimiento de voz mediante la API de voz de Microsoft](speech-recognition.md).
- API de Bing corrector ortográfico. Para obtener más información, consulte [ortográfica mediante la API de Bing corrector comprobar](spell-check.md).
- Traducir API. Para obtener más información, consulte [traducción de texto mediante la API de traductor](text-translation.md).
- Cara API. Para obtener más información, consulte [reconocimiento emociones mediante la API de cara](emotion-recognition.md).

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de cognitivos servicios de Microsoft](https://www.microsoft.com/cognitive-services/documentation)
- [Lista de tareas cognitivos servicios (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
