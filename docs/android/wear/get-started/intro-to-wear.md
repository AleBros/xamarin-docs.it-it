---
title: Introduzione ad Android Wear
description: Con l'introduzione di Google Android Wear, non sono più limitate solo telefoni e Tablet per lo sviluppo di App Android straordinarie. Supporto di xamarin. Android per Android Wear rende possibile l'esecuzione di C# codice su in alcun modo. Questa introduzione viene fornita una panoramica di base di Android Wear, descrive le funzionalità principali e offre una panoramica delle funzionalità disponibili in Android Wear 2.0. Vengono elencati alcuni dei più diffusi dispositivi Android Wear e vengono forniti collegamenti alla documentazione di Google Android Wear essenziale per un approfondimento.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a35cb82f4f6d20e91f45a782c73d3ef811947c3a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61284250"
---
# <a name="introduction-to-android-wear"></a>Introduzione ad Android Wear

_Con l'introduzione di Google Android Wear, non sono più limitate solo telefoni e Tablet per lo sviluppo di App Android straordinarie. Supporto di xamarin. Android per Android Wear rende possibile l'esecuzione di C# codice su in alcun modo. Questa introduzione viene fornita una panoramica di base di Android Wear, descrive le funzionalità principali e offre una panoramica delle funzionalità disponibili in Android Wear 2.0. Vengono elencati alcuni dei più diffusi dispositivi Android Wear e vengono forniti collegamenti alla documentazione di Google Android Wear essenziale per un approfondimento._


## <a name="overview"></a>Panoramica

Android Wear viene eseguito su un'ampia gamma di dispositivi, tra cui la prima generazione 360 Motorola watch G del generatore di carico e in tempo reale a forma di ingranaggio Samsung. Una seconda generazione, tra cui Sony SmartWatch 3, è stata rilasciata anche con funzionalità aggiuntive, tra cui GPS predefiniti e la riproduzione di musica offline. Per Android Wear 2.0, Google ha collaborato con Generatore di carico per due nuove espressioni di controllo: lo Sport Watch LG e lo stile delle espressioni di controllo generatore di carico.

![I dispositivi Android Wear 2.0](intro-to-wear-images/hero-image.png "dispositivi esempio Android Wear 2.0")

Supporto di xamarin. Android 5.0 e versioni successive supporta Android Wear tramite nostro 4.4W Android (API 20) e controlli dell'interfaccia utente Wear specifici di un pacchetto NuGet che aggiunge altro. Xamarin. Android 5.0 e versioni successive include anche funzionalità per la creazione di pacchetti delle App Wear. I pacchetti NuGet sono anche disponibili per Android 2.0 Wear come descritto più avanti in questa Guida.


## <a name="android-wear-basics"></a>Nozioni di base di Android Wear

Android Wear dispone di un paradigma dell'interfaccia utente diversa da quella delle App Android palmari. Nel primo gruppo di App Wear sono stati progettati per estendere un companion App Wear, palmari app in un modo, ma che inizia con Android Wear 2.0 può essere utilizzato autonomo. Quando si distribuisce un'app per Wear, lo strumento è incluso con un'app portatile complementare. Poiché la maggior parte Wear App dipendono da un'app complementare palmari, devono trovare un modo per comunicare con le app palmari. Le sezioni seguenti descrivono questi scenari di utilizzo e indicano le funzionalità essenziali di Android Wear. 



### <a name="usage-scenarios"></a>Utilizzo di scenari

La prima versione di Android Wear era incentrata principalmente sull'estensione delle applicazioni palmari corrente con le notifiche migliorate e la sincronizzazione dei dati tra l'app palmare e l'app indossabile. Di conseguenza, questi scenari sono relativamente semplici da implementare.


#### <a name="wearable-notifications"></a>Notifiche indossabile

