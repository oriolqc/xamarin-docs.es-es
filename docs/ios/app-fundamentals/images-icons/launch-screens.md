---
title: Abrir pantallas
description: "En este artículo se explica cómo crear una pantalla de inicio de la aplicación para todos los dispositivos de iOS, en cualquier resolución y la orientación, con un solo guión gráfico unificado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/19/2018
ms.openlocfilehash: 54ec41636f491708ea72585d3889fbbca85c8eb1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="launch-screens"></a>Abrir pantallas

_En este artículo se explica cómo crear una pantalla de inicio de la aplicación para todos los dispositivos de iOS, en cualquier resolución y la orientación, con un solo guión gráfico unificado._

Antes de iOS 8, crear una pantalla de inicio de una aplicación de iOS el desarrollador tenía que proporcionar un recurso de imagen para cada uno de los distintos factores de forma de dispositivo y las soluciones en el que puede ejecutar la aplicación. Sin embargo, desde la versión de iOS 8, ha sido posible utilizar un solo guión gráfico unificado para crear una pantalla de inicio que tenga la apariencia correcta en todos los casos.

En este breve tutorial describe cómo crear una pantalla de inicio, ya sea un guión gráfico proporcionada de forma predeterminada en un proyecto nuevo o con un guión gráfico agregado manualmente a un proyecto existente. A continuación, se muestra cómo utilizar el Diseñador de iOS para agregar una vista de imagen y una etiqueta al guión gráfico, para establecer restricciones en esas vistas y para comprobar que el guión gráfico está correcto para varios dispositivos y orientaciones.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Administración de las pantallas de inicio con guiones gráficos

En iOS 8 (y versiones posteriores), el programador puede crear un guion especial unificado gráfico para proporcionar la pantalla Inicio en lugar de usar una o varias imágenes de inicio estático. Al crear un inicio de guión gráfico en el Diseñador de iOS, utilice las clases de tamaño y diseño automático para definir diseños diferentes para los diferentes entornos. Mediante el uso de las clases de tamaño y diseño automático, el desarrollador puede crear una pantalla de inicio único que se ve bien en todos los dispositivos y mostrar los entornos.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En Visual Studio para Mac, cree un nuevo proyecto seleccionando **archivo > nueva solución** y, a continuación, elija **ver solo la aplicación**: 

    ![La ventana nuevo proyecto, con la única aplicación de la vista seleccionada](launch-screens-images/launch01.png)

    - De forma predeterminada, un nuevo proyecto incluye una **LaunchScreen.storyboard** archivo que define la interfaz de la pantalla Inicio. 
    - Para agregar en su lugar un guión gráfico iniciar pantalla a un proyecto existente, haga doble clic en el nombre del proyecto en el **solución Pad** y elija **Agregar > nuevo archivo...**  y, a continuación, seleccione **iniciar pantalla**:

    ![La ventana de archivo nuevo, con iOS iniciar pantalla seleccionada](launch-screens-images/launch01b.png)

    - Asignar nombre al archivo **LaunchScreen** u otro nombre de su elección.

2. Configurar el proyecto para que use el guión gráfico adecuado para su pantalla de inicio:

    - Haga doble clic en el **Info.plist** un archivo en el **solución Pad** para abrirlo y editarlo.
    - En el **iniciar imágenes** sección, asegúrese de que **iniciar pantalla** se establece en el nombre del guión gráfico adecuado:

    ![El selector de pantalla de inicio de Info.plist](launch-screens-images/launch02.png)

    - De forma predeterminada, se configura un nuevo proyecto para usar **LaunchScreen.storyboard** como su pantalla de inicio.

