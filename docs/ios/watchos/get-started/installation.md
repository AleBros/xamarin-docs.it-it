---
title: Installazione e configurazione
description: Impostazione per lo sviluppo per watchOS
ms.topic: article
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/05/2017
ms.openlocfilehash: c423c9bf49c735673793f8e61134f7e705816d54
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="installation"></a>Installazione

watchOS 4 richiede macOS Sierra (10.12) con Xcode 9.

watchOS 1 necessario originariamente OS X Yosemite (10.10) con Xcode 7.

> [!WARNING]
> [non verranno accettati watchOS 1 aggiornamenti dopo il 1 aprile 2018](https://developer.apple.com/news/?id=11162017a). Gli aggiornamenti futuri devono utilizzare 2 watchOS SDK o versione successiva. compilazione con il watchOS è consigliabile SDK 4.

## <a name="project-structure"></a>Struttura del progetto

Un'applicazione di espressioni di controllo è costituito da tre progetti:

- **Progetto di app iPhone xamarin** -si tratta di un progetto normale iPhone, può essere uno dei modelli di xamarin. IOS. L'applicazione di espressioni di controllo e l'estensione verrà essere aggregate all'interno di questo progetto principale.

- **Progetto di estensione delle espressioni di controllo** -che contiene il codice (ad esempio classi Controller) per l'applicazione di espressioni di controllo.

- **Progetto di applicazione delle espressioni di controllo** -che contiene il file di interfaccia utente dello storyboard a tutte le risorse dell'interfaccia utente per l'applicazione di espressioni di controllo.

Il [esempio Watch Kit Catalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) soluzione è simile al seguente in Xamarin.Studio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](installation-images/catalog-solution.png "La soluzione in Visual Studio")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/catalog-solution-vs.png "La soluzione in Visual Studio")

-----

Scaricare ed eseguire il [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) esempio per iniziare.
Schermate dell'esempio sono reperibile nel [controlli](~/ios/watchos/user-interface/index.md) pagina.


## <a name="creating-a-new-project"></a>Creazione di un nuovo progetto

Non è possibile creare una nuova "Watch soluzione"... ma è possibile aggiungere un'applicazione di espressioni di controllo a un'applicazione iOS esistente. Seguire questi passaggi per creare un'applicazione di espressioni di controllo:

1. Se non si dispone di un progetto esistente, scegliere **File > nuova soluzione** e creare un'app iOS (ad esempio, un **singola vista App**):

    [![](installation-images/cycle8-2-sml.png "Scegliere File > nuova soluzione e creare un'app iOS")](installation-images/cycle8-2.png#lightbox)

2. Una volta creato l'app iOS (o si prevede di usare app iOS esistente), pulsante destro del mouse sulla soluzione e scegliere **Aggiungi > Aggiungi nuovo progetto...** . Nel **nuovo progetto** finestra selezionare **watchOS > App > App WatchKit**:

    [![](installation-images/cycle8-6-sml.png "Selezionare watchOS > App > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. Nella schermata successiva consente di scegliere il progetto di app iOS deve includere l'applicazione di espressioni di controllo:

    [![](installation-images/cycle8-7-sml.png "Scegliere il progetto di app iOS deve includere l'applicazione di espressioni di controllo")](installation-images/cycle8-7.png#lightbox)

4. Infine, scegliere il percorso in cui salvare il progetto (e facoltativamente abilitato controllo del codice sorgente):

    [![](installation-images/cycle8-8-sml.png "Scegliere il percorso in cui salvare il progetto")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio per Mac configura automaticamente [i riferimenti al progetto e **Info. plist** impostazioni](~/ios/watchos/get-started/project-references.md) automaticamente.

## <a name="creating-the-watch-user-interface"></a>Creazione dell'interfaccia utente di espressioni di controllo

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Utilizzando la finestra di progettazione di iOS Xamarin

Fare doppio clic sul controllo dell'app **Interface.storyboard** modificare utilizzando la finestra di progettazione iOS. È possibile trascinare i controller di interfaccia e i controlli dell'interfaccia utente dello storyboard dal **della casella degli strumenti** e configurarli utilizzando il **proprietà** riempimento:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](installation-images/iosdesigner-sml.png "Nella finestra di progettazione dello storyboard")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](installation-images/iosdesigner-sml-vs.png "Nella finestra di progettazione dello storyboard")](installation-images/iosdesigner-vs.png#lightbox)

-----

È consigliabile assegnare ogni nuovo controller di interfaccia un **classe** selezionandolo e quindi immettere il nome di **proprietà** riempimento (Crea file il codebehind c# necessari automaticamente):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](installation-images/iosdesigner-classname.png "Assegnare ogni nuovo controller di interfaccia di una classe")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/iosdesigner-classname-vs.png "Assegnare ogni nuovo controller di interfaccia di una classe")

-----

Creare segues da **Ctrl + trascinare** da un pulsante, tabella o dell'interfaccia controller in un altro controller di interfaccia.


### <a name="using-xcode-on-the-mac"></a>Usando Xcode nel Mac

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

È possibile continuare a utilizzare Xcode per compilare un'interfaccia utente facendo clic sul file Interface.storyboard e selezionando **Apri con > Xcode interfaccia generatore**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Gli utenti di Visual Studio consente inoltre di creare la propria interfaccia utente tramite passando a utilizzare direttamente l'Host di compilazione Mac Xcode.
Aprire la soluzione in Visual Studio per Mac e quindi fare doppio clic sul file Interface.storyboard e selezionare **Apri con > Xcode interfaccia generatore**:

-----

![](installation-images/openwith-xcode.png "Aprire il Interface.storyboard in Xcode interfaccia generatore")

In Xcode, è necessario seguire gli stessi passaggi per le app di espressioni di controllo come normale [storyboard app iOS](~/ios/user-interface/storyboards/index.md) (ad esempio la creazione di punti vendita e le azioni da **Ctrl + trascinare** nel **h**file di intestazione).

Quando si salva lo storyboard in Generatore di interfaccia di Xcode, verranno aggiunte automaticamente i punti vendita e le azioni create a c# **. designer.cs** file nel progetto di estensione delle espressioni di controllo.


### <a name="adding-additional-screens-in-xcode"></a>Aggiunta di schermate aggiuntive in Xcode

Quando si aggiungono altre schermate (eccedente quello incluso nel modello per impostazione predefinita) a dello storyboard utilizzando il generatore di interfaccia Xcode **è necessario aggiungere manualmente i file di codice c#** per ogni nuovo controller di interfaccia.

Consultare la [avanzate istruzioni su come aggiungere nuovi controller di interfaccia di uno storyboard](~/ios/watchos/troubleshooting.md#add).

*Finestra di progettazione iOS Xamarin viene eseguito automaticamente, non manuali sono necessari passaggi.*


## <a name="building"></a>Compilazione

Un progetto che include un'applicazione di espressioni di controllo Compila come altri progetti iOS. Il processo di compilazione comporterà iPhone applicazioni (App) che contiene un'estensione di espressioni di controllo (.appex), che a sua volta contiene l'applicazione delle espressioni di controllo senza codice (. app).


## <a name="launching"></a>Avvio

È possibile avviare le app di espressioni di controllo nel simulatore mediante Visual Studio per Mac o Visual Studio (Avvia nell'Host di compilazione Mac).

Sono disponibili due modalità per l'avvio di un'app WatchKit:

 - modalità di applicazione normale (impostazione predefinita), e
- [Le notifiche](~/ios/watchos/platform/notifications.md) (che richiede un payload di notifica di prova in formato JSON).

### <a name="xcode-8-support"></a>Supporto Xcode 8

Una volta Xcode 8 (o versione successiva) installato, simulatori Apple Watch sono separati da iOS simulatori (a differenza di [Xcode 6](#xcode6), in cui vengono visualizzati come un *display esterno*).
Quando si seleziona progetto di applicazione delle espressioni di controllo e verificare il progetto di avvio, verrà visualizzato l'elenco dei simulatori *iOS simulatori* da selezionare (come illustrato di seguito).

[![](installation-images/xs-xcode8-watchos3-sml.png "Selezione del tipo di simulatore")](installation-images/xs-xcode8-watchos3.png#lightbox)

Quando si avvia il debug, *due* simulatori deve iniziare - simulatore iOS *e* il simulatore di Apple Watch. Utilizzare **comando + MAIUSC + H** per spostarsi verso l'espressione di controllo menu e l'orologio; e utilizzare il **Hardware** menu per impostare il **Force tocco pressione**. Lo scorrimento lungo il mouse o la trackpad simulerà utilizzando il chioma digitale.

#### <a name="troubleshooting"></a>Risoluzione dei problemi

Verrà visualizzato il seguente errore nella **Output dell'applicazione** se si tenta di avvio per un simulatore che non dispone di un'espressione di controllo associati:

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Fare riferimento a [forum di Apple](https://forums.developer.apple.com/thread/7783) per istruzioni sulla configurazione simulatori, se le impostazioni predefinite non funzionano.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 e watchOS 1

È necessario apportare la *progetto di estensione delle espressioni di controllo* il **progetto di avvio** prima dell'esecuzione o di debug dell'applicazione. È possibile "start" app stessa espressione di controllo, e se si sceglie l'app iOS quindi verrà avviato come di consueto nel simulatore iOS.

Per impostazione predefinita viene avviata un'app di espressioni di controllo normale **app** modalità (non immediatamente o le notifiche) da Visual Studio per Mac **eseguire** o **Debug** comandi.

Quando si utilizza Xcode 6, solo l'iPhone 5, 5S iPhone, iPhone 6 e iPhone 6 Plus può attivare la visualizzazione esterna per uno **Apple Watch - 38mm** o **Apple Watch - 42mm** in cui le applicazioni di espressioni di controllo è visualizzato.

**Nota:** tenere presente che le espressioni di controllo non viene automaticamente visualizzata nel simulatore iOS quando si utilizza Xcode 6.
Utilizzare il **Hardware > Visualizza esterno** menu per visualizzare la schermata di espressioni di controllo.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Modalità di notifica di avvio

Fare riferimento al [pagina notifiche](~/ios/watchos/platform/notifications.md) informazioni come gestire le notifiche nel codice.


Visual Studio per Mac è possibile avviare l'applicazione di espressioni di controllo con una notifica _modalità di avvio_ per le notifiche:



Fare clic sul progetto di applicazione delle espressioni di controllo e scegliere **eseguire con > configurazione personalizzata...** :


[![](installation-images/runwith-customparams-sml.png "Esecuzione di una configurazione personalizzata")](installation-images/runwith-customparams.png#lightbox)


Verrà visualizzata la **parametri personalizzati** finestra in cui è possibile selezionare **notifica** e fornire un payload JSON, quindi premere **eseguire** per avviare l'applicazione di espressioni di controllo nel simulatore:


[![](installation-images/runwith-execargs-sml.png "Impostazione di notifica e un Payload")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>Debug

Il debug è supportato in Visual Studio per Mac e Visual Studio.
È necessario fornire un file JSON di notifica quando il debug in modalità notifiche. Questa schermata è riportato un punto di interruzione di debug viene raggiunto in un'applicazione di espressioni di controllo:

![](installation-images/debug-sml.png "Questa schermata è riportato un punto di interruzione di debug viene raggiunto in un'applicazione di espressioni di controllo")

Dopo aver seguito le istruzioni di avvio si finirà con l'applicazione di espressioni di controllo in esecuzione sul **(controllo) simulatore iOS**.
Per la modalità di notifica è possibile selezionare **Debug > Apri Registro di sistema** (**CMD + /**) e utilizzare `Console.WriteLine` nel codice.

### <a name="debugging-lifecycle-event-handlers"></a>I gestori eventi del ciclo di vita di debug

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

I file di modello watchOS (ad esempio `InterfaceController`, `ExtensionDelegate`, `NotificationController`, e `ComplicationController`) forniti con i relativi metodi del ciclo di vita necessari già implementate. Aggiungere `Console.WriteLine` chiamate e lettura di **Output dell'applicazione** per comprendere meglio il ciclo di vita di eventi.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Primo video App Watch](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Suggerimenti WatchKit di Apple](https://developer.apple.com/watchkit/tips/)
