---
title: Pantallas de inicio para aplicaciones Xamarin.iOS
description: En este artículo se explica cómo crear una pantalla de inicio de la aplicación para todos los dispositivos iOS, en cualquier resolución y la orientación, mediante un solo guión gráfico unificado.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2018
ms.openlocfilehash: 0ec1defa29a4fe85c4ae3e809d8733e68cc268ac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116933"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Pantallas de inicio para aplicaciones Xamarin.iOS

_En este artículo se explica cómo crear una pantalla de inicio de la aplicación para todos los dispositivos iOS, en cualquier resolución y la orientación, mediante un solo guión gráfico unificado._

IOS 8, antes de crear una pantalla de inicio para una aplicación iOS el desarrollador tenía que proporcionar un recurso de imagen para cada uno de los distintos factores de forma y las soluciones en la que podría ejecutar la aplicación. Sin embargo, desde la versión de iOS 8, ha sido posible usar un solo guión gráfico unificado para crear una pantalla de inicio que parece correcta en todos los casos.

En este breve tutorial describe cómo crear una pantalla de inicio, ya sea un guión gráfico proporcionado de forma predeterminada en un proyecto nuevo o con un guión gráfico agregado manualmente a un proyecto existente. A continuación, se muestra cómo usar el Diseñador de iOS para agregar una vista de imagen y una etiqueta al guión gráfico, para establecer las restricciones en esas vistas y para comprobar que el guion gráfico parece correcto para varios dispositivos y orientaciones.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Administración de las pantallas de inicio con guiones gráficos

En iOS 8 (y versiones posteriores), el desarrollador puede crear un Storyboard unificada especial para proporcionar la pantalla de inicio en lugar de usar una o más imágenes de inicio estático. Al crear un guion gráfico de inicio en el Diseñador de iOS, utilice las clases de tamaño y el diseño automático para definir diseños diferentes para diferentes entornos. Mediante las clases de tamaño y el diseño automático, el desarrollador puede crear una pantalla de inicio único que se vea bien en todos los dispositivos y mostrar los entornos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En Visual Studio para Mac, cree un nuevo proyecto seleccionando **archivo > nueva solución** y, a continuación, elija **aplicación de vista única**: 

    ![La ventana nuevo proyecto, con la única aplicación de la vista seleccionada](launch-screens-images/launch01.png)

    - De forma predeterminada, un nuevo proyecto incluye un **LaunchScreen.storyboard** archivo que define la interfaz de la pantalla de inicio. 
    - Para agregar en su lugar un guión gráfico de la pantalla de inicio a un proyecto existente, haga doble clic en el nombre del proyecto en el **panel de solución** y elija **Agregar > nuevo archivo...**  y, a continuación, seleccione **pantalla de inicio**:

    ![La ventana de archivo nuevo, con iOS seleccionado en pantalla de inicio](launch-screens-images/launch01b.png)

    - Nombre del archivo **LaunchScreen** u otro nombre de su elección.

2. Configurar el proyecto para que use el guión gráfico adecuado para su pantalla de inicio:

    - Haga doble clic en el **Info.plist** de archivos en el **panel de solución** para abrirlo y editarlo.
    - En el **imágenes de inicio** sección, asegúrese de que **pantalla de inicio** se establece en el nombre del guión gráfico adecuado:

    ![El selector de pantalla de inicio de Info.plist](launch-screens-images/launch02.png)

    - De forma predeterminada, un nuevo proyecto está configurado para usar **LaunchScreen.storyboard** como pantalla de inicio.

