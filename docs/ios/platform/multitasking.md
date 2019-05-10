---
title: Multitasking per iPad in xamarin. IOS
description: iOS 9 supporta le due App in esecuzione al tempo stesso, tramite diapositiva o doppia visualizzazione. Supporta inoltre la riproduzione Picture In Picture video.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 6a9c66720aeb97c5b894cbea42c6cc0c5285c67d
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978620"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitasking per iPad in xamarin. IOS

_iOS 9 supporta le due App in esecuzione al tempo stesso, tramite diapositiva o doppia visualizzazione. Supporta inoltre la riproduzione Picture In Picture video._

![](multitasking-images/about02-sml.png "Suddividere l'esempio di schermata") ![](multitasking-images/about03-sml.png "in PAP esempio")

Aggiunge il supporto di multitasking per l'esecuzione di due App nello stesso momento su iPad specifiche hardware per iOS 9. Multitasking per iPad è supportato tramite le funzionalità seguenti:

- [**Diapositiva tramite** ](#Slide-Over) -consente all'utente di eseguire temporaneamente una seconda app di iOS in una diapositiva pannello (sia sul lato destro o sinistro dello schermo in base alla direzione language) che copre circa il 25% dell'app principale attualmente in esecuzione. Far scorrere in è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.
- [**Visualizzazione suddivisa** ](#Split-View) -in componenti hardware supportati iPad (iPad Air 2, 4 Mini iPad e iPad Pro solo), l'utente può selezionare una seconda app ed eseguirlo side-by-side con l'app attualmente in esecuzione in modalità a schermo intero una divisione. L'utente può controllare la percentuale della schermata principale che occupa ogni app.
- [**Picture in Picture** ](#Picture-in-Picture) : per le app che riprodurre contenuto video, i video possono ora essere riprodotti in una finestra mobile e ridimensionabile mobile rispetto alle altre App attualmente in esecuzione nel dispositivo iOS. L'utente dispone del controllo completo sulla dimensione e posizione della finestra. Immagine nell'immagine è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.

Esistono diversi aspetti da considerare quando si [che supportano il multitasking nell'app](#Supporting-Multitasking-in-your-App), tra cui:

- [Dimensioni dello schermo e l'orientamento](#Screen-Size-Considerations)
- [Scelte rapide da tastiera Hardware personalizzato](#Custom-Hardware-Keyboard-Shortcuts)
- [Gestione delle risorse](#Resource-Management-Considerations)

Gli sviluppatori di app è anche possibile [rifiutare esplicitamente il multitasking](#Opting-Out-of-Multitasking), tra cui [disabilitare la riproduzione di Video PIP](#Disabling-PIP-Video-Playback).

Questo articolo illustra i passaggi necessari per garantire che l'app xamarin. IOS viene eseguita correttamente in un ambiente di multitasking o come rifiutare esplicitamente il multitasking, se non è una buona base per l'app.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitasking per iPad video**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Guida introduttiva di multitasking

Per supportare **far scorrere tramite** oppure **doppia visualizzazione** l'app deve eseguire le operazioni seguenti:

 - Per la generazione iOS 9 (o versione successiva).
 - Utilizzare uno Storyboard per la schermata di avvio (e non gli asset delle immagini).
 - Utilizzare uno Storyboard con layout automatico e le classi di dimensioni per la relativa interfaccia utente.
 - Supporta tutti i 4 iOS orientamenti del dispositivo (verticale, verticale capovolto, orizzontale sinistra e destra orizzontale).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Sul Multitasking per iPad

iOS 9 offre nuove funzionalità di multitasking su iPad con l'introduzione del _far scorrere tramite_, _doppia visualizzazione_ (iPad Air 2, 4 Mini iPad e iPad Pro) e _Picture in Picture_. Esamina queste funzionalità verranno nelle sezioni seguenti.

<a name="Slide-Over" />

### <a name="slide-over"></a>Diapositiva su

La funzionalità di far scorrere tramite consente all'utente di selezionare una seconda app e visualizzarla in un pannello scorrevole piccole per fornire interazioni rapide. Il pannello di far scorrere su è temporaneo e si chiuderà quando l'utente torna a funzionare con l'app principale di nuovo.

[![](multitasking-images/about01.png "Il pannello Sposta su")](multitasking-images/about01.png#lightbox)

La cosa principale da ricordare è che l'utente decide quale due App verrà eseguito side-by-side e che lo sviluppatore non ha alcun controllo su questo processo. Di conseguenza, esistono alcuni aspetti che occorre fare per assicurarsi che l'app xamarin. IOS viene eseguita correttamente in un pannello Sposta su:

- **Usare il layout automatico e le classi di dimensioni** , perché l'app xamarin. IOS può ora essere eseguito nel pannello laterale orizzontale diapositiva, è non possibile non è più basarsi su dispositivo, le dimensioni dello schermo o relativo orientamento del layout dell'interfaccia utente. Per assicurarsi che l'app viene ridimensionata in modo corretto del, interfaccia, è necessario usare il layout automatico e le classi di dimensioni. Per altre informazioni, vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.
- **Usare le risorse in modo efficiente** , poiché l'app può ora condivida il sistema con un'altra app in esecuzione, è fondamentale che l'app Usa le risorse di sistema in modo efficiente. Quando memoria diventa di tipo sparse, il sistema viene terminato automaticamente le app che impiega molta memoria. Vedere di Apple [Guida per l'efficienza energetica per App per iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) per altri dettagli.

Far scorrere in è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad. Per altre informazioni sulla preparazione dell'app per far scorrere su, vedi di Apple [adottare i miglioramenti di Multitasking su iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentazione.

<a name="Split-View" />

### <a name="split-view"></a>Visualizzazione suddivisa

In componenti hardware supportati iPad (iPad Air 2, 4 Mini iPad e iPad Pro solo), l'utente può selezionare una seconda app ed eseguirlo side-by-side con l'app attualmente in esecuzione in modalità a schermo intero una divisione. L'utente può controllare la percentuale della schermata principale che ogni app occupa trascinando sullo schermo divisore.

[![](multitasking-images/about02.png "La doppia visualizzazione")](multitasking-images/about02.png#lightbox)

Come far scorrere su, l'utente decide quale due App eseguirà side-by-side e anche in questo caso, lo sviluppatore non ha alcun controllo su questo processo. Di conseguenza, la doppia visualizzazione attiva requisiti simili in un'app xamarin. ios:

- **Usare il layout automatico e le classi di dimensioni** , poiché l'app xamarin. IOS possa ora essere eseguito in una modalità a schermo intero split alla dimensione specificata dell'utente, è non possibile non è più basarsi su dispositivo, le dimensioni dello schermo o relativo orientamento del layout dell'interfaccia utente. Per assicurarsi che l'app viene ridimensionata in modo corretto del, interfaccia, è necessario usare il layout automatico e le classi di dimensioni. Per altre informazioni, vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.
- **Usare le risorse in modo efficiente** , poiché l'app può ora condivida il sistema con un'altra app in esecuzione, è fondamentale che l'app Usa le risorse di sistema in modo efficiente. Quando memoria diventa di tipo sparse, il sistema viene terminato automaticamente le app che impiega molta memoria. Vedere di Apple [Guida per l'efficienza energetica per App per iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) per altri dettagli.

Per altre informazioni sulla preparazione dell'app per la doppia visualizzazione, vedere di Apple [adottare i miglioramenti di Multitasking su iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentazione.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Picture in Picture

La nuova immagine nella funzionalità di immagine (noto anche come _PIP_) consente all'utente di guardare un video in una finestra di piccole dimensioni, a virgola mobile che l'utente può posizionare in un punto qualsiasi nella schermata sopra le altre App in esecuzione.

[![](multitasking-images/about03.png "Un esempio di immagine nella finestra mobile immagine")](multitasking-images/about03.png#lightbox)

Come con diapositive failover e la doppia visualizzazione, l'utente ha il controllo completo guardare un video nell'immagine in modalità immagine. Se la funzione principale dell'app è guardare video, sarà necessario apportare alcuna modifica per il corretto comportamento in modalità PIP. In caso contrario, non sono necessarie modifiche per supportare PIP.

Per l'app per la visualizzazione PIP video su richiesta dell'utente, è necessario usare uno _AVKit_ o nella _AV Foundation API_. Media Player framework è stato rimosso in iOS 9 e non supporta il PIP.

Immagine nell'immagine è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad. Per altre informazioni, vedere la [App di esempio PictureInPicture](https://developer.xamarin.com/samples/ios/iOS9/) e di Apple [immagine nell'immagine di avvio rapido](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentazione.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Che supportano il Multitasking nell'App

Per qualsiasi app xamarin. IOS esistente, che supportano il multitasking è un'attività trasparente, purché l'app già segue le procedure consigliate per iOS 8 e guide di progettazione di Apple. Ciò significa che l'app deve usare gli storyboard con layout automatico e le classi di dimensioni per il layout dell'interfaccia utente (vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) per altre informazioni).

Per queste App, le modifiche minime o non sono necessari per supportare il multitasking e si comportano anche all'interno di esso. Se l'app dell'interfaccia utente, è stato creato usando altri metodi, ad esempio direttamente il posizionamento e ridimensionamento di elementi dell'interfaccia utente in C# del codice o se si basa su dimensioni dello schermo del dispositivo specifico o gli orientamenti, sarà necessario modifica significativa per supportare correttamente il multitasking di iOS 9.

Per supportare il multitasking di iOS 9 su qualsiasi nuova app xamarin. IOS, è ancora usare gli storyboard con layout automatico e le classi di dimensioni per tutti i layout di interfaccia utente dell'app e implementare le istruzioni riportate nelle sezioni seguenti.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Considerazioni di orientamento e dimensioni dello schermo

Prima di iOS 9, è possibile progettare l'app con le dimensioni dello schermo di dispositivi specifici e gli orientamenti. Perché un'app può ora essere eseguita in un pannello diapositiva Out o in modalità doppia visualizzazione, è possibile trovare in esecuzione in uno una classe compatta o regolari dimensione orizzontale su iPad, indipendentemente dalle dimensioni dello schermo o orientamento fisica del dispositivo.

[![](multitasking-images/sizeclasses01.png "Considerazioni di orientamento e dimensioni dello schermo")](multitasking-images/sizeclasses01.png#lightbox)

In un iPad, un'app a schermo intero è regolare le classi di dimensioni orizzontali e verticali. Tutti i dispositivi iPhone, ma l'iPhone 6 Plus e iPhone 6s Plus, avere classi di dimensioni Compact in entrambe le direzioni in qualsiasi posizione. L'iPhone 6 Plus e iPhone 6s Plus in modalità orizzontale disporre di una classe di dimensioni orizzontali normale e una classe di dimensioni verticali Compact (molto simile ad un Mini iPad).

Su iPad che supportano la diapositiva failover e la doppia visualizzazione, è possibile finire con le combinazioni seguenti:

| **Orientamento** | **App principale** | **App secondaria** |
|--- |--- |--- |
| **Verticale** |75% della schermata<br />Compact orizzontale<br />Regolare verticale|25% della schermata<br />Compact orizzontale<br />Regolare verticale|
| **Panorama applicativo** |75% della schermata<br />Orizzontale regolari<br />Regolare verticale|25% della schermata<br />Compact orizzontale<br />Regolare verticale|
| **Panorama applicativo** |50% della schermata<br />Compact orizzontale<br />Regolare verticale|50% della schermata<br />Compact orizzontale<br />Regolare verticale|

Nell'esempio [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) app, se viene eseguita a schermo intero in un iPad in modalità orizzontale, presenta l'elenco e la visualizzazione dettagli allo stesso tempo:

[![](multitasking-images/sizeclasses03.png "Nell'elenco e la visualizzazione di dettaglio visualizzato allo stesso tempo")](multitasking-images/sizeclasses03.png#lightbox)

Se la stessa app viene eseguita in un pannello di far scorrere tramite, è strutturato come una classe di dimensioni orizzontali Compact e viene visualizzato solo l'elenco:

[![](multitasking-images/sizeclasses04.png "Solo l'elenco visualizzato quando il dispositivo è orizzontale")](multitasking-images/sizeclasses04.png#lightbox)

Per assicurarsi che l'app si comporta correttamente in queste situazioni, è consigliabile adottare il tratto di raccolte con le classi di dimensioni e sono conformi al `IUIContentContainer` e `IUITraitEnvironment` interfacce. Vedere di Apple [riferimento alla classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) e il [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) Guida per altre informazioni.

Inoltre, non è possibile affidarsi i limiti dello schermo di dispositivi per definire l'area visibile dell'app, è necessario usare invece dei limiti di finestra dell'app. Poiché i limiti di finestra sono completamente sotto il controllo dell'utente, è possibile apportare le modifiche a livello di codice o impedire all'utente di modificare questi limiti.

Infine, l'app deve usare un file storyboard per presentare la schermata di avvio veloce rispetto all'utilizzo di un set di **PNG** file di immagine e supporta tutti gli orientamenti interfaccia quattro (verticale, verticale capovolto, orizzontale sinistra e destra orizzontale) per essere considerato per l'esecuzione in un pannello diapositiva su o in modalità doppia visualizzazione.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Scelte rapide da tastiera Hardware personalizzato

In iOS 9 in esecuzione in un iPad, Apple ha supporto "Extended" per la tastiera hardware. iPad hanno sempre incluso il supporto di base della tastiera esterno tramite Bluetooth e alcune tastiere produttori creati da tastiera che incluso cavo tasti specifici di iOS.

A questo punto, con iOS 9, le app possono creare le proprie scelte rapide da tastiera personalizzato. Inoltre, alcuni tasti di base sono disponibili, ad esempio **comando-C** (copia), **comando-X** (Taglia), **comando-V** (Incolla) e **comando-MAIUSC-H**  (home), senza un'app in fase di appositamente scritto rispondere ad essi.

**Scheda comando** verrà visualizzata una selezione di app che consente all'utente di passare rapidamente tra le app dalla tastiera, molto simile al sistema operativo Mac:

[![](multitasking-images/keyboard01.png "La selezione di app")](multitasking-images/keyboard01.png#lightbox)

Se un'app per iOS 9 include i tasti di scelta rapida, l'utente può tenere premuto **comandi**, **opzione** oppure **controllo** le chiavi per visualizzarli in una finestra popup:

[![](multitasking-images/keyboard02.png "Il popup di tasti di scelta rapida da tastiera")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>La definizione di tasti di scelta rapida personalizzati

Se si aggiunge il codice seguente a una visualizzazione o Controller di visualizzazione nella nostra app quando tale vista o un controller è visibile, una scelta rapida da tastiera personalizzate saranno disponibile:

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

In primo luogo, viene eseguito l'override di `CanBecomeFirstResponder` proprietà e restituire `true` in modo che la visualizzazione o Controller di visualizzazione può ricevere input da tastiera. 

Successivamente, viene eseguito l'override di `KeyCommands` proprietà e creare un nuovo `UIKeyCommand` per il **comando-N** pressione di tasto. Quando la sequenza di tasti è attivato, chiamiamo il `NewEntry` metodo (che è necessario esporre all'uso di iOS 9 il `Export` comando) per eseguire l'azione richiesta.

Se si esegue l'app in un iPad con una tastiera hardware collegato e l'utente digita **comando-N**, una nuova voce verrà aggiunto all'elenco. Se l'utente tiene premuto **comando** chiave, verrà visualizzato l'elenco dei tasti di scelta rapida:

[![](multitasking-images/keyboard03.png "Il popup di tasti di scelta rapida da tastiera")](multitasking-images/keyboard03.png#lightbox)

Vedere l'esempio [MultiTask app](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) per un esempio di implementazione.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considerazioni sulla gestione delle risorse

Anche per le app che usano già le guide di progettazione iOS 8 e le procedure consigliate, gestione efficiente delle risorse potrebbe costituire comunque un problema. In iOS 9, le app non devono più uso esclusivo di memoria, CPU o altre risorse di sistema.

Di conseguenza, è necessario ottimizzare l'app xamarin. IOS per un uso efficace delle risorse di sistema o che sia rivolto verso la terminazione in situazioni di memoria insufficiente. Questo vale anche delle App che rifiutare esplicitamente il multitasking, poiché una seconda app potrebbe essere ancora possibile eseguirlo in un pannello diapositiva in o da un'immagine nella finestra di immagine che richiedono le risorse aggiuntive oppure la causa della frequenza di aggiornamento per scendere di sotto di 60 fotogrammi al secondo.

Prendere in considerazione le seguenti azioni dell'utente e le relative implicazioni:

- **Immissione di testo in un pannello Sposta su** -anche se l'app non dispone di alcun input di testo, la tastiera sistema ora può essere visualizzata tramite la relativa interfaccia utente. Di conseguenza, l'app potrebbe essere necessario rispondere alle notifiche di visualizzazione della tastiera (ad esempio mostrare e nascondere la tastiera).
- **Secondo l'App in esecuzione in un pannello Sposta su** -nuova app ora è in esecuzione in primo piano e in competizione con le app esistenti per le risorse di sistema, ad esempio cicli della CPU e memoria.
- **Riproduzione di un Video in una finestra del PIP** : non solo può coprire in questa finestra parte dell'interfaccia dell'app, ma l'app che ha avviato il video è ancora in esecuzione in background e utilizza le risorse della CPU e memoria.

Per assicurarsi che l'app Usa le risorse in modo efficiente, è necessario eseguire le operazioni seguenti:

- **Profilare l'App con Instruments** -controllare le perdite di memoria, ma l'utilizzo della CPU e le aree in cui l'app potrebbe bloccare il thread principale.
- **Rispondere ai metodi delle transizioni di stato** - nelle **AppDelegate.cs** override di file e lo stato di risposta modificare i metodi, ad esempio l'app immissione o la restituzione di background. Rilascia tutte le risorse non sono richiesti, ad esempio immagini, dati o le visualizzazioni e controller di visualizzazione.
- **Test Side-by-Side con le app con utilizzo intensivo di memoria** : eseguire l'app tramite diapositiva Out e la doppia visualizzazione su hardware fisico iOS con un'app con utilizzo intensivo della memoria, ad esempio mappe (in modalità di visualizzazione Satellite) e verificare che entrambe le app rimangano attiva e che causa l'arresto anomalo.

Vedere di Apple [Guida per l'efficienza energetica per App per iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) per ulteriori informazioni sulla gestione delle risorse.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Rifiuto esplicito Multitasking

Mentre suggerite da Apple che tutte le app di iOS 9 supportano il multitasking, può essere presente motivi molto specifici per un'app non troppo, ad esempio giochi o App della fotocamera che richiedono l'intero schermo a funzionare correttamente.

Per l'app xamarin. IOS rifiutare esplicitamente l'esecuzione in entrambi un pannello diapositiva Out o in modalità doppia visualizzazione, modifica del progetto **Info. plist** del file e controllare **richiede schermo intero**:

[![](multitasking-images/fullscreen01.png "Rifiuto esplicito Multitasking")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Mentre rifiuto esplicito multitasking impedisce che l'app in esecuzione nella diapositiva Out o doppia visualizzazione, non impedisce un'altra app in esecuzione nella diapositiva Out o un'immagine in immagine video da visualizzare insieme all'app.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>La disabilitazione di riproduzione del Video PIP

Nella maggior parte dei casi, l'app deve consentire all'utente per riprodurre contenuto video che visualizza un'immagine nella finestra mobile immagine. Tuttavia, potrebbero esserci situazioni in cui si potrebbe non essere appropriata, ad esempio i video di gioco tagliati scena.

Per rifiutare esplicitamente la riproduzione di video di PIP, eseguire le operazioni seguenti nell'app:

 - Se si usa un' `AVPlayerViewController` per visualizzare video, impostare il `AllowsPictureInPicturePlayback` proprietà `false`.
 - Se si usa la `AVPlayerLayer` per visualizzare video, non creare un'istanza di un `AVPictureInPictureController`.
 - Se si usa un' `WKWebView` per visualizzare video, impostare il `AllowsPictureInPictureMediaPlayback` proprietà `false`.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i passaggi necessari per assicurarsi che un'app xamarin. IOS verrà eseguito e si comportano in modo corretto in iOS 9 nuova capacità di multitasking per iPad. Inoltre, sia nascosto rifiutare esplicitamente di multitasking per le App in cui non è una scelta ideale.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Multitasking (esempio)](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adottare i miglioramenti di Multitasking in iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Post di blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
