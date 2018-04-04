---
title: Trabajar con iconos e imágenes
description: Este artículo tratan diseñar y trabajar con iconos y las imágenes dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c888ecf3d7e0f21734f2b89176eed56bf778dbf9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-icons-and-images"></a>Trabajar con iconos e imágenes

_Este artículo tratan diseñar y trabajar con iconos y las imágenes dentro de una aplicación Xamarin.tvOS._

Crear atractivas iconos e imágenes son una parte fundamental del desarrollo de una experiencia de usuario atractiva para las aplicaciones de TV de Apple. Esta guía tratará los pasos necesarios para crear e incluir los activos gráficos necesarios para las aplicaciones de Xamarin.tvOS:

- [Iniciar la imagen](#Launch-Image) -muestra una imagen de inicio cuando la aplicación se inicia por primera vez y se reemplaza por la primera pantalla de la aplicación cuando se haya completado el inicio.
- [Capas imágenes](#Layered-Images) : específico para Apple TV, nuevos trabajos de imágenes en capas de Apple con el efecto de Parallax para crear un efecto 3D para los elementos seleccionados. Hay varias maneras de [crear imágenes en capas](#Creating-Layered-Images).
- [Icono de la aplicación](#App-Icons) -los iconos son necesarios para la pantalla no solo el Apple TV inicio pero para la tienda de aplicaciones. Debe proporcionar como una imagen en capas.
- [Top estante imagen](#Top-Shelf-Image) -si la aplicación se coloca en la fila de la parte superior de la pantalla Inicio, necesitará una imagen de estante superior para resaltar las características de la aplicación. Si lo desea, puede proporcionar [contenido dinámico de estante superior](#Dynamic-Top-Shelf-Content) para resaltar el contenido de la aplicación.
- [Imágenes de centro de juegos](#Game-Center-Images) -si la aplicación es un juego y usa al centro de juegos, se le solicitará varias imágenes adicionales.
- [Configurar imágenes de proyecto Xamarin.tvOS](#Setting-Xamarin.tvOS-Project-Images) -se describen los pasos necesarios para configurar la imagen de inicio y el icono de la aplicación para la aplicación Xamarin.tvOS.

> [!IMPORTANT]
> Todas las imágenes en Apple TV están en la resolución de 1 x (`@1x`) y debe _sólo_ utilizar imágenes de este tamaño. Incluir más grandes, gráficos de mayor resolución no solo tardan tiempo en descargar y usar más memoria y almacenamiento, pero tiene que ser vuelve a escalar dinámicamente en tiempo de ejecución y afectará negativamente al rendimiento del dibujo.

<a name="Launch-Image" />

## <a name="launch-image"></a>Iniciar la imagen

La imagen de inicio es lo primero que se muestra cuando la aplicación Xamarin.tvOS inicialmente se inicia en el Televisor de Apple y, por lo tanto, todas las aplicaciones de tvOS deben proporcionar una imagen de inicio. 

La imagen de inicio aparece rápidamente y le ofrece la impresión de que la aplicación es rápido y eficaz. Apple TV reemplazará la imagen de inicio con la primera pantalla de la aplicación en breve se después.

Imágenes de inicio no tienen una oportunidad para que los anuncios o expresión artístico, solo existen para dar la impresión de que la aplicación se inicia rápidamente y está lista para usar.

|Iniciar el tamaño de la imagen|Notas|
|---|---|
|1920x1080px|Sólo los archivos .png de capas no|

Apple realiza las siguientes sugerencias para diseñar la imagen de inicio de la aplicación:

- **Casi idéntica a la primera pantalla** -la pantalla de inicio debe ser lo más cerca de primera pantalla de la aplicación como sea posible. Incluidos los diferentes gráficos o elemento pueden producir un molestar "flash" cuando aparezca la primera pantalla.
- **Evite usar texto** -iniciar imágenes son estáticas y por lo tanto, no se localizará antes de mostrarlo.
- **Minimice inicio** -los usuarios porque Apple TV cambian con frecuencia las aplicaciones, no debería llamar la atención sobre el proceso de inicio de la aplicación.
- **No hay anuncios o personalización de marca** -la imagen de inicio no debe usarse como una pantalla acerca o incluir cualquier personalización de marca a menos que sea estática parte de la primera pantalla de la aplicación. Anuncios estrictamente están prohibidos.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Configuración de la imagen de inicio

Para establecer la imagen de inicio para el proyecto tvOS, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo y editarlo: 

    [![](icons-images-images/asset01.png "El archivo Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. En el **Editor activo**, haga clic en el `LaunchImages` activos: 

    [![](icons-images-images/asset02.png "El recurso LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Haga clic en el **1 x Apple TV** entrada y seleccione la imagen de iniciar o que, opcionalmente, arrastre una nueva imagen el sistema de archivos: 

    [![](icons-images-images/asset03.png "Seleccionar una imagen de inicio")](icons-images-images/asset03.png#lightbox)
4. Guarde los cambios.

<a name="Layered-Images" />

## <a name="layered-images"></a>Imágenes en capas

Nuevo para Apple TV, imágenes por niveles de trabajo con el efecto de Parallax para producir un efecto 3D que ayuda a mantener el usuario en el sofá mentalmente conectado en el contenido en la pantalla en el salón.

Contienen imágenes con capas de dos (2) a cinco (5) separar las capas que se combinan para formar una imagen completa. Con la excepción de la capa de fondo, cada nivel utiliza su orden Z junto con transparencia para crear una ilusión de profundidad. Cuando el usuario interactúa con una imagen en capas, niveles superiores de la ordenada Z se escala y superpuestas para crear este efecto.

[![](icons-images-images/layered01.png "Diagrama de ordenada Z de imágenes en capas")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Imágenes en capas son necesarias para los iconos de la aplicación y son opcionales para otros [elementos puede recibir el foco](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (por ejemplo, la imagen de estante superior). Sin embargo, Apple sugiere utilizando imágenes en capas para cualquier imagen que puede recibir el foco de la aplicación.




Apple realiza las siguientes sugerencias para diseñar sus imágenes en capas:

- **Realizar el opaco de capa de fondo** -la capa de fondo (nivel 1) **debe** ser opaco u obtendrá un error al intentar usar la imagen de capas en una TV de Apple. Todas las demás capas pueden contener varios niveles de transparencia para mejorar el efecto 3D.
- **Aislar de primer plano, intermedio y los elementos de fondo** -colocar los elementos principales (por ejemplo, juegos de caracteres) en primer plano, use el centro de elementos secundarios o las sombras. Por último, incluir un fondo neutro para proporcionar una fase de los niveles superiores.
- **Guardar el texto en primer plano** -a menos que desee que el texto para verse oscurecidas por los niveles superiores, normalmente debería estar en el nivel superior.
- **Usar capas Simple** -el efecto de Parallax se ha diseñado para ser sutil por lo que mantener las capas a un mínimo para evitar efectos discordante, poco realistas.
- **Incluir una zona de seguridad** -porque capas superiores pueden recortadas durante un efecto Parallax, necesita crear un borde de la zona de seguridad en cada capa. Si recibe el contenido demasiado cerca del borde de las capas, puede convertirse en recortados. Niveles superiores experimentará más escalado y el recorte de capas inferiores. Consulte la [capas de imagen de ajuste de tamaño](#Sizing-Image-Layers) sección más adelante.
- **Obtener una vista previa a menudo se** -imágenes en capas se debe mostrar a menudo para asegurarse de que se produzca el efecto 3D deseado y que es ninguno del contenido en las capas individuales que se va a recortar. Debe obtener una vista previa de las imágenes en capas en hardware real de Apple TV para asegurarse de que se presentan como se esperaba.

Siempre que sea posible, debe utilizar siempre la integrada `UIKit` controles para mostrar imágenes en capas, como automáticamente obtendrán el efecto Parallax cuando entran en el foco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Capas de imagen de ajuste de tamaño

Es importante recordar incluir un _zona segura para_ borde en cada capa que se va a crear la imagen en capas. Dado que las capas individuales se pueden escalar y recorta durante el efecto Parallax, el contenido de las capas puede recortado si es demasiado cerca de borde de la capa:

[![](icons-images-images/layered02.png "borde de 35 píxeles")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Crear capas imágenes

tvOS funciona con imágenes en capas en los siguientes formatos:

- **Archivos de AUTOMÓVIL** -se trata de un formato propietario de catálogo de activos creado por Apple. No crear directamente archivos de coche, se crean en tiempo de compilación de los archivos LSR e incluidos en el paquete de aplicación.
- **Imágenes de LSR** -éste es un formato de imagen propietario creado por Apple. Use la [Parallax exportador de Adobe Photoshop complemento](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) o la [controlador de vista previa de Parallax](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para crear y obtener una vista previa de imágenes en capas en el formato LSR.
- **Assets.xcassets** - desde dos (2) a cinco (5) estándar `.png` con formato imágenes incluidas en un catálogo de activos que se compilarán en un AUTOMÓVIL o LSR con el formato de imagen de la capa en tiempo de compilación.
- **Archivos de LCR** -éste es un formato de archivo patentado creado por Apple. Archivos LCR están diseñados para usarse como contenido adicional que se descarga desde uno de los servidores de contenido. Archivo LCR nunca debe incluirse en el grupo de aplicaciones. Se generan archivos de replicación continua local de archivos LSR o Photoshop mediante el `layerutil` herramienta de línea de comandos que se incluye con Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>El controlador de vista previa de Parallax

Apple crea el [controlador de vista previa de Parallax](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) vista previa y imágenes creadas por niveles requeridos para iconos de aplicación y elementos opcionales de puede recibir el foco. El controlador de vista previa muestra todas las capas que forman la imagen completada de capas:

[![](icons-images-images/layered03.png "El controlador de vista previa de Parallax")](icons-images-images/layered03.png#lightbox)

Al obtener una vista previa de una imagen en capas, puede utilizar el mouse para girar la imagen y obtener una vista previa del efecto Parallax. Use la **+** (signo más) y **-** (botones para agregar y quitar capas menos).

Al crear una nueva imagen de capas, puede exportar en el formato LSR e incluido en el paquete de la aplicación.

Para obtener más información sobre cómo crear y obtener una vista previa de imágenes en capas, visite de Apple [crear ilustraciones de Parallax](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) sección de la [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Iconos de aplicación

La aplicación Xamarin.tvOS requerirá no solo un icono de la aplicación para la pantalla principal de TV de Apple, sino también un icono para la tienda de aplicaciones. El icono de aplicación es el su primer cambian para realizar una impresión excelente en sus usuarios potenciales y debe comunicarse el propósito de la aplicación de un vistazo.

[![](icons-images-images/icon01.png "El icono de la aplicación")](icons-images-images/icon01.png#lightbox)

Cada aplicación debe proporcionar una pequeña y una versión grande de su icono de la aplicación. Cuando se instala la aplicación, se utilizará el icono pequeño en la pantalla Inicio TV de Apple. Se usa la versión de gran tamaño mediante la tienda de aplicaciones. El icono grande de la aplicación debe imitar la apariencia y funcionamiento de la versión del icono pequeño.

|Icono pequeño||Iconos grandes||
|---|---|---|---|
|Tamaño real|400x240px|Tamaño|1280x768px|
|Tamaño de la zona de seguridad|370x222px|||
|Tamaño sin foco|300x180px|||
|Tamaño tiene el foco|370x222px|||

> [!IMPORTANT]
> Los iconos de la aplicación deberá indicarse como **imágenes en capas**. Vea la [en capas de imagen](#Layered-Images) sección anterior para obtener más detalles.




Apple proporciona las siguientes sugerencias para la creación de los iconos de aplicación:

- **Proporcionar un punto de enfoque único** : diseño su icono con un punto de enfoque único coloca directamente en el centro del icono.
- **Usar un fondo Simple** : simplificar el fondo del icono para que se resaltan los niveles superiores. Considere el uso de un color simple o degradado sutil.
- **Limitar la cantidad de texto** : puesto que el nombre de la aplicación aparecerá debajo del icono cuando se selecciona por el usuario, solo debe incluir texto cuando resulta esencial para el diseño del icono.
- **No use capturas de pantalla** – capturas de pantalla son demasiado complejas para un icono y no se permiten al usuario ver el propósito de la aplicación de un vistazo.
- **Mantener iconos cuadrado** – tvOS aplica automáticamente una máscara que ligeramente redondea las esquinas de los iconos. No incluya este redondeo usted mismo.
- **Separar cuidadosamente su capas** : texto debe estar en la esquina superior capa mayoría, los elementos secundarios en la parte central y un fondo neutro que permite a los niveles superiores destacar.
- **Utilice los degradados y sombras cuidadosamente** : degradados y sombras pueden entrar en conflicto con el efecto de Parallax por lo que debe utilizarse con cuidado. Simple de arriba a abajo, estilos de degradado de claro a oscuro funcionan mejor. Las sombras normalmente funcionan mejor con tonos nítidas y con bordes duros.
- **Variar la transparencia de capas** : uso de distintos niveles de transparencia en los niveles superiores del icono de la aplicación para aumentar el efecto 3D. La capa de fondo debe ser opaca o se producirá un error.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Configuración de los iconos de aplicación

Para establecer los iconos de aplicación necesarios para el proyecto tvOS, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo y editarlo: 

    [![](icons-images-images/asset01.png "El fileg Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. En el **Editor activo**, expanda el `App Icon & Top Shelf Image` activos: 

    [![](icons-images-images/asset04.png "Expanda el recurso de imagen de estante superior")](icons-images-images/asset04.png#lightbox)
3. A continuación, expanda el `App Icon - Small` activos: 

    [![](icons-images-images/asset05.png "Expanda el icono de aplicación - asset pequeño")](icons-images-images/asset05.png#lightbox)
4. A continuación, expanda el `Back` activo y haga clic en el `Contents` entrada: 

    [![](icons-images-images/asset06.png "A continuación, expanda el recurso de copia")](icons-images-images/asset06.png#lightbox)
5. Haga clic en el **1 x entrada de TV de Apple** y seleccione un archivo de imagen.
6. Repita los pasos anteriores para la `Front` y `Middle` activos.
7. A continuación, repetir los mismos pasos para definir la `App Icon - Large` activo.
4. Guarde los cambios.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Imagen de estante superior

Si el usuario ha colocado la aplicación Xamarin.tvOS en la fila de la parte superior de la pantalla Inicio TV de Apple, se mostrará una imagen de estante superior grande cuando se selecciona la aplicación por el usuario. Esta imagen debe resaltar las características de la aplicación o proporcionar vínculos directos a su contenido.

[![](icons-images-images/topshelf01.png "Ejemplo de imagen de estante superior")](icons-images-images/topshelf01.png#lightbox)

La imagen de estante superior pueden ser proporcionada como una sola static `.png` o `.lsr` archivo (vea [crear imágenes en capas](#Creating-Layered-Images)) o puede crearse dinámicamente en tiempo de ejecución como una sola fila de elementos puede recibir el foco (vea [ Contenido dinámico estante superior](#Dynamic-Top-Shelf-Content) a continuación).

|Tamaño de la imagen de estante superior|Notas|
|---|---|
|1920x720px|.Png estático o archivo .lsr por niveles|

Apple proporciona las siguientes sugerencias para crear las imágenes de estante superior:

- **Usar imágenes estáticas enriquecido** : si la aplicación no proporciona un contenido dinámico, su imagen de estante superior será no pueden recibir el foco. Usar esta imagen para resaltar las características de la aplicación o su marca.
- **Vínculo al contenido de la aplicación** : diseños de estante superior dinámicos proporcionan un vínculo rápido al contenido que el usuario busca más importante de la aplicación. Use esta área para proporcionar un vínculo rápido para iniciar la aplicación y saltar inmediatamente en el contenido especificado.
- **Mostrar el contenido más reciente** – contenido enriquecido superior estante puede dibujar un usuario en la aplicación y comprobar si se desea seguir utilizándolo más. Utilícelo como un área para mostrar el contenido más reciente o calificado más alto.
- **Personalizar el contenido** – usados con frecuencia sus más de contexto de los usuarios o aplicaciones favoritas en la fila de la parte superior de la pantalla Inicio. Use el estante superior para mostrar el contenido que sería más interesados en.
- **No se permiten los anuncios** : anuncios estrictamente se les prohíbe que se muestran en la estantería de la parte superior. Puede mostrar el contenido puede adquirir más reciente, pero no debe mostrarse ninguna información sobre los precios.

### <a name="setting-the-top-shelf-image"></a>Configuración de la imagen de estante superior

Para establecer la imagen de estante superior necesaria para el proyecto tvOS, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo y editarlo: 

    [![](icons-images-images/asset01.png "El archivo Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. En el **Editor activo**, expanda el `App Icon & Top Shelf Image` activos: 

    [![](icons-images-images/asset04.png "Expanda el recurso de imagen de estante superior")](icons-images-images/asset04.png#lightbox)
3. Haga clic en el `Top Shelf Image` activos: 

    [![](icons-images-images/asset07.png "El recurso de imagen de estante superior")](icons-images-images/asset07.png#lightbox)
5. Haga clic en el **1 x entrada de TV de Apple** y seleccione un archivo de imagen.
6. Guarde los cambios.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenido dinámico estante superior

En lugar de mostrar una imagen estática de estante superior, el estante superior puede contener una fila dinámica de [elementos puede recibir el foco](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) o un conjunto dinámico de desplazamiento banners. Ambos de estos estilos dinámicos le permiten resaltar el contenido proporcionado por la aplicación o un salto en sus características más usados.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Secciones de contenido de fila

Este tipo de contenido dinámico de estante superior presenta una sola fila de desplazamiento, puede recibir el foco elementos opcionalmente divide en secciones. Se utiliza normalmente para resaltar nuevos, favoritos o recientemente ver contenido de la aplicación.

Contenido se presenta como una lista de desplazamiento horizontal y única del contenido con una etiqueta que aparece en la parte actual del contenido seleccionado (que actualmente tiene el foco). Si el usuario selecciona una parte determinada del contenido, que se ejecutará la aplicación y que se deberían tomar directamente en ese contenido.

Los siguientes tamaños de contenido será necesarios:

||Póster (2:3)|Cuadrado (1:1)|HDTV (16:9)|
|---|---|---|---|
|Tamaño real|404x608px|608x608px|908x512px|
|Tamaño de la zona de seguridad|380x570px|570x570px|852x479px|
|Tamaño sin foco|333x500px|500x500px|782x440px|
|Tamaño tiene el foco|380x570px|570x570px|852x479px|

Apple proporciona las siguientes sugerencias para la fila secciona contenido:

- **Completar la fila** : debe proporcionar suficiente contenido para abarcar todo el ancho de la pantalla.
- **Ajuste de escala de imágenes mixtas** : fila secciona el contenido se ha diseñado para contener una combinación de tamaños de imagen (en la lista proporcionada anteriormente). Si mezcla los tamaños de imagen sin embargo, tenga en cuenta que se aplicará la escala adicional para normalizar la visualización del contenido.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Desplazamiento Banners bajorrelieve

Opcionalmente, la aplicación Xamarin.tvOS puede presentar su contenido en el estante superior como un desplazamiento automáticamente y bucle la colección de estrellas que casi rellene la pantalla. Este estilo se utiliza normalmente para mostrar contenido enriquecido, nuevos como nuevos programas de TV.

Además de desplazamiento automático, el usuario puede tomar el control de los encabezados y desplazarse en cualquier dirección mediante el control remoto Siri. Realizar una pequeña, gestos circular en el servidor remoto de Siri cuando el encabezado tiene el foco activarán el efecto Parallax para ese encabezado.

**Imagen de titular (Extra grande)**

|   |   |
|---|---|
|Tamaño real|1940x624px|
|Tamaño de la zona de seguridad|1740x620px|
|Tamaño sin foco|1740x560px|
|Tamaño tiene el foco|1740x620px|

Desplazamiento bajorrelieve Banners cualquiera se puede proporcionar como una variable static `.png` o superpuesto `.lsr` archivo.

Apple proporciona las siguientes sugerencias para los titulares de margen de desplazamiento:

- **Cantidad de contenido** -debe proporcionar un mínimo de tres (3) estrellas para el desplazamiento para sentirse natural. Debe incluir los titulares de no más de ocho (8) o lo hacen navegación disco duro para el usuario final.
- **Texto de contenido** : si la pancarta requiere texto en debe incluirse en la imagen de titular. Si utiliza imágenes en capas, el texto debe ser del nivel superior.

Vea de Apple [TVServices Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obtener más información sobre cómo agregar una extensión de estante superior a la aplicación para proporcionar contenido dinámico de estante superior.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Imágenes de centro de juegos

Si la aplicación de Xamarin.tvOS es un juego y se ha incluido soporte técnico de centro de juegos, varios activos de imagen más será necesarios:

- **Iconos de realización** -una imagen opaca es necesaria para cada consecución que se van a recortar automáticamente en un círculo. Logros son elementos no pueden recibir el foco.
- **Ilustración de panel** -puede ser una imagen opcional proporcionado por el que se mostrará en la parte superior del panel de la aplicación de centro de juegos. Estas imágenes son no pueden recibir el foco.
- **Ilustración de la tabla de clasificación de** -debe proporcionar entre uno (1) a tres (3) imágenes de relación de aspecto 16:9 para cada tabla de clasificación que admite la aplicación. Éstas pueden ser estáticas `.png` o superpuesto `.lsr` archivos. La ilustración de la tabla de clasificación es puede recibir el foco.

||Iconos de realización|Ilustración de panel|Ilustración de la tabla de clasificación|
|---|---|---|---|
|Tamaño visible|200x200px|923x150px|N/D|
|Tamaño real|320x320px|N/D|659x371px|
|Tamaño de la zona de seguridad|N/D|N/D|618x348px|
|Tamaño sin foco|N/D|N/D|548x309px|
|Tamaño tiene el foco|N/D|N/D|618x348px|

Para obtener más información sobre cómo trabajar con el centro de juegos, consulte de Apple [Guía de programación de centro de juegos](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Trabajar con imágenes

Puesto que tvOS 9 es un subconjunto de iOS 9, las mismas técnicas que se utiliza para incluir y mostrar imágenes en una aplicación Xamarin.iOS, también funcionan para una aplicación Xamarin.tvOS. Visite nuestro [mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documentación para obtener más información.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Configurar proyecto Xamarin.tvOS imágenes

Como se mencionó anteriormente, todas las aplicaciones de tvOS requieren un [iniciar imagen](#Launch-Image), y [icono de la aplicación](#App-Icons). Esta sección tratan al seleccionar la imagen de inicio y el icono de la aplicación para el proyecto de aplicación de Xamarin.tvOS después de que se han establecido en un catálogo de activos.

Haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el `Info.plist` para abrirlo y editarlo: 

    [![](icons-images-images/info01.png "El archivo Info.plist")](icons-images-images/info01.png#lightbox)
2. En el **Info.Plist Editor**, seleccione el catálogo de activos (configurada anteriormente en el [establecer los iconos de aplicación](#Setting-the-App-Icons) sección) para la **iconos de aplicación**: 

    [![](icons-images-images/info02.png "El Editor de Info.Plist")](icons-images-images/info02.png#lightbox)
3. A continuación, seleccione el catálogo de activos (configurada anteriormente en el [configuración de la imagen de inicio](#Setting-the-Launch-Image) sección) para la **imágenes iniciar**.
4. Guarde los cambios.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata todos los tipos de imagen y los tamaños que se usan en una aplicación Xamarin.tvOS. En primer lugar, lo cubre imágenes de inicio, en capas imágenes, iconos de aplicación, imágenes de estante superior y Game Center. A continuación, lo cubre trabajar con imágenes de la aplicación Xamarin.tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
