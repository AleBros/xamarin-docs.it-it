---
title: watchOS interfaccia utente
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: 0717b8484c6094bb1d9589c44df37745d9e21900
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="watchos-user-interface"></a>watchOS interfaccia utente

Il [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) illustra vari controlli watchOS. Storyboard dell'app è illustrato di seguito (fare clic per ingrandire):

[![](images/storyboard-sml.png "Layout di esempio watchOS")](images/storyboard.png#lightbox)

I nomi di tutti i controlli a livello di codice è preceduto da `WKInterface` (ad es. `WKInterfaceLabel`, `WKInterfaceButton`).


<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
        <strong>Controllo</strong>
      </th>
      <th>
        <strong>Descrizione</strong>
      </th>
      <th>
        <strong>schermata di</strong>
      </th>
    </thead>
    <tbody>
    <tr>
      <td valign="top">
Label </td>
      <td valign="top">
Utilizzare <code>SetText</code> e altre proprietà per controllare l'aspetto del testo in un controllo etichetta. <code>NSAttributedString</code> è anche supportato.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/label.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Button </td>
      <td valign="top">
Creare e impostare le proprietà dello storyboard. <kbd>CTRL + trascinare</kbd> per aggiungere un <code>Action</code> per implementare un gestore per quando si fa clic.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/button.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Opzione </td>
      <td valign="top">
Utilizzare <code>SetOn</code> per controllare lo stato del commutatore.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/switch.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Slider </td>
      <td valign="top">
Molti stili diversi sono possibili.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/slider.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Image </td>
      <td valign="top">
Utilizzare <code>myImage.SetImage("MyWatchImage")</code> per caricare immagini in espressioni di controllo o <code>WKInterfaceDevice.CurrentDevice.AddCachedImage</code> memorizzarli nella cache per un utilizzo ripetuto l'orologio.
        <br />
        <a href="~/ios/watchos/user-interface/image.md">Documentazione di controllo Image</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/image.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Separatore </td>
      <td valign="top">
Utilizzare i separatori per creare espressioni di controllo elegante le interfacce utente.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/separator.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Mappa </td>
      <td valign="top">
Immagine mappa viene visualizzato in modo statico nelle espressioni di controllo, ma è possibile controllare numerosi aspetti dell'aspetto, inclusa l'aggiunta di pin.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/map.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Film & InlineMove </td>
      <td valign="top">
Film può aprire in modo autonomo o inline <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/movie.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Gruppo </td>
      <td valign="top">
Utilizzare i gruppi consentono di creare espressioni di controllo elegante le interfacce utente.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/group.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Table </td>
      <td valign="top">
Una versione semplificata di tabelle in iOS.
Implementare <code>DidSelectRow</code> per rispondere alla selezione dell'utente (o utilizzare un segue).
        <br />
        <a href="~/ios/watchos/user-interface/table.md">Documentazione di controllo di tabella</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TableDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/table.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Dispositivo </td>
      <td valign="top">
        <code>WKInterfaceDevice.CurrentDevice</code> include le proprietà, ad esempio <code>ScreenBounds</code>, <code>ScreenScale</code>, e <code>PreferredContentSizeCategory</code>.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/device.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="~/ios/watchos/user-interface/menu.md">Menu</a>
      </td>
      <td valign="top">
Definire il menu force premere dello storyboard e implementare le azioni per ogni pulsante nel codice.
        <br />
        <a href="~/ios/watchos/user-interface/menu.md">Documentazione di controllo (Force Touch) dal menu</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/controller.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Input di testo </td>
      <td valign="top">
Utilizzare <code>PresentTextInputController</code> e <code>WKTextInputMode</code> enumerazione.
        <br />
        <a href="~/ios/watchos/user-interface/text-input.md">Documentazione di Input di testo</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/textinput.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Chioma digitale </td>
      <td valign="top">
Il chioma digitale può essere usata per un selettore di unità o la rotazione è possibile tenere traccia nel codice.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/digital-crown.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Movimenti </td>
      <td valign="top">
Sono disponibili quattro tipi di riconoscimento di movimento che possono essere aggiunti a una scena: Tap, scorrere, panoramica e LongPress.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs">Codice del catalogo</a>
      </td>
      <td>
        <img src="Images/gestures.png" class="tableimg">
      </td>
    </tr>
    </tbody>
</table>



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Riferimento all'API Kit di espressioni di controllo](https://developer.xamarin.com/api/namespace/WatchKit/)
