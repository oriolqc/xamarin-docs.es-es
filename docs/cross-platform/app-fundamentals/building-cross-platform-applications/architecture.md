---
title: 'Parte 2: arquitectura'
ms.topic: article
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 25cd374ef2b3026f5ac7242ee076c8593ce806da
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="part-2---architecture"></a>Parte 2: arquitectura

Un principio clave de la creación de aplicaciones multiplataforma consiste en crear una arquitectura que se presta a una maximización de código compartir código entre plataformas. Respetando los principios de programación orientada a objetos de objeto siguientes le ayuda a crear una aplicación con una arquitectura adecuada:

-   **Encapsulación** : garantizar que las clases y capas de la arquitectura incluso solo exponen una API mínimo que lleva a cabo sus requiere las funciones y oculta los detalles de implementación. En un nivel de clase, esto significa que los objetos se comportan como 'cuadros negros' y que utilizar código no es necesario saber cómo realizar sus tareas. En el nivel de arquitectura, significa que implementar los patrones similares fachada que animan a una API simplificada que orquesta interacciones más complejas en nombre del código en capas más abstractos. Esto significa (que el código de interfaz de usuario por ejemplo) únicamente debería estar responsable de mostrar pantallas y acepta la entrada del usuario; y nunca interactuar directamente con la base de datos. De forma similar el código de acceso a datos debe solo leer y escribir en la base de datos, pero nunca interactúan directamente con botones o etiquetas.
-   **Separación de responsabilidades** : asegúrese de que cada componente (tanto en la arquitectura y clase nivel) tiene un propósito claro y bien definido. Cada componente debe realizar solo sus tareas definidas y exponer esa funcionalidad a través de una API que sea accesible para las demás clases que necesiten usarlo.
-   **Polimorfismo** : programación a una interfaz (o clase abstracta) que admite varios medios de las implementaciones que código básico se puede escribir y comparten entre plataformas, mientras que todavía interactuar con características específicas de la plataforma.


El resultado natural es una aplicación modelada a partir de entidades abstractas con capas lógicas independientes o el mundo real. Separar código en hacer que las capas las aplicaciones más fácil de entender, probar y mantener. Se recomienda que el código en cada capa sea físicamente independiente (ya sea en directorios o proyectos independientes incluso para aplicaciones muy grandes), así como lógicamente independiente (con espacios de nombres).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Capas de aplicación típica

A lo largo de este documento y los casos de estudio nos referimos a las capas de seis aplicación siguientes:

-   **Capa de datos** – no volátil persistencia de los datos, es probable que una base de datos de SQLite, pero podría implementarse con archivos XML o cualquier otro mecanismo adecuado.
-   **Capa de acceso a datos** – contenedor alrededor de la capa de datos que proporciona la creación, lectura, actualización, acceso de eliminación (CRUD) a los datos sin exponer los detalles de implementación al llamador. Por ejemplo, la capa DAL puede contener instrucciones de SQL para consultar o actualizar los datos, pero el código de referencia no sería necesario tenerlo en cuenta.
-   **Capa de negocio** : (a veces denominado capa de lógica empresarial o BLL) contiene las definiciones de entidad de negocio (modelo) y la lógica empresarial. Candidata para el patrón de fachada de negocios.
-   **Nivel de acceso de servicio** : se usa para los servicios de acceso en la nube: de servicios web complejas (REST, JSON, WCF) para la recuperación simple de datos e imágenes desde servidores remotos. Encapsule el comportamiento de la red y proporciona una API sencilla a ser consumidos por los niveles de aplicación y la interfaz de usuario.
-   **Nivel de aplicación** : código que normalmente es específico de plataforma (generalmente no se comparten entre plataformas) o código que es específico de la aplicación (no reutilizable generalmente). Es una buena manera de comprobar si va a colocar código en el nivel de aplicación frente a la capa de interfaz de usuario (a) determinar si la clase tiene los controles para mostrar real o (b) si se puede compartir entre varias pantallas o dispositivos (p. ej. iPhone y iPad).
-   **Capa de interfaz de usuario** : el nivel de cara al usuario, contiene pantallas, widgets y los controladores que administrarlos.


