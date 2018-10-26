---
title: Administración de fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 107877d0e92d3a46101812b78bc0b414c0fbb320
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105472"
---
# <a name="managing-fragments"></a>Administración de fragmentos

Para ayudarle a administrar los fragmentos, Android proporciona el `FragmentManager` clase. Cada actividad tiene una instancia de `Android.App.FragmentManager` que va a buscar o cambiar dinámicamente sus fragmentos. Cada conjunto de estos cambios se conoce como un *transacciones*y se realiza mediante una de las API contenidas en la clase `Android.App.FragmentTransation`, que es administrado por el `FragmentManager`. Una actividad puede iniciar una transacción similar al siguiente:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Estos cambios a los fragmentos se realizan en el `FragmentTransaction` instancia mediante métodos como `Add()`, `Remove(),` y `Replace().` , a continuación, se aplican los cambios mediante el uso de `Commit()`. No se realizan inmediatamente los cambios en una transacción.
En su lugar, que están programados para ejecutarse en el subproceso de interfaz de usuario de la actividad tan pronto como sea posible.

El ejemplo siguiente muestra cómo agregar un fragmento a un contenedor existente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Si una transacción se confirma después `Activity.OnSaveInstanceState()` es llamado, se producirá una excepción. Esto sucede porque cuando la actividad guarda su estado, Android también guarda el estado de cualquier fragmento hospedada. Si se confirman las transacciones de fragmento después de este punto, el estado de estas transacciones se perderán cuando se restaura la actividad.

Es posible guardar las transacciones de fragmento en la actividad [pila de retroceso](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) mediante una llamada a `FragmentTransaction.AddToBackStack()`. Esto permite al usuario navegar hacia atrás a través fragmento cambia cuando el **volver** está presionado. Sin una llamada a este método, los fragmentos que se quitan se destruirá y no estará disponibles si el usuario se desplaza a través de la actividad.

El ejemplo siguiente muestra cómo usar el `AddToBackStack` método de un `FragmentTransaction` para reemplazar un fragmento, conservando el estado del primer fragmento en la pila de retroceso:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```


## <a name="communicating-with-fragments"></a>Comunicación con fragmentos

El *FragmentManager* sabe acerca de todos los fragmentos que están conectados a una actividad y proporciona dos métodos para ayudar a encontrar estos fragmentos:

-   **FindFragmentById** &ndash; este método encontrará un fragmento con el identificador que se especificó en el archivo de diseño o el identificador del contenedor cuando se agregó el fragmento como parte de una transacción.

-   **FindFragmentByTag** &ndash; este método se utiliza para buscar un fragmento que tiene una etiqueta que se proporcionó en el archivo de diseño o que se ha agregado en una transacción.

Referencia de fragmentos y actividades el `FragmentManager`, por lo que se usan las mismas técnicas para comunicarse y hacia atrás entre ellos. Una aplicación puede encontrar un fragmento de referencia mediante uno de estos dos métodos, convertir esa referencia al tipo adecuado y, a continuación, llamar directamente los métodos en el fragmento. El siguiente fragmento de código proporciona un ejemplo:

También es posible que la actividad que se usará el `FragmentManager` para buscar fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Comunicación con la actividad

Es posible que un fragmento que se va a usar el `Fragment.Activity` propiedad para hacer referencia a su host. Al convertir la actividad a un tipo más específico, es posible que una actividad para llamar a métodos y propiedades en su host, tal como se muestra en el ejemplo siguiente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
