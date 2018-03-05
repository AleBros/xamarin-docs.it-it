---
title: Introduzione a usura Android
description: "Con l'introduzione di accenti Android di Google, non sono più limitate a appena telefoni e Tablet per quanto riguarda lo sviluppo di App Android eccezionali. Supporto di xamarin per Android con accenti rende possibile eseguire il codice c# in alcun modo! In questa introduzione fornisce una panoramica di base dell'Android accenti, descrive le funzionalità principali e offre una panoramica delle funzionalità disponibili in 2.0, usare Android. Vengono elencati alcuni dei dispositivi Android, usare più comuni e vengono forniti collegamenti alla documentazione di Google Android accenti essenziale per un approfondimento."
ms.topic: article
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c334e78793f90b4f349f87e12e6b0093fe5cacf8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-android-wear"></a>Introduzione a usura Android

_Con l'introduzione di accenti Android di Google, non sono più limitate a appena telefoni e Tablet per quanto riguarda lo sviluppo di App Android eccezionali. Supporto di xamarin per Android con accenti rende possibile eseguire il codice c# in alcun modo! In questa introduzione fornisce una panoramica di base dell'Android accenti, descrive le funzionalità principali e offre una panoramica delle funzionalità disponibili in 2.0, usare Android. Vengono elencati alcuni dei dispositivi Android, usare più comuni e vengono forniti collegamenti alla documentazione di Google Android accenti essenziale per un approfondimento._

<a name="overview" />

## <a name="overview"></a>Panoramica

Usura Android viene eseguito su una vasta gamma di dispositivi, tra cui la prima generazione 360 Motorola, espressioni di controllo del generatore G e ingranaggio Samsung in tempo reale. Una seconda generazione, tra cui Sony SmartWatch 3, è stata rilasciata anche con funzionalità aggiuntive, inclusi GPS incorporati e riproduzione di file musicali non in linea. Per Android 2.0 accenti, Google ha collaborato con LG per due controlla di nuovo: lo Sport Watch LG e lo stile delle espressioni di controllo generatore di carico.

![I dispositivi Android con accenti 2.0](intro-to-wear-images/hero-image.png "esempio accenti 2.0 i dispositivi Android")

Supporta supporta xamarin 5.0 e versioni successive, usare Android tramite il nostro 4.4W Android (API 20) e un pacchetto NuGet che consente di aggiungere ulteriori controlli di interfaccia utente di usura specifiche. Xamarin 5.0 e versioni successive include inoltre funzionalità per creare il pacchetto App usura. Pacchetti NuGet sono anche disponibili per Android 2.0 accenti come descritto più avanti in questa Guida.


<a name="basics" />

## <a name="android-wear-basics"></a>Nozioni di base usura Android

Usura Android è un paradigma dell'interfaccia utente che è diverso da quello delle App Android di palmari. La prima tornata usura App sono stati progettati per estendere un complementare palmari app in un modo, ma a partire da Android usura 2.0, usura App possono essere utilizzato autonomo. Quando si distribuisce un'app usura, viene creato un pacchetto con un'applicazione palmari complementare. Poiché la maggior parte, usare applicazioni dipendono da un'app complementare palmari, è necessario trovare un modo per comunicare con applicazioni palmari. Le sezioni seguenti vengono descritti questi scenari di utilizzo e descrive le funzionalità essenziali, usare Android. 


<a name="scenarios" />

### <a name="usage-scenarios"></a>Utilizzo di scenari

La prima versione di Android accenti era incentrata principalmente su estendere applicazioni palmari correnti con notifiche avanzate e la sincronizzazione dei dati tra l'app palmari e l'app indossabile. Di conseguenza, questi scenari sono relativamente semplici da implementare.

<a name="notifications" />

#### <a name="wearable-notifications"></a>Notifiche indossabile

Il modo più semplice per il supporto, usare Android è per sfruttare la natura di notifiche tra il portatile e il dispositivo indossabile condivisa. Tramite l'API di notifica v4 supporto e `WearableExtender` classe (disponibile nel [libreria supporta Android di Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), è possibile sfruttare le funzionalità native della piattaforma, ad esempio schede di stile della posta in arrivo o input vocale. Il [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) esempio fornito codice di esempio che illustra come inviare un elenco di notifiche a un dispositivo Android con accenti. 


