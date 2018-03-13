---
title: La gestione di frammenti
ms.topic: article
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: aa7c6eb2435be473049f0799a70a6eb37e678c78
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="managing-fragments"></a>La gestione di frammenti

Per semplificare la gestione di frammenti, Android offre la `FragmentManager` classe. Ogni attività dispone di un'istanza di `Android.App.FragmentManager` che trovare o modificare in modo dinamico i frammenti. Ogni set di queste modifiche è noto come un *transazione*e viene eseguita utilizzando una delle API contenute nella classe di `Android.App.FragmentTransation`, che è gestito dal `FragmentManager`. Un'attività può avviare una transazione simile al seguente:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Vengono eseguite su queste modifiche ai frammenti di `FragmentTransaction` istanza tramite metodi, ad esempio `Add()`, `Remove(),` e `Replace().` le modifiche vengono quindi applicate utilizzando `Commit()`. Le modifiche in una transazione non vengono eseguite immediatamente.
Al contrario, vengono pianificate per l'esecuzione sul thread dell'interfaccia utente dell'attività appena possibile.

Nell'esempio seguente viene illustrato come aggiungere un frammento di un contenitore esistente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Se una transazione viene eseguito il commit dopo `Activity.OnSaveInstanceState()` viene chiamato, verrà generata un'eccezione. Ciò accade perché quando l'attività Salva lo stato, Android salvato anche lo stato di qualsiasi frammenti ospitati. Se tutte le transazioni frammento vengono eseguito il commit dopo questo punto, lo stato di queste transazioni andranno persi quando l'attività viene ripristinato.

È possibile salvare le transazioni di frammento dell'attività [stack Indietro](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) effettuando una chiamata a `FragmentTransaction.AddToBackStack()`. In questo modo all'utente di spostarsi all'indietro tramite frammento cambia quando il **nuovamente** pressione del pulsante. Senza una chiamata a questo metodo, frammenti che sono state rimosse verranno eliminato definitivamente e non saranno disponibili se l'utente passa attraverso l'attività.

Nell'esempio seguente viene illustrato come utilizzare il `AddToBackStack` metodo di un `FragmentTransaction` per sostituire un frammento, mantenendo lo stato del primo frammento nello stack indietro:

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


## <a name="communicating-with-fragments"></a>La comunicazione con i frammenti

Il *FragmentManager* a conoscenza di tutti i frammenti associati a un'attività e fornisce due metodi per individuare questi frammenti:

-   **FindFragmentById** &ndash; questo metodo troverà un frammento utilizzando l'ID specificato nel file di layout o l'ID di contenitore quando è stato aggiunto il frammento come parte di una transazione.

-   **FindFragmentByTag** &ndash; questo metodo viene utilizzato per trovare un frammento che contiene un tag che è stato specificato nel file di layout o che è stato aggiunto in una transazione.

Riferimento frammenti sia le attività di `FragmentManager`, pertanto le stesse tecniche utilizzate per comunicare avanti e indietro tra di essi. Un'applicazione può trovare un frammento di riferimento utilizzando uno di questi due metodi, cast che fanno riferimento al tipo appropriato e quindi chiamare direttamente i metodi per il frammento. Il frammento di codice seguente viene fornito un esempio:

È inoltre possibile utilizzare l'attività di `FragmentManager` trovare frammenti:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>La comunicazione con l'attività

È possibile che un frammento di utilizzare il `Fragment.Activity` proprietà che fa riferimento il relativo host. Eseguendo il cast dell'attività a un tipo più specifico, è possibile che un'attività per chiamare metodi e proprietà sul relativo host, come illustrato nell'esempio seguente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
