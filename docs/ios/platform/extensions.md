---
title: Estensioni iOS in Xamarin.iOS
description: Questo documento descrive le estensioni, ovvero i widget presentati da iOS in un contesto standard, ad esempio all'interno del centro notifiche. Viene illustrato come creare un'estensione e comunicare con esso dall'app padre.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 0cf44a05f8b40a07dcc099d5789171f4a234a0c2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032578"
---
# <a name="ios-extensions-in-xamarinios"></a>estensioni iOS in Xamarin.iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Video sulla creazione di estensioni in iOS**

Le estensioni, come introdotte in iOS 8, sono specializzate `UIViewControllers` presentate da iOS all'interno di contesti standard, ad esempio all'interno del **Centro notifiche**, come tipi di tastiera personalizzati richiesti dall'utente per eseguire un input specializzato o altri contesti come modifica di una foto in cui l'estensione può fornire filtri di effetto speciali.

Tutte le estensioni vengono installate insieme a un'app contenitore (con entrambi gli elementi scritti usando le API unificate a 64 bit) e vengono attivate da un particolare punto di estensione in un'app host. E poiché verranno usati come supplementi per le funzioni di sistema esistenti, devono avere prestazioni elevate, snelle e affidabili. 

## <a name="extension-points"></a>Punti di estensione

|Digitare|Descrizione|Punto di estensione|App host|
|--- |--- |--- |--- |
|Operazione|Editor o Visualizzatore specializzato per un tipo di supporto specifico|`com.apple.ui-services`|Qualsiasi|
|Provider di documenti|Consente all'app di usare un archivio documenti remoto|`com.apple.fileprovider-ui`|App che usano un [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Tastiera|Tastiere alternativi|`com.apple.keyboard-service`|Qualsiasi|
|Modifica foto|Manipolazione e modifica di foto|`com.apple.photo-editing`|Photos. Editor app|
|Condividi|Condivide i dati con i social network, i servizi di messaggistica e così via.|`com.apple.share-services`|Qualsiasi|
|Oggi|"Widget" visualizzati nella schermata di oggi o nel centro notifiche|`com.apple.widget-extensions`|Oggi e centro notifiche|

