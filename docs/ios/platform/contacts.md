---
title: Contacts y ContactsUI en Xamarin.iOS
description: Este artículo trata de trabajar con los contactos y la interfaz de usuario de contactos nuevos marcos de trabajo en una aplicación de Xamarin.iOS. Estos marcos reemplazan la libreta de direcciones existente y la interfaz de usuario de libreta de direcciones utilizada en versiones anteriores de iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: e3f1533605d08df58d8d257714dd8135690c0e5d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105459"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contacts y ContactsUI en Xamarin.iOS

_Este artículo trata de trabajar con los contactos y la interfaz de usuario de contactos nuevos marcos de trabajo en una aplicación de Xamarin.iOS. Estos marcos reemplazan la libreta de direcciones existente y la interfaz de usuario de libreta de direcciones utilizada en versiones anteriores de iOS._

Con la introducción de iOS 9, Apple ha lanzado dos nuevos marcos de trabajo, `Contacts` y `ContactsUI`, que reemplace la existente libreta de direcciones y marcos de interfaz de usuario de libreta de direcciones usan por iOS 8 y versiones anteriores.

Los dos marcos nuevos incorporan la funcionalidad siguiente:

- [**Contactos** ](#contacts) -proporciona acceso a datos de lista de contactos del usuario.
    Dado que la mayoría de las aplicaciones solo requieren acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso seguro y de solo lectura de subproceso.

- [**ContactsUI** ](#contactsui) -elementos de interfaz de usuario de Xamarin.iOS proporciona para mostrar, editar, seleccione y crear contactos en dispositivos iOS.

[![](contacts-images/add01.png "Un ejemplo de hoja de contactos en un dispositivo iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Existente `AddressBook` y `AddressBookUI` marcos usando iOS 8 (y anterior) han quedado obsoletas en iOS 9 y debe reemplazarse por el nuevo `Contacts` y `ContactsUI` marcos tan pronto como sea posible para cualquier aplicación de Xamarin.iOS existente. Nuevas aplicaciones deben escribirse contra los nuevos marcos de trabajo.




En las secciones siguientes, echaremos un vistazo a estos nuevos marcos de trabajo y cómo implementarlos en una aplicación de Xamarin.iOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>El marco de trabajo de contactos

El marco de contactos proporciona Xamarin.iOS acceso a información de contacto del usuario. Dado que la mayoría de las aplicaciones solo requieren acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso seguro y de solo lectura de subproceso.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Objetos de contacto

La `CNContact` clase proporciona acceso de subproceso seguro, de solo lectura a las propiedades de un contacto como nombre, dirección o números de teléfono. `CNContact` funciona como un `NSDictionary` y contiene varias colecciones de solo lectura de propiedades (por ejemplo, direcciones o números de teléfono):

[![](contacts-images/contactobjects.png "Póngase en contacto con información general sobre objetos")](contacts-images/contactobjects.png#lightbox)

Para cualquier propiedad que puede tener varios valores (como números de teléfono o dirección de correo electrónico), se representará como una matriz de `NSLabeledValue` objetos. `NSLabeledValue` es una tupla de subproceso seguro que consta de un conjunto de solo lectura de etiquetas y valores en la etiqueta define el valor para el usuario (por ejemplo correo de electrónico de hogar o trabajo). El marco de contactos proporciona una selección de etiquetas predefinidas (a través de la `CNLabelKey` y `CNLabelPhoneNumberKey` clases estáticas) que puede usar en la aplicación o tiene la opción de definir etiquetas personalizadas para sus necesidades.

Para cualquier aplicación de Xamarin.iOS que necesite ajustar los valores de un contacto ya existente (o crear nuevos), use el `NSMutableContact` versión de la clase y sus clases sub (como `CNMutablePostalAddress`).

Por ejemplo, el código siguiente se crea un contacto nuevo y agregarlo a la colección del usuario de contactos:

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

El marco de trabajo de contactos contiene varios objetos y métodos que pueden ayudarle a dar formato y traducir el contenido para mostrar al usuario. Por ejemplo, el código siguiente correctamente podría dar formato a un nombre de contactos y la dirección de envío para mostrar:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Para las etiquetas de propiedad que se va a mostrar en interfaz de usuario de la aplicación, el marco de trabajo del contacto tiene métodos para la localización de esas cadenas también. Nuevamente, esto se basa en la configuración regional actual de la aplicación se ejecuta en el dispositivo de iOS. Por ejemplo:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Capturando contactos existentes

Mediante el uso de una instancia de la `CNContactStore` (clase), puede capturar información de contacto de la base de datos de contactos del usuario. El `CNContactStore` contiene todos los métodos necesarios para capturar o actualizar los contactos y grupos de la base de datos. Dado que estos métodos son sincrónicos, se sugiere que se ejecute en un subproceso en segundo plano para evitar el bloqueo de la interfaz de usuario.

Mediante el uso de predicados (generado a partir del `CNContact` clase), puede filtrar los resultados devueltos al recuperar los contactos de la base de datos. Para obtener únicamente los contactos que contengan la cadena `Appleseed`, utilice el código siguiente:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Predicados genéricos y compuestos no admiten el marco de trabajo de contactos.

Por ejemplo, para limitar la búsqueda solo a la **GivenName** y **FamilyName** propiedades del contacto, use el código siguiente:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Por último, para buscar la base de datos y devolver los resultados, use el código siguiente:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Si se ha ejecutado este código del ejemplo que hemos creado en el **objeto Contacts** sección anterior, devolvería el contacto "John Appleseed" que acabamos de crear.

### <a name="contact-access-privacy"></a>Póngase en contacto con acceso privacidad

Dado que los usuarios finales puede conceder o denegar el acceso a su información de contacto por la aplicación, la primera vez realice una llamada a la `CNContactStore`, aparecerá un cuadro de diálogo le pide que permitir el acceso a la aplicación.

La solicitud de permiso sólo se mostrará una vez, la primera vez que la aplicación se ejecute y posteriores, se ejecuta o las llamadas a la `CNContactStore` utilizará el permiso que el usuario seleccionado en ese momento.

Debe diseñar la aplicación para que controla correctamente el usuario deniega el acceso a su base de datos de contacto.

#### <a name="fetching-partial-contacts"></a>Capturando contactos parciales

Un _póngase en contacto con parcial_ es un contacto que se han capturado solo algunas de las propiedades disponibles desde el almacén de contactos de. Si se intenta tener acceso a una propiedad que no se ha capturado previamente, se producirá una excepción.

Puede comprobar fácilmente si un contacto especificado tiene la propiedad deseada mediante el uso del `IsKeyAvailable` o `AreKeysAvailable` métodos de la `CNContact` instancia. Por ejemplo:

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
> El `GetUnifiedContact` y `GetUnifiedContacts` métodos de la `CNContactStore` clase _sólo_ devolver un contacto parcial limitado a las propiedades solicitadas de las claves de recuperación proporcionadas.

### <a name="unified-contacts"></a>Contactos unificados

Un usuario podría tener diferentes orígenes de información de contacto de una sola persona en su base de datos de contacto (por ejemplo, iCloud, Facebook o Google correo). En iOS y OS X aplicaciones, esta información de contacto se automáticamente vinculada entre sí y se mostrará al usuario como una sola, _unificada, póngase en contacto con_:

[![](contacts-images/unified01.png "Información general de contactos unificada")](contacts-images/unified01.png#lightbox)

Contacto unificada es una vista temporal en memoria de la información de contacto de vínculo que se asignará a su propio identificador único (que se debe usar para volver a obtener el contacto si es necesario). De forma predeterminada, el marco de trabajo de contactos devolverá un contacto unificada, siempre que sea posible.

### <a name="creating-and-updating-contacts"></a>Crear y actualizar contactos

Como hemos visto en el [póngase en contacto con objetos](#Contact_Objects) la sección anterior, usa un `CNContactStore` y una instancia de un `CNMutableContact` para crear nuevos contactos que, a continuación, se escriben en el usuario póngase en contacto con base de datos mediante un `CNSaveRequest`:

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

Un `CNSaveRequest` también puede utilizarse para almacenar en caché varios cambios de contacto y de grupo en una sola operación y por lotes las modificaciones a la `CNContactStore`.

Para actualizar un contacto no mutable obtenido de una operación de captura, se debe solicitar una copia mutable que, a continuación, modificar y guardar en el almacén de contactos. Por ejemplo:

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

Si se modifica un contacto, publica el Store póngase en contacto con un `CNContactStoreDidChangeNotification` para el centro de notificaciones de forma predeterminada. Si se ha almacenado en caché o actualmente se muestran todos los contactos, deberá actualizar esos objetos desde el Store contacto (`CNContactStore`).

### <a name="containers-and-groups"></a>Contenedores y grupos

Contactos de un usuario pueden existir ya sea localmente en el dispositivo del usuario o como contactos sincronizados en el dispositivo desde una o varias cuentas de servidor (como Facebook o Google). Cada grupo de contactos tiene su propio _contenedor_ y solo puede existir un contacto determinado en un contenedor.

[![](contacts-images/containers01.png "Introducción a los contenedores y grupos")](contacts-images/containers01.png#lightbox)

Permitir que algunos contenedores de contactos para que se organizan en uno o varios _grupos_ o _subgrupos_. Este comportamiento es dependiente de la memoria auxiliar para un contenedor determinado. Por ejemplo, iCloud tiene solo un contenedor, pero puede tener muchos grupos (pero no los subgrupos). Por otro lado, Microsoft Exchange no es compatible con grupos pero puede tener varios contenedores (una para cada carpeta de Exchange).

[![](contacts-images/containers02.png "Solaparse dentro de contenedores y grupos")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>El marco de trabajo de ContactsUI

Para las situaciones donde la aplicación no necesita presentar una interfaz de usuario personalizada, puede usar el marco de trabajo de ContactsUI para presentar elementos de interfaz de usuario para mostrar, editar, seleccione y crear contactos en la aplicación de Xamarin.iOS.

Mediante el uso de los controles integrados de Apple no solo se reduce la cantidad de código que se debe crear para admitir los contactos en la aplicación de Xamarin.iOS, pero presentar una interfaz coherente a los usuarios de la aplicación.

### <a name="the-contact-picker-view-controller"></a>El controlador de vista de selector de contactos

El controlador de vista del selector de contacto (`CNContactPickerViewController`) administra la vista de selector de contacto estándar que permite al usuario seleccionar un contacto o una propiedad de contacto de la base de datos de contacto del usuario. El usuario puede seleccionar uno o más contactos (según su uso) y el controlador de vista de selector de ponerse en contacto con no solicita permiso antes de mostrar el selector.

Antes de llamar a la `CNContactPickerViewController` clase, define las propiedades que el usuario puede seleccionar y definir predicados para controlar la presentación y selección de propiedades del contacto.

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

Para permitir al usuario seleccionar una dirección de correo electrónico de los contactos de su base de datos, podría utilizar el código siguiente:

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

El controlador de vista de contacto (`CNContactViewController`) clase proporciona un controlador para presentar una vista estándar de contacto para el usuario final. La vista de contacto puede mostrar contactos nuevos de nuevo, desconocido o existente y se debe especificar el tipo antes de que se muestra la vista mediante una llamada al constructor estático correcto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Por ejemplo:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con los marcos de contacto y póngase en contacto con la interfaz de usuario en una aplicación de Xamarin.iOS. En primer lugar, se ha descrito los distintos tipos de objetos que proporciona el marco de contacto y cómo usarlos para crear o tener acceso a los contactos existentes. También examina el marco de ponerse en contacto con la interfaz de usuario para seleccionar los contactos existentes y mostrar información de contacto.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [Novedades de iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referencia de Framework de contactos](https://developer.apple.com/documentation/contacts?language=objc)
- [Referencia de Framework ContactsUI](https://developer.apple.com/documentation/contactsui?language=objc)
