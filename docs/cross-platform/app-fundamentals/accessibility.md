---
title: Accessibilità in App Xamarin
description: Questo documento offre diversi suggerimenti per la creazione di App accessibili. Ad esempio, include le raccomandazioni sui grandi tipi di carattere, contrasto elevato, autodescrittive interfacce e altro ancora.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ec264e0f3d381fdac46c79dd479da2bc768954f
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668335"
---
# <a name="accessibility-in-xamarin-apps"></a>Accessibilità in App Xamarin

_Assicurarsi che le app sono utilizzabili per il pubblico più ampio possibile_

Accessibilità si riferisce al concetto di progettazione delle interfacce utente di app che funzionano anche funzionalità di assistenza di input e di visualizzazione del sistema operativo, ad esempio tipo di grandi dimensioni, contrasto elevato, fare zoom avanti, durante la lettura dello schermo (sintesi vocale), segnali visivi feedback visivo o aptico, e metodi di input alternativi.

Piattaforme desktop e per dispositivi mobili come iOS, Android e Windows forniscono compilato per le API che consentono agli sviluppatori di creare App accessibili, ad esempio [Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) e [Apple VoiceOver](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API specifiche della piattaforma

Per implementare le linee guida in questo documento, usare le API fornite da ogni piattaforma:

- [**Accessibilità di Android**](~/android/app-fundamentals/accessibility.md)
- [**iOS accessibilità**](~/ios/app-fundamentals/accessibility.md)
- [**Accessibilità di OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Elenco di controllo di accesso facilitato

Seguire questi suggerimenti per assicurarsi che le app siano accessibili al pubblico più vasto possibile. Consultare il [Android accessibilità test Checklist](https://developer.android.com/training/accessibility/testing.html) e [pagina di accesso facilitato di Apple](http://www.apple.com/accessibility/) per altre informazioni.

### <a name="support-large-fonts-and-high-contrast"></a>Supporta i tipi di carattere di grandi dimensioni e il contrasto elevato

Evitare dimensioni del controllo a livello di codice e, invece, preferire i layout che è possono ridimensionare per contenere i caratteri di dimensioni maggiori.
Testare le combinazioni di colori nella modalità a contrasto elevato per assicurarsi che siano leggibili.

### <a name="make-the-user-interface-self-describing"></a>Rendere l'utente interfaccia autodescrittivi

Contrassegnare tutti gli elementi dell'interfaccia utente con un testo descrittivo e gli hint che sono compatibili con la schermata leggere le API in ciascuna piattaforma.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Assicurarsi che le immagini e icone hanno una descrizione di testo alternativo

Immagini e icone che fanno parte dell'interfaccia utente dell'applicazione, quali pulsanti o indicatori di stato, ad esempio, devono essere contrassegnate con una descrizione accessibile.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Progettare la struttura ad albero visuale con navigazione accessibile presente

Usare i controlli di layout appropriato o le API in modo che lo spostamento tra i controlli usando metodi di input alternativi seguono lo stesso flusso logico come tramite il touchscreen.

Escludere gli elementi non necessari dai lettori dello schermo (immagini decorative o etichette per i campi che sono già accessibili, ad esempio).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Non fare affidamento sugli segnali audio o il colore da solo

Evitare situazioni in cui l'unica indicazione riguardo lo stato di avanzamento, completamento o qualche altro stato è un file audio o modifica del colore. Sia l'interfaccia utente includono segnali visivi Cancella (con file audio e il colore per rinforzo solo) o aggiungere indicatori accessibilità specifiche di progettazione.

Quando si scelgono colori, provare a evitare una tavolozza che è difficile distinguere per gli utenti con cecità colore.

### <a name="captioning-for-video-text-for-audio"></a>I sottotitoli codificati per video o testo per l'audio

Fornire le didascalie per contenuti video e uno script leggibile per il contenuto audio. È inoltre utile fornire i controlli che regolano la velocità di contenuti audio o video e assicurarsi che il volume e sono facili da trovare e usare i pulsanti play/pausa.

### <a name="localize"></a>Localize

Le descrizioni di accessibilità possono (e dovrebbero) essere localizzate in cui l'applicazione supporta più lingue.



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità di Android](~/android/app-fundamentals/accessibility.md)
- [iOS accessibilità](~/ios/app-fundamentals/accessibility.md)
- [Accessibilità di OS X](~/mac/app-fundamentals/accessibility.md)
- [Accessibilità di xamarin. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