3. Agregar una imagen a la **Assets.xcassets** activos de catálogo para que esté disponible para su uso en la pantalla Inicio. Para obtener más información, consulte el [agregar imágenes a un conjunto de imágenes de catálogo de Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sección de la [mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guía.

4. Abra **LaunchScreen.storyboard** para su edición haciendo doble clic en él en el **panel de la solución**.

5. Elija un dispositivo y la orientación en el que se va a obtener una vista previa de la pantalla Iniciar, guión gráfico en el Diseñador de iOS. Abra el panel de selección de dispositivo en la barra de herramientas de la parte inferior y seleccione **iPhone 4S** y **vertical**.

    ![La barra de herramientas de selección de dispositivo](launch-screens-images/launch05.png)

    - Tenga en cuenta que si selecciona un dispositivo y la orientación solo se cambia cómo el Diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección que realice aquí, recién agregado las restricciones se aplican en todos los dispositivos y orientaciones a menos que la **editar rasgos** botón se ha utilizado para que se especifique lo contrario. 

6. Establecer el **fondo** color de la vista principal del controlador de vista. Seleccione la vista haciendo clic en el medio de View Controller y ajustar el color de fondo utilizando el **panel de propiedades**:

    ![Una vista única con un color de fondo de color púrpura](launch-screens-images/launch06.png)

7. Agregar un **imagen vista** a la pantalla Inicio y establezca su origen **imagen**:

    - Arrastre un **imagen vista** desde el **panel cuadro de herramientas** al centro de la vista.
    - Con el **imagen vista** seleccionado, en la **Widget** sección de la **panel de propiedades** establecer el **imagen** propiedad al conjunto de imagen ya agrega a la **Assets.xcassets** catálogo de activos. Cambie la posición y cambiar el tamaño de la **imagen vista** según sea necesario:
    
    ![Vista de la imagen con su conjunto de propiedades de imagen](launch-screens-images/launch07.png)

8. Agregar un **etiqueta** a continuación el **imagen vista** y usar el **panel de propiedades** para establecer sus atributos: 

    ![Una etiqueta con su conjunto de texto y el color](launch-screens-images/launch08.png)

9. Cambiar al modo de edición de restricción mediante el botón derecho en el **barra de herramientas de restricciones**:
    
    ![El botón de modo de edición de restricción](launch-screens-images/launch09.png)

10. Agregar restricciones a la **imagen vista**, establezca el alto y ancho y Centrar horizontalmente y verticalmente:

    ![Vista de la imagen con las restricciones de diseño](launch-screens-images/launch10.png)

    - Para obtener más información sobre cómo agregar restricciones, consulte [Autodiseño con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Agregar restricciones a la **etiqueta**, Centrar horizontalmente, dándole un alto y ancho y posición fija de distancia vertical de la **imagen vista**:

    ![Una etiqueta con las restricciones de diseño](launch-screens-images/launch11.png)

12. Pruebe otros dispositivos y orientaciones para comprobar que el diseño está según lo previsto en todos los escenarios. En casos donde es necesario realizar para un dispositivo específico u orientación ajustes, utilice la **editar rasgos** botón Agregar restricciones para las clases de tamaño específico:

    ![La pantalla de inicio se representa como un iPhone X con orientación horizontal](launch-screens-images/launch12.png)

13. Guarde los cambios en el guión gráfico. Ejecutar la aplicación en un simulador o el dispositivo y la pantalla Inicio estará visible cuando se inicie la aplicación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Cree un nuevo proyecto. En Visual Studio, seleccione **archivo > Nuevo > proyecto**y, a continuación, elija **ver solo la aplicación (iPhone)**:
    
    ![La ventana de nuevo proyecto, con la única aplicación de vista (iPhone) seleccionada](launch-screens-images/launch01-vs.png)

    - Denomine el proyecto, elija una ubicación y seleccione **Aceptar**.

2. Si **recursos > LaunchScreen.xib** existe en el **el Explorador de soluciones**, eliminar, haga doble clic en el archivo y elija **eliminar**. Este archivo se sustituirán por un guión gráfico en el paso siguiente.

3. Crear un guión gráfico que se usará como la pantalla Inicio. En el **el Explorador de soluciones**, haga doble clic en el proyecto y elija **Agregar > nuevo elemento...**  seguido **guión gráfico vacío**. Nombre de este guión gráfico **LaunchScreen.storyboard** y haga clic en **agregar**:

    ![La ventana Agregar nuevo elemento, con el guión gráfico vacío seleccionado](launch-screens-images/launch03-vs.png)

4. Configurar el proyecto para usar **LaunchScreen.storyboard** como su guión gráfico iniciar pantalla:

    - Haga doble clic en el archivo **Info.plist**, en el **Explorador de soluciones**, para abrirlo para su edición. 
    - En el **activos visuales** pestaña, establezca **iniciar pantalla** a **LaunchScreen**.

    ![El selector de pantalla de inicio de Info.plist](launch-screens-images/launch04-vs.png)

5. Agregar una imagen a un catálogo de activos en el proyecto para que esté disponible para su uso en la pantalla de inicio:

    - En el **el Explorador de soluciones**, haga doble clic en **Asset catálogos** y seleccione **agregar el catálogo de activos**. Nombre de este nuevo catálogo de Asset **activos**:

    ![La ventana Agregar nuevo elemento, con el catálogo de activos seleccionada](launch-screens-images/launch05-vs.png)

    - Agregar un nuevo conjunto de imagen a la **activos** catálogo de activos, como se describe en el [agregar imágenes a un conjunto de imágenes de catálogo de Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sección de la [mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guía.

6. Abra **LaunchScreen.storyboard** para su edición haciendo doble clic en él en el **el Explorador de soluciones**.

    - Para editar un archivo de guión gráfico, Visual Studio necesita una conexión activa a un host de compilación de Mac. Consulte la [conectarse al equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) guía para obtener más información.

7. Elija un dispositivo y la orientación en el que se va a obtener una vista previa de la pantalla Iniciar, guión gráfico en el Diseñador de iOS. Abra el panel de selección de dispositivo en la barra de herramientas de la parte inferior y seleccione **iPhone 4S** y **vertical**: 
 
    ![La barra de herramientas de selección de dispositivo](launch-screens-images/launch07-vs.png)

    - Tenga en cuenta que si selecciona un dispositivo y la orientación solo se cambia cómo el Diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección que realice aquí, recién agregado las restricciones se aplican en todos los dispositivos y orientaciones a menos que la **editar rasgos** botón se ha utilizado para que se especifique lo contrario. 

8. Agregar un **View Controller** al guión gráfico, arrastre uno de los **cuadro de herramientas** a la superficie de diseño: 

    ![Vacío View Controller agrega a la superficie de diseño](launch-screens-images/launch08-vs.png)

9. Establecer el **fondo** color de la vista principal del controlador de vista. Seleccione la vista haciendo clic en el medio de View Controller y ajustar el color de fondo mediante el el **ventana propiedades**:
    
    ![Una vista única con un color de fondo de color púrpura](launch-screens-images/launch09-vs.png)

10. Agregar un **imagen vista** a la pantalla Inicio y establezca su origen **imagen**:

    - Arrastre un **imagen vista** desde el **cuadro de herramientas** al centro de la vista.
    - Con el **imagen vista** aún seleccionado, en la **Widget** sección de la **ventana propiedades** establecer el **imagen** propiedad al conjunto de imagen ya se agregó a la **activos** catálogo de activos. Cambie la posición y cambiar el tamaño de la **imagen vista** según sea necesario:
    
    ![Vista de la imagen con su conjunto de propiedades de imagen](launch-screens-images/launch10-vs.png)

11. Agregar un **etiqueta** a continuación el **la imagen de vista**:

    - Arrastre un **etiqueta** desde el **cuadro de herramientas** a la superficie de diseño, colocar a continuación el **imagen de vista**.
    - Establecer atributos de la **etiqueta** mediante la **ventana propiedades**:

    ![Una etiqueta con su conjunto de texto y el color](launch-screens-images/launch11-vs.png) 

12. Cambiar al modo de edición de restricción mediante el botón derecho en el **barra de herramientas de restricciones**:
    
    ![El botón de modo de edición de restricción](launch-screens-images/launch12-vs.png) 

13. Agregar restricciones a la **imagen vista**, establezca el alto y ancho y Centrar horizontalmente y verticalmente:

    ![Vista de la imagen con las restricciones de diseño](launch-screens-images/launch13-vs.png) 

    - Para obtener información acerca de cómo agregar restricciones, consulte [Autodiseño con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Agregar restricciones a la **etiqueta**, Centrar horizontalmente, dándole un alto y ancho y posición fija de distancia vertical de la **imagen vista**:
    
    ![Una etiqueta con las restricciones de diseño](launch-screens-images/launch14-vs.png) 

15. Pruebe otros dispositivos y orientaciones para comprobar que el diseño está según lo previsto en todos los escenarios. En casos donde es necesario realizar para un dispositivo específico u orientación ajustes, utilice la **editar rasgos** botón Agregar restricciones para las clases de tamaño específico:

    ![La pantalla de inicio se representa como un iPhone X con orientación horizontal](launch-screens-images/launch15-vs.png) 

16. Guarde los cambios en el guión gráfico. Ejecutar la aplicación en un simulador o el dispositivo y la pantalla Inicio estará visible cuando se inicie la aplicación.

-----

> [!NOTE]
> **Tenga en cuenta**: guión gráfico de A usar como una pantalla de inicio _debe_ incluir elementos de interfaz de usuario solo simples e integrados y **no** no los cálculos o derivar una clase personalizada.

Para obtener más información acerca de cómo crear una pantalla de inicio con un guión gráfico unificado, vea el [pantallas de inicio dinámica](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) sección de la [guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) guía.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrar para iniciar guiones gráficos de pantalla

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Al actualizar una aplicación existente para usar guiones gráficos para sus pantallas de inicio, haga clic con el **nombre del proyecto** en el **el Explorador de soluciones** y seleccione **agregar**  >  **Nuevo archivo...** . Seleccione **iOS** > **iniciar pantalla** y haga clic en el **New** botón:

![](launch-screens-images/storyboard02.png "Seleccione una pantalla de inicio de iOS")

A continuación, haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo. En **iniciar pantalla**, seleccione el nuevo archivo de guión gráfico creado anteriormente.

![](launch-screens-images/storyboard09.png "Seleccione el nuevo archivo de guión gráfico creado anteriormente")


Para usar el nuevo guión gráfico como una pantalla de inicio, haga lo siguiente:

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hasta la **Universal imágenes iniciar** sección del editor, abra el **iniciar pantalla** lista desplegable y seleccione el nombre del guión gráfico creado anteriormente: 

    ![](launch-screens-images/storyboard08.png "Si la pantalla de inicio se establece en el guión gráfico")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...** : 

    ![](launch-screens-images/image012.png "Agregar nuevo archivo")
2. Escriba un nombre para la pantalla de inicio y haga clic en el **agregar** botón: 

    ![](launch-screens-images/image013.png "Escriba un nombre para la pantalla de inicio")
3. En el **el Explorador de soluciones**, haga doble clic en el archivo de guión gráfico recién creada para abrirlo y editarlo.
4. Asegúrese de que el **tamaño clase** está establecido en **cualquier: cualquier** y **vista como** es **genérico**: 

    ![](launch-screens-images/image016.png "Asegúrese de que la clase de tamaño se establece en uno: cualquier y la vista como sea genérica")
5. La pantalla de inicio de las clases de tamaño, elementos de interfaz de usuario simples del ensamblado (como `UIImageView`) y las imágenes que ha incluido en el paquete de la aplicación: 

    ![](launch-screens-images/image017.png "Ensamblado de la pantalla de inicio en el Diseñador de iOS")
6. Guarde los cambios en el guión gráfico.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Pantallas de inicio dinámica (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Conceptos básicos de iOS Designer](~/ios/user-interface/designer/index.md)
- [Agregar imágenes a una imagen de catálogo de Asset configuradas](~/ios/app-fundamentals/images-icons/displaying-an-image.md#asset-catalogs)
- [Diseño automático con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Directrices de interfaz humana: Pantalla de inicio](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
