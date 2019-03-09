---
title: Gestione dei frammenti
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 3e0430b8ed9c42030441021e71c3b08b1ddccc57
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670545"
---
# <a name="managing-fragments"></a>Gestione dei frammenti

Per facilitare la gestione di frammenti, Android offre il `FragmentManager` classe. Ogni attività dispone di un'istanza di `Android.App.FragmentManager` che verrà trovare o modificare in modo dinamico relativi frammenti. Ogni set di queste modifiche è noto come un *transazione*e viene eseguita usando una delle API contenute nella classe `Android.App.FragmentTransation`, che è gestito dal `FragmentManager`. Un'attività può avviare una transazione simile alla seguente:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Queste modifiche per i frammenti vengono eseguite nel `FragmentTransaction` quando si utilizza, ad esempio i metodi `Add()`, `Remove(),` e `Replace().` le modifiche vengono quindi applicate utilizzando `Commit()`. Le modifiche in una transazione non vengono eseguite immediatamente.
Al contrario, vengono pianificati per l'esecuzione nel thread dell'interfaccia utente dell'attività appena possibile.

Nell'esempio seguente viene illustrato come aggiungere un frammento a un contenitore esistente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Se una transazione viene eseguito il commit dopo `Activity.OnSaveInstanceState()` viene chiamato, verrà generata un'eccezione. Ciò accade perché quando l'attività Salva lo stato, Android salva anche lo stato di eventuali frammenti ospitati. Se tutte le transazioni frammento vengono eseguito il commit dopo questo punto, lo stato di queste transazioni andranno perse quando l'attività viene ripristinato.

È possibile salvare le transazioni di frammento dell'attività [stack Indietro](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) effettuando una chiamata a `FragmentTransaction.AddToBackStack()`. Ciò consente all'utente di spostarsi all'indietro tramite frammento viene modificato quando il **nuovamente** pulsante viene premuto. Senza una chiamata a questo metodo, frammenti che sono state rimosse verranno eliminato definitivamente e non sarà disponibili se l'utente passa attraverso l'attività.

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


## <a name="communicating-with-fragments"></a>Comunicazione con frammenti

Il *FragmentManager* a conoscenza di tutti i frammenti associati a un'attività e fornisce due metodi per consentire di individuare questi frammenti:

-   **FindFragmentById** &ndash; questo metodo troverà un frammento con l'ID specificato nel file di layout o l'ID del contenitore quando il frammento è stato aggiunto come parte di una transazione.

-   **FindFragmentByTag** &ndash; questo metodo viene utilizzato per trovare un frammento che ha un tag che è stato specificato nel file di layout o che è stato aggiunto in una transazione.

I frammenti e attività di riferimento di `FragmentManager`, in modo che le stesse tecniche utilizzate per comunicare avanti e indietro tra di essi. Un'applicazione può trovare un frammento di riferimento usando uno di questi due metodi, eseguire il cast di tale riferimento al tipo appropriato e quindi chiamare direttamente i metodi per il frammento. Il frammento di codice seguente viene fornito un esempio:

È anche possibile usare l'attività di `FragmentManager` trovare frammenti:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Comunicazione con l'attività

È possibile che un frammento di utilizzare il `Fragment.Activity` proprietà che fa riferimento il relativo host. Eseguendo il cast di attività per un tipo più specifico, è possibile che un'attività per chiamare metodi e proprietà sul relativo host, come illustrato nell'esempio seguente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
