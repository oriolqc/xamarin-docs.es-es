---
title: Contactos y ContactsUI
description: "Este artículo tratan trabajar con los contactos y la interfaz de usuario de contactos nuevos marcos en una aplicación de Xamarin.iOS. Estos marcos reemplazan la libreta de direcciones existente y la interfaz de usuario de libreta de direcciones utilizado en versiones anteriores de iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 996723db83a1f972cce26090d1253f97b6c818d3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="contacts-and-contactsui"></a>Contactos y ContactsUI

_Este artículo tratan trabajar con los contactos y la interfaz de usuario de contactos nuevos marcos en una aplicación de Xamarin.iOS. Estos marcos reemplazan la libreta de direcciones existente y la interfaz de usuario de libreta de direcciones utilizado en versiones anteriores de iOS._

Con la introducción de iOS 9, Apple ha lanzado dos marcos nueva, `Contacts` y `ContactsUI`, que reemplace la existente libreta de direcciones y marcos de interfaz de usuario de libreta de direcciones usan por iOS 8 y versiones anteriores.

Los dos marcos nuevo contienen la siguiente funcionalidad:

- [**Contactos** ](#contacts) -proporciona acceso a los datos de lista de contactos del usuario.
    Porque la mayoría de las aplicaciones solo requiere acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso a un subproceso seguro, de solo lectura.

- [**ContactsUI** ](#contactsui) -elementos de interfaz de usuario de Xamarin.iOS proporciona para mostrar, editar, seleccionar y crear contactos en dispositivos iOS.

[![](contacts-images/add01.png "Un ejemplo de la hoja de contactos en un dispositivo iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> **Nota:** existente `AddressBook` y `AddressBookUI` marcos usando iOS 8 (y versiones anteriores) han quedado obsoletas en iOS 9 y debe reemplazarse por el nuevo `Contacts` y `ContactsUI` marcos tan pronto como sea posible para cualquier Xamarin.iOS existente aplicación. Nuevas aplicaciones deben escribirse en los marcos de nuevo.




En las secciones siguientes, echaremos un vistazo en estos marcos de trabajo nueva y cómo implementarlos en una aplicación de Xamarin.iOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>El marco de trabajo de contactos

El marco de trabajo de contactos proporciona acceso de Xamarin.iOS a información de contacto del usuario. Porque la mayoría de las aplicaciones solo requiere acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso a un subproceso seguro, de solo lectura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Objetos de contacto

La `CNContact` clase proporciona acceso de subproceso seguro, de solo lectura a las propiedades de un contacto como nombre, dirección o números de teléfono. `CNContact` funciona como un `NSDictionary` y contiene varias colecciones de solo lectura de propiedades (por ejemplo, direcciones o números de teléfono):

[![](contacts-images/contactobjects.png "Póngase en contacto con información general sobre objetos")](contacts-images/contactobjects.png#lightbox)

Para cualquier propiedad que puede tener varios valores (como números de teléfono o dirección de correo electrónico), se representará como una matriz de `NSLabeledValue` objetos. `NSLabeledValue` es una tupla seguro para subprocesos que consta de un conjunto de solo lectura de etiquetas y valores en la etiqueta define el valor para el usuario (por ejemplo correo de electrónico de hogar o trabajo). El marco de trabajo de contactos proporciona una selección de las etiquetas predefinidas (a través de la `CNLabelKey` y `CNLabelPhoneNumberKey` las clases estáticas) que puede usar en la aplicación o tiene la opción de definir etiquetas personalizadas para sus necesidades.

Para cualquier aplicación de Xamarin.iOS que deba ajustar los valores de un contacto ya existente (o crear nuevos), use la `NSMutableContact` versión de la clase y sus clases de sub (como `CNMutablePostalAddress`).

Por ejemplo, el código siguiente se crea un nuevo contacto y agregarlo a la colección del usuario de contactos:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Si este código se ejecuta en un dispositivo iOS 9, se agregará un nuevo contacto a la colección del usuario. Por ejemplo:

[![](contacts-images/add01.png "Un nuevo contacto agregado a la colección del usuario")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Póngase en contacto con formato y la localización

El marco de trabajo de contactos contiene varios objetos y métodos que pueden ayudarle a dar formato y localización contenido para mostrar al usuario. Por ejemplo, el código siguiente daría formato correctamente a un nombre de contactos y la dirección de envío para mostrar:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Para las etiquetas de propiedad que va a mostrar en la interfaz de usuario de la aplicación, el marco de trabajo del contacto tiene métodos para la localización de esas cadenas también. Una vez más, esto se basa en la configuración regional actual de la aplicación se está ejecutando en el dispositivo de iOS. Por ejemplo:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOption.Nickname));
Console.WriteLine(CNLabeledValue.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Obtener contactos existentes

Mediante el uso de una instancia de la `CNContactStore` (clase), puede capturar información de contacto de base de datos de contactos del usuario. El `CNContactStore` contiene todos los métodos necesarios para capturar o actualizar los contactos y grupos de la base de datos. Dado que estos métodos son sincrónicos, se sugiere que se ejecute en un subproceso en segundo plano para evitar bloquear la interfaz de usuario.

Mediante el uso de predicados (compilado a partir de la `CNContact` clase), puede filtrar los resultados devueltos al capturar los contactos de la base de datos. Para obtener únicamente los contactos que contengan la cadena `Appleseed`, utilice el código siguiente:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> **Nota:** genérico y predicados compuestos no son compatibles con el marco de trabajo de contactos.

Por ejemplo, para limitar la búsqueda solamente para la **GivenName** y **FamilyName** propiedades del contacto, utilice el código siguiente:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Por último, para buscar la base de datos y devolver los resultados, utilice el código siguiente:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Si este código se ha ejecutado después de que el ejemplo que hemos creado en el **objeto Contacts** sección anterior, devolvería el contacto "Juan Appleseed" que se acaba de crear.

### <a name="contact-access-privacy"></a>Póngase en contacto con acceso privacidad

Dado que los usuarios finales pueden conceder o denegar el acceso a su información de contacto en según la aplicación, la primera vez que realiza una llamada a la `CNContactStore`, aparecerá un cuadro de diálogo que se les solicita que permita el acceso de la aplicación.

La solicitud de permiso sólo aparecerá una vez, se ejecuta la primera vez que la aplicación se ejecute y posteriores o las llamadas a la `CNContactStore` utilizará el permiso que el usuario seleccionado en ese momento.

Debe diseñar la aplicación para que controla correctamente el usuario deniega el acceso a su base de datos de contacto.

#### <a name="fetching-partial-contacts"></a>Obtener contactos parciales

A _póngase en contacto con parcial_ es un contacto que solo algunas de las propiedades disponibles se han capturado desde el almacén de contactos de. Si intenta tener acceso a una propiedad que no se ha capturado previamente, se producirá una excepción.

Puede comprobar fácilmente para ver si un contacto determinado tiene la propiedad deseada utilizando el `IsKeyAvailable` o `AreKeysAvailable` métodos de la `CNContact` instancia. Por ejemplo:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> **Nota:** el `GetUnifiedContact` y `GetUnifiedContacts` métodos de la `CNContactStore` clase _sólo_ devolver un contacto parcial limitado a las propiedades solicitadas de las claves de recuperación proporcionadas.

### <a name="unified-contacts"></a>Contactos unificadas

Un usuario podría tener diferentes orígenes de información de contacto para una sola persona en su base de datos de contacto (por ejemplo, iCloud, Facebook o Google correo). En iOS y OS X aplicaciones, esta información de contacto se automáticamente vinculada entre sí y se mostrará al usuario como una sola _unificado póngase en contacto con_:

[![](contacts-images/unified01.png "Información general de contactos unificada")](contacts-images/unified01.png#lightbox)

Este unificado póngase en contacto con es una vista temporal en memoria de la información de contacto de vínculo que se asignará a su propio identificador único (que se debe usar para volver a obtener el contacto si es necesario). De forma predeterminada, el marco de trabajo de contactos devolverá un contacto unificado siempre que sea posible.

### <a name="creating-and-updating-contacts"></a>Crear y actualizar los contactos

Como vimos en el [póngase en contacto con objetos](#Contact_Objects) sección anterior, use un `CNContactStore` y una instancia de un `CNMutableContact` para crear nuevos contactos que, a continuación, se escriben en el usuario póngase en contacto con base de datos mediante un `CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

A `CNSaveRequest` también puede utilizarse para almacenar en caché varios cambios de contacto y grupo en una sola operación y procesar por lotes las modificaciones a la `CNContactStore`.

Para actualizar un contacto no mutable obtenido de una operación de búsqueda, primero debe solicitar una copia mutable que, a continuación, modificar y guardar en el almacén de contactos. Por ejemplo:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Notificaciones de cambio de contacto

Cuando se modifica un contacto, registra el almacén póngase en contacto con un `CNContactStoreDidChangeNotification` para el centro de notificaciones de forma predeterminada. Si se hayan almacenado en caché o actualmente se muestran todos los contactos, debe actualizar los objetos de la tienda de contacto (`CNContactStore`).

### <a name="containers-and-groups"></a>Contenedores y grupos

Contactos de un usuario pueden existir ya sea localmente en el dispositivo del usuario o como contactos sincronizados en el dispositivo de una o varias cuentas de servidor (por ejemplo, Facebook o Google). Cada grupo de contactos tiene su propio _contenedor_ y solo puede existir un contacto determinado en un contenedor.

[![](contacts-images/containers01.png "Introducción a los contenedores y grupos")](contacts-images/containers01.png#lightbox)

Permitir que algunos contenedores de contactos para que se organizan en uno o varios _grupos_ o _subgrupos_. Este comportamiento depende de la memoria auxiliar en un contenedor determinado. Por ejemplo, iCloud tiene solo un contenedor, pero puede tener muchos grupos (pero no grupos). Por otro lado, Microsoft Exchange no admite grupos pero puede tener varios contenedores (uno para cada carpeta de Exchange).

[![](contacts-images/containers02.png "Se superponga dentro de contenedores y grupos")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>El marco de trabajo ContactsUI

En situaciones donde la aplicación no necesita presentar una interfaz de usuario personalizada, puede utilizar el marco de trabajo de ContactsUI para presentar elementos de interfaz de usuario para mostrar, editar, seleccionar y crear contactos en la aplicación Xamarin.iOS.

Usando los controles integrados de Apple no solo reduce la cantidad de código que se debe crear para contactos de soporte técnico de la aplicación Xamarin.iOS y presentar una interfaz coherente a los usuarios de la aplicación.

### <a name="the-contact-picker-view-controller"></a>El controlador de vista de selector de contacto

El controlador de vista de selector de contacto (`CNContactPickerViewController`) administra la vista de selector de contacto estándar que permite al usuario seleccionar un contacto o una propiedad de contacto de base de datos de contacto del usuario. El usuario puede seleccionar uno o más contactos (según su uso) y póngase en contacto con selector View Controller no solicita permiso antes de mostrar el selector.

Antes de llamar a la `CNContactPickerViewController` (clase), defina las propiedades que el usuario puede seleccionar y definir predicados para controlar la presentación y selección de propiedades del contacto.

Usar una instancia de la clase que hereda de `CNContactPickerDelegate` para responder a la interacción del usuario con el selector. Por ejemplo:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Para permitir al usuario seleccionar una dirección de correo electrónico de los contactos de la base de datos, podría utilizar el siguiente código:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>El controlador de vista de contacto

El controlador de vista póngase en contacto con el (`CNContactViewController`) clase proporciona un controlador para presentar una vista estándar de contacto para el usuario final. La vista de contacto puede mostrar nuevos contactos de nuevo, desconocido o existente y se debe especificar el tipo antes de que se muestra la vista mediante una llamada al constructor estático correcto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Por ejemplo:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con los marcos de contacto y póngase en contacto con interfaz de usuario en una aplicación de Xamarin.iOS. En primer lugar, tratan los distintos tipos de objetos que proporciona el marco de trabajo del contacto y cómo usarlos para crear nuevos u obtener acceso a los contactos existentes. También examina el marco de ponerse en contacto con la interfaz de usuario para seleccionar los contactos existentes y mostrar información de contacto.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [What's New en iOS 9](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html#//apple_ref/doc/uid/TP40016198-DontLinkElementID_14))
- [Referencia de marco de contactos](https://developer.apple.com/library/prerelease/ios/documentation/Contacts/Reference/Contacts_Framework/index.html#//apple_ref/doc/uid/TP40015328)
- [Referencia de ContactsUI Framework](https://developer.apple.com/library/prerelease/ios/documentation/ContactsUI/Reference/ContactsUI_Framework/index.html#//apple_ref/doc/uid/TP40016207)
