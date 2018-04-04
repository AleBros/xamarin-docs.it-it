---
title: Layout a schede
description: Una panoramica di layout a schede in Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/23/2017
ms.openlocfilehash: 4095944bb630637a2e761af18796dacdef17785c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="tabbed-layouts"></a>Layout a schede


## <a name="overview"></a>Panoramica

Le schede sono un modello di interfaccia utente più comunemente usati in applicazioni per dispositivi mobili a causa delle loro semplicità e usabilità. Forniscono un modo semplice e coerenza per spostarsi tra le schermate diverse in un'applicazione. Android ha diverse API per interfacce a schede: 

-   **TabHost** &ndash; è l'API originale per la creazione di interfacce utente a schede. Oggetto `TabHost` widget viene aggiunto a un layout e funge da contenitore per le viste a schede. Questa API poiché è stata deprecata e si sconsiglia di utilizzo. 

-   **ActionBar** &ndash; questo fa parte di un nuovo set di API che è stata introdotta in Android 3.0 (livello API 11) con l'obiettivo di fornire un coerente interfaccia cambio di visualizzazione e navigazione. È stata portata nuovamente per Android 2.2 (livello API 8) con il [libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica le pagine corrente, successiva e precedente di un `ViewPager`. `ViewPager` è disponibile solo tramite [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Per ulteriori informazioni su `PagerTabStrip`, vedere [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra degli strumenti** &ndash; `Toolbar` è un componente barra azione più recenti e più flessibile che sostituisce `ActionBar`. `Toolbar` è disponibile in Android simbolo 5.0 o versioni successive ed è anche disponibile per le versioni precedenti di Android tramite il [libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto NuGet. 
    `Toolbar` è attualmente il componente della barra azione consigliata da usare in App per Android.
    Per ulteriori informazioni, vedere [barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 


Queste API sono visivamente molto diverse e non sono compatibili tra loro. La seguente schermata Mostra immagine `TabHost` e `ActionBar` side-by-side: 

![Schermate di TabHost a sinistra e ActionBar a destra](images/image01.png)

Queste API non compatibile presenti a causa di modifiche significative di interfaccia utente poiché Android 3.0 (livello API 11). Una di queste modifiche all'interfaccia utente è stata la [azione barra progettuale](http://www.androidpatterns.com/uap_pattern/action-bar), un modello destinato fornire facile accesso alle funzionalità di navigazione e la chiave in un'applicazione. Il `ActionBar` API è stata introdotta per supportare questo pattern. 

Il `ActionBar` API è più semplice e senza dubbio offre una migliore esperienza utente. Questa è stata portata nuovamente in Android 2.2 ed è la scelta migliore per le applicazioni di xamarin. 

Il `TabHost` API compatibile tra tutte le versioni di Android, ma richiede maggiori interventi a utilizzare e non è coerenza con l'oggetto corrente [linee guida dell'interfaccia utente Android](http://developer.android.com/design/index.html). Gli sviluppatori siano di usare questa API e dovrebbero favorire la ActionBar più recenti per le applicazioni di xamarin. 



## <a name="actionbarsherlock"></a>ActionBarSherlock

Prima dell'API ActionBar backported per Android 2.2, gli sviluppatori che si desidera che l'aspetto più recente dell'API ActionBar ma può utilizzare una libreria di terze parti, [ActionBarSherlock](http://actionbarsherlock.com). ActionBarSherlock è che un'estensione della libreria di supporto Android progettato per backport il modello di progettazione della barra azione per Android 2. x. Quando si esegue in Android 3.0 o versione successiva, ActionBarSherlock utilizzeranno automaticamente l'oggetto nativo `ActionBar` API fornita da Android. Le versioni precedenti di Android utilizzerà un'implementazione personalizzata che imitino l'aspetto di quelle più recenti `ActionBar` API. Il [ActionBarSherlock componente](https://www.nuget.org/packages/xamstore-XamarinActionBarSherlock/) rende facile aggiungere ActionBarSherlock a un'applicazione di xamarin. 



## <a name="related-links"></a>Collegamenti correlati

- [Panoramica di TabHost](tab-host.md)
- [Procedura dettagliata TabHost](~/android/user-interface/layouts/tab-layout/creating-a-tabbed-ui.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacchetto NuGet di supporto Android libreria v7 delle applicazioni](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria delle applicazioni V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
