---
title: Multitasking per iPad in Novell. iOS
description: iOS 9 supporta due app in esecuzione allo stesso tempo, usando la visualizzazione della diapositiva o della divisione. Supporta anche la riproduzione video in immagini.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 8f38200e8274a36783ac7bd1aec4b695036f83e2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769641"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitasking per iPad in Novell. iOS

_iOS 9 supporta due app in esecuzione allo stesso tempo, usando la visualizzazione della diapositiva o della divisione. Supporta anche la riproduzione video in immagini._

![](multitasking-images/about02-sml.png "Suddividere l'esempio di schermata") ![](multitasking-images/about03-sml.png "in PAP esempio")

iOS 9 aggiunge il supporto multitasking per l'esecuzione di due app allo stesso tempo su hardware iPad specifico. Il multitasking per iPad è supportato tramite le funzionalità seguenti:

- [**Scorri su**](#Slide-Over) -consente all'utente di eseguire temporaneamente una seconda app iOS in un pannello scorrevole (sul lato destro o sinistro dello schermo in base alla direzione della lingua) che copre circa il 25% dell'app principale attualmente in esecuzione. La funzionalità di scorrimento è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.
- [**Split View**](#Split-View) : su hardware iPad supportato (solo iPad Air 2, iPad Mini 4 e iPad Pro), l'utente può selezionare una seconda app ed eseguirla side-by-side con l'app attualmente in esecuzione in modalità split screen. L'utente può controllare la percentuale della schermata principale occupata da ogni app.
- [**Immagine nell'immagine**](#Picture-in-Picture) : per le app che eseguono la riproduzione di contenuto video, il video può essere ora riprodotto in una finestra spostabile e ridimensionabile che è in grado di spostarsi sulle altre app attualmente in esecuzione nel dispositivo iOS. L'utente ha il controllo completo sulle dimensioni e la posizione di questa finestra. Immagine in immagine è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.

Esistono diversi aspetti da considerare quando si [supporta il multitasking nell'app](#Supporting-Multitasking-in-your-App), tra cui:

- [Dimensioni e orientamento dello schermo](#Screen-Size-Considerations)
- [Tasti di scelta rapida personalizzati hardware](#Custom-Hardware-Keyboard-Shortcuts)
- [Gestione delle risorse](#Resource-Management-Considerations)

Gli sviluppatori di app possono anche [rifiutare esplicitamente il multitasking](#Opting-Out-of-Multitasking), inclusa [la disabilitazione della riproduzione video PIP](#Disabling-PIP-Video-Playback).

Questo articolo illustra i passaggi necessari per assicurarsi che l'app Novell. iOS venga eseguita correttamente in un ambiente a più attività o come rifiutare esplicitamente il multitasking, se non è una soluzione ottimale per l'app.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitasking per video iPad**

<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Guida introduttiva a più attività

Per supportare la visualizzazione **diapositiva** o **Split** , l'app deve eseguire le operazioni seguenti:

- Essere compilati con iOS 9 (o versione successiva).
- Usare uno storyboard per la schermata di avvio (e non per le risorse dell'immagine).
- Usare uno storyboard con layout automatico e classi di dimensioni per la relativa interfaccia utente.
- Supporta tutti e quattro gli orientamenti dei dispositivi iOS (verticale, verticale, orizzontale a sinistra & orizzontale a destra).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Informazioni sul multitasking per iPad

iOS 9 offre nuove funzionalità multitasking su iPad con l'introduzione di _Slide over_, _Split View_ (solo iPad Air 2, iPad Mini 4 e iPad Pro) e _Picture in Picture_. Nelle sezioni seguenti verranno esaminate in dettaglio queste funzionalità.

<a name="Slide-Over" />

### <a name="slide-over"></a>Scorri sopra

La funzionalità di scorrimento consente all'utente di selezionare una seconda app e di visualizzarla in un piccolo pannello scorrevole per offrire un'interazione rapida. Il pannello di scorrimento è temporaneo e si chiude quando l'utente torna a usare nuovamente l'app principale.

[![](multitasking-images/about01.png "Pannello della diapositiva")](multitasking-images/about01.png#lightbox)

La cosa principale da ricordare è che l'utente decide quali due app verranno eseguite side-by-side e che lo sviluppatore non ha alcun controllo su questo processo. Di conseguenza, è necessario eseguire alcune operazioni per assicurarsi che l'app Novell. iOS venga eseguita correttamente in un pannello di scorrimento:

- **Usare le classi di layout e di ridimensionamento** automatico: poiché è ora possibile eseguire l'app Novell. iOS nel pannello laterale della diapositiva, non è più possibile fare affidamento sul dispositivo, sulle dimensioni dello schermo o sull'orientamento per il layout dell'interfaccia utente. Per assicurarsi che l'app venga ridimensionata correttamente, è necessario usare il layout automatico e le classi di dimensioni. Per ulteriori informazioni, vedere la documentazione [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Usare le risorse in modo efficiente** : poiché l'app può ora condividere il sistema con un'altra app in esecuzione, è fondamentale che l'app usi le risorse di sistema in modo efficiente. Quando la memoria diventa di tipo sparse, il sistema interrompe automaticamente l'app che sta consumando la maggior parte della memoria. Per altri dettagli, vedere la [Guida all'efficienza energetica Apple per le app iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) .

La funzionalità di scorrimento è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4. Per altre informazioni sulla preparazione dell'app per la diapositiva, vedere la pagina relativa all' [adozione di miglioramenti multitasking nella](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentazione di Apple.

<a name="Split-View" />

### <a name="split-view"></a>Visualizzazione suddivisa

Nell'hardware iPad supportato (solo iPad Air 2, iPad Mini 4 e iPad Pro), l'utente può selezionare una seconda app ed eseguirla side-by-side con l'app attualmente in esecuzione in modalità split screen. L'utente può controllare la percentuale della schermata principale occupata da ogni app trascinando un divisore a schermo.

[![](multitasking-images/about02.png "Visualizzazione divisa")](multitasking-images/about02.png#lightbox)

Analogamente alla diapositiva, l'utente decide quali due app verranno eseguite side-by-side e di nuovo, lo sviluppatore non ha alcun controllo su questo processo. Di conseguenza, la visualizzazione divisa pone requisiti simili in un'app Novell. iOS:

- **Usare le classi di layout e di ridimensionamento** automatico: poiché l'app Novell. iOS può ora essere eseguita in modalità a schermo intero a dimensione specificata dall'utente, non è più possibile fare affidamento sul dispositivo, sulle dimensioni dello schermo o sull'orientamento per il layout dell'interfaccia utente. Per assicurarsi che l'app venga ridimensionata correttamente, è necessario usare il layout automatico e le classi di dimensioni. Per ulteriori informazioni, vedere la documentazione [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Usare le risorse in modo efficiente** : poiché l'app può ora condividere il sistema con un'altra app in esecuzione, è fondamentale che l'app usi le risorse di sistema in modo efficiente. Quando la memoria diventa di tipo sparse, il sistema interrompe automaticamente l'app che sta consumando la maggior parte della memoria. Per altri dettagli, vedere la [Guida all'efficienza energetica Apple per le app iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) .

Per altre informazioni sulla preparazione dell'app per la visualizzazione divisa, vedere l'articolo relativo ai [miglioramenti del multitasking nella](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentazione per iPad.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Immagine nell'immagine

La nuova immagine nella funzionalità Picture (anche nota come _PIP_) consente all'utente di guardare un video in una piccola finestra mobile che l'utente può posizionare in un punto qualsiasi dello schermo sopra altre app in esecuzione.

[![](multitasking-images/about03.png "Immagine di esempio nella finestra mobile immagine")](multitasking-images/about03.png#lightbox)

Come per la diapositiva e la visualizzazione divisa, l'utente ha il controllo completo sull'osservazione di un video in modalità immagine. Se la funzione principale dell'app è guardare i video, sarà necessario apportare alcune modifiche per comportarsi correttamente in modalità PIP. In caso contrario, non sono necessarie modifiche per supportare PIP.

Perché l'app visualizzi il video PIP alla richiesta dell'utente, è necessario usare _AVKit_ o le _API di AV Foundation_. Il Framework Media Player è stato ammortizzato in iOS 9 e non supporta PIP.

Immagine in immagine è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4. Per altre informazioni, vedere l' [app di esempio PictureInPicture](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) e l'immagine di Apple [nell'immagine avvio rapido](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentazione.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Supporto di multitasking nell'app

Per qualsiasi app Novell. iOS esistente, il multitasking di supporto è un'attività trasparente, purché l'app segua già le guide di progettazione e le procedure consigliate di Apple per iOS 8. Ciò significa che l'app deve usare gli storyboard con il layout automatico e le classi di dimensioni per i layout dell'interfaccia utente. per ulteriori informazioni, vedere l' [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

Per queste app, sono necessarie modifiche minime o nulle per supportare il multitasking e comportarsi in modo ottimale. Se l'interfaccia utente dell'app è stata creata usando altri metodi, ad esempio il posizionamento diretto e il C# ridimensionamento degli elementi dell'interfaccia utente nel codice o se si basa su dimensioni o orientamenti specifici dello schermo del dispositivo, sarà necessario apportare modifiche significative per supportare correttamente il multitasking di iOS 9.

Per supportare il multitasking iOS 9 in qualsiasi nuova app Novell. iOS, usare di nuovo gli storyboard con layout automatico e le classi di dimensioni per tutti i layout dell'interfaccia utente dell'app e implementare le istruzioni nelle sezioni riportate di seguito.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Considerazioni sulle dimensioni e sull'orientamento dello schermo

Prima di iOS 9, è possibile progettare l'app in base a dimensioni e orientamenti specifici del dispositivo. Poiché ora un'app può essere eseguita in un pannello di scorrimento o in modalità di visualizzazione divisa, può trovarsi in esecuzione in una classe di dimensioni orizzontali compatte o regolari su iPad, indipendentemente dall'orientamento fisico o dalle dimensioni dello schermo del dispositivo.

[![](multitasking-images/sizeclasses01.png "Considerazioni sulle dimensioni e sull'orientamento dello schermo")](multitasking-images/sizeclasses01.png#lightbox)

In un iPad un'app a schermo intero presenta classi di dimensioni orizzontali e verticali regolari. Tutti gli iPhone, ma iPhone 6 Plus e iPhone 6s Plus, hanno classi di dimensioni compatte in entrambe le direzioni in qualsiasi orientamento. IPhone 6 Plus e iPhone 6s Plus in modalità orizzontale hanno una classe di dimensioni orizzontali normale e una classe di dimensioni verticali compatte, molto simile a un iPad mini.

Negli iPad che supportano lo scorrimento e la visualizzazione divisa, è possibile ottenere le combinazioni seguenti:

| **Orientamento** | **App primaria** | **App secondaria** |
|--- |--- |--- |
| **Verticale** |75% dello schermo<br />Compatta orizzontale<br />Normale verticale|25% dello schermo<br />Compatta orizzontale<br />Normale verticale|
| **Orizzontale** |75% dello schermo<br />Normale orizzontale<br />Normale verticale|25% dello schermo<br />Compatta orizzontale<br />Normale verticale|
| **Orizzontale** |50% dello schermo<br />Compatta orizzontale<br />Normale verticale|50% dello schermo<br />Compatta orizzontale<br />Normale verticale|

Nell'app [MuliTask](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) di esempio, se viene eseguita a schermo intero su un iPad in modalità orizzontale, presenta contemporaneamente l'elenco e la visualizzazione Dettagli:

[![](multitasking-images/sizeclasses03.png "Elenco e visualizzazione dettagli presentati allo stesso tempo")](multitasking-images/sizeclasses03.png#lightbox)

Se la stessa app viene eseguita in un pannello di scorrimento, viene definita come classe di dimensioni orizzontali compatte e visualizza solo l'elenco:

[![](multitasking-images/sizeclasses04.png "Solo l'elenco visualizzato quando il dispositivo è orizzontale")](multitasking-images/sizeclasses04.png#lightbox)

Per assicurarsi che l'app si comporta correttamente in queste situazioni, è consigliabile adottare raccolte di tratti insieme alle classi di dimensioni e conformi alle `IUIContentContainer` interfacce e. `IUITraitEnvironment` Per ulteriori informazioni, vedere la Guida di [riferimento alla classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) di Apple e la Guida [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

Inoltre, non è più possibile fare affidamento sui limiti dello schermo dei dispositivi per definire l'area visibile dell'app, ma è necessario usare i limiti della finestra dell'app. Poiché i limiti della finestra sono completamente sotto il controllo dell'utente, non è possibile regolarli a livello di codice o impedire all'utente di modificare questi limiti.

Infine, l'app deve usare un file di storyboard per presentare la schermata di avvio in contrapposizione all'uso di un set di file di immagine con **estensione png** e supportare tutti e quattro gli orientamenti dell'interfaccia (verticale, verticale verticale, orizzontale sinistro e orizzontale a destra) da considerare in esecuzione in una diapositiva sopra il pannello o in modalità di visualizzazione divisa.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Tasti di scelta rapida personalizzati hardware

In iOS 9 in esecuzione su un iPad, Apple ha esteso il supporto per le tastiere hardware. gli iPad hanno sempre incluso il supporto della tastiera esterna di base tramite Bluetooth e alcuni produttori di tastiera hanno creato tastiere che includevano chiavi specifiche per iOS.

Ora, con iOS 9, le app possono creare scelte rapide da tastiera personalizzate. Sono inoltre disponibili alcuni tasti di scelta rapida di base, ad esempio **Command-C** (Copy), **Command-X** (Cut), **Command-V** (paste) e **Command-Shift-H** (Home), senza che un'app sia scritta in modo specifico per rispondere.

La **scheda comando** apre uno strumento di selezione delle app che consente all'utente di spostarsi rapidamente tra le app dalla tastiera, in modo analogo all'Mac OS:

[![](multitasking-images/keyboard01.png "Switcher dell'app")](multitasking-images/keyboard01.png#lightbox)

Se un'app iOS 9 include tasti di scelta rapida, l'utente può mantenere il **comando**, l' **opzione** o i tasti di **controllo** per visualizzarli in un popup:

[![](multitasking-images/keyboard02.png "Popup dei tasti di scelta rapida")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definizione di scelte rapide da tastiera personalizzate

Se si aggiunge il codice seguente a una vista o a un controller di visualizzazione nell'app, quando tale visualizzazione o controller è visibile, sarà disponibile un tasto di scelta rapida personalizzato:

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

In primo luogo, viene `CanBecomeFirstResponder` eseguito l'override `true` della proprietà e viene restituito in modo che il controller di visualizzazione o visualizzazione possa ricevere input da tastiera. 

Successivamente, si esegue l' `KeyCommands` override della proprietà e si `UIKeyCommand` crea un nuovo oggetto per la sequenza di tasti **Command-N** . Quando la sequenza di tasti è attivata, viene chiamato `NewEntry` il metodo (che viene esposto a iOS 9 usando `Export` il comando) per eseguire l'azione richiesta.

Se si esegue l'app in un iPad con una tastiera hardware collegata e l'utente digita **Command-N**, viene aggiunta una nuova voce all'elenco. Se l'utente utilizza il tasto di **comando** , verrà visualizzato l'elenco dei collegamenti seguenti:

[![](multitasking-images/keyboard03.png "Popup dei tasti di scelta rapida")](multitasking-images/keyboard03.png#lightbox)

Per un'implementazione di esempio, vedere l' [app multitasking](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) di esempio.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considerazioni sulla gestione delle risorse

Anche per le app che usano già le guide di progettazione e le procedure consigliate di iOS 8, una gestione efficiente delle risorse può comunque costituire un problema. In iOS 9, le app non hanno più l'uso esclusivo di memoria, CPU o altre risorse di sistema.

Di conseguenza, è necessario ottimizzare l'app Novell. iOS per usare in modo efficace le risorse di sistema o la terminazione in situazioni di memoria insufficiente. Questo vale anche per le app che rifiutano esplicitamente il multitasking, perché una seconda app potrebbe ancora essere eseguita in una diapositiva sopra il pannello o in un'immagine nella finestra immagine che richiede risorse aggiuntive o che la frequenza di aggiornamento scenda sotto 60 fotogrammi al secondo.

Prendere in considerazione le azioni dell'utente seguenti e le relative implicazioni:

- **Immissione di testo in una diapositiva sul pannello** : anche se l'app non ha input di testo, è ora possibile visualizzare la tastiera di sistema sulla relativa interfaccia utente. Di conseguenza, potrebbe essere necessario che l'app risponda alle notifiche di visualizzazione della tastiera, ad esempio mostrando e nascondendo la tastiera.
- **Esecuzione di una seconda app in una diapositiva sul pannello** : la nuova app viene ora eseguita in primo piano e in competizione con l'app esistente per le risorse di sistema, ad esempio i cicli di memoria e CPU.
- **Riproduzione di un video in una finestra PIP** : non solo questa finestra copre parte dell'interfaccia dell'app, ma l'app che ha avviato il video è ancora in esecuzione in background e utilizza risorse di CPU e memoria.

Per assicurarsi che l'app stia usando le risorse in modo efficiente, è necessario eseguire le operazioni seguenti:

- **Profilare l'app con Instruments** : verificare la presenza di perdite di memoria, l'utilizzo della CPU evidente e le aree in cui l'app potrebbe bloccare il thread principale.
- **Rispondere ai metodi di transizioni di stato** : nell'override del file **AppDelegate.cs** e nella risposta ai metodi di modifica dello stato, ad esempio l'immissione o la restituzione di un'app dallo sfondo. Rilascia le risorse non necessarie, ad esempio immagini, dati o visualizzazioni e controller di visualizzazione.
- **Test affiancati alle app con utilizzo intensivo della memoria** : Esegui l'app usando sia la visualizzazione a discesa che la visualizzazione suddivisa su hardware iOS fisico con un'app a elevato utilizzo di memoria, ad esempio mappe (in modalità di visualizzazione satellite) e verifica che entrambe le app rimangano reattive e non vengano arrestate

Per altre informazioni sulla gestione delle risorse, vedere la [Guida all'efficienza energetica Apple per le app iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) .

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Rifiuto esplicito del multitasking

Mentre Apple suggerisce che tutte le app iOS 9 supportano il multitasking, è possibile che non esistano anche motivi specifici per un'app, ad esempio giochi o app per fotocamere che richiedono il corretto funzionamento dello schermo intero.

Per consentire all'app Novell. iOS di rifiutare esplicitamente l'esecuzione in un pannello di scorrimento o in modalità di visualizzazione divisa, modificare il file **info. plist** del progetto e selezionare **richiede schermo intero**:

[![](multitasking-images/fullscreen01.png "Rifiuto esplicito del multitasking")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Quando si rifiuta esplicitamente il multitasking, l'app non può essere eseguita in slide out o Split View, ma non impedisce l'esecuzione di un'altra app in slide out o in un'immagine nel video immagine, oltre che nell'app.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Disabilitazione della riproduzione video PIP

Nella maggior parte dei casi, l'app deve consentire all'utente di riprodurre qualsiasi contenuto video visualizzato in un'immagine nella finestra mobile immagine. In alcuni casi, tuttavia, potrebbe non essere auspicabile, ad esempio i video della scena Cut Game.

Per rifiutare esplicitamente la riproduzione video PIP, eseguire le operazioni seguenti nell'app:

- Se si utilizza un `AVPlayerViewController` per visualizzare video, impostare la `AllowsPictureInPicturePlayback` proprietà su `false`.
- Se si usa il per `AVPlayerLayer` visualizzare il video, non creare un' `AVPictureInPictureController`istanza di.
- Se si utilizza un `WKWebView` per visualizzare video, impostare la `AllowsPictureInPictureMediaPlayback` proprietà su `false`.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato i passaggi necessari per assicurarsi che un'app Novell. iOS venga eseguita e si comporti correttamente nella nuova funzionalità multitasking di iOS 9 per gli iPad. Inoltre, ha trattato la rinuncia al multitasking per le app in cui non è una scelta ottimale.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Multitasking (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)
- [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adozione di miglioramenti multitasking in iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Post di blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