3. Agregar una imagen a la **Assets.xcassets** recurso de catálogo para que esté disponible para su uso en la pantalla Inicio. Para obtener más información, consulte el [agregar imágenes a un conjunto de imágenes de catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sección de la [mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guía.

4. Abra **LaunchScreen.storyboard** para su edición haciendo doble clic en él en el **panel de solución**.

5. Elija un dispositivo y la orientación en el que se va a obtener una vista previa de la pantalla Iniciar, guión gráfico en el Diseñador de iOS. Abra el panel de selección de dispositivo en la barra de herramientas inferior y seleccione **iPhone 4S** y **vertical**.

    ![La barra de herramientas de selección de dispositivo](launch-screens-images/launch05.png)

    - Tenga en cuenta que cuando se activa un dispositivo y orientación solo cambia cómo el Diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección que realice aquí, recién agregado las restricciones se aplican en todos los dispositivos y orientaciones, a menos que el **editar rasgos** botón se ha usado para que se especifique lo contrario. 

6. Establecer el **en segundo plano** color de la vista principal del controlador de vista. Seleccione la vista haciendo clic en el controlador de vista en el medio y ajustar el color de fondo utilizando el **panel de propiedades**:

    ![Una vista única con un color de fondo de color púrpura](launch-screens-images/launch06.png)

7. Agregar un **vista de imagen** a la pantalla de inicio y establezca su origen **imagen**:

    - Arrastre un **vista de imagen** desde el **panel cuadro de herramientas** al centro de la vista.
    - Con el **vista de imagen** seleccionado, en el **Widget** sección de la **panel de propiedades** establecer el **imagen** propiedad a la imagen ya establecido agrega a la **Assets.xcassets** catálogo de recursos. Cambie la posición y tamaño de la **vista de imagen** según sea necesario:
    
    ![Vista de la imagen con su conjunto de propiedades de imagen](launch-screens-images/launch07.png)

8. Agregar un **etiqueta** a continuación el **vista de imagen** y usar el **panel de propiedades** para establecer sus atributos: 

    ![Una etiqueta con su conjunto de texto y el color](launch-screens-images/launch08.png)

9. Cambiar al modo de edición de la restricción con el botón derecho en el **barra de herramientas de restricciones**:
    
    ![El botón de modo de edición de restricción](launch-screens-images/launch09.png)

10. Agregar restricciones a la **vista de imagen**, establezca el alto y ancho y Centrar horizontalmente y verticalmente:

    ![Vista de la imagen con las restricciones de diseño](launch-screens-images/launch10.png)

    - Para obtener más información sobre cómo agregar restricciones, vea [diseño automático con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Agregar restricciones a la **etiqueta**, Centrar horizontalmente, dándole un alto y ancho y posicionamiento fijo verticalmente a distancia desde la **vista de imagen**:

    ![Una etiqueta con las restricciones de diseño](launch-screens-images/launch11.png)

12. Pruebe otros dispositivos y orientaciones para comprobar que el diseño tiene un aspecto según lo previsto en todos los escenarios. En casos donde es necesario realizar para un dispositivo específico o la orientación de los ajustes, utilice la **editar rasgos** para agregar restricciones para las clases de tamaño específico:

    ![La pantalla de inicio se representa como un iPhone X con orientación horizontal](launch-screens-images/launch12.png)

13. Guarde los cambios en el guión gráfico. Ejecute la aplicación en un simulador o dispositivo, y la pantalla de inicio estará visible cuando se inicia la aplicación.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cree un nuevo proyecto. En Visual Studio, seleccione **archivo > Nuevo > proyecto > Visual C# > iPhone & iPad > iOS (Xamarin) de la aplicación**:

    ![La ventana de proyecto nuevo, con iOS (Xamarin) de la aplicación seleccionada](launch-screens-images/launch01.w157.png)

    Seleccione el **aplicación de vista única** plantilla y, a continuación, haga clic en **Aceptar**:

    ![Plantilla de aplicación de vista única](launch-screens-images/launch01-2.w157.png)

2. Si **recursos > LaunchScreen.xib** existe en el **el Explorador de soluciones**, eliminarla, haga clic en el archivo y elegir **eliminar**. Este archivo se reemplazará con un guión gráfico en el paso siguiente.

3. Crear un guion gráfico que se usará como la pantalla de inicio. En el **el Explorador de soluciones**, haga doble clic en el proyecto y elija **Agregar > nuevo elemento...**  seguido **guion gráfico vacío**. Nombre de este guión gráfico **LaunchScreen.storyboard** y haga clic en **agregar**:

    ![La ventana Agregar nuevo elemento, con guion gráfico vacío seleccionado](launch-screens-images/launch03.w157.png)

4. Configurar el proyecto para usar **LaunchScreen.storyboard** como su guión gráfico de la pantalla de inicio:

    - Haga doble clic en el archivo **Info.plist**, en el **Explorador de soluciones**, para abrirlo para su edición. 
    - En el **activos visuales** pestaña, establezca **pantalla de inicio** a **LaunchScreen**.

    ![El selector de pantalla de inicio de Info.plist](launch-screens-images/launch04-vs.png)

5. Agregar una imagen a un catálogo de recursos en el proyecto para que esté disponible para su uso en la pantalla de inicio:

    - En el **el Explorador de soluciones**, haga doble clic en **catálogos de activos** y seleccione **Agregar catálogo de activos**. Nombre de este nuevo catálogo **activos**:

    ![La ventana Agregar nuevo elemento, con el catálogo de recursos seleccionado](launch-screens-images/launch05.w157.png)

    - Agregar un nuevo conjunto de imagen a la **activos** catálogo de recursos, como se describe en el [agregar imágenes a un conjunto de imágenes de catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sección de la [mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guía.

6. Abra **LaunchScreen.storyboard** para su edición haciendo doble clic en él en el **el Explorador de soluciones**.

    - Para editar un archivo de guión gráfico, Visual Studio necesita una conexión activa a un host de compilación de Mac. Consulte la [conectarse al equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) guía para obtener más información.

7. Elija un dispositivo y la orientación en el que se va a obtener una vista previa de la pantalla Iniciar, guión gráfico en el Diseñador de iOS. Abra el panel de selección de dispositivo en la barra de herramientas inferior y seleccione **iPhone 4S** y **vertical**: 
 
    ![La barra de herramientas de selección de dispositivo](launch-screens-images/launch07-vs.png)

    - Tenga en cuenta que cuando se activa un dispositivo y orientación solo cambia cómo el Diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección que realice aquí, recién agregado las restricciones se aplican en todos los dispositivos y orientaciones, a menos que el **editar rasgos** botón se ha usado para que se especifique lo contrario. 

8. Agregar un **View Controller** al guión gráfico arrastrando uno de los **cuadro de herramientas** a la superficie de diseño: 

    ![Controlador de vista de vacío agregado a la superficie de diseño](launch-screens-images/launch08-vs.png)

9. Establecer el **en segundo plano** color de la vista principal del controlador de vista. Seleccione la vista haciendo clic en el controlador de vista en el medio y ajustar el color de fondo utilizando el **ventana propiedades**:
    
    ![Una vista única con un color de fondo de color púrpura](launch-screens-images/launch09-vs.png)

10. Agregar un **vista de imagen** a la pantalla de inicio y establezca su origen **imagen**:

    - Arrastre un **vista de imagen** desde el **cuadro de herramientas** al centro de la vista.
    - Con el **vista de imagen** aún seleccionado, en el **Widget** sección de la **ventana propiedades** establecer el **imagen** propiedad al conjunto de imágenes ya ha agregado a la **activos** catálogo de recursos. Cambie la posición y tamaño de la **vista de imagen** según sea necesario:
    
    ![Vista de la imagen con su conjunto de propiedades de imagen](launch-screens-images/launch10-vs.png)

11. Agregar un **etiqueta** a continuación el **la imagen de vista**:

    - Arrastre un **etiqueta** desde el **cuadro de herramientas** a la superficie de diseño, colocar a continuación el **vista de imagen**.
    - Establecimiento de atributos para el **etiqueta** utilizando el **ventana propiedades**:

    ![Una etiqueta con su conjunto de texto y el color](launch-screens-images/launch11-vs.png) 

12. Cambiar al modo de edición de la restricción con el botón derecho en el **barra de herramientas de restricciones**:
    
    ![El botón de modo de edición de restricción](launch-screens-images/launch12-vs.png) 

13. Agregar restricciones a la **vista de imagen**, establezca el alto y ancho y Centrar horizontalmente y verticalmente:

    ![Vista de la imagen con las restricciones de diseño](launch-screens-images/launch13-vs.png) 

    - Para obtener información acerca de cómo agregar restricciones, vea [diseño automático con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Agregar restricciones a la **etiqueta**, Centrar horizontalmente, dándole un alto y ancho y posicionamiento fijo verticalmente a distancia desde la **vista de imagen**:
    
    ![Una etiqueta con las restricciones de diseño](launch-screens-images/launch14-vs.png) 

15. Pruebe otros dispositivos y orientaciones para comprobar que el diseño tiene un aspecto según lo previsto en todos los escenarios. En casos donde es necesario realizar para un dispositivo específico o la orientación de los ajustes, utilice la **editar rasgos** para agregar restricciones para las clases de tamaño específico:

    ![La pantalla de inicio se representa como un iPhone X con orientación horizontal](launch-screens-images/launch15-vs.png) 

16. Guarde los cambios en el guión gráfico. Ejecute la aplicación en un simulador o dispositivo, y la pantalla de inicio estará visible cuando se inicia la aplicación.

-----

> [!NOTE]
> Un guión gráfico que se usa como una pantalla de inicio _debe_ incluir elementos de interfaz de usuario solo simples e integrados y **no** hacer los cálculos o derivar una clase personalizada.

Para obtener más información acerca de cómo crear una pantalla de inicio con un guión gráfico unificado, vea el [pantallas de inicio dinámica](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) sección de la [guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guía.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrar a iniciar guiones gráficos de pantalla

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Al actualizar una aplicación existente para usar guiones gráficos para pantallas de inicio, haga clic la **nombre del proyecto** en el **el Explorador de soluciones** y seleccione **agregar**  >  **Nuevo archivo...** . Seleccione **iOS** > **pantalla de inicio** y haga clic en el **New** botón:

![](launch-screens-images/storyboard02.png "Seleccione una pantalla de inicio de iOS")

A continuación, haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo. En **pantalla de inicio**, seleccione el nuevo archivo de guion gráfico creado anteriormente.

![](launch-screens-images/storyboard09.png "Seleccione el nuevo archivo de guion gráfico creado anteriormente")


Para usar el nuevo guion gráfico como una pantalla de inicio, haga lo siguiente:

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hasta la **imágenes de inicio Universal** sección del editor, abrirlo el **pantalla de inicio** lista desplegable y seleccione el nombre del guion gráfico creado anteriormente: 

    ![](launch-screens-images/storyboard08.png "Si la pantalla de inicio se establece en el guión gráfico")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...** : 

    ![](launch-screens-images/image012.png "Agregar nuevo archivo")
2. Escriba un nombre para la pantalla de inicio y haga clic en el **agregar** botón: 

    ![](launch-screens-images/image013.png "Escriba un nombre para la pantalla de inicio")
3. En el **el Explorador de soluciones**, haga doble clic en el archivo de guión gráfico recién creada para abrirlo y editarlo.
4. Asegúrese de que el **clase de tamaño** está establecido en **cualquier: cualquier** y **ver como** es **genérico**: 

    ![](launch-screens-images/image016.png "Asegúrese de que la clase de tamaño se establece en uno: cualquier y ver como es genérica")
5. Ensamblado de la pantalla de inicio de las clases de tamaño, elementos de interfaz de usuario simples (como `UIImageView`) y las imágenes que se hayan incluido en el paquete de la aplicación: 

    ![](launch-screens-images/image017.png "Ensamblado de la pantalla de inicio en el Diseñador de iOS")
6. Guarde los cambios en el guión gráfico.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Pantallas de inicio dinámico (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Conceptos básicos de iOS Designer](~/ios/user-interface/designer/index.md)
- [Establecer agregar imágenes a una imagen del catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Diseño automático con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Directrices de interfaz humana: Pantalla de inicio](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
