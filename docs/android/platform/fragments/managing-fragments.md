---
title: Gestione dei frammenti
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027328"
---
# <a name="managing-fragments"></a>Gestione dei frammenti

Per facilitare la gestione dei `FragmentManager` frammenti, Android fornisce la classe. Ogni attività dispone `Android.App.FragmentManager` di un'istanza di che troverà o modificherà dinamicamente i frammenti. Ogni set di queste modifiche è noto come *transazione*e viene eseguito `Android.App.FragmentTransation`utilizzando una delle `FragmentManager`API contenute nella classe , gestita da . Un'attività può avviare una transazione come questa:An Activity may start a transaction like this:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Queste modifiche ai frammenti `FragmentTransaction` vengono eseguite nell'istanza `Remove(),` `Replace().` utilizzando metodi quali `Add()`, `Commit()`e le modifiche vengono quindi applicate utilizzando . Le modifiche in una transazione non vengono eseguite immediatamente.
Al contrario, sono pianificati per l'esecuzione nel thread dell'interfaccia utente dell'attività appena possibile.

L'esempio seguente mostra come aggiungere un frammento a un contenitore esistente:The following example shows how to add a Fragment to an existing container:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Se viene eseguito `Activity.OnSaveInstanceState()` il commit di una transazione dopo la chiamata, verrà generata un'eccezione. Questo accade perché quando l'attività salva il proprio stato, Android salva anche lo stato di tutti i frammenti ospitati. Se dopo questo punto viene eseguito il commit di qualsiasi transazione di frammento, lo stato di queste transazioni andrà perso quando l'attività viene ripristinata.

È possibile salvare le transazioni Fragment nello [stack Indietro](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) dell'attività `FragmentTransaction.AddToBackStack()`effettuando una chiamata a . Ciò consente all'utente di spostarsi all'indietro tra le modifiche di frammento quando viene premuto il pulsante **Indietro.** Senza una chiamata a questo metodo, i frammenti rimossi verranno eliminati e non saranno disponibili se l'utente torna indietro nell'attività.

Nell'esempio seguente viene `AddToBackStack` illustrato come `FragmentTransaction` utilizzare il metodo di a per sostituire un frammento, mantenendo lo stato del primo frammento nello stack indietro:

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

## <a name="communicating-with-fragments"></a>Comunicazione con i frammenti

Il FragmentManager conosce tutti i frammenti che sono associati a un'attività e fornisce due metodi per trovare questi frammenti:The *FragmentManager* knows about all of the Fragments that are attached to an Activity and provides two methods to help find these Fragments:

- **FindFragmentById** &ndash; Questo metodo troverà un frammento utilizzando l'ID specificato nel file di layout o l'ID contenitore quando il frammento è stato aggiunto come parte di una transazione.

- **FindFragmentByTag** &ndash; Questo metodo viene utilizzato per trovare un frammento che dispone di un tag che è stato fornito nel file di layout o che è stato aggiunto in una transazione.

Sia i frammenti `FragmentManager`che le attività fanno riferimento a , pertanto le stesse tecniche vengono utilizzate per comunicare avanti e indietro tra di essi. Un'applicazione può trovare un frammento di riferimento utilizzando uno di questi due metodi, eseguire il cast di tale riferimento al tipo appropriato e quindi chiamare direttamente i metodi sul frammento. Il frammento di codice seguente fornisce un esempio:The following snippet provides an example:

È anche possibile che l'attività utilizzi l'oggetto per trovare frammenti:It is also possible for the Activity to use the `FragmentManager` to find Fragments:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Comunicare con l'attività

È possibile che un frammento utilizzi la `Fragment.Activity` proprietà per fare riferimento al relativo host. Eseguendo il cast di Activity a un tipo più specifico, è possibile che un'attività chiami metodi e proprietà nel relativo host, come illustrato nell'esempio seguente:By casting the Activity to a more specific type, it is possible for an Activity to call methods and properties on its host, as shown in the following example:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
