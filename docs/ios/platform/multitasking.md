---
title: Multitasking per iPad in xamarin. IOS
description: iOS 9 supporta due delle applicazioni in esecuzione contemporaneamente, mediante diapositiva su o doppia visualizzazione. Supporta inoltre la riproduzione di immagini In Picture video.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 7eacd9ece067d2ddf6363c0551055daa3df4433a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787955"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitasking per iPad in xamarin. IOS

_iOS 9 supporta due delle applicazioni in esecuzione contemporaneamente, mediante diapositiva su o doppia visualizzazione. Supporta inoltre la riproduzione di immagini In Picture video._

![](multitasking-images/about02-sml.png "Suddividere l'esempio di schermata") ![](multitasking-images/about03-sml.png "in PAP esempio")

iOS 9 aggiunge il supporto di multitasking per le due applicazioni in esecuzione nello stesso momento su iPad specifiche hardware. Il multitasking per iPad è supportato tramite le funzionalità seguenti:

- [**Diapositiva su** ](#Slide-Over) -consente all'utente eseguire temporaneamente un'app iOS di secondo in una diapositiva out pannello (sia sul lato destro o sinistro dello schermo in base alla direzione language) che copre circa il 25% dell'app principale attualmente in esecuzione. Sposta su è disponibile solo in un iPad Pro, iPad Air, iPad 2 aria, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.
- [**Doppia visualizzazione** ](#Split-View) -sull'hardware supportato iPad (iPad aria 2, 4 Mini iPad e iPad Pro solo), l'utente può selezionare un'app secondo ed eseguito side-by-side con l'app in esecuzione in modalità a schermo intero una divisione. L'utente può controllare la percentuale della schermata principale che occupa ogni app.
- [**Immagine in immagine** ](#Picture-in-Picture) : per App di riprodurre contenuto video, il video può ora essere riprodotto in una finestra mobile e ridimensionabile mobile tramite le altre applicazioni in esecuzione nel dispositivo iOS. L'utente abbia il pieno controllo le dimensioni e posizione di questa finestra. Immagine nell'immagine è disponibile solo in un iPad Pro, iPad Air, iPad 2 aria, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.

Esistono diversi aspetti da considerare quando [supporto multitasking nell'app](#Supporting-Multitasking-in-your-App), tra cui:

- [Dimensioni dello schermo e l'orientamento](#Screen-Size-Considerations)
- [Tasti di scelta rapida di Hardware personalizzato](#Custom-Hardware-Keyboard-Shortcuts)
- [Gestione delle risorse](#Resource-Management-Considerations)

Lo sviluppatore di applicazioni è inoltre possibile [rifiutare esplicitamente il multitasking](#Opting-Out-of-Multitasking), tra cui [la disabilitazione di riproduzione Video PIP](#Disabling-PIP-Video-Playback).

In questo articolo illustra i passaggi necessari per garantire che venga eseguita correttamente l'app xamarin. IOS in un ambiente multitasking o come rifiutare esplicitamente il multitasking, se non è una buona base per l'app.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Il multitasking per iPad, da [University Xamarin](https://university.xamarin.com)**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Guida introduttiva multitasking

Per supportare **diapositiva su** o **doppia visualizzazione** l'app deve eseguire le operazioni seguenti:

 - Essere compilati con iOS 9 (o versione successiva).
 - Utilizzare uno Storyboard per la schermata di avvio (e non immagini Asset).
 - Utilizzare uno Storyboard con layout automatico e le classi di dimensioni per la relativa interfaccia utente.
 - Supporta tutti i 4 iOS dispositivo orientamenti (verticale, verticale capovolto, orizzontale sinistra e destra orizzontale).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Su Multitasking per iPad

iOS 9 offre nuove capacità multitasking su iPad con l'introduzione di _diapositiva su_, _doppia visualizzazione_ (iPad aria 2, 4 Mini iPad e iPad Pro) e _immagine in immagine_. Informazioni dettagliate su queste funzionalità ti guideremo nelle sezioni seguenti.

<a name="Slide-Over" />

### <a name="slide-over"></a>Diapositiva su

La funzionalità di scorrimento su consente all'utente di selezionare un'app secondo e visualizzarli in un piccolo pannello scorrevole per fornire l'interazione rapido. Il Pannello diapositiva su è temporaneo e viene chiusa quando l'utente torna a funzionare di nuovo con l'app principale.

[![](multitasking-images/about01.png "Il pannello Sposta su")](multitasking-images/about01.png#lightbox)

È importante ricordare che l'utente decide quali due App verrà eseguito side-by-side e che lo sviluppatore non ha alcun controllo su questo processo. Di conseguenza, esistono alcuni aspetti che è necessario per garantire che l'app xamarin venga eseguita correttamente in un pannello diapositiva su:

- **Utilizzare le classi di dimensioni e il layout automatico** , poiché è possibile eseguire l'app xamarin nel riquadro laterale diapositiva-out, non è più basarsi sul dispositivo, le dimensioni dello schermo o il relativo orientamento al layout l'interfaccia utente. Per garantire che l'app viene ridimensionata correttamente di interfaccia, è necessario utilizzare le classi di dimensioni e il layout automatico. Per ulteriori informazioni, vedere il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.
- **Usare le risorse in modo efficiente** , perché l'app può ora essere condivisione il sistema con app in esecuzione un'altra, è importante che l'app Usa risorse di sistema in modo efficiente. Quando memoria diventa di tipo sparse, il sistema termina automaticamente l'app che utilizza la maggior quantità di memoria. Vedere Apple [Guida di efficienza energetica per App iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) per altri dettagli.

Sposta su è disponibile solo in un iPad Pro, iPad Air, iPad 2 aria, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad. Per ulteriori informazioni sulla preparazione dell'app per scorrere in, vedere Apple [adozione Multitasking miglioramenti su iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentazione.

<a name="Split-View" />

### <a name="split-view"></a>Visualizzazione suddivisa

Sull'hardware supportato iPad (iPad aria 2, 4 Mini iPad e iPad Pro solo), l'utente può selezionare un'app secondo ed eseguito side-by-side con l'app in esecuzione in modalità a schermo intero una divisione. L'utente può controllare la percentuale della schermata principale che ogni app occupa trascinando sullo schermo divisore.

[![](multitasking-images/about02.png "La visualizzazione suddivisa")](multitasking-images/about02.png#lightbox)

Come diapositiva su, l'utente decide quali due App eseguirà side-by-side e nuovamente, lo sviluppatore non ha alcun controllo su questo processo. Di conseguenza, la visualizzazione suddivisa inserisce requisiti simili in un'app xamarin:

- **Utilizzare le classi di dimensioni e il layout automatico** , perché l'app xamarin ora può essere eseguito in modalità a schermo una divisione alla dimensione specificata dell'utente, è possibile non basarsi sul dispositivo, le dimensioni dello schermo o il relativo orientamento al layout dell'interfaccia utente. Per garantire che l'app viene ridimensionata correttamente di interfaccia, è necessario utilizzare le classi di dimensioni e il layout automatico. Per ulteriori informazioni, vedere il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.
- **Usare le risorse in modo efficiente** , perché l'app può ora essere condivisione il sistema con app in esecuzione un'altra, è importante che l'app Usa risorse di sistema in modo efficiente. Quando memoria diventa di tipo sparse, il sistema termina automaticamente l'app che utilizza la maggior quantità di memoria. Vedere Apple [Guida di efficienza energetica per App iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) per altri dettagli.

Per ulteriori informazioni sulla preparazione dell'app per la doppia visualizzazione, vedere Apple [adozione Multitasking miglioramenti su iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentazione.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Immagine in immagine

La nuova immagine di funzionalità di immagine (noto anche come _PIP_) consente all'utente di guardare un video in una piccola finestra mobile che l'utente è possibile posizionare in un punto qualsiasi nella schermata sopra le altre applicazioni in esecuzione.

[![](multitasking-images/about03.png "Un esempio di immagine nella finestra mobile immagine")](multitasking-images/about03.png#lightbox)

Come con Sposta su e la doppia visualizzazione, l'utente ha il controllo completo su guardare un video in modalità immagine dell'immagine. Se la funzione principale dell'applicazione è guardare video, è necessario apportare alcune modifiche per funzionare correttamente in modalità PIP. In caso contrario, non sono necessarie modifiche per supportare PIP.

Per l'app per la visualizzazione di video PIP su richiesta dell'utente, è necessario essere utilizzando _AVKit_ o _AV Foundation API_. Media Player framework è stato ridimensionato nella iOS 9 e non supporta il PIP.

Immagine nell'immagine è disponibile solo in un iPad Pro, iPad Air, iPad 2 aria, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad. Per ulteriori informazioni, vedere il nostro [App di esempio PictureInPicture](https://developer.xamarin.com/samples/ios/iOS9/) e Apple [immagine in immagine di avvio rapido](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentazione.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Supporto Multitasking nell'App

Per qualsiasi app xamarin esistente, il multitasking di supporto è un'attività trasparente, purché l'app già segue Guide alla progettazione di Apple e procedure consigliate per iOS 8. Ciò significa che l'app deve essere usando storyboard con layout automatico e le classi di dimensioni per il layout dell'interfaccia utente (vedere il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) per altre informazioni).

Per queste applicazioni, modificare o non sono necessarie per supportare il multitasking e si comportano anche all'interno di esso. Se l'interfaccia utente dell'applicazione, è stato creato utilizzando altri metodi, ad esempio direttamente posizionamento e il ridimensionamento di elementi dell'interfaccia utente in codice c# o se si basa su dimensioni dello schermo di dispositivi specifici o gli orientamenti, sarà necessario modifica significativa per il supporto di iOS 9 multitasking correttamente.

Per supportare il multitasking di iOS 9 in qualsiasi nuova app xamarin. IOS, nuovamente utilizzare storyboard con layout automatico e le classi di dimensioni per tutti i layout di interfaccia utente dell'app e implementare le istruzioni riportate nelle sezioni seguenti.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Considerazioni di orientamento e dimensioni dello schermo

Prima di iOS 9, è possibile progettare l'app su orientamenti e dimensioni dello schermo di dispositivi specifici. Poiché è possibile eseguire un'app in un pannello diapositiva Out o in modalità di visualizzazione suddivisa, è possibile trovare in esecuzione in una classe regolare o compact dimensione orizzontale su iPad, indipendentemente dalle dimensioni di orientamento o dello schermo fisica del dispositivo.

[![](multitasking-images/sizeclasses01.png "Considerazioni di orientamento e dimensioni dello schermo")](multitasking-images/sizeclasses01.png#lightbox)

In un iPad, un'app a schermo intero è classi normali di dimensioni orizzontali e verticali. Tutti i dispositivi iPhone ma iPhone 6 Plus e iPhone 6s Plus, dispongono di classi di dimensione compatta in entrambe le direzioni in qualsiasi posizione. IPhone 6 Plus e iPhone 6s Plus in modalità orizzontale disporre di una classe di dimensioni regolari orizzontale e una classe di dimensione verticale Compact (molto simile a un iPad Mini).

Su iPad che supportano Sposta su e la doppia visualizzazione, possono finire con le combinazioni seguenti:

| **Orientamento** | **App primario** | **App secondario** |
|--- |--- |--- |
| **Verticale** |75% della schermata<br />Compact orizzontale<br />Regolare verticale|25% dello schermo<br />Compact orizzontale<br />Regolare verticale|
| **Orizzontale** |75% della schermata<br />Regolare orizzontale<br />Regolare verticale|25% dello schermo<br />Compact orizzontale<br />Regolare verticale|
| **Orizzontale** |50% della schermata<br />Compact orizzontale<br />Regolare verticale|50% della schermata<br />Compact orizzontale<br />Regolare verticale|

Nell'esempio [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) app, se viene eseguito a schermo intero in un iPad in modalità orizzontale, presenta l'elenco e la visualizzazione dettagli contemporaneamente:

[![](multitasking-images/sizeclasses03.png "L'elenco e la visualizzazione di dettaglio visualizzato allo stesso tempo")](multitasking-images/sizeclasses03.png#lightbox)

Se la stessa applicazione viene eseguita in un pannello diapositiva su, disposti come classe Compact dimensioni orizzontali e lo visualizza solo l'elenco:

[![](multitasking-images/sizeclasses04.png "Solo l'elenco visualizzato quando il dispositivo è orizzontale")](multitasking-images/sizeclasses04.png#lightbox)

Per garantire che l'app si comporta correttamente in questi casi, è consigliabile adottare le raccolte di tratto con classi di dimensione e conforme al `IUIContentContainer` e `IUITraitEnvironment` interfacce. Vedere Apple [riferimento alla classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) e nel [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) Guida per ulteriori informazioni.

Inoltre, non è possibile utilizzare i limiti dello schermo di dispositivi per definire l'area visibile dell'app, è necessario utilizzare invece i limiti di finestra dell'applicazione. Poiché i limiti di finestra sono completamente sotto il controllo dell'utente, è possibile apportare le modifiche a livello di codice o impedire all'utente di modificare i limiti.

Infine, l'app deve utilizzare un file di storyboard per presentare la schermata di avvio veloce rispetto all'utilizzo di un set di **PNG** file di immagine e supportano tutti gli orientamenti interfaccia quattro (verticale, verticale capovolto, orizzontale sinistra e destra orizzontale) per essere considerato per l'esecuzione in un pannello diapositiva su o in modalità di visualizzazione suddivisa.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Tasti di scelta rapida di Hardware personalizzato

In iOS 9 in esecuzione in un iPad, Apple ha supporto esteso per la tastiera hardware. supporto di base della tastiera esterno tramite Bluetooth e alcune tastiere produttori creati tastiera incluse chiavi specifiche iOS cablate sempre inclusi iPad.

A questo punto, con iOS 9, le applicazioni possono creare le proprie scelte rapide da tastiera personalizzata. Inoltre, alcuni base tasti di scelta rapida sono disponibili come **comando C** (copia), **comando X** (taglio) **comando V** (incollare) e **comando-MAIUSC-H**  (home), senza un'app da specificamente scritto rispondere ad essi.

**Scheda comando** verrà visualizzata una selezione di app che consente all'utente di spostarsi rapidamente tra le app dalla tastiera, analogamente al sistema operativo Mac:

[![](multitasking-images/keyboard01.png "La selezione di app")](multitasking-images/keyboard01.png#lightbox)

Se un'app per iOS 9 include i tasti di scelta rapida, l'utente può tenere premuto **comando**, **opzione** o **controllo** chiavi per visualizzarli in una finestra popup:

[![](multitasking-images/keyboard02.png "La finestra popup di scelta rapida da tastiera")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definizione di tasti di scelta rapida

Se si aggiunta il codice seguente a una vista o una vista Controller nella nostra app, quando la vista o un controller è visibile, personalizzato tasti di scelta rapida è disponibili:

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

Innanzitutto, viene eseguito l'override di `CanBecomeFirstResponder` proprietà e restituire `true` in modo che la vista o la vista Controller può ricevere input da tastiera. 

Successivamente, viene eseguito l'override di `KeyCommands` proprietà e creare un nuovo `UIKeyCommand` per il **comando-N** tasto di scelta rapida. Quando la sequenza di tasti è attivato, viene chiamato il `NewEntry` metodo (che è necessario esporre in iOS 9 usando il `Export` comando) per eseguire l'azione richiesta.

Se Esegui questa app in un iPad con una tastiera hardware collegati e i tipi di utente **comando-N**, verrà aggiunta una nuova voce all'elenco. Se l'utente tiene premuto **comando** chiave, verrà visualizzato l'elenco di tasti di scelta rapida:

[![](multitasking-images/keyboard03.png "La finestra popup di scelta rapida da tastiera")](multitasking-images/keyboard03.png#lightbox)

Vedere l'esempio [MultiTask app](http://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) per un esempio di implementazione.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considerazioni sulla gestione di risorse

Anche per le app che usano già Guide alla progettazione e procedure consigliate di iOS 8, la gestione delle risorse efficiente potrebbe comunque essere un problema. In iOS 9, le applicazioni non devono più utilizzo esclusivo di memoria, CPU o altre risorse di sistema.

Di conseguenza, è necessario ottimizzare l'app xamarin per utilizzare efficacemente le risorse di sistema o che sia rivolto verso la terminazione in situazioni di memoria insufficiente. Ciò vale anche delle App rifiutare esplicitamente il multitasking, poiché un secondo app potrebbe ancora essere eseguito in un pannello diapositiva su o un'immagine nella finestra di immagine che richiede risorse aggiuntive o che causano la frequenza di aggiornamento cada di sotto di 60 fotogrammi al secondo.

Si considerino le seguenti azioni dell'utente e le relative implicazioni:

- **Immissione di testo in un pannello diapositiva su** -anche se l'applicazione non ha input di testo, la tastiera sistema ora può essere visualizzata tramite la relativa interfaccia utente. Di conseguenza, l'applicazione potrebbe essere necessario rispondere alle notifiche di visualizzazione di tasti (ad esempio visualizzare e nascondere la tastiera).
- **Esegue un'App di secondo in un pannello diapositiva su** -nuova app ora è in esecuzione in primo piano e in competizione con l'app esistente per le risorse di sistema, ad esempio memoria e cicli di CPU.
- **Riproduzione di un Video in una finestra PIP** : non solo possibile questa finestra coprire parte dell'interfaccia dell'app, ma l'app che ha avviato il video è ancora in esecuzione in background e utilizza risorse di CPU e memoria.

Per garantire che l'app Usa le risorse in modo efficiente, è necessario effettuare le seguenti:

- **Profilare l'App con strumenti** -individuare le perdite di memoria, ma l'utilizzo della CPU e le aree in cui l'applicazione potrebbe bloccare il thread principale.
- **Rispondere ai metodi di transizioni di stato** - In del **appdelegate. cs** sostituzione di file e di risposta lo stato di modificare i metodi, ad esempio app immissione o la restituzione dallo sfondo. Rilascia tutte le risorse non sono richiesti, ad esempio immagini, dati o le visualizzazioni e controller di visualizzazione.
- **Test Side-by-Side con le applicazioni con utilizzo intensivo della memoria** : eseguire l'app tramite diapositiva Out e la doppia visualizzazione su hardware i/o fisici con un'app con utilizzo intensivo della memoria, ad esempio mappe (in modalità di visualizzazione Satellite) e verificare che entrambe le app rimangano attiva e non di arresto anomalo.

Vedere Apple [Guida di efficienza energetica per App iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) per ulteriori informazioni sulla gestione delle risorse.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Rifiuto esplicito Multitasking

Sebbene Apple suggerisce che tutte le app di iOS 9 supportano il multitasking, può essere motivi specifici per un'app non troppo, ad esempio giochi o App della fotocamera che richiedono l'intero schermo per funzionare correttamente.

Per l'app xamarin. IOS rifiutare esplicitamente in esecuzione in entrambi un pannello diapositiva Out o in modalità di visualizzazione suddivisa, modificare il progetto **Info. plist** del file e verificare **richiede la modalità a schermo intero**:

[![](multitasking-images/fullscreen01.png "Rifiuto esplicito Multitasking")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Mentre rifiuto esplicito multitasking impedisce l'esecuzione nella diapositiva Out o doppia visualizzazione dell'app, non impedisce un'altra app viene eseguita in diapositiva Out o un'immagine in immagine video da visualizzare con l'app.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Disabilitare la riproduzione di Video PIP

Nella maggior parte dei casi, l'app deve consentire all'utente di riprodurre contenuto video che viene visualizzato in un'immagine in una finestra mobile immagine. Tuttavia, potrebbe essere situazioni in cui ciò potrebbe non essere desiderato, ad esempio video gioco scena di taglio.

Per rifiutare esplicitamente la riproduzione video PIP, eseguire le operazioni seguenti nell'app:

 - Se si utilizza un `AVPlayerViewController` per visualizzare video, impostare il `AllowsPictureInPicturePlayback` proprietà `false`.
 - Se si utilizza il `AVPlayerLayer` per visualizzare video, non creare un'istanza di un `AVPictureInPictureController`.
 - Se si utilizza un `WKWebView` per visualizzare video, impostare il `AllowsPictureInPictureMediaPlayback` proprietà `false`.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i passaggi necessari per garantire che un'app xamarin verrà eseguite e funzionino correttamente nella nuova capacità multitasking iOS 9 per iPad. Inoltre, che trattato acconsentito esplicitamente il multitasking per App di cui non è una scelta ottimale.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Multitasking (esempio)](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introduzione agli storyboard unificato](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adozione Multitasking miglioramenti in iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [post di blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
