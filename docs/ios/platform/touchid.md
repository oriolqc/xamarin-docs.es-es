---
title: "Id. de entrada táctil"
description: "Id. de entrada táctil es tecnología de autenticación de huellas dactilares biométrica de Apple."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: a2378cb439ceed94751e61fd44b54aae3a65bebd
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="touch-id"></a>Id. de entrada táctil

Id. de entrada táctil se introdujo en iOS 7 como un medio para autenticar al usuario - similar a un código de acceso. Sin embargo, se limitaba a desbloquear el dispositivo, uso de la tienda de aplicaciones, utilizando iTunes y autenticar sólo la cadena de claves de iCloud.

Ahora hay dos maneras de utilizar Touch ID como mecanismo de autenticación en una aplicación de iOS 8 mediante la API de autenticación Local. Actualmente no es posible utilizar la autenticación Local para autenticar de manera remota.

Para entender totalmente Touch ID y sus correspondientes, debemos exploramos llaveros servicios y lo que significan estos nuevos cambios de datos de sus usuarios. Acceso a llaveros también se desarrolla en iOS 8 mediante el uso de la nueva característica de listas de Control de acceso (ACL).

## <a name="keychain--secure-enclave"></a>Cadena de claves & Enclave segura

Cadena de claves es una base de datos grande para el almacenamiento seguro de contraseñas, claves, certificados y notas de un ID de Apple individuales. En iOS 8 una aplicación siempre tiene acceso a sus propios elementos de la cadena de claves exclusivo y no se puede obtener acceso a los elementos de la cadena de claves de otras aplicaciones. Esto difiere de OS X en la cadena de claves se desbloquea con una contraseña, que permite que cualquier aplicación compatible con Keychain Services usa la cadena de claves. En este artículo nos centraremos en funcionamiento de la cadena de claves en iOS 8.

Cadena de claves es una base de datos especializada, donde cada fila se conoce como un _llaveros elemento_. Cada elemento describe los atributos de cadena de claves y se compone de valores cifrados. Para permitir un uso eficaz de la cadena de claves, que está optimizado para pequeños elementos, o _secretos_.
Cada elemento de la cadena de claves está protegido por el código de acceso de los usuarios y un secreto de dispositivo único. Elementos de la cadena de claves deben estar protegidos incluso cuando los usuarios no usen sus dispositivos. Esto se implementa en iOS, solo tiene que permitir los elementos que están disponibles cuando el dispositivo se desbloquea: cuando el dispositivo está bloqueado dejarán de estar disponibles. También pueden almacenarse en una copia de seguridad cifrada. Una de las características clave de cadena de claves es exigir el control de acceso; una aplicación tiene acceso a la parte de la cadena de claves y se evitará el resto de las aplicaciones. El diagrama siguiente ilustra cómo una aplicación interactúa con la cadena de claves:

[![](touchid-images/image1.png "Este diagrama muestra cómo una aplicación interactúa con la cadena de claves")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave segura

La cadena de claves no puede descifrar el elemento de la cadena de claves por sí mismo; en su lugar, se realiza en el *seguros Enclave*. Al enclave seguro es un coprocesador dentro el chip de A7 que se encarga de determinar a una coincidencia correcta de datos de la huella digital del sensor Touch ID en un proceso de impresión registrado. A continuación, se descifrar el elemento de la cadena de claves y devuelve el secreto descifrado en la cadena de claves.

### <a name="working-with-keychain"></a>Trabajar con cadenas de claves

La aplicación debe consultar primero en la cadena de claves para ver si existe una contraseña. Si no existe, debe solicitar una contraseña para que el usuario no pide continuamente. Si la contraseña debe actualizarse, pedir al usuario una contraseña nueva y pase el valor actualizado a la cadena de claves.


> ℹ️ **Tenga en cuenta**: con los secretos se recibe de la base de datos, no es simplemente recomendado, pero se espera que ningún secreto se mantienen en la memoria. Solo debe mantener un secreto para como siempre y cuando es necesario y sin duda no asignar a una variable global.




## <a name="keychain-acl-and-touch-id"></a>Id. de ACL de la cadena de claves y entrada táctil

Lista de Control de acceso es un nuevo atributo de elemento de cadena de claves en iOS 8 que describe la información sobre qué debe ocurrir permitir que una operación determinada que se produzca. Esto podría ser en forma de mostrar un cuadro de diálogo Alerta o la solicitud de un código de acceso. ACL permite establecer la accesibilidad y la autenticación para un elemento de la cadena de claves. El diagrama siguiente muestra cómo se asocia este nuevo atributo con el resto del elemento de cadena de claves:

[![](touchid-images/image2.png "Este diagrama muestra cómo se asocia este nuevo atributo con el resto del elemento de cadena de claves")](touchid-images/image2.png#lightbox)

A partir de iOS 8, ahora hay una nueva directiva de presencia de usuario, `SecAccessControl`, que se aplica al enclave seguro en un iPhone 5 s y versiones posteriores. Podemos ver en la tabla siguiente, simplemente cómo la configuración del dispositivo puede influir en la evaluación de directivas:

<table width="100%" border="1px">
<thead>
<tr>
    <td>Configuración del dispositivo</td>
    <td>Evaluación de directivas</td>
    <td>Mecanismo de copia de seguridad</td>
</tr>
</thead>
<tbody>
<tr>
    <td>Dispositivo sin código de acceso</td>
    <td>Sin acceso</td>
    <td>Ninguna</td>
</tr>
<tr>
    <td>Dispositivo con código de acceso</td>
    <td>Requiere código de acceso</td>
    <td>Ninguna</td>
</tr>
<tr>
    <td>Dispositivo con el Id. de entrada táctil</td>
    <td>Preferir Touch ID</td>
    <td>Permite que código de acceso</td>
</tr>
</tbody>
</table>

Pueden confiar en todas las operaciones dentro de la Enclave proteger entre sí. Esto significa que podemos utilizar el resultado de la autenticación de Touch ID para autorizar el descifrado de elemento de la cadena de claves. Al proteger Enclave también mantiene un contador de errores coincidencias Touch ID, en el que caso de un usuario tendrá que volver a usar el código de acceso.
Un nuevo marco en iOS 8, denominado _autenticación Local_, es compatible con este proceso de autenticación en el dispositivo. Se explorará en la sección siguiente.

## <a name="local-authentication"></a>Autenticación local

Tal y como se establece en la sección anterior, las aplicaciones pueden utilizar la autenticación Local para autenticar al usuario en cumplimiento de la directiva de seguridad que se ha configurado en el dispositivo.

Actualmente, la API proporciona solo dos funciones: en primer lugar, ayuda a los servicios de cadenas de claves existentes mediante el uso de nuevos llaveros Control listas de acceso (ACL). Datos de cadena de claves se pueden desbloqueadas con la autenticación correcta de los usuarios mediante las huellas digitales.

En segundo lugar, LocalAuthentication proporciona dos métodos para autenticar su aplicación localmente. Los desarrolladores deben extremar `CanEvaluatePolicy` para determinar si el dispositivo es capaz de Aceptar Touch ID y, a continuación, `EvaluatePolicy` para iniciar la operación de autenticación.

Si bien ambas capacidades ofrecen autenticación local, no proporcionan un mecanismo para la aplicación o el usuario para autenticarse en un servidor remoto.
Autenticación local proporciona una nueva interfaz de usuario estándar para la autenticación. En el caso de Touch ID, se trata de una vista de alertas con dos botones, tal como se muestra a continuación. Un botón Cancelar y desea usar los medios de reserva de autenticación: el código de acceso. También hay un mensaje personalizado al que se debe establecer. Es recomendable utilizar esto para explicar al usuario ¿por qué se requiere la autenticación de Touch ID.

[![](touchid-images/image12.png "La alerta de autenticación Touch ID")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Con los servicios de la cadena de claves

Explicamos anteriormente un poco en forma de un elemento de la cadena de claves descifra, usando la enclave seguro para comprobar el código de acceso. En iOS 8, podemos usar autenticación Local para solicitar comprobación Touch ID junto con la característica de listas de Control de acceso, que proporciona la implementación del mecanismo de reserva o la contraseña.
Para utilizar ACL deberíamos utilizar la `SecAccessControl` directiva y, a continuación, comprobar el estado del dispositivo con `SecAccessible.WhenPasscodeSetThisDeviceOnly` o `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Consideraciones sobre con ACL

Hay muchas cosas que se deben tener en cuenta al usar ACL con la cadena de claves y, a continuación se enumeran algunos de estos:

-   Usar solo con la aplicación de primer plano, si se llama a cualquier operación de cadena de claves en un subproceso en segundo plano que se producirá un error en la llamada.
-   Agregar y actualizar elementos de la cadena de claves pueden requerir la autenticación.
-   Si una solicitud devuelve varios elementos coincidentes en la cadena de claves, la autenticación puede ser necesaria.
-   Elementos protegidos de ACL son solo de dispositivos y por lo tanto no sincronizada o en la copia de seguridad.

### <a name="using-local-authentication-without-keychain-services"></a>Utiliza la autenticación Local sin servicios de la cadena de claves

Autenticación local se creó como una manera para recopilar las credenciales, como el código de acceso o Touch ID y para trabajar con el Enclave seguros a fin de autenticar al usuario. Considerar como un puente entre la aplicación y al Enclave seguros, que pueden comunicarse nunca directamente entre sí. También se puede utilizar para la evaluación de directiva para la aplicación.

Para hacer esto en una aplicación llama a la evaluación de directivas dentro de la autenticación Local, que inicia la operación de Enclave seguros. Puede aprovechar para proporcionar autenticación a la aplicación, sin consultar y obtener acceso directamente al Enclave seguros.

[![](touchid-images/image13a.png "Utiliza la autenticación Local sin servicios de la cadena de claves")](touchid-images/image13a.png#lightbox)

Utilizar autenticación Local en la aplicación proporciona una manera sencilla de implementar la comprobación de usuario, por ejemplo, para desbloquear una característica únicamente para los ojos del propietario del dispositivo, como las aplicaciones bancarias, o a los controles parentales de servir de ayuda para la persona aplicación. También se puede utilizar como una manera de ampliar la autenticación que ya existe: los usuarios que su información para que sea segura, pero también les gusta tener opciones.

La seguridad de autenticación local es distinto de la cadena de claves. Por ejemplo, cuando se utiliza la cadena de claves, es la relación de confianza entre el sistema operativo y al Enclave seguros. Con la autenticación local, está entre la aplicación y el sistema operativo, lo que significa que sólo se tiene acceso a los resultados de la Enclave seguros, no al Enclave de seguros propio.

En el asunto de seguridad, también es muy importante saber que hay **sin acceso** dedos registrados o imágenes de huellas digitales. Al Enclave seguro es el propietario de esta información y, de modo que ningún otro componente del sistema tiene acceso a él.

Para usar Touch ID sin llaves mediante el aprovechamiento de la API de autenticación Local, hay algunas funciones que podemos usar. Estos se detallan a continuación:

*   `CanEvaluatePolicy` : Simplemente se comprobará para ver si el dispositivo es capaz de Aceptar Touch Id.
*   `EvaluatePolicy` : Se inicia la operación de autenticación y se muestra la interfaz de usuario y se devuelve un `true` o `false` respuesta.
*   `DeviceOwnerAuthenticationWithBiometrics` – Ésta es la directiva que puede usarse para mostrar la pantalla Touch ID. Merece la pena indicar que no hay ningún mecanismo de reserva contraseña aquí, en su lugar, debe implementar esta reserva en la aplicación para permitir a los usuarios omitir la autenticación Touch ID.

Existen algunas advertencias con mediante la autenticación Local, que se enumeran a continuación:

*   Al igual que con la cadena de claves, puede realizarse solo en primer plano. Lo llaman en un subproceso en segundo plano, provocará que se producirá un error.
*   Tenga en cuenta que puede producir un error en la evaluación de directivas. Será necesario que un botón de código de acceso se implementa como un paso atrás.
*   Debe proporcionar un `localizedReason` para explicar por qué se necesita la autenticación. Esto ayuda a generar relaciones de confianza con el usuario.

A continuación, en la siguiente sección, se consultará cómo implementar la interfaz API teniendo estas advertencias en cuenta.

## <a name="adding-touch-id-to-your-application"></a>Agregar Touch ID a la aplicación

En las secciones anteriores, se busca en la teoría de acceso y autenticación mediante una cadena de claves y la autenticación Local. Ahora tomaremos un vistazo a cómo se puede integrar Touch ID en su aplicación.

### <a name="walkthrough"></a>Tutorial

Veamos agrega algunos Id. toque la autenticación a nuestra aplicación. En este tutorial vamos a usar la [guión gráfico tabla](https://developer.xamarin.com/samples/StoryboardTable/) ejemplo. Dudes en asegurarse de que solo el propietario del dispositivo puede agregar algo a esta lista, no queremos acumulen en cualquier usuario que permite agregar un elemento.

1.  Descargar el ejemplo y ejecutar en Visual Studio para Mac.
2.  Haga doble clic en `MainStoryboard.Storyboard` para abrir el ejemplo en el Diseñador de iOS. Con este ejemplo, debe agregar una nueva pantalla a nuestra aplicación, que se va a controlar la autenticación. Esto irá antes de la actual `MasterViewController`.
3.  Arrastre una nueva **View Controller** desde el **cuadro de herramientas** a la **superficie de diseño**. Establézcalo como el **raíz View-Controller** por **Ctrl + arrastrar** desde el **navegación controlador**:

    [![](touchid-images/image4.png "Establezca el controlador de la vista raíz")](touchid-images/image4.png#lightbox)
4.  Nombre del nuevo controlador de vista `AuthenticationViewController`.
5.  A continuación, arrastre un botón y lo coloca en el `AuthenticationViewController`. Llame a este método `AuthenticateButton`y asígnele el texto `Add a Chore`.
6.  Crear un evento en el `AuthenticateButton` denominado `AuthenticateMe`.
7.  Crear un manual desplazará tranquilamente de `AuthenticationViewController` haciendo clic en la barra de color negra en la parte inferior y **Ctrl + arrastrar** desde la barra a la `MasterViewController` y elija **inserción** (o **mostrar** Si utiliza las clases de tamaño):

    [![](touchid-images/image5.png "Arrastre desde la barra a la MasterViewController y elegir la inserción o mostrar")](touchid-images/image6.png#lightbox)
8.  Haga clic en desplazará tranquilamente recién creado y asígnele el identificador `AuthenticationSegue`, tal y como se muestra a continuación:

    [![](touchid-images/image7.png "Establecer el identificador de segue en AuthenticationSegue")](touchid-images/image7.png#lightbox)
9.  Agrega el código siguiente a `AuthenticationViewController`:

    ```
    partial void AuthenticateMe (UIButton sender)
        {
            var context = new LAContext();
            NSError AuthError;
            var myReason = new NSString("To add a new chore");


            if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
                var replyHandler = new LAContextReplyHandler((success, error) => {

                    this.InvokeOnMainThread(()=>{
                        if(success){
                            Console.WriteLine("You logged in!");
                            PerformSegue("AuthenticationSegue", this);
                        }
                        else{
                            //Show fallback mechanism here
                        }
                    });

                });
                context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
            };
        }
    ```

Esto es todo el código que necesita para implementar la autenticación de Touch ID mediante la autenticación Local. Las líneas resaltadas en la imagen siguiente muestran el uso de autenticación Local:

[![](touchid-images/image8.png "Las líneas resaltadas muestran el uso de autenticación Local")](touchid-images/image8.png#lightbox)

En primer lugar, es necesario establecer si el dispositivo es capaz de Aceptar Touch ID de entrada, mediante el uso de la `CanEvaluatePolicy` y pasar la directiva `DeviceOwnerAuthenticationWithBiometrics`. Si esto es cierto, a continuación, podemos mostrar la interfaz de Touch Id. de usuario mediante el uso de `EvaluatePolicy`. Hay tres partes de información que se tiene que pasar a `EvaluatePolicy` : la directiva propiamente dicha, una cadena que explica por qué es necesaria la autenticación y un controlador de respuesta. El controlador de respuesta indica a la aplicación en el caso de una autenticación correcta o incorrecta, lo que debe hacer. Echemos un vistazo más de cerca en el controlador de respuesta:

[![](touchid-images/image9.png "El controlador de respuesta")](touchid-images/image9.png#lightbox)

El controlador de respuesta se especifica de tipo `LAContextReplyHandler`, que toma el éxito de parámetros: una `bool` valor y un `NSError` denominado `error`. Si se realiza correctamente, esto es donde realmente realizaremos lo que resulta que queremos autenticar: en este caso se muestra la pantalla que vamos a agregar una tarea ardua nueva. Recuerde que una de las advertencias de autenticación Local es la que debe ejecutar en primer plano, de modo que asegúrese de que usa `InvokeOnMainThread`:

[![](touchid-images/image10.png "Usar InvokeOnMainThread para la autenticación Local")](touchid-images/image10.png#lightbox)

Por último, cuando la autenticación se realizó correctamente, queremos que pase a la `MasterViewController`. El `PerformSegue` método se puede utilizar para hacer esto:

[![](touchid-images/image11.png "Llame al método PerformSegue para realizar la transición a la MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Resumen
En esta guía analizamos llaveros y cómo funciona en iOS. También hemos examinado las llaves ACL y cambia a este en iOS. A continuación, nos adentramos en el marco de trabajo de autenticación Local, que es nueva en iOS 8 y, a continuación, busca en la implementación de la autenticación Touch ID en nuestra aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Id. de entrada táctil](https://developer.xamarin.com/samples/StoryboardTable_LocalAuthentication)
- [Ejemplo de cadena de claves WWDC](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Cadena de claves (ejemplo)](https://developer.xamarin.com/samples/Keychain/)
