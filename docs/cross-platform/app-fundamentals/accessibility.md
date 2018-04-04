---
title: Accessibilità
description: Verificare che le app siano utilizzabile per la più ampia possibile di destinatari
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 3b7912f7875e9d07de51861e573065b3d1b73de0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility"></a>Accessibilità

_Verificare che le app siano utilizzabile per la più ampia possibile di destinatari_

Accessibilità si intende il concetto di progettazione di interfacce utente di app che funzionano anche funzionalità di assistenza di input e di visualizzazione del sistema operativo, ad esempio tipi di grandi dimensioni, contrasto elevato, eseguire lo zoom avanti, la lettura dello schermo (sintesi vocale), segnali feedback visivo o tattili, e metodi di input alternativi.

Desktop e mobile piattaforme come iOS, Android e Windows offrono compilato nelle API che consentono agli sviluppatori di compilare App accessibile, ad esempio [Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) e [Voice over Apple](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API specifiche della piattaforma

Per implementare le indicazioni in questo documento, usare le API fornite da ogni piattaforma:

- [**Accessibilità Android**](~/android/app-fundamentals/accessibility.md)
- [**iOS accessibilità**](~/ios/app-fundamentals/accessibility.md)
- [**Accesso facilitato di OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Elenco di controllo di accesso facilitato

Seguire questi suggerimenti per verificare che le app siano accessibili al gruppo di destinatari più ampio possibile. Estrarre il [Android accessibilità test Checklist](http://developer.android.com/training/accessibility/testing.html) e [pagina di accesso facilitato di Apple](http://www.apple.com/accessibility/) per ulteriori informazioni.

### <a name="support-large-fonts-and-high-contrast"></a>Supporta i tipi di carattere di grandi dimensioni e contrasto elevato

Evitare dimensioni del controllo a livello di codice e, invece, preferire layout che è possibile ridimensionare per contenere i caratteri di dimensioni maggiori.
Testare le combinazioni di colori nella modalità a contrasto elevato per verificare che siano leggibili.

### <a name="make-the-user-interface-self-describing"></a>Verificare che l'utente interfaccia autodescrittivi

Contrassegnare tutti gli elementi dell'interfaccia utente con un testo descrittivo e gli hint che sono compatibili con la schermata lettura API in ciascuna piattaforma.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Verificare che le immagini e icone abbiano una descrizione di testo alternativo

Immagini e icone che fanno parte dell'interfaccia utente dell'applicazione, quali pulsanti o indicatori di stato, ad esempio, devono essere contrassegnate con una descrizione accessibile.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>La struttura ad albero visuale con navigazione accessibile in considerazione di progettazione

Utilizzare i controlli di layout appropriato o le API in modo che la navigazione tra i controlli usando metodi alternativi di input seguono lo stesso flusso logico come usando il touchscreen.

Escludere gli elementi non necessari dalla lettura dello schermo (decorative immagini o le etichette per i campi che sono già accessibili, ad esempio).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Non affidarsi solo segnali audio o di colore

Evitare situazioni in cui l'unica indicazione di stato di avanzamento, completamento o un altro stato è un suono o la modifica del colore. La progettazione dell'interfaccia utente includono segnali visivi deselezionare (con audio e il colore per amplificazione solo) o aggiungere indicatori accessibilità specifico.

Quando si scelgono colori, tentare di evitare una tavolozza che è difficile distinguere cecità colore per gli utenti.

### <a name="captioning-for-video-text-for-audio"></a>Le didascalie per testo video, audio

Fornire le didascalie per contenuto video e uno script leggibile per il contenuto audio. È inoltre utile fornire i controlli che regolare la velocità del contenuto audio o video e assicurarsi che il volume e sono facili da trovare e utilizzare i pulsanti di Riproduci/Pausa.

### <a name="localize"></a>Localize

Le descrizioni di accessibilità può (e deve) essere localizzate in cui l'applicazione supporta più lingue.



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità Android](~/android/app-fundamentals/accessibility.md)
- [iOS accessibilità](~/ios/app-fundamentals/accessibility.md)
- [Accesso facilitato di OS X](~/mac/app-fundamentals/accessibility.md)
- [Accessibilità xamarin. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
