---
title: Installazione di Xamarin.iOS in Windows
description: In questo articolo viene illustrato come impostare Xamarin.iOS per Visual Studio. Viene descritto il processo di installazione per l'estensione Xamarin per Visual Studio e la connessione ad Apple SDK installato sul Mac.
ms.topic: article
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/29/2017
ms.openlocfilehash: e3416b0551bafcd7ab87b21eafbe56b733f359a4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="installing-xamarinios-on-windows"></a>Installazione di Xamarin.iOS in Windows

_Questo articolo illustra come configurare Xamarin.iOS per Visual Studio. Viene descritto il processo di installazione per l'estensione Xamarin per Visual Studio e la connessione ad Apple SDK installato nel Mac._

## <a name="overview"></a>Panoramica

Xamarin.iOS per Visual Studio consente di scrivere e testare le applicazioni iOS nei computer Windows, con un Mac connesso alla rete che assicura il servizio di compilazione e distribuzione.

Lo sviluppo per iOS in Visual Studio offre la possibilità di:

- Creare soluzioni multipiattaforma per applicazioni iOS, Android e Windows.
- Usare gli strumenti di Visual Studio, ad esempio *ReSharper* e *Team Foundation Server*, per tutti i progetti multipiattaforma, incluso il codice sorgente di iOS.
- Usare un ambiente di sviluppo integrato familiare, sfruttando i data binding di Xamarin.iOS di tutte le API di Apple

Xamarin.iOS per Visual Studio supporta le configurazioni in cui Visual Studio viene eseguito all'interno di una macchina virtuale Windows su un Mac (con Parallels o VMWare), oppure quando è installato in un computer separato, visibile nella stessa rete come Mac. Indipendentemente dalla configurazione adatta per l'utente, Visual Studio si connetterà al Mac immediatamente e in modo sicuro tramite SSH.

In questo articolo vengono illustrati i passaggi per installare e configurare gli strumenti di Xamarin.iOS nei computer Mac e Windows, nonché i passaggi per la connessione all'host Mac per permettere agli sviluppatori di compilare, eseguire il debug e distribuire applicazioni Xamarin.iOS con Visual Studio.

Il diagramma seguente offre una semplice panoramica del flusso di lavoro di sviluppo di Xamarin.iOS:

[![Flusso di lavoro di sviluppo di Xamarin.iOS](images/xma2.png)](images/xma2.png)

> [!IMPORTANT]
>  Visual Studio avvia un processo MSBuild separato per compilare i progetti. Questo processo crea una nuova connessione al Mac, quindi esistono effettivamente due connessioni SSH da Windows a Mac durante la compilazione di Visual Studio. La compilazione dalla [riga di comando](~/ios/get-started/installation/windows/connecting-to-mac/index.md) crea un unico processo MSBuild. Per semplificare il diagramma, tutte le connessioni sono rappresentate da una sola freccia.

## <a name="requirements"></a>Requisiti

Xamarin.iOS per Visual Studio svolge una funzione straordinaria: consente agli sviluppatori di creare, compilare ed eseguire il debug di applicazioni iOS in un computer Windows tramite l'ambiente di sviluppo integrato (IDE) di Visual Studio. Ma non può fare tutto questo da solo. Le applicazioni iOS non possono infatti essere create senza il compilatore Apple e non possono essere distribuite senza i certificati e gli strumenti di firma del codice Apple. Questo significa che l'installazione di Xamarin.iOS per Visual Studio richiede una connessione a un computer Mac OS X connesso a una rete per poter eseguire queste attività. Una volta configurati, gli strumenti Xamarin semplificheranno al massimo il processo.


<a name="system-requirements">

### <a name="system-requirements"></a>Requisiti di sistema

I requisiti di sistema sono i seguenti:

### <a name="windows"></a>WINDOWS

1. Windows 7 o versioni successive.

2. Visual Studio 2015 Professional o versione successiva

    a. Se si dispone di una licenza Enterprise, è necessario installare Visual Studio Enterprise.

3. Xamarin per Visual Studio.

Gli strumenti Xamarin non possono essere usati con le edizioni Express di Visual Studio a causa della mancanza del supporto per il plug-in. Xamarin è supportato nella Community di Visual Studio.

### <a name="mac"></a>Mac

1. Un Mac che esegue macOS Sierra (10.12) o versione successiva (benché sia consigliata la versione stabile più recente).

2. Xamarin iOS SDK. Viene installato durante il download di Visual Studio per Mac

