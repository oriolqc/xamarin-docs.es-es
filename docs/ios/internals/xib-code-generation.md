---
title: Generación de código de .xib en Xamarin.iOS
description: Este documento describe cómo Xamarin.iOS genera código para asignar archivos .xib C#, hacer que los controles visuales sean accesibles mediante programación.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 57988374b4383f5659e29edff3834958b8f99f1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103925"
---
# <a name="xib-code-generation-in-xamarinios"></a>Generación de código de .xib en Xamarin.iOS

> [!IMPORTANT]
>  Este documento explica con Visual Studio para la integración del equipo Mac con solo, Interface Builder de Xcode como las acciones y salidas no se usan en el Diseñador de Xamarin para iOS. Para obtener más información sobre el Diseñador de iOS, consulte el [iOS Designer](~/ios/user-interface/designer/index.md) documento.

La herramienta Apple Interface Builder ("IB") puede usarse para diseñar visualmente las interfaces de usuario. Las definiciones de interfaz creadas por IB se guardan en **.xib** archivos. Widgets y otros objetos de **.xib** pueden recibir archivos "identity clase", que puede ser un tipo personalizado definido por el usuario. Esto le permite personalizar el comportamiento de los widgets y escribir widgets personalizados.

Estas clases de usuario normalmente son subclases de clases de controlador de interfaz de usuario. Tienen *salidas* (análogas a las propiedades) y *acciones* (de manera similar a los eventos) que se pueden conectar a los objetos de interfaz. En tiempo de ejecución, cuando se carga el archivo IB, se crean los objetos y las salidas y acciones se conectan de forma dinámica a los distintos objetos de interfaz de usuario. Al definir estas clases administradas, debe definir todas las acciones y salidas para que coincida con los que espera IB. Visual Studio para Mac usa un modelo de código subyacente como para simplificar este proceso. Esto es similar a lo que hace Xcode para Objective-C, pero el modelo de generación de código y convenciones han se han modificado para que sea mucho más familiar para los desarrolladores. NET.

Trabajar con **.xib** archivos no se admite actualmente en Xamarin.iOS para Visual Studio.

## <a name="xib-files-and-custom-classes"></a>archivos .xib y clases personalizadas

Además de usar los tipos existentes de Cocoa Touch, es posible definir tipos personalizados en **.xib** archivos. También es posible usar tipos definidos en otros **.xib** los archivos o define exclusivamente en C# código. Actualmente, no es compatible con los detalles de los tipos definidos fuera actual Interface Builder **.xib** de archivos, por lo que no aparezcan ni mostrar sus salidas personalizadas y las acciones. Quitar esta limitación está prevista en el futuro.

Las clases personalizadas se pueden definir en un **.xib** de archivos mediante el comando "Agregar subclase" en la pestaña "Clases" de Interface Builder. Nos referimos a ellas como clases de "Código subyacente". Si el **.xib** archivo tiene un ". xib.designer.cs" archivo homólogo en el proyecto, a continuación, Visual Studio para Mac completa automáticamente con las definiciones de clases parciales para todas las clases personalizadas en el **.xib**. Nos referimos a estas clases parciales como "clases de diseñador".

## <a name="generating-code"></a>Generar código

Para cualquier  **{0}.xib** archivo con una acción de compilación *página*, si un  **{0}. xib.designer.cs** también existe el archivo en el proyecto, Visual Studio para Mac generará las clases parciales en el archivo del diseñador para todas las clases de usuario que puede encontrar en el **.xib** archivo, con las propiedades de las salidas y los métodos parciales para todas las acciones. Está habilitada la generación de código simplemente por la presencia de este archivo.

El archivo de diseñador es automáticamente cuando actualiza el **.xib** archivo cambios y Visual Studio para Mac vuelva a obtener el foco. El archivo del diseñador no debe modificarse manualmente, ya que los cambios estarán sobrescrito próxima vez que Visual Studio para las actualizaciones de Mac el archivo.

## <a name="registration-and-namespaces"></a>Registro y espacios de nombres

Visual Studio para Mac genera las clases de diseñador utilizando el espacio de nombres del proyecto predeterminado para la ubicación del archivo del diseñador, para que sea coherente con namespacing de proyecto de .NET normal. El espacio de nombres de los archivos de diseñador depende "espacio de nombres predeterminado" del proyecto y su configuración de "Directivas de nomenclatura de. NET". Tenga en cuenta que si cambia el espacio de nombres del proyecto predeterminado, MD volverá a generar las clases en el espacio de nombres nuevo, por lo que es posible que las clases parciales ya no coinciden.

Para que reconozca la clase por el tiempo de ejecución Objective-C, Visual Studio para Mac se aplica un `[Register (name)]` a la clase de atributo. Aunque se registra automáticamente Xamarin.iOS `NSObject`-las clases derivadas, utiliza los nombres de .NET completo. El atributo aplicado por Visual Studio para Mac invalida para asegurarse de cada clase se registra con el nombre usado en el **.xib** archivo. Si usa clases personalizadas en IB sin usar Visual Studio para Mac para generar los archivos de diseñador, tendrá que aplicar esto manualmente para que las clases administradas que coincida con los nombres de clase de Objective-C esperados.

No se puede definir clases en más de una **.xib**, o que entren en conflicto.

## <a name="non-designer-class-parts"></a>Partes de la clase que no sean de diseñador

