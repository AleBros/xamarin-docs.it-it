---
title: le estensioni iOS
description: Introdotta in iOS 8, le estensioni sono widget che vengono presentati da iOS in contesti standard, ad esempio all'interno del centro di notifica, quando l'utente richiede una tastiera personalizzata o quando sono foto di modifica. Tutte le estensioni vengono installate insieme a un'applicazione contenitore e vengono attivate da un determinato punto dell'estensione in un'applicazione Host.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: f6e80b21c76089c0f3f7ac655584b7e18400307e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="ios-extensions"></a>le estensioni iOS

_Introdotta in iOS 8, le estensioni sono widget che vengono presentati da iOS in contesti standard, ad esempio all'interno del centro di notifica, quando l'utente richiede una tastiera personalizzata o quando sono foto di modifica. Tutte le estensioni vengono installate insieme a un'applicazione contenitore e vengono attivate da un determinato punto dell'estensione in un'applicazione Host._

Le estensioni, come è stato introdotto in iOS 8, sono specializzate `UIViewControllers` che vengono presentati dalla iOS all'interno di contesti standard, ad esempio all'interno di **centro notifiche**, come richiesti dall'utente per eseguire i tipi di tasti specializzato input o altri contesti, come la modifica di una foto in cui l'estensione può fornire filtri effetto speciale.

Tutte le estensioni vengono installate insieme a un'applicazione contenitore (con entrambi gli elementi scritti utilizzando le API unificata a 64 bit) e vengono attivate da un determinato punto dell'estensione in un'applicazione Host. E, in quanto verranno utilizzati come supplementi alle funzioni di sistema esistente, devono essere prestazioni elevate, pulita e affidabili. 

In questo articolo vengono illustrati gli argomenti seguenti:

- [Punti di estensione](#Extension-Points) -vengono elencati il tipo di punti di estensione disponibili e il tipo di estensione che può essere creati per ogni punto.
- [Limitazioni](#Limitations) -estensioni dispongono di un numero di limiti, alcuni dei quali sono universale a tutti i tipi, mentre altri tipi di estensione possono presentare limitazioni specifiche sul loro utilizzo.
- [La distribuzione, installazione e le estensioni di esecuzione](#Distributing-Installing-and-Running-Extensions) -estensioni sono distribuite da all'interno di un'applicazione contenitore, che a sua volta viene inviato e distribuiti tramite App Store. Le estensioni distribuite con l'applicazione vengono installate a questo punto, ma l'utente è necessario abilitare ogni estensione in modo esplicito. I diversi tipi di estensioni sono abilitati in modi diversi.
- [Ciclo di vita di estensione](#Extension-Lifecycle) : un'estensione di `UIViewController` verrà creata un'istanza e iniziare il ciclo di vita di Controller di visualizzazione normale. Tuttavia, a differenza di un'applicazione normale, le estensioni vengono caricate, eseguite e quindi terminate ripetutamente.
- [Creazione di un'estensione](#Creating-an-Extension) -quando si sviluppa un'estensione, le soluzioni conterrà almeno due progetti: il contenitore app e uno per ogni estensione per il contenitore include del progetto.
- [Procedura dettagliata](#Walkthrough) : viene illustrato come creare un semplice **oggi** widget estensione che fornisce l'interfaccia utente utilizzando uno Storyboard o nel codice, l'installazione dell'estensione e il relativo test nel simulatore iOS.
- [La comunicazione con l'applicazione Host](#Communicating-with-the-Host-App) -brevemente la comunicazione con l'estensione dell'applicazione Host.
- [La comunicazione con l'App padre](#Communicating-with-the-Parent-App) -brevemente la comunicazione con l'estensione dell'applicazione padre.
- [Considerazioni e le precauzioni](#Precautions-and-Considerations) -elenco alcuni conoscere precauzioni e considerazioni di cui devono essere preso in considerazione durante la progettazione e implementazione di un'estensione.
 

<a name="Extension-Points" />

## <a name="extension-points"></a>Punti di estensione

Esistono diversi tipi di estensione che possono essere creati in iOS 8 (e versioni successive):

<table>
<colgroup>
<col />
<col />
<col />
</colgroup>

<thead>
<tr>
    <th >Tipo</th>
    <th >Descrizione</th>
    <th >Punto di estensione</th>
    <th >Host applicazione</th>
</tr>
</thead>

<tbody>
<tr>
    <td >Operazione</td>
    <td >Editor specializzato o un visualizzatore per un determinato tipo di supporto</td>
    <td ><code>com.apple.ui-services</code></td>
    <td >Qualsiasi</td>
</tr>
<tr>
    <td >Provider di documenti</td>
    <td >Consente di app di usare un archivio remoto</td>
    <td ><code>com.apple.fileprovider-ui</code></td>
    <td >Le app usando un <a href="https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/">UIDocumentPickerViewController</a></td>
</tr>
<tr>
    <td >Tastiera</td>
    <td >Tastiere alternative</td>
    <td ><code>com.apple.keyboard-service</code></td>
    <td >Qualsiasi</td>
</tr>
<tr>
    <td >Modifica di foto</td>
    <td >La modifica di foto e modifica</td>
    <td ><code>com.apple.photo-editing</code></td>
    <td >Photos.app editor</td>
</tr>
<tr>
    <td >Condividi</td>
    <td >Condivide dati con i social network, messaggistica di servizi e così via.</td>
    <td ><code>com.apple.share-services</code></td>
    <td >Qualsiasi</td>
</tr>
<tr>
    <td >Oggi</td>
    <td >"Widget" che vengono visualizzati nella schermata Oggi o centro notifiche</td>
    <td ><code>com.apple.widget-extensions</code></td>
    <td >Centro notifiche e attuali</td>
</tr>
</tbody>
</table>

[Punti di estensione aggiuntivi](~/ios/platform/introduction-to-ios10/index.md#app-extensions) sono state aggiunte in iOS 10.

<a name="Limitations" />

## <a name="limitations"></a>Limitazioni

Estensioni dispongono di un numero di limiti, alcuni dei quali sono universale a tutti i tipi (per istanza, nessun tipo di estensione può accedere il fotocamere o microfoni) mentre altri tipi di estensione possono presentare limitazioni specifiche (ad esempio, personalizzate tastiere all'utilizzo non può essere utilizzato per i campi di immissione dati protetti, ad esempio per le password). 

Le limitazioni universale sono:

- Il [Kit integrità](~/ios/platform/healthkit.md) e [evento Kit UI](~/ios/platform/eventkit.md) Framework non sono disponibili
- Non è possibile utilizzare le estensioni [estesi modalità di background](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- Estensioni non possono accedere il dispositivo fotocamere o microfoni (anche se potrebbero accedere al file di supporto esistenti)
- Le estensioni non possono ricevere dati di eliminare Air (sebbene trasmettono dati tramite Drop Air)
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/) e [UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/) non sono disponibili; le estensioni è necessario utilizzare [UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- Diversi membri di [UIApplication](https://developer.xamarin.com/api/type/UIKit.UIApplication/) non sono disponibili: [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/), `UIApplication.OpenURL`, `UIApplication.BeginIgnoringInteractionEvents` e `UIApplication.EndIgnoringInteractionEvents`
- iOS impone un limite di utilizzo della memoria di 16 MB per le estensioni di oggi.
- Per impostazione predefinita le estensioni della tastiera non hanno accesso alla rete. Ciò influisce sul debug nel dispositivo (la restrizione non viene applicata nel simulatore), poiché xamarin. IOS richiede l'accesso alla rete affinché il debug. È possibile richiedere l'accesso rete impostando il `Requests Open Access` valore in Info. plist del progetto per `Yes`. Vedere Apple [Guida tastiera personalizzate](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) per ulteriori informazioni sulle limitazioni di estensione della tastiera.

Per le singole limitazioni, vedere Apple [Guida per programmatori estensione App](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

<a name="Distributing-Installing-and-Running-Extensions" />

## <a name="distributing-installing-and-running-extensions"></a>La distribuzione, l'installazione e l'esecuzione di estensioni

Le estensioni sono distribuite da all'interno di un'applicazione contenitore, che a sua volta viene inviato e distribuiti tramite App Store. Le estensioni distribuite con l'applicazione vengono installate a questo punto, ma l'utente è necessario abilitare ogni estensione in modo esplicito. I diversi tipi di estensioni sono abilitati in modi diversi; Alcuni richiedono all'utente di passare al **impostazioni** app e consentire loro da qui. Mentre altri sono abilitati nel punto di utilizzo, ad esempio abilitare un'estensione di condivisione durante l'invio di una foto. 

L'app in cui viene utilizzata l'estensione (in cui l'utente rileva il punto di estensione) viene definito il **Host app**perché è l'applicazione che ospita l'estensione quando viene eseguita. L'applicazione che installa l'estensione è il **app contenitore**, poiché si tratta dell'applicazione che contiene l'estensione al momento dell'installazione.  

In genere, l'applicazione contenitore descrive l'estensione e Guida l'utente attraverso il processo di abilitarlo.

<a name="Extension-Lifecycle" />

## <a name="extension-lifecycle"></a>Ciclo di vita di estensione

Un'estensione può essere semplice come una singola [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) o estensioni più complesse che presentano più schermate dell'interfaccia utente. Quando l'utente rileva un _punti di estensione_ (ad esempio quando la condivisione di un'immagine), hanno la possibilità di scegliere tra le estensioni registrate per tale punto di estensione. 

Se si sceglie una delle app di estensioni relativa `UIViewController` verrà creata un'istanza e iniziare il ciclo di vita di Controller di visualizzazione normale. Tuttavia, a differenza di un'applicazione normale, che vengono sospesi ma in genere non vengono terminate quando l'utente termina di interagire con essi, le estensioni vengono caricate, eseguite e quindi terminate ripetutamente.

Le estensioni possono comunicare con le applicazioni Host tramite un [NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) oggetto. Alcune estensioni includono operazioni di ricevano i callback asincroni con i risultati. Questi callback verranno eseguiti su thread in background e l'estensione deve tenere in considerazione questo; ad esempio, tramite [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/) se si desidera aggiornare l'interfaccia utente. Vedere il [la comunicazione con l'applicazione Host](#Communicating-with-the-Host-App) sezione riportata di seguito per ulteriori dettagli.

Per impostazione predefinita, le estensioni e le applicazioni contenitore possono non comunicare, pur essendo installati insieme. In alcuni casi, l'applicazione contenitore è essenzialmente un "shipping" contenitore vuoto con lo scopo viene fornito una volta installata l'estensione. Tuttavia, se circostanze, l'applicazione contenitore e l'estensione può condividere le risorse da un'area comune. Inoltre, un **oggi estensione** potrebbe richiedere il contenitore app per aprire un URL. Questo comportamento è illustrato nella [evoluzione del conto alla rovescia Widget](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

<a name="Creating-an-Extension" />

## <a name="creating-an-extension"></a>Creazione di un'estensione

Estensioni (e le applicazioni contenitore) devono essere i file binari a 64 bit e compilato utilizzando il xamarin [Unified API](http://developer.xamarin.com/guides/cross-platform/macios/unified). Quando si sviluppa un'estensione, le soluzioni conterrà almeno due progetti: il contenitore app e uno per ogni estensione per il contenitore include del progetto. 

<a name="Container-App-Project-Requirements" />

### <a name="container-app-project-requirements"></a>Requisiti dei contenitori di progetto di App

L'applicazione contenitore usato per installare l'estensione presenta i requisiti seguenti:

- È necessario mantenere un riferimento al progetto di estensione.   
- Deve essere un'app completa (deve essere in grado di avviare ed eseguire correttamente) anche se non esegue alcuna operazione maggiore di fornire un modo per installare un'estensione. 
- Deve avere un identificatore Bundle che costituisce la base per l'identificatore Bundle dell'estensione per il progetto (vedere la sezione seguente per ulteriori dettagli).

<a name="Extension-Project-Requirements" />

### <a name="extension-project-requirements"></a>Requisiti di progetto di estensione

Inoltre, il progetto dell'estensione è i seguenti requisiti:

- Deve avere un identificatore Bundle che inizia con l'identificatore Bundle dell'app relativo contenitore. Ad esempio, se il contenitore dell'app con l'identificatore Bundle di `com.myCompany.ContainerApp`, potrebbe essere l'identificatore dell'estensione `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Deve definire la chiave `NSExtensionPointIdentifier`, con un valore appropriato (ad esempio `com.apple.widget-extension` per un **oggi** widget centro notifiche), nel relativo `Info.plist` file.
- Deve anche definire *entrambi* il `NSExtensionMainStoryboard` chiave o `NSPrincipalClass` chiave nel relativo `Info.plist` file con un valore appropriato:
    - Utilizzare il `NSExtensionMainStoryboard` chiave per specificare il nome dello Storyboard che presenta l'interfaccia utente principale per l'estensione (meno `.storyboard`). Ad esempio, `Main` per il `Main.storyboard` file.
    - Utilizzare il `NSPrincipalClass` chiave per specificare la classe che verrà inizializzata quando viene avviata l'estensione. Il valore deve corrispondere il **registrare** valore il `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Tipi specifici di estensioni possono prevedere requisiti aggiuntivi. Ad esempio, un **oggi** o **centro notifiche** classe principale dell'estensione deve implementare [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> **Nota:** se si avvia il progetto usando uno i modelli di estensioni forniti da Visual Studio per Mac, la maggior parte delle (se non tutte) questi requisiti verranno forniti e soddisfatti per l'utente automaticamente dal modello.

<a name="Walkthrough" />

## <a name="walkthrough"></a>Procedura dettagliata 

Nella seguente procedura dettagliata, si creerà un esempio **oggi** widget che calcola il giorno e il numero di giorni rimanenti nell'anno:

[ ![](extensions-images/carpediemscreenshot-sm.png "Un widget di oggi di esempio che calcola il giorno e il numero di giorni rimanenti dell'anno")](extensions-images/carpediemscreenshot.png)

<a name="Creating-the-Solution" />

### <a name="creating-the-solution"></a>Creazione della soluzione

Per creare la soluzione richiesta, eseguire le operazioni seguenti:

1. Innanzitutto, creare un nuovo iOS, **singola vista App** sul progetto e scegliere il **Avanti** pulsante: 

    [ ![](extensions-images/today01.png "Innanzitutto, creare un nuovo iOS, un progetto di singola vista App e fare clic sul pulsante Avanti")](extensions-images/today01.png)
2. Denominare il progetto `TodayContainer` e fare clic su di **Avanti** pulsante: 

    [ ![](extensions-images/today02.png "Denominare il progetto TodayContainer e fare clic sul pulsante Avanti")](extensions-images/today02.png)
3. Verificare il **nome progetto** e **SolutionName** e fare clic su di **crea** pulsante per creare la soluzione: 

    [ ![](extensions-images/today03.png "Verificare il nome del progetto e SolutionName e fare clic sul pulsante Crea per creare la soluzione")](extensions-images/today03.png)
4. Successivamente, nel **Esplora**, pulsante destro del mouse sulla soluzione e aggiungere un nuovo **iOS estensione** dal progetto di **oggi estensione** modello: 

    [ ![](extensions-images/today04.png "Successivamente, in Esplora soluzioni, pulsante destro del mouse sulla soluzione e aggiungere un nuovo progetto di estensione iOS dal modello di estensione oggi")](extensions-images/today04.png)
5. Denominare il progetto `DaysRemaining` e fare clic su di **Avanti** pulsante: 

    [ ![](extensions-images/today05.png "Denominare il progetto DaysRemaining e fare clic sul pulsante Avanti")](extensions-images/today05.png)
6. Esaminare il progetto e scegliere il **crea** pulsante per crearla: 

    [ ![](extensions-images/today06.png "Esaminare il progetto e fare clic sul pulsante Crea per crearla")](extensions-images/today06.png)

La soluzione risultante deve ora dispone di due progetti, come illustrato di seguito:

[ ![](extensions-images/today07.png "La soluzione risultante deve ora dispone di due progetti, come illustrato di seguito")](extensions-images/today07.png)

<a name="Creating-the-Extension-User-Interface" />

### <a name="creating-the-extension-user-interface"></a>Creazione dell'interfaccia utente di estensione

Successivamente, sarà necessario progettare l'interfaccia per il **oggi** widget. Questo può essere eseguito utilizzando uno Storyboard o mediante la creazione dell'interfaccia utente nel codice. Entrambi i metodi verranno descritta di seguito in dettaglio.

<a name="Using-Storyboards" />

#### <a name="using-storyboards"></a>Utilizzo di storyboard

Per compilare l'interfaccia utente con uno Storyboard, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic sul progetto di estensione `Main.storyboard` file per aprirlo e modificarlo: 

    [ ![](extensions-images/today08.png "Doppio clic sul file Main progetti di estensione per aprirlo e modificarlo")](extensions-images/today08.png)
2. Selezionare l'etichetta che è stato aggiunto automaticamente all'interfaccia utente dal modello e assegnargli il **nome** `TodayMessage` nel **Widget** scheda della finestra di **Esplora proprietà**: 

    [ ![](extensions-images/today09.png "Selezionare l'etichetta che è stato aggiunto automaticamente all'interfaccia utente dal modello e assegnargli il nome di TodayMessage nella scheda Widget del Visualizzatore proprietà")](extensions-images/today09.png)
3. Salvare le modifiche allo Storyboard.

<a name="Using-Code" />

#### <a name="using-code"></a>Uso del codice

Per compilare l'interfaccia utente nel codice, eseguire le operazioni seguenti: 

1. Nel **Esplora**, selezionare il **DaysRemaining** del progetto, aggiungere una nuova classe e chiamarlo `CodeBasedViewController`: 

    [ ![](extensions-images/code01.png "Aelect DaysRemaining progetto, aggiungere una nuova classe e chiamarlo CodeBasedViewController")](extensions-images/code01.png)
2. Ripetere l'operazione, il **Esplora**, fare doppio clic su dell'estensione `Info.plist` file per aprirlo e modificarlo: 

    [ ![](extensions-images/code02.png "Fare doppio clic sul file Info. plist estensioni per aprirlo e modificarlo")](extensions-images/code02.png)
3. Selezionare il **visualizzazione origine** (dalla parte inferiore dello schermo) e aprire il `NSExtension` nodo: 

    [ ![](extensions-images/code03.png "Selezionare la vista di origine dalla parte inferiore dello schermo e aprire il nodo NSExtension")](extensions-images/code03.png)
4. Rimuovere il `NSExtensionMainStoryboard` chiave e aggiungere un `NSPrincipalClass` con il valore `CodeBasedViewController`: 

    [ ![](extensions-images/code04.png "Rimuovere la chiave NSExtensionMainStoryboard e aggiungere un NSPrincipalClass con il valore CodeBasedViewController")](extensions-images/code04.png)
5. Salvare le modifiche.

Successivamente, modificare il `CodeBasedViewController.cs` file e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewContoller")]
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

Si noti che il `[Register("CodeBasedViewContoller")]` corrisponde al valore specificato per il `NSPrincipalClass` sopra.

<a name="Coding-the-Extension" />

### <a name="coding-the-extension"></a>L'estensione per la generazione di codice

Con l'interfaccia utente creato, aprire il `TodayViewController.cs` o `CodeBasedViewController.cs` file (in base del metodo usato per creare l'interfaccia utente precedente), modifica il **ViewDidLoad** metodo e renderlo simile al seguente:

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

Se tramite il codice, il metodo di interfaccia utente basato su, sostituire il `// Insert code to power extension here...` commento con il nuovo codice precedente. Dopo chiamare l'implementazione di base e l'inserimento di un'etichetta per la versione del codice basato su, questo codice esegue un calcolo semplice per ottenere il giorno dell'anno e il numero di giorni rimanenti. Viene quindi visualizzato il messaggio nell'etichetta (`TodayMessage`) creata nella progettazione dell'interfaccia utente.

Si noti la similitudine questo processo per il normale processo di scrittura di un'app. Un'estensione `UIViewController` ha il ciclo di vita stesso come Controller di visualizzazione in un'applicazione, ad eccezione del fatto le estensioni non dispongono di una modalità di background e non vengono sospesi quando l'utente viene terminato usarli. Al contrario, le estensioni sono ripetutamente inizializzate e DEALLOCATE come richiesto.

<a name="Creating-the-Container-App-User-Interface" />

### <a name="creating-the-container-app-user-interface"></a>La creazione dell'interfaccia utente di App contenitore

Questa procedura dettagliata, l'applicazione contenitore viene usato semplicemente come un metodo per fornire e installare l'estensione e non fornisce funzionalità di un proprio. Modificare il TodayContainer `Main.storyboard` file e aggiungere il testo che definisce la funzione dell'estensione e modalità di installazione:

[ ![](extensions-images/today10.png "Modificare il file TodayContainers Main e aggiungere il testo che definisce la funzione di estensioni e modalità di installazione")](extensions-images/today10.png)

Salvare le modifiche allo Storyboard.

<a name="Testing-the-Extension" />

### <a name="testing-the-extension"></a>Test dell'estensione

Per testare l'estensione nel simulatore iOS, eseguire il **TodayContainer** app. Verrà visualizzata la visualizzazione principale del contenitore:

[ ![](extensions-images/run01.png "Verrà visualizzata la vista principale di contenitori")](extensions-images/run01.png)

Successivamente, è stato raggiunto il **Home** pulsante nel simulatore, scorrere verso il basso dalla parte superiore della schermata per aprire il **centro notifiche**, selezionare il **oggi** scheda e scegliere il **Modifica** pulsante:

[ ![](extensions-images/run02.png "Fare clic sul pulsante Home nel simulatore, scorrere verso il basso dalla parte superiore della schermata per aprire il centro notifiche, selezionare la scheda di oggi e fare clic sul pulsante Modifica")](extensions-images/run02.png)

Aggiungere il **DaysRemaining** estensione per il **oggi** consente di visualizzare e fare clic su di **eseguita** pulsante:

[ ![](extensions-images/run03.png "Aggiungere l'estensione DaysRemaining alla visualizzazione oggi e fare clic sul pulsante Chiudi")](extensions-images/run03.png)

Verrà aggiunto il nuovo widget per il **oggi** Vista e i risultati verranno visualizzati:

[ ![](extensions-images/run04.png "Verrà aggiunto il nuovo widget per visualizzare la vista Today e verranno visualizzati i risultati")](extensions-images/run04.png)

<a name="Communicating-with-the-Host-App" />

## <a name="communicating-with-the-host-app"></a>La comunicazione con l'applicazione Host

Nell'esempio oggi estensione creato in precedenza non comunica con un'applicazione host (il **oggi** schermata). In caso contrario, viene utilizzato il [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) proprietà del `TodayViewController` o `CodeBasedViewController` classi. 

Per le estensioni che riceveranno i dati da delle app di host, i dati sono nel formato di una matrice di [NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/) gli oggetti archiviati nel [InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/) proprietà del [ExtensionContext ](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) dell'estensione `UIViewController`.

Altra estensione, ad esempio estensioni di modifica di foto, può distinguere tra all'utente di completamento o l'annullamento di utilizzo. Questo verrà segnalato all'applicazione host tramite il [CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/) e [CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/) metodi di [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) proprietà.

Per ulteriori informazioni, vedere Apple [Guida per programmatori estensione App](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

<a name="Communicating-with-the-Parent-App" />

## <a name="communicating-with-the-parent-app"></a>La comunicazione con l'App padre

Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- [Le impostazioni di Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [Condiviso NSUserDefaults](~/ios/app-fundamentals/user-defaults.md).
- [File condivisi](~/ios/watchos/app-fundamentals/parent-app.md#files).

Per ulteriori informazioni, vedere il [gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sezione del nostro **utilizzo con funzionalità** documentazione.

<a name="MobileCoreServices" />

## <a name="mobilecoreservices"></a>MobileCoreServices

Quando si utilizzano le estensioni, utilizzare un identificatore di tipo uniforme (UTI) per creare e modificare i dati scambiati tra l'app, altre applicazioni e/o servizi.

Il `MobileCoreServices.UTType` classe statica definisce le proprietà di supporto seguenti correlati all'Apple `kUTType...` definizioni:

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

Per ulteriori informazioni, vedere il [gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sezione del nostro **utilizzo con funzionalità** documentazione.


<a name="Precautions-and-Considerations" />

## <a name="precautions-and-considerations"></a>Precauzioni e considerazioni

Estensioni dispongono di molto meno memoria a loro disposizione rispetto alle app. Previsti per eseguire rapidamente e con intrusione minima per l'utente e l'app in cui in che sono ospitati. Tuttavia, un'estensione deve fornire anche una funzione utile distintivo per l'app consumer con un'interfaccia utente personalizzata che consentono all'utente di identificare sviluppatore dell'estensione o contenitore a che appartengono.

Dato questi requisiti rigidi, è consigliabile distribuire solo le estensioni che accuratamente testate e ottimizzate per le prestazioni e il consumo di memoria. 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo documento è illustrati le estensioni, che cosa sono, il tipo di punti di estensione e le limitazioni note imposte a un'estensione per iOS. È stato illustrato la creazione, la distribuzione, l'installazione e che esegue il ciclo di vita di estensione e di estensioni. Fornita una procedura dettagliata della creazione di una semplice **oggi** widget che mostra due modi per creare l'interfaccia del widget utilizzando storyboard o codice. Mostrato come un'estensione di test nel simulatore iOS. Infine, è illustrato brevemente la comunicazione con l'applicazione Host e alcune precauzioni e considerazioni di cui devono essere eseguite quando si sviluppa un'estensione. 


## <a name="related-links"></a>Collegamenti correlati

- [ContainerApp (esempio)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Creazione di estensioni in xamarin. IOS (video)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
