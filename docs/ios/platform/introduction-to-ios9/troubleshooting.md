---
title: Risoluzione dei problemi
description: Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo di iOS 9 in App xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 6ea274ca198a4ca76a87198b02b6229e0e1c88b2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo di iOS 9 in App xamarin. IOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Si è verificato un problema durante l'analisi di XML

Xamarin iOS progettazione non supporta ancora le funzionalità di Xcode 7. Gli storyboard non verranno caricato nella finestra di progettazione con _"Si è verificato un problema durante l'analisi di XML"_ durante il tentativo di utilizzare di nuovo iOS 9 (Xcode 7) gli elementi della finestra di progettazione, ad esempio StackView.

iOS supporto di progettazione per le funzionalità di Xcode 7 è destinata alla nuova versione di funzionalità di ciclo 6. La versione di anteprima di ciclo 6 è attualmente disponibile nel canale alfa e un supporto limitato per le nuove funzionalità di Xcode 7.

Soluzione alternativa parziale per Visual Studio per Mac: lo storyboard di mouse e scegliere **Apri con** > **Xcode interfaccia generatore**.

## <a name="where-are-the-ios-8-simulators"></a>Dove sono iOS 8 simulatori?

Se è stato installato Xcode 7 (o versione successiva) verrà automaticamente sostituito tutti i simulatore di iOS 8 con il simulatore di iOS 9 per impostazione predefinita. Se è ancora necessario eseguire i test su iOS 8, è possibile avviare Xcode, quindi scaricare e installare i simulatore di iOS 8.

In Xcode, selezionare il **Xcode** menu quindi **preferenze...**   >  **Scarica**:

[ ![](troubleshooting-images/ios8.png "Scarica simulatori iOS 8")](troubleshooting-images/ios8.png)

Fare clic su di **verifica e installa** pulsante per reinstallare i simulatore di iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Vincolo di layout con errori di attributo sinistra/destra

In iOS 8 (e versioni precedenti), storyboard elementi dell'interfaccia utente potrebbero usare una combinazione di entrambi **destra** & **sinistra** attributi (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) e  **Iniziali** & **finale** attributi (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) nella stessa posizione.

Se lo stesso Storyboard in esecuzione in iOS 9, si verificherà un'eccezione nel formato seguente:

> Terminazione, a causa dell'eccezione non rilevata 'NSInvalidArgumentException', motivo: ' * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: Impossibile eseguire un vincolo tra iniziali o finali attributo e un attributo di destra/sinistra. Utilizzare iniziali o finali per entrambi o nessuno.'

iOS 9 Applica layout per l'utilizzo di **destra** & **sinistra** _o_ **iniziali**  &   **Finali** attributi ma *non* entrambi. Per risolvere questo problema, modificare tutti i vincoli di layout per utilizzare lo stesso attributo impostato all'interno del file di Storyboard.

