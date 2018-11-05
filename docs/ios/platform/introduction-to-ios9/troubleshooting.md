---
title: Xamarin. IOS 9-risoluzione dei problemi
description: Questo articolo fornisce vari suggerimenti sulla risoluzione dei problemi per l'uso di iOS 9 di xamarin. IOS. Suggerimenti per coprire l'analisi XML-, simulatori, i vincoli di layout, i problemi di rete e numerosi altri argomenti.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 322bb630194f973d37d7ca27a0ca9fe1b548b240
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107215"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. IOS 9-risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'uso di iOS 9 nelle App xamarin. IOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Si è verificato un problema durante l'analisi XML

Xamarin iOS Designer non supporta ancora le funzionalità di Xcode 7. Gli storyboard avrà esito negativo da caricare nella finestra di progettazione con _"Si è verificato un problema durante l'analisi XML"_ quando provano a usare nuovi iOS 9 (Xcode 7) gli elementi della finestra di progettazione, ad esempio StackView.

la prossima versione di funzionalità di ciclo 6 è destinata a iOS supporto di progettazione per le funzionalità di Xcode 7. La versione di anteprima di ciclo 6 è attualmente disponibile nel canale alfa e ha supporto limitato per le nuove funzionalità di Xcode 7.

Soluzione alternativa parziale per Visual Studio per Mac: fare doppio clic sullo storyboard e scegliere **Apri con** > **Interface Builder Xcode**.

## <a name="where-are-the-ios-8-simulators"></a>Dove si trovano iOS 8 simulatori?

Se è stato installato Xcode 7 (o versione successiva) viene automaticamente sostituiscono tutti i simulatori iOS 8 con i simulatori iOS 9 per impostazione predefinita. Se è ancora necessario eseguire il test in iOS 8, è possibile avviare Xcode, quindi scaricare e installare i simulatori iOS 8.

In Xcode, selezionare la **Xcode** menu quindi **preferenze...**   >  **Scarica**:

[![](troubleshooting-images/ios8.png "Scarica i simulatori iOS 8")](troubleshooting-images/ios8.png#lightbox)

Scegliere il **verifica e installa ora** pulsante per reinstallare i simulatori iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Vincolo di layout con errori di attributi di sinistra/destra

In iOS 8 (e versioni precedenti), storyboard elementi dell'interfaccia utente potrebbe usare una combinazione di entrambi **a destra** & **sinistra** attributi (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) e  **Leader** & **finale** gli attributi (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) nello stesso layout.

Se lo Storyboard stesso in esecuzione in iOS 9, si verificherà un'eccezione nel formato seguente:

> Terminare l'app a causa dell'eccezione non rilevata 'NSInvalidArgumentException', motivo: ' * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: non è possibile creare un vincolo tra iniziali e finali attributo e un attributo di destra/sinistra. Usare iniziali e finali per entrambi o nessuno.'

iOS 9 Applica layout per l'utilizzo di **a destra** & **sinistra** _oppure_ **iniziali**  &   **Finali** attributi ma *non* entrambi. Per risolvere questo problema, modificare tutti i vincoli del layout per usare lo stesso attributo impostato all'interno del file di Storyboard.

Per altre informazioni, vedere la [errore di vincolo di iOS 9](http://stackoverflow.com/questions/32692841/ios-9-constraint-error) discussione su Stack Overflow.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>90535-errore ITMS: Chiave CFBundleExecutable imprevisto

Dopo il passaggio a iOS 9, da un'app Usa 3rd party i componenti (in particolare il componente Google Maps esistente) compilato ed eseguito in iOS 8 (o versioni precedenti), durante il tentativo di inviare la nuova compilazione in iTunes Connect è possibile ottenere un errore nel formato:

> Errore ITMS-90535: Imprevisto CFBundleExecutable chiave. Il bundle di 'Payload/app-name.app/component.bundle' non contiene un file eseguibile di bundle...

Questo problemi possono in genere essere risolti mediante la ricerca del pacchetto denominato nel progetto e - solo come suggerito dal messaggio di errore - modificare la `Info.plist` che si trova in bundle rimuovendo il `CFBundleExecutable` chiave. Il `CFBundlePackageType` chiave deve essere impostata su `BNDL` anche.

Dopo aver apportato queste modifiche, è normale e ricompila l'intero progetto. Dovrebbe essere possibile inviare a iTunes Connect senza problemi dopo aver apportato queste modifiche.

Per altre informazioni, vedere questo [Stack Overflow](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) discussione.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake è verificato un errore (-9824)

Quando si prova a connettersi a internet, direttamente o da una visualizzazione web in iOS 9, è possibile ottenere un errore nel formato:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

O nel formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

In iOS9, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app. ATS richiede inoltre la comunicazione con il `HTTPS` protocollo e la comunicazione di API ad alto livello viene crittografata utilizzando TLS versione 1.2 con PFS.

Perché è abilitata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (El Capitan), tutte le connessioni usando la ATS `NSURLConnection`, `CFURL` o `NSURLSession` saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

Vedere il [ATS Opting esplicitamente](~/ios/app-fundamentals/ats.md) sezione del nostro [App Transport Security](~/ios/app-fundamentals/ats.md) Guida per informazioni su come risolvere questo problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Le app esistenti non vengono eseguiti su iOS 9

Vedere la [informazioni sulla compatibilità di iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) per istruzioni su ricompilare e ridistribuire le app esistenti per l'esecuzione in iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView è Null nei costruttori

**Motivo:** In iOS 9 il `initWithFrame:` costruttore è ora obbligatorio, a causa di modifiche del comportamento in iOS 9, come le [stati documentazione UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se una classe per l'identificatore specificato è stato registrato e deve essere creata una nuova cella, la cella a questo punto viene inizializzata chiamando il `initWithFrame:` (metodo).

**Correzione:** Aggiungi il `initWithFrame:` costruttore analogo al seguente:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempi correlati: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView non riesce a Init con programmatore durante il caricamento di una vista da un file Xib/Nib

**Motivo:** il `initWithCoder:` costruttore è quella chiamata durante il caricamento di una vista da un file Xib di Interface Builder. Se questo costruttore non viene esportato il codice non gestito non è possibile chiamare la versione gestita di esso. In precedenza (ad es. in iOS 8) di `IntPtr` costruttore è stato richiamato per inizializzare visualizzazione.

**Correzione:** creazione ed esportazione di `initWithCoder:` costruttore analogo al seguente:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempio correlato: [avvia una Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Messaggio Dyld: Nessuna immagine di Cache con nome...

Potrebbe verificarsi un arresto anomalo del sistema con le informazioni seguenti nel registro:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** si tratta di un bug nel linker nativo di Apple, cosa che accade quando effettuano una struttura di private pubblico (JavaScriptCore è stato reso pubblico in iOS 7, prima che fosse una struttura di private), e la distribuzione dell'app sia destinata per una versione iOS durante il sia privato al Framework. In questo caso si collegherà linker di Apple con la versione di framework anziché la versione pubblica privata.

**Correzione:** questo problema verrà risolto per iOS 9, ma non esiste una soluzione semplice è possibile applicare manualmente nel frattempo: semplicemente come destinazione una versione iOS successiva nel progetto (è possibile provare in questo caso iOS 7). Altri Framework possono presentare problemi simili, ad esempio il framework di WebKit è stato reso pubblico in iOS 8 e destinate a iOS 7 causerà questo errore, si deve avere come destinazione iOS 8 per usare WebKit nell'app.

## <a name="untrusted-enterprise-developer"></a>Sviluppatori aziendali attendibili

Quando si tenta di eseguire la versione di iOS 9 dell'app xamarin. IOS su hardware iOS reali, potrebbe essere visualizzato un messaggio che informa che l'account per sviluppatore non è considerato affidabile sul dispositivo. Ad esempio:

[![](troubleshooting-images/untrusted01.png "Avviso di Enterprise Developer non attendibile")](troubleshooting-images/untrusted01.png#lightbox)

Per risolvere questo problema, effettuare le operazioni seguenti:

1. Avviare Xcode (la versione beta più recente) sullo sviluppo Mac.
2. Selezionare **periferiche** dalle **finestra** menu per aprire la finestra di dispositivi: 

    [![](troubleshooting-images/untrusted02.png "La finestra di dispositivi")](troubleshooting-images/untrusted02.png#lightbox)
3. Sotto il **dispositivi** lato pannello, selezionare il dispositivo, pulsante destro del mouse e selezionare **Mostra profili di Provisioning...** : 

    [![](troubleshooting-images/untrusted03.png "Profili di Provisioning SShow")](troubleshooting-images/untrusted03.png#lightbox)
4. Selezionare ogni profilo di provisioning attualmente nel dispositivo e fare clic sui **-** pulsante eliminarlo: 

    [![](troubleshooting-images/untrusted04.png "L'eliminazione di un profilo di provisioning")](troubleshooting-images/untrusted04.png#lightbox)
5. Dal **Xcode** dal menu **preferenze...**  e **account**: 

    [![](troubleshooting-images/untrusted05.png "Preferenze dell'account di Xcode")](troubleshooting-images/untrusted05.png#lightbox)
6. Fare clic su di **visualizzare i dettagli...**  , quindi scegliere il **Scarica tutto** pulsante: 

    [![](troubleshooting-images/untrusted06.png "Scarica tutti i profili")](troubleshooting-images/untrusted06.png#lightbox)
7. Al termine dell'aggiornamento nell'elenco, fare clic sui **Done** pulsante e chiudere la finestra Preferenze.
8. Rimuovere la versione esistente dell'app xamarin. IOS che si stava tentando di test dal dispositivo iOS.
9. Tornare a Visual Studio per Mac, eseguire una compilazione pulita e provare a eseguire nuovamente l'app nel dispositivo.

Si potrebbe essere necessario arrestare e riavviare Visual Studio per Mac prima vengono visualizzati i nuovi profili di provisioning caricati da Xcode. Potrebbe anche essere necessario regolare i **firma del Bundle iOS** opzioni per l'app xamarin. IOS selezionare i profili di provisioning di nuovi.

## <a name="launch-screen-issues"></a>Problemi relativi alle schermate di avvio

iOS 9 applica i requisiti di schermata avviare ora la stessa immagine di avvio non è più può essere riutilizzato per supportare gli orientamenti di interfaccia diverse. Vedere di Apple [UILanchImage riferimento](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) per altre informazioni.

Facoltativamente, è possibile usare un file storyboard alla schermata di avvio dell'app rispetto all'utilizzo di un set di presentare **PNG** file di immagine. Questo è l'ora preferite dell'Apple fino alla presentazione di schermate di avvio. Vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) Guida per altre informazioni.

Infine, l'app deve usare un file storyboard per la schermata di avvio e supportare tutti gli orientamenti interfaccia quattro (verticale, verticale capovolto, orizzontale sinistra e destra Landscape) per essere considerato per l'esecuzione in un pannello diapositiva su o in modalità doppia visualizzazione. Per altre informazioni sulle nuove funzionalità il multitasking di iOS 9, vedere la [Multitasking per iPad](~/ios/platform/multitasking.md) Guida.

## <a name="nsinternalinconsistencyexception-exception"></a>Eccezione NSInternalInconsistencyException

Durante la compilazione e l'esecuzione di un'app xamarin. IOS esistente per iOS 9 si verifichi un errore nel formato:

> Generata eccezione di Objective-C.  Nome: NSInternalInconsistencyException motivo: applicazione di windows devono avere un controller visualizzazione radice alla fine di avvio dell'applicazione

Si tratta di errore viene generato perché Windows app devono avere un Controller visualizzazione radice alla fine di avvio dell'applicazione e non un'app esistente.

Sono disponibili almeno due possibili soluzioni alternative per questo problema:

1. Aggiorna app usare file di storyboard anziché `xib` file per definire l'interfaccia utente. Anche questo richiede molto tempo a seconda delle dimensioni dell'app e della Knowledge Base relativo all'uso di iOS Designer (o Interface Builder di Xcode) agli storyboard di layout. Per altre informazioni, vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.
2. Il programma di installazione `RootViewController` proprietà dell'app finestra nel `FinishedLaunching` metodo `AppDelegate` classe in modo che punti a un Controller di visualizzazione nell'interfaccia utente dell'app.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quando inizializzare le visualizzazioni e controller di visualizzazione

Con xamarin. IOS è possibile eseguire l'inizializzazione di visualizzazione o Controller di visualizzazione all'interno di costruttori che vengono chiamati quando qualcosa viene esposta al codice gestito ma interrompe il progetto iOS.

In generale non è necessario inizializzare tutto ciò che può richiamare il codice Objective-C dal costruttore perché non è possibile assicurarsi quando verrà chiamato. Questo significa tuttavia è presente un migliori risorse (altre. ctor) o chiamate a eseguire l'override (come Objective-C è associato alcun evento) in cui deve essere eseguita l'inizializzazione.

## <a name="related-links"></a>Collegamenti correlati

- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [What ' s New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [L'aggiornamento delle App xamarin. IOS per iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
