---
title: iOS estensioni in xamarin. IOS
description: Questo documento descrive le estensioni, che sono widget presentato da iOS nel contesto standard, ad esempio all'interno del centro di notifica. Illustra come creare un'estensione e consentire la comunicazione da app padre.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e02d7a13a1fd5b554943f9facd6c9f120096a6a5
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667815"
---
# <a name="ios-extensions-in-xamarinios"></a>estensioni di iOS in xamarin. IOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Creazione di estensioni in iOS, da [Xamarin University](https://university.xamarin.com/)**

Le estensioni, come introdotto in iOS 8, sono specializzate `UIViewControllers` che vengono presentati da iOS all'interno di contesti standard, ad esempio all'interno di **centro notifiche**, come richiesti dall'utente per eseguire i tipi di tastiera personalizzata specializzata input o altri contesti, come la modifica di una foto in cui l'estensione può fornire filtri effetto speciale.

Tutte le estensioni vengono installate insieme a un'app contenitore (con entrambi gli elementi scritti usando l'API unificata a 64 bit) e vengono attivate da un particolare punto di estensione in un'applicazione Host. E poiché verranno usate come supplementi alle funzioni di sistema esistente, devono essere prestazioni elevate, efficienti e affidabili. 

## <a name="extension-points"></a>Punti di estensione

|Tipo|Descrizione|Punto di estensione|Host App|
|--- |--- |--- |--- |
|Operazione|Editor specializzato o un visualizzatore per un determinato tipo di supporto|`com.apple.ui-services`|Qualsiasi|
|Provider di documento|Consente di usare un archivio remoto documenti dell'app|`com.apple.fileprovider-ui`|Le app usando un [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Tastiera|Tastiere alternative|`com.apple.keyboard-service`|Qualsiasi|
|Modifica foto|Modifica foto e la modifica|`com.apple.photo-editing`|Editor Photos.app|
|Condividi|Condivide i dati con i social network, messaggistica di servizi e così via.|`com.apple.share-services`|Qualsiasi|
|Oggi|"Widget" che vengono visualizzati sullo schermo oggi o centro notifiche|`com.apple.widget-extensions`|Oggi e centro notifiche|

