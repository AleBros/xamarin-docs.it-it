---
title: Installazione e uso watchOS in Xamarin
description: Questo documento descrive come installare e usare watchOS con Xamarin. Viene descritto l'installazione, watchOS progetto struttura, come usare la finestra di progettazione iOS, l'integrazione di Xcode e vengono forniti suggerimenti sulla risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: f986099011dbccb0eb43c62d253ee497d46ca08e
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304905"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Installazione e uso watchOS in Xamarin

watchOS 4 richiede macOS Sierra (10.12) con Xcode 9.

watchOS 1 richiesto originariamente OS X Yosemite (10.10) con Xcode 7.

> [!WARNING]
> [gli aggiornamenti watchos 1 non verranno accettati dopo il 1 ° aprile 2018](https://developer.apple.com/news/?id=11162017a). Gli aggiornamenti futuri devono usare watchOS 2 SDK o versione successiva. compilazione watchOS 4 SDK è consigliato.

## <a name="project-structure"></a>Struttura progetto

Un'app è costituito da tre progetti:

- **Progetto di app iPhone Novell. iOS** : si tratta di un normale progetto iPhone, che può essere uno qualsiasi dei modelli Novell. iOS. L'App e l'estensione viene possibile raggruppare queste all'interno di questo progetto principale.

- **Progetto di estensione Watch** : contiene il codice, ad esempio le classi controller, per l'app Watch.

- **Progetto di app Watch** : contiene il file dello storyboard dell'interfaccia utente con tutte le risorse dell'interfaccia utente per l'app Watch.

La soluzione di [esempio Watch Kit Catalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) ha un aspetto simile al seguente in Novell. studio:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![](installation-images/catalog-solution.png "The solution in Visual Studio")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "The solution in Visual Studio")

-----

Scaricare ed eseguire l'esempio [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) per iniziare.
Le schermate dell'esempio sono disponibili nella pagina [controlli](~/ios/watchos/user-interface/index.md) .

## <a name="creating-a-new-project"></a>Creazione di un nuovo progetto

Non è possibile creare una nuova "Watch soluzione"... è invece possibile aggiungere un'App a un'applicazione iOS esistente. Seguire questi passaggi per creare un'app per le espressioni di controllo:

1. Se non si dispone di un progetto esistente, prima scegliere **File > nuova soluzione** e creare un'app iOS (ad esempio, un' **app visualizzazione singola**):

    [![](installation-images/cycle8-2-sml.png "Choose File > New Solution and create an iOS app")](installation-images/cycle8-2.png#lightbox)

2. Una volta creata l'app iOS (o si prevede di usare l'app iOS esistente), fare clic con il pulsante destro del mouse sulla soluzione e scegliere **aggiungi > Aggiungi nuovo progetto.** Nella finestra **nuovo progetto** selezionare **watchos > app > app WatchKit**:

    [![](installation-images/cycle8-6-sml.png "Select watchOS > App > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. Nella schermata successiva consente di scegliere il progetto di app iOS deve includere l'app watch:

    [![](installation-images/cycle8-7-sml.png "Choose which iOS app project should include the watch app")](installation-images/cycle8-7.png#lightbox)

4. Infine, scegliere il percorso in cui salvare il progetto (e facoltativamente abilitare controllo del codice sorgente):

    [![](installation-images/cycle8-8-sml.png "Choose the location to save the project")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio per Mac configura automaticamente i [riferimenti al progetto e le impostazioni **info. plist** ](~/ios/watchos/get-started/project-references.md) .

## <a name="creating-the-watch-user-interface"></a>Creazione dell'interfaccia utente di espressioni di controllo

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Uso di Xamarin iOS Designer

Fare doppio clic sull'interfaccia dell'app Watch **. Storyboard** per modificare usando iOS designer. È possibile trascinare i controller di interfaccia e i controlli dell'interfaccia utente nello storyboard dalla **casella degli strumenti** e configurarli usando il riquadro delle **Proprietà** :

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "The storyboard in the Designer")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "The storyboard in the Designer")](installation-images/iosdesigner-vs.png#lightbox)

-----

È necessario assegnare a ogni nuovo controller di interfaccia una **classe** selezionandola e quindi immettendo il nome nel riquadro delle **Proprietà** (in questo modo C# verranno creati automaticamente i file codebehind necessari):

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Give each new interface controller a Class")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Give each new interface controller a Class")

-----

Creare gli elementi segue premendo **CTRL + trascinamento** da un pulsante, una tabella o un controller di interfaccia su un altro controller di interfaccia.

### <a name="using-xcode-on-the-mac"></a>Uso di Xcode nel Mac

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

È possibile continuare a usare Xcode per compilare l'interfaccia utente facendo clic con il pulsante destro del mouse sul file Interface. Storyboard e selezionando **Apri con > xcode Interface Builder**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Gli utenti di Visual Studio possono anche usare Xcode per creare la propria interfaccia utente, passando da usare direttamente l'Host di compilazione Mac.
Aprire la soluzione in Visual Studio per Mac, quindi fare clic con il pulsante destro del mouse sul file Interface. Storyboard e scegliere **Apri con > Xcode Interface Builder**:

-----

![](installation-images/openwith-xcode.png "Open the Interface.storyboard in Xcode Interface Builder")

Se si usa Xcode, è necessario seguire la stessa procedura per le app Watch come per gli [storyboard delle app iOS](~/ios/user-interface/storyboards/index.md) normali, ad esempio la creazione di Outlet e azioni con **CTRL + trascinamento** nel file di intestazione con **estensione h** .

Quando si salva lo storyboard in Xcode Interface Builder verranno aggiunti automaticamente gli Outlet e le azioni creati C# nei file con estensione **Designer.cs** nel progetto di estensione Watch.

### <a name="adding-additional-screens-in-xcode"></a>L'aggiunta di altre schermate in Xcode

Quando si aggiungono schermate aggiuntive (oltre a quanto incluso nel modello per impostazione predefinita) allo Storyboard usando Xcode Interface Builder **è necessario aggiungere manualmente C# i file di codice** per ogni nuovo controller di interfaccia.

Vedere le [istruzioni avanzate su come aggiungere nuovi controller di interfaccia a uno storyboard](~/ios/watchos/troubleshooting.md#add).

*Questa operazione viene eseguita automaticamente da Novell iOS designer, non sono necessari passaggi manuali.*

## <a name="building"></a>Compilazione

Un progetto che include un'app viene compilato come altri progetti iOS. Il processo di compilazione comporterà un'applicazione iPhone (App) che contiene un'estensione di espressioni di controllo (.appex), che a sua volta contiene l'applicazione di espressioni di controllo senza codice (. app).

## <a name="launching"></a>L'avvio

È possibile avviare App watch nel simulatore mediante Visual Studio per Mac o Visual Studio (avvio nell'Host di compilazione Mac).

Sono disponibili due modalità per l'avvio di un'app WatchKit:

- modalità app normale (impostazione predefinita), e
- [Notifiche](~/ios/watchos/platform/notifications.md) (che richiedono un payload di notifica di prova in formato JSON).

### <a name="xcode-8-support"></a>Supporto per Xcode 8

Una volta installato Xcode 8 (o versione successiva), i simulatori di Apple Watch sono separati dai simulatori iOS (a differenza di [Xcode 6](#xcode6), in cui appaiono come *display esterni*).
Quando si seleziona il progetto di app Watch e lo si imposta come progetto di avvio, l'elenco simulatore mostrerà i *simulatori iOS* tra cui scegliere, come illustrato di seguito.

[![](installation-images/xs-xcode8-watchos3-sml.png "Selecting the Simulator type")](installation-images/xs-xcode8-watchos3.png#lightbox)

Quando si avvia il debug, verranno avviati *due* simulatori, il simulatore iOS *e* il simulatore di Apple Watch. Usare il **comando + MAIUSC + H** per passare al menu espressioni di controllo e al quadrante dell'orologio; e usare il menu **hardware** per impostare la **pressione del Force Touch**. Lo scorrimento lungo il trackpad o passare il mouse simulerà utilizzando il Crown digitale.

#### <a name="troubleshooting"></a>Risoluzione dei problemi

L'errore seguente verrà visualizzato nell' **output dell'applicazione** se si tenta di avviare un simulatore che non dispone di un controllo associato:

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Per istruzioni sulla configurazione dei simulatori, vedere i [Forum di Apple](https://forums.developer.apple.com/thread/7783) , se le impostazioni predefinite non funzionano.

<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 e watchOS 1

È necessario fare in modo che l' *estensione Watch Proietti* il **progetto di avvio** prima di eseguire o eseguire il debug dell'app. È possibile "start" la stessa app watch, e se si sceglie l'app per iOS quindi inizierà come di consueto nel simulatore iOS.

Per impostazione predefinita, **un'app Watch** viene avviata in modalità normale (non visualizzazione o modalità di notifica) dai comandi di **esecuzione** o **debug** di Visual Studio per Mac.

Quando si usa Xcode 6, solo iPhone 5, iPhone 5S, iPhone 6 e iPhone 6 Plus possono attivare lo schermo esterno per **Apple Watch-38** o **Apple Watch-42** , in cui verranno visualizzate le applicazioni Watch.

> [!NOTE]
> Tenere presente che la schermata di controllo non viene visualizzata automaticamente nel simulatore iOS quando si usa Xcode 6.
> Usare il menu **Hardware > External Displays** per visualizzare la schermata espressioni di controllo.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>L'avvio in modalità di notifica

Per informazioni su come gestire le notifiche nel codice, fare riferimento alla [pagina notifiche](~/ios/watchos/platform/notifications.md) .

Visual Studio per Mac possibile avviare l'app Watch con le _modalità di avvio_ delle notifiche per le notifiche:

Fare clic con il pulsante destro del mouse sul progetto Watch app e scegliere **Esegui con > configurazione personalizzata...** :

[![](installation-images/runwith-customparams-sml.png "Running a Custom Configuration")](installation-images/runwith-customparams.png#lightbox)

Verrà visualizzata la finestra **parametri personalizzati** in cui è possibile selezionare **notifica** (e fornire un payload JSON), quindi premere **Esegui** per avviare l'app Watch nel simulatore:

[![](installation-images/runwith-execargs-sml.png "Setting the Notification and Payload")](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>Debug

Il debug è supportato in Visual Studio per Mac e Visual Studio.
È necessario fornire un file JSON di notifica durante il debug in modalità notifiche. In questo screenshot appare un punto di interruzione di debug che colpiscono in un'app:

![](installation-images/debug-sml.png "This screenshot shows a debug breakpoint being hit in a watch app")

Dopo aver seguito le istruzioni di avvio, l'app Watch viene eseguita nel **simulatore iOS (Watch)** .
Per la modalità di notifica è possibile selezionare **Debug > aprire Registro di sistema** (**cmd +/** ) e usare `Console.WriteLine` nel codice.

### <a name="debugging-lifecycle-event-handlers"></a>I gestori eventi del ciclo di vita di debug

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

I file di modello watchos, ad esempio `InterfaceController`, `ExtensionDelegate`, `NotificationController`e `ComplicationController`, sono dotati dei metodi del ciclo di vita necessari già implementati. Aggiungere `Console.WriteLine` le chiamate e leggere l' **output dell'applicazione** per comprendere meglio il ciclo di vita dell'evento.

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Video dell'app First Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Suggerimenti WatchKit Apple](https://developer.apple.com/watchkit/tips/)
