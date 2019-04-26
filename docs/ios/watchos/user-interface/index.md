---
title: Controlli dell'interfaccia utente in Xamarin watchOS
description: Questo documento descrive i vari controlli che sono disponibili per l'uso nelle interfacce utente watchOS. Fornisce una descrizione di etichette, i pulsanti, commutatori, i dispositivi di scorrimento, immagini, i separatori, mappe e altro.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2016
ms.openlocfilehash: c8ef76f24b017f5e3e6bec9d39534f3626e79147
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892995"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Controlli dell'interfaccia utente in Xamarin watchOS

Il [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) esempio vengono illustrati i vari controlli di watchOS. Storyboard dell'app è illustrato di seguito (fare clic per ingrandire):

[![](images/storyboard-sml.png "Layout di esempio watchOS")](images/storyboard.png#lightbox)

I nomi di tutti i controlli a livello di codice è preceduto `WKInterface` (ad es. `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Descrizione|Schermata|
|---|---|---|
|Label|Usare `SetText` e altre proprietà per controllare l'aspetto del testo in un controllo etichetta. `NSAttributedString` è anche supportato.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|Creare e impostare le proprietà nello storyboard. CTRL + trascinare per aggiungere un `Action` per implementare un gestore per quando si fa.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Opzione|Usare `SetOn` per controllare lo stato del commutatore.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|Sono possibili diversi stili.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Image|Usare `myImage.SetImage("MyWatchImage")` per caricare immagini in espressioni di controllo o `WKInterfaceDevice.CurrentDevice.AddCachedImage` memorizzarli nella cache per l'uso ripetuto sulle espressioni di controllo.<br />[Documentazione sul controllo immagine](~/ios/watchos/user-interface/image.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separatore|Usare i separatori per creare espressioni di controllo delle interfacce utente accattivanti.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Mappa|Immagine mappa viene visualizzato in modo statico sulle espressioni di controllo, ma è possibile controllare numerosi aspetti dell'aspetto della forma, inclusa l'aggiunta di pin.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Movie & InlineMove|Film possono aprire in modo autonomo o inline<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Raggruppa|Usare gruppi per creare espressioni di controllo delle interfacce utente accattivanti.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Tabella|Una versione semplificata di tabelle in iOS. Implementare `DidSelectRow` per rispondere alla selezione dell'utente (o usare un elemento segue).<br />[Documentazione sul controllo tabella](~/ios/watchos/user-interface/table.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` include le proprietà, ad esempio `ScreenBounds`, `ScreenScale`, e `PreferredContentSizeCategory`.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Definire il menu force-premere nello storyboard e implementare le azioni per ogni pulsante nel codice.<br />[Documentazione relativa al controllo (Force Touch) dal menu](~/ios/watchos/user-interface/menu.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Input di testo|Uso `PresentTextInputController` e il `WKTextInputMode` enumerazione.<br />[Documentazione di Input di testo](~/ios/watchos/user-interface/text-input.md)<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Corona digitale|Il Crown digitale può essere utilizzata per guidare una selezione o la rotazione è possibile tenere traccia nel codice.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Movimenti|Esistono quattro tipi di riconoscimento di movimento che possono essere aggiunti a una scena: TAP, scorrere rapidamente, zoom e LongPress.<br />[Codice del catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Riferimento all'API di espressioni di controllo Kit](xref:WatchKit)
