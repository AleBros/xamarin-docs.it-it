---
title: Funzionalità di Ice Cream Sandwich
description: Questo articolo descrive alcune delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API Android 4-Ice Cream Sandwich. Illustra diverse nuove tecnologie dell'interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità offerte da Android 4 per la condivisione di dati tra le applicazioni e tra dispositivi.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 382315f755102d7111db1a5c0f71d43bdea97a10
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020176"
---
# <a name="ice-cream-sandwich-features"></a>Funzionalità di Ice Cream Sandwich

_Questo articolo descrive alcune delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con l'API Android 4-Ice Cream Sandwich. Illustra diverse nuove tecnologie dell'interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità offerte da Android 4 per la condivisione di dati tra le applicazioni e tra dispositivi._

## <a name="overview"></a>Panoramica

Il sistema operativo Android versione 4,0 (livello API 14) rappresenta una maggiore rielaborazione del sistema operativo Android e include una serie di importanti modifiche e aggiornamenti, tra cui:

- **Interfaccia utente aggiornata** : alcune nuove funzionalità dell'interfaccia utente offrono agli sviluppatori maggiore potenza e flessibilità durante la creazione di interfacce utente dell'applicazione. Queste nuove funzionalità includono: `GridLayout`, `PopupMenu`, `Switch` widget e `TextureView`. 
- **Accelerazione hardware migliorata** : il rendering 2D viene ora eseguito sulla GPU per tutti i controlli Android. Inoltre, l'accelerazione hardware è abilitata per impostazione predefinita in tutte le applicazioni sviluppate per Android 4,0. 
- **Nuove API di dati** : è disponibile un nuovo accesso ai dati che non erano precedentemente accessibili ufficialmente, ad esempio i dati del calendario e il profilo utente del proprietario del dispositivo. 
- **Condivisione dei dati delle app** : la condivisione di dati tra applicazioni e dispositivi è ora più semplice che mai tramite tecnologie come la `ShareActionProvider`, che semplifica la creazione di un'azione di condivisione da una barra delle azioni e *Android Beam* per le *comunicazioni Near Field ( NFC)* , che lo rende uno snap per condividere i dati tra i dispositivi in prossimità. 

In questo articolo verranno esaminate queste funzionalità e altre modifiche apportate all'API Android 4,0 e verrà illustrato come usare ogni funzionalità con Novell. Android.

## <a name="user-interface-features"></a>Funzionalità dell'interfaccia utente

Con Android 4 sono disponibili diverse nuove tecnologie dell'interfaccia utente, tra cui:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** : supporta il layout griglia 2D dei controlli. 
- **[Switch Widget](~/android/user-interface/controls/switch.md)** : consente di attivare o disattivare la selezione. 
- **[TextureView](~/android/user-interface/controls/texture-view.md)** : Abilita il contenuto video e OpenGL in una visualizzazione. 
- **[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)** : contiene i pulsanti virtuali per back, Home e multitasking. 

Sono stati inoltre migliorati gli altri elementi dell'interfaccia utente, ad esempio la `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, che ora è più facile da usare e le schede, che hanno un aspetto più lucido.

## <a name="sharing-features"></a>Funzionalità di condivisione

Android 4 include diverse nuove tecnologie che consentono di condividere i dati tra i dispositivi e tra le applicazioni. Fornisce inoltre l'accesso a diversi tipi di dati non disponibili in precedenza, ad esempio le informazioni sul calendario e il profilo utente del proprietario del dispositivo. In questa sezione verrà esaminata una serie di funzionalità offerte da Android 4 che si rivolgono a queste aree, tra cui:

- **[Android Beam](~/android/platform/android-beam.md)** : consente la condivisione dei dati tramite NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** : crea un provider che consente agli sviluppatori di specificare le azioni di condivisione dalla barra delle azioni. 
- **[Profilo utente](~/android/user-interface/user-profile.md)** : consente di accedere ai dati del profilo del proprietario del dispositivo. 
- **[API Calendar](~/android/user-interface/controls/calendar.md)** : consente di accedere ai dati del calendario dal provider del calendario. 

## <a name="x86-emulators"></a>Emulatori x86

ICS non supporta ancora lo sviluppo con un emulatore x86. gli emulatori x86 sono supportati solo con Android 2.3.3, API level 10. Per ulteriori informazioni, vedere [configurazione dell'emulatore x86](~/android/get-started/installation/android-emulator/index.md) .

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato una serie di nuove tecnologie disponibili con Android 4. Sono state rivedute le nuove funzionalità dell'interfaccia utente, ad esempio *GridLayout*, *PopupMenu*e *Switch* widget. È stato anche esaminato un nuovo supporto per il controllo dell'interfaccia utente del sistema, oltre a come usare *TextureView*. È stata quindi discussa una serie di nuove tecnologie di condivisione. È stato illustrato il modo in cui *Android Beam* consente di condividere le informazioni tra i dispositivi che usano *NFC*, ha illustrato la nuova *API Calendar*e illustrato come usare il *ShareActionProvider*incorporato.
Infine, è stato esaminato come utilizzare il provider *ContactsContract* per accedere ai dati del profilo utente.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Ice Cream Sandwich](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-ics-samples)
- [TextureViewDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Esercitazione sul layout a schede](~/android/user-interface/layouts/tab-layout/index.md)
- [Ice Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Piattaforma Android 4,0](https://developer.android.com/about/versions/android-4.0.html)