<a name="companion" />

#### <a name="companion-applications"></a>Applicazioni complementare

Un'altra strategia consiste nel creare un'applicazione completa che viene eseguito in modo nativo nel dispositivo indossabile e coppie con un'app palmari complementare. Un buon esempio di questo approccio è la [Quiz](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) app di esempio, in cui viene illustrato come creare un quiz in esecuzione su un dispositivo palmare e vengono poste delle domande quiz sul dispositivo indossabile. 


<a name="ui" />

### <a name="user-interface"></a>Interfaccia utente

Il modello di navigazione principale per usura è una serie di schede disposte verticalmente. Ognuna di queste schede può essere associate le azioni che sono disposti nella stessa riga. Il `GridViewPager` classe fornisce questa funzionalità, è conforme allo stesso concetto di adapter di `ListView`. In genere si associa il `GridViewPager` con un `FragmentGridPagerAdaptor` (o `GridPagerAdaptor`) che consente di rappresentano le celle di ogni riga e colonna come un `Fragment`: 

[ ![Navigazione con accenti](intro-to-wear-images/2d-picker-sml.png "accenti navigazione")](intro-to-wear-images/2d-picker.png)

Usare inoltre rende l'utilizzo di pulsanti di azione costituiti da una grande colorato cerchio con testo della descrizione di piccole dimensioni sottostanti (come illustrato sopra).  Il [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) esempio viene illustrato come utilizzare `GridViewPager` e `GridPagerAdapter` in un'app di scadenza.

