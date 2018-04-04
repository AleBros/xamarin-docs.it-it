---
title: Scheda Layout con TabHost
description: In questo articolo verrà fornita una panoramica di alto livello dell'il TabHost, un'API precedente usato per creare un layout a schede in un'applicazione di xamarin.
ms.prod: xamarin
ms.assetid: 77B890A4-27A6-41DF-81BA-22C6116A8FB2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: ae5b9020e08575bcd453703f3df14f63b288d2f5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="tab-layout-with-tabhost"></a>Scheda Layout con TabHost

_In questo articolo verrà fornita una panoramica di alto livello dell'il TabHost, un'API precedente usato per creare un layout a schede in un'applicazione di xamarin._


## <a name="overview"></a>Panoramica

> [!NOTE]
> `TabHost` è un'API precedente che è stata deprecata da Google. Gli sviluppatori sono invitati a compilare le applicazioni a schede utilizzando il [ActionBar](~/android/user-interface/controls/action-bar.md). Il `ActionBar` è disponibile in tutte le versioni di Android. È stata introdotta in Android 3.0 (livello API 11) e nuovamente è stato trasferito per Android 2.2 (livello API 8) e Android 2.3 (livello API 10) nei [V7 delle applicazioni libreria](http://developer.android.com/tools/support-library/features.html#v7-appcompat), che è disponibile per xamarin tramite il [Xamarin Libreria di supporto Android - V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto.

Il `TabHost` è l'API originale precedente per la creazione di un utente a schede interfacesIt è più adatta per le applicazioni che devono supportare Android 2.2 e 2.3 Android e non è possibile utilizzare xamarin **ActionBarSherlock**.
I seguenti cinque componenti sono tutti aspetti con il `TabHost` API:

-  **TabActivity** &ndash; si tratta di una vista specializzata che funge da contenitore per un `TabHost` (descritte di seguito).

-  **TabHost** &ndash; si tratta di un contenitore per l'interfaccia a schede. Include due elementi figlio: un elenco delle etichette delle schede e un `FrameLayout` che consente di visualizzare il contenuto della scheda.

-  **TabWidget** &ndash; questo controllo viene visualizzato un elenco delle etichette delle schede, una per ogni scheda di `TabHost` . Ogni scheda in un `TabHost` sono rappresentate da un `TabHost.TabSpec` oggetto. Questo oggetto contiene i metadati per ogni scheda. Quando l'utente seleziona una scheda, il `TabHost` risponde alla selezione visualizzando la scheda appropriata.

-  **FrameLayout** &ndash; un'interfaccia a schede deve avere un `FrameLayout` contenuti all'interno di un `TabHost`. Consente di visualizzare il contenuto per la scheda.

-  **Le attività o viste** &ndash; quando viene selezionata una scheda, viene visualizzata un'attività o una vista nel `FrameLayout`.

Il diagramma seguente mostra come tutti questi componenti correlati tra loro:

![Diagramma che illustra il Layout di Frame all'interno di TabWidget all'interno di TabHost](tab-host-images/image03.png)

Il contenuto della scheda potrebbe essere attività o viste. Le visualizzazioni sono relativamente leggera e semplice, ma possono comportare una notevole quantità di codice non correlato co-habitating nell'attività. Il risultato sarà una scarsa separazione delle problematiche e una classe di diventare difficile da gestire. Al contrario, le attività richiedono risorse di sistema, ma consentono un approccio più modulare con la logica per ogni scheda incapsulato nella classe distinct proprio.


## <a name="summary"></a>Riepilogo

Questo articolo ha descritto i componenti di alto livelli delle versioni precedenti `TabHost` API per Android e tutte le correlazioni tra loro.



## <a name="related-links"></a>Collegamenti correlati

- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabHost.TabSpec](https://developer.xamarin.com/api/type/Android.Widget.TabHost+TabSpec/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacchetto NuGet di supporto Android libreria v7 delle applicazioni](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria delle applicazioni V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