Sono stati aggiunti [altri punti di estensione](~/ios/platform/introduction-to-ios10/index.md#app-extensions) in iOS 10.

## <a name="limitations"></a>Limitazioni

Le estensioni hanno diverse limitazioni, alcune delle quali sono universali per tutti i tipi (ad esempio, nessun tipo di estensione può accedere alle fotocamere o ai microfoni), mentre altri tipi di estensione possono avere limitazioni specifiche sull'utilizzo (ad esempio, tastiere personalizzate). non può essere usato per i campi di immissione dati protetti, ad esempio per le password. 

Le limitazioni universali sono:

- Il [Kit di integrità](~/ios/platform/healthkit.md) e i Framework [dell'interfaccia utente del kit di eventi](~/ios/platform/eventkit.md) non sono disponibili
- Le estensioni non possono usare le [modalità di sfondo estese](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- Le estensioni non possono accedere alle fotocamere o ai microfoni del dispositivo (anche se possono accedere ai file multimediali esistenti)
- Le estensioni non possono ricevere dati di rilascio aria (anche se possono trasmettere dati tramite l'aria)
- [UIActionSheet](xref:UIKit.UIActionSheet) e [UIAlertView](xref:UIKit.UIAlertView) non sono disponibili. le estensioni devono usare [UIAlertController](xref:UIKit.UIAlertController)
- Diversi membri di [UIApplication](xref:UIKit.UIApplication) non sono disponibili: [UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication. OpenURL](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication. BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) e [ UIApplication. EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS impone un limite di utilizzo della memoria di 16 MB sulle estensioni odierne.
- Per impostazione predefinita, le estensioni della tastiera non hanno accesso alla rete. Ciò influiscono sul debug nel dispositivo (la restrizione non viene applicata nel simulatore), perché Xamarin.iOS richiede l'accesso alla rete per il debug. È possibile richiedere l'accesso alla rete impostando il valore `Requests Open Access` nel file INFO. plist del progetto su `Yes`. Per ulteriori informazioni sulle limitazioni dell'estensione per la tastiera, vedere la [Guida alla tastiera personalizzata](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) di Apple.

Per le singole limitazioni, vedere la [Guida alla programmazione dell'estensione app](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)di Apple.

## <a name="distributing-installing-and-running-extensions"></a>Distribuzione, installazione ed esecuzione di estensioni

Le estensioni vengono distribuite dall'interno di un'app contenitore che, a sua volta, viene inviata e distribuita tramite l'App Store. Le estensioni distribuite con l'app vengono installate a questo punto, ma l'utente deve abilitare in modo esplicito ogni estensione. I diversi tipi di estensioni sono abilitati in modi diversi; diverse richiedono all'utente di passare all'app **Impostazioni** e abilitarle da questa posizione. Mentre altre sono abilitate in fase di utilizzo, ad esempio l'abilitazione di un'estensione di condivisione quando si invia una foto. 

L'app in cui viene usata l'estensione (dove l'utente rileva il punto di estensione) viene definita **app host**, poiché si tratta dell'app che ospita l'estensione quando viene eseguita. L'app che installa l'estensione è l' **app contenitore**, perché è l'app che contiene l'estensione al momento dell'installazione.  

In genere, l'app contenitore descrive l'estensione ed esamina l'utente attraverso il processo di abilitazione.

## <a name="extension-lifecycle"></a>Ciclo di vita dell'estensione

Un'estensione può essere semplice come una singola [UIViewController](xref:UIKit.UIViewController) o più estensioni complesse che presentano più schermate dell'interfaccia utente. Quando l'utente incontra un punto di _estensione_ , ad esempio quando si condivide un'immagine, avrà la possibilità di scegliere tra le estensioni registrate per il punto di estensione. 

Se scelgono una delle estensioni dell'app, verrà creata un'istanza del relativo `UIViewController` e inizierà il ciclo di vita del controller di visualizzazione normale. Tuttavia, a differenza di una normale app, che viene sospesa, ma che in genere non termina quando l'utente termina l'interazione con loro, le estensioni vengono caricate, eseguite e quindi interrotte ripetutamente.

Le estensioni possono comunicare con le app host tramite un oggetto [NSExtensionContext](xref:Foundation.NSExtensionContext) . Per alcune estensioni sono disponibili operazioni che ricevono callback asincroni con i risultati. Questi callback verranno eseguiti sui thread in background e l'estensione deve prendere in considerazione questo aspetto. ad esempio, usando [NSObject. InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) se si desidera aggiornare l'interfaccia utente. Per altri dettagli, vedere la sezione [comunicazione con l'app host](#communicating-with-the-host-app) riportata di seguito.

Per impostazione predefinita, le estensioni e le relative app contenitore non possono comunicare, nonostante siano installate insieme. In alcuni casi, l'app contenitore è essenzialmente un contenitore "Shipping" vuoto il cui scopo viene servito dopo l'installazione dell'estensione. Tuttavia, se le circostanze stabiliscono, l'app contenitore e l'estensione possono condividere le risorse da un'area comune. Inoltre, un' **estensione di oggi** potrebbe richiedere all'app contenitore di aprire un URL. Questo comportamento è illustrato nel [Widget Countdown evento](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions).

## <a name="creating-an-extension"></a>Creazione di un'estensione

Le estensioni (e le relative app contenitore) devono essere binari a 64 bit e compilate usando le [API unificate](~/cross-platform/macios/unified/index.md)Xamarin.iOS. Quando si sviluppa un'estensione, le soluzioni conterranno almeno due progetti: l'app contenitore e un progetto per ogni estensione fornita dal contenitore.

### <a name="container-app-project-requirements"></a>Requisiti del progetto di app contenitore

L'app contenitore usata per installare l'estensione presenta i requisiti seguenti:

- Deve mantenere un riferimento al progetto di estensione.   
- Deve essere un'app completa (deve essere in grado di avviarsi ed essere eseguita correttamente) anche se non esegue alcuna operazione oltre a fornire un modo per installare un'estensione. 
- Deve avere un identificatore del bundle che costituisce la base per l'identificatore del bundle del progetto di estensione. per ulteriori informazioni, vedere la sezione seguente.

### <a name="extension-project-requirements"></a>Requisiti del progetto di estensione

Il progetto dell'estensione, inoltre, presenta i requisiti seguenti:

- Deve avere un identificatore del bundle che inizia con l'identificatore del bundle dell'app contenitore. Se, ad esempio, l'identificatore del bundle dell'app contenitore è `com.myCompany.ContainerApp`, l'identificatore dell'estensione potrebbe essere `com.myCompany.ContainerApp.MyExtension`: 

  ![](extensions-images/bundleidentifiers.png) 
- Deve definire il `NSExtensionPointIdentifier` chiave, con un valore appropriato, ad esempio `com.apple.widget-extension` per un widget del centro notifiche di **oggi** , nel file `Info.plist`.
- Deve anche definire la chiave di `NSExtensionMainStoryboard` *o la chiave* di `NSExtensionPrincipalClass` nel file `Info.plist` con un valore appropriato:
  - Usare la chiave `NSExtensionMainStoryboard` per specificare il nome dello storyboard che presenta l'interfaccia utente principale per l'estensione (meno `.storyboard`). Ad esempio, `Main` per il file di `Main.storyboard`.
  - Utilizzare la chiave `NSExtensionPrincipalClass` per specificare la classe che verrà inizializzata quando viene avviata l'estensione. Il valore deve corrispondere al valore di **Registro** della `UIViewController`: 

  ![](extensions-images/registerandprincipalclass.png)

Tipi specifici di estensioni possono avere requisiti aggiuntivi. Ad esempio, una classe principale dell'estensione **Today** o **Notification Center** deve implementare [INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding).

> [!IMPORTANT]
> Se si avvia il progetto usando uno dei modelli di estensioni forniti da Visual Studio per Mac, la maggior parte (se non tutti) questi requisiti verranno forniti e soddisfatti automaticamente dal modello.

## <a name="walkthrough"></a>Procedura dettagliata 

Nella procedura dettagliata seguente si creerà un widget **Today** di esempio che calcola il giorno e il numero di giorni rimanenti nell'anno:

[![](extensions-images/carpediemscreenshot-sm.png "An example Today widget that calculates the day and number of days remaining in the year")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Creazione della soluzione

Per creare la soluzione necessaria, procedere come segue:

1. Prima di tutto, creare un nuovo progetto di app iOS, **visualizzazione singola** e fare clic sul pulsante **Avanti** : 

    [![](extensions-images/today01.png "First, create a new iOS, Single View App project and click the Next button")](extensions-images/today01.png#lightbox)
2. Chiamare il progetto `TodayContainer` e fare clic sul pulsante **Avanti** : 

    [![](extensions-images/today02.png "Call the project TodayContainer and click the Next button")](extensions-images/today02.png#lightbox)
3. Verificare il **nome del progetto** e **SolutionName** e fare clic sul pulsante **Crea** per creare la soluzione: 

    [![](extensions-images/today03.png "Verify the Project Name and SolutionName and click the Create button to create the solution")](extensions-images/today03.png#lightbox)
4. Quindi, nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla soluzione e aggiungere un nuovo progetto di **estensione iOS** dal modello di **estensione Today** : 

    [![](extensions-images/today04.png "Next, in the Solution Explorer, right-click on the Solution and add a new iOS Extension project from the Today Extension template")](extensions-images/today04.png#lightbox)
5. Chiamare il progetto `DaysRemaining` e fare clic sul pulsante **Avanti** : 

    [![](extensions-images/today05.png "Call the project DaysRemaining and click the Next button")](extensions-images/today05.png#lightbox)
6. Esaminare il progetto e fare clic sul pulsante **Crea** per crearlo: 

    [![](extensions-images/today06.png "Review the project and click the Create button to create it")](extensions-images/today06.png#lightbox)

La soluzione risultante dovrebbe ora avere due progetti, come illustrato di seguito:

[![](extensions-images/today07.png "The resulting Solution should now have two projects, as shown here")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Creazione dell'interfaccia utente dell'estensione

Successivamente, sarà necessario progettare l'interfaccia per il widget **attuale** . Questa operazione può essere eseguita usando uno storyboard o creando l'interfaccia utente nel codice. Entrambi i metodi verranno analizzati in dettaglio.

#### <a name="using-storyboards"></a>Uso degli storyboard

Per compilare l'interfaccia utente con uno storyboard, procedere come segue:

1. Nella **Esplora soluzioni**fare doppio clic sul file di `Main.storyboard` del progetto di estensione per aprirlo per la modifica: 

    [![](extensions-images/today08.png "Double-click the Extension projects Main.storyboard file to open it for editing")](extensions-images/today08.png#lightbox)
2. Selezionare l'etichetta aggiunta automaticamente all'interfaccia utente per modello e assegnarle il **nome** `TodayMessage` nella scheda **widget** di **Esplora proprietà**: 

    [![](extensions-images/today09.png "Select the Label that was automatically added to the UI by template and give it the Name TodayMessage in the Widget tab of the Properties Explorer")](extensions-images/today09.png#lightbox)
3. Salvare le modifiche apportate allo storyboard.

#### <a name="using-code"></a>Uso del codice

Per compilare l'interfaccia utente nel codice, eseguire le operazioni seguenti: 

1. Nella **Esplora soluzioni**selezionare il progetto **DaysRemaining** , aggiungere una nuova classe e chiamarla `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect the DaysRemaining project, add a new class and call it CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Ancora una volta, nella **Esplora soluzioni**fare doppio clic sul file di `Info.plist` dell'estensione per aprirlo per la modifica: 

    [![](extensions-images/code02.png "Double-click Extensions Info.plist file to open it for editing")](extensions-images/code02.png#lightbox)
3. Selezionare la **visualizzazione origine** (nella parte inferiore della schermata) e aprire il nodo `NSExtension`: 

    [![](extensions-images/code03.png "Select the Source View from the bottom of the screen and open the NSExtension node")](extensions-images/code03.png#lightbox)
4. Rimuovere la chiave di `NSExtensionMainStoryboard` e aggiungere una `NSExtensionPrincipalClass` con il valore `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Remove the NSExtensionMainStoryboard key and add a NSExtensionPrincipalClass with the value CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Salvare le modifiche.

Modificare quindi il file di `CodeBasedViewController.cs` e renderlo simile al seguente:

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

Si noti che il `[Register("CodeBasedViewController")]` corrisponde al valore specificato per il `NSExtensionPrincipalClass` precedente.

### <a name="coding-the-extension"></a>Codifica dell'estensione

Con l'interfaccia utente creata, aprire il `TodayViewController.cs` o il file di `CodeBasedViewController.cs` (in base al metodo usato per creare l'interfaccia utente precedente), modificare il metodo **ViewDidLoad** e renderlo simile al seguente:

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

Se si usa il metodo dell'interfaccia utente basata sul codice, sostituire il commento `// Insert code to power extension here...` con il nuovo codice riportato sopra. Dopo la chiamata all'implementazione di base e l'inserimento di un'etichetta per la versione basata sul codice, questo codice esegue un calcolo semplice per ottenere il giorno dell'anno e il numero di giorni rimanenti. Quindi Visualizza il messaggio nell'etichetta (`TodayMessage`) creato nella progettazione dell'interfaccia utente.

Si noti che questo processo è simile al processo normale di scrittura di un'app. Il `UIViewController` di un'estensione ha lo stesso ciclo di vita di un controller di visualizzazione in un'app, ad eccezione delle estensioni che non dispongono di modalità in background e non vengono sospese al termine dell'utilizzo da parte dell'utente. Le estensioni vengono invece inizializzate e deallocate ripetutamente in modo obbligatorio.

### <a name="creating-the-container-app-user-interface"></a>Creazione dell'interfaccia utente dell'app contenitore

Per questa procedura dettagliata, l'app contenitore viene semplicemente usata come metodo per la spedizione e l'installazione dell'estensione e non fornisce alcuna funzionalità autonoma. Modificare il file di `Main.storyboard` di TodayContainer e aggiungere il testo che definisce la funzione dell'estensione e come installarlo:

[![](extensions-images/today10.png "Edit the TodayContainers Main.storyboard file and add some text defining the Extensions function and how to install it")](extensions-images/today10.png#lightbox)

Salvare le modifiche apportate allo storyboard.

### <a name="testing-the-extension"></a>Test dell'estensione

Per testare l'estensione nel simulatore iOS, eseguire l'app **TodayContainer** . Verrà visualizzata la visualizzazione principale del contenitore:

[![](extensions-images/run01.png "The containers main view will be displayed")](extensions-images/run01.png#lightbox)

Quindi, fare clic sul pulsante **Home** nel simulatore, scorrere verso il basso dalla parte superiore della schermata per aprire **il centro notifiche**, selezionare la scheda **Today** e fare clic sul pulsante **Edit (modifica** ):

[![](extensions-images/run02.png "Hit the Home button in the Simulator, swipe down from the top of the screen to open the Notification Center, select the Today tab and click the Edit button")](extensions-images/run02.png#lightbox)

Aggiungere l'estensione **DaysRemaining** alla visualizzazione **odierna** e fare clic sul pulsante **fine** :

[![](extensions-images/run03.png "Add the DaysRemaining Extension to the Today view and click the Done button")](extensions-images/run03.png#lightbox)

Il nuovo widget verrà aggiunto alla visualizzazione **Today** e i risultati verranno visualizzati:

[![](extensions-images/run04.png "The new widget will be added to the Today view and the results will be displayed")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicazione con l'app host

L'estensione Today di esempio creata in precedenza non comunica con la relativa app host (la schermata **odierna** ). In caso affermativo, utilizzerebbe la proprietà [ExtensionContext](xref:Foundation.NSExtensionContext) delle classi `TodayViewController` o `CodeBasedViewController`. 

Per le estensioni che riceveranno i dati dalle app host, i dati sono sotto forma di matrice di oggetti [NSExtensionItem](xref:Foundation.NSExtensionItem) archiviati nella proprietà [InputItems](xref:Foundation.NSExtensionContext.InputItems) di [ExtensionContext](xref:Foundation.NSExtensionContext) dell'`UIViewController` dell'estensione.

Un'altra estensione, ad esempio le estensioni per la modifica di foto, può distinguere tra il completamento o l'annullamento dell'utilizzo da parte dell'utente. Questa operazione verrà segnalata all'app host tramite i metodi [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) e [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) della proprietà [ExtensionContext](xref:Foundation.NSExtensionContext) .

Per ulteriori informazioni, vedere la guida alla [programmazione dell'estensione app](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)di Apple.

## <a name="communicating-with-the-parent-app"></a>Comunicazione con l'app padre

Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- [Impostazioni Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults condiviso](~/ios/app-fundamentals/user-defaults.md).
- [File condivisi](~/ios/watchos/app-fundamentals/parent-app.md#files).

Per ulteriori informazioni, vedere la sezione [gruppi di app](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) della documentazione **utilizzo delle funzionalità** .

## <a name="mobilecoreservices"></a>MobileCoreServices

Quando si lavora con le estensioni, usare un identificatore di tipo uniforme (UTI) per creare e modificare i dati scambiati tra l'app, altre app e/o servizi.

La classe statica `MobileCoreServices.UTType` definisce le seguenti proprietà Helper correlate alle definizioni di `kUTType...` di Apple:

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

Per ulteriori informazioni, vedere la sezione [gruppi di app](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) della documentazione **utilizzo delle funzionalità** .

## <a name="precautions-and-considerations"></a>Precauzioni e considerazioni

Le estensioni hanno una quantità significativamente inferiore di memoria disponibile rispetto alle app. Si prevede che vengano eseguite rapidamente e con un'intrusione minima all'utente e all'app in cui sono ospitati. Tuttavia, un'estensione deve fornire anche una funzione distinta e utile all'app consumer con un'interfaccia utente personalizzata che consente all'utente di identificare lo sviluppatore o l'app contenitore dell'estensione a cui appartengono.

Considerati questi rigidi requisiti, è consigliabile distribuire solo le estensioni testate e ottimizzate per l'utilizzo di memoria e prestazioni. 

## <a name="summary"></a>Riepilogo

In questo documento sono state analizzate le estensioni, il tipo di punti di estensione e le limitazioni note imposte in un'estensione da iOS. È stato illustrato come creare, distribuire, installare ed eseguire estensioni e il ciclo di vita dell'estensione. Fornisce una procedura dettagliata per la creazione di un semplice widget di **oggi** che mostra due modi per creare l'interfaccia utente del widget usando gli storyboard o il codice. È stato illustrato come testare un'estensione nel simulatore iOS. Infine, è stata discussa brevemente la comunicazione con l'app host e alcune precauzioni e considerazioni da intraprendere quando si sviluppa un'estensione. 

## <a name="related-links"></a>Collegamenti correlati

- [ContainerApp (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/intro-to-extensions)
- [Creazione di estensioni in Xamarin.iOS (video)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
