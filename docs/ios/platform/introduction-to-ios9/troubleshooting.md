---
title: Novell. iOS 9-risoluzione dei problemi
description: Questo articolo fornisce vari suggerimenti per la risoluzione dei problemi per l'uso di iOS 9 in Novell. iOS. Suggerimenti per l'analisi XML, i simulatori, i vincoli di layout, i problemi di rete e molti altri argomenti.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 437698fcda6e85090cd7bdce90959300436e0bc2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031751"
---
# <a name="xamarinios-9--troubleshooting"></a>Novell. iOS 9-risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per l'uso di iOS 9 nelle app Novell. iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Si è verificato un problema durante l'analisi del codice XML

Novell iOS designer non supporta ancora le funzionalità di Xcode 7. Non è possibile caricare gli storyboard nella finestra di progettazione con _"si è verificato un problema durante l'analisi del codice XML"_ quando si tenta di usare nuovi elementi della finestra di progettazione iOS 9 (Xcode 7), ad esempio StackView.

il supporto della finestra di progettazione iOS per le funzionalità di Xcode 7 è destinato alla prossima versione della funzionalità ciclo 6. La versione di anteprima del ciclo 6 è attualmente disponibile nel canale alfa e ha un supporto limitato per le nuove funzionalità di Xcode 7.

Soluzione alternativa parziale per Visual Studio per Mac: fare clic con il pulsante destro del mouse sullo storyboard e scegliere **Apri con** > **Interface Builder Xcode**.

## <a name="where-are-the-ios-8-simulators"></a>Dove si trovano i simulatori iOS 8?

Se è stato installato Xcode 7 (o versione successiva), per impostazione predefinita verranno sostituiti automaticamente tutti i simulatori iOS 8 con i simulatori iOS 9. Se è ancora necessario eseguire test su iOS 8, è possibile avviare Xcode, quindi scaricare e installare i simulatori iOS 8.

In Xcode selezionare il menu **Xcode** , quindi **Preferenze...**  > **Downloads**:

