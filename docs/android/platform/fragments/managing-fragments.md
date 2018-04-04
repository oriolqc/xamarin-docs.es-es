---
title: Administración de fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: b39067b0cb1bbb344866761042db0125fd4a4dc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="managing-fragments"></a>Administración de fragmentos

Para ayudar con la administración de fragmentos, Android proporciona la `FragmentManager` clase. Cada actividad tiene una instancia de `Android.App.FragmentManager` que va a buscar o cambiar dinámicamente sus fragmentos. Cada conjunto de estos cambios se conoce como un *transacciones*y se realiza mediante una de las API contenidas en la clase `Android.App.FragmentTransation`, que es administrado por el `FragmentManager`. Una actividad puede iniciar una transacción similar al siguiente:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Estos cambios a los fragmentos se realizan en el `FragmentTransaction` instancia mediante métodos como `Add()`, `Remove(),` y `Replace().` , a continuación, se aplican los cambios mediante el uso de `Commit()`. No se realizan inmediatamente los cambios en una transacción.
En su lugar, que están programados para ejecutarse en el subproceso de interfaz de usuario de la actividad tan pronto como sea posible.

En el ejemplo siguiente se muestra cómo agregar un fragmento a un contenedor existente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Si una transacción se confirma después `Activity.OnSaveInstanceState()` es llama, se producirá una excepción. Esto sucede porque cuando la actividad guarda su estado, Android también guarda el estado de cualquier fragmento hospedado. Si las transacciones de fragmento se han confirmado después de este punto, el estado de estas transacciones se perderá cuando se restaura la actividad.

Es posible guardar las transacciones de fragmento a la actividad [pila de retroceso](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) realizando una llamada a `FragmentTransaction.AddToBackStack()`. Esto permite al usuario navegar hacia atrás a través fragmento cambia cuando el **volver** se presiona el botón. Sin una llamada a este método, fragmentos que se quitan se destruirá y no estará disponibles si el usuario se desplaza a través de la actividad.

En el ejemplo siguiente se muestra cómo utilizar el `AddToBackStack` método de una `FragmentTransaction` para reemplazar un fragmento, al tiempo que conserva el estado del primer fragmento en la pila de retroceso:

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


## <a name="communicating-with-fragments"></a>Comunicarse con fragmentos

El *FragmentManager* conoce todos los fragmentos que están conectados a una actividad y proporciona dos métodos para ayudar a encontrar estos fragmentos:

-   **FindFragmentById** &ndash; este método encontrará un fragmento con el Id. que se especificó en el archivo de diseño o el identificador del contenedor cuando se agregó el fragmento como parte de una transacción.

-   **FindFragmentByTag** &ndash; este método se usa para buscar un fragmento que tiene una etiqueta que se proporcionó en el archivo de diseño o que se ha agregado en una transacción.

Referencia de fragmentos y actividades el `FragmentManager`, por lo que se usan las mismas técnicas para comunicarse y hacia atrás entre ellos. Una aplicación puede buscar una fragmento de referencia mediante uno de estos dos métodos, convierte que hacen referencia al tipo adecuado y directamente la llamada a métodos en el fragmento. El fragmento de código siguiente muestra un ejemplo:

También es posible que la actividad que se va a usar el `FragmentManager` para buscar fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Comunicarse con la actividad

Es posible que un fragmento usar el `Fragment.Activity` propiedad para hacer referencia a su host. Convirtiendo la actividad a un tipo más específico, es posible que una actividad para llamar a métodos y propiedades de su host, tal como se muestra en el ejemplo siguiente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
