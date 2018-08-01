---
title: Risoluzione dei problemi di suggerimenti per xamarin. IOS
description: Questo documento fornisce vari suggerimenti utili per la risoluzione dei problemi relativi durante lo sviluppo di applicazioni di xamarin. IOS. Vengono descritti i messaggi di errore specifici, nonché altri problemi potenziali.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/0201
ms.openlocfilehash: 26fe2fb848fb81940bc01a34c69b1b28897005dc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789353"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Risoluzione dei problemi di suggerimenti per xamarin. IOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin non è possibile risolvere System.ValueTuple

Questo errore si verifica a causa di un problema di incompatibilità con Visual Studio.

- **2017 di Visual Studio Update 1** (versione 15.1 o precedente) è compatibile solo con **System.ValueTuple NuGet 4.3.0** (o versioni precedenti).

- **Aggiornamento 2 di Visual Studio 2017** (versione più recente o 15.2) è compatibile solo con il **System.ValueTuple NuGet 4.3.1** o versione successiva.

Scegliere il corretto System.ValueTuple NuGet corrispondente con l'installazione di Visual Studio 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Ricezione di messaggi di errore 'Errore di recupero delle informazioni di aggiornamento'

Quando il tentativo di aggiornare il software e questo messaggio di errore viene visualizzato, provare a riavviare l'IDE e la disconnessione e quindi di nuovo al proprio account nell'IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Come creare azioni o prese con Generatore interfaccia?

Con l'introduzione della finestra di progettazione di Xamarin per iOS in Visual Studio per Mac e Visual studio, gli sviluppatori di xamarin. IOS possono ora usufruire della creazione di un'interfaccia utente tramite storyboard e .xibs. Consultare la [Hello, iOS](~/ios/get-started/hello-ios/index.md) Guide per ulteriori informazioni sull'utilizzo di progettazione.

È anche possibile fare riferimento a Apple [presa](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) e [azioni](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) Guide per ulteriori informazioni sull'utilizzo di punti vendita e le azioni in IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding genera un'eccezione NotSupportedException

Che si stia utilizzando una codifica non viene aggiunto per impostazione predefinita. Controllare il [internazionali](~/ios/app-fundamentals/localization/index.md) pagina per informazioni su come aggiungere il supporto per la codifica più.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (altro)

Il membro probabilmente è stato rimosso dal linker e pertanto non esiste nell'assembly in fase di esecuzione.  Esistono diverse soluzioni a questo:

-  Aggiungere il [[conservare]](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) al membro dell'attributo.  Ciò impedirà al linker di rimuoverlo.
-  Quando si richiama [mtouch](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29) , utilizzare il **- nolink** o **- linksdkonly** opzioni. -    **-nolink** Disabilita tutti i collegamenti.
-    **-linksdkonly** solo collegherà assembly fornito xamarin. IOS, ad esempio *monotouch.dll* o xamarin.ios.dll.

Si noti che gli assembly vengono collegati in modo che il file eseguibile risultante piccolo; di conseguenza, la disabilitazione di collegamento può comportare un file eseguibile maggiore rispetto a quanto sia utile.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Si riceve un ModelNotImplementedException

Se si riceve questa eccezione, ciò significa che si sta chiamando base. Metodo () in una classe che esegue l'override di un modello. Non è necessario chiamare il metodo di base in una classe per i modelli (queste sono le classi contrassegnate con l'attributo [modello]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Questa classe non è il valore della chiave di codifica compatibile per la chiave XXXX

Se questo errore si verifica quando si carica un file NIB significa che il valore che xxxx non è stato trovato nella classe gestita. Ciò significa che manchi una dichiarazione simile al seguente:

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

La definizione precedente viene generata automaticamente da Visual Studio per Mac per tutti i file che si aggiungono a Visual Studio per Mac in XI il `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` file.

Inoltre, i tipi che contiene il codice sopra riportato devono essere una sottoclasse di [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).  Se il tipo contenitore è all'interno di uno spazio dei nomi, è necessario che abbia un [[registrare]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) attributo che fornisce un nome di tipo senza uno spazio dei nomi (come generatore di interfaccia non supporta gli spazi dei nomi in tipi):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe sconosciuta nel file di interfaccia generatore XXXX

