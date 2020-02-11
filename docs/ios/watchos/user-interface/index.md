---
title: Controlli dell'interfaccia utente watchos in Xamarin
description: Questo documento descrive i vari controlli disponibili per l'uso nelle interfacce utente watchos. Fornisce una descrizione di etichette, pulsanti, commutatori, dispositivi di scorrimento, immagini, separatori, mappe e altro ancora.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: 8836eafbbce30586116fd7a7b125da55fe6edf8e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032713"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Controlli dell'interfaccia utente watchos in Xamarin

L'esempio [**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) illustra diversi controlli watchos. Lo storyboard dell'app è visualizzato qui (fare clic per ingrandire):

[![](images/storyboard-sml.png "Sample watchOS layout")](images/storyboard.png#lightbox)

I nomi a livello di codice di tutti i controlli sono preceduti dal prefisso `WKInterface` (ad esempio `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Descrizione|Schermata|
|---|---|---|
|Label|Utilizzare `SetText` e altre proprietà per controllare l'aspetto del testo in un controllo etichetta. è supportata anche `NSAttributedString`.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|Creare e impostare le proprietà nello storyboard. Premere Ctrl + trascina per aggiungere un `Action` per implementare un gestore quando viene selezionato.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Opzione|Usare `SetOn` per controllare lo stato dell'opzione.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|Sono possibili molti stili diversi.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Immagine|Usare `myImage.SetImage("MyWatchImage")` per caricare immagini nell'espressione di controllo o `WKInterfaceDevice.CurrentDevice.AddCachedImage` per memorizzarle nella cache per l'uso ripetuto nell'orologio.<br />[Documentazione del controllo immagine](~/ios/watchos/user-interface/image.md)<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separator|Usare i separatori per semplificare la creazione di interfacce utente di controllo accattivanti.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Mappa|L'immagine della mappa viene visualizzata in modo statico nell'espressione di controllo, ma è possibile controllare molti aspetti dell'aspetto, inclusa l'aggiunta di pin.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Film & InlineMove|I filmati possono essere aperti autonomamente o inline<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Raggruppa|Usare i gruppi per semplificare la creazione di interfacce utente di controllo accattivanti.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Table|Una versione semplificata delle tabelle in iOS. Implementare `DidSelectRow` per rispondere alla selezione dell'utente (o usare un segue).<br />[Documentazione del controllo Table](~/ios/watchos/user-interface/table.md)<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` include proprietà quali `ScreenBounds`, `ScreenScale`e `PreferredContentSizeCategory`.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Definire il menu Force-Press nello storyboard e implementare le azioni per ogni pulsante nel codice.<br />[Documentazione del controllo menu (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Input di testo|Usare `PresentTextInputController` e l'enumerazione `WKTextInputMode`.<br />[Documentazione di input di testo](~/ios/watchos/user-interface/text-input.md)<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Digital Crown|Il Digital Crown può essere usato per guidare una selezione o la rotazione può essere rilevata nel codice.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Movimenti|Sono disponibili quattro tipi di riconoscimento dei movimenti che possono essere aggiunti a una scena: Tap, swipe, Pan e LongPress.<br />[Codice catalogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Riferimento all'API del kit di controllo](xref:WatchKit)
