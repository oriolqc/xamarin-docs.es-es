---
title: Trabajar con iconos e imágenes en Xamarin de tvOS
description: Este documento describe cómo trabajar con iconos e imágenes en una aplicación de tvOS con Xamarin. Analiza imágenes de inicio, imágenes en capas, el icono de la aplicación y mucho más.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: dab1b0f7bf7aabb4dfcfbfdcb5e202baa48e664d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865163"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Trabajar con iconos e imágenes en Xamarin de tvOS

Creación de atractivas iconos e imágenes son una parte fundamental de desarrollar una experiencia de usuario amplias para las aplicaciones de Apple TV. Esta guía describen los pasos necesarios para crear e incluir los activos gráficos necesarios para las aplicaciones Xamarin.tvOS:

- [Imagen de inicio](#Launch-Image) -una imagen de inicio que se muestra cuando la aplicación se inicia por primera vez y se reemplaza por la primera pantalla de la aplicación cuando se haya completado el inicio.
- [Imágenes con capas](#Layered-Images) - específica a Apple TV, nuevos trabajos de imágenes con capas de Apple con el efecto de paralaje para crear un efecto 3D para los elementos seleccionados. Hay varias maneras de [crear imágenes con capas](#Creating-Layered-Images).
- [Icono de la aplicación](#App-Icons) -los iconos son necesarios para la pantalla no solo Apple TV inicio pero para la aplicación de Store. Debe proporcionarse como una imagen en capas.
- [Imagen del estante de principales](#Top-Shelf-Image) -si la aplicación se coloca en la fila superior de la pantalla Inicio, necesitará una imagen del estante superior para resaltar las características de la aplicación. Opcionalmente, puede proporcionar [contenido dinámico de estante superior](#Dynamic-Top-Shelf-Content) para resaltar el contenido de la aplicación.
- [Las imágenes del centro de juegos](#Game-Center-Images) -si la aplicación es un juego y usa Game Center, se le solicitará varias imágenes adicionales.
- [Configurar imágenes de proyectos Xamarin.tvOS](#Setting-Xamarin.tvOS-Project-Images) -cubre los pasos necesarios para establecer la imagen de inicio y el icono de la aplicación para la aplicación Xamarin.tvOS.

> [!IMPORTANT]
> Todas las imágenes en Apple TV están en la resolución de 1 x (`@1x`) y debe _sólo_ usar imágenes de este tamaño. Incluir más grandes, los gráficos de mayor resolución no sólo tardan tiempo en descargar y usar más memoria y almacenamiento, pero tiene que ser la escala de forma dinámica en tiempo de ejecución y afectará negativamente al rendimiento del dibujo.

<a name="Launch-Image" />

## <a name="launch-image"></a>Imagen de inicio

La imagen de inicio es lo primero que se muestra cuando la aplicación Xamarin.tvOS inicialmente se inicia en Apple TV, y por lo tanto, cada aplicación tvOS debe proporcionar una imagen de inicio. 

La imagen de inicio aparece rápidamente y da la impresión de que la aplicación es rápido y con capacidad de respuesta. Apple TV reemplazará la imagen de inicio con la primera pantalla de la aplicación en breve allí después.

Imágenes de inicio no son una oportunidad para anuncios o expresión artístico, sino que sirven únicamente para dar la impresión de que la aplicación se inicia rápidamente y está lista para usar.

|Tamaño de imagen de inicio|Notas|
|---|---|
|1920 x 1080 px|Sólo los archivos no capas PNG|

Apple realiza las siguientes sugerencias para diseñar las imágenes de inicio de la aplicación:

- **Casi idéntica a la primera pantalla** -su pantalla de inicio debe ser lo más cerca de la primera pantalla como sea posible de la aplicación. Incluidos los diferentes gráficos o elemento pueden producir un molestos "flash" cuando aparezca la primera pantalla.
- **Evitar usar texto** -imágenes de inicio son estáticas y por lo tanto, no se localizará antes de mostrarlo.
- **Restarles inicio** -los usuarios porque Apple TV cambian con frecuencia las aplicaciones, no debería llamar la atención sobre el proceso de inicio de la aplicación.
- **No hay anuncios o personalización de marca** -su imagen de inicio no debe utilizarse como pantalla About o incluir cualquier personalización de marca a menos que sea parte estática de la primera pantalla de la aplicación. Anuncios estrictamente están prohibidos.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Configuración de la imagen de inicio

Para establecer la imagen de inicio para el proyecto de tvOS, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo y editarlo: 

    [![](icons-images-images/asset01.png "El archivo Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. En el **Editor activo**, haga clic en el `LaunchImages` activos: 

    [![](icons-images-images/asset02.png "El recurso LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Haga clic en el **1 x de Apple TV** entrada y seleccione la imagen de inicio o que, opcionalmente, arrastre una nueva imagen el sistema de archivos: 

    [![](icons-images-images/asset03.png "Seleccione una imagen de inicio")](icons-images-images/asset03.png#lightbox)
4. Guarde los cambios.

<a name="Layered-Images" />

## <a name="layered-images"></a>Imágenes superpuestas

Nuevo para Apple TV, imágenes por niveles de trabajo con el efecto de paralaje para producir un efecto 3D que ayuda a mantener al usuario en el sofá mentalmente conectado al contenido en la pantalla en la sala.

Contienen imágenes con capas de dos (2) a cinco (5) separar las capas que se combinan para formar una imagen completa. A excepción de la capa de fondo, cada capa usa su orden Z junto con la transparencia para crear una ilusión de profundidad. Cuando el usuario interactúa con una imagen en capas, niveles superiores de ordenar de Z se escala y superpuestos para crear este efecto.

[![](icons-images-images/layered01.png "Diagrama de imágenes Z ordenadas por capas")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Imágenes superpuestas son necesarias para los iconos de la aplicación y son opcionales para otros [elementos enfocable](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (por ejemplo, la imagen del estante superior). Sin embargo, Apple sugiere el uso de imágenes en capas por cualquier imagen que se puede obtener el foco en la aplicación.




Apple realiza las siguientes sugerencias para diseñar sus imágenes en capas:

- **Realice el opaco de capa de fondo** -la capa de fondo (nivel 1) **debe** ser opaca u obtendrá un error al intentar usar la imagen en capas en Apple TV. Todas las demás capas pueden contener varios niveles de transparencia para mejorar el efecto 3D.
- **Aislar el primer plano, intermedio y en segundo plano elementos** -colocar elementos importantes (por ejemplo, el juego de caracteres) en primer plano, use el medio de elementos secundarios o sombras. Por último, incluir un fondo neutro para proporcionar una fase para los niveles superiores.
- **Mantener el texto en primer plano** -a menos que desee que el texto para estar ocultado por niveles más altos, por lo general debe estar en la capa superior.
- **Usar la creación de capas Simple** -el efecto de paralaje se diseñó para ser sutil así mantener las capas a un mínimo para evitar efectos discordante, realistas.
- **Incluir una zona segura para** -porque las capas superiores pueden recortarse durante un efecto de paralaje, que necesita para crear un borde de la zona de seguridad en cada capa. Si recibe el contenido demasiado cerca del borde de capas, puede convertirse en recortados. Las capas superiores experimentará más escalado y el recorte de las capas inferiores. Consulte la [las capas de imagen de ajuste de tamaño](#Sizing-Image-Layers) sección más adelante.
- **Obtener una vista previa a menudo se** -se debe mostrar imágenes con capas a menudo para asegurarse de que se produzca el efecto 3D deseado y que ninguna parte del contenido en las capas individuales es que se va a recortar. Debe obtener una vista previa de las imágenes en capas en hardware real de Apple TV para asegurarse de que se representan según lo previsto.

Siempre que sea posible, debe utilizar siempre los integrados `UIKit` controles para mostrar las imágenes en capas, como éstos obtendrán automáticamente el efecto de paralaje cuando entran en el foco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Capas de imagen de ajuste de tamaño

Es importante que recuerde incluir un _zona segura para_ borde en cada capa que se va a crear la imagen en capas. Dado que las capas individuales se pueden escalar y recorta durante el efecto de paralaje, el contenido de las capas puede recortará si es demasiado cerca al borde de la capa:

[![](icons-images-images/layered02.png "borde de 35 píxeles")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Creación de imágenes con capas

tvOS funciona con imágenes en capas en los siguientes formatos:

- **Archivos de AUTOMÓVIL** -se trata de un formato del catálogo de activos creado por Apple. No crear directamente archivos de AUTOMÓVIL, se crean en tiempo de compilación de los archivos LSR e incluidos en el grupo de aplicaciones.
- **Imágenes LSR** -éste es un formato de imagen de su propiedad creado por Apple. Use el [paralaje exportador de Adobe Photoshop complemento](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) o [controlador de vista previa de Parallax](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para crear y obtener una vista previa de imágenes en capas en el formato LSR.
- **Assets.xcassets** : desde dos (2) a cinco (5) estándar `.png` imágenes con formato incluidas en un catálogo de recursos que se compilarán en un AUTOMÓVIL o LSR con el formato de imagen en capas en tiempo de compilación.
- **Archivos de LCR** -éste es un formato de archivo propietario creado por Apple. Archivos LCR están pensados para usarse como contenido adicional que se descargan desde uno de los servidores de contenido. Archivo LCR nunca debe incluirse en el grupo de aplicaciones. Archivos LCR se generan a partir de archivos LSR o Photoshop mediante el `layerutil` herramienta de línea de comandos incluida con Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>El controlador de vista previa de paralaje

Apple que se creó el [controlador de vista previa de Parallax](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para vista previa y las imágenes creadas por capas necesarias para iconos de aplicación y los elementos opcionales de recibir el foco. El controlador de vista previa muestra todas las capas que constituye la imagen completada de capas:

[![](icons-images-images/layered03.png "The Parallax Previewer")](icons-images-images/layered03.png#lightbox)

Durante la vista previa de una imagen en capas, puede usar el mouse para girar la imagen y obtener una vista previa del efecto de paralaje. Use la **+** (más) y **-** (botones para agregar y quitar capas menos).

Al crear una nueva imagen en capas, puede exportar en el formato LSR e incluido en el paquete de la aplicación.

Para obtener más información sobre cómo crear y obtener una vista previa de imágenes en capas, vea Apple [crear obras de Parallax](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) sección de la [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Iconos de aplicación

La aplicación Xamarin.tvOS deberá disponer de un icono de aplicación no solo la pantalla principal de TV de Apple, sino también un icono para la aplicación de Store. El icono de aplicación es el por primera vez cambiar para realizar una impresión excelente sobre el usuario potencial y debe comunicar el propósito de la aplicación con un solo vistazo.

[![](icons-images-images/icon01.png "El icono de la aplicación")](icons-images-images/icon01.png#lightbox)

Cada aplicación debe proporcionar una pequeña y una versión grande de su icono de la aplicación. El icono pequeño que se usará en la pantalla principal de TV de Apple cuando se instala la aplicación. Store de la aplicación utiliza la versión de gran tamaño. El icono grande de la aplicación debe imitar la apariencia y comportamiento de la versión del icono pequeño.

|Icono pequeño||Icono grande||
|---|---|---|---|
|Tamaño real|400x240px|Tamaño|1280x768px|
|Tamaño de la zona de seguridad|370x222px|||
|Tamaño sin foco|300x180px|||
|Tamaño con foco|370x222px|||

> [!IMPORTANT]
> Los iconos de aplicación se debe proporcionar como **imágenes con capas**. Consulte la [en capas de imagen](#Layered-Images) sección anterior para obtener más detalles.




Apple proporciona las siguientes sugerencias para crear los iconos de aplicación:

- **Proporcionar un punto de enfoque único** : el icono con un punto único enfoque de diseño se coloca directamente en el centro del icono.
- **Utilice un fondo Simple** : simplificar el fondo del icono para que destaquen los niveles superiores. Considere el uso de un color simple o el degradado sutil.
- **Limitar la cantidad de texto** : puesto que el nombre de la aplicación aparecerá debajo del icono cuando está seleccionado por el usuario, solo debe incluir texto cuando resulta esencial para el diseño del icono.
- **No utilice las capturas de pantalla** – capturas de pantalla son demasiado complejos para un icono y no se permiten al usuario ver el propósito de la aplicación con un solo vistazo.
- **Mantener los iconos cuadrados** – tvOS automáticamente aplica una máscara que se redondea sutilmente las esquinas de los iconos. No incluya este redondeo usted mismo.
- **Separar cuidadosamente su capas** : texto debe estar en la esquina superior de la mayoría de capa, los elementos secundarios en la parte central y un fondo neutro que permite a los niveles superiores destacar.
- **Usar degradados y sombras cuidadosamente** – degradados y sombras pueden entrar en conflicto con el efecto de paralaje por lo que debe utilizarse con cuidado. Simple de arriba a abajo, los estilos de degradado claro a oscuro funcionan mejor. Las sombras normalmente funcionan mejor como nítidas y con bordes duros matices.
- **Variar la transparencia de la capa** : Use distintos niveles de transparencia en los niveles superiores del icono de la aplicación para aumentar el efecto 3D. La capa de fondo debe ser opaca o se producirá un error.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Establecer los iconos de aplicación

Para establecer los iconos de aplicación necesarios para el proyecto de tvOS, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo y editarlo: 

    [![](icons-images-images/asset01.png "El fileg Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. En el **Editor activo**, expanda el `App Icon & Top Shelf Image` activos: 

    [![](icons-images-images/asset04.png "Expanda el recurso de imagen del estante superior")](icons-images-images/asset04.png#lightbox)
3. A continuación, expanda el `App Icon - Small` activos: 

    [![](icons-images-images/asset05.png "Expanda el icono de aplicación - Small activo")](icons-images-images/asset05.png#lightbox)
4. A continuación, expanda el `Back` activo y haga clic en el `Contents` entrada: 

    [![](icons-images-images/asset06.png "A continuación, expanda el recurso de Back")](icons-images-images/asset06.png#lightbox)
5. Haga clic en el **1 x entrada Apple TV** y seleccione un archivo de imagen.
6. Repita los pasos anteriores para el `Front` y `Middle` activos.
7. A continuación, repita los mismos pasos para definir el `App Icon - Large` activo.
8. Guarde los cambios.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Imagen del estante superior

Si el usuario ha colocado la aplicación Xamarin.tvOS en la fila superior en la pantalla principal de TV de Apple, se mostrará una imagen de estante superior grande cuando se selecciona la aplicación por el usuario. Esta imagen debe resaltar las características de la aplicación o proporcionar vínculos directos a su contenido.

[![](icons-images-images/topshelf01.png "Ejemplo de imagen del estante superior")](icons-images-images/topshelf01.png#lightbox)

La imagen del estante superior o bien puede proporcionarse como una única static `.png` o `.lsr` archivo (consulte [crear imágenes con capas](#Creating-Layered-Images)) o puede crearse dinámicamente en tiempo de ejecución como una sola fila de elementos puede recibir el foco (consulte [ Contenido dinámico del estante superior](#Dynamic-Top-Shelf-Content) a continuación).

|Tamaño de la imagen del estante superior|Notas|
|---|---|
|1920x720px|.Png estático o archivo .lsr por capas|

Apple proporciona las siguientes sugerencias para crear las imágenes del estante superior:

- **Usar imágenes estáticas de Rich** : si la aplicación no proporciona un contenido dinámico, su imagen del estante superior será no puede recibir el foco. Use esta imagen para resaltar las características de la aplicación o su marca.
- **Vínculo a contenido de la aplicación** – diseños dinámicos de estante superior proporcionan un vínculo rápido al contenido que el usuario busca más importante en la aplicación. Use esta área para proporcionar un vínculo rápido para iniciar la aplicación y comenzar inmediatamente el contenido especificado.
- **Presentar el contenido más reciente** – contenido enriquecido superior estante puede dibujar un usuario en la aplicación y hacerlos desea usarlo más. Use esto como un área para presentar el contenido más reciente o más valorado más alto.
- **Personalizar el contenido** – usados con frecuencia sus más de contexto de los usuarios o aplicaciones favoritas en la fila de la parte superior de la pantalla de inicio. Utilice las estanterías de la parte superior para mostrar el contenido que sería más interesados.
- **No permite la ADS** : anuncios estrictamente se les prohíbe que se muestran en las estanterías de la parte superior. Puede mostrar el contenido de compra más reciente, pero no se debe mostrar ninguna información de precios.

### <a name="setting-the-top-shelf-image"></a>Establecimiento de la imagen del estante superior

Para establecer la imagen del estante superior necesarios para el proyecto de tvOS, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo y editarlo: 

    [![](icons-images-images/asset01.png "El archivo Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. En el **Editor activo**, expanda el `App Icon & Top Shelf Image` activos: 

    [![](icons-images-images/asset04.png "Expanda el recurso de imagen del estante superior")](icons-images-images/asset04.png#lightbox)
3. Haga clic en el `Top Shelf Image` activos: 

    [![](icons-images-images/asset07.png "El recurso de imagen del estante superior")](icons-images-images/asset07.png#lightbox)
4. Haga clic en el **1 x entrada Apple TV** y seleccione un archivo de imagen.
5. Guarde los cambios.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenido dinámico del estante superior

En lugar de mostrar una imagen estática de estante superior, el estante superior puede contener una fila dinámica de [elementos enfocable](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) o un conjunto dinámico de banners de desplazamiento. Ambos de estos estilos dinámicos permiten resaltar el contenido proporcionado por la aplicación o de salto en sus características más usados.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>En secciones de contenido de fila

Este tipo de contenido dinámico de estante superior presenta una sola fila de desplazamiento, puede recibir el foco elementos opcionalmente dividido en secciones. Se utiliza normalmente para resaltar nuevos, favoritos o vistos recientemente el contenido de la aplicación.

Contenido se presenta como una lista de desplazamiento horizontal solo de contenido con una etiqueta que aparece en la parte actual del contenido seleccionado (que actualmente tiene el foco). Si el usuario selecciona una parte determinada del contenido, se iniciará la aplicación y deben tomarse directamente en ese contenido.

Los siguientes tamaños de contenido será necesarios:

||Póster de (2:3)|Cuadrado (1:1)|HDTV (16:9)|
|---|---|---|---|
|Tamaño real|404x608px|608x608px|908x512px|
|Tamaño de la zona de seguridad|380x570px|570x570px|852x479px|
|Tamaño sin foco|333x500px|500x500px|782x440px|
|Tamaño con foco|380x570px|570x570px|852x479px|

Apple proporciona las siguientes sugerencias para la fila secciona contenido:

- **Completar la fila** : debe proporcionar suficiente contenido para abarcar todo el ancho de la pantalla.
- **Escalar imágenes mixtas** : fila secciona el contenido se ha diseñado para contener una combinación de tamaños de imagen (en la lista proporcionada anteriormente). Si mezcla los tamaños de imagen sin embargo, tenga en cuenta que se aplicará la escala adicional para normalizar la visualización del contenido.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Titulares de margen desplazamiento

Opcionalmente, la aplicación Xamarin.tvOS puede presentar su contenido en las estanterías de la parte superior como un desplazamiento automáticamente y bucle de recopilación de los titulares que llene casi de la pantalla. Este estilo se utiliza normalmente para presentar contenido enriquecido, nuevos como nuevos programas de TV.

Además de desplazamiento automático, el usuario puede tomar el control de los titulares y desplazarse en cualquier dirección con el control remoto de Siri. Realizar una pequeña, gesto circular en el control remoto de Siri cuando un mensaje emergente tiene el foco activará el efecto de paralaje para ese encabezado.

**Imagen del banner (Extra grande)**

|   |   |
|---|---|
|Tamaño real|1940x624px|
|Tamaño de la zona de seguridad|1740x620px|
|Tamaño sin foco|1740x560px|
|Tamaño con foco|1740x620px|

Desplazamiento bajorrelieve Banners cualquiera se pueden proporcionar como estático `.png` o superpuesto `.lsr` archivo.

Apple proporciona las siguientes sugerencias para los titulares de margen desplazamiento:

- **Cantidad de contenido** -debe proporcionar un mínimo de banners de tres (3) para el desplazamiento que sienta naturales. Debe incluir los banners de no más de ocho (8) o lo que la navegación por disco duro para el usuario final.
- **Texto de contenido** : si la pancarta requiere texto en que debe incluirse en la imagen del banner. Si usa imágenes con capas, el texto debe estar en la capa superior.

Consulte Apple [TVServices Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obtener más información sobre cómo agregar una extensión del estante superior a la aplicación para proporcionar contenido dinámico de estante superior.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Imágenes de Game Center

Si su aplicación Xamarin.tvOS es un juego y ha incluido compatibilidad con Game Center, se requerirá varios activos de imagen más:

- **Iconos de logro** -una imagen opaca es necesaria para cada mención especial de que se recortará automáticamente en un círculo. Logros son elementos no puede recibir el foco.
- **Ilustración del panel** -puede ser una imagen opcional proporcionado que aparecerá en la parte superior del panel de la aplicación dentro de Game Center. Estas imágenes son no puede recibir el foco.
- **Ilustración de marcador** -debe proporcionar entre uno (1) para tres (3) las imágenes de relación de aspecto 16:9 para cada tabla de clasificación que admite la aplicación. Estos pueden ser estáticas `.png` o superpuesto `.lsr` archivos. La ilustración del marcador es recibir el foco.

||Iconos de logro|Ilustración del panel|Ilustración de marcador|
|---|---|---|---|
|Tamaño visible|200x200px|923x150px|N/D|
|Tamaño real|320x320px|N/D|659x371px|
|Tamaño de la zona de seguridad|N/D|N/D|618x348px|
|Tamaño sin foco|N/D|N/D|548x309px|
|Tamaño con foco|N/D|N/D|618x348px|

Para obtener más información sobre cómo trabajar con Game Center, consulte Apple [Game Center Programming Guide](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Trabajar con imágenes

Puesto que tvOS 9 es un subconjunto de iOS 9, las mismas técnicas que se utiliza para incluir y mostrar imágenes en una aplicación de Xamarin.iOS, también funciona para una aplicación Xamarin.tvOS. Consulte nuestra [mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documentación para obtener más información.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Configurar proyectos Xamarin.tvOS imágenes

Como se indicó anteriormente, todas las aplicaciones de tvOS requieren un [iniciar imagen](#Launch-Image), y [icono de la aplicación](#App-Icons). Esta sección trata de seleccionar las imágenes de inicio y el icono de la aplicación para el proyecto de aplicación Xamarin.tvOS después de que se han establecido en un catálogo de recursos.

Haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el `Info.plist` para abrirlo y editarlo: 

    [![](icons-images-images/info01.png "El archivo Info.plist")](icons-images-images/info01.png#lightbox)
2. En el **Editor Info.Plist**, seleccione el catálogo de activos (configurada anteriormente en el [establecer los iconos de aplicación](#Setting-the-App-Icons) sección) para el **iconos de aplicación**: 

    [![](icons-images-images/info02.png "El Editor Info.Plist")](icons-images-images/info02.png#lightbox)
3. A continuación, seleccione el catálogo de recursos (configurada anteriormente en el [establecimiento de la imagen de inicio](#Setting-the-Launch-Image) sección) para el **imágenes de inicio**.
4. Guarde los cambios.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto todos los tipos de imagen y los tamaños que se usan en una aplicación Xamarin.tvOS. En primer lugar, tratan de imágenes de inicio, en capas imágenes, iconos de aplicación, las imágenes del estante superior y Game Center. A continuación, se ha descrito cómo trabajar con imágenes en la aplicación Xamarin.tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