2.0, usare Android aggiunge un pannello di navigazione, un cassetto di azione e i pulsanti di azione inline per l'interfaccia utente di scadenza. Per ulteriori informazioni sugli elementi dell'interfaccia utente 2.0, usare Android, vedere il Android [Anatomia](https://www.google.com/design/spec-wear/system-overview/anatomy.html) argomento. 


<a name="comm" />

### <a name="communications"></a>Comunicazioni

Android usura fornisce due API per semplificare le comunicazioni tra le app indossabile e palmari complementare di comunicazione diversi: 

**Dati API** &ndash; questa API è simile a un archivio dati sincronizzato tra il dispositivo indossabile e il dispositivo portatile. Android si occupa di propagazione delle modifiche tra palmari e indossabile quando è ottimale per eseguire questa operazione. Quando l'indossabile è compreso nell'intervallo, la sincronizzazione per un secondo momento mette in coda. Il punto di ingresso principale per questa API è `WearableClass.DataApi`. Per ulteriori informazioni su questa API, vedere il Android [sincronizzazione elementi di dati](https://developer.android.com/training/wearables/data-layer/data-items.html) argomento. 

**Messaggio API** &ndash; l'API consente di utilizzare un percorso di comunicazione di livello inferiore: invio unidirezionale senza sincronizzazione tra le app palmari e indossabile un payload di piccole dimensioni.
Il punto di ingresso principale per questa API è `WearableClass.MessageApi`.
Per ulteriori informazioni su questa API, vedere il Android [invio e ricezione di messaggi](https://developer.android.com/training/wearables/data-layer/messages.html) argomento.

È possibile scegliere di registrare i callback per ricevere i messaggi tramite ognuna delle interfacce API listener o, in alternativa, implementare un servizio nell'app che deriva da `WearableListenerService`.
Questo servizio verrà automaticamente creata un'istanza da Android accenti.
Il [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) esempio viene illustrato come implementare un `WearableListenerService`.


<a name="deploy" />

### <a name="deployment"></a>Distribuzione

Ogni app indossabile viene distribuito con il proprio file APK incorporato all'interno dell'applicazione principale APK. Questo pacchetto viene gestito automaticamente in xamarin 5.0 e versioni successive, ma deve essere eseguito manualmente per le versioni precedenti alla versione 5.0 di xamarin. 
[Utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md) illustra la distribuzione in modo più dettagliato. 


<a name="further" />

## <a name="going-further"></a>Continua 

Il modo migliore per acquisire familiarità con accenti Android è per compilare e testare la prima app. Nell'elenco seguente fornisce un ordine di letture consigliate che consentono di acquisire rapidamente dimestichezza:

1.  [Installazione e configurazione](~/android/wear/get-started/installation.md) vengono fornite istruzioni dettagliate per l'installazione e configurazione dell'ambiente di sviluppo per la compilazione di App xamarin usura. 

2.  Dopo aver installato i pacchetti richiesti e configurato un emulatore o dispositivo, vedere [salve, usura](~/android/wear/get-started/hello-wear.md) per istruzioni dettagliate che illustrano come creare un piccolo progetto Android, usare il pulsante di handle fa clic e visualizza un Fare clic su contatore sul dispositivo usura. 

3.  [Distribuzione e test](~/android/wear/deploy-test/index.md) fornisce ulteriori informazioni sulla configurazione e la distribuzione in emulatori e dispositivi, incluse le istruzioni su come distribuire l'app in un dispositivo usura tramite Bluetooth.

4.  [Utilizzo delle dimensioni dello schermo](~/android/wear/screen-sizes.md) viene illustrato come visualizzare in anteprima e ottimizzare l'interfaccia utente per varie dimensioni di schermata disponibili nei dispositivi usura. 

5.  [Utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md) vengono descritti i passaggi per creare manualmente il pacchetto App di scadenza per la distribuzione su Google Play.

Dopo aver creato la prima app usura, si desidera ripetere la compilazione di un tipo di carattere del controllo personalizzato per Android con accenti. 
[Creazione di un tipo di carattere di controllo](~/android/wear/platform/creating-a-watchface.md) fornisce istruzioni dettagliate e codice di esempio per lo sviluppo di un' servizio faccia digitale espressioni di controllo, seguito da altro codice che rende per un quadrante di orologio analogico stile con funzionalità aggiuntive. 


<a name="wear2" />

## <a name="android-wear-20"></a>Android usura 2.0

2.0, usare Android introduce varie nuove caratteristiche e funzionalità, ad esempio *complicazioni*, curve, layout, spostamento e l'azione cassetti e notifiche espanse. Inoltre, accenti 2.0 rende possibile compilare App autonoma che funzionano in modo indipendente da app palmari. Il nuovo *movimenti polso* capacità consente motori interazioni con l'app. Nelle sezioni seguenti evidenziano queste funzionalità e forniscono i collegamenti che consentono di iniziare a usare tali nell'app.


<a name="install2" />

### <a name="install-wear-20-packages"></a>Installare 2.0 pacchetti, usare

Per compilare un'app con accenti 2.0 con xamarin, è necessario aggiungere il **v 2.0 Xamarin.Android.Wear** pacchetto al progetto (fare clic su di **scheda Sfoglia**):

[![V 2.0 Xamarin.Android.Wear](intro-to-wear-images/wear-nuget-2.0-sml.png "installare NuGet Xamarin.Android.Wear v 2.0")](intro-to-wear-images/wear-nuget-2.0.png)

Il pacchetto NuGet contiene associazioni per le librerie di Android supporto indossabile sia compatibile con accenti.

Oltre a **Xamarin.Android.Wear**, si consiglia di installare il **Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "installare Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png)

<a name="wear2feat" />

### <a name="key-features-of-wear-20"></a>Funzionalità principali di usura 2.0

2.0, usare Android è il più grande aggiornamento portino Android dalla sua introduzione iniziale nel 2014. Le sezioni seguenti illustrano le funzionalità principali di Android accenti 2.0 e vengono forniti collegamenti per iniziare a utilizzare queste nuove funzionalità nell'app. 

<a name="compl" />

#### <a name="complications"></a>Complessità

*Complessità* sono espressioni di controllo piccoli widget di tipo di carattere che è possibile visualizzare immediatamente senza dover scorrere verso il quadrante dell'orologio. Complicazioni sono simili ai widget di dashboard desktop stile. contengono informazioni quali il meteo, la durata della batteria, gli eventi del calendario e statistiche app adeguatezza: 

![Esempio complicazioni](intro-to-wear-images/complications.png "esempio complicazioni")

Per ulteriori informazioni sui problemi, vedere il Android [espressioni di controllo faccia complicazioni](https://developer.android.com/wear/preview/features/complications.html) argomento. 


<a name="drawers" />

#### <a name="navigation-and-action-drawers"></a>Navigazione e azioni cassetti 

Due cassetti nuovo sono inclusi 2.0 accenti. Il *Pannello di navigazione*, che viene visualizzato nella parte superiore dello schermo, consente agli utenti di spostarsi tra le visualizzazioni di app (visualizzata a sinistra sotto). Il *cassetto azione*, che viene visualizzato nella parte inferiore dello schermo (come illustrato a destra), consente agli utenti di scegliere da un elenco di azioni. 

![Navigazione e azioni cassetti](intro-to-wear-images/drawers.png "cassetti di azione e navigazione")

Per ulteriori informazioni su questi due nuovi cassetti interattivi, vedere il Android [accenti navigazione e azioni](https://developer.android.com/wear/preview/features/ui-nav-actions.html) argomento. 


<a name="curved" />

#### <a name="curved-layouts"></a>Layout di curve 

Usura 2.0 introduce nuove funzionalità per la visualizzazione layout curvo nei dispositivi usura arrotondamenti. In particolare, il nuovo `WearableRecyclerView` è con ottimizzazione per la classe per visualizzare un elenco di elementi verticali round Visualizza: 

![Esempio di Layout di linee curve](intro-to-wear-images/curved-layout.png "esempio curve Layout")

`WearableRecyclerView` estende la `RecyclerView` classe per supportare la curvo layout e i movimenti di scorrimento circolari. Per ulteriori informazioni, vedere il Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) documentazione dell'API. 


<a name="standalone" />

#### <a name="standalone-apps"></a>Applicazioni autonome 

Le app Android con accenti 2.0 possono funzionare indipendentemente dalle App palmari. Ciò significa che, ad esempio, un'espressione di controllo smart possono continuare a offrire funzionalità complete, anche se il dispositivo portatile complementare è attivato o disattivato lontano dal dispositivo indossabile. Per ulteriori informazioni su questa funzionalità, vedere il Android [autonomo app](https://developer.android.com/wear/preview/features/standalone-apps.html) argomento.


<a name="wrist" />

#### <a name="wrist-gestures"></a>Movimenti polso 

I movimenti polso consentono agli utenti di interagire con l'app senza utilizzare il touchscreen &ndash; gli utenti possano rispondere all'app con una sola mano. Sono supportati i movimenti polso due: 

-   Polso gesto rapido out
-   Polso gesto rapido in

Per ulteriori informazioni, vedere il Android [polso movimenti](https://developer.android.com/wear/preview/features/gestures.html) argomento. 


Sono disponibili molte altre funzionalità con accenti 2.0 quali azioni inline, risposta smart, input remoto, notifiche espanse e una nuova modalità di bridging per le notifiche. Per ulteriori informazioni sulle nuove funzionalità accenti 2.0, vedere il Android [panoramica dell'API](https://developer.android.com/wear/preview/api-overview.html). 


<a name="devices" />

## <a name="devices"></a>Dispositivi

Di seguito sono riportati alcuni esempi dei dispositivi che è possono eseguire con accenti Android:

* [360 Motorola](https://moto360.motorola.com/)
* [LG G espressioni di controllo](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [Espressioni di controllo LG G R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Icona dell'ingranaggio Samsung in tempo reale](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASU ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)


<a name="reading" />

## <a name="further-reading"></a>Ulteriori informazioni

Consultare la documentazione, usare Android di Google:

* [Su usura Android](http://www.android.com/wear/)
* [Progettazione di App Android usura](https://developer.android.com/design/wear/index.html)
* [libreria Android.support.wearable ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android usura 2.0](https://developer.android.com/wear/preview/index.html)


<a name="summary" />

## <a name="summary"></a>Riepilogo

Questa introduzione fornita una panoramica di Android accenti. Descritta la funzionalità di base di Android, usare e incluso una panoramica delle funzionalità introdotte in 2.0, usare Android. Forniti collegamenti alla lettura essenziali per consentire agli sviluppatori informazioni introduttive allo sviluppo di xamarin usura ed esempi di alcuni dispositivi Android con accenti attualmente sul mercato elencata.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione e configurazione](~/android/wear/get-started/installation.md)
- [Introduzione](~/android/wear/get-started/index.md)
