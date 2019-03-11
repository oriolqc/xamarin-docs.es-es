---
title: 'Parte 2: Arquitectura'
description: Este documento describe los patrones de arquitectura que resulta útiles para compilar aplicaciones multiplataforma. Describe las capas de aplicación típico (capa de datos, capa de acceso a datos, etc.) y los patrones comunes de software móvil (MVVM, MVC, etcetera.)
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: cfb2bddceea7717ac87bd7a78fd9cd45e8b93144
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670446"
---
# <a name="part-2---architecture"></a>Parte 2: Arquitectura

Es un principio clave de la creación de aplicaciones multiplataforma crear una arquitectura que se presta a un maximización de compartir código entre plataformas. Cumple los siguientes principios de programación orientada a objetos ayuda a compilar una aplicación bien diseñada:

-   **Encapsulación** : lo que asegura que, clases y capas de arquitectura incluso solo exponen una API mínimo que lleva a cabo sus requiere funciones y oculta los detalles de implementación. En un nivel de clase, esto significa que los objetos se comportan como 'cajas negras' y que consumen el código no necesita saber cómo cumplir sus tareas. En un nivel de arquitectura, significa que la implementación de patrones como fachada que fomentan una API simplificada que organiza las interacciones más complejas en nombre del código en capas más abstractas. Esto significa que el código de interfaz de usuario (por ejemplo) sólo debe responsable de mostrar pantallas y aceptar la entrada del usuario; y nunca interactuar directamente con la base de datos. Del mismo modo el código de acceso a datos debe solo leer y escribir en la base de datos, pero nunca interactúan directamente con los botones o etiquetas.
-   **Separación de responsabilidades** : asegúrese de que cada componente (tanto en la arquitectura y nivel de clases) tiene una finalidad clara y bien definida. Cada componente debe realizar sólo sus tareas definidas y exponer esa funcionalidad a través de una API que sea accesible para las demás clases que deben usarlo.
-   **Polimorfismo** : programación a una interfaz (o clase abstracta) que admite varias formas de implementaciones que puede escribir código básico y comparte entre plataformas, mientras sigue interactuando con características específicas de plataforma.


El resultado natural es una aplicación modelada después reales o entidades abstractas con capas lógicas independientes. Separar el código en capas hacer aplicaciones más fáciles de entender, probar y mantener. Se recomienda que el código en cada capa sea físicamente independiente (ya sea en proyectos independientes incluso aplicaciones muy grandes o directorios), así como lógicamente independiente (con espacios de nombres).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Capas de aplicación típico

A lo largo de este documento y los casos prácticos, nos referimos a las capas de seis aplicación siguientes:

-   **Capa de datos** – Non-volatile persistencia de los datos, es probable que una base de datos de SQLite, pero se podría implementar con archivos XML o cualquier otro mecanismo adecuado.
-   **Capa de acceso a datos** : contenedor en torno a la capa de datos que proporciona la creación, lectura, actualización y acceso de eliminación (CRUD) a los datos sin exponer los detalles de implementación al llamador. Por ejemplo, la capa DAL puede contener instrucciones SQL para consultar o actualizar los datos, pero el código de referencia no necesitaría saber esto.
-   **Capa de negocio** : (a veces denominado capa de lógica empresarial o BLL) contiene las definiciones de entidad de negocio (modelo) y la lógica de negocios. Candidato para el patrón de fachada de negocios.
-   **Capa de acceso de servicio** : se usa para el acceso a servicios en la nube: desde los servicios web complejo (REST, JSON, WCF) a la simple recuperación de datos e imágenes desde servidores remotos. Encapsula el comportamiento de la red y proporciona una API sencilla que será consumido por las capas de aplicación y la interfaz de usuario.
-   **Nivel de aplicación** : código que normalmente es específico de plataforma (que generalmente no se comparte entre plataformas) o el código que es específico para la aplicación (no reutilizable con carácter general). Es una buena prueba si se va a colocar código en la capa de aplicación frente a la capa de interfaz de usuario (a) determinar si la clase tiene todos los controles de presentación real o (b) si se puede compartir entre varias pantallas o dispositivos (p ej. iPhone y iPad).
-   **Capa de interfaz de usuario** : la capa de cara al usuario, contiene las pantallas, widgets y los controladores que ellos administran.