Questo errore viene generato se si definisce una classe nei file del generatore di interfaccia, ma non si specifica l'implementazione effettiva per tale nel codice c#.

È necessario aggiungere il codice simile al seguente:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System. MissingMethodException: Trovato alcun costruttore per Foo.Bar::ctor(System.IntPtr)

Questo errore viene generato in fase di esecuzione quando il codice tenta di creare un'istanza delle classi che si è fatto riferimento dal file di interfaccia generatore. Ciò significa che si è dimenticato di aggiungere un costruttore che accetta un singolo elemento IntPtr come parametro.

Il costruttore con un handle IntPtr viene utilizzato per associare gli oggetti gestiti con rappresentazioni non gestite.

Per risolvere questo problema, aggiungere la riga di codice seguente alla classe Foo. bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Tipo {Foo} non contiene una definizione per `GetNativeField' and no extension method `GetNativeField' di tipo è stato possibile trovare {Foo}

Se questo errore si verifica nei file della finestra di progettazione generati (*. xib.designer.cs), significa che una delle seguenti operazioni:

 **1) non di classe parziale o classe di base**

Le classi parziali generato da progettazione devono avere classi parziali corrispondente nel codice utente che ereditano da alcuni sottoclasse `NSObject`, spesso `UIViewController`. Verificare di disporre di una classe per il tipo da cui proviene l'errore.

 **2) spazi dei nomi predefinito di modificato**

Vengono generati i file della finestra di progettazione utilizzando le impostazioni di spazio dei nomi predefinito del progetto. Se si hanno modificato le impostazioni o rinominare il progetto, le classi parziali generate potrebbero non essere più dello stesso spazio dei nomi delle rispettive controparti codice utente.

Impostazioni Namespace sono reperibile nella finestra di dialogo Project Options. Spazio dei nomi predefinito è presente nel **generale -> Impostazioni principali** sezione. Se è vuota, il nome del progetto viene utilizzato come impostazione predefinita. Impostazioni dello spazio dei nomi più avanzate, vedere il **codice sorgente -> Criteri di denominazione .NET** sezione.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Per le azioni di avviso: il metodo privato "Foo" non viene mai utilizzato. (CS0169)

Azioni per i file di interfaccia generatore sono connessi i widget mediante reflection in fase di esecuzione, pertanto questo avviso è previsto.

È possibile utilizzare "avviso #pragma Disabilita 0169" "Abilita avviso #pragma 0169" per le azioni se si desidera escludere questo avviso solo per questi metodi oppure aggiungere 0169 al campo "Ignora gli avvisi" nelle opzioni del compilatore se si desidera disabilitare la funzionalità per l'intero progetto (non opzione consigliata).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch non riuscita con il seguente messaggio: Impossibile aprire l'assembly ' / path/to/yourproject.exe'

Se viene visualizzato questo messaggio di errore, in genere il problema è che il percorso assoluto per il progetto contiene uno spazio. Questo problema verrà risolto in una futura versione di xamarin, ma è possibile risolvere il problema, spostare il progetto in una cartella senza spazi.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>La versione sqlite3 è precedente, aggiornare almeno alla v3.5.0!

Ciò si verifica quando si esegue l'operazione di tutti gli elementi seguenti:

1.  Utilizzare Mono.Data.Sqlite
1.  Usare Mac OS X Leopard (10.5)
1.  Eseguire l'app nel simulatore.


Il problema è che Mono preleva OS X `libsqlite3.dylib`, del non il iPhoneSimulator `libsqlite3.dylib` file. L'app *verrà* aziendale sul dispositivo, ma non il simulatore.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Distribuire a dispositivi non riesce con System. Exception: AMDeviceInstallApplication restituito 3892346901

Questo errore indica che la configurazione di firma codice per l'id certificato/bundle non corrisponde al profilo di provisioning installato nel dispositivo.  Verificare di avere il certificato appropriato selezionato nelle opzioni progetto -> iPhone firma Bundle e l'id corretto bundle specificato nelle opzioni progetto -> applicazione iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Il completamento del codice non funziona in Visual Studio per Mac

Verificare che si utilizza la versione più recente di Visual Studio per Mac e xamarin

