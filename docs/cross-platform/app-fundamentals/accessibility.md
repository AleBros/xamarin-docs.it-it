---
title: Accessibilità nelle app Novell
description: Questo documento fornisce diversi suggerimenti per la creazione di app accessibili. Sono inclusi, ad esempio, consigli su tipi di carattere di grandi dimensioni, contrasto elevato, interfacce autodescrittive e altro ancora.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: bd97406501df155561e0e240607740430dbf4ac3
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284580"
---
# <a name="accessibility-in-xamarin-apps"></a>Accessibilità nelle app Novell

_Verificare che le app siano utilizzabili dal più ampio pubblico possibile_

L'accessibilità si riferisce al concetto di progettazione di interfacce utente di app che funzionano correttamente con funzionalità di visualizzazione e di input del sistema operativo, ad esempio tipi di grandi dimensioni, contrasto elevato, zoom avanti, lettura dello schermo (sintesi vocale), suggerimenti per commenti visivi o tattili e metodi di input alternativi.

Le piattaforme desktop e per dispositivi mobili, ad esempio iOS, Android e Windows, offrono API incorporate che consentono agli sviluppatori di creare app accessibili, ad esempio [Google](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) discussione e [VoiceOver di Apple](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API specifiche della piattaforma

Per implementare le linee guida in questo documento, usare le API fornite da ogni piattaforma:

- [**Accessibilità Android**](~/android/app-fundamentals/accessibility.md)
- [**Accessibilità iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Accessibilità OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Elenco di controllo di accessibilità

Seguire questi suggerimenti per assicurarsi che le app siano accessibili al più ampio pubblico possibile. Per ulteriori informazioni, vedere l'elenco di controllo dell' [accessibilità Android](https://developer.android.com/training/accessibility/testing.html) e la [pagina di accessibilità di Apple](http://www.apple.com/accessibility/) .

### <a name="support-large-fonts-and-high-contrast"></a>Supporto di caratteri di grandi dimensioni e contrasto elevato

Evitare il hardcoded delle dimensioni del controllo e preferisce invece i layout che possono essere ridimensionati in base alle dimensioni del carattere più grandi.
Testare le combinazioni di colori in modalità a contrasto elevato per assicurarsi che siano leggibili.

### <a name="make-the-user-interface-self-describing"></a>Rendere la descrizione automatica dell'interfaccia utente

Contrassegnare tutti gli elementi dell'interfaccia utente con testo descrittivo e hint compatibili con le API per la lettura dello schermo in ogni piattaforma.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Assicurarsi che immagini e icone abbiano una descrizione del testo alternativo

Le immagini e le icone che fanno parte dell'interfaccia utente dell'applicazione, ad esempio pulsanti o indicatori dello stato, devono essere contrassegnate con una descrizione accessibile.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Progettare la struttura ad albero visuale con lo spostamento accessibile

Usare i controlli di layout appropriati o le API in modo che lo spostamento tra i controlli usando metodi di input alternativi segua lo stesso flusso logico usato per la schermata touch screen.

Escludere gli elementi non necessari dalle utilità per la lettura dello schermo (immagini decorative o etichette per i campi già accessibili, ad esempio).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Non fare affidamento solo su segnali audio o colori

Evitare situazioni in cui l'unica indicazione di avanzamento, completamento o altro stato è un suono o una modifica del colore. Progettare l'interfaccia utente in modo da includere segnali visivi chiari (solo con suono e colore per il rinforzo) oppure aggiungere indicatori di accessibilità specifici.

Quando si scelgono i colori, provare a evitare una tavolozza difficile da distinguere per gli utenti con la cecità dei colori.

### <a name="captioning-for-video-text-for-audio"></a>Didascalia per video, testo per audio

Fornire didascalie per il contenuto video e uno script leggibile per il contenuto audio. È anche utile fornire controlli che regolano la velocità del contenuto audio o video e garantire che i pulsanti volume e play/pause siano facili da trovare e usare.

### <a name="localize"></a>Localize

Le descrizioni di accessibilità possono e devono essere localizzate in cui l'applicazione supporta più lingue.



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità Android](~/android/app-fundamentals/accessibility.md)
- [Accessibilità iOS](~/ios/app-fundamentals/accessibility.md)
- [Accessibilità OS X](~/mac/app-fundamentals/accessibility.md)
- [Accessibilità di Novell. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
