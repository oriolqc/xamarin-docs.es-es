---
title: Generación de código .xib
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: b887dbf09693452f62f744669ad9713927020cea
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xib-code-generation"></a>Generación de código .xib

> [!IMPORTANT]
>  Este documento explica Visual Studio para la integración de Mac con el generador de interfaz de Xcode, ya que las acciones y las salidas no se usan en el Diseñador de Xamarin para iOS. Para obtener más información sobre el Diseñador de iOS, revise la [iOS diseñador](~/ios/user-interface/designer/index.md) documento.

La herramienta de generador de interfaz de Apple ("IB") se puede utilizar para diseñar visualmente las interfaces de usuario. Las definiciones de interfaz creadas por IB se guardan en **.xib** archivos. Widgets y otros objetos en **.xib** archivos pueden recibir una "clase identity", que puede ser un tipo personalizado definido por el usuario. Esto le permite personalizar el comportamiento de widgets y escribir widgets personalizados.

Estas clases de usuario normalmente son subclases de las clases de controlador de interfaz de usuario. Tienen *tomas* (análogas a las propiedades) y *acciones* (análogo a eventos) que se puede conectar a objetos de interfaz. En tiempo de ejecución, cuando se carga el archivo IB, se crean los objetos y las salidas y las acciones se conectan a los distintos objetos de interfaz de usuario de forma dinámica. Al definir estas clases administradas, debe definir todas las acciones y las salidas para que coincida con los que espera IB. Visual Studio para Mac usa un modelo de código subyacente similar para simplificar este proceso. Esto es similar a lo que hace Xcode para Objective-C, pero el modelo de generación de código y convenciones se ha ajustado para que sea mucho más familiar para los desarrolladores de .NET.

Trabajar con **.xib** archivos no se admite actualmente en Xamarin.iOS para Visual Studio.

## <a name="xib-files-and-custom-classes"></a>archivos de .xib y las clases personalizadas

Equivalente a tipos existentes de cacao Touch, es posible definir tipos personalizados en **.xib** archivos. También es posible utilizar tipos definidos en otros **.xib** archivos, o definir puramente en código de C#. Actualmente, el generador de interfaz no es consciente de los detalles de los tipos definidos fuera actual **.xib** de archivos, por lo que no se muestre o mostrar sus distribuidores personalizados y las acciones. Quitar esta limitación es prevista en el futuro.

Se pueden definir las clases personalizadas en un **.xib** archivo utilizando el comando "Agregar subclase" en la ficha "Clases" del generador de interfaz de. Nos referimos a ellas como clases de "Código subyacente". Si el **.xib** archivo tiene una ". xib.designer.cs" archivo equivalente en el proyecto, a continuación, Visual Studio para Mac rellenará automáticamente con las definiciones de clases parciales para todas las clases personalizadas en el **.xib**. Nos referimos a estas clases parciales como "clases de diseñador".

## <a name="generating-code"></a>Generar código

Para cualquier **{0} .xib** archivo con una acción de compilación de *página*, si un **{0}.xib.designer.cs** archivo también existe en el proyecto, Visual Studio para Mac generará clases parciales en el archivo del diseñador para todas las clases de usuario que se puede encontrar en el **.xib** archivo, con las propiedades para las salidas y métodos parciales para todas las acciones. Generación de código se habilita simplemente mediante la presencia de este archivo.

El archivo del diseñador es automáticamente se actualiza cuando el **.xib** archivo cambios y Visual Studio para Mac vuelve a obtener el foco. El archivo del diseñador no debe modificarse manualmente, como cambios serán sobrescrito próxima vez que Visual Studio para las actualizaciones de Mac el archivo.

## <a name="registration-and-namespaces"></a>Registro y espacios de nombres

Visual Studio para Mac genera las clases de diseñador utilizando el espacio de nombres del proyecto predeterminado para la ubicación de archivo del diseñador, para que sea coherente con namespacing de proyecto de .NET normal. El espacio de nombres de archivos de diseñador depende "espacio de nombres predeterminado" del proyecto y su configuración de "Políticas de nomenclatura. NET". Tenga en cuenta que si se cambia el espacio de nombres del proyecto predeterminado, MD volverá a generar las clases en el espacio de nombres nuevo, por lo que es posible que las clases parciales ya no coinciden.

Para hacer que la clase sea reconocible Objective-C en tiempo de ejecución, Visual Studio para Mac aplica un `[Register (name)]` a la clase de atributo. Aunque registra automáticamente Xamarin.iOS `NSObject`-las clases derivadas, utiliza los nombres de .NET completo. El atributo aplicado por Visual Studio para Mac invalida esta opción para asegurarse de cada clase se registra con el nombre utilizado en el **.xib** archivo. Si utiliza las clases personalizadas en IB sin utilizar Visual Studio para Mac para generar archivos de diseñador, tendrá que aplicar esta opción manualmente para hacer visibles las clases administradas coinciden con los nombres de clase C objetivo esperados.

No se puede definir clases en más de una **.xib**, o que entren en conflicto.

## <a name="non-designer-class-parts"></a>Partes de la clase de diseñador no

