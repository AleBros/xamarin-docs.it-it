---
title: Installazione e uso watchOS in Xamarin
description: Questo documento descrive come installare e usare watchOS con Xamarin. Viene descritto l'installazione, watchOS progetto struttura, come usare la finestra di progettazione iOS, l'integrazione di Xcode e vengono forniti suggerimenti sulla risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 12/05/2017
ms.openlocfilehash: 790e32131822e83f092b352b91b4461392150657
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768050"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Installazione e uso watchOS in Xamarin

watchOS 4 richiede macOS Sierra (10.12) con Xcode 9.

watchOS 1 richiesto originariamente OS X Yosemite (10.10) con Xcode 7.

> [!WARNING]
> [gli aggiornamenti di watchOS 1 non verranno accettati dopo il 1 aprile 2018](https://developer.apple.com/news/?id=11162017a). Gli aggiornamenti futuri devono usare watchOS 2 SDK o versione successiva. compilazione watchOS 4 SDK è consigliato.

## <a name="project-structure"></a>Struttura del progetto

Un'app è costituito da tre progetti:

- **Progetto di app xamarin. IOS per iPhone** -si tratta di un progetto di iPhone normale, può essere uno dei modelli di xamarin. IOS. L'App e l'estensione viene possibile raggruppare queste all'interno di questo progetto principale.

- **Progetto di estensione Watch** : contiene il codice, ad esempio le classi controller, per l'app Watch.

- **Progetto di app Watch** : contiene il file dello storyboard dell'interfaccia utente con tutte le risorse dell'interfaccia utente per l'app Watch.

Il [esempio Watch Kit Catalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) soluzione questo aspetto in xamarin Studio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](installation-images/catalog-solution.png "La soluzione in Visual Studio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "La soluzione in Visual Studio")

-----

Scaricare ed eseguire la [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) esempio per iniziare.
Le schermate dell'esempio sono reperibile nella [controlli](~/ios/watchos/user-interface/index.md) pagina.

## <a name="creating-a-new-project"></a>Creazione di un nuovo progetto

Non è possibile creare una nuova "Watch soluzione"... è invece possibile aggiungere un'App a un'applicazione iOS esistente. Seguire questi passaggi per creare un'app per le espressioni di controllo:

1. Se non si dispone di un progetto esistente, scegliere **File > nuova soluzione** e creare un'app per iOS (ad esempio, un **App visualizzazione singola**):

    [![](installation-images/cycle8-2-sml.png "Scegliere File > nuova soluzione e creare un'app per iOS")](installation-images/cycle8-2.png#lightbox)

2. Dopo aver creato l'app per iOS (o si prevede di usare app iOS esistente), fare clic sulla soluzione e scegliere **Aggiungi > Aggiungi nuovo progetto...** . Nel **nuovo progetto** finestra selezionare **watchOS > App > App WatchKit**:

    [![](installation-images/cycle8-6-sml.png "Selezionare watchOS > App > App WatchKit")](installation-images/cycle8-6.png#lightbox)

3. Nella schermata successiva consente di scegliere il progetto di app iOS deve includere l'app watch:

    [![](installation-images/cycle8-7-sml.png "Scegliere il progetto di app iOS deve includere l'app watch")](installation-images/cycle8-7.png#lightbox)

4. Infine, scegliere il percorso in cui salvare il progetto (e facoltativamente abilitare controllo del codice sorgente):

    [![](installation-images/cycle8-8-sml.png "Scegliere il percorso in cui salvare il progetto")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio per Mac configura automaticamente [i riferimenti al progetto e **Info. plist** impostazioni](~/ios/watchos/get-started/project-references.md) automaticamente.

## <a name="creating-the-watch-user-interface"></a>Creazione dell'interfaccia utente di espressioni di controllo

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Uso di Xamarin iOS Designer

Fare doppio clic su dell'app watch **Interface.storyboard** modificare usando iOS Designer. È possibile trascinare i controller di interfaccia e controlli dell'interfaccia utente lo storyboard nel **casella degli strumenti** e configurarle usando la **proprietà** riempimento:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "Lo storyboard nella finestra di progettazione")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "Lo storyboard nella finestra di progettazione")](installation-images/iosdesigner-vs.png#lightbox)

-----

È consigliabile assegnare ogni nuovo controller di interfaccia un **classe** selezionandolo e quindi immettendo il nome nel **delle proprietà** pad (verrà creata la C# codebehind file automaticamente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Assegnare ogni nuovo controller di interfaccia di una classe")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Assegnare ogni nuovo controller di interfaccia di una classe")

-----

Creare gli elementi segue dalla **Ctrl + trascinare** da un controller di pulsante, tabella o dell'interfaccia in un altro controller di interfaccia.

### <a name="using-xcode-on-the-mac"></a>Uso di Xcode nel Mac

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

È possibile continuare a usare Xcode per creare un'interfaccia utente facendo clic sul file Interface.storyboard e selezionando **Apri con > Interface Builder Xcode**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Gli utenti di Visual Studio possono anche usare Xcode per creare la propria interfaccia utente, passando da usare direttamente l'Host di compilazione Mac.
Aprire la soluzione in Visual Studio per Mac e quindi fare doppio clic sul file Interface.storyboard e selezionare **Apri con > Interface Builder Xcode**:

-----

![](installation-images/openwith-xcode.png "Aprire il Interface.storyboard in Interface Builder di Xcode")

Se si usa Xcode, è consigliabile seguire gli stessi passaggi per le app watch a quello normale [storyboard di app iOS](~/ios/user-interface/storyboards/index.md) (ad esempio la creazione Outlet e azioni da **Ctrl + trascinare** nel **. h**file di intestazione).

Quando si salva lo storyboard di Interface Builder di Xcode verranno automaticamente aggiunti la Outlet e azioni per cui si crea il C# **. designer.cs** file del progetto di estensione di espressioni di controllo.

### <a name="adding-additional-screens-in-xcode"></a>L'aggiunta di altre schermate in Xcode

Quando si aggiungono schermate aggiuntive (oltre che cosa è incluso nel modello per impostazione predefinita) allo storyboard usando Interface Builder di Xcode **è necessario aggiungere manualmente il C# file di codice** per ogni nuovo controller di interfaccia.

Vedere le [Advanced istruzioni su come aggiungere nuovi controller di interfaccia di uno storyboard](~/ios/watchos/troubleshooting.md#add).

*Xamarin iOS Designer esegue questa operazione automaticamente, richiedere passaggi manuali non sono necessari.*

## <a name="building"></a>Compilazione

Un progetto che include un'app viene compilato come altri progetti iOS. Il processo di compilazione comporterà un'applicazione iPhone (App) che contiene un'estensione di espressioni di controllo (.appex), che a sua volta contiene l'applicazione di espressioni di controllo senza codice (. app).

## <a name="launching"></a>L'avvio

È possibile avviare App watch nel simulatore mediante Visual Studio per Mac o Visual Studio (avvio nell'Host di compilazione Mac).

Sono disponibili due modalità per l'avvio di un'app WatchKit:

- modalità app normale (impostazione predefinita), e
- [Le notifiche](~/ios/watchos/platform/notifications.md) (che richiede un payload di notifica di test in formato JSON).

### <a name="xcode-8-support"></a>Supporto per Xcode 8

Dopo aver installato Xcode 8 (o versioni successive), simulatori di Apple Watch sono separati dai simulatori iOS (a differenza [Xcode 6](#xcode6), in cui vengono visualizzati come un' *display esterno*).
Quando si seleziona il progetto di App Watch e renderla il progetto di avvio, verrà visualizzato l'elenco dei simulatori *simulatori iOS* scegliere tra (come illustrato di seguito).

[![](installation-images/xs-xcode8-watchos3-sml.png "Selezione del tipo simulatore")](installation-images/xs-xcode8-watchos3.png#lightbox)

Quando si avvia Debug *due* simulatori deve iniziare - il simulatore iOS *e* il simulatore di Apple Watch. Usare **comando + MAIUSC + H** per passare al quadrante dell'orologio menu e l'orologio; e utilizzare il **Hardware** dal menu per impostare il **Force Touch pressione**. Lo scorrimento lungo il trackpad o passare il mouse simulerà utilizzando il Crown digitale.

#### <a name="troubleshooting"></a>Risoluzione dei problemi

Verrà visualizzato l'errore seguente nel **Output applicazione** se si tenta di avviare un simulatore che non dispone di una watch associato:

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Fare riferimento a [forum di Apple](https://forums.developer.apple.com/thread/7783) per istruzioni su come configurare i simulatori, se le impostazioni predefinite non funzionano.

<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 e watchOS 1

È necessario apportare le *progetto di estensione watch* il **progetto di avvio** prima dell'esecuzione o il debug dell'app. È possibile "start" la stessa app watch, e se si sceglie l'app per iOS quindi inizierà come di consueto nel simulatore iOS.

Per impostazione predefinita un'app viene avviata in modalità normale **app** modalità (Glance non o le notifiche) da Visual Studio per Mac **eseguito** oppure **Debug** comandi.

Quando si usa Xcode 6, solo l'iPhone 5, 5S iPhone, iPhone 6 e iPhone 6 Plus possono attivare il monitor esterno per uno **Apple Watch - 38mm** oppure **Apple Watch - 42mm** in cui le applicazioni di espressioni di controllo sarà visualizzato.

> [!NOTE]
> Tenere presente che la schermata di controllo non viene visualizzata automaticamente nel simulatore iOS quando si usa Xcode 6.
> Usare la **Hardware > Visualizza esterno** menu per visualizzare la schermata di espressioni di controllo.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>L'avvio in modalità di notifica

Vedere le [pagina notifiche](~/ios/watchos/platform/notifications.md) per informazioni come gestire le notifiche nel codice.

Visual Studio per Mac puoi avviare l'app watch con una notifica _modalità di avvio_ per le notifiche:

Pulsante destro del mouse sul progetto di app di espressioni di controllo e scegliere **eseguire con > configurazione personalizzata...** :

[![](installation-images/runwith-customparams-sml.png "Esecuzione di una configurazione personalizzata")](installation-images/runwith-customparams.png#lightbox)

Verrà visualizzata la **parametri personalizzati** finestra in cui è possibile selezionare **notifica** e fornire un payload JSON, quindi premere **eseguire** per avviare l'app watch nel simulatore:

[![](installation-images/runwith-execargs-sml.png "Impostazione di notifica e un Payload")](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>Debug

Il debug è supportato in Visual Studio per Mac e Visual Studio.
È necessario fornire un file JSON di notifica durante il debug in modalità notifiche. In questo screenshot appare un punto di interruzione di debug che colpiscono in un'app:

![](installation-images/debug-sml.png "In questo screenshot appare un punto di interruzione di debug che colpiscono in un'app watch")

Dopo aver seguito le istruzioni di avvio finirà con l'app watch in esecuzione sul **iOS Simulator (Guarda)** .
Per la modalità di notifica è possibile selezionare **Debug > Apri Log di sistema** (**CMD + /** ) e usare `Console.WriteLine` nel codice.

### <a name="debugging-lifecycle-event-handlers"></a>I gestori eventi del ciclo di vita di debug

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

I file di modello watchOS (ad esempio `InterfaceController`, `ExtensionDelegate`, `NotificationController`, e `ComplicationController`) forniti con i relativi metodi del ciclo di vita necessari già implementati. Aggiungere `Console.WriteLine` chiamate e leggere il **Output applicazione** per comprendere meglio il ciclo di vita di eventi.

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Primo video App Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Suggerimenti di Apple WatchKit](https://developer.apple.com/watchkit/tips/)
