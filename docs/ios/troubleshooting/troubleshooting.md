---
title: Suggerimenti per la risoluzione dei problemi per Xamarin.iOS
description: Questo documento fornisce diversi suggerimenti utili per la risoluzione dei problemi durante lo sviluppo di applicazioni Xamarin.iOS. Vengono descritti i messaggi di errore specifici e altri potenziali problemi.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 21b1f0c29962b7aeb45a836c976ec2635a39622e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030872"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Suggerimenti per la risoluzione dei problemi per Xamarin.iOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS non è in grado di risolvere System. ValueTuple

Questo errore si verifica a causa di un'incompatibilità con Visual Studio.

- **Visual Studio 2017 Update 1** (versione 15,1 o precedente) è compatibile solo con **System. ValueTuple NuGet 4.3.0** (o versione precedente).

- **Visual Studio 2017 Update 2** (versione 15,2 o successiva) è compatibile solo con il **sistema. ValueTuple NuGet 4.3.1** o versione successiva.

Scegliere il pacchetto NuGet System. ValueTuple corretto che corrisponde all'installazione di Visual Studio 2017.

## <a name="receiving-error-retrieving-update-information-error-message"></a>Ricezione del messaggio di errore ' errore durante il recupero delle informazioni sull'aggiornamento '

Quando si tenta di aggiornare il software e viene visualizzato questo messaggio di errore, provare a riavviare l'IDE e a disconnettersi e quindi tornare al proprio account nell'IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Ricerca per categorie creare Outlet o azioni con Interface Builder?

Con l'introduzione del Xamarin Designer per iOS in Visual Studio per Mac e Visual Studio, gli sviluppatori di Xamarin.iOS possono ora sfruttare i vantaggi della creazione di un'interfaccia utente tramite gli storyboard e XIB. Per ulteriori informazioni sull'utilizzo della finestra di progettazione, consultare le guide [Hello, iOS](~/ios/get-started/hello-ios/index.md) .

È anche possibile fare riferimento alle guide di [Outlet](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) e [azioni](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) di Apple per altre informazioni sull'uso degli Outlet e delle azioni in IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System. Text. Encoding. GetEncoding genera NotSupportedException

Per impostazione predefinita, è possibile che venga utilizzata una codifica non aggiunta. Per informazioni su come aggiungere il supporto per la codifica, vedere la pagina relativa all' [internazionalizzazione](~/ios/app-fundamentals/localization/index.md) .

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (qualsiasi altra)

Il membro è stato probabilmente rimosso dal linker e pertanto non esiste nell'assembly in fase di esecuzione.  Sono disponibili diverse soluzioni:

- Aggiungere l'attributo [`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) al membro.  Ciò impedirà al linker di rimuoverlo.
- Quando si richiama [**mTouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), usare le opzioni **-nolink** o **-linksdkonly** :
  - **-nolink** Disabilita tutti i collegamenti.
  - **-linksdkonly** collegherà solo gli assembly forniti da Xamarin.iOS, ad esempio **Xamarin.iOS. dll**, mantenendo tutti i tipi negli assembly creati dall'utente (ad esempio, i progetti di app).

Si noti che gli assembly sono collegati in modo che l'eseguibile risultante sia minore. Pertanto, la disabilitazione del collegamento può comportare un eseguibile di dimensioni maggiori rispetto a quello auspicabile.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Si sta ricevendo un ModelNotImplementedException

Se si sta ottenendo questa eccezione, significa che si sta chiamando base. Metodo () in una classe che esegue l'override di un modello. Non è necessario chiamare il metodo di base in una classe per i modelli (si tratta di classi contrassegnate con l'attributo [Model]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Questa classe non è conforme alla codifica del valore chiave per la chiave XXXX

Se si verifica questo errore durante il caricamento di un file del PENNino, significa che il valore XXXX non è stato trovato nella classe gestita. Ciò significa che manca una dichiarazione come la seguente:

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

La definizione precedente viene generata automaticamente da Visual Studio per Mac per tutti i file XIB aggiunti al Visual Studio per Mac nel file `NAME_OF_YOUR_XIB_FILE.designer.xib.cs`.