Se il problema è ancora presente, [segnalare un bug](http://monodevelop.com/Developers#Reporting_Bugs), allegare il **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componenti-{TIMESTAMP}. log** i file di log.

Se non si riesce, è possibile provare a rimuovere la cache di completamento di codice in modo che viene rigenerato:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Assicurarsi che si digita il comando correttamente o che è possibile rimuovere inavvertitamente file importanti.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Arresto anomalo di Visual Studio per Mac quando si copia testo

Le comuni utilità Mac QuickSilver, Google sulla barra degli strumenti e finestra di avvio dispongono di funzionalità di Appunti che danneggiata di Visual Studio per la memoria del Mac. Nelle opzioni, è possibile elencare Visual Studio per Mac come processo a che cui non dovrebbe interferire con.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visualizza un errore di Visual Studio per Mac su 2.4 Mono richiesto

Se è stato aggiornato Visual Studio per Mac a causa di un aggiornamento recente e quando si tenta di avviarlo nuovamente errore è relativo al Mono 2.4 non è presente, è necessario effettuare solo [aggiornare l'installazione Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

2.4.2.3_6 mono corregge alcuni problemi importanti che hanno impedito di Visual Studio per Mac da eseguire in modo affidabile, talvolta bloccato Visual Studio per Mac all'avvio o ha impedito il database di completamento di codice viene generato.

Dopo aver installato il nuovo Mono, Visual Studio per Mac verrà avviato come previsto.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Asserzione in... /.. /.. /.. /mono/Metadata/Generic-Sharing.c:704, condizione 'oti' non soddisfatto

Se si riceve la traccia dello stack seguente:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Ciò significa che si sta collegando una libreria statica, compilata con codice thumb nel progetto. A partire dalla versione SDK iPhone 3.1 (o versione successiva al momento della redazione del presente documento) Apple ha introdotto un bug nel loro linker durante il collegamento non Thumb codice (xamarin) con codice Thumb (la libreria statica). È necessario eseguire il collegamento con una versione non è una casella di scorrimento della libreria statica per attenuare questo problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System. ExecutionEngineException: Tentativo di Foo[]:System.Collections.Generic.ICollection'1.get_Count di metodo (wrapper gestito a codice gestito) (compilazione JIT)

Il suffisso [] indica che è la libreria di classi o chiama un metodo in una matrice utilizzando una raccolta generica, ad esempio <> IEnumerable, ICollection <> o <> IList. In alternativa, è possibile in modo esplicito forza il compilatore AOT per includere tale metodo chiamando il metodo manualmente e assicurandosi che questo codice viene eseguito prima della chiamata che ha generato l'eccezione. In questo caso, è possibile scrivere:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Forzando il compilatore AOT per includere il metodo.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio per l'editor di origine Mac è molto lento

In alcuni casi di Visual Studio per l'editor di origine Mac diventa molto lento, in fase di blocco per alcuni secondi tra la digitazione di caratteri.

Questo problema è molto rari ed estremamente difficili da riprodurre: in genere non può essere riprodotto nello stesso computer dopo il riavvio di Visual Studio per Mac. Per questo motivo si sarebbe apprezza se è possibile eseguire diverse operazioni di debug prima di riavviare Visual Studio per Mac e inviare i risultati.

1.  La scheda dell'editor, chiudere e riaprire. È necessario un po' di modifica o spostare il punto di inserimento fino a quando il rallentamento si verifica ancora?
1.  Disabilitare "Trasmetti sincronizzazione" utilizzando lo strumento per sviluppatori "Quarzo Debug" (che è possibile trovare tramite Spotlight) e verificare se le prestazioni dell'editor di origine sono ripristinate al normale.
1.  Provare a ripetere il passaggio (1) con sincronizzazione Trasmetti rimane disabilitato.
1.  Se l'editor si blocca per più di pochi secondi, provare a eseguire "killall-chiudere [Visual Studio per Mac]" in un terminal mentre si è bloccato. Potrebbe essere difficile ora il comando kill si verifica durante l'editor è bloccato, ma è essenziale eseguire questa operazione, perché il comando forza Mono per scrivere tracce dello stack di tutti i thread per il log MD, che è possibile utilizzare per individuare lo stato che i thread sono in mentre XS è bloccato.



Allegare i registri XS, **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componenti-{TIMESTAMP}. log**(nelle versioni precedenti di XS/MonoDevelop, è sufficiente inviare **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **Nota: Il problema sopra indicato è stato corretto in XS 2.2 finale**

## <a name="compiled-application-is-very-large"></a>Applicazione compilata è molto grande

Per supportare il debug, le compilazioni di debug contengono codice aggiuntivo. Progetti compilati in modalità di rilascio sono una frazione delle dimensioni.

A partire da xamarin 1.3 debug compilazioni incluse supporto del debug per ogni singolo componente di Mono (ogni metodo in ogni classe dei Framework).  

Con xamarin. IOS 1.4 si introdurrà un metodo con granularità fine maggiore per il debug, il valore predefinito, è necessario fornire solo strumentazione di debug per il codice e le librerie e non eseguire questa operazione per tutti i [assembly Mono](~/cross-platform/internals/available-assemblies.md) (questo continua è possibile, ma è necessario registrarsi per il debug di tali assembly).

## <a name="installation-hangs"></a>Blocchi di installazione

Programmi di installazione sia Mono e xamarin. IOS blocco se è in esecuzione il simulatore iPhone. Questo problema non è limitato a Mono o xamarin, si tratta di un problema costante per qualsiasi software che tenta di installare software su MacOS neve Leopard se iPhone simulatore è in esecuzione al momento dell'installazione.

Assicurarsi di chiudere il simulatore di iPhone e ripetere l'installazione.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Si è esaurito trampolines di tipo 0

Se viene visualizzato questo messaggio durante l'esecuzione del dispositivo, è possibile creare ulteriori, digitare 0 trampolines (tipo specifico) modificando la sezione "compilazione iPhone" Opzioni di progetto.  Si desidera aggiungere destinazioni di compilazione argomenti aggiuntivi per il dispositivo:

 `-aot "ntrampolines=2048"`

Il numero predefinito di trampolines è 1024.  Provare ad aumentare questo numero fino a quando non si dispone di sufficiente per l'applicazione.

## <a name="ran-out-of-trampolines-of-type-1"></a>Si è esaurito trampolines di tipo 1

Se si apportano un uso massiccio di generics ricorsiva, si potrebbe essere visualizzato questo messaggio sul dispositivo.  È possibile creare ulteriori digitare 1 trampolines (tipo RGCTX) modificando la sezione "compilazione iPhone" Opzioni di progetto.  Si desidera aggiungere destinazioni di compilazione argomenti aggiuntivi per il dispositivo:

 `-aot "nrgctx-trampolines=2048"`

Il numero predefinito di trampolines è 1024.  Provare ad aumentare questo numero fino a quando non si dispone di sufficiente per l'utilizzo dei generics.

## <a name="ran-out-of-trampolines-of-type-2"></a>Si è esaurito trampolines di tipo 2

Se si apportano elevato utilizzo di interfacce, si potrebbe essere visualizzato questo messaggio nel dispositivo.
È possibile creare ulteriori digitare 2 trampolines (tipo IMT thunk) modificando la sezione "compilazione iPhone" Opzioni di progetto.  Si desidera aggiungere destinazioni di compilazione argomenti aggiuntivi per il dispositivo:

 `-aot "nimt-trampolines=512"`

Il numero predefinito di trampolines IMT Thunk è 128.  Provare ad aumentare questo numero fino a quando non si dispone di sufficiente per l'utilizzo di interfacce.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Debugger è in grado di connettersi con il dispositivo

Quando si avvia il debug di una configurazione del dispositivo, verrà visualizzato il debugger visualizza una finestra di dialogo che indica che sta tentando di connettersi all'applicazione. Esistono diversi motivi, che il debugger potrebbe non essere in grado di connettersi all'applicazione, a seconda della modalità sta usando per connettersi (USB o Wi-Fi).

 **Se il dispositivo e l'host del debugger si trovano in reti diverse**, un firewall o una rete privata potrebbe impedire l'applicazione di connettersi all'host del debugger in modalità Wi-Fi.

 **Visual Studio per Mac potrebbe non essere in grado di eseguire una query IP corretti dell'host**. In Wi-Fi modalità Visual Studio per Mac assegna all'applicazione tutti gli indirizzi IP è possibile individuare dell'host e l'applicazione prova a utilizzarli tutti per vedere se è possibile utilizzare uno di essi per la connessione a Visual Studio per Mac.

 **Un altro dispositivo è connesso a una porta USB sull'host.** In alcuni casi, gli altri dispositivi collegati a USB porte nell'host sono certo noti in qualche modo interferire con il debug in modalità USB.

Se la modalità Wi-Fi o USB non funziona, è possibile provare con facilità l'altro: in Visual Studio per Mac, aprire le preferenze, passare alla pagina Debugger Debugger/preferenze/iPhone e attivare o disattivare la casella di controllo "Debug dispositivi iOS tramite Wi-Fi anziché tramite USB".   Se non funziona, è possibile visualizzare ulteriori informazioni sull'errore nella console del dispositivo in modalità dettagliata (che è attivata per l'aggiunta di "-v - v a" per gli argomenti aggiuntivi mtouch nelle opzioni del progetto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Errore 134: mtouch non riuscita con il messaggio seguente:

Se si sta tentando di compilare con - nolink lo stile di xamarin. IOS 1.4 delle versioni è possibile che venga generato l'errore. È possibile risolvere questo errore, specificare argomenti aggiuntivi nella configurazione del progetto monodevelop.

Aggiungere l'argomento

 `-nosymbolstrip`

e il problema dovrebbe essere risolto.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identità di distribuzione non viene visualizzato in Visual Studio per il progetto Mac opzioni di firma

Visual Studio per Mac 2.2 dispone di un bug che causa la non rileva i certificati di distribuzione che contengono una virgola. Eseguire l'aggiornamento a Visual Studio per Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Errore "AFCFileRefWrite restituito: 1" durante il caricamento

Si potrebbe ricevere un errore durante il caricamento di un'app al dispositivo "AFCFileRefWrite restituito: 1". Questa situazione può verificarsi se si dispone di un file di lunghezza zero.

## <a name="error-mtouch-failed-with-no-output"></a>Errore "non è riuscita senza generazione dell'output mtouch"

La versione corrente di Visual Studio e xamarin. IOS per Mac esito negativo quando il nome del progetto o la directory in cui archiviare la soluzione o progetto contengono spazi.
Per risolvere il problema:


-  Assicurarsi che né il progetto o la directory in cui è memorizzato contiene uno spazio.
-  Nelle impostazioni di progetto"Main" assicurarsi che il nome del progetto non contiene spazi.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Errore "il file binario caricato non è valido. Una versione beta di pre-release di SDK è stata utilizzata per compilare l'applicazione"

Questo errore si verifica in genere con un progetto che è stato avviato in fase di sviluppo iPad prima del rilascio di xamarin. IOS 2.0.0, è probabile che alcune chiavi nelle Info. plist come:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Questa coppia di chiavi deve essere rimosso perché Visual Studio per Mac gestisce automaticamente automaticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Errore "è stata utilizzata una versione preliminare beta del SDK per compilare l'app"

(Fornito da Ed Anuff)

Attenersi ai passaggi riportati di seguito.

-  Modifica la versione del SDK iPhone compilazione 3.2 o iTunes connect rifiuterà al momento del caricamento perché che visualizzi un'app compatibili iPad compilata utilizzando una versione SDK minore 3.2
-  Creare un file Info. plist personalizzato per il progetto e impostare in modo esplicito MinimumOSVersion a 3.0 in essa contenuti.   Questo percorso sostituirà il valore di MinimumOSVersion 3.2 impostato da xamarin. IOS.   Se non esegue questa operazione, l'applicazione non sarà in grado di eseguire in un iPhone.
-  Ricompilazione, zip e il caricamento iTunes connect.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Eccezione non gestita: Exception: Impossibile trovare il selettore someSelector: su {type}

Questa eccezione è causata da una delle tre operazioni:


1.  È stato fornito un selettore per il runtime Objective-C senza applicare l'attributo [Esporta] corrispondente a un metodo
1.  Sono state abilitate il collegamento completo, non applicato l'attributo [Preserve] per il metodo ed [Esporta].
1.  L'attributo [Esporta] è stato applicato a un metodo privato in un tipo ereditato.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>File MainWindow.xib.designer.cs non viene aggiornato

Si è verificato un bug in Xamarin Studio 2.4 che lo ha causato non raggruppare il file MainWindow.xib con il file MainWindow.xib.designer in nuovi progetti. In questo modo che non aggiorna il codice della finestra di progettazione per il file in questione.

Il problema viene risolto nella versione di Visual Studio per Mac disponibile nel relativo aggiornamento predefinita, in modo da verificare è utilizzare la versione più recente.

È possibile correggere i progetti esistenti rimuovendo (non l'eliminazione) di XI e il file della finestra di progettazione, quindi aggiungendola nuovamente. Questo nuovo deve raggruppare correttamente i file.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView o UIActionSheet scompaiono dopo la creazione

Se si dispone di un codice simile al seguente:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

l'oggetto "actionSheet" risiede come una variabile temporanea nella funzione e non appena la funzione termina, l'oggetto è idoneo per l'operazione di garbage collection, pertanto viene eseguita la procedura di garbage collection.

Per risolvere questo problema, è necessario mantenere un riferimento a "actionSheet" all'esterno del metodo, un punto che verrà inserito oltre il metodo.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Progetto sempre viene eseguito nell'iPad simulatore

Il programma di installazione di SDK 4.0 iPhone installa 2 SDK - 3,2 SDK, per la compilazione di App iPad-only e il SDK 4.0, utilizzato per iPhone predefinito di tipo e App universali. Inoltre, viene installato un simulatore 3.2, che simula solo un iPad, e un simulatore 4.0 che simula l'iPhone o iPhone 4. Tutti gli SDK e simulatori meno recenti vengono rimossi.

Visual Studio per le opzioni di compilazione progetto Mac iPhone includono un'impostazione per la versione del SDK che verrà utilizzata nella compilazione dell'app. Questa impostazione è reperibile **opzioni progetto -> generazione -> iPhone compilazione**.

Se il SDK specificato non esiste, Visual Studio per Mac verrà utilizzato il più vicino per compilare l'app Trova nuovi progetti in Visual Studio per Mac utilizzano il SDK installato meno recente come impostazione di SDK. Questa operazione viene eseguita in modo che i progetti non sempre si requre SDK più recenti. Tuttavia, attualmente è nel SDK da 3,2 utilizzati - che comporta il simulatore iPad in uso.

Per risolvere questo problema utilizzando il SDK 4.0, passare a **opzioni progetto -> generazione -> iPhone compilazione**> e modificare il valore SDK su "4.0" tramite la casella a discesa. Questa operazione è necessaria per ciascuna configurazione e piattaforma, accessibili tramite i menu a discesa nella parte superiore del pannello.

La versione del SDK non va confuso con l'impostazione "Versione del sistema operativo minimo".
Questo valore non deve corrispondere al valore di versione SDK - riguarda la versione minima del sistema operativo verrà installato l'app, che può essere antecedente il SDK, purché si utilizzano solo le API che esistono nel sistema operativo precedente o evitare l'utilizzo delle funzionalità più recenti utilizzando BAC versione di runtime del sistema operativo localmente. La versione del sistema operativo meno recente prima di provare l'app, è consigliabile impostare tale.

Si noti inoltre che il **progetto -> iPhone destinazione simulatore**> menu può essere utilizzato per selezionare il simulatore è usato per impostazione predefinita, quando in esecuzione o il debug di un progetto. Inoltre, il **-> Esegui eseguire con**> menu può essere utilizzato per selezionare un simulatore specifico da utilizzare per eseguire

## <a name="ibtool-returns-error-133"></a>ibtool restituisce l'errore 133

Questo significa che è di 4 XCode installato.   In XCode 4, il ibtool strumento è stato rimosso, non è più possibile modificare i file XI con uno strumento autonomo.

Se si desidera utilizzare il generatore di interfaccia, installare [XCode serie 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponibile dal sito web di Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"Impossibile creare il binding di visualizzazione per il tipo mime: application/vnd.apple -<wbr/>interfaccia generatore"

Questo errore si verifica se si tenta di creare un iPhone dell'interfaccia utente da un progetto non iPhone. Assicurarsi di iniziare con una soluzione di iPhone/iPad, non è possibile aggiungere soltanto gli elementi dell'interfaccia utente di iPhone a un progetto di iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Arresto anomalo di avvio durante l'esecuzione all'interno di simulatore iOS

Se si verifica un arresto anomalo di runtime (SIGSEGV) all'interno del simulatore insieme a una traccia dello stack che è simile al seguente:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
.... then già assembly non aggiornato (più) nella directory dell'applicazione di simulatore. Tali assembly potrebbero esiste perché Apple simulatore iOS aggiunge e aggiorna i file, ma non li elimina. In questo caso quindi la soluzione più semplice consiste nel selezionare "Reimpostare e contenuto e impostazioni..." dal menu di simulatore.   

**Avviso:** verranno rimossi tutti i file, applicazioni e dati dal simulatore.   Successivo tentativo di eseguire l'applicazione, Visual Studio per Mac verrà distribuito nel simulatore e non sarà presente alcun assembly precedente, non essere aggiornato a causa dell'arresto anomalo.

## <a name="simulator-hangs-during-application-installation"></a>Simulatore si blocca durante l'installazione dell'applicazione

Questa situazione può verificarsi quando i nomi delle applicazioni includono un '.' (punto) nel nome.
Questo non è consentito come nome del file eseguibile in CFBundleExecutable - anche se può essere utilizzata in molti altri casi (ad esempio dispositivi).

 * "Il valore non può includere qualsiasi estensione del nome." - [http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Errore: "il tipo di attributo personalizzato 0x43 non è supportato" quando doppio clic su file .xib

Ciò è dovuto il tentativo di aprire file .xib quando le variabili di ambiente vengono impostate in modo non corretto. Ciò non dovrebbe verificarsi con il normale utilizzo di Visual Studio per Mac/Xamarin.iOS e riaprire Visual Studio per Mac da /Applications per risolvere il problema.

Quando si tenta di aggiornare il software e questo messaggio di errore viene visualizzato,. messaggio di posta elettronica *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Applicazione viene eseguita nel simulatore, ma non sul dispositivo

Questo problema può manifestarsi in diversi moduli e non produce sempre un errore di coerenza. Se l'applicazione contiene un .xib, assicurarsi che il **azione di compilazione** il .xib è impostata su **InterfaceDefinition**. Questo è l'azione di compilazione predefinito per .xibs.

Per verificare l'azione di compilazione, fare clic con il pulsante destro sul file .xib e scegliere **azione di compilazione**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: Non sono disponibili dati per la codifica 437

Quando si include librerie di terze parti 3rd nell'app xamarin, si verifichi un errore nel modulo "System. NotSupportedException: dati non sono disponibili per la codifica 437" durante il tentativo di compilare ed eseguire l'app. Ad esempio, le librerie, ad esempio `Ionic.Zip.ZipFile`, può generare questa eccezione durante l'operazione.

Questo può essere risolto, aprire le opzioni per il progetto xamarin. IOS, passare a **compilazione iOS** > **internazionalizzazione** e controllando il **occidentale** internazionalizzazione.



<a name="Can't_upgrade_to_Indie/Business_from_Trial_Account" />


## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>Non è possibile eseguire l'aggiornamento a immaginare/Business dall'Account di valutazione

Se si recentemente acquistato xamarin e avviato in precedenza una versione di valutazione di xamarin, si potrebbe essere necessario completare i passaggi seguenti per ottenere questa modifica licenza prelevata da Visual Studio per Mac o Visual Studio.

-  Chiudere Visual Studio per Mac e Visual Studio
-  Rimuovere tutti i file da ~/Library/MonoTouch su Mac o %PROGRAMDATA%\MonoTouch\License\ per Windows
-  Riaprire Visual Studio per Mac e Visual Studio e compila un progetto xamarin


## <a name="receiving-activation-incomplete-error-message"></a>Riceve il messaggio di errore di 'attivazione incompleto'

Questo problema può verificarsi quando si usa xamarin per Visual Studio. Per risolvere questo problema, inviare i log dal seguente percorso per [ contact@xamarin.com ](mailto:contact@xamarin.com).

-  Percorso file di registro: %LocalAppData%/Xamarin/Logs
