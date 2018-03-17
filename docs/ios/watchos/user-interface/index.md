---
title: watchOS interfaccia utente
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: f7a7b565372970cc487b664ed7415cf876e290b6
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="watchos-user-interface"></a>watchOS interfaccia utente

Il [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) illustra vari controlli watchOS. Storyboard dell'app è illustrato di seguito (fare clic per ingrandire):

[![](images/storyboard-sml.png "Layout di esempio watchOS")](images/storyboard.png#lightbox)

I nomi di tutti i controlli a livello di codice è preceduto da `WKInterface` (ad es. `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Descrizione|Schermata|
|---|---|---|
|Label|Utilizzare `SetText` e altre proprietà per controllare l'aspetto del testo in un controllo etichetta. `NSAttributedString` è anche supportato.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|Creare e impostare le proprietà dello storyboard. CTRL + trascinare per aggiungere un `Action` per implementare un gestore per quando si fa clic.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Opzione|Utilizzare `SetOn` per controllare lo stato del commutatore.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|Molti stili diversi sono possibili.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Image|Utilizzare `myImage.SetImage("MyWatchImage")` per caricare immagini in espressioni di controllo o `WKInterfaceDevice.CurrentDevice.AddCachedImage` memorizzarli nella cache per un utilizzo ripetuto l'orologio.<br />[Documentazione di controllo Image](~/ios/watchos/user-interface/image.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separatore|Utilizzare i separatori per creare espressioni di controllo elegante le interfacce utente.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Mappa|Immagine mappa viene visualizzato in modo statico nelle espressioni di controllo, ma è possibile controllare numerosi aspetti dell'aspetto, inclusa l'aggiunta di pin.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Film & InlineMove|Film può aprire in modo autonomo o inline<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Gruppo|Utilizzare i gruppi consentono di creare espressioni di controllo elegante le interfacce utente.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Table|Una versione semplificata di tabelle in iOS. Implementare `DidSelectRow` per rispondere alla selezione dell'utente (o utilizzare un segue).<br />[Documentazione di controllo di tabella](~/ios/watchos/user-interface/table.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` include le proprietà, ad esempio `ScreenBounds`, `ScreenScale`, e `PreferredContentSizeCategory`.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Definire il menu force premere dello storyboard e implementare le azioni per ogni pulsante nel codice.<br />[Documentazione di controllo (Force Touch) dal menu](~/ios/watchos/user-interface/menu.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Input di testo|Utilizzare `PresentTextInputController` e `WKTextInputMode` enumerazione.<br />[Documentazione di Input di testo](~/ios/watchos/user-interface/text-input.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Chioma digitale|Il chioma digitale può essere usata per un selettore di unità o la rotazione è possibile tenere traccia nel codice.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Movimenti|Sono disponibili quattro tipi di riconoscimento di movimento che possono essere aggiunti a una scena: Tap, scorrere, panoramica e LongPress.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Riferimento all'API Kit di espressioni di controllo](https://developer.xamarin.com/api/namespace/WatchKit/)
