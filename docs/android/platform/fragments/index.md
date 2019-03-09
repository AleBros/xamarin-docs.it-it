---
title: Frammenti
description: 'Android 3.0 ha introdotto i frammenti, che illustra come supportare le progettazioni più flessibile per le dimensioni dello schermo diverse molti trovate su telefoni e Tablet. Questo articolo illustra come usare i frammenti per sviluppare applicazioni xamarin. Android, nonché come supportare frammenti dispositivi precedenti ad Android 3.0 (API livello 11).'
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
---

# <a name="fragments"></a>Frammenti

_Android 3.0 ha introdotto i frammenti, che illustra come supportare le progettazioni più flessibile per le dimensioni dello schermo diverse molti trovate su telefoni e Tablet. Questo articolo illustra come usare i frammenti per sviluppare applicazioni xamarin. Android, nonché come supportare frammenti dispositivi precedenti ad Android 3.0 (API livello 11)._

## <a name="fragments-overview"></a>Panoramica di frammenti

Le dimensioni dello schermo più grande trovate nella maggior parte dei Tablet aggiunti a un ulteriore livello di complessità per lo sviluppo di Android, un layout destinato schermo di piccole dimensioni non funzionano necessariamente anche per schermi più grandi e viceversa. Per ridurre il numero di complicazioni che questa situazione ha introdotto, Android 3.0 ha aggiunto due nuove funzionalità *frammenti* e *pacchetti di supporto*.

Frammenti possono essere considerati come moduli di interfaccia utente. Essi consentono agli sviluppatori di suddividere l'interfaccia utente in parti isolate e riutilizzabili che possono essere eseguiti in attività distinte. In fase di esecuzione, le attività stesse deciderà quale frammenti da utilizzare.

Pacchetti di supporto sono stati chiamati originariamente *compatibilità delle librerie* e consentiti frammenti da usare nei dispositivi che eseguono versioni di Android precedente alla 3.0 Android (API livello 11).

Ad esempio, nell'immagine seguente illustra il modo in cui una singola applicazione usa frammenti tra diverse tipologie di dispositivi.

[![Diagramma della modalità di utilizzo frammenti nei Tablet e dispositivi](images/00.png)](images/00.png#lightbox)

*Un frammento* contiene un elenco, mentre *frammento B* contiene i dettagli relativi agli elementi selezionati nell'elenco. Quando l'applicazione viene eseguita su un tablet, è possibile visualizzare entrambi i frammenti nella stessa attività. Quando la stessa applicazione viene eseguita in un ricevitore (con le dimensioni dello schermo più piccolo), i frammenti sono ospitati in due attività distinte. Frammento di A e B frammento sono gli stessi in entrambi i fattori di forma, ma le attività che li ospitano sono diverse.

Per coordinare e gestire tutti i frammenti di un'attività, Android ha introdotto una nuova classe denominata la *FragmentManager*. Ogni attività ha una propria istanza di un `FragmentManager` per l'aggiunta, eliminazione e individuazione ospitato frammenti. Il diagramma seguente illustra la relazione tra le attività e frammenti:

[![Diagramma che illustra le relazioni tra attività, gestione di frammento e frammenti](images/01.png)](images/01.png#lightbox)

In alcuni saluti, frammenti possono essere considerati come i controlli composti o mini-Activities. Essi vengono raggruppate le parti dell'interfaccia utente in moduli riutilizzabili che possono quindi essere utilizzati in modo indipendente dagli sviluppatori nelle attività. Un frammento dispone di una gerarchia di visualizzazione, ovvero come un'attività, ma, a differenza di un'attività, può essere condivisa tra le schermate. Le visualizzazioni sono diverse dalle frammenti in frammenti hanno proprio ciclo di vita; le viste non lo sono.

L'attività è un host a uno o più frammenti, ma non direttamente a conoscenza dei frammenti stessi. I frammenti in modo analogo, non sono direttamente a conoscenza di altri frammenti nell'attività di hosting. Tuttavia, sono consapevoli di frammenti e attività di `FragmentManager` le loro attività. Tramite il `FragmentManager`, è possibile che un'attività o un frammento di ottenere un riferimento a un'istanza specifica di un frammento, e quindi chiamare metodi su quell'istanza. In questo modo, l'attività o frammenti possono comunicare e interagire con altri frammenti.

Questa guida contiene una descrizione dettagliata sull'uso di frammenti, tra cui:

-   **Creazione di frammenti** : come creare un frammento di base e metodi principali che devono essere implementati.
-   **Frammento di gestione e le transazioni** : come modificare i frammenti in fase di esecuzione.
-   **Pacchetto di supporto Android** : usare le librerie che consentono di frammenti da utilizzare nelle versioni precedenti di Android.


## <a name="requirements"></a>Requisiti

I frammenti sono disponibili in Android SDK inizia con il livello API 11 (Android 3.0), come illustrato nello screenshot seguente:

[![Selezionando il livello API di Android SDK Manager](images/02.png)](images/02.png#lightbox)

I frammenti sono disponibili in xamarin. Android 4.0 e versioni successive. Un'applicazione xamarin. Android deve avere come destinazione almeno API livello 11 (Android 3.0) o versione successiva per poter usare i frammenti. Il Framework di destinazione può essere impostato nel progetto di proprietà come illustrato di seguito:

[![Impostazione del livello API di Framework di destinazione nelle opzioni del progetto](images/03-sml.png)](images/03.png#lightbox)

È possibile usare i frammenti in versioni precedenti di Android usando il pacchetto di supporto Android e xamarin. Android 4.2 o versione successiva. Come eseguire questa operazione è descritta più dettagliatamente nei documenti in questa sezione.


## <a name="related-links"></a>Collegamenti correlati

- [Raccolta honeycomb (esempio)](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [Frammenti](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](https://developer.android.com/sdk/compatibility-library.html)
- [Webinar MOTODEV: Introduzione a frammenti](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