Inoltre, i tipi che contengono il codice precedente devono essere una sottoclasse di [NSObject](xref:Foundation.NSObject).  Se il tipo che lo contiene è incluso in uno spazio dei nomi, deve disporre anche di un attributo [[Register]](xref:Foundation.RegisterAttribute) che fornisce un nome di tipo senza uno spazio dei nomi (come Interface Builder non supporta gli spazi dei nomi nei tipi):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe XXXX sconosciuta nel file di Interface Builder

Questo errore viene generato se si definisce una classe nei file di Interface Builder ma non si fornisce l'implementazione effettiva per tale classe nel C# codice.

È necessario aggiungere codice simile al seguente:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System. MissingMethodException: non è stato trovato alcun costruttore per foo. bar:: ctor (System. IntPtr)

Questo errore viene generato in fase di esecuzione quando il codice tenta di creare un'istanza delle classi a cui si fa riferimento dal file di Interface Builder. Ciò significa che si è dimenticato di aggiungere un costruttore che accetta un singolo IntPtr come parametro.

Il costruttore con un handle IntPtr viene utilizzato per associare gli oggetti gestiti alle relative rappresentazioni non gestite.

Per risolvere il problema, aggiungere la seguente riga di codice alla classe Foo. bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Il tipo {Foo} non contiene una definizione per `GetNativeField` e non è stato trovato alcun metodo di estensione `GetNativeField` di tipo {Foo}

Se si verifica questo errore nei file generati dalla finestra di progettazione (*. xib.designer.cs), significa che è possibile procedere in uno dei due modi seguenti:

 **1) manca la classe parziale o la classe di base**

Nelle classi parziali generate dalla finestra di progettazione devono essere presenti classi parziali corrispondenti nel codice utente che ereditano da una sottoclasse di `NSObject`, spesso `UIViewController`. Assicurarsi di disporre di una classe di questo tipo per il tipo che fornisce l'errore.

 **2) spazi dei nomi predefiniti modificati**

I file della finestra di progettazione vengono generati usando le impostazioni predefinite dello spazio dei nomi del progetto. Se queste impostazioni sono state modificate o il progetto è stato rinominato, le classi parziali generate potrebbero non trovarsi più nello stesso spazio dei nomi delle rispettive controparti del codice utente.

Le impostazioni dello spazio dei nomi sono disponibili nella finestra di dialogo Opzioni progetto. Lo spazio dei nomi predefinito si trova nella sezione **generale-> impostazioni principali** . Se è vuota, il nome del progetto viene usato come valore predefinito. Impostazioni dello spazio dei nomi più avanzate sono reperibili nella sezione **codice sorgente-> .NET Naming Policies** .

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Avviso per le azioni: il metodo privato ' foo ' non viene mai usato. CS0169

Le azioni per i file di Interface Builder sono connesse ai widget tramite reflection in fase di esecuzione, pertanto è previsto questo avviso.

È possibile utilizzare "#pragma avviso Disable 0169" "#pragma avviso Enable 0169" per le azioni se si desidera eliminare questo avviso solo per questi metodi oppure aggiungere 0169 al campo "Ignora avvisi" nelle opzioni del compilatore se si desidera disabilitarlo per l'intero progetto (non scelta consigliata).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mTouch non riuscito con il messaggio seguente: Impossibile aprire l'assembly '/path/to/YOURPROJECT.exe '

Se viene visualizzato questo messaggio di errore, in genere il problema è il percorso assoluto del progetto che contiene uno spazio. Questo problema verrà risolto in una versione futura di Xamarin.iOS, ma è possibile aggirare il problema spostando il progetto in una cartella senza spazi.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>La versione di sqlite3 è obsoleta. eseguire l'aggiornamento ad almeno v 3.5.0!

Questo problema si verifica quando si eseguono tutte le operazioni seguenti:

1. Usare mono. Data. sqlite
1. Usare Mac OS X Leopard (10,5)
1. Eseguire l'app all'interno del simulatore.

Il problema è che mono sta raccogliendo il `libsqlite3.dylib`OS X, non il file di `libsqlite3.dylib` di iPhoneSimulator. L'app funzionerà sul dispositivo, ma non solo il simulatore.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>La distribuzione nel dispositivo non riesce con System. Exception: AMDeviceInstallApplication ha restituito 3892346901