Una aplicación no puede contener necesariamente todas las capas, por ejemplo el nivel de acceso de servicio no existiría en una aplicación que no tiene acceso a recursos de red. Una aplicación muy simple puede combinar la capa de datos y la capa de acceso a datos porque las operaciones son muy básicas.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Patrones comunes de Software móvil

Patrones son una manera establecida para capturar periódicas soluciones a problemas comunes. Hay algunos patrones clave que son útiles para entender en la creación de aplicaciones para dispositivos móviles que se pueda mantener y comprender.

-   **Modelo de vista, ViewModel (MVVM)** : The Model-View-ViewModel patrón es popular entre los marcos de trabajo que admiten enlace de datos, como Xamarin.Forms. Se popularizó por SDK basadas en XAML como Windows Presentation Foundation (WPF) y Silverlight; donde el modelo de vista actúa como intermediario entre los datos (modelo) y la interfaz de usuario (vista) a través de enlace de datos y comandos.
-   **Modelo de vista, Controller (MVC)** – un patrón común y a menudo interpretación incorrecta, se suele utilizar al crear Interfaces de usuario y proporciona una separación entre la definición de una pantalla de interfaz de usuario (vista), el motor subyacente que controla de MVC interacción (controlador) y los datos que llena (modelo). El modelo es realmente una parte opcional completamente y por lo tanto, el núcleo de la descripción de este patrón se encuentra en la vista y el controlador. MVC es un método popular para las aplicaciones de iOS.
-   **Fachada de negocios** , también conocido como administrador de modelo, proporciona un punto de entrada simplificado para un trabajo complejo. Por ejemplo, en una aplicación de seguimiento de tareas, podría tener un `TaskManager` clase con métodos como `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` , etcetera. La `TaskManager` clase proporciona una fachada para el funcionamiento interno de guardar/recuperar realmente de los objetos de las tareas.
-   **Singleton** : patrón Singleton la proporciona una manera en la que puede existir alguna vez una sola instancia de un objeto determinado. Por ejemplo, cuando se usa código en las aplicaciones móviles, solo alguna vez quieres una instancia de la base de datos. Utiliza el modelo Singleton es una manera sencilla de asegurarse de esto.
-   **Proveedor** – un patrón acuñó por Microsoft (sin duda similar a la estrategia o básica de inserción de dependencias) para promover la reutilización de código en todas las aplicaciones de formularios Windows Forms, WPF y Silverlight. Se puede escribir código compartido en una interfaz o clase abstracta y se escriben y se pasa cuando se utiliza el código específico de la plataforma de implementaciones concretas.
-   **Async** : no se deben confundir con la palabra clave Async, Async patrón se utiliza cuando el trabajo de ejecución prolongada se debe ejecutar sin mantener la seguridad de la interfaz de usuario o la transformación actual. En su forma más simple, el patrón asincrónico describe simplemente que las tareas de ejecución prolongada deberían pondrá en marcha en otro subproceso (o similar abstracción de subproceso, como una tarea) mientras el subproceso actual sigue procesando y busca una respuesta desde el proceso en segundo plano y, a continuación, actualiza la interfaz de usuario cuando se devuelven los datos y estado.


Cada uno de los patrones se examinará con más detalle, tal y como se muestra su uso resulta muy práctico en los casos de estudio. Wikipedia con más detallada de las descripciones de los [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [fachada](http://en.wikipedia.org/wiki/Facade_pattern), [Singleton](http://en.wikipedia.org/wiki/Singleton_pattern), [estrategia](http://en.wikipedia.org/wiki/Strategy_pattern)y [proveedor](http://en.wikipedia.org/wiki/Provider_model) patrones (y de [patrones de diseño](http://en.wikipedia.org/wiki/Design_Patterns) normalmente).
