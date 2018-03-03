---
title: HomeKit
description: "HomeKit es framework de Apple para controlar dispositivos de automatización del hogar. Este artículo presenta HomeKit y explica cómo configurar accesorios de prueba en el simulador de accesorio HomeKit y escribir una aplicación sencilla de Xamarin.iOS para interactuar con estos accesorios."
ms.topic: article
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 1a49c3a3181b477b777de74b0eb53f5e0da6f041
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="homekit"></a>HomeKit

_HomeKit es framework de Apple para controlar dispositivos de automatización del hogar. Este artículo presenta HomeKit y explica cómo configurar accesorios de prueba en el simulador de accesorio HomeKit y escribir una aplicación sencilla de Xamarin.iOS para interactuar con estos accesorios._

[ ![](homekit-images/accessory01.png "Aplicación habilitada para un ejemplo HomeKit")](homekit-images/accessory01.png)

Apple incorporó HomeKit en iOS 8 como una manera de integrar fácilmente varios dispositivos de domótica de proveedores de una serie en una sola unidad coherente. Mediante la promoción de un protocolo común para detectar, configurar y controlar dispositivos domótica, HomeKit permite que los dispositivos de no relacionado con los proveedores para trabajar conjuntamente, sin necesidad de los proveedores individuales tener que coordinar esfuerzos.

Con HomeKit, puede crear una aplicación de Xamarin.iOS que controla cualquier dispositivo HomeKit habilitado sin usar proveedor proporciona API o aplicaciones. Con HomeKit, puede hacer lo siguiente:

- Detectar nuevos dispositivos de domótica HomeKit habilitado y agregarlos a una base de datos que se conservará en todos los dispositivos del usuario iOS.
- El programa de instalación, configurar, mostrar y controlar cualquier dispositivo en el HomeKit _base de datos de la configuración de inicio_.
- Comunicarse con cualquier dispositivo HomeKit preconfigurada y comandos para realizar acciones individuales o trabajan en conjunto, como activar todas las luces de la cocina.

Además de servir dispositivos en la configuración de base de datos principal a las aplicaciones de HomeKit habilitado, HomeKit proporciona acceso a los comandos de voz Siri. Dado un HomeKit configurado adecuadamente el programa de instalación, el usuario puede emitir comandos de voz como "Siri, activar las luces en la sala de estar."

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>La base de datos de configuración de inicio

HomeKit organiza todos los dispositivos de automatización en una ubicación especificada en una colección. Esta colección proporciona una manera para que el usuario agrupar sus dispositivos domótica en unidades organizadas lógicamente con etiquetas significativas y legible.

La colección se almacena en una configuración base de datos principal que estarán automáticamente copias de seguridad y sincronizados en todos los dispositivos del usuario iOS. HomeKit proporciona las siguientes clases para trabajar con la base de datos en configuración de inicio:

- `HMHome` -Éste es el contenedor de nivel superior que contiene toda la información y las configuraciones de todos los dispositivos de domótica en una ubicación física única (p. ej. una sola familia residencia). El usuario podría tener más de una residencia, como su casa principal y una casa de vacaciones. O es posible que tengan diferentes "contiene" en la misma propiedad, como la casa principal y una casa de invitado en el mercado de artículos. En cualquier caso, al menos una `HMHome` objeto _debe_ configurará y almacenan antes de que se puede especificar cualquier otra información de HomeKit.
- `HMRoom` -While opcional, un `HMRoom` permite al usuario definir salas específicas dentro de una casa (`HMHome`) como: cocina, baño, artículos o sala de estar. El usuario puede agrupar todos los dispositivos de domótica en una ubicación específica dentro de su casa en un `HMRoom` y actuar como una unidad. Por ejemplo, pedir Siri para desactivar las luces de artículos.
- `HMAccessory` -Esto representa un usuario individual, HomeKit físico habilitado el dispositivo de automatización que se ha instalado en residencia del usuario (por ejemplo, un termostato inteligente). Cada `HMAccessory` se asigna a un `HMRoom`. Si el usuario no ha configurado los salones, HomeKit asigna accesorios en una sala predeterminada especial.
- `HMService` -Representa un servicio suministrado por un determinado `HMAccessory`, como el estado de encendido/apagado de una luz o su color (si se admite el cambio de color). Cada `HMAccessory` puede tener más de un servicio, como la apertura de la tapa de artículos que también incluye una luz. Además, un determinado `HMAccessory` podría tener servicios, como la actualización de firmware, que se encuentran fuera del control de usuario.
- `HMZone` : Permite al usuario que agrupa una colección de `HMRoom` objetos en zonas lógicas, como escaleras arriba, escaleras abajo o sótano. Aunque es opcional, esto permite interacciones como preguntarle Siri para todos los de la luz activar está desactivado.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Aprovisionamiento de una aplicación HomeKit

