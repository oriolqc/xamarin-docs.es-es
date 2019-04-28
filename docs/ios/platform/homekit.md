---
title: HomeKit en Xamarin.iOS
description: HomeKit es el marco de Apple para controlar los dispositivos de automatización del hogar. Este artículo presenta HomeKit y cubre configurar accesorios de prueba en el simulador de accesorios de HomeKit y la escritura de una aplicación Xamarin.iOS simple para interactuar con estos accesorios.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 6793190fa3278455a00d7ea08ab52a643c369a35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371624"
---
# <a name="homekit-in-xamarinios"></a>HomeKit en Xamarin.iOS

_HomeKit es el marco de Apple para controlar los dispositivos de automatización del hogar. Este artículo presenta HomeKit y cubre configurar accesorios de prueba en el simulador de accesorios de HomeKit y la escritura de una aplicación Xamarin.iOS simple para interactuar con estos accesorios._

[![](homekit-images/accessory01.png "Aplicación habilitada para un ejemplo de HomeKit")](homekit-images/accessory01.png#lightbox)

Apple introdujo HomeKit en iOS 8 como una manera de integrar sin problemas varios dispositivos de domótica de proveedores de una variedad de en una sola unidad coherente. Mediante la promoción de un protocolo común para detectar, configurar y controlar los dispositivos de automatización del hogar, HomeKit permite a los dispositivos de los proveedores no relacionado con la que funcionan conjuntamente, todo ello sin tener que coordinar los esfuerzos de los proveedores de individuales.

Con HomeKit, puede crear una aplicación de Xamarin.iOS que controla cualquier dispositivo HomeKit habilitado sin con proveedores proporcionados en las API o aplicaciones. Cómo usar HomeKit, puede hacer lo siguiente:

- Descubra nuevos dispositivos de automatización del hogar HomeKit habilitado y agregarlos a una base de datos que se mantendrá en todos los dispositivos del usuario de iOS.
- Programa de instalación, configurar, mostrar y controlar cualquier dispositivo en el HomeKit _base de datos de la configuración de inicio_.
- Comunicarse con cualquier dispositivo HomeKit preconfigurados y comandos para realizar acciones individuales o trabajan en conjunto, como la activación de todas las luces en la cocina.

Además de servir los dispositivos en la configuración base de datos principal a las aplicaciones de HomeKit habilitado, HomeKit proporciona acceso a los comandos de voz Siri. Dado que un programa de instalación de HomeKit configurado adecuadamente, el usuario puede emitir comandos de voz, como "Siri, activar las luces en el salón."

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>La base de datos de configuración principal

HomeKit organiza todos los dispositivos de automatización en una ubicación determinada en una colección. Esta colección proporciona una manera para el usuario agrupar sus dispositivos de automatización del hogar en unidades organizadas lógicamente con etiquetas significativas y legible.

La colección se almacena en una configuración base de datos principal que será automáticamente la copia de seguridad y sincronizados en todos los dispositivos del usuario de iOS. HomeKit proporciona las siguientes clases para trabajar con la base de datos en configuración de inicio:

- `HMHome` : Es el contenedor de nivel superior que contiene toda la información y las configuraciones para todos los dispositivos de automatización del hogar en una ubicación física única (p ej. una sola familia residencia). El usuario podría tener más de una residencia, como su casa principal y una casa de vacaciones. O bien, podrían tener diferentes "aloja" en la misma propiedad, como el principal de enrutamiento y una casa de invitado a través del garaje. En cualquier caso, al menos una `HMHome` objeto _debe_ configurarse y almacenan antes de que se puede escribir cualquier otra información de HomeKit.
- `HMRoom` : Aunque opcional, un `HMRoom` permite al usuario definir salas específicas dentro de una casa (`HMHome`), como: Cocina, baño, garaje o sala de estar. El usuario puede agrupar todos los dispositivos de automatización del hogar en una ubicación específica en su casa en un `HMRoom` y actuar como una unidad. Por ejemplo, solicitando Siri para apagar las luces de garaje.
- `HMAccessory` -Esto representa un usuario individual, HomeKit físico habiliten el dispositivo de automatización que se ha instalado en lugar de residencia del usuario (como un termostato inteligente). Cada `HMAccessory` se asigna a un `HMRoom`. Si el usuario no ha configurado las salas, HomeKit asigna accesorios a un salón predeterminado especial.
- `HMService` : Representa un servicio suministrado por un determinado `HMAccessory`, como el estado de activación/desactivación de una luz o su color (si se admite el cambio de color). Cada `HMAccessory` puede tener más de un servicio, como la apertura de la tapa de garaje que también incluye una luz. Además, un determinado `HMAccessory` podría tener servicios, como la actualización de firmware, que se encuentran fuera del control de usuario.
- `HMZone` -Permite al usuario agrupar una colección de `HMRoom` objetos en zonas lógicas, como siesta, escaleras abajo o sótano. Aunque es opcional, esto permite interacciones como preguntar Siri para activar todos de la luz está desactivado.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Aprovisionamiento de una aplicación de HomeKit

Debido a los requisitos de seguridad impuestos por HomeKit, una aplicación de Xamarin.iOS que usa el marco de trabajo de HomeKit debe estar configurada correctamente en el Portal de desarrollador de Apple y en el archivo de proyecto de Xamarin.iOS.

Haga lo siguiente:

1. Inicie sesión en el [Portal Apple Developer](https://developer.apple.com).
2. Haga clic en **certificados, identificadores y perfiles**.
3. Si aún no lo ha hecho, haga clic en **identificadores** y crear un identificador para la aplicación (por ejemplo, `com.company.appname`), editar de lo contrario, su identificador existente.
4. Asegúrese de que el **HomeKit** service se ha comprobado para el identificador dado: 

    [![](homekit-images/provision01.png "Habilitar el servicio de HomeKit para el identificador especificado")](homekit-images/provision01.png#lightbox)
5. Guarde los cambios.
4. Haga clic en **perfiles de aprovisionamiento** > **desarrollo** y cree un nuevo perfil para la aplicación de aprovisionamiento de desarrollo: 

    [![](homekit-images/provision02.png "Crear un nuevo perfil para la aplicación de aprovisionamiento de desarrollo")](homekit-images/provision02.png#lightbox)
5. Ya sea descargar e instalar el nuevo perfil de aprovisionamiento o usar Xcode para descargar e instalar el perfil.
6. Editar las opciones del proyecto de Xamarin.iOS y asegúrese de que está usando el perfil de aprovisionamiento que acaba de crear: 

    [![](homekit-images/provision03.png "Seleccione el perfil de aprovisionamiento que acaba de crear")](homekit-images/provision03.png#lightbox)
7. A continuación, edite su **Info.plist** de archivos y asegúrese de que está usando el identificador de aplicación que se usó para crear el perfil de aprovisionamiento: 

    [![](homekit-images/provision04.png "Establece el identificador de aplicación ")](homekit-images/provision04.png#lightbox)
8. Por último, edite su **Entitlements.plist** de archivos y asegúrese de que el **HomeKit** derecho se ha seleccionado: 

    [![](homekit-images/provision05.png "Habilitar los derechos de HomeKit")](homekit-images/provision05.png#lightbox)
9. Guarde los cambios a todos los archivos.

Con esta configuración en su lugar, la aplicación ahora está lista para tener acceso a las API de marco de trabajo de HomeKit. Para obtener información detallada sobre el aprovisionamiento, consulte nuestra [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) y [aprovisionamiento de la aplicación](~/ios/get-started/installation/device-provisioning/index.md) guías.

> [!IMPORTANT]
> Probar una aplicación de HomeKit habilitado, requiere un dispositivo iOS real que se ha aprovisionado correctamente para el desarrollo. No se pueden probar HomeKit desde el simulador de iOS.

## <a name="the-homekit-accessory-simulator"></a>El simulador de accesorios de HomeKit

Para proporcionar una manera de probar todos los dispositivos de automatización del hogar posibles y los servicios, sin necesidad de contar con un dispositivo físico, Apple creado el _simulador de accesorios de HomeKit_. Con este simulador, puede instalar y configurar dispositivos virtuales de HomeKit.

### <a name="installing-the-simulator"></a>Instalar el simulador

Apple proporciona el simulador de accesorios de HomeKit como una descarga independiente de Xcode, por lo que deberá instalarlo antes de continuar.

Haga lo siguiente:

1. En un explorador web, visite [descargas para desarrolladores de Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Descargue el **herramientas adicionales para Xcode xxx** (donde xxx es la versión de Xcode que ha instalado): 

    [![](homekit-images/simulator01.png "Descargue las herramientas adicionales de Xcode")](homekit-images/simulator01.png#lightbox)
3. Abra la imagen de disco e instalar las herramientas en su **aplicaciones** directory.

Con el simulador de accesorios de HomeKit instalado, se pueden crear accesorios virtuales para las pruebas.

### <a name="creating-virtual-accessories"></a>Creación de accesorios Virtual

Para iniciar el simulador de accesorios de HomeKit y crear algunos accesorios virtuales, realice lo siguiente:

1. En la carpeta de aplicaciones, iniciar el simulador de accesorios de HomeKit: 

    [![](homekit-images/simulator02.png "El simulador de accesorios de HomeKit")](homekit-images/simulator02.png#lightbox)
2. Haga clic en el **+** y seleccione **nuevo accesorio...** : 

    [![](homekit-images/simulator03.png "Agregar un nuevo accesorio")](homekit-images/simulator03.png#lightbox)
3. Rellene la información sobre el accesorio de nuevo y haga clic en el **finalizar** botón: 

    [![](homekit-images/simulator04.png "Rellene la información sobre el nuevo accesorio")](homekit-images/simulator04.png#lightbox)
4. Haga clic en el **Agregar servicio...** botón y seleccione un tipo de servicio en la lista desplegable: 

    [![](homekit-images/simulator05.png "Seleccione un tipo de servicio en la lista desplegable")](homekit-images/simulator05.png#lightbox)
5. Proporcione un **nombre** para el servicio y haga clic en el **finalizar** botón: 

    [![](homekit-images/simulator06.png "Escriba un nombre para el servicio")](homekit-images/simulator06.png#lightbox)
6. Puede proporcionar características opcionales para un servicio, haga clic en el **Agregar característica** botón y configurar las opciones necesarias: 

    [![](homekit-images/simulator07.png "Configuración de la configuración necesaria")](homekit-images/simulator07.png#lightbox)
7. Repita los pasos anteriores para crear uno de cada tipo de dispositivo de la automatización del hogar virtual que admita HomeKit.

Con algunos ejemplo virtuales accesorios de HomeKit creado y configurado, ahora puede consumir y controlar estos dispositivos de la aplicación de Xamarin.iOS.

## <a name="configuring-the-infoplist-file"></a>Configuración del archivo Info.plist

Nuevo para iOS 10 (y superior), el desarrollador tendrá que agregar el `NSHomeKitUsageDescription` clave a la aplicación `Info.plist` de archivo y proporcione una cadena para declarar ¿por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario. Esta cadena se le presentará el usuario la primera vez que ejecute la aplicación:

[![](homekit-images/info01.png "El cuadro de diálogo de permiso de HomeKit")](homekit-images/info01.png#lightbox)

Para establecer esta clave, haga lo siguiente:

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. En la parte inferior de la pantalla, cambie a la **origen** vista.
3. Agregue un nuevo **entrada** a la lista.
4. En la lista desplegable, seleccione **privacidad: descripción de uso de HomeKit**: 

    [![](homekit-images/info02.png "Seleccione privacidad: descripción de uso de HomeKit")](homekit-images/info02.png#lightbox)
5. Escriba una descripción de por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario: 

    [![](homekit-images/info03.png "Escriba una descripción")](homekit-images/info03.png#lightbox)
6. Guarde los cambios en el archivo.

> [!IMPORTANT]
> No se pudo establecer el `NSHomeKitUsageDescription` clave en el `Info.plist` archivo dará como resultado de la aplicación _con errores en modo silencioso_ (que se cierra el sistema en tiempo de ejecución) sin errores cuando se ejecuta en iOS 10 (o superior).

## <a name="connecting-to-homekit"></a>Conectarse a HomeKit

Para comunicarse con HomeKit, debe crear primero una instancia de la aplicación Xamarin.iOS el `HMHomeManager` clase. El administrador es el punto de entrada central HomeKit y es responsable de proporcionar una lista de resultados, actualizar y mantener esa lista y devolver al usuario _principal principal_.

La `HMHome` objeto contiene toda la información acerca de una casa dar, incluidas las salas, grupos o las zonas que pueden contener, junto con los accesorios de automatización del hogar que han instalado. Antes de poder realizar cualquier operación en HomeKit, al menos uno `HMHome` deben crearse y asignado como el lugar principal.

La aplicación es responsable de comprobando si existe un principal primario y la creación y asignación de uno si no es así.

### <a name="adding-a-home-manager"></a>Agregar un administrador de inicio

Para agregar el reconocimiento de HomeKit a una aplicación de Xamarin.iOS, edite el **AppDelegate.cs** archivo para editarlo y darle un aspecto similar al siguiente:

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

Cuando la aplicación se ejecuta primero, se pedirá al usuario si desea permitir para acceder a su información de HomeKit:

[![](homekit-images/home01.png "Se pedirá al usuario si desea permitir para acceder a su información de HomeKit")](homekit-images/home01.png#lightbox)

Si el usuario responde **Aceptar**, a continuación, la aplicación podrá trabajar con sus accesorios de HomeKit en caso contrario, no usará ni las llamadas a HomeKit se producirán un error.

Con el Administrador de inicio en su lugar, a continuación la aplicación necesitará ver si se ha configurado un principal primario y, si no, proporciona un método para el usuario crear y asignar uno.

### <a name="accessing-the-primary-home"></a>Obtener acceso a la página principal de principal

Como se indicó anteriormente, una casa principal debe crear y configurar antes de HomeKit está disponible y es responsabilidad de la aplicación para proporcionar una forma para el usuario crear y asignar una casa principal si aún no existe.

Cuando la aplicación en primer lugar se inicia o se devuelve desde un segundo plano, necesita supervisar la `DidUpdateHomes` eventos de la `HMHomeManager` clase para comprobar la existencia de un principal primario. Si no existe, debe proporcionar una interfaz para el usuario crear uno.

El siguiente código se puede agregar a un controlador de vista para comprobar si la página de principal primario:

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

Cuando el administrador realiza una conexión a HomeKit, el `DidUpdateHomes` se desencadenará el evento, se cargarán los hogares existentes a la colección del Administrador de casas y se cargará la página principal de la principal, si está disponible.

### <a name="adding-a-primary-home"></a>Adición de una casa principal

Si el `PrimaryHome` propiedad de la `HMHomeManager` es `null` después un `DidUpdateHomes` eventos, deberá proporcionar una forma para el usuario crear y asignar una casa principal antes de continuar.

Normalmente, la aplicación presentará un formulario para el usuario un nombre de un nuevo hogar que, a continuación, se pasa al administrador de inicio al programa de instalación como el lugar principal. Para el **HomeKitIntro** aplicación de ejemplo, una vista modal se creó en el Diseñador de IOS y lo llama el `AddHomeSegue` segue desde la interfaz principal de la aplicación.

Proporciona un campo de texto para el usuario que escriba un nombre para la página principal nueva y un botón para agregar la página principal. Cuando el usuario pulsa el **agregar principal** botón, el código siguiente llama al administrador de inicio para agregar la página principal:

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

El `AddHome` método intentará crear la nueva principal y vuelva a la rutina de devolución de llamada especificada. Si el `error` propiedad no es `null`, se ha producido un error y se debe presentar al usuario. Los errores más comunes son causados por un nombre de inicio no es único o el administrador principal de no poder comunicarse con HomeKit.

Si la página principal se creó correctamente, se debe llamar a la `UpdatePrimaryHome` método para establecer el nuevo lugar como el lugar principal. Nuevamente, si la `error` propiedad no es `null`, se ha producido un error y se debe presentar al usuario.

También debe supervisar el inicio del Administrador de `DidAddHome` y `DidRemoveHome` eventos y actualización de la interfaz de usuario de la aplicación según sea necesario.

> [!IMPORTANT]
> El `AlertView.PresentOKAlert` método usado en el código de ejemplo anterior es una clase auxiliar en la aplicación de HomeKitIntro que hace que trabajar con iOS alertas con mayor facilidad.


## <a name="finding-new-accessories"></a>Buscar nuevos Accesorios

Una vez que se ha definido un principal primario o cargado desde el Administrador de inicio, puede llamar la aplicación Xamarin.iOS el `HMAccessoryBrowser` para encontrar los accesorios de automatización del hogar nuevos y agregarlos a un hogar.

Llame a la `StartSearchingForNewAccessories` método para empezar a buscar nuevos Accesorios y `StopSearchingForNewAccessories` método cuando acabe.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` no se debe dejar ejecutando durante largos períodos de tiempo, ya que afectará negativamente al tanto de la batería y el rendimiento del dispositivo iOS. Apple sugiere lo que realiza la llamada `StopSearchingForNewAccessories` después de un minuto o buscar solo cuando se presenta la interfaz de usuario de accesorio buscar al usuario.

El `DidFindNewAccessory` evento se llamará cuando se detectan nuevos Accesorios y se agregará a la `DiscoveredAccessories` lista en el Explorador de accesorio.

El `DiscoveredAccessories` lista contendrá una colección de `HMAccessory` objetos que definen un determinado HomeKit habilitado el dispositivo de la automatización del hogar y sus servicios disponibles, como las luces o control de la puerta de garaje.

Una vez que se ha encontrado el accesorio de nuevo, se debe presentar al usuario y por lo que pueden seleccionarla y agregarlo a un hogar. Ejemplo:

[![](homekit-images/accessory01.png "Buscar un nuevo accesorio")](homekit-images/accessory01.png#lightbox)

Llame a la `AddAccessory` para agregar el accesorio seleccionado a la colección de la casa. Por ejemplo:

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

Si el `err` propiedad no es `null`, se ha producido un error y se debe presentar al usuario. En caso contrario, se pedirá al usuario que escriba el código de programa de instalación agregar el dispositivo:

[![](homekit-images/accessory02.png "Escriba el código de programa de instalación agregar el dispositivo")](homekit-images/accessory02.png#lightbox)

En el simulador de accesorios de HomeKit este número puede encontrarse bajo la **código del programa de instalación** campo:

[![](homekit-images/accessory03.png "El campo de código de programa de instalación en el simulador de accesorios de HomeKit")](homekit-images/accessory03.png#lightbox)

De accesorios de HomeKit real, o bien se imprimirá el código del programa de instalación en una etiqueta en el propio dispositivo, en el embalaje del producto o en el manual del usuario del accesorio.

Debe supervisar el explorador Accesorios `DidRemoveNewAccessory` eventos y el usuario de la actualización de la interfaz para quitar un accesorio en la lista disponible una vez que el usuario ha agregado a su colección.

## <a name="working-with-accessories"></a>Trabajar con accesorios

Una vez un principal primario y se ha establecido y accesorios se agregaron a ella, puede presentar una lista de los accesorios (y, opcionalmente, las salas) para que el usuario trabajar con.

La `HMRoom` objeto contiene toda la información acerca de una sala determinada y los accesorios que pertenecen a ella. Salas, opcionalmente, se pueden organizar en una o varias zonas. Un `HMZone` contiene toda la información acerca de una zona determinada y todas las salas que pertenecen a ella.

Para este ejemplo, se deberá ser mantener las cosas sencillas y trabajar con accesorios de una casa directamente, en lugar de organizarlos en salas o zonas.

El `HMHome` objeto contiene una lista de accesorio asignado que se puede presentar al usuario en su `Accessories` propiedad. Por ejemplo:

[![](homekit-images/accessory04.png "Un accesorio de ejemplo")](homekit-images/accessory04.png#lightbox)

Formulario de aquí, el usuario puede seleccionar un accesorio determinado y trabajar con los servicios que proporciona.

## <a name="working-with-services"></a>Trabajar con servicios

Cuando el usuario interactúa con un determinado dispositivo habilitado domótica de HomeKit, normalmente es a través de los servicios que proporciona. El `Services` propiedad de la `HMAccessory` clase contiene una colección de `HMService` ofrece un dispositivo de los objetos que definen los servicios.

Los servicios son cosas como las luces, Termostatos, puertas del garaje, conmutadores o bloqueos. Algunos dispositivos (por ejemplo, la apertura de la tapa de un garaje) proporcionará a más de un servicio, por ejemplo, una luz y la capacidad de abrir o cerrar una puerta.

Además de los servicios específicos que proporciona un accesorio determinado, cada accesorio contiene un `Information Service` que define las propiedades como su nombre, fabricante, modelo y número de serie.

### <a name="accessory-service-types"></a>Tipos de servicio de accesorios

Los siguientes tipos de servicio están disponibles a través de la `HMServiceType` enum:

- **AccessoryInformation** -proporciona información sobre el dispositivo de la automatización del hogar determinado (accesorios).
- **AirQualitySensor** -define un sensor de calidad del aire.
- **Batería** -define el estado de la batería del accesorio.
- **CarbonDioxideSensor** -define un Sensor de dióxido de carbono.
- **CarbonMonoxideSensor** -define un Sensor de monóxido de carbono.
- **ContactSensor** -define un sensor de contacto (por ejemplo, una ventana que se abrió o cerró).
- **Puerta** -define un sensor de estado de la puerta (como abierto o cerrado).
- **Ventilador** -define un ventilador de control remoto.
- **GarageDoorOpener** -define un garaje tapa.
- **HumiditySensor** -define un sensor de humedad.
- **LeakSensor** -define un sensor de pérdida (como de una máquina de lavado o calentador de agua).
- **Bombilla** -define una luz independiente o una luz que forma parte del accesorio otro (por ejemplo, la apertura de la tapa de un garaje).
- **LightSensor** -define un sensor de luz.
- **LockManagement** -define un servicio que administra un bloqueo de la puerta automatizadas.
- **LockMechanism** -define un bloqueo remoto controlado (por ejemplo, un puerta de bloqueo).
- **MotionSensor** -define un sensor de movimiento.
- **OccupancySensor** -define un sensor de ocupación.
- **Toma de corriente** -define un enchufe controlado remoto.
- **SecuritySystem** -define un sistema de seguridad principal.
- **StatefulProgrammableSwitch** -define un modificador programable que permanece en un estado determinado desencadenado una vez (por ejemplo, un conmutador flip).
- **StatelessProgrammableSwitch** -define un modificador programable que se devuelve a su estado inicial después de desencadenarse (por ejemplo, un botón de inserción).
- **SmokeSensor** -define un detector de humo.
- **Cambiar** -define un modificador on/off como un conmutador de pared estándar.
- **TemperatureSensor de** -define un sensor de temperatura.
- **Termostato** -define un termostato inteligente que se usan para controlar un sistema HVAC.
- **Ventana** -define una ventana automatizada que caña se abran o cierren de forma remota.
- **WindowCovering** -define una ventana controlada de forma remota, que abarcan como Persianas que se pueden abrir o cerrar.

### <a name="displaying-service-information"></a>Mostrar información de servicio

Después de cargar un `HMAccessory` puede consultar el individuo `HNService` objetos proporciona y mostrar esa información al usuario:

[![](homekit-images/accessory05.png "Mostrar información de servicio")](homekit-images/accessory05.png#lightbox)

Siempre debe comprobar la `Reachable` propiedad de un `HMAccessory` antes de intentar trabajar con ella. Puede ser un accesorio inaccesible el usuario no está dentro del intervalo del dispositivo o si se ha desconectado.

Una vez que se ha seleccionado un servicio, el usuario puede ver o modificar una o varias características de ese servicio para supervisar o controlar un dispositivo determinado automatización del hogar.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Trabajar con las características

Cada `HMService` objeto puede contener una colección de `HMCharacteristic` objetos que pueden proporcionar información sobre el estado del servicio (por ejemplo, una puerta que se abrió o cerró) o permitir al usuario ajustar un estado (por ejemplo, al establecer el color de una luz).

`HMCharacteristic` no solo proporciona información sobre una característica y su estado, pero también proporciona métodos para trabajar con el estado a través de _característica metadatos_ (`HMCharacteristisMetadata`). Estos metadatos pueden proporcionar propiedades (por ejemplo, los intervalos de valores mínimo y máximo) que son útiles al mostrar información al usuario o a lo que les permite modificar los Estados.

El `HMCharacteristicType` enum proporciona un conjunto de valores de metadatos de la característica que se puede definir o modificar como sigue:

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
 - Fabricante
 - Modelo
 - MotionDetected
 - Name
 - ObstructionDetected
 - OccupancyDetected
 - OutletInUse
 - OutputState
 - PositionState
 - PowerState
 - RotationDirection
 - RotationSpeed
 - Saturación
 - SerialNumber
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

### <a name="working-with-a-characteristics-value"></a>Trabajar con valor de una característica

Para asegurarse de que la aplicación tiene el estado más reciente de una característica determinada, llame a la `ReadValue` método de la `HMCharacteristic` clase. Si el `err` propiedad no es `null`, se ha producido un error y puede o no es posible que se presentan al usuario.

La característica `Value` propiedad contiene el estado actual de la característica especificada como un `NSObject`y por lo tanto no puede actuar directamente en C#.

Para leer el valor, la siguiente clase auxiliar se agregó a la **HomeKitIntro** aplicación de ejemplo:

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

El `NSObjectConverter` se usa siempre que la aplicación debe leer el estado actual de una característica. Por ejemplo:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La línea anterior convierte el valor en un `float` que, a continuación, se puede usar en Xamarin C# código.

Para modificar un `HMCharacteristic`, llame a su `WriteValue` método y ajustar el nuevo valor en un `NSObject.FromObject` llamar. Por ejemplo:

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

### <a name="testing-characteristic-value-changes"></a>Cambia el valor característico de pruebas

Cuando se trabaja con `HMCharacteristics` y accesorios simulados, las modificaciones en el `Value` propiedad puede supervisarse en el simulador de accesorios de HomeKit.

Con el **HomeKitIntro** aplicación que se ejecuta en el Hardware del dispositivo, los cambios realizados en el valor de una característica de iOS real debe considerarse casi al instante en el simulador de accesorios de HomeKit. Por ejemplo, si se cambia el estado de una luz en la aplicación de iOS:

[![](homekit-images/test01.png "Cambiar el estado de una luz en una aplicación de iOS")](homekit-images/test01.png#lightbox)

Debe cambiar el estado de la luz en el simulador de accesorios de HomeKit. Si el valor no cambia, compruebe el estado del mensaje de error al escribir nuevos valores de característica y asegúrese de que el accesorio sigue siendo accesible.

## <a name="advanced-homekit-features"></a>Características avanzadas de HomeKit

Este artículo ha tratado las características básicas necesarias para trabajar con accesorios de HomeKit en una aplicación de Xamarin.iOS. Sin embargo, hay varias características avanzadas de HomeKit que no se tratan en esta introducción:

- **Salas** -accesorios de HomeKit habilitada opcionalmente pueden organizar en salones por el usuario final. Esto permite HomeKit accesorios presente de forma que sea fácil de entender y trabajar con el usuario. Para obtener más información sobre cómo crear y mantener las salas, consulte Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentación.
- **Las zonas** -salas, opcionalmente, se pueden organizar en zonas por el usuario final. Una zona se refiere a una colección de salas de que el usuario puede tratar como una sola unidad. Por ejemplo: Siesta, escaleras abajo o sótano. Nuevamente, esto permite a HomeKit presentar y trabajar con accesorios de manera que tenga sentido para el usuario final. Para obtener más información sobre cómo crear y mantener las zonas, consulte Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentación.
- **Conjuntos de acciones y acciones** -acciones modificar características de accesorios de servicio y se pueden agrupar en conjuntos. Conjuntos de acciones de actúan como secuencias de comandos para controlar un grupo de accesorios y coordinar sus acciones. Por ejemplo, una secuencia de comandos "Ver TV" podría cerrar las persianas, atenuar las luces y activar el televisor y el sonido del sistema. Para obtener más información sobre cómo crear y mantener las acciones y los conjuntos de acciones, vea Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) y [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentación.
- **Desencadenadores** : un desencadenador puede activar una o más acción establecida cuando un determinado conjunto de condiciones se cumplen. Por ejemplo, encienda la luz portch y bloquear todas las puertas externas cuando llega oscuro fuera. Para obtener más información sobre cómo crear y mantener los desencadenadores, vea Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentación.

Dado que estas características usan las mismas técnicas presentadas más arriba, debe ser fáciles de implementar siguiente Apple [HomeKitDeveloper guía](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [directrices de interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/) y [ Referencia de Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Directrices de revisión de aplicación de HomeKit

Antes de enviar un HomeKit habilitar aplicación de Xamarin.iOS en iTunes Connect para el lanzamiento en iTunes App Store, asegúrese de seguir las directrices de Apple para aplicaciones de HomeKit habilitado:

 - Propósito principal de la aplicación _debe_ ser domótica si usa el marco de trabajo de HomeKit.
 - Texto de marketing de la aplicación debe notificar a los usuarios que se está usando HomeKit y deben proporcionar una directiva de privacidad.
 - Recopilación de información de usuario o usar HomeKit con fines publicitarios está prohibido.

Para la versión completa, revise las directrices, consulte Apple [directrices de revisión del App Store](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>Novedades de iOS 9

Apple ha realizado los siguientes cambios e incorporaciones a HomeKit para iOS 9:

- **Mantenimiento de los objetos existentes** : si se modifica un accesorio existente, el Administrador de inicio (`HMHomeManager`) le informará del elemento específico que se ha modificado.
- **Los identificadores persistentes** -todas las clases pertinentes de HomeKit ahora incluyen una `UniqueIdentifier` habilitada la propiedad para identificar de forma única un elemento determinado a través de HomeKit aplicaciones (o instancias de la misma aplicación).
- **Administración de usuarios** -agregar un controlador de vistas integradas para proporcionar administración de usuarios a través de los usuarios que tienen acceso a los dispositivos de HomeKit en la página principal del usuario principal.
- **Funciones de usuario** : HomeKit a los usuarios ahora tienen un conjunto de privilegios que controlan qué funciones pueden usar en HomeKit y HomeKit habilitado accesorios. La aplicación solo debe mostrar la funcionalidad pertinente al usuario actual. Por ejemplo, sólo los administradores deben poder mantener otros usuarios.
- **Predefinidos escenas** -predefinidos de segundo plano se han creado para cuatro eventos comunes que se producen para el usuario HomeKit medio: Ponerse en marcha, deje, devolver, vaya a la cama. No se puede eliminar estas escenas predefinidas de una casa.
- **Segundo plano y Siri** -Siri tiene soporte técnico más profundo de segundo plano en iOS 9 y puede reconoce el nombre de cualquier escena definido en HomeKit. Un usuario puede ejecutar una escena simplemente por su nombre a Siri de habla.
- **Categorías de accesorio** -se agregó a todos los accesorios y ayuda a identificar el tipo de accesorio que se agrega a un hogar o trabajando desde dentro de la aplicación un conjunto de categorías predefinidas. Estas nuevas categorías están disponibles durante la instalación del accesorio.
- **Soporte técnico de Apple Watch** : HomeKit ahora está disponible para watchOS y el Apple Watch podrán HomeKit dispositivos habilitados sin un iPhone está cerca de la inspección del control. HomeKit para watchOS admite las siguientes funcionalidades: Visualización de las casas, controlar Accesorios y ejecutar en segundo plano.
- **Nuevo tipo de desencadenador de evento** : además de los desencadenadores de tipo temporizador compatible con iOS 8, iOS 9 ahora admite los desencadenadores de eventos basada en estado de accesorio (por ejemplo, los datos de sensor) o la ubicación geográfica. Usar desencadenadores de eventos `NSPredicates` para establecer condiciones para su ejecución.
- **Acceso remoto** -con acceso remoto, el usuario ahora es capaz de controlar su HomeKit habilitado Home accesorios de automatización cuando se encuentran fuera de la casa en una ubicación remota. En iOS 8 esto solo se admite si el usuario tuviera una generación 3rd Apple TV en el hogar. En iOS 9, esta limitación se eleva y se permite el acceso remoto a través de iCloud y el protocolo de accesorios de HomeKit (GRACIA).
- **Nuevas capacidades de Bluetooth Low Energy (BLE)** -HomeKit ahora admite más tipos de accesorios que pueden comunicarse a través del protocolo de Bluetooth Low Energy (BLE). Mediante túnel seguro de GRACIA, un accesorio de HomeKit pueden exponer otro accesorio Bluetooth a través de Wi-Fi (si está fuera del intervalo de Bluetooth). En iOS 9, BLE accesorios tienen compatibilidad completa para las notificaciones y metadatos.
- **Nuevas categorías de accesorio** -Apple agregó las siguientes categorías de accesorio en iOS 9: Cubiertas de las ventanas, con motor puertas y Windows, sistemas de alarma, sensores y conmutadores programables.

Para obtener más información sobre las nuevas características de HomeKit en iOS 9, consulte Apple [HomeKit índice](https://developer.apple.com/homekit/) y [What ' s New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) vídeo.

## <a name="summary"></a>Resumen

En este artículo ha presentado el marco de automatización del hogar HomeKit de Apple. Se ha mostrado cómo instalar y configurar dispositivos de prueba mediante el simulador de accesorios de HomeKit y cómo crear una aplicación Xamarin.iOS simple para detectar, se comunican y controlar los dispositivos de automatización del hogar con HomeKit.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Directrices de interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referencia de Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
