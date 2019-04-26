---
title: 'App watchos: procedura dettagliata'
description: Questo documento fornisce una procedura dettagliata di creazione di un'applicazione semplice watchOS con Xamarin. Viene descritto come lavorare in Visual Studio e Visual Studio per Mac, lavorare con gli storyboard e rispondere agli eventi nel codice.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: de04a2d7f42ec36c464c75ced73bf8f8029ec1da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291062"
---
# <a name="hello-watchos--walkthrough"></a>App watchos: procedura dettagliata

Dopo aver creato una soluzione seguendo i passaggi descritti in [installazione e configurazione](~/ios/watchos/get-started/installation.md), sarà necessario 3 progetti:

- L'app iOS padre che viene usato per il programma di installazione o altre attività amministrative sul dispositivo. (Gli altri tipi di estensioni iOS, ciò è noto anche come l'app "Contenitore".) Con le app Watch, sarà possibile per gli utenti iniziare a eseguire l'App Watch senza **mai** in esecuzione l'app padre.
- L'estensione di espressioni di controllo che contiene il codice programma per l'app watch. e
- L'App Watch, che contiene le risorse dello storyboard e l'immagine che vengono eseguito il rendering nelle espressioni di controllo.

Verificare che il [riferimenti siano corretti](~/ios/watchos/get-started/project-references.md): che l'app padre contiene un riferimento all'estensione e che l'estensione contiene un riferimento all'App Watch.

Verificare che gli identificatori di Bundle di seguire le \*.watchkitextension \*.watchkitapp convenzione e file Info. plist dell'estensione risulta ha **ID Bundle di WKApp** valore impostato per l'identificatore del Bundle di l'App Watch.

Dovrebbe essere possibile eseguire l'App Watch a questo punto, ma poiché il file di storyboard all'interno dell'App Watch è vuoto, non sarà in grado di indicare.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "Esplora soluzioni")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "Esplora soluzioni")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
    
Fare doppio clic su Interface.storyboard nell'App Watch avviare Xamarin iOS Designer (se si usa un Mac è possibile anche fare doppio clic e **Apri con > Interface Builder Xcode**)


1.  Verificare che il **casella degli strumenti** e **proprietà** riquadri sono visibili,
1.  Fare clic per selezionare il Controller di interfaccia
1.  Impostare l'identificatore e il titolo del Controller dell'interfaccia **interfaceController** e **Hi Watch**,
1.  Verificare i **classe** è impostata su **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Impostare l'identificatore e il titolo del Controller di interfaccia su interfaceController ed espressioni di controllo di Hi")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Fare doppio clic su Interface.storyboard nell'App Watch per modificare il codice con Xamarin iOS Designer in Visual Studio:

1.  Aprire il riquadro proprietà.
1.  Modificare la classe in **InterfaceController**;
1.  Scegliere il Controller di interfaccia. e
1.  Impostare l'identificatore e il titolo del Controller dell'interfaccia **interfaceController** e **Hi Watch**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Impostare l'identificatore e il titolo del Controller di interfaccia su interfaceController ed espressioni di controllo di Hi")

-----


Creare l'interfaccia utente:

1. Dal **casella degli strumenti** tastierino,
1. Trascinare e rilasciare un **sul pulsante** e una **etichetta** nella scena, e
1. Impostare il testo e gli attributi dei controlli come illustrato:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "Impostare gli attributi dei controlli come illustrato e testo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "Impostare gli attributi dei controlli come illustrato e testo")

-----

1. Impostare il **nome** nel **proprietà** blocco note per ogni controllo. In questo esempio abbiamo utilizzato `myButton` e `myLabel`.

1. Selezionare il pulsante sullo storyboard e passare al **delle proprietà** pad **eventi** visualizzare un elenco, quindi

1. Creare una nuova **azione** digitando `OnButtonPress` e premere **invio**.
  L'azione verrà visualizzato nell'elenco e un metodo parziale viene automaticamente creato nella C#.

![](hello-watch-images/buttonaction.png "L'azione OnButtonPress aggiunta a un pulsante")

Dopo avere salvato lo storyboard, la **InterfaceController.designer.cs** viene aggiornato con i nomi dei controlli e le azioni... Se si apre questo file dopo l'aggiornamento, è possibile visualizzare il `RegisterAttribute` corrisponde al controller e come controlli dell'interfaccia utente corrispondono alle C# variabili di istanza contrassegnati con il `OutletAttribute` e come le azioni vengono mappate a metodi parziali contrassegnati con il `ActionAttribute`:

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

A questo punto aprire **InterfaceController.cs** (*non* InterfaceController.designer.cs) e aggiungere il codice seguente:

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Questo codice dovrebbe essere piuttosto trasparente: la variabile di istanza `clickCount` viene incrementato ogni volta che la funzione `OnButtonPress` viene chiamato. Il testo di `myLabel` viene modificato in modo da riflettere questo conteggio; `myLabel`, naturalmente, è il nome di una delle prese creato in XCode. Il `partial` funzione è l'implementazione della funzione associata al nome dell'azione specificato.

Se non è già il progetto di avvio,

1. Fare clic sul progetto di estensione di espressioni di controllo e scegliere **imposta come progetto di avvio**,

1. Impostare la destinazione di distribuzione di un'immagine di simulatore Kit compatibili con espressioni di controllo (ad esempio iPhone 6 iOS 8.2),

1. Premere il **Debug** pulsante per attivare un avvio di compilazione e del simulatore.

    [![](hello-watch-images/readytodebug-sml.png "Gli elementi di interfaccia di Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

Quando viene avviata nel simulatore, premere il pulsante per incrementare l'etichetta.
Complimenti, Stephen Toub un'App.

![](hello-watch-images/running.png "L'app in esecuzione nel simulatore")


## <a name="related-links"></a>Collegamenti correlati

- [Guida introduttiva (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Installazione e configurazione](~/ios/watchos/get-started/installation.md)
- [Primo video App Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
