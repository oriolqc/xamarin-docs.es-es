---
title: Id. de toque en Xamarin.iOS
description: Este documento describe cómo usar Touch ID, tecnología de autenticación de huellas dactilares biométrica de Apple, en aplicaciones de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 2d67bc71361e335515cfba8b5a20e157ed6b6b05
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114345"
---
# <a name="touch-id-in-xamarinios"></a>Id. de toque en Xamarin.iOS

Id. de toque se introdujo en iOS 7 como un medio para autenticar al usuario - similar a un código de acceso. Sin embargo, estaba limitado a desbloquear el dispositivo, utilizando el Store de la aplicación, mediante iTunes y autenticar sólo la cadena de claves de iCloud.

Ahora hay dos formas de usar Touch ID como mecanismo de autenticación en una aplicación iOS 8 mediante la API de autenticación Local. Actualmente no es posible usar la autenticación Local para autenticar de forma remota.

Para comprender totalmente Touch ID y vale la pena, debemos exploramos servicios de la cadena de claves y lo que significan estos nuevos cambios de los datos del usuario. Acceso a llaves también se desarrolla en iOS 8 mediante el uso de la nueva característica de listas de Control de acceso (ACL).

## <a name="keychain--secure-enclave"></a>Cadena de claves & Enclave seguro

Cadena de claves es una base de datos de gran tamaño para el almacenamiento seguro de contraseñas, claves, certificados y notas de un identificador de Apple individuales. En iOS 8 una aplicación siempre tiene acceso a sus propios elementos de la cadena de claves exclusivo y no puede obtener acceso a los elementos de la cadena de claves de otras aplicaciones. Esto difiere de OS X en la cadena de claves se desbloquea con una contraseña, lo que cualquier aplicación compatible con Keychain Services permite usar la cadena de claves. En este artículo nos centraremos en cómo funciona la cadena de claves en iOS 8.

Cadena de claves es una base de datos especializado, donde cada fila se conoce como un _llaves elemento_. Cada elemento describe los atributos de cadena de claves y se compone de los valores cifrados. Para permitir un uso eficaz de la cadena de claves, está optimizado para los elementos pequeños, o _secretos_.
Cada elemento de la cadena de claves está protegido por el código de acceso a los usuarios y un secreto de dispositivo único. Los elementos de la cadena de claves deben estar protegidos incluso cuando los usuarios no usan sus dispositivos. Esto se implementa en iOS permitiendo únicamente los elementos que están disponibles cuando el dispositivo se desbloquea, cuando el dispositivo está bloqueado dejarán de estar disponibles. También pueden almacenarse en una copia de seguridad cifrada. Una de las características clave de cadena de claves es exigir el control de acceso; una aplicación tiene acceso a su parte de la cadena de claves y se evitará todas las demás aplicaciones. El diagrama siguiente muestra cómo una aplicación interactúa con la cadena de claves:

[![](touchid-images/image1.png "Este diagrama ilustra cómo interactúa una aplicación con la cadena de claves")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave seguro

La cadena de claves no puede descifrar el elemento de la cadena de claves por sí mismo; en su lugar, se realiza en el *Enclave seguro*. El enclave seguro es un procesador conjunta en el chip de A7 es responsable de determinar a una coincidencia correcta de datos de huellas digitales del sensor de Touch ID con un proceso de impresión registrado. A continuación, se descifrar el elemento de la cadena de claves y devolver el secreto descifrado a la cadena de claves.

### <a name="working-with-keychain"></a>Trabajar con cadenas de claves

Primero debe consultar la aplicación en la cadena de claves para ver si existe una contraseña. Si no existe, deberá solicitar una contraseña para que el usuario no se pide continuamente. Si la contraseña debe actualizarse, pedir al usuario una contraseña nueva y pase el valor actualizado en la cadena de claves.

> [!NOTE]
> Después de usar un secreto recuperada de la cadena de claves, se deben purgar todas las referencias a los datos de la memoria. Nunca asignarla a una variable global.

## <a name="keychain-acl-and-touch-id"></a>Identificador de ACL de la cadena de claves y la función táctil

Lista de Control de acceso es un nuevo atributo de elemento de cadena de claves en iOS 8 que describe la información sobre qué debe ocurrir permitir que una operación determinada que se produzca. Esto podría ser en forma de mostrar un cuadro de diálogo de alerta o de solicitar un código de acceso. ACL permite establecer la accesibilidad y la autenticación para un elemento de la cadena de claves. El diagrama siguiente muestra cómo se asocia este nuevo atributo con el resto del elemento de cadena de claves:

[![](touchid-images/image2.png "Este diagrama muestra cómo se asocia este nuevo atributo con el resto del elemento de cadena de claves")](touchid-images/image2.png#lightbox)

A partir de iOS 8, ahora hay una nueva directiva de presencia del usuario, `SecAccessControl`, que se aplica mediante el enclave seguro en un iPhone 5s y versiones posteriores. Podemos ver en la tabla siguiente, simplemente cómo la configuración del dispositivo puede influir en la evaluación de directivas:

|Configuración del dispositivo|Evaluación de directivas|Mecanismo de copia de seguridad|
|--- |--- |--- |
|Dispositivo sin código de acceso|Sin acceso|Ninguna|
|Dispositivo con el código de acceso|Requiere el código de acceso|Ninguna|
|Dispositivo con Touch ID|Prefiere Touch ID|Permite el código de acceso|

Pueden confiar en todas las operaciones dentro el Enclave seguro entre sí. Esto significa que podemos utilizar el resultado de la autenticación de Touch ID para autorizar el descifrado de elemento de la cadena de claves. El Enclave seguro también mantiene un contador de coincidencias de Touch ID con errores, en el que caso de un usuario tendrá que volver a usar el código de acceso.
Un nuevo marco en iOS 8, llamado _autenticación Local_, es compatible con este proceso de autenticación en el dispositivo. Se explorará en la sección siguiente.

## <a name="local-authentication"></a>Autenticación local

Que se haya establecido en la sección anterior, las aplicaciones pueden usar la autenticación Local para autenticar al usuario en cumplimiento con la directiva de seguridad que se ha establecido en el dispositivo.

Actualmente, la API proporciona solo dos funciones: en primer lugar, que ayuda a los servicios existentes de la cadena de claves mediante el uso de nuevos Keychain Access Control Lists (ACL). Datos de cadena de claves se pueden desbloqueadas con la autenticación correcta de una huella digital de los usuarios.

En segundo lugar, LocalAuthentication proporciona dos métodos para autenticar su aplicación localmente. Los desarrolladores deben usar `CanEvaluatePolicy` para determinar si el dispositivo es capaz de Aceptar Touch ID y, a continuación, `EvaluatePolicy` para iniciar la operación de autenticación.

Si bien ambas capacidades ofrecen autenticación local, no proporcionan un mecanismo para la aplicación o el usuario autenticar a un servidor remoto.
Autenticación local proporciona una nueva interfaz de usuario estándar para la autenticación. En el caso de Touch ID, se trata de una vista de alertas con dos botones, tal como se muestra a continuación. Un botón Cancelar, y para usar los medios de reserva de autenticación: el código de acceso. También hay un mensaje personalizado que se debe establecer. Es recomendable utilizar esto para explicar al usuario por qué se requiere la autenticación de Touch ID.

[![](touchid-images/image12.png "La alerta de autenticación de Touch ID")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Con los servicios de la cadena de claves

Examinamos un poco antes en cómo es un elemento de cadena de claves puede descifrar, con el enclave seguro para comprobar nuestro código de acceso. En iOS 8, podemos usar la autenticación Local para solicitar la verificación de Touch ID junto con la característica de listas de Control de acceso, que proporciona la implementación de que el mecanismo de reserva o la contraseña.
Para usar ACL deberíamos utilizar la `SecAccessControl` directiva y, a continuación, comprobar el estado del dispositivo usando `SecAccessible.WhenPasscodeSetThisDeviceOnly` o `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Consideraciones con ACL

Hay muchas cosas que nos debemos tener en cuenta al usar ACL con la cadena de claves y, a continuación se enumeran algunos de estos:

-   Use solo con la aplicación en primer plano: si se llama a cualquier operación de cadena de claves en un subproceso en segundo plano, que se producirá un error en la llamada.
-   Agregar y actualizar elementos de la cadena de claves pueden requerir la autenticación.
-   Si una solicitud devuelve varios elementos coincidentes en la cadena de claves, puede requerirse autenticación.
-   Elementos protegidos de ACL son solo dispositivo y, por tanto, no sincronizado o copia de seguridad.

### <a name="using-local-authentication-without-keychain-services"></a>Mediante la autenticación Local sin servicios de la cadena de claves

Autenticación local se creó como una manera de recopilar las credenciales, como el código de acceso o Touch ID y para trabajar con el Enclave seguro a fin de autenticar al usuario. Pensar como un puente entre la aplicación y el Enclave seguro, que puede comunicarse nunca directamente entre sí. También puede usarse para la evaluación de directiva para la aplicación.

Para ello, una aplicación llama a la evaluación de directivas dentro de la autenticación Local, que se inicia la operación dentro de Enclave seguro. Puede aprovechar para proporcionar autenticación a la aplicación, sin consultar y obtener acceso directamente al Enclave seguro.

[![](touchid-images/image13a.png "Mediante la autenticación Local sin servicios de la cadena de claves")](touchid-images/image13a.png#lightbox)

Mediante la autenticación Local en la aplicación proporciona una manera sencilla de implementar la verificación de usuario, por ejemplo, para desbloquear una característica únicamente para los ojos del propietario del dispositivo, como las aplicaciones bancarias, o para el control parental servir de ayuda para la persona aplicación. También se puede utilizar como una manera de extender la autenticación que ya existe: los usuarios, como su información para ser seguro, pero también desea tener opciones.

La seguridad de autenticación local difiere de la cadena de claves. Por ejemplo, cuando se usa la cadena de claves, es la relación de confianza entre el sistema operativo y el Enclave seguro. Con la autenticación local, es entre la aplicación y el sistema operativo, lo que significa que solo tienen acceso a los resultados de Enclave seguro, no seguro Enclave de sí mismo.

En la materia de seguridad, también es muy importante saber que hay **sin acceso** dedos registrados o imágenes de huellas digitales. El Enclave seguro es el propietario de esta información y, por lo que ningún otro componente del sistema tiene acceso a él.

Para usar Touch ID sin llaves mediante el aprovechamiento de la API de autenticación Local, hay algunas funciones que podemos usar. Se detallan a continuación:

*   `CanEvaluatePolicy` : Simplemente se comprobará para ver si el dispositivo es capaz de Aceptar Touch Id.
*   `EvaluatePolicy` : Se inicia la operación de autenticación y muestra la interfaz de usuario y devuelve un `true` o `false` respuesta.
*   `DeviceOwnerAuthenticationWithBiometrics` : Es la directiva que se puede usar para mostrar la pantalla de Touch ID. Es importante destacar que no hay ningún mecanismo de reserva de código de acceso aquí, en su lugar, debe implementar esta reserva de la aplicación para permitir a los usuarios omitir la autenticación de Touch ID.

Hay algunas advertencias con el uso de autenticación Local, que se enumeran a continuación:

*   Al igual que con la cadena de claves, puede realizarse solo en primer plano. Si se llama en un subproceso en segundo plano, se producirá un error.
*   Tenga en cuenta que puede producir un error en la evaluación de directivas. Un botón de código de acceso debe implementarse como un retroceso.
*   Debe proporcionar un `localizedReason` para explicar por qué se necesita la autenticación. Esto ayuda a crear relaciones de confianza con el usuario.

A continuación, en la siguiente sección, veremos cómo implementar la API teniendo presentes en estas advertencias.

## <a name="adding-touch-id-to-your-application"></a>Adición de Touch ID para la aplicación

En las secciones anteriores, analizamos la teoría detrás de acceso y autenticación mediante la cadena de claves y la autenticación Local. Ahora echaremos un vistazo a cómo se puede integrar Touch ID en la aplicación.

### <a name="walkthrough"></a>Tutorial

Así que veamos incorporación Touch Id. de autenticación a nuestra aplicación. En este tutorial vamos a actualizar el [guión gráfico tabla](https://developer.xamarin.com/samples/StoryboardTable/) ejemplo, agregar autenticación local para que funcione como la [guión gráfico de tabla: la autenticación Local](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) ejemplo, donde solo se permite usuarios autenticados para agregar tareas a la lista.

1. Descargue el ejemplo y ejecútelo en Visual Studio para Mac.
2. Haga doble clic en `MainStoryboard.Storyboard` para abrir el ejemplo en el Diseñador de iOS. Con este ejemplo, queremos agregar una nueva pantalla a nuestra aplicación, que controlará la autenticación. Esto irá antes de la actual `MasterViewController`.
3. Arrastre una nueva **View Controller** desde el **cuadro de herramientas** a la **superficie de diseño**. Establecer como el **controlador de vista raíz** por **Ctrl + arrastrar** desde el **controlador de navegación**:

    [![](touchid-images/image4.png "Establezca el controlador de vista raíz")](touchid-images/image4.png#lightbox)
4.  Nombre del nuevo controlador de vista `AuthenticationViewController`.
5. A continuación, arrastre un botón y lo coloca en el `AuthenticationViewController`. Llame a este método `AuthenticateButton`y asígnele el texto `Add a Chore`.
6. Crear un evento en el `AuthenticateButton` llamado `AuthenticateMe`.
7. Cree un manual de segue de `AuthenticationViewController` haciendo clic en la barra negra situada en la parte inferior y **Ctrl + arrastrar** en la barra para la `MasterViewController` y eligiendo **inserción** (o **mostrar** Si utiliza las clases de tamaño):

    [![](touchid-images/image5.png "Arrastre desde la barra de al método MasterViewController y eligiendo la inserción o mostrar")](touchid-images/image6.png#lightbox)
8. Haga clic en segue recién creado y asígnele el identificador `AuthenticationSegue`, tal y como se muestra a continuación:

    [![](touchid-images/image7.png "Establece el identificador de segue en AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. Agrega el código siguiente a `AuthenticationViewController`:

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

Esto es todo el código que necesita para implementar la autenticación de Touch ID mediante la autenticación Local. Las líneas resaltadas en la imagen siguiente muestran el uso de autenticación Local:

[![](touchid-images/image8.png "Las líneas resaltadas muestran el uso de autenticación Local")](touchid-images/image8.png#lightbox)

En primer lugar, es necesario establecer si el dispositivo es capaz de Aceptar Touch ID de entrada, mediante el `CanEvaluatePolicy` y pasar la directiva `DeviceOwnerAuthenticationWithBiometrics`. Si esto ocurre, a continuación, podemos mostrar la interfaz de Touch Id. de usuario mediante el uso de `EvaluatePolicy`. Hay tres tipos de información que se tiene que pasar a `EvaluatePolicy` : la propia directiva, una cadena que explica por qué es necesaria la autenticación y un controlador de respuesta. El controlador de respuesta indica que la aplicación en el caso de una autenticación correcta o incorrecta, lo que debe hacer. Echemos un vistazo más de cerca en el controlador de respuesta:

[![](touchid-images/image9.png "El controlador de respuesta")](touchid-images/image9.png#lightbox)

Se especifica el controlador de respuesta de tipo `LAContextReplyHandler`, que toma el éxito de los parámetros – un `bool` valor y un `NSError` llamado `error`. Si es correcto, esto es donde realmente realizaremos lo que sea que queremos autenticar: en este caso se muestra la pantalla que nos gustaría agregará una nueva tarea. Recuerde que una de las advertencias de autenticación Local es que debe ejecutar en primer plano, así que asegúrese de usar `InvokeOnMainThread`:

[![](touchid-images/image10.png "Usar InvokeOnMainThread para la autenticación Local")](touchid-images/image10.png#lightbox)

Por último, cuando la autenticación se realizó correctamente, queremos realizar la transición a la `MasterViewController`. El `PerformSegue` método puede utilizarse para hacer esto:

[![](touchid-images/image11.png "Llame al método para realizar la transición al método MasterViewController PerformSegue")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Resumen
En esta guía hemos analizado en la cadena de claves y cómo funciona en iOS. También exploramos la ACL, de la cadena de claves y los cambios a esta en iOS. A continuación, hemos echado un vistazo a la plataforma de autenticación Local, que es nueva en iOS 8 y, a continuación, examinamos la implementación de la autenticación de Touch ID en nuestra aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Tabla de guión gráfico: autenticación Local](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) 
- [Ejemplo de la sesión WWDC de cadena de claves](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Cadena de claves (ejemplo)](https://developer.xamarin.com/samples/Keychain/)