Debido a los requisitos de seguridad impuestos por HomeKit, una aplicación de Xamarin.iOS que usa el marco de trabajo de HomeKit debe estar correctamente configurada en el Portal de desarrollador de Apple y en el archivo de proyecto Xamarin.iOS.

Haga lo siguiente:

1. Inicie sesión en el [Portal para desarrolladores de Apple](http://developer.apple.com).
2. Haga clic en **certificados, identificadores & perfiles**.
3. Si aún no lo ha hecho, haga clic en **identificadores** y crear un identificador para la aplicación (por ejemplo, `com.company.appname`), persona editar su identificador existente.
4. Asegúrese de que el **HomeKit** se ha comprobado el servicio para el Id. especificado: 

    [ ![](homekit-images/provision01.png "Habilitar el servicio de HomeKit para el identificador especificado.")](homekit-images/provision01.png)
5. Guarde los cambios.
4. Haga clic en **perfiles de aprovisionamiento** > **desarrollo** y crear un nuevo perfil de aprovisionamiento para la aplicación de desarrollo: 

    [ ![](homekit-images/provision02.png "Crear un nuevo perfil de aprovisionamiento para la aplicación de desarrollo")](homekit-images/provision02.png)
5. Ya sea descargar e instalar el nuevo perfil de aprovisionamiento o use Xcode para descargar e instalar el perfil.
6. Editar las opciones de proyecto Xamarin.iOS y asegúrese de que está usando el perfil de aprovisionamiento que acaba de crear: 

    [ ![](homekit-images/provision03.png "Seleccione el perfil de aprovisionamiento que acaba de crear")](homekit-images/provision03.png)
7. A continuación, edite la **Info.plist** de archivos y asegúrese de que está utilizando el identificador de aplicación que se usó para crear el perfil de aprovisionamiento: 

    [ ![](homekit-images/provision04.png "Establecer el identificador de aplicación ")](homekit-images/provision04.png)
8. Por último, edite su **Entitlements.plist** de archivos y asegúrese de que el **HomeKit** derechos se ha seleccionado: 

    [ ![](homekit-images/provision05.png "Habilitar los derechos de HomeKit")](homekit-images/provision05.png)
9. Guarde los cambios a todos los archivos.

Con esta configuración en su lugar, la aplicación ahora está lista para tener acceso a las API de Framework HomeKit. Para obtener información detallada sobre el aprovisionamiento, vea nuestra [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) y [aprovisionamiento de la aplicación](~/ios/get-started/installation/device-provisioning/index.md) guías.

> [!IMPORTANT]
> **Nota:** probar una aplicación HomeKit habilitado requiere un dispositivo iOS real que se ha aprovisionado correctamente para el desarrollo. No se pueden probar HomeKit desde el simulador de iOS.

## <a name="the-homekit-accessory-simulator"></a>El simulador HomeKit accesorio

Para proporcionar una manera de probar todos los dispositivos posibles domótica y servicios, sin tener que definir un dispositivo físico, creado Apple el _HomeKit accesorio simulador_. Con este simulador, puede instalar y configurar dispositivos HomeKit virtuales.

### <a name="installing-the-simulator"></a>Instalar el simulador

Apple proporciona el simulador de accesorio HomeKit como una descarga independiente de Xcode, por lo que deberá instalarlo antes de continuar.

Haga lo siguiente:

1. En un explorador web, visite [descargas para desarrolladores de Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Descargue el **herramientas adicionales para Xcode xxx** (donde xxx es la versión de Xcode que ha instalado): 

    [ ![](homekit-images/simulator01.png "Descargar las herramientas adicionales para Xcode")](homekit-images/simulator01.png)
3. Abra la imagen de disco e instale las herramientas de la **aplicaciones** directory.

Con el simulador de accesorio HomeKit instalado, se pueden crear accesorios virtuales para pruebas.

### <a name="creating-virtual-accessories"></a>Creación de accesorios virtuales

Para iniciar el simulador de accesorio HomeKit y crear algunos accesorios virtuales, haga lo siguiente:

1. En la carpeta de aplicaciones, iniciar el simulador de accesorio HomeKit: 

    [ ![](homekit-images/simulator02.png "El simulador HomeKit accesorio")](homekit-images/simulator02.png)
2. Haga clic en el  **+**  botón y seleccione **nuevo accesorio...** : 

    [ ![](homekit-images/simulator03.png "Agregar un nuevo accesorio")](homekit-images/simulator03.png)
3. Rellene la información sobre el descriptor de acceso nuevo y haga clic en el **finalizar** botón: 

    [ ![](homekit-images/simulator04.png "Rellene la información sobre el descriptor de acceso nuevo")](homekit-images/simulator04.png)
4. Haga clic en el **Agregar servicio...** botón y seleccione un tipo de servicio en la lista desplegable: 

    [ ![](homekit-images/simulator05.png "Seleccione un tipo de servicio en la lista desplegable")](homekit-images/simulator05.png)
5. Proporcionar un **nombre** para el servicio y haga clic en el **finalizar** botón: 

    [ ![](homekit-images/simulator06.png "Escriba un nombre para el servicio")](homekit-images/simulator06.png)
6. Puede proporcionar características opcionales para un servicio, haga clic en el **Agregar característica** botón y configurar las opciones necesarias: 

    [ ![](homekit-images/simulator07.png "Configurar las opciones necesarias")](homekit-images/simulator07.png)
7. Repita los pasos anteriores para crear uno de cada tipo de dispositivo virtual domótica admitido por HomeKit.

Con algunos ejemplo virtual HomeKit accesorios creado y configurado, ahora puede consumir y controlar estos dispositivos de la aplicación Xamarin.iOS.

## <a name="configuring-the-infoplist-file"></a>Configuración del archivo Info.plist

Nuevo para iOS 10 (o superior), el desarrollador necesita agregar el `NSHomeKitUsageDescription` clave a la aplicación `Info.plist` de archivos y proporcionar una cadena para declarar ¿por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario. Esta cadena se presentará para el usuario la primera vez que ejecuta la aplicación:

[ ![](homekit-images/info01.png "El cuadro de diálogo de permiso HomeKit")](homekit-images/info01.png)

Para establecer esta clave, haga lo siguiente:

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. En la parte inferior de la pantalla, cambie a la **origen** vista.
3. Agregue un nuevo **entrada** a la lista.
4. En la lista desplegable, seleccione **privacidad - descripción de uso de HomeKit**: 

    [ ![](homekit-images/info02.png "Seleccione privacidad - descripción de uso de HomeKit")](homekit-images/info02.png)
5. Escriba una descripción de por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario: 

    [ ![](homekit-images/info03.png "Escriba una descripción")](homekit-images/info03.png)
6. Guarde los cambios en el archivo.

> [!IMPORTANT]
> **Nota:** error al configurar el `NSHomeKitUsageDescription` clave en el `Info.plist` archivo dará como resultado de la aplicación _en modo silencioso errores_ (se cierra el sistema en tiempo de ejecución) sin errores cuando se ejecuta en iOS 10 (o posterior).

## <a name="connecting-to-homekit"></a>Conectarse a HomeKit

Para comunicarse con HomeKit, debe crear primero una instancia de la aplicación de Xamarin.iOS la `HMHomeManager` clase. El Administrador de inicio es el punto de entrada central HomeKit y es responsable de proporcionar una lista de casas disponibles, actualización y mantenimiento de esa lista y devolver al usuario _principal principal_.

La `HMHome` objeto contiene toda la información acerca de una casa ofrecen a incluidos los salones, grupos o zonas que puede contener, junto con cualquier accesorios domótica que se han instalado. Para poder realizar cualquier operación en HomeKit, al menos una `HMHome` debe ser creado y asignado como el directorio principal de inicio.

La aplicación es responsable de comprobar si existe un inicio de principal y la creación y asignación si no es así.

### <a name="adding-a-home-manager"></a>Agregar un administrador de inicio

Para agregar conocimiento HomeKit a una aplicación de Xamarin.iOS, edite el **AppDelegate.cs** archivo para editarlo y darle un aspecto similar al siguiente:

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

Cuando se ejecuta la aplicación por primera vez, se le pide al usuario si desea permitirlo tener acceso a su información de HomeKit:

[ ![](homekit-images/home01.png "Se le pide al usuario si desea permitirlo tener acceso a su información de HomeKit")](homekit-images/home01.png)

Si el usuario responde **Aceptar**, la aplicación podrá trabajar con sus accesorios HomeKit en caso contrario, no usará ni las llamadas a HomeKit producirá un error.

Con el Administrador de inicio en su lugar, a continuación la aplicación necesitará ver si se ha configurado un inicio de principal y, si no es así, proporcionan una manera para que el usuario crear y asignar uno.

### <a name="accessing-the-primary-home"></a>Obtener acceso a la página principal del principal

Como se mencionó anteriormente, una casa principal debe ser creada y configurada antes de HomeKit está disponible y es responsabilidad de la aplicación para proporcionar una manera para que el usuario crear y asignar una casa principal si uno no existe ya.

Cuando la aplicación primero inicia o se devuelve desde el fondo, es necesario supervisar el `DidUpdateHomes` eventos de la `HMHomeManager` clase para comprobar la existencia de una casa principal. Si no existe, debe proporcionar una interfaz para el usuario crear uno.

El código siguiente puede agregarse a un controlador de vista para comprobar si el directorio principal de inicio:

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

Cuando el administrador realiza una conexión a HomeKit, el `DidUpdateHomes` se desencadenará el evento, se cargarán los hogares existentes en la colección del Administrador de casas y se cargará la página principal del principal, si está disponible.

### <a name="adding-a-primary-home"></a>Agregar un inicio de principal

Si el `PrimaryHome` propiedad de la `HMHomeManager` es `null` después de un `DidUpdateHomes` eventos, debe proporcionar una manera para que el usuario crear y asignar una casa principal antes de continuar.

Normalmente, la aplicación presentará un formulario para el usuario para el nombre de una nueva página principal que, a continuación, se pasa al administrador de inicio para el programa de instalación como la página principal del principal. Para el **HomeKitIntro** aplicación de ejemplo, una vista modal se creó en el Diseñador de IOS y llama a la `AddHomeSegue` desplazará tranquilamente desde la interfaz principal de la aplicación.

Proporciona un campo de texto para el usuario escriba un nombre para la nueva página principal y un botón para agregar el directorio de inicio. Cuando el usuario puntea el **complemento principal** botón, el código siguiente llama al administrador de inicio para agregar el directorio de inicio:

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

El `AddHome` método intentará crear la nueva página principal y volver a la rutina de devolución de llamada especificada. Si el `error` propiedad no es `null`, se ha producido un error y se debe presentar al usuario. Los errores más comunes se deben a un nombre de inicio no es único o el administrador principal de la imposibilidad de comunicarse con HomeKit.

Si el directorio de inicio se creó correctamente, debe llamar a la `UpdatePrimaryHome` método para establecer la nueva página principal como la página principal del principal. Una vez más, si la `error` propiedad no es `null`, se ha producido un error y se debe presentar al usuario.

También debe supervisar el inicio del Administrador de `DidAddHome` y `DidRemoveHome` eventos y actualización de la interfaz de usuario de la aplicación según sea necesario.

> [!IMPORTANT]
> **Nota:** el `AlertView.PresentOKAlert` método usado en el código de ejemplo anterior es una clase auxiliar en la aplicación de HomeKitIntro que hace que trabajar con iOS alertas con mayor facilidad.


## <a name="finding-new-accessories"></a>Buscar nuevos Accesorios

Una vez que una ubicación principal original se ha definido o cargado desde el Administrador de inicio, puede llamar la aplicación de Xamarin.iOS la `HMAccessoryBrowser` para buscar cualquier accesorios domótica nuevas y agregarlas a un principal.

Llame a la `StartSearchingForNewAccessories` método para empezar la búsqueda de accesorios nuevos y `StopSearchingForNewAccessories` método cuando acabe.

> [!IMPORTANT]
> **Nota:** `StartSearchingForNewAccessories` no debe dejar ejecutando durante largos períodos de tiempo, ya que afectará negativamente al tanto de la batería y el rendimiento del dispositivo iOS. Apple sugiere que realiza la llamada `StopSearchingForNewAccessories` después de un minuto o buscar solo cuando se presenta la interfaz de usuario de accesorio buscar al usuario.

El `DidFindNewAccessory` evento se llamará cuando se detectan nuevos Accesorios y se agregará a la `DiscoveredAccessories` lista en el Explorador de accesorio.

El `DiscoveredAccessories` lista contendrá una colección de `HMAccessory` objetos que definen una determinada HomeKit habilitado dispositivo domótica y sus servicios disponibles, como las luces y control de la puerta de artículos.

Una vez que se ha encontrado el descriptor de acceso nuevo, debe presentarse al usuario y por lo que podrán seleccionarla y agregar un directorio de inicio. Ejemplo:

[ ![](homekit-images/accessory01.png "Buscar un accesorio nuevo")](homekit-images/accessory01.png)

Llame a la `AddAccessory` método para agregar el descriptor de acceso seleccionada a la colección del principal. Por ejemplo:

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

Si el `err` propiedad no es `null`, se ha producido un error y se debe presentar al usuario. En caso contrario, el usuario se le pide que escriba el código de programa de instalación para el dispositivo que desea agregar:

[ ![](homekit-images/accessory02.png "Escriba el código de programa de instalación para el dispositivo que desea agregar")](homekit-images/accessory02.png)

En el simulador de accesorio HomeKit este número puede encontrarse en el **código de instalación** campo:

[ ![](homekit-images/accessory03.png "El campo de código de programa de instalación en el simulador de accesorio HomeKit")](homekit-images/accessory03.png)

Para accesorios HomeKit reales, el código de instalación o se imprimirán en una etiqueta en el propio dispositivo, en la caja del producto o en el manual del usuario del accesorio.

Debe supervisar el explorador Accesorios `DidRemoveNewAccessory` eventos y actualizar el usuario de la interfaz para quitar un accesorio de la lista disponible una vez que el usuario ha agregado a su colección.

## <a name="working-with-accessories"></a>Trabajar con accesorios

Una vez una casa de principal y no ha establecido y accesorios se agregaron a ella, puede presentar una lista de accesorios (y, opcionalmente, los centros de reunión) para que el usuario trabajar con.

La `HMRoom` objeto contiene toda la información sobre una habitación determinada y los accesorios que pertenecen a ella. Los centros de reunión, opcionalmente, pueden organizarse en una o más zonas. Un `HMZone` contiene toda la información acerca de una zona determinada y todas las salas que pertenecen a ella.

Para este ejemplo, se le pueden mantener las cosas sencillas y trabajar con accesorios del principal directamente, en lugar de su organización en los centros de reunión o zonas.

El `HMHome` objeto contiene una lista de accesorio asignado que se puede presentar al usuario en su `Accessories` propiedad. Por ejemplo:

[ ![](homekit-images/accessory04.png "Accesorio de ejemplo")](homekit-images/accessory04.png)

Formulario aquí, el usuario puede seleccionar un accesorio determinado y trabajar con los servicios que proporciona.

## <a name="working-with-services"></a>Trabajar con los servicios

Cuando el usuario interactúa con un determinado dispositivo habilitado domótica de HomeKit, normalmente es a través de los servicios que proporciona. El `Services` propiedad de la `HMAccessory` clase contiene una colección de `HMService` ofrece un dispositivo de objetos que definen los servicios.

Los servicios son cosas como las luces, Termostatos, abren el puerta artículos, conmutadores o bloqueos. Algunos dispositivos (por ejemplo, una tapa artículos) proporcionará más de un servicio, por ejemplo, una luz y la capacidad de abrir o cerrar una puerta.

Además de los servicios específicos que proporciona un accesorio determinado, cada accesorio contiene un `Information Service` que define las propiedades, como su nombre, fabricante, modelo y número de serie.

### <a name="accessory-service-types"></a>Tipos de servicio Accesorios

Los siguientes tipos de servicio están disponibles a través de la `HMServiceType` enum:

- **AccessoryInformation** -proporciona información sobre el dispositivo de domótica determinado (accesorio).
- **AirQualitySensor** -define un sensor de calidad de aire.
- **Batería** -define el estado de la batería de un accesorio.
- **CarbonDioxideSensor** -define un Sensor de dióxido de carbono.
- **CarbonMonoxideSensor** -define un Sensor monóxido de carbono.
- **ContactSensor** -define un sensor de contacto (por ejemplo, una ventana que se abre o se cierra).
- **Puerta de** -define un sensor de estado de la puerta (como si se abre o se cierra).
- **Ventilador** -define un ventilador de control remoto.
- **GarageDoorOpener** -define una tapa artículos.
- **HumiditySensor** -define un sensor de humedad.
- **LeakSensor** -define un sensor de pérdida (como para un calentador de agua o lavadora).
- **Bombilla** -define una luz independiente o una luz que forma parte de otro accesorio (por ejemplo, una tapa artículos).
- **LightSensor** -define un sensor de luz.
- **LockManagement** -define un servicio que administra un bloqueo de la puerta automatizadas.
- **LockMechanism** -define un bloqueo remoto controlado (por ejemplo, un bloqueo de puertas).
- **MotionSensor** -define un sensor de movimiento.
- **OccupancySensor** -define un sensor de ocupación.
- **Toma de corriente** -define un enchufe controlado remoto.
- **SecuritySystem** -define un sistema de seguridad principal.
- **StatefulProgrammableSwitch** -define un conmutador programable que permanece en un Estado conceda a desencadena una vez (por ejemplo, un conmutador flip).
- **StatelessProgrammableSwitch** -define un conmutador programable que devuelve a su estado inicial después de desencadenarse (por ejemplo, un botón de comando).
- **SmokeSensor** -define un detector de humo.
- **Cambiar** -define un modificador on/off, al igual que un conmutador de pared estándar.
- **TemperatureSensor de** -define un sensor de temperatura.
- **Termostato** -define un termostato inteligente utilizado para controlar un sistema HVAC.
- **Ventana** -define una ventana automatizada que caña se abran o cierren de forma remota.
- **WindowCovering** -define una ventana controlada remotamente que abarcan como descubrir que se puede abrir o cerrar.

### <a name="displaying-service-information"></a>Mostrar información de servicio

Después de cargar una `HMAccessory` puede consultar el individuo `HNService` objetos que proporciona y mostrar esa información al usuario:

[ ![](homekit-images/accessory05.png "Mostrar información de servicio")](homekit-images/accessory05.png)

Siempre debe debe comprobar la `Reachable` propiedad de un `HMAccessory` antes de intentar trabajar con ellos. Puede ser un accesorio inaccesible el usuario no está dentro del alcance del dispositivo o si se ha desconectado.

Una vez que se ha seleccionado un servicio, el usuario puede ver o modificar una o varias características de ese servicio para supervisar o controlar un dispositivo determinado domótica.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Trabajar con las características

Cada `HMService` objeto puede contener una colección de `HMCharacteristic` objetos que pueden proporcionar información sobre el estado del servicio (por ejemplo, una puerta que se abre o se cierra) o permitir al usuario ajustar un estado (por ejemplo, al establecer el color de una luz).

`HMCharacteristic` no solo proporciona información acerca de una característica y su estado, pero también proporciona métodos para trabajar con el estado a través de _característica metadatos_ (`HMCharacteristisMetadata`). Estos metadatos pueden proporcionar propiedades (por ejemplo, los intervalos de valores mínimo y máximo) que son útiles cuando se muestra información al usuario o a lo que permite modificar los Estados.

El `HMCharacteristicType` enum proporciona un conjunto de valores de metadatos de la característica que puede definir o modificar como se indica a continuación:

 - AdminOnlyAccess
 - AirParticulateDensity
 - AirParticulateSize
 - AirQuality
 - AudioFeedback
 - BatteryLevel
 - Luminosidad
 - CarbonDioxideDetected
 - CarbonDioxideLevel
 - CarbonDioxidePeakLevel
 - CarbonMonoxideDetected
 - CarbonMonoxideLevel
 - CarbonMonoxidePeakLevel
 - ChargingState
 - ContactState
 - CoolingThreshold
 - CurrentDoorState
 - CurrentHeatingCooling
 - CurrentHorizontalTilt
 - CurrentLightLevel
 - CurrentLockMechanismState
 - CurrentPosition
 - CurrentRelativeHumidity
 - CurrentSecuritySystemState
 - CurrentTemperature
 - CurrentVerticalTilt
 - FirmwareVersion
 - HardwareVersion
 - HeatingCoolingStatus
 - HeatingThreshold
 - HoldPosition
 - Matiz
 - Identificar
 - InputEvent
 - LeakDetected
 - LockManagementAutoSecureTimeout
 - LockManagementControlPoint
 - LockMechanismLastKnownAction
 - Registros
 - fabricante
 - Modelo
 - MotionDetected
 - nombre
 - ObstructionDetected
 - OccupancyDetected
 - OutletInUse
 - OutputState
 - PositionState
 - PowerState
 - RotationDirection
 - RotationSpeed
 - Saturación
 - número de serie
 - SmokeDetected
 - SoftwareVersion
 - StatusActive
 - StatusFault
 - StatusJammed
 - StatusLowBattery
 - StatusTampered
 - TargetDoorState
 - TargetHeatingCooling
 - TargetHorizontalTilt
 - TargetLockMechanismState
 - TargetPosition
 - TargetRelativeHumidity
 - TargetSecuritySystemState
 - TargetTemperature
 - TargetVerticalTilt
 - TemperatureUnits
 - Versión

### <a name="working-with-a-characteristics-value"></a>Trabajar con el valor de una característica

Para asegurarse de que la aplicación tiene el estado más reciente de una característica determinada, llame a la `ReadValue` método de la `HMCharacteristic` clase. Si el `err` propiedad no es `null`, se ha producido un error y puede o no puede presentarse al usuario.

La característica `Value` propiedad contiene el estado actual de la característica determinado como un `NSObject`y por lo tanto no puede trabajar con directamente en C#.

Para leer el valor, se agregó la clase auxiliar siguiente a la **HomeKitIntro** aplicación de ejemplo:

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

El `NSObjectConverter` se usa siempre que la aplicación necesita conocer el estado actual de una característica. Por ejemplo:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La línea anterior convierte el valor en un `float` que, a continuación, se puede utilizar en el código de C# de Xamarin.

Para modificar un `HMCharacteristic`, llame a su `WriteValue` método y ajustan el nuevo valor en un `NSObject.FromObject` llamar. Por ejemplo:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Si el `err` propiedad no es `null`, ha ocurrido un error y debe presentarse al usuario.

### <a name="testing-characteristic-value-changes"></a>Comprobación de los cambios de características de valor

Cuando se trabaja con `HMCharacteristics` y accesorios simuladas, las modificaciones realizadas en el `Value` propiedad puede supervisarse en el simulador de accesorio HomeKit.

Con el **HomeKitIntro** aplicación se ejecuta en el Hardware del dispositivo, cualquier cambio en los valores de una característica de iOS real debe considerarse casi al instante en el simulador de accesorio HomeKit. Por ejemplo, si se cambia el estado de una luz en la aplicación de iOS:

[ ![](homekit-images/test01.png "Cambiar el estado de una luz en una aplicación de iOS")](homekit-images/test01.png)

Debe cambiar el estado de la luz en el simulador de accesorio HomeKit. Si el valor no cambia, compruebe el estado del mensaje de error al escribir nuevos valores de características y asegúrese de que el accesorio sigue siendo accesible.

## <a name="advanced-homekit-features"></a>Características avanzadas HomeKit

En este artículo se trata las características básicas requeridas para trabajar con accesorios de HomeKit en una aplicación de Xamarin.iOS. Sin embargo, hay varias características avanzadas de HomeKit que no se tratan en esta introducción:

- **Los centros de reunión** -accesorios HomeKit habilitado opcionalmente pueden organizar en salones por el usuario final. Esto permite HomeKit accesorios presente de forma que sea fácil de comprender y trabajar con el usuario. Para obtener más información sobre cómo crear y mantener los centros de reunión, vea de Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentación.
- **Las zonas** -salones, opcionalmente, pueden organizarse en zonas que el usuario final. Una zona hace referencia a una colección de los centros de reunión que el usuario puede tratar como una sola unidad. Por ejemplo: escaleras arriba, escaleras abajo o sótano. Una vez más, esto permite HomeKit presentar y trabajar con accesorios de forma que tenga sentido para el usuario final. Para obtener más información sobre cómo crear y mantener las zonas, vea de Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentación.
- **Las acciones y establece la acción** -acciones modificar las características de servicio Accesorios y se pueden agrupar en conjuntos. Conjuntos de acciones actúan como secuencias de comandos para controlar un grupo de accesorios y coordinar sus acciones. Por ejemplo, una secuencia de comandos "Ver TV" podría cerrar los barridos, atenuar las luces y activar el televisor y el sonido del sistema. Para obtener más información sobre cómo crear y mantener las acciones y conjuntos de acciones, vea de Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) y [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentación.
- **Desencadenadores** : un desencadenador puede activar uno o más acción establecida cuando un determinado conjunto de condiciones se cumplen. Por ejemplo, encienda la luz de portch y bloquear todas las puertas externas cuando obtiene oscuro fuera. Para obtener más información sobre cómo crear y mantener los desencadenadores, vea de Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentación.

Puesto que estas características utilizan las mismas técnicas presentadas anteriormente, debe ser fáciles de implementar siguiente Apple [HomeKitDeveloper guía](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [directrices de interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/) y [ Referencia de marco HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Directrices de revisión de aplicación HomeKit

Antes de enviar un HomeKit aplicación Xamarin.iOS habilitada a iTunes Connect para el lanzamiento de iTunes App Store, asegúrese de seguir las directrices de Apple para las aplicaciones de HomeKit habilitado:

 - Propósito principal de la aplicación _debe_ escribirse domótica si usa el marco de trabajo HomeKit.
 - Texto de marketing de la aplicación debe notificar a los usuarios que se está utilizando HomeKit y deben proporcionar una directiva de privacidad.
 - Está prohibido recopilar información del usuario o mediante HomeKit con fines publicitarios.

Para la versión completa, revise las directrices, consulte de Apple [directrices de revisión de App Store](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>What's New en iOS 9

Apple ha realizado los siguientes cambios y adiciones en HomeKit IOS 9:

- **Mantener los objetos existentes** : si se modifica un accesorio existente, el Administrador de inicio (`HMHomeManager`) le informará de que el elemento específico que se ha modificado.
- **Identificadores persistentes** -todas las clases de HomeKit relevantes ahora incluyen un `UniqueIdentifier` propiedad para identificar de forma única un elemento determinado en HomeKit habilitado aplicaciones (o instancias de la misma aplicación).
- **Administración de usuarios** -agrega un controlador de vistas integradas para proporcionar administración de usuarios a través de los usuarios que tienen acceso a los dispositivos de HomeKit en la página principal del usuario principal.
- **Funciones de usuario** : HomeKit a los usuarios ahora tienen un conjunto de privilegios que controlan qué funciones pueden usar en HomeKit y HomeKit habilitado accesorios. La aplicación solo debe mostrar capacidades relativas al usuario actual. Por ejemplo, solo los administradores deben poder mantener otros usuarios.
- **Predefinidos plano** -plano predefinido se han creado para cuatro eventos comunes que se producen por un usuario HomeKit común: levantarse, deje, retorno, vaya a la cama. No se puede eliminar estas escenas predefinidas desde una casa.
- **Plano y Siri** -Siri tiene compatibilidad más lograda con un valor para escenas en iOS 9 y puede reconocen el nombre de cualquier escena definido en HomeKit. Un usuario puede ejecutar una escena simplemente habla su nombre a Siri.
- **Categorías de accesorios** -un conjunto de categorías predefinidas se han agregado a todos los accesorios y le ayuda a identificar el tipo de descriptor de acceso que se va a agregar a un inicio o trabajando desde dentro de la aplicación. Estas nuevas categorías están disponibles durante la instalación de accesorio.
- **Soporte técnico de Apple Watch** : HomeKit ahora está disponible para watchOS y el de Apple Watch podrán realizar control HomeKit dispositivos habilitados sin un iPhone está cerca de la inspección. HomeKit para watchOS es compatible con las siguientes capacidades: casas de visualización, controlar Accesorios y ejecutar plano.
- **Nuevo tipo de desencadenador de evento** : además de los desencadenadores de tipo de temporizador compatibles con iOS 8, iOS 9 ahora admite desencadenadores de eventos basada en estado de descriptor de acceso (por ejemplo, datos de sensor) o la ubicación geográfica. Usar desencadenadores de eventos `NSPredicates` para establecer condiciones para su ejecución.
- **Acceso remoto** -con acceso remoto, el usuario ya está capaces de controlar su HomeKit habilita inicio accesorios de automatización cuando se encuentran fuera de la casa en una ubicación remota. En iOS 8 esto solo se admite si el usuario tuviera una generación 3rd Apple TV del hogar. En iOS 9, se eleva esta limitación y se admite el acceso remoto a través de iCloud y el protocolo de accesorio HomeKit (GRACIA).
- **Nuevas capacidades de Bluetooth baja energía (n)** -HomeKit ahora admite más tipos de accesorios que pueden comunicarse a través del protocolo de Bluetooth baja energía (n). Con GRACIA túnel seguro, un accesorio HomeKit pueden exponer otro accesorio de Bluetooth a través de Wi-Fi (si está fuera del intervalo de Bluetooth). En iOS 9, Accesorios Bilitar tienen compatibilidad completa para las notificaciones y metadatos.
- **Nuevas categorías de accesorio** -Apple agregó las siguientes categorías de accesorio nuevo en iOS 9: cubiertas de las ventanas, puertas de motor y Windows, sistemas de alarma, sensores y conmutadores programables.

Para obtener más información sobre las nuevas características de HomeKit en iOS 9, consulte de Apple [HomeKit índice](https://developer.apple.com/homekit/) y [What's New en HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) vídeo.

## <a name="summary"></a>Resumen

En este artículo ha introducido framework de domótica HomeKit de Apple. Ha explicado cómo instalar y configurar dispositivos de prueba con el simulador de accesorio HomeKit y cómo crear una aplicación sencilla de Xamarin.iOS para descubrir, se comunican y controlar los dispositivos de automatización del hogar con HomeKit.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [What's New en iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Directrices de interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referencia de HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
