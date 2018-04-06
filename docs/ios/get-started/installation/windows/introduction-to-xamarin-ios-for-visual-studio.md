---
title: Introduzione a Xamarin.iOS per Visual Studio
description: Questo articolo spiega come compilare e testare le applicazioni Xamarin iOS con Visual Studio. Verrà spiegato come usare Visual Studio per creare nuovi progetti iOS, compilare un'applicazione iOS e quindi compilare, testare ed eseguire il debug usando un Mac connesso alla rete per ospitare il compilatore e il simulatore di Apple e la toolchain di compilazione di Xamarin.
ms.prod: xamarin
ms.assetid: bf3c779f-959f-428d-babb-428f363f7e4e
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a8264d3ebd5f294b1b77fbbafd660825d5ce5180
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-xamarinios-for-visual-studio"></a>Introduzione a Xamarin.iOS per Visual Studio

_Questo articolo illustra come compilare e testare le applicazioni Xamarin iOS con Visual Studio. Viene spiegato come usare Visual Studio per creare nuovi progetti iOS, compilare un'applicazione iOS e quindi compilare, testare ed eseguire il debug usando un Mac connesso alla rete per ospitare il compilatore e il simulatore di Apple e la toolchain di compilazione di Xamarin._

Xamarin per Windows consente di scrivere e testare le applicazioni iOS all'interno di Visual Studio, con un Mac connesso alla rete che assicura il servizio di compilazione e distribuzione.

In questo articolo sono illustrate le procedure di installazione e configurazione degli strumenti di Xamarin.iOS in ogni computer per compilare le applicazioni iOS con Visual Studio.

Lo sviluppo per iOS in Visual Studio offre diversi vantaggi:

-  Creazione di soluzioni multipiattaforma per applicazioni iOS, Android e Windows.
-  Possibilità di usare gli strumenti preferiti di Visual Studio, ad esempio **ReSharper** e **Team Foundation Server**, per tutti i progetti multipiattaforma, incluso il codice sorgente di iOS.
-  Uso di un ambiente di sviluppo integrato familiare, sfruttando i data binding di Xamarin.iOS di tutte le API di Apple.


<a name="Requirements_and_Installation" />

## <a name="requirements-and-installation"></a>Requisiti e installazione

Esistono alcuni requisiti che devono essere rispettati durante lo sviluppo per iOS in Visual Studio. Come accennato brevemente nella panoramica, è necessario un computer Mac per compilare i file IPA e le applicazioni non possono essere distribuite in un dispositivo senza i certificati e gli strumenti per la firma del codice di Apple.

Sono disponibili alcune opzioni di configurazione, quindi è possibile scegliere quella più adatta alle proprie esigenze di sviluppo. Gli scenari possibili sono i seguenti:

-  Usare un Mac come computer di sviluppo principale ed eseguire una macchina virtuale Windows con Visual Studio installato. Si consiglia di usare software per macchina virtuale, ad esempio [Parallels](http://www.parallels.com/products/desktop/) o [VMWare](http://www.vmware.com/products/fusion/).
-  Usare un Mac solo come host di compilazione. In questo scenario verrebbe connesso alla stessa rete come computer Windows con gli strumenti [necessari](~/cross-platform/get-started/installation/windows.md#installation) installati.


In entrambi i casi è necessario seguire questi passaggi:

- [Installare gli strumenti Xamarin.iOS nell'host Mac](https://docs.microsoft.com/visualstudio/mac/installation)
- [Configurare il Mac](~/ios/get-started/installation/windows/index.md#configuring)
- [Installare gli strumenti di Xamarin in Windows](~/cross-platform/get-started/installation/windows.md)

Per lo sviluppo con Xamarin in Visual Studio, è necessario usare **almeno** Visual Studio 2015 Professional o versione successiva. Xamarin **non funziona** con le edizioni Express di Visual Studio, poiché non supporta i componenti aggiuntivi.

## <a name="connecting-to-the-mac"></a>Connessione al Mac

È possibile connettersi all'host di compilazione Mac usando l'icona sulla barra degli strumenti di Visual Studio (purché sia aperta un'applicazione iOS):

[![](introduction-to-xamarin-ios-for-visual-studio-images/xma1a.png "Icona di connessione al Mac")](introduction-to-xamarin-ios-for-visual-studio-images/xma1a.png#lightbox)

Oppure passare a **Strumenti > Opzioni** in Visual Studio e selezionare **Xamarin > Impostazioni iOS**:

 [![](introduction-to-xamarin-ios-for-visual-studio-images/xma-ios-options.png "Opzione iOS")](introduction-to-xamarin-ios-for-visual-studio-images/xma-ios-options.png#lightbox)

Per modificare l'host di compilazione Mac, fare clic sul pulsante **Trova Xamarin Mac Agent**. Per aggiornare l'host di compilazione Mac viene visualizzata questa schermata:

  [![](introduction-to-xamarin-ios-for-visual-studio-images/xma-dialog.png "Finestra di dialogo Xamarin Mac Agent")](introduction-to-xamarin-ios-for-visual-studio-images/xma-dialog.png#lightbox)


## <a name="visual-studio-toolbar-overview"></a>Panoramica della barra degli strumenti di Visual Studio

Xamarin iOS per Visual Studio aggiunge elementi alla barra degli strumenti standard e alla nuova barra degli strumenti iOS.
Le funzioni di queste barre degli strumenti sono illustrate di seguito.



### <a name="standard-toolbar"></a>Barra degli strumenti standard

I controlli relativi allo sviluppo con Xamarin iOS sono cerchiati in rosso:

 [![](introduction-to-xamarin-ios-for-visual-studio-images/03.png "I controlli relativi allo sviluppo con Xamarin iOS sono cerchiati in rosso")](introduction-to-xamarin-ios-for-visual-studio-images/03.png#lightbox "I controlli relativi allo sviluppo con Xamarin iOS sono cerchiati in rosso")

-  **Avvio**: avvia il debug o l'esecuzione dell'applicazione nella piattaforma selezionata. Deve essere presente un Mac connesso (vedere l'indicatore di stato nella barra degli strumenti iOS).
-  **Configurazioni soluzione**: consente di selezionare la configurazione da usare, ad esempio debug o rilascio.
-  **Piattaforme soluzione**: consente di selezionare iPhone o iPhoneSimulator per la distribuzione.


### <a name="ios-toolbar"></a>Barra degli strumenti iOS

La barra degli strumenti iOS in Visual Studio è simile in ogni versione di Visual Studio. Sono tutte riportate di seguito:

[![](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png "Barra degli strumenti iOS")](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png#lightbox)

Ogni elemento viene illustrato di seguito:

-  **Gestione connessione/Mac Agent**: consente di visualizzare la finestra di dialogo Xamarin Mac Agent. Questa icona è *arancione* mentre è in corso la connessione e *verde* quando è stabilita la connessione.
-  **Mostra il simulatore iOS** : visualizza la finestra del simulatore iOS in primo piano nel Mac.
-  **Mostra il file IPA nel server di compilazione**: apre il Finder nel Mac in corrispondenza del percorso del file di output IPA dell'applicazione.



## <a name="ios-output-options"></a>Opzioni di output iOS


### <a name="output-window"></a>Finestra di output

Nel riquadro *Output* sono disponibili opzioni che è possibile visualizzare per individuare errori e messaggi relativi a compilazione, distribuzione e connessione.

Nello screenshot seguente sono riportate le finestre di output disponibili, che possono variare a seconda del tipo di progetto:

[![](introduction-to-xamarin-ios-for-visual-studio-images/output-sml.png "Le finestre di output disponibili")](introduction-to-xamarin-ios-for-visual-studio-images/output-large.png#lightbox)

- **Xamarin**: contiene informazioni che riguardano esclusivamente Xamarin, ad esempio la connessione al Mac e lo stato di attivazione.

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output3-sml.png "Informazioni che riguardano esclusivamente Xamarin, ad esempio la connessione al Mac e lo stato di attivazione")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

- **Xamarin Diagnostics**: visualizza informazioni dettagliate sul progetto Xamarin, ad esempio l'interazione con e per Android.

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output4-sml.png "Informazioni dettagliate sul progetto Xamarin")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

Altri riquadri di output predefiniti di Visual Studio, ad esempio Debug e Build, sono disponibili all'interno della visualizzazione Output e vengono usati per l'output di debug e l'output di MSBuild:

-  **Debug**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output2-sml.png "Output di debug")](introduction-to-xamarin-ios-for-visual-studio-images/output2-large.png#lightbox)

- **Build** & **Ordine di compilazione**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output1-sml.png "Output di MSBuild")](introduction-to-xamarin-ios-for-visual-studio-images/output1-large.png#lightbox)


## <a name="ios-project-properties"></a>Proprietà del progetto iOS

Per accedere alle proprietà del progetto di Visual Studio, fare clic con il pulsante destro del mouse sul nome del progetto e selezionare *Proprietà* nel menu di scelta rapida. Ciò consente di configurare l'applicazione iOS, come illustrato nello screenshot seguente:


 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosproperties.png "Configurazione di un'applicazione iOS")

-  *Firma bundle iOS*: consente di connettersi al Mac per popolare le identità di firma del codice e i profili di provisioning:


 ![](introduction-to-xamarin-ios-for-visual-studio-images/bundlesigning.png "Popolare le identità di firma del codice e i profili di provisioning")

-  *Opzioni IPA iOS*: il file IPA verrà salvato nel file system del Mac:


 ![](introduction-to-xamarin-ios-for-visual-studio-images/ipaoptions.png "Opzioni IPA iOS")

-  *Opzioni esecuzione iOS*: configurare i parametri aggiuntivi:

 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosrunoptions.png "Opzioni di esecuzione iOS")



## <a name="creating-a-new-project-for-ios-applications"></a>Creazione di un nuovo progetto per applicazioni iOS

La creazione di un nuovo progetto iOS da Visual Studio viene eseguita esattamente come per qualsiasi altro tipo di progetto. Selezionando **File > Nuovo progetto** si apre la finestra di dialogo riportata di seguito, che illustra alcuni dei modelli disponibili per la creazione di un nuovo progetto iOS:


![](introduction-to-xamarin-ios-for-visual-studio-images/newproject.png "Creazione di un nuovo progetto")

I file storyboard e XIB possono essere modificati in Visual Studio usando iOS Designer. Per creare uno storyboard, scegliere uno dei modelli di storyboard. In questo modo viene generato un file **Main.storyboard** in **Esplora soluzioni** come illustrato nello screenshot riportato di seguito:

![](introduction-to-xamarin-ios-for-visual-studio-images/solution-explorer-new.png "File Main.storyboard in Esplora soluzioni")

Per avviare la creazione o la modifica dello storyboard, fare doppio clic su `Main.storyboard` per aprirlo in iOS Designer:

![](introduction-to-xamarin-ios-for-visual-studio-images/iosdesigner.png "Main.storyboard in iOS Designer")

Per aggiungere oggetti alla visualizzazione, usare il riquadro **Casella degli strumenti** per trascinare gli elementi nell'area di progettazione. Per aggiungere la casella degli strumenti, selezionare **Visualizza > Casella degli strumenti**, se non è già stata aggiunta. Per modificare le proprietà degli oggetti e i relativi layout e creare gli eventi, usare il riquadro **Proprietà** come illustrato di seguito:

![](introduction-to-xamarin-ios-for-visual-studio-images/properties.png "Riquadro Proprietà")

 Per altre informazioni sull'uso di iOS Designer, vedere le guide relative alla [finestra di progettazione](~/ios/user-interface/designer/index.md).


## <a name="running--debugging-ios-applications"></a>Esecuzione e debug delle applicazioni iOS

### <a name="device-logging"></a>Registrazione dispositivi

In Visual Studio 2015 e versioni successive i riquadri dei log di Android e iOS sono unificati

La finestra del nuovo strumento Log del dispositivo per Visual Studio consente di visualizzare i log per i dispositivi Android e iOS. È possibile visualizzarla eseguendo uno dei comandi seguenti:

- **Visualizza -> Altre finestre -> Log del dispositivo**
- **Strumenti > iOS > Log del dispositivo**
- **Barra degli strumenti iOS -> Log del dispositivo**

Quando viene visualizzata la finestra degli strumenti, l'utente può selezionare il dispositivo fisico dall'elenco a discesa dei dispositivi. Quando si seleziona un dispositivo, i log vengono automaticamente aggiunti alla tabella. Il passaggio da un dispositivo all'altro arresta e avvia la registrazione del dispositivo.

Affinché i dispositivi appaiano nella casella combinata, è necessario caricare un progetto iOS. Per iOS è anche necessario che Visual Studio sia [connesso al server Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per individuare i dispositivi iOS connessi al Mac.

La finestra dello strumento offre una tabella di voci di log, un elenco a discesa per la selezione dei dispositivi, la possibilità di cancellare le voci di log, una casella di ricerca e pulsanti di riproduzione/arresto/sospensione.


### <a name="set-debugging-stops"></a>Impostare interruzioni del debug

In qualsiasi punto dell'applicazione è possibile impostare punti di interruzione per segnalare al debugger di interrompere temporaneamente l'esecuzione del programma. Per impostare un punto di interruzione in Visual Studio, fare clic sull'area del margine nell'editor, accanto al numero di riga del codice dove si vuole applicare l'interruzione:

![](introduction-to-xamarin-ios-for-visual-studio-images/image18.png "Impostazione di un punto di debug")

Avviare il debug e usare il simulatore o il dispositivo per portare l'applicazione a un punto di interruzione. Quando viene raggiunto un punto di interruzione, la riga viene evidenziata e viene abilitato il normale comportamento di debug di Visual Studio: è possibile eseguire, ignorare o uscire dall'istruzione, esaminare le variabili locali o usare la finestra di controllo immediato.

In questo screenshot appare il simulatore iOS in esecuzione accanto a Visual Studio usando Parallels su OS X:


![](introduction-to-xamarin-ios-for-visual-studio-images/image19.png "In questo screenshot appare il simulatore iOS in esecuzione accanto a Visual Studio usando Parallels su OS X")

### <a name="examine-local-variables"></a>Esaminare le variabili locali

![](introduction-to-xamarin-ios-for-visual-studio-images/image20.png "Analisi delle variabili locali con il debug")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'uso di Xamarin iOS per Visual Studio. Sono state esaminate le funzionalità disponibili per la creazione, la compilazione e il test di un'applicazione iOS da Visual Studio e descritte in dettaglio le procedure di compilazione e debug di un'applicazione iOS semplice.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Xamarin.iOS](~/ios/get-started/installation/windows/index.md)
- [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)
- [Creazione di un'interfaccia utente di iOS nel codice](~/ios/app-fundamentals/ios-code-only.md)
- [Connessione di un Mac all'ambiente di Visual Studio in uso con XMA (video)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
