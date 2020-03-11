---
title: Frammenti
description: Android 3,0 ha introdotto frammenti, che illustrano come supportare soluzioni più flessibili per le diverse dimensioni dello schermo disponibili in telefoni e tablet. Questo articolo illustra come usare i frammenti per sviluppare applicazioni Novell. Android e come supportare i frammenti nei dispositivi pre-Android 3,0 (livello API 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 5d243429fe4f61768568a634b205055c1ad94297
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020258"
---
# <a name="fragments"></a>Frammenti

_Android 3,0 ha introdotto frammenti, che illustrano come supportare soluzioni più flessibili per le diverse dimensioni dello schermo disponibili in telefoni e tablet. Questo articolo illustra come usare i frammenti per sviluppare applicazioni Novell. Android e come supportare i frammenti nei dispositivi pre-Android 3,0 (livello API 11)._

## <a name="fragments-overview"></a>Cenni preliminari sui frammenti

Le dimensioni dello schermo più grandi trovate nella maggior parte dei tablet hanno aggiunto un ulteriore livello di complessità allo sviluppo per Android: un layout progettato per la piccola schermata non funziona necessariamente anche per schermi più grandi e viceversa. Per ridurre il numero di complicazioni introdotte da questa introduzione, Android 3,0 ha aggiunto due nuove funzionalità, *frammenti* e *pacchetti di supporto*.

I frammenti possono essere considerati come moduli dell'interfaccia utente. Consentono allo sviluppatore di dividere l'interfaccia utente in parti isolate e riutilizzabili che possono essere eseguite in attività separate. In fase di esecuzione, le attività stabiliranno i frammenti da usare.

I pacchetti di supporto sono stati originariamente denominati *librerie di compatibilità* e frammenti consentiti da usare nei dispositivi che eseguono versioni di Android prima di Android 3,0 (livello API 11).

Nell'immagine seguente, ad esempio, viene illustrato il modo in cui una singola applicazione utilizza frammenti in diversi fattori di forma del dispositivo.

[![diagramma del modo in cui vengono usati i frammenti nei tablet e nei dispositivi portatili](images/00.png)](images/00.png#lightbox)

Il *frammento a* contiene un elenco, mentre il *frammento B* contiene i dettagli per un elemento selezionato nell'elenco. Quando l'applicazione viene eseguita in un tablet, può visualizzare entrambi i frammenti nella stessa attività. Quando la stessa applicazione viene eseguita su un dispositivo portatile (con dimensioni dello schermo inferiori), i frammenti sono ospitati in due attività separate. Il frammento A e il frammento B sono gli stessi in entrambi i fattori di forma, ma le attività che li ospitano sono differenti.

Per consentire a un'attività di coordinare e gestire tutti questi frammenti, Android ha introdotto una nuova classe denominata *FragmentManager*. Ogni attività dispone di una propria istanza di un `FragmentManager` per l'aggiunta, l'eliminazione e la ricerca di frammenti ospitati. Il diagramma seguente illustra la relazione tra i frammenti e le attività:

[![diagramma che illustra le relazioni tra attività, Gestione frammenti e frammenti](images/01.png)](images/01.png#lightbox)

Per quanto riguarda, i frammenti possono essere considerati come controlli compositi o come mini-attività. Combinano parti dell'interfaccia utente in moduli riutilizzabili che possono essere usati in modo indipendente dagli sviluppatori nelle attività. Un frammento ha una gerarchia di visualizzazione, come un'attività, ma, a differenza di un'attività, può essere condivisa tra le schermate. Le visualizzazioni differiscono da frammenti in quanto i frammenti hanno il proprio ciclo di vita; le visualizzazioni non lo sono.

Mentre l'attività è un host di uno o più frammenti, non è in grado di riconoscere direttamente i frammenti. Analogamente, i frammenti non sono in grado di riconoscere direttamente altri frammenti nell'attività di hosting. Tuttavia, i frammenti e le attività sono consapevoli del `FragmentManager` nell'attività. Utilizzando la `FragmentManager`, è possibile che un'attività o un frammento ottenga un riferimento a un'istanza specifica di un frammento e quindi chiami i metodi su tale istanza. In questo modo, l'attività o i frammenti possono comunicare e interagire con altri frammenti.

Questa guida contiene una copertura completa su come usare i frammenti, tra cui:

- **Creazione di frammenti** : come creare un frammento di base e metodi chiave che devono essere implementati.
- **Gestione dei frammenti e transazioni** : come modificare i frammenti in fase di esecuzione.
- **Pacchetto di supporto Android** : come usare le librerie che consentono di usare i frammenti nelle versioni precedenti di Android.

## <a name="requirements"></a>Requisiti

I frammenti sono disponibili nell'Android SDK a partire dal livello API 11 (Android 3,0), come illustrato nello screenshot seguente:

[![selezione del livello API in gestione Android SDK](images/02.png)](images/02.png#lightbox)

I frammenti sono disponibili in Novell. Android 4,0 e versioni successive. Per poter usare i frammenti, un'applicazione Novell. Android deve avere come destinazione almeno l'API livello 11 (Android 3,0) o versione successiva. Il Framework di destinazione può essere impostato nelle proprietà del progetto, come illustrato di seguito:

[![l'impostazione del livello API del Framework di destinazione nelle opzioni del progetto](images/03-sml.png)](images/03.png#lightbox)

È possibile usare i frammenti nelle versioni precedenti di Android usando il pacchetto del supporto Android e Novell. Android 4,2 o versione successiva. Questa operazione viene illustrata più dettagliatamente nei documenti di questa sezione.

## <a name="related-links"></a>Collegamenti correlati

- [Raccolta di honeycomb (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [Frammenti](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto per il supporto](https://developer.android.com/sdk/compatibility-library.html)
