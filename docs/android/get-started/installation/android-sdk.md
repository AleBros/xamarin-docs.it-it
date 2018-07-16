---
title: Configurazione di Android SDK per Xamarin.Android
description: Visual Studio include Android SDK Manager che consente di scaricare gli strumenti, le piattaforme e gli altri componenti di Android SDK necessari per sviluppare app Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/10/2018
ms.openlocfilehash: 895496f6a198f679ce08322ae48fe88e03b85629
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947270"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configurazione di Android SDK per Xamarin.Android

_Visual Studio include Android SDK Manager che consente di scaricare gli strumenti, le piattaforme e gli altri componenti di Android SDK necessari per sviluppare app Xamarin.Android._


## <a name="overview"></a>Panoramica

Questa guida illustra come usare Xamarin Android SDK Manager in Visual Studio e Visual Studio per Mac.

> [!NOTE]
> Questa guida si applica solo a Visual Studio 2017 e Visual Studio per Mac.  

Xamarin Android SDK Manager viene installato come componente del carico di lavoro**Sviluppo di app per dispositivi mobili con .NET** e consente di scaricare i componenti Android più recenti necessari per lo sviluppo dell'app Xamarin.Android. Sostituisce lo strumento autonomo SDK Manager di Google, che è stato deprecato.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="requirements"></a>Requisiti

Per usare Xamarin Android SDK Manager sono necessari i componenti seguenti:

- Visual Studio 2017 (edizione Community, Professional o Enterprise). È necessario Visual Studio 2017 versione 15.7 o versione successiva.

- Strumenti di Visual Studio per Xamarin 4.10.0 o versione successiva. 

Xamarin Android SDK Manager non è compatibile con Visual Studio
2015. Gli utenti di Visual Studio 2015 devono usare gli strumenti di SDK Manager che Google offre in Android SDK.


In Xamarin Android SDK Manager è anche necessario Java Development Kit, che viene installato automaticamente con Xamarin.Android.
Xamarin.Android usa [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), la versione necessaria per sviluppare il livello API 24 o un livello superiore. JDK 8 supporta anche i livelli API precedenti al livello 24. È possibile continuare a usare [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) per attività di sviluppo specifiche per il livello API 23 o un livello precedente.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

 
## <a name="sdk-manager"></a>SDK Manager 

Per avviare SDK Manager in Visual Studio, selezionare **Strumenti -> Android -> Android SDK Manager**:

[![Percorso della voce di menu Android SDK Manager](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

**Xamarin Android SDK Manager** si apre nella schermata **Android SDK e strumenti Android**, in cui sono disponibili due schede &ndash; **Piattaforme** e **Strumenti**:

[![Screenshot di Android SDK Manager aperto nella scheda Piattaforme](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

La schermata **Android SDK e strumenti Android** viene descritta più nel dettaglio nelle sezioni seguenti.


### <a name="android-sdk-location"></a>Percorso di Android SDK

Il percorso di Android SDK è configurato nella parte superiore della schermata **Android SDK e strumenti Android**, come illustrato nell'immagine precedente. Questo percorso deve essere configurato correttamente per l'esatto funzionamento delle schede **Piattaforme** e **Strumenti**. È possibile dover impostare il percorso di Android SDK per uno o più dei motivi seguenti:

1. Non è stato possibile per Xamarin SDK Manager individuare Android SDK. 

2. Android SDK è stato installato in un percorso diverso (percorso non predefinito). 

Per impostare il percorso di Android SDK, fare clic sul pulsante &hellip; all'estrema destra di **Percorso di Android SDK**. Si aprirà la finestra di dialogo **Sfoglia per cartelle** da usare per spostarsi nel percorso di Android SDK. Nella schermata seguente Android SDK viene selezionato in **Programmi (x86)\\Android**:

![Schermata della finestra di dialogo Sfoglia per cartelle di Windows in cui si trova android sdk](android-sdk-images/win/05-browse-for-folder.png)

Facendo clic su **OK**, Xamarin Android SDK Manager gestirà Android SDK installato nella posizione selezionata.



### <a name="tools-tab"></a>Scheda Strumenti

Nella scheda **Strumenti** viene visualizzato un elenco di _strumenti_ e _funzionalità aggiuntive_. Usare questa scheda per installare gli strumenti di Android SDK, gli strumenti della piattaforma e gli strumenti di compilazione.
È anche possibile installare l'emulatore Android, il debugger di basso livello, NDK, l'accelerazione HAXM e le librerie di Google Play.


Ad esempio, per scaricare il pacchetto relativo all'emulatore Android di Google, selezionare il segno di spunta accanto a **Emulatore Android** e fare clic sul pulsante **Applica modifiche**:

[![Installazione dell'emulatore Android dalla scheda Strumenti](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)



Potrebbe essere visualizzata una finestra di dialogo contenente il messaggio, _È possibile aggiornare alcuni componenti. Aggiornarli ora?_ Scegliere **Sì**. Si aprirà la finestra di dialogo Accettazione della licenza:


![Schermata Accettazione della licenza](android-sdk-images/win/07-license-acceptance.png)


Selezionare **Accetto** se si accettano i termini e le condizioni. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine dell'installazione, nella scheda **Strumenti** saranno visualizzati gli strumenti e le funzionalità aggiuntive installati.



### <a name="platforms-tab"></a>Scheda Piattaforme

Nella scheda **Piattaforme** viene visualizzato un elenco delle versioni SDK della piattaforma e altre risorse (ad esempio, le immagini del sistema) per ogni piattaforma.


[![Schermata del riquadro Piattaforme](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)


In questa schermata vengono elencati la versione Android (ad esempio **Android 7.0**), il nome in codice (**Nougat**), il livello API (ad esempio **24**) e lo stato (**Installato** se la piattaforma è installata). Usare la scheda **Piattaforme** per installare i componenti per il livello API Android di destinazione. Per altre informazioni sulle versioni Android e sui livelli API, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android).

Se tutti i componenti di una piattaforma sono installati, accanto al nome della piattaforma viene visualizzato un segno di spunta. Se non tutti i componenti di una piattaforma sono installati, la casella per la piattaforma è compilata. 


Fare clic sulla casella **+** a sinistra della piattaforma per espandere una piattaforma e visualizzare i relativi componenti e i componenti installati.
Fare clic su **-** per comprimere l'elenco dei componenti di una piattaforma.


Per aggiungere un'altra piattaforma a SDK, selezionare la casella accanto alla piattaforma finché viene visualizzato il segno di spunta per installare tutti i relativi componenti, quindi fare clic su **Applica modifiche**:


[![Esempio di aggiunta dei componenti di Android Nougat 7.1 ad Android SDK](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)


Per installare solo SDK fare clic una sola volta sulla casella accanto alla piattaforma. È possibile selezionare i singoli componenti necessari:


[![Esempio di aggiunta di alcuni componenti di Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)




Si noti che il numero di componenti da installare viene visualizzato accanto al pulsante **Applica modifiche**. Nell'esempio precedente sono sei i componenti pronti per l'installazione. Dopo aver fatto clic sul pulsante **Applica modifiche**, si aprirà la schermata **Accettazione della licenza**:



![Finestra di dialogo Accettazione della licenza della scheda Piattaforme](android-sdk-images/win/11-license-screen.png)


Selezionare **Accetto** se si accettano i termini e le condizioni. È possibile che questa finestra di dialogo sia visualizzata più di una volta se i componenti da installare sono più di uno. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine del download e dell'installazione (l'operazione può richiedere molti minuti, a seconda del numero di componenti da scaricare), i componenti aggiunti vengono contrassegnati da un segno di spunta ed elencati con lo stato **Installato**.



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


## <a name="requirements"></a>Requisiti

Per usare Xamarin Android SDK Manager sono necessari i componenti seguenti:

-   Visual Studio per Mac 7.5 (o versione successiva).

In Xamarin Android SDK Manager è anche necessario Java Development Kit, che viene installato automaticamente con Xamarin.Android.
Xamarin.Android usa [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), la versione necessaria per sviluppare il livello API 24 o un livello superiore. JDK 8 supporta anche i livelli API precedenti al livello 24. È possibile continuare a usare [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) per attività di sviluppo specifiche per il livello API 23 o un livello precedente.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

 
## <a name="sdk-manager"></a>SDK Manager 

Per avviare SDK Manager in Visual Studio per Mac, selezionare **Strumenti -> SDK Manager**:
 
![Percorso della voce di menu Android SDK Manager](android-sdk-images/mac/sdkmanager-01.png )

**Android SDK Manager** si apre nel **finestra Preferenze**, che contiene tre schede **Piattaforme**, **Strumenti** e **Percorsi**:

![Screenshot di Android SDK Manager aperto nella scheda Piattaforme](android-sdk-images/mac/sdkmanager-02.png)

Le schede di Xamarin Android SDK Manager sono descritte nelle sezioni seguenti.


### <a name="locations-tab"></a>Scheda Percorsi

Nella scheda **Percorsi** sono disponibili tre impostazioni per la configurazione di Android SDK, Android NDK e Java SDK (JDK). Questi percorsi devono essere configurati correttamente per l'esatto funzionamento delle schede **Piattaforme** e **Strumenti**.

All'avvio, SDK Manage determina automaticamente il percorso per ogni pacchetto installato e indica che è stato **Trovato** inserendo un'icona di spunta verde accanto al percorso:

![Schermata della scheda Percorsi](android-sdk-images/mac/sdkmanager-03.png)

Fare clic sul pulsante **Ripristina valori predefiniti** perché SDK Manager esegua la ricerca di SDK, NDK e JDK in percorsi predefiniti. 

In genere, si usa la scheda **Percorsi** per modificare il percorso di Android SDK e/o Java JDK. Non è necessario installare NDK per sviluppare app Xamarin.Android. NDK viene usato solo quando è necessario sviluppare parti di app che usano linguaggi di codice nativo, ad esempio C e C++.

### <a name="tools-tab"></a>Scheda Strumenti

Nella scheda **Strumenti** viene visualizzato un elenco di _strumenti_ e _funzionalità aggiuntive_. Usare questa scheda per installare gli strumenti di Android SDK, gli strumenti della piattaforma e gli strumenti di compilazione.
È anche possibile installare l'emulatore Android, il debugger di basso livello, NDK, l'accelerazione HAXM e le librerie di Google Play.


Ad esempio, per scaricare il pacchetto relativo all'emulatore Android di Google, selezionare il segno di spunta accanto a **Emulatore Android** e fare clic sul pulsante **Installa aggiornamenti**:

![Installazione dell'emulatore Android dalla scheda Strumenti](android-sdk-images/mac/sdkmanager-08.png)


Potrebbe essere visualizzata una finestra di dialogo contenente il messaggio, _È possibile aggiornare alcuni componenti. Aggiornarli ora?_ Scegliere **Sì**. Si aprirà la finestra di dialogo Accettazione della licenza:


![Schermata Accettazione della licenza](android-sdk-images/mac/sdkmanager-09.png)


Selezionare **Accetto** se si accettano i termini e le condizioni. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine dell'installazione, nella scheda **Strumenti** saranno visualizzati gli strumenti e le funzionalità aggiuntive installati.



### <a name="platforms-tab"></a>Scheda Piattaforme

Nella scheda **Piattaforme** viene visualizzato un elenco delle versioni SDK della piattaforma e altre risorse (ad esempio, le immagini del sistema) per ogni piattaforma.


![Schermata del riquadro Piattaforme](android-sdk-images/mac/sdkmanager-11.png)


In questa schermata vengono elencati la versione Android (ad esempio **Android 7.0**), il nome in codice (**Nougat**), il livello API (ad esempio **24**) e lo stato (**Installato** se la piattaforma è installata). Usare la scheda **Piattaforme** per installare i componenti per il livello API Android di destinazione. Per altre informazioni sulle versioni Android e sui livelli API, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android).

Se tutti i componenti di una piattaforma sono installati, accanto al nome della piattaforma viene visualizzato un segno di spunta. Se non tutti i componenti di una piattaforma sono installati, la casella per la piattaforma è compilata. 


Fare clic sulla casella **freccia** a sinistra della piattaforma per espandere una piattaforma e visualizzare i relativi componenti e i componenti installati.
Fare clic su **freccia giù** per comprimere l'elenco dei componenti di una piattaforma.


Per aggiungere un'altra piattaforma a SDK, selezionare la casella accanto alla piattaforma finché viene visualizzato il segno di spunta per installare tutti i relativi componenti, quindi fare clic su **Applica modifiche**:


![Esempio di aggiunta dei componenti di Android 4.4 ad Android SDK](android-sdk-images/mac/sdkmanager-12.png)


Per installare solo SDK fare clic una sola volta sulla casella accanto alla piattaforma. È possibile selezionare i singoli componenti necessari:


![Esempio di aggiunta di alcuni componenti di Android 4.4](android-sdk-images/mac/sdkmanager-13.png)




Si noti che il numero di componenti da installare viene visualizzato accanto al pulsante **Applica modifiche**. Dopo aver fatto clic sul pulsante **Applica modifiche**, si aprirà la schermata **Accettazione della licenza**:



![Finestra di dialogo Accettazione della licenza della scheda Piattaforme](android-sdk-images/mac/sdkmanager-14.png)


Selezionare **Accetto** se si accettano i termini e le condizioni. È possibile che questa finestra di dialogo sia visualizzata più di una volta se i componenti da installare sono più di uno. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine del download e dell'installazione (l'operazione può richiedere molti minuti, a seconda del numero di componenti da scaricare), i componenti aggiunti vengono contrassegnati da un segno di spunta ed elencati con lo stato **Installato**.

-----

 
## <a name="summary"></a>Riepilogo

In questa guida è stato spiegato come installare e usare lo strumento Xamarin Android SDK Manager in Visual Studio e Visual Studio per Mac.


## <a name="related-links"></a>Collegamenti correlati

- [Modifiche agli strumenti di Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
