---
title: Frammenti
description: "Android 3.0 sono state introdotte frammenti, che illustra come supportare progettazioni più flessibile per le dimensioni dello schermo diverse molti presenti nei telefoni e Tablet. In questo articolo illustra come usare i frammenti di sviluppo di applicazioni di xamarin e come supportare frammenti dispositivi pre-Android 3.0 (API livello 11)."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 0486b9e4371a1bcab02921da42bcb929f00a782f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="fragments"></a>Frammenti

_Android 3.0 sono state introdotte frammenti, che illustra come supportare progettazioni più flessibile per le dimensioni dello schermo diverse molti presenti nei telefoni e Tablet. In questo articolo illustra come usare i frammenti di sviluppo di applicazioni di xamarin e come supportare frammenti dispositivi pre-Android 3.0 (API livello 11)._

## <a name="fragments-overview"></a>Panoramica di frammenti

Le dimensioni dello schermo più grande trovate nella maggior parte dei Tablet aggiunto un ulteriore livello di complessità per lo sviluppo di Android, un layout per schermo di piccole dimensioni non necessariamente funziona anche per le schermate più grande e viceversa. Per ridurre il numero di complicazioni che questa situazione ha introdotto, Android 3.0 ha aggiunto due nuove funzionalità, *frammenti* e *pacchetti di supporto*.

Frammenti possono essere considerati come moduli di interfaccia utente. Essi consentono agli sviluppatori di dividere l'interfaccia utente in parti isolate e riutilizzabile che può essere eseguito in attività distinte. In fase di esecuzione, le attività stesse stabilirà frammenti da utilizzare.

Pacchetti di supporto sono stati originariamente chiamati *compatibilità librerie* e consentiti frammenti da usare nei dispositivi che eseguono versioni di Android precedente alla 3.0 Android (API livello 11).

Ad esempio, l'immagine seguente viene illustrato come una singola applicazione usa i frammenti in diversi fattori di forma dispositivo.

[![Diagramma dell'utilizzo di frammenti in Tablet e ricevitori](images/00.png)](images/00.png)

*Frammento A* contiene un elenco, mentre *frammento B* contiene i dettagli per un elemento selezionato nell'elenco. Quando l'applicazione viene eseguita su un tablet, è possibile visualizzare entrambi i frammenti nella stessa attività. Quando la stessa applicazione viene eseguita in un ricevitore (con le dimensioni dello schermo più piccolo), i frammenti sono ospitati in due attività distinte. Frammento di A e B frammento sono gli stessi in entrambi i fattori di forma, ma le attività che li ospitano sono diverse.

Per facilitare un impegno di coordinare e gestire tutti i frammenti, Android ha introdotto una nuova classe denominata la *FragmentManager*. Ogni attività dispone della propria istanza di un `FragmentManager` per l'aggiunta, eliminazione e individuazione di frammenti ospitato. Il diagramma seguente illustra la relazione tra frammenti e attività:

[![Diagramma che illustra le relazioni tra attività, gestione di frammento e frammenti](images/01.png)](images/01.png)

Alcuni relativamente, frammenti possono essere considerati come controlli compositi o come attività di formattazione rapida. Essi aggregare le parti dell'interfaccia utente in moduli riutilizzabili che possono quindi essere utilizzati indipendentemente dagli sviluppatori di attività. Un frammento dispone di una gerarchia di visualizzazione, come un'attività, ma, a differenza di un'attività, può essere condivise tra le schermate. Viste differiscono dai frammenti in frammenti hanno i propri ciclo di vita; viste non.

Mentre l'attività è un host per uno o più frammenti, non è direttamente compatibile con i frammenti stessi. Analogamente, frammenti non sono direttamente a conoscenza di altri frammenti nell'attività di hosting. Tuttavia, sono consapevoli di frammenti e le attività di `FragmentManager` di attività. Tramite il `FragmentManager`, è possibile che un'attività o un frammento ottenere un riferimento a un'istanza specifica di un frammento e quindi chiamare i metodi in tale istanza. In questo modo, l'attività o frammenti possono comunicare e interagire con altri frammenti.

Questa guida contiene una descrizione dettagliata su come usare i frammenti, tra cui:

-   **Creazione di frammenti** : come creare un frammento di base e i metodi principali che devono essere implementati.
-   **Frammento di gestione e le transazioni** : utilizzo dei frammenti in fase di esecuzione.
-   **Pacchetto di supporto Android** : la modalità per utilizzare le librerie che consentono di frammenti da utilizzare nelle versioni precedenti di Android.


## <a name="requirements"></a>Requisiti

I frammenti sono disponibili nel SDK Android a partire da livello API 11 (Android 3.0), come illustrato nella schermata seguente:

[![Selezionare il livello di API di Android SDK Manager](images/02.png)](images/02.png)

I frammenti sono disponibili in xamarin 4.0 e versioni successive. Un'applicazione di xamarin destinazione deve essere almeno il livello di API 11 (Android 3.0) o versione successiva per poter utilizzare i frammenti. Il Framework di destinazione può essere impostato nelle opzioni del progetto, come illustrato di seguito:

[![Impostazione del livello di API di Framework di destinazione nelle opzioni di progetto](images/03.png)](images/03.png)

È possibile utilizzare frammenti nelle versioni precedenti di Android utilizzando il pacchetto di supporto Android e xamarin 4.2 o versioni successive. Come eseguire questa operazione viene illustrata più dettagliatamente nei documenti di questa sezione.


## <a name="related-links"></a>Collegamenti correlati

- [Raccolta Nido d'API (esempio)](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [Frammenti](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](http://developer.android.com/sdk/compatibility-library.html)
- [Webinar MOTODEV: Introduzione a frammenti](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