[![](troubleshooting-images/ios8.png "iOS 8 Simulators Downloads")](troubleshooting-images/ios8.png#lightbox)

Fare clic sul pulsante **Controlla e installa ora** per reinstallare i simulatori iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Vincolo di layout con errori di attributo Left/Right

In iOS 8 (e versioni precedenti), gli elementi dell'interfaccia utente negli storyboard possono usare una combinazione di attributi **Right** & **Left** (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) e degli attributi **finali** & **iniziali** (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) nel stesso layout.

Se lo stesso storyboard in viene eseguito in iOS 9, viene generata un'eccezione nel formato seguente:

> Terminazione dell'app a causa di un'eccezione non rilevata ' NSInvalidArgumentException '. motivo:' * * * + [NSLayoutConstraint constraintWithItem: attributo: relatedBy: toItem: attributo: moltiplicatore: costante:]: Impossibile eseguire un vincolo tra un elemento iniziali/finale attribute e un attributo Right/Left. Usare il carattere iniziali/finali per entrambi o nessuno dei due.

iOS 9 applica i layout in modo da usare gli attributi **finali** a **destra** & a **sinistra** _o_ & **iniziali** , ma *non* entrambi. Per risolvere questo problema, modificare tutti i vincoli di layout in modo da usare lo stesso set di attributi all'interno del file dello storyboard.

Per ulteriori informazioni, vedere la discussione sull' [errore di vincolo iOS 9](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) stack overflow.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ERRORE ITMS-90535: chiave CFBundleExecutable imprevista

Dopo il trasferimento a iOS 9, da un'app usa componenti di terze parti (in particolare il componente Google Maps esistente) che sono stati compilati ed eseguiti in iOS 8 (o versioni precedenti), quando si tenta di inviare la nuova compilazione a iTunes Connect è possibile ottenere un errore nel formato:

> ERRORE ITMS-90535: chiave CFBundleExecutable imprevista. Il bundle in ' payload/app-name. app/Component. bundle ' non contiene un file eseguibile del bundle...

Questi problemi possono in genere essere risolti individuando il bundle denominato nel progetto, così come il messaggio di errore suggerisce di modificare la `Info.plist` che si trova nel bundle rimuovendo la chiave di `CFBundleExecutable`. Anche la chiave di `CFBundlePackageType` deve essere impostata su `BNDL`.

Dopo avere apportato queste modifiche, eseguire una pulizia e ricompilare l'intero progetto. Si dovrebbe essere in grado di inviare a iTunes Connect senza problemi dopo aver apportato queste modifiche.

Per ulteriori informazioni, vedere la discussione [stack overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) .

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Errore SSLHandshake CFNetwork non riuscito (-9824)

Quando si tenta di connettersi a Internet, direttamente o da una visualizzazione Web in iOS 9, è possibile che venga visualizzato un errore nel formato seguente:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

O nel formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

In iOS9, la sicurezza del trasporto app (ATS) applica connessioni sicure tra le risorse Internet, ad esempio il server back-end dell'app, e l'app. Inoltre, ATS richiede la comunicazione con il protocollo `HTTPS` e la comunicazione API di alto livello per la crittografia con la versione 1,2 di TLS con segretezza diretta.

Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9 e OS X 10,11 (El Capitan), tutte le connessioni che usano `NSURLConnection`, `CFURL` o `NSURLSession` saranno soggette ai requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.

Per informazioni su come risolvere questo problema, vedere la sezione relativa alla disattivazione [dell'ATS](~/ios/app-fundamentals/ats.md) nella Guida alla [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md) .

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Le app esistenti non vengono eseguite in iOS 9

Per istruzioni sulla ricompilazione e la ridistribuzione delle app esistenti per l'esecuzione in iOS 9, vedere le [informazioni sulla compatibilità con iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) .

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView è null nei costruttori

**Motivo:** In iOS 9 è ora necessario il costruttore `initWithFrame:`, a causa di modifiche del comportamento in iOS 9 come [Stati della documentazione UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se è stata registrata una classe per l'identificatore specificato ed è necessario creare una nuova cella, la cella viene ora inizializzata chiamando il relativo metodo `initWithFrame:`.

**Correzione:** Aggiungere il costruttore `initWithFrame:` come segue:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempi correlati: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView non riesce a inizializzare con coder durante il caricamento di una visualizzazione da un XIB/pennino

**Motivo:** Il costruttore `initWithCoder:` è quello chiamato durante il caricamento di una visualizzazione da un file di Interface Builder XIB. Se questo costruttore non viene esportato, il codice non gestito non può chiamare la versione gestita. In precedenza (ad esempio in iOS 8) il costruttore `IntPtr` è stato richiamato per inizializzare la visualizzazione.

**Correzione:** Creare ed esportare il costruttore `initWithCoder:` in questo modo:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempio correlato: [chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Messaggio dyld: nessuna immagine della cache con nome...

È possibile che si verifichi un arresto anomalo con le informazioni seguenti nel log:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Si tratta di un bug nel linker nativo di Apple, che si verifica quando rende pubblico un Framework privato (JavaScriptCore è stato reso pubblico in iOS 7, prima che fosse un Framework privato) e la destinazione di distribuzione dell'app è per una versione di iOS quando il Framework era privato. In questo caso il linker di Apple verrà collegato con la versione privata del Framework invece che con la versione pubblica.

**Correzione:** Questo problema verrà risolto per iOS 9, ma esiste una semplice soluzione alternativa che è possibile applicare nel frattempo: è sufficiente fare riferimento a una versione successiva di iOS nel progetto (in questo caso, è possibile provare iOS 7). Altri Framework possono presentare problemi simili. ad esempio, il Framework WebKit è stato reso pubblico in iOS 8 (e pertanto la destinazione di iOS 7 comporterà l'errore. è consigliabile usare iOS 8 per usare WebKit nell'app).

## <a name="untrusted-enterprise-developer"></a>Sviluppatore aziendale non attendibile

Quando si tenta di eseguire la versione iOS 9 dell'app Novell. iOS su hardware iOS reale, potrebbe essere presente un messaggio che informa che l'account sviluppatore non è stato considerato attendibile nel dispositivo. Esempio:

[![](troubleshooting-images/untrusted01.png "Untrusted Enterprise Developer alert")](troubleshooting-images/untrusted01.png#lightbox)

Per risolvere questo problema, eseguire le operazioni seguenti:

1. Avviare Xcode (la versione beta più recente) nel computer di sviluppo.
2. Selezionare **dispositivi** dal menu **finestra** per aprire la finestra dispositivi: 

    [![](troubleshooting-images/untrusted02.png "The Devices Window")](troubleshooting-images/untrusted02.png#lightbox)
3. Nel pannello laterale **dispositivi** selezionare il dispositivo, fare clic con il pulsante destro del mouse e scegliere **Mostra profili di provisioning...** : 

    [![](troubleshooting-images/untrusted03.png "SShow Provisioning Profiles")](troubleshooting-images/untrusted03.png#lightbox)
4. Selezionare il profilo di provisioning attualmente nel dispositivo e fare clic sul pulsante **-** per eliminarlo: 

    [![](troubleshooting-images/untrusted04.png "Deleting a provisioning profile")](troubleshooting-images/untrusted04.png#lightbox)
5. Dal menu **Xcode** selezionare **Preferenze...** e **account**: 

    [![](troubleshooting-images/untrusted05.png "Xcode account preferences")](troubleshooting-images/untrusted05.png#lightbox)
6. Fare clic sul pulsante **Visualizza dettagli...** , quindi fare clic sul pulsante **Scarica tutto** : 

    [![](troubleshooting-images/untrusted06.png "Download all profiles")](troubleshooting-images/untrusted06.png#lightbox)
7. Al termine dell'aggiornamento dell'elenco, fare clic sul pulsante **fine** e chiudere la finestra delle preferenze.
8. Rimuovere la versione esistente dell'app Novell. iOS che si sta provando a testare dal dispositivo iOS.
9. Tornare a Visual Studio per Mac, eseguire una compilazione pulita e provare a eseguire di nuovo l'app nel dispositivo.

Potrebbe essere necessario arrestare e riavviare Visual Studio per Mac prima che vengano visualizzati i nuovi profili di provisioning caricati da Xcode. Potrebbe anche essere necessario modificare le opzioni di **firma del bundle iOS** per l'app Novell. iOS per selezionare i nuovi profili di provisioning.

## <a name="launch-screen-issues"></a>Problemi di avvio della schermata

iOS 9 applica ora i requisiti della schermata di avvio in modo che non sia più possibile riutilizzare la stessa immagine di avvio per supportare diversi orientamenti dell'interfaccia. Per ulteriori informazioni, vedere la Guida di [riferimento a UILanchImage](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) di Apple.

Facoltativamente, è possibile usare un file storyboard per presentare la schermata di avvio dell'app in contrapposizione all'uso di un set di file di immagine con **estensione png** . Questo è il modo preferito di Apple per presentare le schermate di avvio. Per ulteriori informazioni, vedere la Guida [introduttiva agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

Infine, l'app deve usare un file storyboard per la schermata di avvio e supportare tutti e quattro gli orientamenti dell'interfaccia (verticale, verticale a discesa, orizzontale sinistro e orizzontale a destra) da considerare per l'esecuzione in una diapositiva sopra il pannello o in modalità di visualizzazione divisa. Per altre informazioni sulle nuove funzionalità multitasking di iOS 9, vedere la guida per il multitasking [per iPad](~/ios/platform/multitasking.md) .

## <a name="nsinternalinconsistencyexception-exception"></a>Eccezione NSInternalInconsistencyException

Durante la compilazione e l'esecuzione di un'app Novell. iOS esistente per iOS 9 è possibile che si ottenga un errore nel formato seguente:

> Eccezione Objective-C generata.  Nome: NSInternalInconsistencyException motivo: le finestre dell'applicazione dovrebbero avere un controller visualizzazione radice alla fine dell'avvio dell'applicazione

Questo errore viene generato perché le finestre dell'app dovrebbero avere un controller di visualizzazione radice alla fine dell'avvio dell'applicazione e l'app esistente non lo è.

Esistono almeno due possibili soluzioni alternative per questo problema:

1. Aggiornare l'app per usare il file dello storyboard invece dei file `xib` per definire la relativa interfaccia utente. Questo richiede molto tempo a seconda delle dimensioni dell'app e della conoscenza dell'uso di iOS designer (o Interface Builder di Xcode) per gli storyboard del layout. Per ulteriori informazioni, vedere la documentazione [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .
2. Configurare `RootViewController` proprietà della finestra dell'app nel metodo `FinishedLaunching` della classe `AppDelegate` per puntare a un controller di visualizzazione nell'interfaccia utente dell'app.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quando inizializzare le visualizzazioni e i controller di visualizzazione

Con Novell. iOS è possibile fare in modo che l'inizializzazione di visualizzazione o visualizzazione del controller all'interno dei costruttori venga chiamata quando un elemento viene esposto nel codice gestito, ma interrompe la progettazione iOS.

In generale, non è consigliabile inizializzare qualsiasi elemento che possa richiamare il codice Objective-C dal costruttore, perché non è possibile assicurarsi quando verrà chiamato. Ciò significa anche che sono presenti migliori posizioni (other. ctor) o chiamate di cui eseguire l'override (in quanto Objective-C non ha eventi) in cui l'inizializzazione deve essere eseguita.

## <a name="related-links"></a>Collegamenti correlati

- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità di iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Aggiornamento delle app Novell. iOS in iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