Per ulteriori informazioni, vedere il [errore di vincolo di iOS 9](http://stackoverflow.com/questions/32692841/ios-9-constraint-error) discussione Overflow dello Stack.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>90535-errore ITMS: Chiave CFBundleExecutable imprevisto

Dopo il passaggio a iOS 9, da un'app utilizza 3 componenti di terze parti (in particolare il componente Google Maps esistente) compilato ed eseguito in iOS 8 (o versioni precedenti), durante il tentativo di inviare una nuova compilazione in iTunes Connect è possibile ottenere un errore nel formato:

> Errore ITMS-90535: La chiave CFBundleExecutable imprevista. Il bundle di 'Payload/app-name.app/component.bundle' non contiene un file eseguibile di bundle...

Questo problema può in genere essere risolto individuando il bundle denominato nel progetto successivamente - come suggerisce il messaggio di errore - modificare il `Info.plist` che si trova in bundle rimuovendo il `CFBundleExecutable` chiave. Il `CFBundlePackageType` chiave deve essere impostata su `BNDL` anche.

Dopo aver apportato queste modifiche, eseguire una nuova e ricompilare l'intero progetto. Dovrebbe essere possibile inviare a iTunes Connect senza problemi dopo aver apportato queste modifiche.

Per ulteriori informazioni, consultare la [Overflow dello Stack](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) discussione.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake (-9824) non riuscita

Quando si tenta di connettersi a internet, direttamente o da una visualizzazione web a iOS 9, si potrebbe ricevere un errore nel formato:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

O nel formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

Sicurezza trasporto App (AT) iOS9, garantisce connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app. AT richiede inoltre la comunicazione con il `HTTPS` protocollo e la comunicazione di alto livello dell'API da crittografare con TLS versione 1.2 PFS.

Poiché AT è attivata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (montagna di El Capitan), tutte le connessioni utilizzando `NSURLConnection`, `CFURL` o `NSURLSession` saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.

Vedere il [Opting-Out di at](~/ios/app-fundamentals/ats.md) sezione del nostro [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md) guide per informazioni su come risolvere il problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Le app di esistenti non vengono eseguiti su iOS 9

Vedere il nostro [informazioni sulla compatibilità di iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) per istruzioni su come ricompilare e ridistribuire le app esistenti in esecuzione in iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView è Null nei costruttori

**Motivo:** In iOS 9 di `initWithFrame:` costruttore è obbligatorio, a causa di modifiche del comportamento in iOS 9 come il [stati documentazione UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se è necessario creare una nuova cella di una classe per l'identificatore specificato è stato registrato, la cella a questo punto viene inizializzata chiamando il relativo `initWithFrame:` metodo.

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

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView non riesce a Init con codificatore durante il caricamento di una vista da un XI/Nib

**Motivo:** il `initWithCoder:` costruttore è quella chiamato durante il caricamento di una vista da un file di interfaccia generatore XI. Se questo costruttore non viene esportato il codice non gestito non è possibile chiamare la versione gestita. In precedenza (ad es. in iOS 8) di `IntPtr` costruttore è stato richiamato per inizializzare una vista.

**Correzione:** creazione ed esportazione di `initWithCoder:` costruttore analogo al seguente:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempio correlato: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Messaggio Dyld: Nessuna immagine di Cache con nome...

Potrebbe verificarsi un arresto anomalo del sistema con le seguenti informazioni nel log:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** si tratta di un bug nel linker Apple nativo, si verifica quando vengono rendere pubblico un framework privato (JavaScriptCore è stato reso pubblico in iOS 7, prima che sia stato di un framework privato), e la destinazione di distribuzione dell'app è una versione di iOS quando il Framework è privata. In questo caso il linker Apple verrà collegato con la versione di framework anziché la versione pubblica privata.

**Correzione:** questo verrà risolto per iOS 9, ma non esiste una soluzione semplice, è possibile applicare manualmente nel frattempo: destinati solo a una versione iOS successiva nel progetto (è possibile provare in questo caso iOS 7). Altri Framework possono presentare problemi simili, ad esempio il framework WebKit è stato reso pubblico in iOS 8 (e destinato a iOS 7 causerà questo errore; necessario scegliere come destinazione iOS 8 utilizzare WebKit nell'app).

## <a name="untrusted-enterprise-developer"></a>Sviluppatore aziendale non attendibili

Quando si tenta di eseguire la versione di iOS 9 di app xamarin iOS reale hardware, è possibile che venga visualizzato un messaggio che informa che l'account sviluppatore non è stato attendibili sul dispositivo. Ad esempio:

[ ![](troubleshooting-images/untrusted01.png "Avviso di Enterprise Developer non attendibile")](troubleshooting-images/untrusted01.png)

Per risolvere questo problema, effettuare le operazioni seguenti:

1. Avviare Xcode (ultima versione beta) sullo sviluppo Mac.
2. Selezionare **dispositivi** dal **finestra** menu per aprire la finestra di dispositivi: 

    [ ![](troubleshooting-images/untrusted02.png "La finestra di dispositivi")](troubleshooting-images/untrusted02.png)
3. Sotto il **dispositivi** sul lato di pannello, selezionare il dispositivo, pulsante destro del mouse e selezionare **Mostra profili di Provisioning...** : 

    [ ![](troubleshooting-images/untrusted03.png "Profili di Provisioning SShow")](troubleshooting-images/untrusted03.png)
4. Selezionare ogni profilo di provisioning attualmente nel dispositivo e fare clic su di  **-**  pulsante eliminarla: 

    [ ![](troubleshooting-images/untrusted04.png "L'eliminazione di un profilo di provisioning")](troubleshooting-images/untrusted04.png)
5. Dal **Xcode** dal menu **preferenze...**  e **account**: 

    [ ![](troubleshooting-images/untrusted05.png "Preferenze dell'account Xcode")](troubleshooting-images/untrusted05.png)
6. Fare clic su di **visualizzare i dettagli...**  , quindi scegliere il **scaricare tutti** pulsante: 

    [ ![](troubleshooting-images/untrusted06.png "Scaricare tutti i profili")](troubleshooting-images/untrusted06.png)
7. Al termine dell'aggiornamento nell'elenco, fare clic su di **eseguita** pulsante e chiudere la finestra Preferenze.
8. Rimuovere la versione esistente dell'app xamarin che si sta tentando di test dal dispositivo iOS.
9. Tornare a Visual Studio per Mac, eseguire una compilazione pulita e provare a eseguire nuovamente l'app nel dispositivo.

Potrebbe essere necessario arrestare e riavviare Visual Studio per Mac sono visibili i nuovi profili di provisioning caricati da Xcode. Potrebbe inoltre essere necessario regolare il **firma Bundle iOS** opzioni per l'applicazione di xamarin selezionare i nuovi profili di provisioning.

## <a name="launch-screen-issues"></a>Avviare i problemi relativi alle schermate

iOS 9 Applica ora i requisiti della schermata di avvio in modo che la stessa immagine di avvio non può essere riutilizzata per supportare gli orientamenti diversi di interfaccia. Vedere Apple [UILanchImage riferimento](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) per ulteriori informazioni.

Facoltativamente, è possibile utilizzare un file di storyboard per presentare una schermata di avvio dell'app rispetto all'utilizzo di un set di **PNG** file di immagine. Questo è Apple preferita presentare le schermate di avvio. Consultare il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) Guida per ulteriori informazioni.

Infine, l'app deve utilizzare un file di storyboard per la schermata di avvio e supporta tutti gli orientamenti interfaccia quattro (verticale, verticale capovolto, orizzontale sinistra e destra orizzontale) da considerare per l'esecuzione in un pannello diapositiva su o in modalità di visualizzazione suddivisa. Per ulteriori dettagli sulle nuove funzionalità multitasking di iOS 9, vedere il nostro [Multitasking per iPad](~/ios/platform/multitasking.md) Guida.

## <a name="nsinternalinconsistencyexception-exception"></a>Eccezione NSInternalInconsistencyException

Durante la compilazione e l'esecuzione di un'app xamarin esistente per iOS 9 si potrebbe ricevere un errore nel formato:

> Generata eccezione di Objective-C.  Nome: NSInternalInconsistencyException motivo: le finestre dell'applicazione devono avere un controller di visualizzazione principale alla fine dell'avvio dell'applicazione

Si tratta di errore viene generato perché Windows app devono avere un Controller di visualizzazione principale alla fine dell'avvio dell'applicazione e non l'app esistente.

Esistono almeno due possibili soluzioni per questo problema:

1. App di aggiornamento di usare i file storyboard anziché `xib` file per definire l'interfaccia utente. Questo richiede molto tempo a seconda delle dimensioni dell'app e le informazioni sull'utilizzo di iOS Designer (o interfaccia generatore del Xcode) agli storyboard di layout. Per ulteriori informazioni, vedere il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.
2. Il programma di installazione `RootViewController` proprietà della finestra di app in `FinishedLaunching` metodo `AppDelegate` classe in modo che punti a un Controller di visualizzazione nell'interfaccia utente dell'applicazione.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quando inizializzare visualizzazioni e controller di visualizzazione

Con xamarin. IOS è possibile eseguire l'inizializzazione di Controller di visualizzazione o una vista all'interno di costruttori che vengono chiamati quando qualcosa viene esposta al codice gestito, ma interrompe il progetto iOS.

In generale non è necessario inizializzare tutto ciò che è possibile richiamare codice Objective-C dal costruttore perché non è possibile assicurarsi quando verrà chiamato. Anche significa che contiene un migliori risorse (altri ctor) o chiamate a eseguire l'override (come Objective-C è associato alcun evento) in cui deve essere eseguita l'inizializzazione.



## <a name="related-links"></a>Collegamenti correlati

- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Aggiornare le app xamarin a iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
