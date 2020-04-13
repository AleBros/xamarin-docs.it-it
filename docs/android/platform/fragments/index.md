---
title: Frammenti
description: Android 3.0 ha introdotto Fragments, mostrando come supportare progetti più flessibili per le diverse dimensioni dello schermo presenti su telefoni e tablet. Questo articolo tratterà come usare frammenti per sviluppare applicazioni Xamarin.Android e anche come supportare frammenti su dispositivi pre-Android 3.0 (API livello 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 5d243429fe4f61768568a634b205055c1ad94297
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020258"
---
# <a name="fragments"></a>Frammenti

_Android 3.0 ha introdotto Fragments, mostrando come supportare progetti più flessibili per le diverse dimensioni dello schermo presenti su telefoni e tablet. Questo articolo tratterà come usare frammenti per sviluppare applicazioni Xamarin.Android e anche come supportare frammenti su dispositivi pre-Android 3.0 (API livello 11)._

## <a name="fragments-overview"></a>Cenni preliminari sui frammentiFragments Overview

Le dimensioni dello schermo più grandi presenti nella maggior parte dei tablet hanno aggiunto un ulteriore livello di complessità allo sviluppo Android, un layout progettato per il piccolo schermo non funziona necessariamente anche per schermi più grandi e viceversa. Per ridurre il numero di complicazioni introdotte, Android 3.0 ha aggiunto due nuove funzionalità, *Frammenti* e Pacchetti di *supporto.*

I frammenti possono essere considerati come moduli dell'interfaccia utente. Consentono allo sviluppatore di suddividere l'interfaccia utente in parti isolate e riutilizzabili che possono essere eseguite in attività separate. In fase di esecuzione, le attività stesse decideranno quali frammenti utilizzare.

Pacchetti di supporto sono stati originariamente chiamati librerie di *compatibilità* e consentito frammenti da utilizzare su dispositivi che eseguono versioni di Android precedenti ad Android 3.0 (API livello 11).

Ad esempio, l'immagine seguente illustra come una singola applicazione usa frammenti tra diversi fattori di forma del dispositivo.

[![Diagramma dell'utilizzo dei frammenti in compresse e portatili](images/00.png)](images/00.png#lightbox)

*Il frammento A* contiene un elenco, mentre il *frammento B* contiene i dettagli per un elemento selezionato in tale elenco. Quando l'applicazione viene eseguita in una tavoletta, può visualizzare entrambi i frammenti nella stessa attività. Quando la stessa applicazione viene eseguita su un portatile (con le dimensioni dello schermo più piccole), i frammenti sono ospitati in due attività separate. Frammento A e Frammento B sono gli stessi in entrambi i fattori di forma, ma le attività che li ospitano sono diversi.

Per consentire a un'attività di coordinare e gestire tutti questi frammenti, Android ha introdotto una nuova classe denominata *FragmentManager*. Ogni attività ha la `FragmentManager` propria istanza di un per l'aggiunta, l'eliminazione e la ricerca di frammenti ospitati. The following diagram illustrates the relationship between Fragments and Activities:

[![Diagramma che illustra le relazioni tra Attività, Gestione frammenti e Frammenti](images/01.png)](images/01.png#lightbox)

Per alcuni aspetti, i frammenti possono essere considerati come controlli compositi o come mini-attività. Raggruppano parti dell'interfaccia utente in moduli riutilizzabili che possono essere usati in modo indipendente dagli sviluppatori in Attività.They bundle up pieces of UI into reusable modules that can then be used independently by developers in Activities. Un frammento ha una gerarchia di visualizzazione, proprio come un'attività, ma, a differenza di un'attività, può essere condiviso tra le schermate. Le viste differiscono dai frammenti in quanto i frammenti hanno il proprio ciclo di vita; viste non lo fanno.

Mentre l'attività è un host di uno o più frammenti, non è direttamente a conoscenza dei frammenti stessi. Analogamente, i frammenti non sono direttamente a conoscenza di altri frammenti nell'attività di hosting. Tuttavia, frammenti e attività `FragmentManager` sono consapevoli di in loro attività. Utilizzando `FragmentManager`, è possibile che un'attività o un frammento ottenga un riferimento a un'istanza specifica di un frammento e quindi chiami i metodi su tale istanza. In questo modo, l'attività o frammenti possono comunicare e interagire con altri frammenti.

Questa guida contiene una copertura completa su come usare i frammenti, tra cui:This guide contains comprehensive coverage about how to use Fragments, including:

- **Creazione di frammenti** – Come creare un frammento di base e metodi chiave che devono essere implementati.
- **Gestione frammenti e transazioni:** come modificare i frammenti in fase di esecuzione.
- **Pacchetto di supporto Android:** come usare le librerie che consentono l'utilizzo di frammenti nelle versioni precedenti di Android.Android Support Package – How to use the libraries that allow Fragments to be used on older versions of Android.

## <a name="requirements"></a>Requisiti

I frammenti sono disponibili nell'SDK Android a partire dal livello API 11 (Android 3.0), come illustrato nella schermata seguente:Fragments are available in the Android SDK starting with API level 11 (Android 3.0), as shown in the following screenshot:

[![Selezione del livello API in Android SDK Manager](images/02.png)](images/02.png#lightbox)

Frammenti sono disponibili in Xamarin.Android 4.0 e versioni successive. Un'applicazione Xamarin.Android deve essere destinata almeno API livello 11 (Android 3.0) o superiore per utilizzare frammenti. Il Framework di destinazione può essere impostato nelle proprietà del progetto come illustrato di seguito:The Target Framework may be set in the project Properties as shown below:

[![Impostazione del livello API del framework di destinazione nelle opzioni del progettoSetting the Target Framework API level in the Project Options](images/03-sml.png)](images/03.png#lightbox)

È possibile usare frammenti nelle versioni precedenti di Android utilizzando il pacchetto di supporto Android e Xamarin.Android 4.2 o versione successiva. Come fare questo è trattato in modo più dettagliato nei documenti di questa sezione.

## <a name="related-links"></a>Collegamenti correlati

- [Galleria a nido d'ape (esempio)Honeycomb Gallery (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [Frammenti](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](https://developer.android.com/sdk/compatibility-library.html)
