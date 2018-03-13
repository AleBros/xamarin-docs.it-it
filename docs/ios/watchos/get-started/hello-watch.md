---
title: Hello, espressioni di controllo
description: Introduzione a Xamarin e watchOS
ms.topic: article
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/14/2016
ms.openlocfilehash: 732fd9808ded4bf97e99e7ab0e6ab63b989452d1
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="hello-watch"></a>Hello, espressioni di controllo

Dopo aver creato una soluzione seguendo i passaggi descritti in [installazione e configurazione](~/ios/watchos/get-started/installation.md), sarà necessario 3 progetti:

- L'app iOS padre che viene utilizzato per l'installazione o altre attività amministrative sul dispositivo. (Gli altri tipi di estensioni di iOS, questo è noto anche come app "Container".) Con le app di espressioni di controllo, sarà possibile per gli utenti di avviare l'esecuzione dell'applicazione di espressioni di controllo senza **mai** esecuzione dell'applicazione padre;
- L'estensione di espressioni di controllo che contiene il codice di programma per l'applicazione di espressioni di controllo; e
- Applicazione di espressioni di controllo, che contiene le risorse di storyboard e l'immagine che vengono eseguito il rendering nell'orologio.

Verificare che il [riferimenti siano corretti](~/ios/watchos/get-started/project-references.md): che l'app padre ha un riferimento all'estensione e che l'estensione dispone di un riferimento all'App di espressioni di controllo.

Confermare che seguono il Bundle di identificatori di \*.watchkitextension \*.watchkitapp convenzione e file Info. plist dell'estensione risulta ha **ID Bundle WKApp** valore impostato per l'identificatore Bundle di l'applicazione di espressioni di controllo.

Dovrebbe essere possibile eseguire l'App di espressioni di controllo a questo punto, ma poiché il file dello storyboard all'interno dell'App di espressioni di controllo è vuoto, non sarà in grado di indicare.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](hello-watch-images/projectstructure.png "Esplora soluzioni")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-projectstructure.png "Esplora soluzioni")

-----

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
Fare doppio clic su Interface.storyboard nell'App espressioni di controllo per avviare Xamarin iOS progettazione (se si usa un Mac è possibile anche fare doppio clic e **Apri con > Xcode interfaccia generatore**)


1.  Verificare che il **della casella degli strumenti** e **proprietà** Pad sono visibili,
1.  Fare clic per selezionare il Controller di interfaccia
1.  Impostare l'identificatore e il titolo del Controller di interfaccia **interfaceController** e **Hi Watch**,
1.  Verificare il **classe** è impostato su **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Impostare l'identificatore e il titolo del Controller di interfaccia interfaceController ed espressioni di controllo di Hi")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Fare doppio clic su Interface.storyboard nell'App espressioni di controllo per modificare con Xamarin iOS progettazione di Visual Studio:

1.  Aprire il riquadro proprietà.
1.  Modificare la classe in **InterfaceController**;
1.  Fare clic su Controller di interfaccia. e
1.  Impostare l'identificatore e il titolo del Controller di interfaccia **interfaceController** e **Hi Watch**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Impostare l'identificatore e il titolo del Controller di interfaccia interfaceController ed espressioni di controllo di Hi")

-----


Creare un'interfaccia utente:

1. Dal **della casella degli strumenti** , riempimento
1. Trascinare e rilasciare un **pulsante** e **etichetta** nella scena, e
1. Impostare il testo e gli attributi dei controlli come illustrato:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](hello-watch-images/draganddrop.png "Impostare gli attributi dei controlli come illustrato e testo")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-draganddrop.png "Impostare gli attributi dei controlli come illustrato e testo")

-----

1. Impostare il **nome** nel **proprietà** riempimento per ogni controllo. In questo esempio è stata utilizzata la `myButton` e `myLabel`.

1. Selezionare il pulsante nello storyboard e passare al **proprietà** pad **eventi** elenco, quindi

1. Creare un nuovo **azione** digitando `OnButtonPress` e premendo **invio**.
  L'azione verrà visualizzato nell'elenco, e verrà automaticamente creato un metodo parziale in c#.

![](hello-watch-images/buttonaction.png "L'azione OnButtonPress aggiunto a un pulsante")

Dopo aver salvato lo storyboard, il **InterfaceController.designer.cs** viene aggiornata con i nomi dei controlli e le azioni... Se si apre questo file dopo l'aggiornamento, è possibile visualizzare come `RegisterAttribute` corrisponde al controller e come i controlli dell'interfaccia utente corrispondono alle variabili di istanza c# contrassegnate con il `OutletAttribute` e mappano di azioni per i metodi parziali contrassegnati con il `ActionAttribute`:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

Aprire quindi **InterfaceController.cs** (*non* InterfaceController.designer.cs) e aggiungere il codice seguente:

```csharp
int clickCount = 0;

partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}

```

Questo codice deve essere abbastanza trasparente: la variabile di istanza `clickCount` viene incrementato ogni volta che la funzione `OnButtonPress` viene chiamato. Il testo di `myLabel` viene modificato in modo da riflettere questo conteggio; `myLabel`, naturalmente, è il nome di una delle prese creato in XCode. Il `partial` funzione è l'implementazione della funzione associata al nome dell'azione specificato.

Se non è già il progetto di avvio

1. Fare clic sul progetto di estensione delle espressioni di controllo e scegliere **imposta come progetto di avvio**,

1. Impostare la destinazione di distribuzione a un'immagine di simulatore compatibile Kit espressioni di controllo (ad esempio iPhone 6 iOS 8.2),

1. Premere il **Debug** pulsante per attivare il lancio di un simulatore e di compilazione.

    [![](hello-watch-images/readytodebug-sml.png "Elementi dell'interfaccia di Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

Quando viene avviata nel simulatore, premere il pulsante per incrementare l'etichetta.
Complimenti, hai manualmente un'applicazione di espressioni di controllo.

![](hello-watch-images/running.png "L'app in esecuzione nel simulatore")


## <a name="related-links"></a>Collegamenti correlati

- [Guida introduttiva (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Installazione e configurazione](~/ios/watchos/get-started/installation.md)
- [Primo video App Watch](http://blog.xamarin.com/your-first-watch-kit-app/)
