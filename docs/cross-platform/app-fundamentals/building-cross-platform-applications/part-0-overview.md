---
title: "Building Cross información general sobre aplicaciones de plataforma"
ms.topic: article
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8e7b25f615939c0ae902e09c728615188d1cb86e
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2018
---
# <a name="building-cross-platform-applications-overview"></a>Building Cross información general sobre aplicaciones de plataforma

Esta guía describe la plataforma Xamarin y cómo diseñar una aplicación multiplataforma para maximizar la reutilización de código y ofrecer una experiencia nativa de alta calidad en todas las principales plataformas móviles: iOS, Android y Windows Phone.

El enfoque usado en este documento es generalmente aplicable a las aplicaciones de productividad y aplicaciones de juegos, pero el foco está en la productividad y la utilidad (aplicaciones de juego no). Consulte la [Introducción a los documentos de MonoGame](https://developer.xamarin.com/guides/cross-platform/game_development/monogame/introduction/) o visite [Visual Studio Tools para Unity](https://docs.microsoft.com/en-us/visualstudio/cross-platform/visual-studio-tools-for-unity) para obtener instrucciones de desarrollo de juegos multiplataforma.

La frase "escribir-ejecutar una vez, en todas partes" se utiliza a menudo para ensalzaban las virtudes de un solo código base que se ejecuta sin cambios en varias plataformas. Aunque tiene la ventaja de reutilización del código, ese método conduce generalmente a las aplicaciones que tienen un conjunto de características más bajo de denominador común y una interfaz de usuario buscando genérico que no se ajusta bien en cualquiera de las plataformas de destino.

Xamarin no es simplemente una "escritura-ejecutar una vez, en todas partes" plataforma, como uno de sus ventajas es la capacidad de implementar las interfaces de usuario nativas específicamente para cada plataforma. Sin embargo, con un diseño concienzudo todavía es posible compartir la mayoría de los no es de usuario código de la interfaz y obtener lo mejor de ambos mundos: escribir el código de lógica empresarial y de almacenamiento de datos una vez y no hay interfaces de usuario nativas en cada plataforma. Este documento describen un enfoque de arquitectura general para lograr este objetivo.

Este es un resumen de los puntos clave para crear aplicaciones multiplataforma de Xamarin:

-   **Usar C#** -escribir las aplicaciones en C#. Código existente escrito en C# se puede pasar a iOS y Android con Xamarin muy fácilmente y obviamente se utiliza en aplicaciones de Windows.
-   **Usar patrones de diseño MVC o MVVVM** -desarrollar la interfaz de usuario de la aplicación mediante el modelo de controlador, vista o modelo. Diseñar su aplicación mediante un enfoque de controlador, vista o modelo o un enfoque de vista de modelo/ViewModel donde hay una separación clara entre el "modelo" y el resto. Determinar qué partes de la aplicación se usando los elementos de interfaz de usuario nativa de cada plataforma (iOS, Android, Windows, Mac) y utilícelo como guía para dividir su aplicación en dos componentes: "Core" y "Interfaz de usuario".
-   **Crear interfaces de usuario nativas** -cada aplicación específicas del sistema operativo proporciona una capa de interfaz de usuario diferente (implementado en C# con la Ayuda de herramientas de diseño de interfaz de usuario nativas):

1.  En iOS, usar las APIs UIKit para crear aplicaciones de aspecto nativa, opcionalmente mediante el Diseñador de iOS de Xamarin para crear visualmente la interfaz de usuario.
1.  En Android, usar Android.Views para crear aplicaciones de aspecto nativa, al aprovechar la posibilidad de diseñador de interfaz de usuario de Xamarin.
1.  En Windows va a usar XAML para la capa de presentación, creado en el Diseñador de interfaz de usuario de Visual Studio o de mezcla.
1.  Mac, vaya a usar guiones gráficos para la capa de presentación, creada en Xcode.

Proyectos de Xamarin.Forms se admiten en todas las plataformas y permiten que crear interfaces de usuario que se pueden compartir entre plataformas con Xamarin.Forms XAML. 

La cantidad de reutilización del código dependerá en gran medida de la cantidad de código se mantiene en el núcleo compartido y la cantidad de código es la interfaz de usuario específica. El código básico es todo lo que no interactúa directamente con el usuario, sino que proporciona servicios para las partes de la aplicación que se recopilan y muestran esta información.

Para aumentar la cantidad de reutilización del código, puede adoptar multiplataforma componentes que proporcionan servicios comunes en todos estos sistemas, como:

1.   [SQLite net](https://www.nuget.org/packages/sqlite-net-pcl/) para el almacenamiento local de SQL,
1.   [Xamarin Plugins](https://xamarin.com/plugins) para tener acceso a funciones específicas del dispositivo, por ejemplo la cámara, contactos y la ubicación geográfica,
1.   [Paquetes de NuGet](https://nuget.org) que son compatibles con los proyectos de Xamarin, como [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/),
1.  Uso de características de .NET framework para las redes, servicios web, E/S y mucho más.


Algunos de estos componentes se implementan en el *Tasky* caso práctico.

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>Separar código reutilizable en una biblioteca de núcleo

Si sigue el principio de separación de la responsabilidad por capas de la arquitectura de la aplicación y, a continuación, mover la funcionalidad básica que es independiente de la plataforma en una biblioteca reutilizable core, puede maximizar el código compartir código entre plataformas, como la siguiente ilustración se muestran:

 ![](part-0-overview-images/layers2.png "Si sigue el principio de separación de la responsabilidad por capas de la arquitectura de la aplicación y, a continuación, mover la funcionalidad básica que es independiente de la plataforma en una biblioteca reutilizable core, puede maximizar el código compartir código entre plataformas")

 <a name="Case_Studies" />


## <a name="case-studies"></a>Casos prácticos

Hay un caso práctico que acompaña a este documento: *Tasky Pro*. Cada caso práctico describe la implementación de los conceptos que se describen en este documento, en un ejemplo del mundo real. El código es código abierto y está disponible en [github](https://github.com/xamarin/mobile-samples/).