Las clases de diseñador parciales no están diseñadas para usarse como-es. Distribuidores son privadas y se especifica ninguna clase base. Se espera que cada clase tendrá una parte de "no-diseñador" clase correspondiente en otro archivo, que establece la clase base, utiliza o expone las salidas y define constructores necesarios para crear una instancia de la clase desde código nativo cuando se carga el **.xib**. El valor predeterminado **.xib** plantillas de hacer esto, pero para las clases personalizadas adicionales definen en un **.xib**, debe agregar el elemento de diseñador no manualmente.

La razón de ello es la necesidad de flexibilidad. Por ejemplo, varias clases de código subyacente podrían subclase que común administrada clase abstracta, las subclases de la clase que puede crear subclases por IB.

Es convencional para colocar este elemento un **{0}.xib.cs** archivo junto a la **.xib.designer.cs {0}** archivo del diseñador.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Distribuidores y acciones generadas

En las clases de diseñador parciales, Visual Studio para Mac genera propiedades que corresponden a los definidos en IB y métodos parciales correspondientes a las acciones conectadas las salidas conectadas.

### <a name="outlet-properties"></a>Propiedades de salida

Las clases de diseñador contienen propiedades que corresponden a todas las tomas definidas en la clase personalizada. El hecho de que se trata de propiedades es un detalle de implementación de la Xamarin.iOS al puente Objective C, para habilitar el enlace diferido. Debe considerar que sean equivalentes a los campos privados, diseñados para utilizarse sólo desde la clase de código subyacente. Si desea hacerlas públicas, agregar propiedades de descriptor de acceso a la parte de la clase de diseñador no, como lo haría con cualquier otro campo privado.

Si se definen propiedades de salida que tiene un tipo de `id` (equivalente a `NSObject`), a continuación, el generador de código del diseñador actualmente determina el tipo posibles más fuerte basándose en los objetos que se ha conectado a ese toma, para su comodidad.
Sin embargo, esto puede no admitirse en versiones futuras, por lo que se recomienda que explícitamente fuertemente tipado las salidas al definir la clase personalizada.

### <a name="action-properties"></a>Propiedades de la acción

Las clases de diseñador contienen métodos parciales correspondientes a todas las acciones definidas en la clase personalizada. Éstos son métodos sin una implementación. El propósito de los métodos parciales es doble:

1.  Si escribe `partial` en el cuerpo de la clase de la parte de la clase de diseñador no, Visual Studio para Mac le ofrece las firmas de todos los métodos parciales no implementa para Autocompletar.
2.  Las firmas de método parcial tienen aplicado un atributo que se expone al mundo Objective-C, por lo que puede tratar como la acción correspondiente.


Si lo desea, puede omitir el método parcial e implementar la acción aplicando el atributo a un método diferente o dejarlo pasar explícitamente a una clase base.

Si se definen las acciones que tiene un tipo de remitente de `id` (equivalente a `NSObject`), a continuación, el generador de código del diseñador actualmente determina el tipo posibles más fuerte en función de los objetos conectados a esa acción. Sin embargo, esto puede no admitirse en versiones futuras, por lo que se recomienda que explícitamente fuertemente tipado las acciones al definir la clase personalizada.

Tenga en cuenta que estos métodos parciales se crean solo en C#, porque CodeDOM no es compatible con los métodos parciales, por lo que no se generan para otros idiomas.

## <a name="cross-xib-class-usage"></a>Uso de la clase de XIB entre

En ocasiones, los usuarios desean hacer referencia a la misma clase de varios **.xib** archivos, por ejemplo con controladores de pestaña. Esto puede realizarse explícitamente que hacen referencia a la definición de clase de otro **.xib** de archivos o al definir el mismo nombre de clase nuevo en el segundo **.xib**.

El último caso puede ser problemático debido a Visual Studio para el procesamiento de Mac **.xib** archivos individualmente. No se puede detectar automáticamente y combinar las definiciones duplicadas, por lo que puede acabar con los conflictos de aplicar el atributo de registro varias veces cuando se define la misma clase parcial en varios archivos de diseñador. Las versiones recientes de Visual Studio para Mac intentan resolver este problema, pero puede que no siempre funcione según lo previsto. En el futuro esto es probable que quede no compatible y, en su lugar, Visual Studio para Mac hará que todos los tipos definidos en todos los **.xib** archivos y código administrado en el proyecto directamente visible de todos los **.xib** archivos.

## <a name="type-resolution"></a>Resolución de tipos

Tipos usados en IB son nombres de tipo C de objetivo. Éstos se asignan a tipos CLR mediante el uso de atributos de registro. Al generar el código de salida y la acción, Visual Studio para Mac resolver los tipos CLR correspondientes para todos los tipos de C objetivo ajustados por el núcleo de Xamarin.iOS y sus nombres de tipo completos.

Sin embargo, el generador de código actualmente no puede resolver los tipos CLR de los nombres de tipo de objetivo-C en código de usuario o en bibliotecas, por lo que en estos casos genera el nombre de tipo literalmente. Esto significa que el tipo CLR correspondiente debe tener el mismo nombre que el tipo C de objetivo y estar en el mismo espacio de nombres que el código que lo está usando. Se ha planeado corregirse algún tiempo en el futuro teniendo en cuenta todos los tipos de C de objetivo en el proyecto durante la generación de código.