Diseñadores clases parciales no están diseñadas para usarse como-es. Las salidas son privadas, y no se especifica ninguna clase base. Se espera que cada clase tendrán una parte de la clase "que no sean de diseñador" correspondiente en otro archivo, que establece la clase base, utiliza o expone las salidas y define constructores necesarios para crear una instancia de la clase desde código nativo al cargar el **.xib**. El valor predeterminado **.xib** plantillas para hacerlo, pero para las clases personalizadas adicionales se define en un **.xib**, debe agregar el elemento que no es diseñador manualmente.

La razón de esto es la necesidad de flexibilidad. Por ejemplo, varias clases de código subyacente podrían subclase que común administrada clase abstracta, cuyas subclases de la clase que se puede crear subclases por IB.

Es convencional colocarlos un  **{0}. xib.cs** archivo situada junto a la  **{0}. xib.designer.cs** archivo del diseñador.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Salidas y acciones generadas

En las clases de diseñador parciales, Visual Studio para Mac genera propiedades que corresponden a cualquier conectadas salidas definidas en IB y métodos parciales correspondientes a las acciones conectadas.

### <a name="outlet-properties"></a>Propiedades de salida

Las clases de diseñador contienen propiedades que corresponden a todas las salidas definidas en la clase personalizada. El hecho de que estas son propiedades es un detalle de implementación de Xamarin.iOS al puente Objective C, para habilitar el enlace diferido. Debe considerar que sean equivalentes a los campos privados, diseñados para utilizarse únicamente desde la clase CodeBehind. Si desea hacerlas públicas, agregar propiedades de descriptor de acceso a la parte de la clase que no es diseñador, como lo haría para cualquier otro campo privado.

Si se definen las propiedades de salida que tiene un tipo de `id` (equivalente a `NSObject`) a continuación, el generador de código del diseñador actualmente determina el tipo posibles más fuerte basándose en los objetos conectados a esa salida, para mayor comodidad.
Sin embargo, esto es posible que no se admite en versiones futuras, por lo que se recomienda que escriba fuertemente explícitamente las salidas al definir la clase personalizada.

### <a name="action-properties"></a>Propiedades de acción

Las clases de diseñador contienen métodos parciales correspondientes a todas las acciones definidas en la clase personalizada. Estos son métodos sin una implementación. El propósito de los métodos parciales es doble:

1.  Si escribe `partial` en el cuerpo de la clase de la parte de la clase que no es diseñador, Visual Studio para Mac ofrecerá a Autocompletar las firmas de todos los métodos parciales no implementado.
2.  Las firmas de método parcial tienen aplicado un atributo que se expone al mundo de Objective-C, por lo que puede tratar como la acción correspondiente.


Si lo desea, puede omitir el método parcial e implementar la acción aplicando el atributo a un método diferente o dejar que se pasen a una clase base.

Si se definen las acciones que tiene un tipo de remitente de `id` (equivalente a `NSObject`), a continuación, el generador de código del diseñador actualmente determina el tipo posibles más fuerte basándose en los objetos conectados a esa acción. Sin embargo, esto es posible que no se admite en versiones futuras, por lo que se recomienda que escriba fuertemente explícitamente las acciones al definir la clase personalizada.

Tenga en cuenta que estos métodos parciales se crean únicamente para C#, ya que CodeDOM no admite los métodos parciales, por lo que no se generan para otros idiomas.

## <a name="cross-xib-class-usage"></a>Uso de la clase entre XIB

A veces, los usuarios desean hacer referencia a la misma clase de varios **.xib** archivos, por ejemplo, con los controladores de ficha. Esto puede hacerse por explícitamente que hacen referencia a la definición de clase de otro **.xib** de archivos o al definir el mismo nombre de clase nuevo en el segundo **.xib**.

El último caso puede ser problemático debido a Visual Studio para el procesamiento de Mac **.xib** archivos individualmente. No se puede detectar automáticamente y combinar las definiciones duplicadas, por lo que es posible que acabe teniendo conflictos al aplicar el atributo registrar varias veces cuando se define la misma clase parcial en varios archivos de diseñador. Las versiones recientes de Visual Studio para Mac intentan resolver este problema, pero puede que no siempre funcione según lo previsto. En el futuro es probable que no serán compatibles y, en su lugar Visual Studio para Mac hará que todos los tipos definidos en todos los **.xib** archivos y código administrado en el proyecto directamente visible en todos los **.xib** archivos.

## <a name="type-resolution"></a>Resolución de tipos

Tipos utilizados en IB son nombres de tipo C de objetivo. Estos se asignan a tipos CLR mediante el uso de atributos de registro. Al generar el código de salida y la acción, Visual Studio para Mac resolver los tipos CLR correspondientes para todos los tipos de Objective-C ajustados por el núcleo de Xamarin.iOS y sus nombres de tipo completos.

Sin embargo, el generador de código actualmente no puede resolver los tipos CLR en los nombres de tipos de Objective-C en código de usuario o en bibliotecas, por lo que genera el nombre del tipo textual en estos casos. Esto significa que el tipo CLR correspondiente debe tener el mismo nombre que el tipo de Objective-C y estar en el mismo espacio de nombres que el código que está usando. Esto se prevé fijar en algún momento en el futuro considerando todos los tipos de Objective-C en el proyecto durante la generación de código.
