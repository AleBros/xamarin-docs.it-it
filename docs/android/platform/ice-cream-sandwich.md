---
title: ICE Cream Sandwich funzionalità
description: Questo articolo vengono illustrate alcune delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API di 4 - Ice Cream Sandwich Android. Illustra diverse nuove tecnologie di interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità che offre la condivisione dei dati tra applicazioni e tra i dispositivi Android 4.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: adb53af9d2e6707cb1fca3c59af63db76e5346d5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102620"
---
# <a name="ice-cream-sandwich-features"></a>ICE Cream Sandwich funzionalità

_Questo articolo vengono illustrate alcune delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API di 4 - Ice Cream Sandwich Android. Illustra diverse nuove tecnologie di interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità che offre la condivisione dei dati tra applicazioni e tra i dispositivi Android 4._

## <a name="overview"></a>Panoramica

Sistema operativo Android versione 4.0 (API livello 14) rappresenta un importante alla rielaborazione del sistema operativo Android e include una serie di importanti modifiche e gli aggiornamenti, tra cui:

-   **L'interfaccia utente aggiornata** : alcune nuove funzionalità dell'interfaccia utente offrono agli sviluppatori di potenza e flessibilità durante la creazione di utenti dell'applicazione di interfacce. Queste nuove funzionalità includono: `GridLayout` , `PopupMenu` , `Switch` widget e `TextureView` . 
-   **Meglio l'accelerazione Hardware** : ora viene eseguita nella GPU per tutte le Android controlli per il rendering 2D. Inoltre, l'accelerazione hardware è on, per impostazione predefinita, in tutte le applicazioni sviluppate per Android 4.0. 
-   **Nuove API dati** – nuovo accesso ai dati che non era in precedenza ufficialmente accessibili, ad esempio i dati del calendario e il profilo utente del proprietario del dispositivo. 
-   **Condivisione dei dati delle app** – la condivisione dei dati tra applicazioni e dispositivi è ora più facile che mai tramite le tecnologie, ad esempio il `ShareActionProvider` , rendendo più semplice creare un'azione di condivisione da una barra delle azioni, e *Trasmetti Android* per la *quasi campo Communications (NFC)* , che rende un gioco da ragazzi per condividere dati tra dispositivi vicine tra loro. 


In questo articolo, dobbiamo esaminare queste funzionalità e altre modifiche che sono state apportate all'API di Android 4.0 e verrà illustrato come usare ciascuna funzionalità con xamarin. Android.

## <a name="user-interface-features"></a>Funzionalità dell'interfaccia utente

Sono disponibili con Android 4 e include un'ampia gamma di nuove tecnologie di interfaccia utente:

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  – layout griglia 2D supporta dei controlli. 
-   **[Passare widget](~/android/user-interface/controls/switch.md)**  : consente di disattivazione/attivazione tra ON oppure OFF. 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – consente a video e contenuto OpenGL all'interno di una vista. 
-   **[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)**  – contiene pulsanti virtuali per il backup, home e multitasking. 


Inoltre, sono stati migliorati gli altri elementi dell'interfaccia utente, ad esempio il `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, che è ora più facile lavorare con e schede che hanno un aspetto più elegante.

## <a name="sharing-features"></a>Le funzionalità di condivisione

Android 4 include molte nuove tecnologie che consentono di condividere dati tra dispositivi e tutte le applicazioni. Fornisce inoltre l'accesso a vari tipi di dati che non erano disponibili in precedenza, ad esempio le informazioni del calendario e profili utente del proprietario del dispositivo. In questa sezione che esamineremo un'ampia gamma di funzionalità offerte da Android 4 quell'indirizzo queste aree tra cui:

-  **[Android Trasmetti](~/android/platform/android-beam.md)**  : dati consente la condivisione tramite NFC.
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  : crea un provider che consente agli sviluppatori di specificare azioni condivisione dalla barra delle azioni. 
-   **[Profilo utente](~/android/user-interface/user-profile.md)**  : fornisce l'accesso ai dati di profilo del proprietario del dispositivo. 
-   **[API del calendario](~/android/user-interface/controls/calendar.md)**  : fornisce l'accesso ai dati di calendario dal provider di calendario. 

## <a name="x86-emulators"></a>x86 emulatori

Condivisione connessione Internet non supporta ancora lo sviluppo con un x86 dell'emulatore. x86 emulatori sono supportati solo con Android 2.3.3, il livello API 10. Visualizzare [configurazione x86 Emulator](~/android/get-started/installation/android-emulator/index.md) per altre informazioni.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato numerose nuove tecnologie che sono ora disponibili con Android 4. Abbiamo esaminato nuove funzionalità dell'interfaccia utente, ad esempio la *GridLayout*, *PopupMenu*, e *commutatore* widget. È inoltre esaminate alcune del nuovo supporto per il controllo dell'interfaccia utente, il sistema e viene descritto come usare il *TextureView*. Quindi abbiamo discusso un'ampia gamma di nuove tecnologie di condivisione. Sono trattati come *Trasmetti Android* è possibile condividere informazioni tra i dispositivi che usano *NFC*, illustrato il nuovo *calendario API*e ha anche illustrato come usare incorporato in  *ShareActionProvider*.
Infine, abbiamo esaminato come utilizzare il *ContactsContract* provider per accedere ai dati di profilo utente.



## <a name="related-links"></a>Collegamenti correlati

- [ICE Cream Sandwich esempi](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo (esempio)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo (esempio)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Scheda Layout esercitazione](~/android/user-interface/layouts/tab-layout/index.md)
- [ICE Cream Sandwich](http://developer.android.com/about/versions/android-4.0-highlights.html)
- [Piattaforma Android 4.0](http://developer.android.com/about/versions/android-4.0.html)
