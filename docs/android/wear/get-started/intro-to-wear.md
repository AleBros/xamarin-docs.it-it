---
title: Introduzione ad Android Wear
description: Con l'introduzione di Android Wear di Google, non si è più limitati solo a telefoni e tablet quando si tratta di sviluppare app Android eccezionali. Il supporto di Novell. Android per Android Wear ti permette di eseguire C# il codice con il polso. Questa introduzione fornisce una panoramica di base di Android Wear, ne descrive le funzionalità principali e offre una panoramica delle funzionalità disponibili in Android Wear 2,0. Sono elencati alcuni dei dispositivi Android Wear più diffusi e sono disponibili collegamenti alla documentazione essenziale di Google Android Wear per ulteriori letture.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3b1d27b1489cb71d4bd1922c2de993567ddf36bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028615"
---
# <a name="introduction-to-android-wear"></a>Introduzione ad Android Wear

_Con l'introduzione di Android Wear di Google, non si è più limitati solo a telefoni e tablet quando si tratta di sviluppare app Android eccezionali. Il supporto di Novell. Android per Android Wear ti permette di eseguire C# il codice con il polso. Questa introduzione fornisce una panoramica di base di Android Wear, ne descrive le funzionalità principali e offre una panoramica delle funzionalità disponibili in Android Wear 2,0. Sono elencati alcuni dei dispositivi Android Wear più diffusi e sono disponibili collegamenti alla documentazione essenziale di Google Android Wear per ulteriori letture._

## <a name="overview"></a>Panoramica

Android Wear viene eseguito su diversi dispositivi, tra cui Motorola 360, LG G Watch e Samsung Gear Live. Una seconda generazione, inclusa la SmartWatch 3 di Sony, è stata rilasciata anche con funzionalità aggiuntive, tra cui il GPS integrato e la riproduzione di musica offline. Per Android Wear 2,0, Google ha collaborato con LG per due nuovi orologi: il LG Watch Sport e lo stile LG Watch.

![Dispositivi Android Wear 2,0](intro-to-wear-images/hero-image.png "Esempio di dispositivi Android Wear 2,0")

Novell. Android 5,0 e versioni successive supportano l'uso di Android tramite il supporto per Android 4.4 W (API 20) e un pacchetto NuGet che aggiunge altri controlli dell'interfaccia utente specifici per l'utilizzo. Novell. Android 5,0 e versioni successive includono anche funzionalità per la creazione di pacchetti di app di consumo. I pacchetti NuGet sono disponibili anche per Android Wear 2,0, come descritto più avanti in questa guida.

## <a name="android-wear-basics"></a>Nozioni fondamentali su Android Wear

Android Wear presenta un paradigma dell'interfaccia utente diverso da quello delle app palmari Android. Il primo Wave of wear Apps è stato progettato per estendere in qualche modo un'app palmare complementare, ma a partire da Android Wear 2,0, le app Wear possono essere usate autonomamente. Quando si distribuisce un'app Wear, questa viene assemblata con un'app palmare complementare. Poiché la maggior parte delle app di consumo dipende da un'app complementare palmare, è necessario un modo per comunicare con le app palmari. Le sezioni seguenti descrivono questi scenari di utilizzo e delineano le principali funzionalità di Android Wear. 

### <a name="usage-scenarios"></a>Utilizzo di scenari

La prima versione di Android Wear è stata incentrata principalmente sull'estensione delle applicazioni palmari correnti con notifiche avanzate e sulla sincronizzazione dei dati tra l'app palmare e l'app indossabile. Pertanto, questi scenari sono relativamente semplici da implementare.

#### <a name="wearable-notifications"></a>Notifiche indossabili

Il modo più semplice per supportare Android Wear consiste nel sfruttare la natura condivisa delle notifiche tra il palmare e il dispositivo indossabile. Usando l'API di notifica del supporto V4 e la classe `WearableExtender` (disponibile nella [libreria di supporto per Novell Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), è possibile sfruttare le funzionalità native della piattaforma, ad esempio le schede di stile della posta in arrivo o l'input vocale. L'esempio [RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant) fornisce un codice di esempio che illustra come inviare un elenco di notifiche a un dispositivo Android Wear. 

