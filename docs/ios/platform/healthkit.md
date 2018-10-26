---
title: HealthKit en Xamarin.iOS
description: Este documento describe HealthKit, un marco de trabajo que se introdujo en iOS 8 que proporciona un almacén de datos centralizado, coordinado y seguro para obtener información relacionada con el mantenimiento. Describe cómo aprovisionar una aplicación HealthKit y cómo escribir código que usa el marco de trabajo HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: df0b0e8dd57129917f2d8dab07115551ca675acf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109229"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit en Xamarin.iOS

Kit de mantenimiento proporciona un almacén de datos seguro para la información del usuario relacionada con el mantenimiento. Las aplicaciones del Kit de mantenimiento pueden, con el permiso del usuario explícita, leer y escribir en este almacén de datos y recibir notificaciones cuando se agregan datos pertinentes. Las aplicaciones pueden presentar los datos o usuario puede usar la aplicación de mantenimiento proporcionado de Apple para ver un panel de todos sus datos.

Dado que datos relacionados con el estado están tan confidenciales y crucial, el Kit de mantenimiento está fuertemente tipado, con unidades de medida y una asociación explícita con el tipo de información que se está grabando (por ejemplo, el nivel de glucosa sanguínea o el ritmo cardiaco). Además, las aplicaciones de Kit de mantenimiento deben usar los derechos explícitos, debe solicitar acceso a los tipos de información determinados y el usuario debe conceder explícitamente el acceso a la aplicación a esos tipos de datos.

Este artículo se presentan:

- Requisitos de seguridad del Kit de mantenimiento, incluido el aprovisionamiento de la aplicación y que solicite permiso de usuario para tener acceso a la base de datos de estado Kit;
- Sistema de tipos del Kit de mantenimiento, lo que reduce la posibilidad de aplicar mal o interpretar erróneamente datos;
- Al escribir en el almacén de datos de estado Kit compartido, todo el sistema.

En este artículo no se tratará temas más avanzados, como consultar la base de datos, convertir entre las unidades de medida o recibir notificaciones de nuevos datos.

En este artículo, vamos a crear una aplicación de ejemplo para registrar la tasa de corazón del usuario:

[![](healthkit-images/image01.png "Una aplicación de ejemplo para registrar el ritmo cardíaco de usuarios")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisitos

Los siguientes son necesarios para completar los pasos que aparecen en este artículo:

- **Xcode 7 y iOS 8 (o superior)** : Xcode y API de iOS más reciente de Apple deben estar instalado y configurado en el equipo del desarrollador.
- **Visual Studio para Mac o Visual Studio** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el equipo del desarrollador.
- **iOS 8 (o superior) de dispositivo** : un dispositivo iOS ejecutando la última versión de iOS 8 o superior para las pruebas.

> [!IMPORTANT]
> Kit de mantenimiento se introdujo en iOS 8. Actualmente, el Kit de mantenimiento no está disponible en el simulador de iOS y la depuración requiere conexión a un dispositivo iOS físico.




## <a name="creating-and-provisioning-a-health-kit-app"></a>Crear y aprovisionar una aplicación del Kit de mantenimiento
Antes de que una aplicación Xamarin iOS 8 puede usar la API de HealthKit, debe tener correctamente configurado y aprovisionado. En esta sección se describen los pasos necesarios para configurar correctamente la aplicación de Xamarin.

Las aplicaciones de Kit de mantenimiento requieren:

- Explícita **Id. de aplicación**.
- Un **perfil de aprovisionamiento** asociada con dicho explícita **Id. de aplicación** y con **Kit Health** permisos.
- Un `Entitlements.plist` con un `com.apple.developer.healthkit` propiedad de tipo `Boolean` establecido en `Yes`.
- Un `Info.plist` cuyo `UIRequiredDeviceCapabilities` clave contiene una entrada con el `String` valor `healthkit`.
- El `Info.plist` también se deben tener entradas de explicación de privacidad adecuado: un `String` explicación de la clave `NSHealthUpdateUsageDescription` si la aplicación se va a escribir los datos y un `String` explicación de la clave `NSHealthShareUsageDescription` si la aplicación se va a leer el Kit de mantenimiento datos.

Para obtener más información sobre el aprovisionamiento de una aplicación de iOS, el [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) artículo en Xamarin **Introducción** serie describe la relación entre el desarrollador certificados, identificadores de aplicación Perfiles de aprovisionamiento y derechos de la aplicación.

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>Id. de aplicación explícito y el perfil de aprovisionamiento

La creación de una explícita **Id. de aplicación** y una adecuada **perfil de aprovisionamiento** se realiza dentro de Apple [centro de desarrollo de iOS](https://developer.apple.com/devcenter/ios/index.action). 

Actual **identificadores de aplicación** aparecen dentro de la [certificados, identificadores y perfiles](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) sección el centro de desarrollo. A menudo, esta lista mostrará **ID** valores de `*`, lo que indica que el **Id. de aplicación** - **nombre** puede utilizarse con cualquier número de sufijos. Este tipo *Wildcard App IDs* no se puede usar con el Kit de mantenimiento.
 
Para crear una explícita **Id. de aplicación**, haga clic en el **+** botón de la parte superior derecha para ir a la **registrar el identificador de aplicación de iOS** página:


[![](healthkit-images/image02.png "Registrar una aplicación en el Portal para desarrolladores de Apple")](healthkit-images/image02.png#lightbox)

Como se muestra en la ilustración anterior, después de crear una descripción de la aplicación, use la **identificador de aplicación explícito** sección para crear un identificador para la aplicación. En el **App Services** sección, verificación **Kit Health** en el **habilitar servicios** sección.

Cuando haya terminado, presione el **continuar** botón para registrar el **Id. de aplicación** en su cuenta. Se volvió a la **certificados, identificadores y perfiles** página. Haga clic en **perfiles de aprovisionamiento** le llevará a la lista de los perfiles de aprovisionamiento actuales y haga clic en el **+** situado en la esquina superior derecha para ir a la **agregar iOS Perfil de aprovisionamiento** página. Seleccione el **iOS App Development** opción y haga clic en **continuar** para llegar a la **seleccione Id. de aplicación** página. En este caso, seleccione la configuración explícita **Id. de aplicación** que especificó anteriormente:


[![](healthkit-images/image03.png "Seleccione el identificador de aplicación explícito")](healthkit-images/image03.png#lightbox)

Haga clic en **continuar** y de trabajo a través de las pantallas restantes, donde especificará la **certificados de desarrollador**, **dispositivos**y un **nombre** para este **perfil de aprovisionamiento**:

[![](healthkit-images/image04.png "Generar el perfil de aprovisionamiento")](healthkit-images/image04.png#lightbox)

Haga clic en **generar** y esperar la creación de su perfil. Descargue el archivo y haga doble clic en él para instalarlo en Xcode. Puede confirmar su instalación en **Xcode > Preferencias > cuentas > Ver detalles...** Debería ver el perfil de aprovisionamiento recién instalado, y debe tener el icono para el Kit de mantenimiento y otros servicios especiales en su **derechos** fila:

[![](healthkit-images/image05.png "Visualización del perfil en Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Asociar el identificador de aplicación y el perfil con la aplicación de Xamarin.iOS de aprovisionamiento

Una vez haya creado e instalado un adecuado **perfil de aprovisionamiento** tal como se describe, normalmente sería tiempo para crear una solución en Visual Studio para Mac o Visual Studio. Acceso de Kit de mantenimiento está disponible para cualquier operación de E/s C# o F# proyecto.

En lugar de recorrer el proceso de creación de un proyecto de Xamarin iOS 8 a mano, abra la aplicación de ejemplo asociada a este artículo (que incluye un guión gráfico y el código creado previamente). Para asociar la aplicación de ejemplo con el Kit de mantenimiento habilitado **perfil de aprovisionamiento**, en el **panel de solución**, haga doble clic en el proyecto y abrirá sus **opciones** cuadro de diálogo. Cambie a la **aplicación de iOS** panel y escriba la configuración explícita **Id. de aplicación** que creó anteriormente como la aplicación **identificador de paquete**:

[![](healthkit-images/image06.png "Escriba el identificador de aplicación explícito")](healthkit-images/image06.png#lightbox)

Ahora cambie a la **firma de lote de iOS** panel. El recientemente instalado **perfil de aprovisionamiento**, con su asociación a la configuración explícita **Id. de aplicación**, ahora estará disponible como el **perfil de aprovisionamiento**:

[![](healthkit-images/image07.png "Seleccione el perfil de aprovisionamiento")](healthkit-images/image07.png#lightbox)

Si el **perfil de aprovisionamiento** no está disponible, vuelva a comprobar la **identificador de paquete** en el **aplicación de iOS** panel frente a la que especificó en el **iOS Centro de desarrollo** y que la **perfil de aprovisionamiento** está instalado (**Xcode > Preferencias > cuentas > Ver detalles...** ).

Cuando el Kit de mantenimiento habilitado **perfil de aprovisionamiento** está seleccionado, haga clic en **Aceptar** para cerrar el cuadro de diálogo Opciones del proyecto.

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements.plist y los valores de Info.plist

La aplicación de ejemplo incluye un `Entitlements.plist` archivo (que es necesario para Kit de mantenimiento en aplicaciones habilitadas) y no se incluye en cada plantilla de proyecto. Si el proyecto no incluye los derechos, haga doble clic en el proyecto, elija **archivo > nuevo archivo... > iOS > Entitlements.plist** para agregar uno manualmente.

En última instancia, su `Entitlements.plist` debe tener el siguiente par de clave y valor:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

De forma similar, el `Info.plist` para la aplicación debe tener un valor de `healthkit` asociado con el `UIRequiredDeviceCapabilities` clave:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

La aplicación de ejemplo proporcionada con este artículo incluye un preconfigurada `Entitlements.plist` que incluye todas las claves necesarias.

<a name="programming" />

## <a name="programming-health-kit"></a>Kit de programación de mantenimiento

El almacén de datos de estado Kit es un almacén de datos privado, específicos del usuario que se comparte entre las aplicaciones. Porque la información de estado se distingue por lo tanto, el usuario debe tomar pasos positivos para permitir el acceso a datos. Este acceso puede ser parcial (escritura pero no de lectura, acceso para algunos tipos de datos, pero no otros, etc.) y se pueden revocar en cualquier momento. Las aplicaciones del Kit de mantenimiento deben escribirse de forma defensiva, teniendo en cuenta que muchos usuarios será dudas sobre cómo almacenar su información de estado.

Datos del Kit de mantenimiento se limita a Apple tipos especificados. Estos tipos se definen estrictamente: algunas, como el tipo de sangre, están limitados a los valores de una enumeración de Apple proporcionada determinados mientras otros que combinan una magnitud con una unidad de medida (por ejemplo, gramos, calorías y litros). Incluso los datos que comparten una unidad de medida compatible se distinguen por sus `HKObjectType`; por ejemplo, el sistema de tipos detectará un intento de errónea para almacenar un `HKQuantityTypeIdentifier.NumberOfTimesFallen` valor a un campo que se espera un `HKQuantityTypeIdentifier.FlightsClimbed` , aunque ambos usan la` HKUnit.Count` unidad de medida.

Los tipos que se pueda almacenables en el almacén de datos del Kit de mantenimiento son todas las subclases de `HKObjectType`. `HKCharacteristicType` los objetos almacenan biológicos sexo, tipo de sangre y fecha de nacimiento. Sin embargo, son más comunes `HKSampleType` objetos que representan los datos que se muestrean en un momento determinado o durante un período de tiempo. 

[![](healthkit-images/image08.png "Gráfico de objetos HKSampleType")](healthkit-images/image08.png#lightbox)

`HKSampleType` es abstracta y tiene cuatro subclases concretas. Actualmente hay solo un tipo de `HKCategoryType` datos, que es el modo de suspensión de análisis. La gran mayoría de los datos en el Kit de mantenimiento son del tipo `HKQuantityType` y almacenar sus datos en `HKQuantitySample` objetos, que se crean mediante el patrón de diseño Factory familiar:

[![](healthkit-images/image09.png "La gran mayoría de los datos en el Kit de mantenimiento son del tipo HKQuantityType y almacenar sus datos en objetos HKQuantitySample")](healthkit-images/image09.png#lightbox)

`HKQuantityType` tipos de intervalo de `HKQuantityTypeIdentifier.ActiveEnergyBurned` a `HKQuantityTypeIdentifier.StepCount`. 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>Solicitar permiso del usuario

Los usuarios finales debe realizar pasos positivos para permitir que una aplicación leer o escribir datos del Kit de mantenimiento. Esto se realiza a través de la aplicación de mantenimiento que viene preinstalada en dispositivos iOS 8. La primera vez que se ejecuta una aplicación de Kit de mantenimiento, se presenta al usuario con un sistema controlado por la **mantenimiento acceso** cuadro de diálogo:

[![](healthkit-images/image10.png "El usuario ve un cuadro de diálogo de mantenimiento acceso controlado por el sistema")](healthkit-images/image10.png#lightbox)

Más adelante, el usuario puede cambiar los permisos mediante la aplicación de mantenimiento **orígenes** cuadro de diálogo:

[![](healthkit-images/image11.png "El usuario puede cambiar los permisos mediante el cuadro de diálogo de mantenimiento aplicaciones orígenes")](healthkit-images/image11.png#lightbox)

Puesto que la información de estado es extremadamente confidencial, los desarrolladores de aplicaciones deben escribir sus programas de forma defensiva y con la expectativa de que se rechazó permisos y se puede cambiar mientras se ejecuta la aplicación. La forma más habitual consiste en solicitar permisos en el `UIApplicationDelegate.OnActivated` método y, a continuación, modificar la interfaz de usuario según corresponda.

### <a name="permissions-walkthrough"></a>Tutorial de permisos

En el proyecto aprovisionado para el Kit de mantenimiento, abra el `AppDelegate.cs` archivo. Tenga en cuenta el uso de la instrucción `HealthKit`; en la parte superior del archivo.


El código siguiente se relaciona con los permisos del Kit de mantenimiento:

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

Todo el código de estos métodos podría realizarse en línea en `OnActivated`, pero la aplicación de ejemplo usa métodos independientes para realizar su intención más clara: `ValidateAuthorization()` consta de los pasos necesarios para solicitar acceso a tipos que se escriben específico (y lectura, si la aplicación así lo desea) y `ReactToHealthCarePermissions()` es una devolución de llamada que se activa después de que el usuario interactúe con el cuadro de diálogo de permisos en el Health.app.

El trabajo de `ValidateAuthorization()` consiste en generar el conjunto de `HKObjectTypes` que la aplicación escribirá y solicitar autorización para actualizar los datos. En la aplicación de ejemplo, el `HKObjectType` es para la clave `KHQuantityTypeIdentifierKey.HeartRate`. Este tipo se agrega al conjunto `typesToWrite`, mientras que el conjunto `typesToRead` se deja en blanco. Estos conjuntos y una referencia a la `ReactToHealthCarePermissions()` devolución de llamada, se pasa a `HKHealthStore.RequestAuthorizationToShare()`.

El `ReactToHealthCarePermissions()` devolución de llamada que se llamará cuando el usuario ha interactuado con el cuadro de diálogo de permisos y se pasa dos fragmentos de información: un `bool` valor que será `true` si el usuario ha interactuado con el cuadro de diálogo de permisos y un `NSError`que, si es distinto de null, indica algún tipo de error asociado a presentar el cuadro de diálogo de permisos.

> [!IMPORTANT]
> Para ser claro sobre los argumentos a esta función: el _éxito_ y _error_ parámetros no indica si el usuario tiene permiso para tener acceso a datos de estado Kit! Sólo indican que el usuario se ha dado la oportunidad para permitir el acceso a los datos.

Para confirmar si la aplicación tiene acceso a los datos, el `HKHealthStore.GetAuthorizationStatus()` sirve, pasando `HKQuantityTypeIdentifierKey.HeartRate`. Según el estado devuelto, la aplicación habilita o deshabilita la capacidad de introducir datos. No hay ninguna experiencia de usuario estándar para tratar con una denegación de acceso y hay muchas opciones posibles. En la aplicación de ejemplo, el estado se establece en un `HeartRateModel` objeto singleton que, a su vez, provoca eventos relevantes.

## <a name="model-view-and-controller"></a>Modelo, vista y controlador

Para revisar la `HeartRateModel` objeto singleton, abra el `HeartRateModel.cs` archivo:

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

La primera sección es código reutilizable para crear controladores y los eventos genéricos. La parte inicial de la `HeartRateModel` clase también es código reutilizable para crear un objeto singleton de subprocesos.

A continuación, `HeartRateModel` expone 3 eventos: 

- `EnabledChanged` : Indica que se habilitó o deshabilitó el ritmo cardiaco almacenamiento (tenga en cuenta que inicialmente está deshabilitado el almacenamiento). 
- `ErrorMessageChanged` -Para esta aplicación de ejemplo, tenemos un modelo de control de errores muy sencillo: una cadena con el último error. 
- `HeartRateStored` -Se genera cuando un ritmo cardíaco se almacena en la base de datos del Kit de mantenimiento.

Tenga en cuenta que la cada vez que se activan estos eventos, se realiza a través de `NSObject.InvokeOnMainThread()`, que permite a los suscriptores actualizar la interfaz de usuario. Como alternativa, podrían documentarse los eventos de lo que se genera en subprocesos en segundo plano y la responsabilidad de garantizar la compatibilidad podría quedar a sus controladores. Consideraciones de subprocesos son importantes en las aplicaciones del Kit de mantenimiento porque muchas de las funciones, como la solicitud de permiso, son asincrónicas y ejecutan las devoluciones de llamada en subprocesos no principal.

El código específico de Heath Kit en `HeartRateModel` está en las dos funciones `HeartRateInBeatsPerMinute()` y `StoreHeartRate()`. 

`HeartRateInBeatsPerMinute()` convierte su argumento en un Kit de mantenimiento fuertemente tipado `HKQuantity`. El tipo de la cantidad es especificado por el `HKQuantityTypeIdentifierKey.HeartRate` y son las unidades de la cantidad `HKUnit.Count` dividido por `HKUnit.Minute` (en otras palabras, la unidad es *pulsaciones por minuto*). 

El `StoreHeartRate()` función toma un `HKQuantity` (en la aplicación de ejemplo, uno creado por `HeartRateInBeatsPerMinute()` ). Para validar sus datos, utiliza el `HKQuantity.IsCompatible()` método, que devuelve `true` si las unidades del objeto se pueden convertir en las unidades en el argumento. Si la cantidad que se creó con `HeartRateInBeatsPerMinute()` Obviamente esto devolverá `true`, pero también podría devolver `true` si la cantidad que se crearon como, por ejemplo, *pulsaciones por hora*. Es más frecuente `HKQuantity.IsCompatible()` puede usarse para validar la masa, distancia y la energía que el usuario o un dispositivo podría de entrada o mostrar en un sistema de medición (por ejemplo, unidades Imperial) pero que podrían almacenarse en otro sistema (por ejemplo, unidades métricas). 

Una vez que se haya validado la compatibilidad de la cantidad, la `HKQuantitySample.FromType()` se usa el método de fábrica para crear fuertemente tipado `heartRateSample` objeto. `HKSample` los objetos tienen una fecha de inicio y finalización; para lecturas de instantáneas, estos valores deben ser el mismo, tal como están en el ejemplo. El ejemplo también no establecer ningún dato de pares clave-valor en su `HKMetadata` argumento, pero uno podría utilizar código como el código siguiente para especificar la ubicación del sensor:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Una vez el `heartRateSample` ha sido creado, el código crea una nueva conexión a la base de datos con el uso de bloque. Dentro de dicho bloque, el `HKHealthStore.SaveObject()` método trata de la escritura asincrónica en la base de datos. La llamada a la expresión lambda resultante desencadena eventos relevantes, ya sea `HeartRateStored` o `ErrorMessageChanged`.

Ahora que se ha programado el modelo, es momento de ver cómo el controlador refleja el estado del modelo. Abra el `HKWorkViewController.cs` archivo. El constructor simplemente conecta el `HeartRateModel` singleton a los métodos de control de eventos (nuevamente, esto puede realizarse en línea con las expresiones lambda, pero métodos independientes para aclarar la intención un poco más obvio):

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

Estos son los controladores relevantes:

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

Obviamente, en una aplicación con un único controlador, sería posible evitar la creación de un objeto de modelo independiente y el uso de los eventos de flujo de control, pero el uso de objetos del modelo es más adecuado para aplicaciones del mundo real.

## <a name="running-the-sample-app"></a>Ejecutar la aplicación de ejemplo

El simulador de iOS no admite el Kit de mantenimiento. La depuración debe realizarse en un dispositivo físico con iOS 8.

Conectar un dispositivo de desarrollo iOS aprovisionado correctamente 8 a su sistema. Seleccione como destino de implementación en Visual Studio para Mac y en el menú, elija **ejecutar > depurar**.

> [!IMPORTANT]
> Errores de aprovisionamiento se detectarán en este momento. Para solucionar errores, revise la creación y aprovisionamiento de una sección de aplicación del Kit de estado anterior. Los componentes son: 
>
> - **Centro de desarrollo de iOS** -identificador de aplicación explícito & Kit Health habilitado el perfil de aprovisionamiento. 
> - **Opciones del proyecto** -identificador de paquete (identificador de aplicación explícito) y un perfil de aprovisionamiento.
> - **Código fuente** -Entitlements.plist & Info.plist

Suponiendo que se han establecido correctamente las disposiciones, se iniciará la aplicación. Cuando llega a su `OnActivated` método, solicitará la autorización del Kit de mantenimiento. La primera vez que se encuentra el sistema operativo, el usuario le mostrará el cuadro de diálogo siguiente:


[![](healthkit-images/image12.png "El usuario se presentará este cuadro de diálogo")](healthkit-images/image12.png#lightbox)

Habilitar la aplicación para actualizar los datos de ritmo cardiaco y volverá a aparecer la aplicación. El `ReactToHealthCarePermissions` devolución de llamada se activará de forma asincrónica. Esto hará que el `HeartRateModel’s` `Enabled` propiedad quiere cambiar, lo que provocará la `EnabledChanged` eventos, lo que hará que el `HKPermissionsViewController.OnEnabledChanged()` controlador de eventos para ejecutar, lo que permite el `StoreData` botón. El siguiente diagrama muestra la secuencia:


[![](healthkit-images/image13.png "Este diagrama muestra la secuencia de eventos")](healthkit-images/image13.png#lightbox)

Presione el **registro** botón. Esto hará que el `StoreData_TouchUpInside()` controlador se ejecute, que intentará analizar el valor de la `heartRate` campo de texto, convertir en un `HKQuantity` a través de la que se han descrito anteriormente `HeartRateModel.HeartRateInBeatsPerMinute()` de función y pasar esa cantidad a `HeartRateModel.StoreHeartRate()`. Como se explicó anteriormente, esto intentará almacenar los datos y se producirá una un `HeartRateStored` o `ErrorMessageChanged` eventos.

Haga doble clic en el **inicio** situado en el dispositivo y abra la aplicación de mantenimiento. Haga clic en el **orígenes** pestaña y verá la aplicación de ejemplo que aparece. Elíjalo y denegar el permiso para actualizar los datos de ritmo cardíaco. Haga doble clic en el **inicio** botón y vuelva a la aplicación. Una vez más, `ReactToHealthCarePermissions()` llamará, pero esta vez, porque se denegó el acceso, el **DatosAlmacenados** se deshabilitará el botón (tenga en cuenta que esto ocurre de forma asincrónica y el cambio en la interfaz de usuario puede ser visible para el usuario final).

## <a name="advanced-topics"></a>Temas avanzados

Leer datos del Kit de mantenimiento de base de datos es muy similar a la escritura de datos: uno especifica los tipos de datos uno está intentando obtener acceso a las solicitudes de autorización, y si esa autorización se concede, los datos están disponibles, con la conversión automática a las unidades compatibles de medida.

Hay una serie de funciones de consulta más sofisticadas que permiten que las consultas basadas en el predicado y las consultas que realizan actualizaciones cuando se actualizan los datos relevantes. 

Los desarrolladores de aplicaciones del Kit de mantenimiento deben revisar la sección del Kit de mantenimiento de Apple [directrices de revisión del App](https://developer.apple.com/app-store/review/guidelines/#healthkit).

Una vez que se entienden los modelos de sistema de tipos y seguridad, almacenar y leer los datos en la base de datos de estado Kit compartido es bastante sencillo. Muchas de las funciones dentro de Kit de mantenimiento funcionan de forma asincrónica y los desarrolladores de aplicaciones deben escribir sus programas de forma adecuada.

Al redactar este artículo, hay actualmente ningún equivalente al Kit de mantenimiento en Android o Windows Phone.

## <a name="summary"></a>Resumen

En este artículo que hemos visto cómo mantenimiento Kit permite que las aplicaciones almacenar, recuperar y el estado del recurso compartido de información relacionan, proporcionando al mismo tiempo una aplicación estándar de estado que permita el acceso de usuario y el control sobre estos datos. 

También hemos visto cómo va a reemplazar preocupaciones para obtener información relacionada con el mantenimiento privacidad, seguridad e integridad de datos y aplicaciones mediante el Kit de mantenimiento deben tratar con el aumento en complejidad en la aplicación aspectos de la administración (aprovisionamiento), codificación (estado del tipo de Kit el sistema) y el usuario experimente (control de usuario de permisos a través de los cuadros de diálogo del sistema y aplicación de mantenimiento). 

Por último, hemos echar un vistazo a una implementación sencilla del Kit de mantenimiento mediante la aplicación de ejemplo incluido que escribe datos de latido en el almacén de estado Kit y tiene un diseño asincrónicas basadas en.

## <a name="related-links"></a>Vínculos relacionados

- [HKWork (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