Il modo più semplice per supportare Android Wear è sfruttare la natura condivisa delle modifiche tra il portatile e il dispositivo indossabile. Tramite l'API di notifica v4 supporto e il `WearableExtender` classe (disponibile nel [Xamarin Android Support Library](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), è possibile sfruttare le funzionalità native della piattaforma, ad esempio le schede lo stile della posta in arrivo o input vocale. Il [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) esempio fornisce il codice di esempio che illustra come inviare un elenco di notifiche a un dispositivo Android Wear. 



#### <a name="companion-applications"></a>Applicazioni complementari

Un'altra strategia consiste nel creare un'applicazione completa che viene eseguito in modalità nativa nel dispositivo indossabile e coppie con un'app portatile complementare. Un buon esempio di questo approccio è il [Quiz](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) app di esempio che illustra come creare un quiz eseguito in un dispositivo palmare che vengono poste delle domande quiz sul dispositivo indossabile. 



### <a name="user-interface"></a>Interfaccia utente

Il modello di navigazione principale per Wear è una serie di schede disposte verticalmente. Ognuna di queste schede può essere associate le azioni che si sovrappongono a orizzontale sulla stessa riga. Il `GridViewPager` classe fornisce questa funzionalità, è conforme allo stesso concetto adapter `ListView`. In genere si associa la `GridViewPager` con un `FragmentGridPagerAdaptor` (o `GridPagerAdaptor`) che consente di rappresentano le celle di ogni riga e colonna come una `Fragment`: 