#### <a name="companion-applications"></a>Applicazioni complementari

Un'altra strategia consiste nel creare un'applicazione completa che viene eseguita in modo nativo sul dispositivo indossabile e le coppie con un'app palmare complementare. Un esempio valido di questo approccio è l'app di esempio [quiz](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-quiz) , che illustra come creare un quiz in esecuzione su un dispositivo palmare e porre domande ai quiz sul dispositivo indossabile. 

### <a name="user-interface"></a>Interfaccia utente

Il modello di navigazione principale per l'usura è costituito da una serie di schede disposte verticalmente. Ognuna di queste schede può disporre di azioni associate sovrapposte nella stessa riga. Questa funzionalità è fornita dalla classe `GridViewPager`. si attiene allo stesso concetto di adapter `ListView`. In genere, il `GridViewPager` viene associato a una `FragmentGridPagerAdaptor` (o `GridPagerAdaptor`) che consente di rappresentare le celle di ogni riga e colonna come `Fragment`: 

[![Navigazione con usura](intro-to-wear-images/2d-picker-sml.png "Navigazione con usura")](intro-to-wear-images/2d-picker.png#lightbox)

Wear USA anche pulsanti di azione che sono costituiti da un cerchio grande colorato con testo di descrizione piccolo sotto di esso, come illustrato in precedenza.  L'esempio [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager) illustra come usare `GridViewPager` e `GridPagerAdapter` in un'app Wear.

Android Wear 2,0 aggiunge un cassetto di navigazione, un cassetto delle azioni e pulsanti di azione inline all'interfaccia utente Wear. Per altre informazioni sugli elementi dell'interfaccia utente di Android Wear 2,0, vedere l'argomento relativo all' [anatomia](https://www.google.com/design/spec-wear/system-overview/anatomy.html) Android. 

### <a name="communications"></a>Comunicazioni

Android Wear offre due diverse API di comunicazione per facilitare le comunicazioni tra app indossabili e app palmari complementari: 

**API dati** &ndash; questa API è simile a un archivio dati sincronizzato tra il dispositivo indossabile e il dispositivo palmare. Android si occupa della propagazione delle modifiche tra indossabili e palmari quando è ottimale. Quando il Wearable non è compreso nell'intervallo, Accoda la sincronizzazione per un momento successivo. Il punto di ingresso principale per questa API è `WearableClass.DataApi`. Per altre informazioni su questa API, vedere l'argomento Android [syncing data items](https://developer.android.com/training/wearables/data-layer/data-items.html) . 

**API messaggi** &ndash; questa API consente di usare un percorso di comunicazione di livello inferiore: un payload ridotto viene inviato unidirezionale senza sincronizzazione tra le app palmari e indossabili.
Il punto di ingresso principale per questa API è `WearableClass.MessageApi`.
Per altre informazioni su questa API, vedere l'argomento [invio e ricezione dei messaggi](https://developer.android.com/training/wearables/data-layer/messages.html) Android.

È possibile scegliere di registrare i callback per la ricezione dei messaggi tramite ciascuna interfaccia del listener API o, in alternativa, implementare un servizio nell'app che deriva da `WearableListenerService`.
Questo servizio verrà automaticamente creato da Android Wear.
Nell'esempio [FindMyPhone](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-findmyphonesample) viene illustrato come implementare un `WearableListenerService`.

### <a name="deployment"></a>Distribuzione

Ogni app indossabile viene distribuita con il proprio file APK incorporato all'interno dell'APK dell'applicazione principale. Questo pacchetto viene gestito automaticamente in Novell. Android 5,0 e versioni successive, ma deve essere eseguito manualmente per le versioni di Novell. Android precedenti alla versione 5,0. 
L'uso della creazione di [pacchetti illustra la](~/android/wear/deploy-test/packaging.md) distribuzione in modo più dettagliato. 

## <a name="going-further"></a>Più avanti 

Il modo migliore per acquisire familiarità con Android Wear è compilare e testare la prima app. L'elenco seguente fornisce un ordine di lettura consigliato per velocizzare rapidamente:

1. Installazione [& installazione](~/android/wear/get-started/installation.md) fornisce istruzioni dettagliate per l'installazione e la configurazione dell'ambiente di sviluppo per la compilazione di app Novell. Android Wear. 

2. Dopo aver installato i pacchetti necessari e configurato un emulatore o un dispositivo, vedere [Hello, Wear](~/android/wear/get-started/hello-wear.md) per istruzioni dettagliate che illustrano come creare un piccolo progetto Android Wear che gestisce i clic sui pulsanti e visualizza un contatore di clic per l'usura dispositivo. 

3. La [distribuzione & test](~/android/wear/deploy-test/index.md) fornisce informazioni più dettagliate sulla configurazione e la distribuzione in emulatori e dispositivi, incluse le istruzioni su come distribuire l'app in un dispositivo Wear tramite Bluetooth.

4. [Con le dimensioni dello schermo](~/android/wear/screen-sizes.md) viene illustrato come visualizzare in anteprima e ottimizzare l'interfaccia utente per le diverse dimensioni dello schermo disponibili nei dispositivi di consumo. 

5. L'uso della creazione di [pacchetti](~/android/wear/deploy-test/packaging.md) descrive i passaggi per la creazione manuale di app per la distribuzione in Google Play.

Dopo aver creato la prima app Wear, è possibile provare a creare un quadrante di orologio personalizzato per Android Wear. 
[La creazione di un quadrante con espressioni di controllo](~/android/wear/platform/creating-a-watchface.md) fornisce istruzioni dettagliate e codice di esempio per lo sviluppo di un servizio di tipo Watch-out digitale rimosso, seguito da un altro codice che lo migliora in modo analogo a un quadrante con funzionalità aggiuntive. 

## <a name="android-wear-20"></a>Android Wear 2,0

Android Wear 2,0 introduce un'ampia gamma di nuove caratteristiche e funzionalità, ad esempio le *complicazioni*, i layout curvi, i cassetti di navigazione e di azione e le notifiche espanse. Inoltre, l'uso di 2,0 rende possibile la creazione di app autonome che funzionano indipendentemente dalle app palmari. La nuova funzionalità dei *movimenti del polso* consente interazioni con una sola mano con l'app. Le sezioni seguenti evidenziano queste funzionalità e forniscono collegamenti che consentono di iniziare a usarle nell'app.

### <a name="install-wear-20-packages"></a>Installare i pacchetti Wear 2,0

Per creare un'app Wear 2,0 con Novell. Android, è necessario aggiungere il pacchetto **Novell. Android. Wear v 2.0** al progetto (fare clic sulla **scheda browse**):

[![Novell. Android. Wear v 2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "Installare Novell. Android. Wear v 2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Questo pacchetto NuGet contiene le associazioni per il supporto Android e le librerie compatibili Wear.

Oltre a **Novell. Android. Wear**, è consigliabile installare il pacchetto NuGet **Novell. GooglePlayServices. Wearable** : 

[![Novell. GooglePlayServices. Wearable](intro-to-wear-images/gpsw-nuget-sml.png "Installare il pacchetto NuGet Novell. GooglePlayServices. Wearable")](intro-to-wear-images/gpsw-nuget.png#lightbox)

### <a name="key-features-of-wear-20"></a>Funzionalità principali di Wear 2,0

Android Wear 2,0 è il più importante aggiornamento per Android Wear dopo il suo avvio iniziale in 2014. Le sezioni seguenti evidenziano le funzionalità principali di Android Wear 2,0 e sono disponibili collegamenti che consentono di iniziare a usare queste nuove funzionalità nell'app. 

#### <a name="complications"></a>Complicazioni

Le *complicazioni* sono un piccolo widget del quadrante dell'orologio che è possibile visualizzare immediatamente senza dover scorrere il quadrante dell'orologio. Le complicazioni sono simili ai widget del dashboard di tipo desktop; visualizzano informazioni quali Meteo, durata della batteria, eventi del calendario e statistiche delle app per la fitness: 

![Esempio di complicazioni](intro-to-wear-images/complications.png "Esempio di complicazioni")

Per altre informazioni sulle complicazioni, vedere l'argomento Android [Watch Complications](https://developer.android.com/wear/preview/features/complications.html) . 

#### <a name="navigation-and-action-drawers"></a>Cassetti di spostamento e azione 

Due nuovi cassetti sono inclusi in Wear 2,0. Il *cassetto di spostamento*, visualizzato nella parte superiore della schermata, consente agli utenti di spostarsi tra le visualizzazioni dell'app, come illustrato di seguito. Il *cassetto delle azioni*, visualizzato nella parte inferiore della schermata, come illustrato a destra, consente agli utenti di scegliere da un elenco di azioni. 

![Cassetti di spostamento e azione](intro-to-wear-images/drawers.png "Cassetti di spostamento e azione")

Per ulteriori informazioni su questi due nuovi cassetti interattivi, vedere l'argomento Android [Wear Navigation and actions](https://developer.android.com/wear/preview/features/ui-nav-actions.html) . 

#### <a name="curved-layouts"></a>Layout curvi 

Wear 2,0 introduce nuove funzionalità per la visualizzazione di layout curvi nei dispositivi round Wear. In particolare, la nuova classe `WearableRecyclerView` è ottimizzata per la visualizzazione di un elenco di elementi verticali nelle visualizzazioni round: 

![Esempio di layout curvo](intro-to-wear-images/curved-layout.png "Esempio di layout curvo")

`WearableRecyclerView` estende la classe `RecyclerView` per supportare layout curvi e movimenti di scorrimento circolari. Per altre informazioni, vedere la documentazione dell'API [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) per Android. 

#### <a name="standalone-apps"></a>App autonome 

Le app Android Wear 2,0 possono funzionare indipendentemente dalle app palmari. Ciò significa che, ad esempio, una Smart Watch può continuare a offrire funzionalità complete anche se il dispositivo palmare complementare è spento o lontano dal dispositivo indossabile. Per altre informazioni su questa funzionalità, vedere l'argomento [app autonome](https://developer.android.com/wear/preview/features/standalone-apps.html) Android.

#### <a name="wrist-gestures"></a>Movimenti del polso 

I movimenti dei polsi consentono agli utenti di interagire con l'app senza usare il touchscreen &ndash; gli utenti possono rispondere all'app con una sola mano. Sono supportati due movimenti del polso: 

- Scorrimenti rapidi
- Scorri il polso

Per ulteriori informazioni, vedere l'argomento relativo ai [movimenti dei polsi](https://developer.android.com/wear/preview/features/gestures.html) Android. 

Sono disponibili molte altre funzionalità di 2,0, ad esempio azioni inline, risposta intelligente, input remoto, notifiche espanse e una nuova modalità di bridging per le notifiche. Per altre informazioni sulle nuove funzionalità di Wear 2,0, vedere Panoramica dell' [API](https://developer.android.com/wear/preview/api-overview.html)Android. 

## <a name="devices"></a>Dispositivi

Di seguito sono riportati alcuni esempi di dispositivi che possono eseguire Android Wear:

- [Motorola 360](https://moto360.motorola.com/)
- [Orologio LG G](https://www.lg.com/us/smart-watches/lg-W100-g-watch)
- [LG G Watch R](https://www.lg.com/us/smartwatch/g-watch-r)
- [Samsung Gear Live](https://www.samsung.com/global/microsite/gear/gearlive_design.html)
- [Sony SmartWatch 3](https://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
- [ASUS ZenWatch](https://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)

## <a name="further-reading"></a>Ulteriori informazioni

Consultare la documentazione di Android Wear di Google:

- [Informazioni su Android Wear](https://www.android.com/wear/)
- [Progettazione di app Android Wear](https://developer.android.com/design/wear/index.html)
- [libreria Android. support. Wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [Android Wear 2,0](https://developer.android.com/wear/preview/index.html)

## <a name="summary"></a>Riepilogo

Questa introduzione fornisce una panoramica di Android Wear. Sono state descritte le funzionalità di base di Android Wear e è stata inclusa una panoramica delle funzionalità introdotte in Android Wear 2,0. Sono stati forniti collegamenti a letture essenziali per aiutare gli sviluppatori a iniziare a usare lo sviluppo di Novell. Android e sono stati elencati esempi di alcuni dei dispositivi Android Wear attualmente presenti sul mercato.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione e configurazione](~/android/wear/get-started/installation.md)
- [Introduzione](~/android/wear/get-started/index.md)