[I punti di estensione aggiuntivi](~/ios/platform/introduction-to-ios10/index.md#app-extensions) sono state aggiunte in iOS 10.

## <a name="limitations"></a>Limitazioni

Le estensioni con un numero di limiti, alcuni dei quali sono universale a tutti i tipi (per istanza, nessun tipo di estensione può accedere le fotocamere o microfoni) mentre altri tipi di estensione possono presentare limitazioni specifiche sul loro utilizzo (ad esempio, personalizzate tastiere può essere usata per campi di immissione dati protetto, ad esempio per le password). 

Le limitazioni universali sono:

- Il [integrità Kit](~/ios/platform/healthkit.md) e [evento Kit UI](~/ios/platform/eventkit.md) non sono disponibili Framework
- Non è possibile usare le estensioni [estesi modalità in background](https://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- Le estensioni non possono accedere fotocamere o microfoni del dispositivo (anche se potrebbero accedere al file di supporto esistenti)
- Le estensioni non possono ricevere dati Drop Air (anche se è possibile trasmettere i dati tramite Drop aria)
- [UIActionSheet](xref:UIKit.UIActionSheet) e [UIAlertView](xref:UIKit.UIAlertView) non sono disponibili; è necessario usare le estensioni [UIAlertController](xref:UIKit.UIAlertController)
- Diversi membri della [UIApplication](xref:UIKit.UIApplication) non sono disponibili: [UIApplication.SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication.OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication.BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) and [UIApplication.EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS impone un limite di utilizzo di memoria 16 MB per le estensioni di oggi.
- Per impostazione predefinita le estensioni della tastiera non hanno accesso alla rete. Questo fattore influisce sul debug nel dispositivo (la restrizione non viene applicata nel simulatore), poiché xamarin. IOS richiede l'accesso alla rete affinché il debug. È possibile richiedere l'accesso di rete impostando il `Requests Open Access` valore nel file Info. plist del progetto per `Yes`. Vedere di Apple [Guida per tastiera personalizzata](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) per altre informazioni sulle limitazioni di estensione della tastiera.

Per le limitazioni singoli, vedere di Apple [Guida alla programmazione di estensione App](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

## <a name="distributing-installing-and-running-extensions"></a>La distribuzione, installazione e l'esecuzione delle estensioni

Le estensioni vengono distribuite da all'interno di un'app contenitore, che, a sua volta viene inviato e distribuito tramite l'App Store. Le estensioni distribuite con le app vengono installate a questo punto, ma l'utente deve abilitare in modo esplicito ogni estensione. I diversi tipi di estensioni sono abilitati in modi diversi, diverse richiedono all'utente di passare al **impostazioni** app e abilitare li da lì. Mentre altri sono abilitati nel punto di utilizzo, ad esempio l'abilitazione di un'estensione di condivisione durante l'invio di una foto. 

L'app in cui viene utilizzata l'estensione (in cui l'utente rileva il punto di estensione) viene definito il **app Host**, poiché si tratta dell'app che ospita l'estensione durante l'esecuzione. L'app che consente di installare l'estensione è il **app contenitore**, poiché si tratta dell'app contenente l'estensione al momento dell'installazione.  

In genere, l'app contenitore descrive l'estensione e assiste l'utente attraverso il processo di abilitazione.

## <a name="extension-lifecycle"></a>Ciclo di vita di estensione

Un'estensione può essere semplice come una singola [UIViewController](xref:UIKit.UIViewController) o le estensioni più complesse che presentano più schermate dell'interfaccia utente. Quando l'utente rileva un _punti di estensione_ (ad esempio, quando la condivisione di un'immagine), hanno la possibilità di scegliere tra le estensioni registrate per tale punto di estensione. 

Se si sceglie una delle app di estensioni, relativo `UIViewController` verranno create istanze e iniziare il ciclo di vita del Controller di visualizzazione normale. Tuttavia, a differenza una normale app che vengono sospesi ma in genere non vengono terminate quando l'utente termina l'interazione con essi, estensioni sono caricate, eseguite e quindi terminate ripetutamente.

Le estensioni possono comunicare con le proprie applicazioni Host tramite un [NSExtensionContext](xref:Foundation.NSExtensionContext) oggetto. Alcune estensioni sono disponibili operazioni che ricevono richiamate asincrone con i risultati. Questi callback verranno eseguiti sul thread in background e l'estensione deve tenere in considerazione questo; ad esempio, usando [NSObject.InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) se desiderano aggiornare l'interfaccia utente. Vedere le [comunica con l'App Host](#Communicating-with-the-Host-App) sezione di seguito per altri dettagli.

Per impostazione predefinita, le estensioni e le loro applicazioni contenitore non possono comunicare, pur essendo installati insieme. In alcuni casi, l'app contenitore è essenzialmente un "shipping" contenitore vuoto con lo scopo verrà resi disponibili dopo aver installato l'estensione. Tuttavia, se seconda delle circostanze, l'app contenitore e l'estensione può condividere le risorse da un'area comune. Inoltre, un **estensione per oggi** potrebbe richiedere l'app di contenitore per aprire un URL. Questo comportamento è illustrato nella [evolvere Widget del conto alla rovescia](https://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

## <a name="creating-an-extension"></a>Creazione di un'estensione

Estensioni (e le proprie App contenitore) devono essere file binari a 64 bit e compilato utilizzando la xamarin. IOS [Unified API](https://developer.xamarin.com/guides/cross-platform/macios/unified). Quando si sviluppa un'estensione, le soluzioni conterrà almeno due progetti: l'app contenitore e una per ogni estensione per il contenitore include del progetto. 

### <a name="container-app-project-requirements"></a>Requisiti del progetto app contenitore

L'app contenitore usato per installare l'estensione presenta i requisiti seguenti:

- È necessario mantenere un riferimento al progetto di estensione.   
- Deve trattarsi di un'app completa (deve essere in grado di avviare ed eseguire correttamente) anche se non fa altro che consentono di installare un'estensione. 
- Deve avere un identificatore del Bundle che costituisce la base per l'identificatore del Bundle dell'estensione per il progetto (vedere la sezione seguente per altri dettagli).

### <a name="extension-project-requirements"></a>Requisiti di progetto di estensione

Inoltre, il progetto dell'estensione è i requisiti seguenti:

- Deve avere un identificatore del Bundle che inizia con l'identificatore del Bundle dell'app relativo contenitore. Ad esempio, se il contenitore dell'app ha un identificatore del Bundle del `com.myCompany.ContainerApp`, potrebbe essere l'identificatore dell'estensione `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Deve definire la chiave `NSExtensionPointIdentifier`, con un valore appropriato (ad esempio `com.apple.widget-extension` per un **oggi stesso** widget centro notifiche), nel relativo `Info.plist` file.
- Deve anche definire *entrambi* il `NSExtensionMainStoryboard` chiave o il `NSExtensionPrincipalClass` chiave nel relativo `Info.plist` file con un valore appropriato:
    - Usare la `NSExtensionMainStoryboard` chiave per specificare il nome dello Storyboard che presenta l'interfaccia utente principale per l'estensione (meno `.storyboard`). Ad esempio, `Main` per il `Main.storyboard` file.
    - Usare il `NSExtensionPrincipalClass` chiave per specificare la classe che verrà inizializzata quando viene avviato l'estensione. Il valore deve corrispondere il **registrare** pari al `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Tipi specifici di estensioni possono avere requisiti aggiuntivi. Ad esempio, un **oggi** oppure **centro notifiche** classe dell'entità dell'estensione deve implementare [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> Se si avvia il progetto uno usando i modelli di estensioni forniti da Visual Studio per Mac, la maggior parte (se non tutti) questi requisiti verranno forniti e soddisfatti per l'utente automaticamente dal modello.

## <a name="walkthrough"></a>Procedura dettagliata 

Nella procedura riportata di seguito, si creerà un esempio **oggi stesso** widget che calcola il giorno e il numero di giorni rimanenti nel corso dell'anno:

[![](extensions-images/carpediemscreenshot-sm.png "Un widget oggi di esempio che calcola il giorno e il numero di giorni rimanenti nel corso dell'anno")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Creazione della soluzione

Per creare la soluzione necessaria, eseguire le operazioni seguenti:

1. Innanzitutto, creare un nuova iOS **App visualizzazione singola** sul progetto e scegliere il **successivo** pulsante: 

    [![](extensions-images/today01.png "Innanzitutto, creare un progetto di App visualizzazione singola iOS nuovo e fare clic sul pulsante Avanti")](extensions-images/today01.png#lightbox)
2. Denominare il progetto `TodayContainer` e scegliere il **successivo** pulsante: 

    [![](extensions-images/today02.png "Denominare il progetto TodayContainer e fare clic sul pulsante Avanti")](extensions-images/today02.png#lightbox)
3. Verificare i **nome progetto** e **SolutionName** e fare clic sui **Create** pulsante per creare la soluzione: 

    [![](extensions-images/today03.png "Verificare il nome del progetto e SolutionName e fare clic sul pulsante Crea per creare la soluzione")](extensions-images/today03.png#lightbox)
4. Successivamente, nella **Esplora soluzioni**, pulsante destro del mouse sulla soluzione e aggiungere un nuovo **iOS estensione** dal progetto di **oggi estensione** modello: 

    [![](extensions-images/today04.png "Successivamente, in Esplora soluzioni, fare doppio clic sulla soluzione e aggiungere un nuovo progetto di estensione iOS dal modello di estensione per oggi")](extensions-images/today04.png#lightbox)
5. Denominare il progetto `DaysRemaining` e scegliere il **successivo** pulsante: 

    [![](extensions-images/today05.png "Denominare il progetto DaysRemaining e fare clic sul pulsante Avanti")](extensions-images/today05.png#lightbox)
6. Rivedere il progetto e scegliere il **crea** pulsante crearla: 

    [![](extensions-images/today06.png "Rivedere il progetto e scegliere il pulsante Crea per crearla")](extensions-images/today06.png#lightbox)

La soluzione risultante avranno ora due progetti, come illustrato di seguito:

[![](extensions-images/today07.png "La soluzione risultante avranno ora due progetti, come illustrato di seguito")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Creazione dell'interfaccia utente di estensione

Successivamente, sarà necessario progettare l'interfaccia per il **oggi stesso** widget. Ciò può essere eseguita utilizzando uno Storyboard o creando l'interfaccia utente nel codice. Entrambi i metodi verranno descritta di seguito in dettaglio.

#### <a name="using-storyboards"></a>Usando gli storyboard

Per compilare l'interfaccia utente con uno Storyboard, effettuare le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic il progetto di estensione `Main.storyboard` file per aprirlo e modificarlo: 

    [![](extensions-images/today08.png "Fare doppio clic sul file Main. Storyboard progetti di estensione per aprirlo e modificarlo")](extensions-images/today08.png#lightbox)
2. Selezionare l'etichetta che è stato aggiunto automaticamente all'interfaccia utente dal modello e assegnargli il **nome** `TodayMessage` nel **Widget** scheda della finestra di **Esplora proprietà**: 

    [![](extensions-images/today09.png "Selezionare l'etichetta che è stato aggiunto automaticamente all'interfaccia utente dal modello e assegnargli il nome TodayMessage nella scheda Widget del Visualizzatore proprietà")](extensions-images/today09.png#lightbox)
3. Salvare le modifiche allo Storyboard.

#### <a name="using-code"></a>Utilizzo di codice

Per compilare l'interfaccia utente nel codice, eseguire le operazioni seguenti: 

1. Nel **Esplora soluzioni**, selezionare la **DaysRemaining** del progetto, aggiungere una nuova classe e chiamarlo `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect progetto DaysRemaining, aggiungere una nuova classe e chiamarlo CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Ripetere l'operazione, il **Esplora soluzioni**, fare doppio clic dell'estensione `Info.plist` file per aprirlo e modificarlo: 

    [![](extensions-images/code02.png "Fare doppio clic sul file di estensioni file Info. plist per aprirlo e modificarlo")](extensions-images/code02.png#lightbox)
3. Selezionare il **vista origine** (nella parte inferiore della schermata) e aprire il `NSExtension` nodo: 

    [![](extensions-images/code03.png "Selezionare la visualizzazione origine nella parte inferiore della schermata e aprire il nodo NSExtension")](extensions-images/code03.png#lightbox)
4. Rimuovere il `NSExtensionMainStoryboard` la chiave e aggiungere un `NSExtensionPrincipalClass` con il valore `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Rimuovere la chiave NSExtensionMainStoryboard e aggiungere un NSExtensionPrincipalClass con il valore CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Salvare le modifiche.

Modificare quindi il `CodeBasedViewController.cs` file e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewController")]
    public class CodeBasedViewController : UIViewController, INCWidgetProviding
    {
        public CodeBasedViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Add label to view
            var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
                TextAlignment = UITextAlignment.Center
            };

            View.AddSubview (TodayMessage);
            
            // Insert code to power extension here...

        }
    }
}
```

Si noti che il `[Register("CodeBasedViewController")]` corrisponde al valore specificato per il `NSExtensionPrincipalClass` sopra.

### <a name="coding-the-extension"></a>L'estensione di codifica

Con l'interfaccia utente creato, aprire il `TodayViewController.cs` o il `CodeBasedViewController.cs` file (in base del metodo usato per creare l'interfaccia utente precedente), modifica il **ViewDidLoad** (metodo) e renderlo simile al seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Calculate the values
    var dayOfYear = DateTime.Now.DayOfYear;
    var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
    var daysRemaining = 365 + leapYearExtra - dayOfYear;

    // Display the message
    if (daysRemaining == 1) {
        TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
    } else {
        TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
    }
}
```

Se tramite il codice basato su metodo di interfaccia utente, sostituire il `// Insert code to power extension here...` commento con il nuovo codice dall'esempio precedente. Dopo la chiamata l'implementazione di base e inserimento di un'etichetta per la versione del codice basato su, questo codice esegue un calcolo semplice per ottenere il giorno dell'anno e il numero di giorni rimanenti. Verrà quindi visualizzato il messaggio nell'etichetta (`TodayMessage`) creata nella progettazione dell'interfaccia utente.

Si noti la similitudine questo processo per il normale processo di scrittura di un'app. Un'estensione `UIViewController` ha lo stesso ciclo di vita un Controller di visualizzazione in un'app, ad eccezione del fatto le estensioni non dispongono di modalità in background e non vengono sospesi quando l'utente è terminato il loro utilizzo. Al contrario, le estensioni sono ripetutamente inizializzate e DEALLOCATE come richiesto.

### <a name="creating-the-container-app-user-interface"></a>Creazione dell'interfaccia utente di app contenitore

Per questa procedura dettagliata, l'app contenitore viene usato semplicemente come un metodo per fornire e installare l'estensione e non fornisce alcuna funzionalità di un proprio. Modificare il TodayContainer `Main.storyboard` file e aggiungere il testo che definisce la funzione dell'estensione e modalità di installazione:

[![](extensions-images/today10.png "Modificare il file Main. Storyboard TodayContainers e aggiungere il testo che definisce la funzione di estensioni e modalità di installazione")](extensions-images/today10.png#lightbox)

Salvare le modifiche allo Storyboard.

### <a name="testing-the-extension"></a>L'estensione per il testing

Per testare l'estensione nel simulatore iOS, eseguire la **TodayContainer** app. Verrà visualizzato visualizzazione principale del contenitore:

[![](extensions-images/run01.png "Verrà visualizzata nella vista principale dei contenitori")](extensions-images/run01.png#lightbox)

Successivamente, premere il **Home** pulsante nel simulatore, scorrere verso il basso dalla parte superiore della schermata per aprire il **centro notifiche**, selezionare il **oggi** scheda, scegliere il **Modifica** pulsante:

[![](extensions-images/run02.png "Fare clic sul pulsante Home nel simulatore, scorrere verso il basso dalla parte superiore della schermata per aprire il centro notifiche, selezionare la scheda Data odierna e fare clic sul pulsante Modifica")](extensions-images/run02.png#lightbox)

Aggiungere il **DaysRemaining** estensione per il **oggi stesso** consente di visualizzare e fare clic sui **eseguita** pulsante:

[![](extensions-images/run03.png "Aggiungere l'estensione DaysRemaining alla visualizzazione oggi e fare clic sul pulsante Fine")](extensions-images/run03.png#lightbox)

Verrà aggiunto il nuovo widget per la **oggi stesso** Vista e i risultati verranno visualizzati:

[![](extensions-images/run04.png "Verrà aggiunto il nuovo widget per la visualizzazione oggi e verranno visualizzati i risultati")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicazione con l'applicazione host

L'esempio oggi estensione creato in precedenza non comunica con l'app di host (il **oggi stesso** schermata). In caso affermativo, viene utilizzato il [ExtensionContext](xref:Foundation.NSExtensionContext) proprietà delle `TodayViewController` o `CodeBasedViewController` classi. 

Per le estensioni che verranno inviati dati dal loro hosting delle App, i dati sono in forma di una matrice di [NSExtensionItem](xref:Foundation.NSExtensionItem) gli oggetti archiviati nel [InputItems](xref:Foundation.NSExtensionContext.InputItems) proprietà del [ExtensionContext ](xref:Foundation.NSExtensionContext) dell'estensione `UIViewController`.

Altre estensioni, ad esempio le estensioni modifica foto, possono distinguere tra l'utente il completamento o l'annullamento dell'utilizzo. Questo verrà segnalato all'host di App tramite il [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) e [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) metodi [ExtensionContext](xref:Foundation.NSExtensionContext) proprietà.

Per altre informazioni, vedere di Apple [Guida alla programmazione di estensione App](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

## <a name="communicating-with-the-parent-app"></a>Comunicazione con l'app padre

Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- [Apple Watch impostazioni](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults condivisa](~/ios/app-fundamentals/user-defaults.md).
- [File condivisi](~/ios/watchos/app-fundamentals/parent-app.md#files).

Per altre informazioni, vedere la [gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sezione del nostro **uso delle funzionalità** documentazione.

## <a name="mobilecoreservices"></a>MobileCoreServices

Quando si lavora con le estensioni, è possibile usare un identificatore di tipo Uniform (UTI) per creare e modificare i dati scambiati tra l'app, altre App e/o servizi.

Il `MobileCoreServices.UTType` classe statica definisce le proprietà di helper seguenti correlate a Apple `kUTType...` definizioni:

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

Vedere l'esempio seguente:

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

Per altre informazioni, vedere la [gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sezione del nostro **uso delle funzionalità** documentazione.

## <a name="precautions-and-considerations"></a>Considerazioni e le precauzioni

Le estensioni sono notevolmente meno memoria a loro disposizione rispetto alle app. È previsto che eseguire rapidamente e con minime intrusione all'utente e le app in che sono ospitati. Tuttavia, un'estensione deve fornire anche una funzione utile distintivo dispendiosa in termini di app con un'interfaccia utente personalizzata che consentono all'utente di identificare per gli sviluppatori dell'estensione o nell'app contenitore che a cui appartengono.

Dato questi requisiti rigidi, è consigliabile distribuire solo le estensioni che sono state testate attentamente e ottimizzate per le prestazioni e utilizzo di memoria. 

## <a name="summary"></a>Riepilogo

Questo documento ha illustrato le estensioni, cosa sono, il tipo di punti di estensione e le limitazioni note in un'estensione imposte da iOS. È stato illustrato la creazione, distribuzione, installazione e l'esecuzione di estensioni e il ciclo di vita di estensione. Fornita una procedura dettagliata di creazione di una semplice **oggi stesso** widget che mostra due modi per creare un'interfaccia utente del widget tramite storyboard o codice. È stato illustrato come testare un'estensione nel simulatore iOS. Infine, illustrato brevemente la comunicazione con l'applicazione Host e alcune precauzioni e considerazioni da prendere durante lo sviluppo di un'estensione. 

## <a name="related-links"></a>Collegamenti correlati

- [ContainerApp (esempio)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Creazione di estensioni in xamarin. IOS (video)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
