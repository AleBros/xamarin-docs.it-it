---
title: Hello, watchos-procedura dettagliata
description: Questo documento fornisce una procedura dettagliata per la creazione di una semplice applicazione watchos con Novell. Viene descritto come lavorare sia in Visual Studio che in Visual Studio per Mac, utilizzare gli storyboard e rispondere agli eventi nel codice.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: dba84c32390be8017952206de9e23facd58931fe
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199040"
---
# <a name="hello-watchos--walkthrough"></a>Hello, watchos-procedura dettagliata

Dopo aver creato una soluzione seguendo i passaggi descritti in [installazione e installazione](~/ios/watchos/get-started/installation.md), si avranno 3 progetti:

- App padre iOS utilizzata per l'installazione o altre attività amministrative del dispositivo. (Con altri tipi di estensioni iOS, questo viene spesso definito app "container"). Con le app Watch, è possibile che gli utenti inizino a eseguire l'app Watch senza **mai** eseguire l'app padre.
- Estensione Watch che contiene il codice programma per l'app Watch; e
- L'app Watch, che include le risorse storyboard e image di cui viene eseguito il rendering nell'espressione di controllo.

Verificare che i [riferimenti siano corretti](~/ios/watchos/get-started/project-references.md): che l'app padre includa un riferimento all'estensione e che l'estensione includa un riferimento all'app Watch.

Verificare che gli identificatori del bundle seguano \*la \*convenzione. watchkitextension. watchkitapp e che il file INFO. plist dell'estensione disponga del valore di **ID bundle WKApp** impostato sull'identificatore del bundle dell'app Watch.

Dovrebbe essere possibile eseguire l'app Watch adesso, ma poiché il file dello storyboard nell'app Watch è vuoto, non sarà possibile dirlo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "Esplora soluzioni")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "Esplora soluzioni")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Fare doppio clic sull'interfaccia. Storyboard nell'app Watch per avviare Novell iOS designer. Se si è in un Mac, è anche possibile fare clic con il pulsante destro del mouse e **aprire con > Xcode Interface Builder**)


1. Verificare che i rilievi della **casella degli strumenti** e delle **Proprietà** siano visibili,
1. Fare clic per selezionare il controller di interfaccia,
1. Impostare l'identificatore e il titolo del controller di interfaccia su **interfaceController** e **Hi Watch**,
1. Verificare che la **classe** sia impostata su **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Impostare l'identificatore e il titolo del controller di interfaccia su interfaceController e Hi Watch")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Fare doppio clic sull'interfaccia. Storyboard nell'app Watch per modificare con Novell iOS designer in Visual Studio:

1. Aprire il riquadro proprietà;
1. Modificare la classe in **InterfaceController**;
1. Fare clic sul controller di interfaccia; e
1. Impostare l'identificatore e il titolo del controller di interfaccia su **interfaceController** e **Hi Watch**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Impostare l'identificatore e il titolo del controller di interfaccia su interfaceController e Hi Watch")

-----


Creare l'interfaccia utente:

1. Dal riquadro **casella degli strumenti** ,
1. Trascinare e rilasciare un **pulsante** e un' **etichetta** nella scena e
1. Impostare il testo e gli attributi dei controlli come illustrato:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "Imposta il testo e gli attributi dei controlli come illustrato")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "Imposta il testo e gli attributi dei controlli come illustrato")

-----

1. Impostare il **nome** nel riquadro delle **Proprietà** per ogni controllo. In questo esempio sono stati usati `myButton` e `myLabel`.

1. Selezionare il pulsante nello storyboard e passare all'elenco di **eventi** del riquadro **Proprietà** , quindi

1. Per creare una nuova **azione** , `OnButtonPress` digitare e premere **invio**.
  L'azione verrà visualizzata nell'elenco e un metodo parziale verrà creato automaticamente in C#.

![](hello-watch-images/buttonaction.png "Azione OnButtonPress aggiunta a un pulsante")

Dopo aver salvato lo storyboard, il **InterfaceController.designer.cs** viene aggiornato con i nomi e le azioni del controllo. Se si apre questo file dopo che è stato aggiornato, è possibile vedere in `RegisterAttribute` che modo corrisponde al controller e in che modo i C# controlli dell'interfaccia utente corrispondono `OutletAttribute` alle variabili di istanza contrassegnate con il e il modo in cui le azioni vengono mappate ai metodi parziali contrassegnati con `ActionAttribute`:

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

Aprire ora **InterfaceController.cs** (*non* InterfaceController.designer.cs) e aggiungere il codice seguente:

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Questo codice deve essere piuttosto trasparente: la variabile `clickCount` di istanza viene incrementata ogni volta che viene chiamata la funzione. `OnButtonPress` Il testo di `myLabel` viene modificato per riflettere questo conteggio; `myLabel`naturalmente, è il nome di uno degli Outlet creati in Xcode. La `partial` funzione è l'implementazione della funzione associata al nome dell'azione specificata.

Se non è già il progetto di avvio,

1. Fare clic con il pulsante destro del mouse sul progetto di estensione Watch e scegliere **Imposta come progetto di avvio**.

1. Impostare la destinazione di distribuzione su un'immagine del simulatore compatibile con il kit di controllo (ad esempio iPhone 6 iOS 8,2).

1. Premere il pulsante **debug** per attivare la compilazione e l'avvio del simulatore.

    [![](hello-watch-images/readytodebug-sml.png "Elementi dell'interfaccia di Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

Quando viene avviato il simulatore, premere il pulsante per incrementare l'etichetta.
Congratulazioni, è disponibile un'app Watch.

![](hello-watch-images/running.png "L'app in esecuzione nel simulatore")


## <a name="related-links"></a>Collegamenti correlati

- [Introduzione (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gettingstarted)
- [Installazione e configurazione](~/ios/watchos/get-started/installation.md)
- [Primo video App Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
