---
title: Configurazione di Android SDK per Xamarin.Android
description: Visual Studio include Android SDK Manager che sostituisce SDK Manager, lo strumento autonomo di Google. Questa guida illustra come usare SDK Manager per scaricare gli strumenti, le piattaforme e gli altri componenti di Android SDK necessari per sviluppare Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 6a3f3f79e81339cc903d85081ca173a7ac707f6a
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935477"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configurazione di Android SDK per Xamarin.Android

_Visual Studio include Android SDK Manager che sostituisce SDK Manager, lo strumento autonomo di Google. Questa guida illustra come usare SDK Manager per scaricare gli strumenti, le piattaforme e gli altri componenti di Android SDK necessari per sviluppare app Xamarin.Android._


## <a name="overview"></a>Panoramica

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Questa guida illustra come installare e usare Xamarin Android SDK Manager per Visual Studio in Windows (o [per Mac](?tabs=vsmac)).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Questa guida illustra come installare e usare Xamarin Android SDK Manager per Visual Studio per Mac (o [per Windows](?tabs=vswin)).

> [!NOTE]
> Questa guida si applica solo a Visual Studio 2017 e Visual Studio per Mac.  

-----

Xamarin Android SDK Manager consente di scaricare i componenti Android più recenti necessari per lo sviluppo dell'app Xamarin.Android.
Sostituisce lo strumento autonomo SDK Manager di Google, che è stato deprecato.

Perché usare Xamarin Android SDK Manager anziché SDK Manager incluso in Android SDK? Nella versione 25.2.3 del pacchetto Android SDK Tools Google introduce un nuovo strumento per gestire Android SDK. Questo nuovo strumento è **[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)**, un'utilità della riga di comando che sostituisce la gestione autonoma dell'interfaccia utente per Android SDK. Se si esegue l'aggiornamento alla versione di 26.0.1 di SDK Tools (obbligatorio per Android 8.0) o versione successiva e si vuole gestire Android SDK ancora tramite un'interfaccia dell'interfaccia utente, è quindi necessario usare Xamarin Android SDK Manager.

## <a name="requirements"></a>Requisiti

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per usare Xamarin Android SDK Manager sono necessari i componenti seguenti:

- Visual Studio 2017 (edizione Community, Professional o Enterprise). È necessario Visual Studio 2017 versione 15.5 o versione successiva.

- Strumenti di Visual Studio per Xamarin 4.5.0 o versione successiva. 

Xamarin Android SDK Manager non è compatibile con Visual Studio
2015. Gli utenti di Visual Studio 2015 devono usare gli strumenti di SDK Manager che Google offre in Android SDK.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

-   Visual Studio per Mac 7.0.0.3146 o versione successiva.

-----

In Xamarin Android SDK Manager è anche necessario Java Development Kit, che viene installato automaticamente con Xamarin.Android.
Xamarin.Android usa [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), la versione necessaria per sviluppare il livello API 24 o un livello superiore. JDK 8 supporta anche i livelli API precedenti al livello 24. È possibile continuare a usare [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) per attività di sviluppo specifiche per il livello API 23 o un livello precedente.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installation"></a>Installazione

Xamarin SDK Manager può essere aggiunto a Visual Studio 2017 al momento dell'installazione. Quando si installa Visual Studio, fare clic su **Singoli componenti** e scorrere verso il basso fino alla sezione **Attività di sviluppo**.
Abilitare **Xamarin SDK Manager** se l'opzione non è già selezionata:

![Abilitare Xamarin SDK Manager da Singoli componenti](android-sdk-images/win/01-sdk-manager-install.png)

Se Visual Studio 2017 è già installato, vedere [Modificare Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/modify-visual-studio) per istruzioni su come modificare Visual Studio, quindi seguire la procedura sopra riportata per abilitare Xamarin SDK Manager. Se viene visualizzato un prompt per aggiornare SDK Manager, è possibile usare la stessa procedura per installare Xamarin SDK Manager.

Selezionando **Strumenti > Android > Android SDK Manager** (come descritto di seguito), si avvia Xamarin Android SDK Manager anziché Android SDK Manager di Google. Se si usa una versione precedente di Android SDK che supporta la versione autonoma di Android SDK Manager di Google, l'installazione di Xamarin Android SDK Manager non crea conflitto. È comunque possibile avviare la versione autonoma di SDK Manager di Google all'esterno di Visual Studio per gestire Android SDK.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
 
