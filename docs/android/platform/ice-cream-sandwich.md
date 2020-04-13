---
title: Ice Cream Sandwich Caratteristiche
description: Questo articolo descrive alcune delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API Android 4 - Ice Cream Sandwich. Copre diverse nuove tecnologie dell'interfaccia utente e quindi esamina una serie di nuove funzionalità che Android 4 offre per la condivisione dei dati tra le applicazioni e tra i dispositivi.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4fbbe1bec317e66166d5218ef0ed54247aa4f6dd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292664"
---
# <a name="ice-cream-sandwich-features"></a>Ice Cream Sandwich Caratteristiche

_Questo articolo descrive alcune delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API Android 4 - Ice Cream Sandwich. Copre diverse nuove tecnologie dell'interfaccia utente e quindi esamina una serie di nuove funzionalità che Android 4 offre per la condivisione dei dati tra le applicazioni e tra i dispositivi._

## <a name="overview"></a>Panoramica

Android OS versione 4.0 (livello API 14) rappresenta una rielaborazione importante del sistema operativo Android e include una serie di importanti modifiche e aggiornamenti, tra cui:

- **Interfaccia utente aggiornata:** diverse nuove funzionalità dell'interfaccia utente offrono agli sviluppatori maggiore potenza e flessibilità quando creano interfacce utente dell'applicazione. Queste nuove funzionalità `GridLayout` `PopupMenu` includono: , , `Switch` widget e `TextureView` .
- **Migliore accelerazione hardware** – il rendering 2D ora si svolge sulla GPU per tutti i controlli Android. Inoltre, l'accelerazione hardware è attivata, per impostazione predefinita, in tutte le applicazioni sviluppate per Android 4.0.
- **Nuove API** per i dati: è disponibile un nuovo accesso ai dati che in precedenza non erano ufficialmente accessibili, ad esempio i dati del calendario e il profilo utente del proprietario del dispositivo.
- **Condivisione dei dati** delle app – La condivisione dei `ShareActionProvider` dati tra applicazioni e dispositivi è ora più facile che mai tramite tecnologie come la , che rende facile creare un'azione di condivisione da una barra delle azioni, e *Android Beam* per Near Field *Communications (NFC),* il che rende un gioco da ragazzi condividere i dati tra i dispositivi in stretta vicinanza tra loro.

In questo articolo, esploreremo queste funzionalità e altre modifiche apportate all'API Android 4.0 e spiegheremo come usare ogni funzionalità con Xamarin.Android.

## <a name="user-interface-features"></a>Funzionalità dell'interfaccia utente

Con Android 4 sono disponibili una varietà di nuove tecnologie di interfaccia utente, tra cui:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** – Supporta il layout di griglia 2D dei controlli.
- **[Cambia widget](~/android/user-interface/controls/switch.md)** – Consente di alternare tra ON o OFF.
- **[TextureView:](~/android/user-interface/controls/texture-view.md)** abilita il video e il contenuto OpenGL all'interno di una visualizzazione.
- **[Barra di navigazione](~/android/user-interface/controls/navigation-bar.md)** – Contiene i pulsanti virtuali per la schiena, casa, e multi-tasking.

Inoltre, sono stati migliorati altri elementi `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`dell'interfaccia utente, ad esempio , che ora è più facile da usare, e schede, che hanno un aspetto più raffinato.

## <a name="sharing-features"></a>Condivisione delle funzioni

Android 4 include diverse nuove tecnologie che ci consentono di condividere i dati tra dispositivi e applicazioni. Fornisce inoltre l'accesso a vari tipi di dati che in precedenza non erano disponibili, ad esempio le informazioni del calendario e il profilo utente del proprietario del dispositivo. In questa sezione esamineremo una varietà di funzionalità offerte da Android 4 che affrontano queste aree, tra cui:

- **[Android Beam](~/android/platform/android-beam.md)** – Consente la condivisione dei dati tramite NFC.
- **[ShareActionProvider:](~/android/user-interface/controls/action-bar.md)** crea un provider che consente agli sviluppatori di specificare le azioni di condivisione dalla barra delle azioni.
- **[Profilo utente](~/android/user-interface/user-profile.md)** – Fornisce l'accesso ai dati del profilo del proprietario del dispositivo.
- **[API calendario:](~/android/user-interface/controls/calendar.md)** fornisce l'accesso ai dati del calendario dal provider di calendario.

## <a name="x86-emulators"></a>Emulatori x86

ICS non supporta ancora lo sviluppo con un emulatore x86. Gli emulatori x86 sono supportati solo con Android 2.3.3, livello API 10. Per ulteriori informazioni, vedere [Configurazione dell'emulatore x86.](~/android/get-started/installation/android-emulator/index.md)

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato una varietà di nuove tecnologie che sono ora disponibili con Android 4. Sono stati esaminate le nuove funzionalità dell'interfaccia utente, ad esempio il *controllo GridLayout*, *PopupMenu*e *Switch* . Abbiamo anche esaminato alcuni dei nuovi supporti per il controllo dell'interfaccia utente del sistema, nonché come utilizzare il *TextureView*. Poi abbiamo discusso una varietà di nuove tecnologie di condivisione. È stato illustrato come *Android Beam* consente di condividere le informazioni tra i dispositivi che utilizzano *NFC*, discusso la nuova *API calendario*e anche illustrato come utilizzare il built in *ShareActionProvider*.
Infine, è stato esaminato come utilizzare il *ContactsContract* provider per accedere ai dati del profilo utente.

## <a name="related-links"></a>Collegamenti correlati

- [TextureViewDemo (esempio)TextureViewDemo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (esempio)CalendarDemo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Esercitazione sul layout delle schede](~/android/user-interface/layouts/tab-layout/index.md)
- [Sandwich gelato](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Piattaforma Android 4.0](https://developer.android.com/about/versions/android-4.0.html)