Questo errore indica che la configurazione della firma del codice per il certificato o l'ID Bundle non corrisponde al profilo di provisioning installato nel dispositivo.  Verificare che sia selezionato il certificato appropriato in opzioni progetto-> firma bundle iPhone e l'ID bundle corretto specificato in opzioni progetto-> applicazione iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Il completamento del codice non funziona in Visual Studio per Mac

Assicurarsi di usare la versione più recente di Visual Studio per Mac e Xamarin.iOS

Se il problema è ancora presente, inviare [un bug](https://monodevelop.com/Developers#Reporting_Bugs), collegando i file di log **~/Library/Logs/XamarinStudio-{Version}/IDE-{timestamp}.log**, **AndroidTools-{timestamp}. log**e **Components-{timestamp}. log** .

Se l'operazione non riesce, è possibile provare a rimuovere la cache di completamento del codice in modo che venga rigenerata:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Prestare attenzione a digitare correttamente il comando oppure rimuovere accidentalmente i file importanti.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio per Mac si arresta in modo anomalo quando si copia il testo

Le famose utilità Mac di QuickSilver, Google Toolbar e LaunchBar includono funzionalità degli appunti che hanno danneggiato la memoria di Visual Studio per Mac. Nelle opzioni è possibile elencare Visual Studio per Mac come processo con cui non interferire.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio per Mac si lamenta per la necessità di mono 2,4

Se è stato aggiornato Visual Studio per Mac a causa di un aggiornamento recente e quando si prova ad avviarlo nuovamente, si verifica un errore di mono 2,4 che non è presente, è sufficiente [aggiornare l'installazione di mono 2,4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3 _6 corregge alcuni importanti problemi che impediscono l'esecuzione di Visual Studio per Mac in modo affidabile, a volte bloccate Visual Studio per Mac all'avvio o impediscono la generazione del database di completamento del codice.

Una volta installato il nuovo mono, Visual Studio per Mac si avvierà come previsto.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Asserzione in.. /.. /.. /.. /mono/Metadata/Generic-sharing.c: 704, condizione ' OTI ' non soddisfatta

Se si riceve la traccia dello stack seguente:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Significa che si sta collegando una libreria statica compilata con codice Thumb nel progetto. A partire dalla versione 3,1 di iPhone SDK (o versione successiva al momento della stesura di questo articolo) Apple ha introdotto un bug nel linker durante il collegamento di codice non Thumb (Xamarin.iOS) con codice Thumb (la libreria statica). Per attenuare questo problema, è necessario eseguire il collegamento con una versione non Thumb della libreria statica.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>System. ExecutionEngineException: tentativo di compilare il metodo JIT (gestito da wrapper) foo []: System. Collections. Generic. ICollection'1. get_Count ()

Il suffisso [] indica che l'utente o la libreria di classi chiama un metodo su una matrice tramite una raccolta generica, ad esempio IEnumerable < >, ICollection < > o IList < >. Come soluzione alternativa, è possibile forzare in modo esplicito il compilatore AOT ad includere tale metodo chiamando il metodo autonomamente e assicurandosi che il codice venga eseguito prima della chiamata che ha generato l'eccezione. In questo caso, è possibile scrivere:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Che forzerà il compilatore AOT a includere il metodo get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Editor origine Visual Studio per Mac è estremamente lento

In alcuni casi l'editor dell'origine Visual Studio per Mac diventa estremamente lento, in modo da bloccarsi per diversi secondi tra la digitazione dei caratteri.

Questo problema è molto raro ed estremamente difficile da riprodurre. non è in genere possibile riprodurlo nello stesso computer dopo il riavvio Visual Studio per Mac. Per questo motivo, è possibile eseguire diverse operazioni di debug prima di riavviare Visual Studio per Mac e inviare i risultati a noi.

1. Provare a chiudere la scheda dell'editor e ad aprirla di nuovo. La modifica o lo spostamento del punto di inserimento viene eseguito fino a quando non si verifica di nuovo il rallentamento?
1. Disabilitare "Beam Sync" utilizzando lo strumento di sviluppo "Quartz debug" (che è possibile trovare tramite Spotlight) e verificare se le prestazioni dell'editor di origine vengono ripristinate al normale.
1. Provare a ripetere il passaggio (1) con la sincronizzazione del Beam ancora disabilitata.
1. Se l'editor si blocca per più di pochi secondi, provare a eseguire "killall-QUIT [Visual Studio per Mac]" in un terminale mentre è bloccato. Potrebbe essere difficile eseguire il comando Kill mentre l'editor è bloccato, ma è essenziale per farlo, perché il comando forza mono a scrivere tracce dello stack di tutti i thread nel log MD, che è possibile usare per individuare lo stato in cui si trovano i thread mentre la XS è bloccata.

Alleghi i log XS, **~/Library/Logs/XamarinStudio-{Version}/IDE-{timestamp}.log**, **ANDROIDTOOLS-{timestamp}. log**e i **componenti-{timestamp}. log** (nelle versioni precedenti di XS/MonoDevelop, basta inviare **~/Library/Logs /MonoDevelop-(3.0 | 2.8 | 2.6)/MonoDevelop.log**).

> [!NOTE]
> Il problema precedente è stato risolto in XS 2,2 Final * *

## <a name="compiled-application-is-very-large"></a>L'applicazione compilata è molto grande

Per supportare il debug, le compilazioni di debug contengono codice aggiuntivo. I progetti compilati in modalità versione sono una frazione delle dimensioni.

A partire da Xamarin.iOS 1,3, le build di debug includono il supporto del debug per ogni singolo componente di mono (ogni metodo in ogni classe dei Framework).  

Con Xamarin.iOS 1,4 verrà introdotto un metodo con granularità fine per il debug, il valore predefinito sarà fornire solo la strumentazione di debug per il codice e le librerie e non eseguire questa operazione per tutti gli [assembly mono](~/cross-platform/internals/available-assemblies.md) (questo sarà ancora possibile, ma si dovrà acconsentire esplicitamente al debug degli assembly.

## <a name="installation-hangs"></a>Blocco installazione

I programmi di installazione mono e Xamarin.iOS si bloccano se è in esecuzione il simulatore iPhone. Questo problema non è limitato a mono o Xamarin.iOS. si tratta di un problema coerente tra tutti i software che tentano di installare software in MacOS Snow Leopard se il simulatore di iPhone è in esecuzione al momento dell'installazione.

Assicurarsi di uscire dal simulatore iPhone e riprovare l'installazione.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Sono stati esauriti i trampoli di tipo 0

Se si riceve questo messaggio durante l'esecuzione del dispositivo, è possibile creare più tipi 0 di elastici (specifici del tipo) modificando la sezione delle opzioni del progetto "Build iPhone".  Si desidera aggiungere argomenti aggiuntivi per le destinazioni di compilazione del dispositivo:

 `-aot "ntrampolines=2048"`

Il numero predefinito di tappeti elastici è 1024. Provare ad aumentare questo numero fino a quando non è sufficiente per l'applicazione.

## <a name="ran-out-of-trampolines-of-type-1"></a>Sono stati esauriti i trampoli di tipo 1

Se si usa in modo intensivo i generics ricorsivi, è possibile che venga generato questo messaggio sul dispositivo.  È possibile creare altri trampolini di tipo 1 (digitare RGCTX) modificando la sezione Opzioni progetto "compilazione iPhone".  Si desidera aggiungere argomenti aggiuntivi per le destinazioni di compilazione del dispositivo:

 `-aot "nrgctx-trampolines=2048"`

Il numero predefinito di tappeti elastici è 1024. Provare ad aumentare questo numero fino a quando non è sufficiente per l'utilizzo di generics.

## <a name="ran-out-of-trampolines-of-type-2"></a>Sono stati esauriti i trampoli di tipo 2

Se si utilizzano interfacce con utilizzo intensivo, è possibile che venga ricevuto questo messaggio sul dispositivo.
È possibile creare altri trampolini di tipo 2 (digitare i thunk di IMT) modificando la sezione delle opzioni del progetto "Build iPhone".  Si desidera aggiungere argomenti aggiuntivi per le destinazioni di compilazione del dispositivo:

 `-aot "nimt-trampolines=512"`

Il numero predefinito di i tappeti elastici del thunk di IMT è 128. Provare ad aumentare questo numero fino a quando non è sufficiente per l'utilizzo delle interfacce.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Il debugger non è in grado di connettersi al dispositivo

Quando si avvia il debug di una configurazione del dispositivo, viene visualizzato il debugger che mostra una finestra di dialogo che indica che sta tentando di connettersi all'applicazione. Esistono diversi motivi per cui il debugger potrebbe non essere in grado di connettersi all'applicazione, a seconda della modalità usata per la connessione (USB o Wi-Fi).

 **Se il dispositivo e l'host del debugger si trovano in reti diverse**, è possibile che un firewall o una rete privata impedisca la connessione dell'applicazione all'host del debugger in modalità Wi-Fi.

 **Visual Studio per Mac potrebbe non essere in grado di eseguire query sull'IP corretto dell'host**. In modalità Wi-Fi Visual Studio per Mac fornisce all'applicazione tutti gli indirizzi IP individuati dall'host, che vengono tentati dall'applicazione per verificare se è possibile utilizzarli per connettersi a Visual Studio per Mac.

 **Un altro dispositivo è connesso a una porta USB nell'host.** In alcuni casi, gli altri dispositivi connessi alle porte USB nell'host sono stati noti per interferire con il debug in modalità USB.

Se la modalità Wi-Fi o USB non funziona, è possibile provare facilmente l'altra: in Visual Studio per Mac, aprire le preferenze, passare alla pagina Preferenze/debugger/debugger iPhone e impostare la casella di controllo "debug di dispositivi iOS su Wi-Fi anziché su USB".   Se non funziona, è possibile visualizzare altre informazioni sull'errore nella console del dispositivo in modalità dettagliata (abilitata aggiungendo "-v-v-v" agli argomenti mTouch aggiuntivi nelle opzioni del progetto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Errore 134: mTouch non riuscito con il messaggio seguente:

Questo errore può essere generato se si tenta di compilare con-nolink nello stile Xamarin.iOS 1,4 delle versioni. Per risolvere questo errore, è possibile specificare argomenti aggiuntivi nella configurazione del progetto MonoDevelop.

Aggiungere l'argomento

 `-nosymbolstrip`

il problema deve essere risolto.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>L'identità di distribuzione non viene visualizzata nelle opzioni di firma del progetto Visual Studio per Mac

Visual Studio per Mac 2,2 presenta un bug che impedisce di rilevare i certificati di distribuzione che contengono una virgola. Eseguire l'aggiornamento a Visual Studio per Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Errore "AFCFileRefWrite restituito: 1" durante il caricamento

Durante il caricamento di un'app nel dispositivo è possibile che venga visualizzato l'errore "AFCFileRefWrite restituito: 1". Questo problema può verificarsi se si dispone di un file di lunghezza zero.

## <a name="error-mtouch-failed-with-no-output"></a>Errore "mTouch non riuscito senza output"

La versione corrente di Xamarin.iOS e Visual Studio per Mac ha esito negativo quando il nome del progetto o la directory in cui è archiviata la soluzione o il progetto contengono spazi.
Per risolvere il problema:

- Assicurarsi che né il progetto o la directory in cui è archiviato contenga uno spazio.
- Nel progetto "impostazioni principali" assicurarsi che il nome del progetto non contenga spazi.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Errore "il file binario caricato non è valido. Per la compilazione dell'applicazione è stata usata una versione beta non definitiva dell'SDK.

Questo errore è in genere causato da un progetto avviato nello sviluppo di iPad prima del rilascio di Xamarin.iOS 2.0.0. è probabile che siano presenti alcune chiavi nel file INFO. plist, ad esempio:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Questa coppia di elementi deve essere rimossa come Visual Studio per Mac la gestisce automaticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Errore "è stata usata una versione beta della versione non definitiva dell'SDK per compilare l'app"

(Fornito da ed Anuff)

Attenersi ai passaggi riportati di seguito.

- Modificare la versione dell'SDK nella build di iPhone in 3,2 o iTunes Connect lo rifiuterà al caricamento perché viene visualizzato un app compatibile con iPad creato con una versione di SDK inferiore a 3,2
- Creare un file INFO. plist personalizzato per il progetto e impostare in modo esplicito MinimumOSVersion su 3,0.   Verrà eseguito l'override del valore MinimumOSVersion 3,2 impostato da Xamarin.iOS.   Se non si esegue questa operazione, l'app non potrà essere eseguita su un iPhone.
- Ricompilare, zip e caricare in iTunes Connect.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Eccezione non gestita: System. Exception: Impossibile trovare il selettore someSelector: in {Type}

Questa eccezione è causata da uno dei tre elementi seguenti:

1. È stato fornito un selettore al runtime di Objective-C senza applicare l'attributo [Export] corrispondente a un metodo
1. È stato abilitato il collegamento completo e non è stato applicato l'attributo [Preserve] al metodo [Export] ed.
1. L'attributo [Export] è stato applicato a un metodo privato in un tipo ereditato.

## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>Il file MainWindow.xib.designer.cs non è stato aggiornato

Si è verificato un errore in Xamarin Studio 2,4 che ha causato il mancato raggruppamento del file MainWindow. xib con il file MainWindow. xib. designer nei nuovi progetti. Questo significa che non aggiornerà il codice della finestra di progettazione per quel file specifico.

Questo problema è stato risolto nella versione di Visual Studio per Mac disponibile nell'aggiornamento predefinito. Assicurarsi quindi di usare la versione più recente.

È possibile correggere i progetti esistenti rimuovendo (senza eliminare) il XIB e il relativo file di progettazione, quindi aggiungendolo di nuovo. I file devono essere riraggruppati correttamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView o UIActionSheet svaniscono dopo la creazione

Se il codice è simile al seguente:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

l'oggetto "actionSheet" si trova come una variabile temporanea nella funzione e non appena la funzione termina, l'oggetto è idoneo per Garbage Collection, quindi viene sottoposta a Garbage Collection.

Per risolvere questo problema, è necessario lasciare un riferimento a "actionSheet" all'esterno del metodo, in un punto che vivrà oltre il metodo.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Il progetto viene sempre eseguito nel simulatore iPad

Il programma di installazione di iPhone SDK 4,0 installa 2 SDK: SDK di 3,2, per la creazione di app solo iPad e 4,0 SDK, usati per la compilazione di app per iPhone e universali. Installa anche un simulatore 3,2, che simula solo un iPad e un simulatore 4,0 che simula iPhone o iPhone 4. Tutti gli SDK e i simulatori meno recenti vengono rimossi.

Visual Studio per Mac opzioni di compilazione del progetto iPhone includono un'impostazione per la versione dell'SDK che verrà usata per la compilazione dell'app. Questa impostazione è disponibile in **Opzioni progetto-> compilazione Build-> iPhone**.

I nuovi progetti in Visual Studio per Mac usano l'SDK installato meno recente come impostazione predefinita dell'SDK e se l'SDK specificato non esiste, Visual Studio per Mac utilizzerà il più vicino possibile per compilare l'app. Questa operazione è stata eseguita in modo che i progetti non richiedano sempre l'SDK più recente. Tuttavia, ciò comporta attualmente l'uso di 3,2 SDK, che comporta l'uso del simulatore iPad.

Per risolvere il problema usando l'SDK 4,0, passare a **Project Options-> Build-> iPhone build**> e modificare il valore di SDK in "4,0" usando la casella a discesa. È necessario eseguire questa operazione per ogni combinazione di configurazione e piattaforma, a cui si accede usando gli elenchi a discesa nella parte superiore del pannello.

La versione dell'SDK non va confusa con l'impostazione "versione minima del sistema operativo".
Questo valore non deve corrispondere al valore della versione dell'SDK. influiscono sulla versione minima del sistema operativo in cui verrà installata l'app, che può essere precedente all'SDK, purché si usino solo le API presenti nel sistema operativo precedente o si protegga l'uso delle funzionalità più recenti con la versione del sistema operativo di runtime chec KS. È consigliabile impostarla sulla versione del sistema operativo meno recente in cui si testa l'app.

Si noti anche che è possibile usare il menu > di **destinazione del simulatore di progetto > iPhone**per selezionare il simulatore usato per impostazione predefinita durante l'esecuzione/debug di un progetto. Inoltre, è possibile usare il menu Esegui **> con**> per selezionare un simulatore specifico con cui eseguire

## <a name="ibtool-returns-error-133"></a>ibtool restituisce l'errore 133

Ciò significa che XCode 4 è installato.   In XCode 4, lo strumento ibtool è stato rimosso, non è più possibile modificare i file XIB con uno strumento autonomo.

Se si vuole usare Interface Builder, installare [Xcode Series 3](https://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponibile nel sito Web di Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>"Impossibile creare l'associazione di visualizzazione per il tipo MIME: application/vnd. Apple-Interface-Builder"

Questo errore si verifica se si tenta di creare un'interfaccia utente iPhone da un progetto non iPhone. Assicurarsi di iniziare con una soluzione iPhone/iPad, ma non è possibile aggiungere solo elementi dell'interfaccia utente iPhone a un progetto non iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Arresto anomalo dell'avvio durante l'esecuzione all'interno del simulatore iOS

Se si ottiene un arresto anomalo del runtime (SIGSEGV) all'interno del simulatore insieme a una traccia dello stack simile alla seguente:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

... probabilmente si avrà un assembly non aggiornato nella directory dell'applicazione del simulatore. Tali assembly possono esistere perché il simulatore iOS di Apple aggiunge e aggiorna i file, ma non li elimina. In questo caso, la soluzione più semplice consiste nel selezionare "Reimposta e contenuto e impostazioni". dal menu simulatore.   

> [!WARNING]
> Tutti i file, le applicazioni e i dati vengono rimossi dal simulatore.   Alla successiva esecuzione dell'applicazione, Visual Studio per Mac la distribuirà nel simulatore e non sarà presente un assembly obsoleto obsoleto per causare l'arresto anomalo.

## <a name="simulator-hangs-during-application-installation"></a>Il simulatore si blocca durante l'installazione dell'applicazione

Questo problema può verificarsi quando i nomi delle applicazioni includono ' .' (punto) nel nome.
Questa operazione non è consentita come nome eseguibile in CFBundleExecutable, anche se può funzionare in molti altri casi, come i dispositivi.

 \* "Il valore non deve includere alcuna estensione per il nome". - [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Errore: "il tipo di attributo personalizzato 0x43 non è supportato" quando si fa doppio clic su file XIB

Ciò è dovuto al tentativo di aprire i file con estensione XIB quando le variabili di ambiente non sono impostate correttamente. Questa situazione non dovrebbe verificarsi con l'utilizzo normale di Visual Studio per Mac/Xamarin.iOS e la riapertura Visual Studio per Mac da/Applications dovrebbe risolvere il problema.

Quando si tenta di aggiornare il software e questo messaggio di errore viene visualizzato, inviare un messaggio di posta elettronica *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>L'applicazione viene eseguita nel simulatore ma non riesce nel dispositivo

Questo problema può manifestarsi in diverse forme e non sempre genera un errore coerente. Se l'applicazione contiene un. xib, verificare che l'azione di **compilazione** su. xib sia impostata su **InterfaceDefinition**. Si tratta dell'azione di compilazione predefinita per. xib.

Per controllare l'azione di compilazione, fare clic con il pulsante destro del mouse sul file con estensione XIB e scegliere **azione di compilazione**.

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: non sono disponibili dati per la codifica 437

Quando si includono librerie di terze parti nell'app Xamarin.iOS, è possibile che venga ricevuto un errore nel formato "System. NotSupportedException: non sono disponibili dati per la codifica 437" durante il tentativo di compilare ed eseguire l'app. Ad esempio, le librerie, ad esempio `Ionic.Zip.ZipFile`, possono generare questa eccezione durante l'operazione.

Questo problema può essere risolto aprendo le opzioni per il progetto Xamarin.iOS, passando a **iOS compila** > **internazionalizzazione** e controllando l'internazionalizzazione **occidentale** .
