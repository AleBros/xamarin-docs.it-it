---
title: Funzionalità di gelato Sandwich
description: In questo articolo vengono descritte molte delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API Android di 4 - Sandwich gelato. Vengono illustrate diverse nuove tecnologie di interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità che offre aggiornamenti per la condivisione dei dati tra le applicazioni e tra i dispositivi Android 4.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 3c5412629f6dcd31fb0a82634ef530569eef459a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764931"
---
# <a name="ice-cream-sandwich-features"></a>Funzionalità di gelato Sandwich

_In questo articolo vengono descritte molte delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API Android di 4 - Sandwich gelato. Vengono illustrate diverse nuove tecnologie di interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità che offre aggiornamenti per la condivisione dei dati tra le applicazioni e tra i dispositivi Android 4._

## <a name="overview"></a>Panoramica

Sistema operativo Android versione 4.0 (API livello 14) rappresenta un principale alla rielaborazione del sistema operativo Android e include una serie di importanti modifiche e aggiornamenti, tra cui:

-   **L'interfaccia utente aggiornata** : numerose nuove funzionalità dell'interfaccia utente offrono agli sviluppatori di potenza e flessibilità durante la creazione di utenti dell'applicazione di interfacce. Queste nuove funzionalità includono: `GridLayout` , `PopupMenu` , `Switch` widget e `TextureView` . 
-   **Accelerazione Hardware migliori** : 2D ora viene eseguita nella GPU per Android tutti i controlli per il rendering. Inoltre, l'accelerazione hardware è on, per impostazione predefinita, in tutte le applicazioni sviluppate per Android 4.0. 
-   **Nuove API di dati** – è di nuovo l'accesso ai dati che non era in precedenza ufficialmente accessibili, ad esempio i dati del calendario e il profilo utente del proprietario del dispositivo. 
-   **App di condivisione dei dati** : condivisione dei dati tra applicazioni e dispositivi è ora più semplice che mai tramite tecnologie quali la `ShareActionProvider` , che rende più facile creare un'azione di condivisione da una barra delle azioni e *Trasmetti Android* per *Communications (prossimità)* , che rende un gioco da ragazzi per condividere dati tra dispositivi in prossimità tra loro. 


In questo articolo si intende esplorare queste funzionalità e altre modifiche che sono state apportate per l'API di Android 4.0 e verrà illustrato come utilizzare ogni funzionalità con xamarin.

## <a name="user-interface-features"></a>Funzionalità interfaccia utente

Un'ampia gamma di nuove tecnologie di interfaccia utente sono disponibili con Android 4, tra cui:

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  : layout di griglia 2D supporta dei controlli. 
-   **[Passare il widget](~/android/user-interface/controls/switch.md)**  : consente di alternare ON oppure OFF. 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  : consente di video e contenuto OpenGL all'interno di una vista. 
-   **[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)**  – contiene pulsanti virtuali per il backup, home e multitasking. 


Inoltre, sono stati migliorati altri elementi dell'interfaccia utente, ad esempio il `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, che è ora più facile lavorare con e schede che hanno un aspetto più elegante.

## <a name="sharing-features"></a>Funzionalità di condivisione

Android 4 include molte nuove tecnologie che consentono di condividere dati tra i dispositivi e tutte le applicazioni. Fornisce inoltre l'accesso a diversi tipi di dati non disponibili in precedenza, ad esempio le informazioni del calendario e profili utente del proprietario del dispositivo. In questa sezione che si esamineranno un'ampia gamma di funzionalità offerte da Android 4 tale indirizzo queste aree inclusi:

-  **[Trasmetti Android](~/android/platform/android-beam.md)**  : consente di condivisione dei dati attraverso NFC.
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  : crea un provider che consente agli sviluppatori di specificare azioni condivisione dalla barra delle azioni. 
-   **[Profilo utente](~/android/user-interface/user-profile.md)**  : fornisce l'accesso ai dati di profilo del proprietario del dispositivo. 
-   **[API del calendario](~/android/user-interface/controls/calendar.md)**  : fornisce l'accesso ai dati di calendario dal provider di calendario. 

## <a name="x86-emulators"></a>x86 emulatori

Condivisione connessione Internet non supporta ancora lo sviluppo con x86 emulatore. x86 emulatori sono supportati solo con Android 2.3.3, il livello API 10. Vedere [configurazione x86 Emulator](~/android/get-started/installation/android-emulator/index.md) per ulteriori informazioni.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrata un'ampia gamma di nuove tecnologie che sono ora disponibili con Android 4. È stata esaminata la nuove funzionalità dell'interfaccia utente, ad esempio il *GridLayout*, *PopupMenu*, e *commutatore* widget. È inoltre stato esaminato tra il nuovo supporto per il controllo del sistema, e come utilizzare il *TextureView*. Quindi, abbiamo parlato un'ampia gamma di nuove tecnologie di condivisione. Sono trattati come *Trasmetti Android* consente condividere informazioni tra i dispositivi che utilizzano *NFC*, illustrato il nuovo *API del calendario*e viene inoltre illustrato come utilizzare incorporato in  *ShareActionProvider*.
Infine, viene esaminato come utilizzare il *ContactsContract* provider per accedere ai dati di profilo utente.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di gelato Sandwich](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo (sample)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo (esempio)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Scheda Layout esercitazione](~/android/user-interface/layouts/tab-layout/index.md)
- [Gelato Sandwich](http://developer.android.com/about/versions/android-4.0-highlights.html)
- [Piattaforma 4.0 Android](http://developer.android.com/about/versions/android-4.0.html)
