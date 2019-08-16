---
title: Gestione dei frammenti
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 5b829b1c38007bbb070b643a355d70bffaef01a5
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524308"
---
# <a name="managing-fragments"></a>Gestione dei frammenti

Per semplificare la gestione dei frammenti, Android fornisce la `FragmentManager` classe. Ogni attività dispone di un'istanza `Android.App.FragmentManager` di in grado di trovare o modificare in modo dinamico i relativi frammenti. Ogni set di queste modifiche è noto come *transazione*e viene eseguito tramite una delle API contenute nella classe `Android.App.FragmentTransation`, gestito da. `FragmentManager` Un'attività può avviare una transazione come la seguente:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Queste modifiche ai frammenti vengono eseguite nell' `FragmentTransaction` istanza di tramite metodi `Add()`quali `Remove(),` `Commit()`e `Replace().` le modifiche vengono quindi applicate tramite. Le modifiche in una transazione non vengono eseguite immediatamente.
Sono invece pianificati per essere eseguiti nel thread dell'interfaccia utente dell'attività appena possibile.

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

Se viene eseguito il commit di `Activity.OnSaveInstanceState()` una transazione dopo la chiamata a, verrà generata un'eccezione. Questo problema si verifica perché quando l'attività Salva il proprio stato, Android salva anche lo stato dei frammenti ospitati. Se dopo questo punto viene eseguito il commit di transazioni di frammenti, lo stato di queste transazioni andrà perso quando viene ripristinata l'attività.

È possibile salvare le transazioni Fragment nello [stack back](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) dell'attività effettuando una chiamata a `FragmentTransaction.AddToBackStack()`. Ciò consente all'utente di spostarsi all'indietro tra le modifiche del frammento quando viene premuto il pulsante **indietro** . Senza una chiamata a questo metodo, i frammenti rimossi verranno eliminati definitivamente e non saranno disponibili se l'utente torna all'interno dell'attività.

Nell'esempio seguente viene illustrato come utilizzare il `AddToBackStack` metodo di un `FragmentTransaction` oggetto per sostituire un frammento, mantenendo allo stesso tempo lo stato del primo frammento sullo stack indietro:

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

*FragmentManager* conosce tutti i frammenti collegati a un'attività e fornisce due metodi che consentono di trovare i frammenti seguenti:

- **FindFragmentById** &ndash; Questo metodo troverà un frammento usando l'ID specificato nel file di layout o l'ID del contenitore quando il frammento è stato aggiunto come parte di una transazione.

- **FindFragmentByTag** &ndash; Questo metodo viene utilizzato per trovare un frammento con un tag fornito nel file di layout o aggiunto in una transazione.

Sia i frammenti che le `FragmentManager`attività fanno riferimento a, quindi le stesse tecniche vengono usate per comunicare tra loro. Un'applicazione può trovare un frammento di riferimento usando uno di questi due metodi, eseguire il cast del riferimento al tipo appropriato e quindi chiamare direttamente i metodi sul frammento. Il frammento di codice seguente fornisce un esempio:

È anche possibile che l'attività usi per individuare i `FragmentManager` frammenti:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Comunicazione con l'attività

È possibile che un frammento usi la `Fragment.Activity` proprietà per fare riferimento al relativo host. Eseguendo il cast dell'attività a un tipo più specifico, è possibile che un'attività chiami metodi e proprietà nell'host, come illustrato nell'esempio seguente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