3. IDE di Apple Xcode e iOS SDK (è consigliabile installare la versione più recente del canale Stable disponibile nell'App Store Mac).

**Il computer Windows deve essere in grado di connettersi al Mac attraverso la rete.**

<a name="appleaccount" />

### <a name="apple-developer-account"></a>Account per sviluppatore Apple

Per distribuire applicazioni in un dispositivo o inviarle all'App Store, è necessario un account per sviluppatori Apple. I certificati di sviluppatore pertinenti e i profili di provisioning devono essere creati e installati nel Mac connesso alla rete prima di poter usare Xamarin.iOS per Visual Studio. Vedere l'articolo [Provisioning di dispositivi](~/ios/get-started/installation/device-provisioning/index.md) per i passaggi destinati a ottenere un certificato di sviluppo e per eseguire il provisioning di dispositivi.

<a name="features" />

## <a name="features"></a>Funzionalità 

Xamarin.iOS per Visual Studio consente la creazione, modifica, compilazione e distribuzione di progetti Xamarin.iOS da Windows. e include le funzionalità seguenti:

- Creare nuovi progetti iOS.

- Modificare progetti iOS e soluzioni multipiattaforma che includono anche progetti Xamarin.Android e UWP.

- Compilare progetti iOS e soluzioni multipiattaforma che includono anche progetti Xamarin.Android e UWP.

- Storyboard e .xib supporta l'utilizzo di iOS Designer.

- Distribuire ed eseguire il debug di applicazioni iOS, in cui l'app viene eseguita in un simulatore o in un dispositivo connesso al Mac.

- Simulatore iOS in Windows: per ulteriori informazioni sull'utilizzo del simulatore iOS in Windows, fare riferimento a [questa guida](~/tools/ios-simulator.md).

<a name="configuring" />

## <a name="configuring-your-mac"></a>Configurazione del Mac


### <a name="installation"></a>Installazione

Per installare gli strumenti Xamarin.iOS nell'host mac, è necessario [installare Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation).

Dopo aver installato il software, seguire i passaggi nelle sezioni successive per configurare Xamarin.iOS su macOS per consentire a Xamarin per Visual Studio di connettersi a questo.

> [!IMPORTANT]
>  Il computer Windows deve usare la stessa versione di Xamarin.iOS del Mac a cui è connesso. Per verificare che sia così:
>
> - **Visual Studio 2015 e versioni precedenti**: assicurarsi di trovarsi nello stesso [canale aggiornamenti](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) di Visual Studio per Mac.
>
> - **Visual Studio 2017, versione di rilascio**: assicurarsi di trovarsi nel canale **stabile** di Visual Studio per Mac.
>
> - **Visual Studio 2017, versione di rilascio**: assicurarsi di trovarsi nel canale **alfa** di Visual Studio per Mac.

<a name="configuration" />


### <a name="configuration"></a>Configurazione

Per accedere alla comunicazione tra l'estensione Xamarin per Visual Studio e il Mac è necessario consentire **l'accesso remoto** sul Mac. Per impostarlo, seguire questa procedura:

1. Aprire *Spotlight* (**Cmd-BARRA SPAZIATRICE**), cercare **Login remoto** e selezionare il risultato **Condivisione**. Verrà aperta la finestra **Preferenze di sistema** nel pannello **Condivisione**.

![Ricerca Spotlight per l'accesso remoto](images/spotlight.png)

2. Selezionare l'opzione **Login remoto** nell'elenco **Servizio** sulla sinistra per consentire a Xamarin per Visual Studio di connettersi a Mac.

![Selezionare l'opzione Login remoto nell'elenco Servizio](images/sharing.png)

3. Assicurarsi che in **Login remoto** sia impostata l'opzione **All users** (Tutti gli utenti), oppure che il nome utente o il gruppo Mac sia incluso nell'elenco di utenti consentiti sulla destra.

A questo punto il Mac dovrebbe essere individuabile da Visual Studio se si trova nella stessa rete.

> [!NOTE]
> Se il firewall macOS è impostato per poter bloccare le applicazioni firmate per impostazione predefinita, potrebbe essere necessario consentire a `mono-sgen` di ricevere connessioni in entrata. In tal caso verrà visualizzata una finestra di dialogo con un avviso.

<a name="developersetup"/>

### <a name="ios-developer-setup"></a>Configurazione per sviluppatori iOS

Per lo sviluppo di iOS, è importante che il computer Mac sia configurato con le identità di firma pertinenti. Ciò consente di firmare correttamente le app in modo che possano essere distribuite nell'App Store o Ad Hoc. Seguire il collegamento seguente per istruzioni su come configurare un Mac per lo sviluppo di iOS con Xamarin:

- [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md?ide=vs)

Dopo aver configurato il Mac, è necessario configurare il computer Windows.

<a name="windowsinstallation">

## <a name="windows-installation"></a>Installazione di Windows

Xamarin può essere installato durante l'installazione di Visual Studio 2017 o 2015. Per installare gli strumenti di Visual Studio per Xamarin, vedere la guida [Installazione di Windows](~/cross-platform/get-started/installation/windows.md).

## <a name="installation-complete"></a>Installazione completata

Al termine del processo di installazione, esistono alcuni altri passaggi necessari per assicurarsi che tutto funzioni:

- [Connettere Visual Studio al Mac](#connectingtomac): Visual Studio deve essere connesso all'host di compilazione Mac prima di poter compilare progetti iOS.
- [Configurare la barra degli strumenti di Visual Studio](#Visual_Studio_Toolbar_Overview) – Ciò consente di accedere facilmente alle funzionalità di Xamarin.iOS in Visual Studio.

<a name="connectingtomac" /> 

### <a name="connecting-to-the-mac"></a>Connessione al Mac

Viene eseguita una connessione da Xamarin.iOS per Visual Studio all'host di compilazione Mac tramite una connessione SSH tra computer. Per altre informazioni sulla connessione, vedere la guida [Connessione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

Per connettersi al Mac, seguire i passaggi seguenti:

- Passare a **Strumenti > Opzioni** e in **Xamarin** selezionare **Impostazioni iOS**:

  [![Schermata delle impostazioni iOS](images/image2.png)](images/image2.png)

- A condizione che il Mac sia stato correttamente [configurato](#configuration) per consentire **l'accesso remoto**, dovrebbe essere possibile selezionare il Mac nell'elenco:

  [![Finestra di dialogo Host remoto](images/xma3.png)](images/xma3.png)

- Questo richiederà le credenziali amministrative dell'host del Mac:

  [![Finestra di dialogo di accesso](images/xma4.png)](images/xma4.png)

- Una volta connessi, verrà visualizzata l'icona 'Connessione riuscita' accanto al nome del computer:

  [![Nella finestra di dialogo Host remoto è visualizzata l'icona Connessione riuscita accanto al nome del computer](images/image6.png)](images/image6.png)

Si verrà riconnessi ogni volta che si avvia Visual Studio.

<a name="toolbar" />

## <a name="visual-studio-toolbar-configuration"></a>Configurazione della barra degli strumenti di Visual Studio

Quando viene aperto un progetto iOS, la barra degli strumenti di iOS sarà visibile per impostazione predefinita e non dovrà essere configurata.

Se la barra degli strumenti di iOS non viene visualizzata, si possono usare i passaggi seguenti.

Per configurare la barra degli strumenti, aprire innanzitutto il menu **Visualizza > Barre degli strumenti** e assicurarsi che sia selezionata la voce **iOS**. Scegliere la voce di menu, come indicato in questa schermata; deve essere selezionata per indicare che la barra degli strumenti è visibile:

[![Scegliere Barre degli strumenti > iOS](images/image31.png)](images/image31.png)

### <a name="visual-studio-2015"></a>Visual Studio 2015

Nelle versioni precedenti a Visual Studio 2017, può essere necessario aggiungere il pulsante **Piattaforme soluzione** alla barra degli strumenti Standard. In questo modo è possibile selezionare un dispositivo iOS o il simulatore iOS durante il debug. Attenersi alle istruzioni riportate di seguito per impostarlo

Fare clic sul pulsante di menu a destra della barra degli strumenti Standard:

- Scegliere **Aggiungi o rimuovi pulsanti**
- Selezionare **Piattaforme soluzione**

[![Selezionare Piattaforma soluzione](images/image35.png)](images/image35.png)

Le barre degli strumenti **Standard** e **iOS** dovrebbero essere simili a questa schermata:

[![Le barre degli strumenti Standard e iOS dovrebbero essere simili a questa schermata](images/image36.png)](images/image36.png)

Una volta completata la configurazione della barra degli strumenti, si è pronti per iniziare a usare Xamarin iOS per Visual Studio.

## <a name="summary"></a>Riepilogo

In questo articolo è presentata una guida dettagliata per l'installazione, la configurazione e l'utilizzo di Xamarin iOS per Visual Studio.

Sono state trattate l'installazione e la configurazione degli strumenti prerequisiti in Windows e Mac OS X.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione](~/cross-platform/get-started/installation/windows.md)
- [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)
- [Introduzione a Xamarin.iOS per Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Connessione di un Mac all'ambiente di Visual Studio in uso con XMA (video)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