-----

 
## <a name="sdk-manager"></a>SDK Manager 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per avviare SDK Manager in Visual Studio, selezionare **Strumenti -> Android -> Android SDK Manager**:

[![Percorso della voce di menu Android SDK Manager](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

**Xamarin Android SDK Manager** si apre nella schermata **Android SDK e strumenti Android**, in cui sono disponibili due schede &ndash; **Piattaforme** e **Strumenti**:

[![Screenshot di Android SDK Manager aperto nella scheda Piattaforme](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

La schermata **Android SDK e strumenti Android** viene descritta più nel dettaglio nelle sezioni seguenti.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per avviare SDK Manager in Visual Studio per Mac, selezionare **Strumenti -> SDK Manager**:
 
![Percorso della voce di menu Android SDK Manager](android-sdk-images/mac/sdkmanager-01.png )

**Android SDK Manager** si apre nel **finestra Preferenze**, che contiene tre schede **Piattaforme**, **Strumenti** e **Percorsi**:

![Screenshot di Android SDK Manager aperto nella scheda Piattaforme](android-sdk-images/mac/sdkmanager-02.png)

Le schede di Xamarin Android SDK Manager sono descritte nelle sezioni seguenti.

-----



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-location"></a>Percorso di Android SDK

Il percorso di Android SDK è configurato nella parte superiore della schermata **Android SDK e strumenti Android**, come illustrato nell'immagine precedente. Questo percorso deve essere configurato correttamente per l'esatto funzionamento delle schede **Piattaforme** e **Strumenti**. È possibile dover impostare il percorso di Android SDK per uno o più dei motivi seguenti:

1. Non è stato possibile per Xamarin SDK Manager individuare Android SDK. 

2. Android SDK è stato installato in un percorso diverso (percorso non predefinito). 

Per impostare il percorso di Android SDK, fare clic sul pulsante &hellip; all'estrema destra di **Percorso di Android SDK**. Si aprirà la finestra di dialogo **Sfoglia per cartelle** da usare per spostarsi nel percorso di Android SDK. Nella schermata seguente Android SDK viene selezionato in **Programmi (x86)\\Android**:

![Schermata della finestra di dialogo Sfoglia per cartelle di Windows in cui si trova android sdk](android-sdk-images/win/05-browse-for-folder.png)

Facendo clic su **OK**, Xamarin Android SDK Manager gestirà Android SDK installato nella posizione selezionata.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

### <a name="locations-tab"></a>Scheda Percorsi

Nella scheda **Percorsi** sono disponibili tre impostazioni per la configurazione di Android SDK, Android NDK e Java SDK (JDK). Questi percorsi devono essere configurati correttamente per l'esatto funzionamento delle schede **Piattaforme** e **Strumenti**.

All'avvio, SDK Manage determina automaticamente il percorso per ogni pacchetto installato e indica che è stato **Trovato** inserendo un'icona di spunta verde accanto al percorso:

![Schermata della scheda Percorsi](android-sdk-images/mac/sdkmanager-03.png)

Fare clic sul pulsante **Ripristina valori predefiniti** perché SDK Manager esegua la ricerca di SDK, NDK e JDK in percorsi predefiniti. 

In genere, si usa la scheda **Percorsi** per modificare il percorso di Android SDK e/o Java JDK. Non è necessario installare NDK per sviluppare app Xamarin.Android. NDK viene usato solo quando è necessario sviluppare parti di app che usano linguaggi di codice nativo, ad esempio C e C++.

-----


### <a name="tools-tab"></a>Scheda Strumenti

Nella scheda **Strumenti** viene visualizzato un elenco di _strumenti_ e _funzionalità aggiuntive_. Usare questa scheda per installare gli strumenti di Android SDK, gli strumenti della piattaforma e gli strumenti di compilazione.
È anche possibile installare l'emulatore Android, il debugger di basso livello, NDK, l'accelerazione HAXM e le librerie di Google Play.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Ad esempio, per scaricare il pacchetto relativo all'emulatore Android di Google, selezionare il segno di spunta accanto a **Emulatore Android** e fare clic sul pulsante **Applica modifiche**:

[![Installazione dell'emulatore Android dalla scheda Strumenti](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Ad esempio, per scaricare il pacchetto relativo all'emulatore Android di Google, selezionare il segno di spunta accanto a **Emulatore Android** e fare clic sul pulsante **Installa aggiornamenti**:

![Installazione dell'emulatore Android dalla scheda Strumenti](android-sdk-images/mac/sdkmanager-08.png)

-----


Potrebbe essere visualizzata una finestra di dialogo contenente il messaggio, _È possibile aggiornare alcuni componenti. Aggiornarli ora?_ Scegliere **Sì**. Si aprirà la finestra di dialogo Accettazione della licenza:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Schermata Accettazione della licenza](android-sdk-images/win/07-license-acceptance.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Schermata Accettazione della licenza](android-sdk-images/mac/sdkmanager-09.png)

-----

Selezionare **Accetto** se si accettano i termini e le condizioni. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine dell'installazione, nella scheda **Strumenti** saranno visualizzati gli strumenti e le funzionalità aggiuntive installati.



### <a name="platforms-tab"></a>Scheda Piattaforme

Nella scheda **Piattaforme** viene visualizzato un elenco delle versioni SDK della piattaforma e altre risorse (ad esempio, le immagini del sistema) per ogni piattaforma.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Schermata del riquadro Piattaforme](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Schermata del riquadro Piattaforme](android-sdk-images/mac/sdkmanager-11.png)

-----

In questa schermata vengono elencati la versione Android (ad esempio **Android 7.0**), il nome in codice (**Nougat**), il livello API (ad esempio **24**) e lo stato (**Installato** se la piattaforma è installata). Usare la scheda **Piattaforme** per installare i componenti per il livello API Android di destinazione. Per altre informazioni sulle versioni Android e sui livelli API, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android).

Se tutti i componenti di una piattaforma sono installati, accanto al nome della piattaforma viene visualizzato un segno di spunta. Se non tutti i componenti di una piattaforma sono installati, la casella per la piattaforma è compilata. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Fare clic sulla casella **+** a sinistra della piattaforma per espandere una piattaforma e visualizzare i relativi componenti e i componenti installati.
Fare clic su **-** per comprimere l'elenco dei componenti di una piattaforma.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Fare clic sulla casella **freccia** a sinistra della piattaforma per espandere una piattaforma e visualizzare i relativi componenti e i componenti installati.
Fare clic su **freccia giù** per comprimere l'elenco dei componenti di una piattaforma.

-----

Per aggiungere un'altra piattaforma a SDK, selezionare la casella accanto alla piattaforma finché viene visualizzato il segno di spunta per installare tutti i relativi componenti, quindi fare clic su **Applica modifiche**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Esempio di aggiunta dei componenti di Android Nougat 7.1 ad Android SDK](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Esempio di aggiunta dei componenti di Android 4.4 ad Android SDK](android-sdk-images/mac/sdkmanager-12.png)

-----

Per installare solo SDK fare clic una sola volta sulla casella accanto alla piattaforma. È possibile selezionare i singoli componenti necessari:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Esempio di aggiunta di alcuni componenti di Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Esempio di aggiunta di alcuni componenti di Android 4.4](android-sdk-images/mac/sdkmanager-13.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Si noti che il numero di componenti da installare viene visualizzato accanto al pulsante **Applica modifiche**. Nell'esempio precedente sono sei i componenti pronti per l'installazione. Dopo aver fatto clic sul pulsante **Applica modifiche**, si aprirà la schermata **Accettazione della licenza**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Si noti che il numero di componenti da installare viene visualizzato accanto al pulsante **Applica modifiche**. Dopo aver fatto clic sul pulsante **Applica modifiche**, si aprirà la schermata **Accettazione della licenza**:

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Finestra di dialogo Accettazione della licenza della scheda Piattaforme](android-sdk-images/win/11-license-screen.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Finestra di dialogo Accettazione della licenza della scheda Piattaforme](android-sdk-images/mac/sdkmanager-14.png)

-----

Selezionare **Accetto** se si accettano i termini e le condizioni. È possibile che questa finestra di dialogo sia visualizzata più di una volta se i componenti da installare sono più di uno. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine del download e dell'installazione (l'operazione può richiedere molti minuti, a seconda del numero di componenti da scaricare), i componenti aggiunti vengono contrassegnati da un segno di spunta ed elencati con lo stato **Installato**.

A questo punto è possibile iniziare a sviluppare l'app per il livello API Android più recente e più elevato.


 
## <a name="summary"></a>Riepilogo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In questa guida è stato spiegato come installare e usare lo strumento Xamarin Android SDK Manager in Visual Studio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In questa guida è stato spiegato come usare lo strumento Xamarin Android SDK Manager in Visual Studio per Mac.

-----


## <a name="related-links"></a>Collegamenti correlati

- [Modifiche agli strumenti di Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