[![Spostamento di usura](intro-to-wear-images/2d-picker-sml.png "Wear navigazione")](intro-to-wear-images/2d-picker.png#lightbox)

Wear inoltre semplifica l'utilizzo di pulsanti di azione costituiti da una grande colorate cerchio con testo della descrizione di piccole dimensioni sottostanti (come illustrato sopra).  Il [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) esempio di seguito viene illustrato come utilizzare `GridViewPager` e `GridPagerAdapter` in un'app per Wear.

Android Wear 2.0 aggiunge un menu di spostamento, un cassetto di azione e i pulsanti di azione inline all'interfaccia utente Wear. Per altre informazioni sugli elementi dell'interfaccia utente Android Wear 2.0, vedere l'Android [Anatomia](https://www.google.com/design/spec-wear/system-overview/anatomy.html) argomento. 



### <a name="communications"></a>Comunicazioni

Android Wear fornisce due API per semplificare le comunicazioni tra App indossabile e portatile complementare di comunicazione diversi: 

**Dati API** &ndash; questa API è simile a un archivio dati sincronizzati tra il dispositivo indossabile e il dispositivo portatile. Android si occupa di propagare le modifiche tra indossabile e portatile quando è ottimale per eseguire questa operazione. Quando l'indossabile è compreso nell'intervallo valido, inserisce in coda la sincronizzazione per un secondo momento. Il punto di ingresso principale per questa API è `WearableClass.DataApi`. Per altre informazioni su questa API, vedere l'Android [sincronizzazione elementi di dati](https://developer.android.com/training/wearables/data-layer/data-items.html) argomento. 

**API dei messaggi** &ndash; questa API consente di usare un percorso di comunicazione di livello inferiore: un piccolo payload viene inviato unidirezionale senza la sincronizzazione tra le app palmari e indossabile.
Il punto di ingresso principale per questa API è `WearableClass.MessageApi`.
Per altre informazioni su questa API, vedere l'Android [invio e ricezione di messaggi](https://developer.android.com/training/wearables/data-layer/messages.html) argomento.

È possibile scegliere di registrare i callback per ricevere tali messaggi tramite ognuna delle interfacce API del listener o, in alternativa, implementare un servizio nell'app che deriva da `WearableListenerService`.
Questo servizio verrà automaticamente creata un'istanza per Android Wear.
Il [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) esempio di seguito viene illustrato come implementare un `WearableListenerService`.



### <a name="deployment"></a>Distribuzione

Ogni app indossabile viene distribuito con il proprio file APK incorporato all'interno dell'applicazione principale APK. La creazione di pacchetti viene gestita automaticamente in xamarin. Android 5.0 e versioni successive, ma deve essere eseguito manualmente per le versioni di xamarin. Android precedenti alla versione 5.0. 
[Utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md) illustra la distribuzione in modo più dettagliato. 



## <a name="going-further"></a>Approfondimenti 

Il modo migliore per acquisire familiarità con Android Wear consiste nel compilare e testare la prima app. Nell'elenco seguente offre un ordine di letture consigliate che consentono di iniziare rapidamente:

1.  [Configurazione e installazione](~/android/wear/get-started/installation.md) vengono fornite istruzioni dettagliate per installare e configurare l'ambiente di sviluppo per la creazione di App xamarin. Android Wear. 

2.  Dopo aver installato i pacchetti necessari e configurato un emulatore o dispositivo, vedere [salve, Wear](~/android/wear/get-started/hello-wear.md) per istruzioni dettagliate che illustrano come creare un progetto Android Wear piccolo pulsante handle fa clic e visualizza un Fare clic sul contatore del dispositivo Wear. 

3.  [Distribuzione e test](~/android/wear/deploy-test/index.md) fornisce maggiori informazioni sulla configurazione e distribuzione in emulatori e dispositivi, incluse le istruzioni su come distribuire l'app in un dispositivo Wear tramite Bluetooth.

4.  [Lavorare con le dimensioni dello schermo](~/android/wear/screen-sizes.md) spiega come visualizzare in anteprima e ottimizzare l'interfaccia utente per le diverse dimensioni dello schermo disponibile nei dispositivi Wear. 

5.  [Utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md) vengono descritti i passaggi per la creazione manuale del pacchetto usura App per la distribuzione in Google Play.

Dopo aver creato la prima app Wear, è possibile provare a compilare una personalizzata watch face per Android Wear. 
[Creazione di una Watch Face](~/android/wear/platform/creating-a-watchface.md) fornisce istruzioni dettagliate e codici di esempio per lo sviluppo di un' servizio faccia watch digitale, seguito da altro codice che rende più efficace per un quadrante orologio analogico-style con funzionalità aggiuntive. 



## <a name="android-wear-20"></a>Android Wear 2.0

Android Wear 2.0 introduce un'ampia gamma di nuove caratteristiche e funzionalità, ad esempio *complicazioni*, curvi layout, i pannelli di navigazione e azioni e notifiche espanse. Inoltre, Wear 2.0 rende possibile per la creazione di App autonoma che funzionano indipendentemente dalle App palmari. Il nuovo *movimenti del polso* funzionalità consente di motori delle interazioni con l'app. Le sezioni seguenti vengono evidenziate queste funzionalità e offrono collegamenti che consentono di iniziare a farne uso nell'app.



### <a name="install-wear-20-packages"></a>Installazione Wear 2.0 pacchetti

Per compilare un'app 2.0 Wear con xamarin. Android, è necessario aggiungere il **wear v2.0** pacchetto al progetto (fare clic sui **scheda Sfoglia**):

[![V2.0 wear](intro-to-wear-images/wear-nuget-2.0-sml.png "installare NuGet v2.0 wear")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Questo pacchetto NuGet contiene le associazioni per Android indossabile supporto librerie e di compatibilità Wear.

Oltre a **wear**, è consigliabile installare la **Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "installare Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Funzionalità principali di usura 2.0

Android Wear 2.0 è il più grande aggiornamento per Android Wear dalla sua introduzione iniziale nel 2014. Le sezioni seguenti evidenziano le funzionalità principali di Android Wear 2.0 e vengono forniti collegamenti per iniziare a usare queste nuove funzionalità nell'app. 


#### <a name="complications"></a>Complicazioni

*Complicazioni* sono watch piccoli widget del volto che è possibile vedere immediatamente senza dover scorrere verso il quadrante dell'orologio. Complicazioni sono simili ai widget del dashboard desktop stile; vengono visualizzate informazioni quali il meteo, la durata della batteria, gli eventi del calendario e le statistiche di idoneità all'app: 

![Esempio di complicazioni](intro-to-wear-images/complications.png "esempio complicazioni")

Per altre informazioni sui problemi, vedere l'Android [Watch Face complicazioni](https://developer.android.com/wear/preview/features/complications.html) argomento. 



#### <a name="navigation-and-action-drawers"></a>Azione cassetti e navigazione 

Sono inclusi due pannelli nuovo Wear 2.0. Il *menu di spostamento*, che viene visualizzato nella parte superiore dello schermo, consente agli utenti di spostarsi tra le visualizzazioni delle app (come illustrato in basso a sinistra). Il *drawer azione*, che viene visualizzato nella parte inferiore dello schermo, come illustrato a destra, consente agli utenti di scegliere da un elenco di azioni. 

![Navigazione e azioni cassetti](intro-to-wear-images/drawers.png "cassetti di azione e navigazione")

Per ulteriori informazioni su queste due nuove cassetti interattive, vedere l'Android [Wear navigazione e azioni](https://developer.android.com/wear/preview/features/ui-nav-actions.html) argomento. 



#### <a name="curved-layouts"></a>Layout di curve 

Wear 2.0 introduce nuove funzionalità per la visualizzazione layout curvo nei dispositivi Wear arrotondamenti. In particolare, il nuovo `WearableRecyclerView` classe è ottimizzata per la visualizzazione di un elenco degli elementi verticali sugli schermi round: 

![Esempio di Layout linee curve](intro-to-wear-images/curved-layout.png "esempio Layout curvi")

`WearableRecyclerView` estende il `RecyclerView` classe per supportare la curvo layout e i movimenti di scorrimento circolari. Per altre informazioni, vedere l'Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) documentazione dell'API. 



#### <a name="standalone-apps"></a>App autonome 

Le app Android Wear 2.0 possono funzionare indipendentemente dalle App palmari. Ciò significa che, ad esempio, un'espressione di controllo smart può continuare a offrire funzionalità complete, anche se il dispositivo portatile complementare è attivato o disattivato a portata di mano dal dispositivo indossabile. Per altre informazioni su questa funzionalità, vedere l'Android [le app autonome](https://developer.android.com/wear/preview/features/standalone-apps.html) argomento.



#### <a name="wrist-gestures"></a>Movimenti del polso 

I movimenti del polso consentono agli utenti di interagire con l'app senza utilizzare il touchscreen &ndash; gli utenti possano rispondere all'app con una mano singola. Sono supportati i movimenti del polso due: 

-   Polso tratto secco e out
-   Polso tratto secco e in

Per altre informazioni, vedere l'Android [movimenti del polso](https://developer.android.com/wear/preview/features/gestures.html) argomento. 


Esistono molte altre funzionalità Wear 2.0, ad esempio azioni inline, risposta intelligente, input remoto, notifiche espanse e una nuova modalità di bridging per le notifiche. Per altre informazioni sulle nuove funzionalità Wear 2.0, vedere l'Android [panoramica dell'API](https://developer.android.com/wear/preview/api-overview.html). 



## <a name="devices"></a>Dispositivi

Di seguito sono riportati alcuni esempi dei dispositivi che è possono eseguire Android Wear:

* [Motorola 360](https://moto360.motorola.com/)
* [LG G Watch](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G Watch R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung Gear Live](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASU ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>Ulteriori informazioni

Consultare la documentazione di Google Android Wear:

* [Su Android Wear](http://www.android.com/wear/)
* [Progettazione di App Android Wear](https://developer.android.com/design/wear/index.html)
* [android.support.wearable library ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android Wear 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>Riepilogo

Questa introduzione è fornita una panoramica di Android Wear. Viene descritta la funzionalità di base di Android Wear e incluso una panoramica delle funzionalità introdotte in Android Wear 2.0. Fornito collegamenti per la lettura essenziale per aiutare gli sviluppatori di introduzione allo sviluppo di xamarin. Android Wear ed elencata esempi di alcuni dei dispositivi Android Wear attualmente sul mercato.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione e configurazione](~/android/wear/get-started/installation.md)
- [Introduzione](~/android/wear/get-started/index.md)