Una aplicación no puede contener necesariamente todas las capas, por ejemplo la capa de acceso de servicio no existiría en una aplicación que no tiene acceso a los recursos de red. Una aplicación muy sencilla podría combinar la capa de datos y la capa de acceso a datos porque las operaciones son muy básicas.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Patrones comunes de Software móvil

Patrones son una forma establecida para capturar periódicas soluciones a problemas comunes. Hay algunos patrones claves que son útiles para comprender en la creación de aplicaciones móviles fácil de mantener y comprensible.

-   **Modelo de vista, ViewModel (MVVM)** : The Model-View-ViewModel patrón es popular con marcos de trabajo que admiten el enlace de datos, por ejemplo, Xamarin.Forms. Se popularizó mediante SDK basadas en XAML, como Windows Presentation Foundation (WPF) y Silverlight. donde ViewModel actúa como un intermediario entre los datos (modelo) y la interfaz de usuario (vista) mediante los comandos y el enlace de datos.
-   **Modelo de vista, controlador (MVC)** : un patrón común y a menudo menos entendido, MVC se suele utilizar al compilar Interfaces de usuario y proporciona una separación entre la definición de una pantalla de la interfaz de usuario (vista), el motor subyacente que controla interacción (controlador) y los datos que rellena (modelo). El modelo es realmente una parte totalmente opcional y por lo tanto, el núcleo de la descripción de este patrón se encuentra en la vista y controlador. MVC es un enfoque popular para aplicaciones de iOS.
-   **Fachada de negocios** : también conocido como modelo de administrador, proporciona un punto de entrada simplificado para un trabajo complejo. Por ejemplo, en una aplicación de seguimiento de tareas, podría tener un `TaskManager` de clases con métodos como `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` , etcetera. La `TaskManager` clase proporciona una fachada para el funcionamiento interno de guardar/recuperar realmente de los objetos de las tareas.
-   **Singleton** : patrón Singleton The brinda una manera en que puede existir alguna vez una sola instancia de un objeto determinado. Por ejemplo, al usar SQLite en aplicaciones móviles, solo quiere una instancia de la base de datos. Con el patrón Singleton es una manera sencilla de asegurarse de esto.
-   **Proveedor** – acuñado un patrón de Microsoft (posiblemente similar a la estrategia o básica de inserción de dependencias) para promover la reutilización del código en todas las aplicaciones de Silverlight, WPF y WinForms. Se puede escribir código compartido en una interfaz o clase abstracta y se escriben y se pasa cuando se usa el código específico de la plataforma de implementaciones concretas.
-   **Async** – para que no se debe confundir con la palabra clave Async, el patrón se utiliza cuando el trabajo de larga ejecución se debe ejecutar sin necesidad de mantener la seguridad de la interfaz de usuario o la transformación actual de Async. En su forma más simple, el patrón asincrónico describe simplemente que las tareas de ejecución prolongada deberían interrumpir en otro subproceso (o una abstracción de subproceso como una tarea similar) mientras el subproceso actual continúa procesando y realiza escuchas para una respuesta del proceso en segundo plano y, a continuación, actualiza la interfaz de usuario cuando se devuelven datos y o estado.


Se examinará cada uno de los patrones más detalladamente según su uso práctico se ilustra en los casos prácticos. Wikipedia ha más descripciones detalladas de los [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [fachada](https://en.wikipedia.org/wiki/Facade_pattern), [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [estrategia](https://en.wikipedia.org/wiki/Strategy_pattern)y [proveedor](https://en.wikipedia.org/wiki/Provider_model) patrones (y de [patrones de diseño](https://en.wikipedia.org/wiki/Design_Patterns) con carácter general).
