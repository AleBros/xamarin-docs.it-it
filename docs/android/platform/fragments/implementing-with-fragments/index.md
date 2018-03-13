---
title: Implementazione di frammenti
description: Android 3.0 sono state introdotte frammenti. I frammenti sono componenti indipendenti e modulari usati per semplificare la scrittura di applicazioni che possono essere eseguite su schermi di dimensioni diverse. Questo articolo descrive come utilizzare i frammenti per sviluppare applicazioni di xamarin e come supportare i frammenti nei 3.0 dispositivi pre-Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2ed67eac51f6edcfda16caf73e4667c49124082c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="implementing-with-fragments"></a>Implementazione di frammenti

_Android 3.0 sono state introdotte frammenti. I frammenti sono componenti indipendenti e modulari usati per semplificare la scrittura di applicazioni che possono essere eseguite su schermi di dimensioni diverse. Questo articolo descrive come utilizzare i frammenti per sviluppare applicazioni di xamarin e come supportare i frammenti nei 3.0 dispositivi pre-Android._


## <a name="overview"></a>Panoramica

In questa sezione verrà esaminato come creare un'applicazione che consente di visualizzare un elenco di riproduzione di considerato e un'offerta da ogni riproduzione selezionato. L'applicazione utilizzerà frammenti in modo che è possibile definire i componenti dell'interfaccia utente in un'unica posizione, ma quindi utilizzarli in diversi fattori di forma. Ad esempio, le schermate seguenti mostrano l'applicazione in esecuzione su un tablet 10", nonché su un telefono:

[![Schermate dell'app di esempio in esecuzione sul telefono e tablet](images/intro-screenshot-sml.png)](images/intro-screenshot.png#lightbox)

In questa sezione verrà trattati i seguenti argomenti:

- **Creazione di frammenti** &ndash; viene illustrato come creare un frammento di visualizzare un elenco di riproduzione di considerato e un altro frammento per la visualizzazione di un'offerta da ogni play.

- **Supporto di diverse dimensioni dello schermo** &ndash; viene illustrato come layout, l'applicazione per usufruire di maggiori dimensioni dello schermo.

- **Mediante il pacchetto di supporto Android** &ndash; implementa il pacchetto di supporto Android, quindi apporta alcune modifiche minori per le attività nell'applicazione, consentendo l'esecuzione in versioni precedenti di Android.


## <a name="requirements"></a>Requisiti

Questa procedura dettagliata richiede xamarin 4.0 o versione successiva. Inoltre sarà necessario installare il pacchetto di supporto Android, come descritto nella documentazione di frammenti.


## <a name="introduction"></a>Introduzione

Nell'esempio che verrà creato in questa sezione, le attività non contengono la logica per il caricamento dell'elenco, rispondere alla selezione dell'utente o visualizzare l'offerta per la riproduzione selezionato. Questa logica esiste nei singoli frammenti.
Inserendo questa logica nei frammenti autonomamente, è possibile suddividere il flusso di lavoro dell'applicazione per supportare schermi di grandi dimensioni con un'attività o schermi di piccole dimensioni con più attività senza dover scrivere una logica diversa per ogni attività. Su un tablet, entrambi i frammenti saranno in un'attività. Su un telefono, i frammenti verranno ospitati in attività diverse.

Questa applicazione include le parti seguenti:

 **MainActivity** : consente di visualizzare uno o entrambi i frammenti, a seconda delle dimensioni dello schermo. Questo è l'attività di avvio.

 **TitlesFragment** : visualizza un elenco di riproduzione di considerato da cui l'utente può selezionare.

 **DetailsFragment** : Visualizza l'offerta dalla riproduzione selezionato.

 **DetailsActivity** : ospita e visualizza il DetailsFragment.
Questa attività viene utilizzata dai dispositivi Monitor di piccole dimensioni, ad esempio telefoni.



## <a name="related-links"></a>Collegamenti correlati

- [FragmentsWalkthrough (esempio)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Panoramica della finestra di progettazione](~/android/user-interface/android-designer/index.md)
- [Esempi di xamarin: raccolta Nido d'API](https://developer.xamarin.com/samples/HoneycombGallery/)
- [Implementazione di frammenti](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](http://developer.android.com/sdk/compatibility-library.html)
