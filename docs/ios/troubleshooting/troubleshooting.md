---
title: Suggerimenti di risoluzione dei problemi per xamarin. IOS
description: Questo documento offre diversi suggerimenti utili per la risoluzione dei problemi relativi durante lo sviluppo di applicazioni xamarin. IOS. Vengono descritti i messaggi di errore specifici, nonché altri potenziali problemi.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2018
ms.openlocfilehash: 38c0ece3e8f0361f3c891713e53b033351512f94
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829913"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Suggerimenti di risoluzione dei problemi per xamarin. IOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin. IOS non riesce a risolvere valuetuple

Questo errore si verifica a causa di un problema di incompatibilità con Visual Studio.

- **Visual Studio 2017 Update 1** (versione 15.1 o versioni precedenti) è compatibile solo con **System. valuetuple NuGet 4.3.0** (o versioni precedenti).

- **Visual Studio 2017 Update 2** (versione 15.2 o versione successiva) è compatibile solo con il **System. valuetuple NuGet 4.3.1** o versione successiva.

Scegliere di NuGet valuetuple corretto che corrisponde all'installazione di Visual Studio 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Ricezione di messaggi di errore 'Errore di recupero delle informazioni sugli aggiornamenti'

Quando si tenta di aggiornare il software e questo messaggio di errore viene visualizzato, provare a riavviare l'IDE e la disconnessione e quindi di nuovo al proprio account nell'IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Come creare Outlet o le azioni con Interface Builder

Con l'introduzione della finestra di progettazione di Xamarin per iOS in Visual Studio per Mac e Visual studio, gli sviluppatori di xamarin. IOS possono ora sfruttare i vantaggi della creazione di un'interfaccia utente tramite storyboard e .xibs. Vedere le [Hello, iOS](~/ios/get-started/hello-ios/index.md) Guide per altre informazioni sull'uso della finestra di progettazione.

È anche possibile fare riferimento a Apple [Outlet](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) e [azioni](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) Guide per altre informazioni sull'uso in IB Outlet e azioni.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding genera un'eccezione NotSupportedException

È possibile usare una codifica non viene aggiunto per impostazione predefinita. Verificare i [internazionalizzazione](~/ios/app-fundamentals/localization/index.md) pagina per informazioni su come aggiungere il supporto per la codifica di più.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (altro)

Il membro probabilmente è stato rimosso dal linker e pertanto non esiste nell'assembly in fase di esecuzione.  Esistono diverse soluzioni a questo:

- Aggiungere il [ `[Preserve]` ](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) al membro dell'attributo.  Ciò impedirà al linker di rimuoverlo.
- Quando si richiama [ **mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), usare il **- nolink** oppure **- linksdkonly** opzioni:
  - **-nolink** Disabilita tutti i collegamenti.
  - **-linksdkonly** verranno collegati, ad esempio solo gli assembly fornita da xamarin. IOS, **DLL**, mantenendo tutti i tipi negli assembly creato dall'utente (ie. i progetti di app).

Si noti che gli assembly vengono collegati in modo che il file eseguibile risultante è più piccolo; di conseguenza, la disabilitazione del collegamento può comportare un file eseguibile maggiore rispetto a cui è auspicabile.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Si riceve un ModelNotImplementedException

Se si riceve questa eccezione ciò significa che si sta chiamando base. Metodo () in una classe che esegue l'override di un modello. Non è necessario chiamare il metodo di base in una classe di modelli (queste sono le classi contrassegnate con l'attributo [modello]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Questa classe non è il valore di chiave di codifica compatibile per la chiave XXXX

Se questo errore si verifica quando si carica un file NIB significa che il valore che xxxx non è stato trovato nella classe gestita. Ciò significa che mancano una dichiarazione simile alla seguente:

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

La definizione precedente viene generata automaticamente da Visual Studio per Mac per tutti i file XIB che aggiungono a Visual Studio per Mac nel `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` file.

Inoltre, i tipi che contiene il codice sopra riportato devono essere una sottoclasse [NSObject](xref:Foundation.NSObject).  Se il tipo contenitore è all'interno di uno spazio dei nomi, è necessario che abbia una [[registrare]](xref:Foundation.RegisterAttribute) attributo che fornisce un nome di tipo senza uno spazio dei nomi (come Interface Builder non supporta gli spazi dei nomi di tipi):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe sconosciuta XXXX nel file di Interface Builder

Questo errore viene generato se si definisce una classe nei file del generatore di interfaccia, ma non si specifica l'implementazione effettiva corrispondente nei C# codice.

È necessario aggiungere il codice simile al seguente:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException: Non sono stati trovati per Foo.Bar::ctor(System.IntPtr) costruttori

Questo errore viene generato in fase di esecuzione quando il codice tenta di creare un'istanza delle classi che si è fatto riferimento dal file di Interface Builder. Ciò significa che si sia dimenticato di aggiungere un costruttore che accetta un singolo elemento IntPtr come parametro.

Il costruttore con un handle IntPtr viene utilizzato per associare gli oggetti gestiti con le relative rappresentazioni non gestite.

Per risolvere questo problema, aggiungere la riga di codice seguente alla classe Foo. bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Tipo {Foo} non contiene una definizione per `GetNativeField` alcun metodo di estensione e `GetNativeField` di tipo {Foo} è stata trovata

Se viene visualizzato questo errore nei file della finestra di progettazione generati (*. xib.designer.cs), significa che una delle seguenti operazioni:

 **1) manca di classe parziale o classe di base**

Le classi parziali generate-finestra di progettazione devono avere corrispondenti classi parziali nel codice utente da cui ereditare alcuni sottoclasse `NSObject`, spesso `UIViewController`. Assicurarsi di avere una classe per il tipo da cui proviene l'errore.

 **2) gli spazi dei nomi predefinito modificato**

Vengono generati i file della finestra di progettazione utilizzando le impostazioni del progetto predefinito dello spazio dei nomi. Se si hanno queste impostazioni modificate o rinominato il progetto, le classi parziali generate potrebbero non essere più nello spazio di nomi delle rispettive controparti codice utente.

Le impostazioni di Namespace sono reperibile nella finestra di dialogo Opzioni progetto. Spazio dei nomi predefinito viene trovato nel **generale -> Impostazioni principali** sezione. Se è vuota, l'impostazione predefinita viene utilizzato il nome del progetto. Le impostazioni dello spazio dei nomi più avanzate sono reperibile nella **codice sorgente -> Criteri di denominazione .NET** sezione.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Avviso per le azioni: Il metodo privato 'Foo' non è mai utilizzato. (CS0169)

Azioni per i file generatore dell'interfaccia sono connesse per i widget tramite reflection in fase di esecuzione, in modo che questo avviso sia previsto.

È possibile usare "avviso #pragma Disabilita 0169" "Abilita avviso #pragma 0169" per le azioni se si desidera eliminare l'avviso solo per questi metodi oppure aggiungere 0169 al campo "Ignora gli avvisi" nelle opzioni del compilatore se si desidera disabilitarlo per l'intero progetto (non scelta consigliata).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch non è riuscita con messaggio analogo al seguente: Non è possibile aprire l'assembly ' / path/to/yourproject.exe'

Se viene visualizzato questo messaggio di errore, in genere il problema è che il percorso assoluto per il progetto contiene uno spazio. Questo problema verrà risolto in una futura versione di xamarin. IOS, ma è possibile risolvere il problema tramite lo spostamento del progetto in una cartella senza spazi.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>La versione sqlite3 è precedente, aggiornare almeno alla 3.5.0.

Ciò si verifica quando si esegue l'operazione di tutti gli elementi seguenti:

1.  Usare Mono.Data.Sqlite
1.  Usare Mac OS X Leopard (10.5)
1.  Eseguire l'app all'interno del simulatore.


Il problema è che Mono preleva per OS X `libsqlite3.dylib`, del non il iPhoneSimulator `libsqlite3.dylib` file. L'app *verranno* operano con il dispositivo, ma non il simulatore.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Distribuire avrà esito negativo di periferica e System. Exception: AMDeviceInstallApplication restituito 3892346901

Questo errore indica che la configurazione di firma codice per l'id bundle/certificato non corrisponde al profilo di provisioning installato nel dispositivo.  Verificare che sia il certificato appropriato selezionato in Opzioni progetto -> firma del Bundle iPhone e l'id bundle corrette specificata in Opzioni progetto -> applicazione iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Il completamento del codice non funziona in Visual Studio per Mac

Assicurarsi che si usa la versione più recente di Visual Studio per Mac e xamarin. IOS

Se il problema è ancora presente, [segnalare un bug](http://monodevelop.com/Developers#Reporting_Bugs), allegare il **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componenti-{TIMESTAMP}. log** i file di log.

Se tutto il resto non riesce, è possibile provare a rimuovere la cache di completamento di codice in modo che viene rigenerato:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Prestare attenzione che si digita il comando in modo corretto oppure è possibile rimuovere inavvertitamente file importanti.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Arresto anomalo di Visual Studio per Mac quando si copia testo

Le comuni utilità Mac QuickSilver, sulla barra degli strumenti di Google e finestra di avvio hanno funzionalità Appunti che danneggiata di Visual Studio per la memoria del Mac. Nelle opzioni, è possibile elencare Visual Studio per Mac come processo a che cui non dovrebbe interferire con.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio per Mac Visualizza un errore su Mono 2.4 obbligatorio

Se è stato aggiornato Visual Studio per Mac a causa di un aggiornamento recente e quando si tenta di avviarlo nuovamente Visualizza un errore su Mono 2.4 non è presente, è necessario eseguire resta [aggiornare l'installazione di Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3_6 corregge alcuni problemi importanti che ha impedito Visual Studio per Mac che esegue in modo affidabile, a volte bloccata di Visual Studio per Mac all'avvio o ha impedito il database di completamento di codice da generare.

Dopo aver installato il nuovo Mono, Visual Studio per Mac verrà avviato come previsto.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Asserzione in... /.. /.. /.. /mono/Metadata/Generic-Sharing.c:704, condizione 'oti' non soddisfatti

Se si riceve l'analisi dello stack seguenti:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Ciò significa che si sta collegando una libreria statica compilata con codice thumb nel progetto. A partire dalla versione di iPhone SDK 3.1 (o versione successiva al momento della stesura di questo articolo) Apple ha introdotto un bug nel loro linker quando si collega il codice non Thumb (xamarin. IOS) con codice Thumb (la libreria statica). È necessario eseguire il collegamento con una versione non è una casella di scorrimento della libreria statica per attenuare il problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException: Tentativo di Foo[]:System.Collections.Generic.ICollection'1.get_Count di metodo (wrapper gestito a codice gestito) (compilazione JIT)

Il suffisso [] indica che è la libreria di classi o chiama un metodo su una matrice tramite una raccolta generica, ad esempio <> IEnumerable, <> ICollection o <> IList. In alternativa, è possibile forzare in modo esplicito al compilatore AOT di includere tale metodo chiamando il metodo manualmente e assicurandosi che viene eseguito questo codice prima della chiamata che ha attivato l'eccezione. In questo caso, è possibile scrivere:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Forzando il compilatore AOT di includere il metodo.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio per l'editor di origine Mac è molto lento

In alcuni casi l'editor Visual Studio per Mac origine diventa estremamente lento, apparenza sembrano in blocco per alcuni secondi tra la digitazione di caratteri.

Questo problema è molto raro ed estremamente difficili da riprodurre: in genere non può essere riprodotta sullo stesso computer dopo il riavvio di Visual Studio per Mac. Per questo motivo, si sarebbe apprezza se è possibile eseguire diversi passaggi di debug prima di riavviare Visual Studio per Mac e inviare i risultati per noi.

1.  Provare a chiudere la scheda dell'editor e aprirlo nuovamente. Richiede un po' di modifica o spostare il cursore fino a quando il rallentamento si verifica nuovamente?
1.  Disabilitare "Trasmetti Sync" utilizzando lo strumento di sviluppo "Quartz Debug" (che è possibile trovare usando Spotlight) e controllare se le prestazioni di editor di origine viene ripristinata allo stato normale.
1.  Con la sincronizzazione Trasmetti ancora disabilitata, provare a ripetere il passaggio (1).
1.  Se l'editor si blocca per più di pochi secondi, provare a eseguire "killall-Esci [Visual Studio per Mac]" in un terminale, mentre si è bloccato. Potrebbe essere difficile per il comando kill per verificarsi mentre l'editor è bloccato, ma è essenziale per eseguire questa operazione, poiché il comando forza Mono per scrivere le analisi dello stack di tutti i thread nel registro MD, che è possibile usare per individuare i thread sono in mentre si blocca il XS allo stato di tempo.



Allegare i log, XS **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componenti-{TIMESTAMP}. log**(nelle versioni precedenti di MonoDevelop/XS, è sufficiente inviare **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **NOTA: È stato risolto il problema sopra indicato in XS 2.2 finale**

## <a name="compiled-application-is-very-large"></a>Applicazione compilata è molto grande

Per supportare il debug, le compilazioni di debug contengono codice aggiuntivo. I progetti compilati in modalità di rilascio sono una frazione della dimensione.

A partire da xamarin. IOS 1.3 del debug compilazioni incluso il supporto per ogni singolo componente di Mono (ogni metodo in ogni classe dei Framework) del debug.  

Con xamarin. IOS 1.4 viene introdotta un metodo con granularità maggiore per il debug, il valore predefinito sarà solo fornire strumentazione debug per il codice e le librerie e non eseguire questa operazione per tutte le [assembly Mono](~/cross-platform/internals/available-assemblies.md) (questo verrà comunque è possibile, ma è necessario acconsentire esplicitamente all'esecuzione del debug degli assembly).

## <a name="installation-hangs"></a>Blocchi di installazione

Programmi di installazione sia Mono e xamarin. IOS di blocco se hai il simulatore iPhone in esecuzione. Questo problema non è limitato a Mono o xamarin. IOS, si tratta di un problema costante per qualsiasi software che prova a installare il software su MacOS Snow Leopardato se iPhone simulatore è in esecuzione al momento dell'installazione.

Assicurarsi di uscire dal simulatore di iPhone e ripetere l'installazione.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Si è esaurito trampolines di tipo 0

Se viene visualizzato questo messaggio durante l'esecuzione di dispositivo, è possibile creare ulteriori, digitare 0 trampolines (tipo specifiche) modificando la sezione "iPhone compilazione" Opzioni di progetto.  Si vuole aggiungere argomenti aggiuntivi per il dispositivo di compilazione di destinazioni:

 `-aot "ntrampolines=2048"`

Il numero predefinito di trampolines è 1024.  Provare ad aumentare questo numero fino a quando non sufficiente per l'applicazione.

## <a name="ran-out-of-trampolines-of-type-1"></a>Si è esaurito trampolines di tipo 1

Se si apportata un uso massiccio di generics ricorsiva, si potrebbe essere visualizzato questo messaggio nel dispositivo.  È possibile creare ulteriori digitare 1 trampolines (tipo RGCTX) modificando la sezione "iPhone compilazione" Opzioni di progetto.  Si vuole aggiungere argomenti aggiuntivi per il dispositivo di compilazione di destinazioni:

 `-aot "nrgctx-trampolines=2048"`

Il numero predefinito di trampolines è 1024.  Provare ad aumentare questo numero fino a quando non sufficiente per l'utilizzo dei generics.

## <a name="ran-out-of-trampolines-of-type-2"></a>Si è esaurito trampolines di tipo 2

Se si usano un'intensa attività di interfacce, si potrebbe essere visualizzato questo messaggio nel dispositivo.
È possibile creare ulteriori digitare 2 trampolines (tipo IMT thunk) modificando la sezione "iPhone compilazione" Opzioni di progetto.  Si vuole aggiungere argomenti aggiuntivi per il dispositivo di compilazione di destinazioni:

 `-aot "nimt-trampolines=512"`

Il numero predefinito di trampolines IMT Thunk è 128.  Provare ad aumentare questo numero fino a quando non sufficiente per l'utilizzo di interfacce.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Debugger non riesce a connettersi con il dispositivo

Quando si avvia il debug di una configurazione del dispositivo, verrà visualizzato il debugger visualizza una finestra di dialogo che indica che sta tentando di connettersi all'applicazione. Esistono diversi motivi, che il debugger potrebbe non essere in grado di connettersi all'applicazione, a seconda della modalità sta usando per connettersi (USB o Wi-Fi).

 **Se il dispositivo e l'host del debugger si trovano in reti diverse**, un firewall o rete privata può impedire l'applicazione di connettersi all'host del debugger in modalità Wi-Fi.

 **Visual Studio per Mac non siano in grado di eseguire una query l'indirizzo IP corretto dell'host**. In Wi-Fi modalità Visual Studio per Mac fornisce all'applicazione tutti gli indirizzi IP è possibile trovare dell'host e l'applicazione prova a utilizzarli tutti per vedere se è possibile usare uno di essi per la connessione a Visual Studio per Mac.

 **Un altro dispositivo è connesso a una porta USB sull'host.** In alcuni casi gli altri dispositivi collegati a USB porte nell'host sono certo noti in qualche modo interferisca con il debug in modalità USB.

Se non funziona in modalità Wi-Fi o USB, è possibile provare con facilità l'altro: in Visual Studio per Mac, aprire le preferenze, passare alla pagina delle preferenze/Debugger/iPhone Debugger e attivare/disattivare la casella di controllo "Debug i dispositivi iOS tramite WiFi anziché tramite USB".   Se non funziona, è possibile visualizzare altre informazioni sull'errore nella console del dispositivo in modalità dettagliata (che è abilitato aggiungendo "-v - v a" per gli argomenti aggiuntivi di mtouch nelle opzioni del progetto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Errore 134: mtouch non è riuscita con messaggio analogo al seguente:

Questo errore potrebbe essere generato se si sta tentando di compilare con - nolink lo stile di xamarin. IOS 1.4 delle versioni. È possibile risolvere l'errore specificando argomenti aggiuntivi nella configurazione del progetto monodevelop.

Aggiungere l'argomento

 `-nosymbolstrip`

e deve essere risolto il problema.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identità di distribuzione non è visualizzato in Visual Studio per progetti Mac opzioni di firma

Visual Studio per Mac 2.2 include un bug che causa la non rilevare i certificati di distribuzione che contengono una virgola. Aggiornare Visual Studio per Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Errore "AFCFileRefWrite restituito: 1" durante il caricamento

Si potrebbe ricevere un errore durante il caricamento di un'app nel dispositivo "AFCFileRefWrite restituito: 1". Questa situazione può verificarsi se si dispone di un file di lunghezza zero.

## <a name="error-mtouch-failed-with-no-output"></a>Errore "non è riuscita con alcun output di mtouch"

La versione corrente di xamarin. IOS e Visual Studio per Mac fail quando: il nome del progetto o la directory in cui vengono archiviati la soluzione o progetto contenere spazi.
Per risolvere il problema:


-  Assicurarsi che né il progetto o la directory in cui è archiviato contiene uno spazio.
-  Nelle impostazioni del progetto"Main" assicurarsi che il nome del progetto non contiene spazi.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Errore "il file binario caricato non è valido. Una versione beta di versioni non definitive del SDK è stata usata per compilare l'applicazione"

Questo errore è causato in genere con un progetto che è stato avviato in fase di sviluppo iPad prima di xamarin. IOS 2.0.0 è stata rilasciata, hai probabilmente alcune chiavi nel file Info. plist, ad esempio:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Questa coppia di chiavi deve essere rimosso perché Visual Studio per Mac gestisce automaticamente automaticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Errore "è stata utilizzata una versione beta di versioni non definitive del SDK per compilare l'app"

(Fornito da Ed Anuff)

Attenersi ai passaggi riportati di seguito.

-  Modifica la versione del SDK in iPhone compilazione 3.2 o iTunes connect rifiuterà. al momento del caricamento poiché vede un'app per iPad compatibile compilata utilizzando una versione del SDK è inferiore a 3.2
-  Creare un file Info. plist personalizzato per il progetto e impostare in modo esplicito MinimumOSVersion 3.0 in esso.   Sostituirà il valore 3.2 MinimumOSVersion impostato da xamarin. IOS.   Se non eseguire questa operazione, l'app non sarà in grado di eseguire in un iPhone.
-  Ricompilazione, zip e caricare in iTunes connect.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Eccezione non gestita: System.Exception: Impossibile trovare il selettore someSelector: su {type}

Questa eccezione è causata da una delle tre operazioni:


1.  È stato specificato un selettore al runtime di Objective-C senza applicare l'attributo [esportazione] corrispondente a un metodo
1.  Sono state abilitate collegamento completo, non applicare l'attributo [Preserve] al metodo ed [esportazione].
1.  È stato applicato l'attributo [Export] a un metodo privato in un tipo ereditato.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>File MainWindow.xib.designer.cs non viene aggiornata

Si è verificato un bug in Xamarin Studio 2.4 che ha causato il non il file MainWindow.xib con il file MainWindow.xib.designer nei nuovi progetti di gruppo. Questo significava che non aggiorna il codice della finestra di progettazione per il file in questione.

Questo problema viene risolto nella versione di Visual Studio per Mac è disponibile nel relativo strumento di aggiornamento predefinita, in modo da assicurarsi che si usa la versione più recente.

È possibile correggere i progetti esistenti tramite la rimozione (non eliminare) il file xib e il file della finestra di progettazione, quindi aggiungerlo nuovamente. Questo nuovo deve raggruppare correttamente i file.

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

l'oggetto "actionSheet" disponibile come una variabile temporanea nella funzione e non appena la funzione termina, l'oggetto è idonea per garbage collection, pertanto viene eseguita la procedura di garbage collection.

Per risolvere questo problema, è necessario mantenere un riferimento al "actionSheet" all'esterno del metodo, in una posizione che verrà eseguito di là del metodo.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Progetto sempre viene eseguito nell'iPad simulatore

Il programma di installazione di iPhone SDK 4.0 viene installato 2 SDK - SDK 3,2, per la creazione di App solo iPad e il SDK 4.0, usato per creare App universali e iPhone. Inoltre, viene installato un simulatore 3.2, che simula soltanto un iPad, e un simulatore 4.0 che simula un iPhone o iPhone 4. Tutti gli SDK e simulatori meno recenti vengono rimossi.

Visual Studio per le opzioni di compilazione Mac iPhone progetto includono un'impostazione per la versione del SDK che verrà usata nella creazione dell'app. Questa impostazione è disponibile nel **opzioni progetto -> Build -> iPhone compilazione**.

I nuovi progetti in Visual Studio per Mac usano meno recente SDK installato come impostazione predefinita SDK e se il SDK specificato non esiste, verrà utilizzato il più vicino per compilare l'app è possibile trovare Visual Studio per Mac. Questa operazione è stata eseguita in modo che i progetti non richiede sempre il SDK più recente. Tuttavia, attualmente è nel SDK 3,2 da utilizzato - conseguente simulatore iPad in uso.

Per risolvere questo problema usando il SDK 4.0, passare a **opzioni progetto -> Build -> iPhone compilazione**> e modificare il valore SDK su "4.0" usando la casella a discesa. Questa operazione è necessaria per ogni configurazione e la combinazione di piattaforma, accedere usando gli elenchi a discesa nella parte superiore del pannello.

La versione del SDK non va confuso con l'impostazione della "Versione minima del sistema operativo".
Questo valore non deve corrispondere al valore di versione SDK: riguarda la versione minima del sistema operativo verrà installato l'app, che può essere meno recente del SDK, purché si utilizzino solo le API che esistono nel sistema operativo meno recente o evitare l'uso delle funzionalità più recenti usando Estrai versione di runtime del sistema operativo KS. È consigliabile impostare tale la versione del sistema operativo meno recente in cui testare l'app.

Si noti inoltre che il **progetto -> destinazione simulatore iPhone**> menu può essere utilizzato per selezionare il simulatore viene usato per impostazione predefinita, quando in esecuzione e un progetto di debug. Inoltre, il **Run -> Esegui con**> menu può essere utilizzato per selezionare un simulatore specificato con cui si desidera eseguire

## <a name="ibtool-returns-error-133"></a>ibtool restituisce l'errore 133

Ciò significa che hai 4 XCode sia installato.   In XCode 4, il ibtool strumento è stato rimosso, non è più possibile modificare i file XIB con uno strumento autonomo.

Se si desidera usare Interface Builder, installare [serie di XCode 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponibile dal sito web di Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"Impossibile creare binding di visualizzazione per il tipo mime: application/vnd.apple -<wbr/>interface builder"

Questo errore si verifica se si prova a creare un iPhone dell'interfaccia utente da un progetto non iPhone. Assicurarsi di iniziare con una soluzione di iPhone/iPad, non è possibile aggiungere solo gli elementi dell'interfaccia utente di iPhone a un progetto di iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Arresto anomalo di avvio durante l'esecuzione all'interno del simulatore iOS

Se si verifica un arresto anomalo del runtime (SIGSEGV) all'interno del simulatore insieme a un'analisi dello stack che si presenta come segue:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
... then sono probabilmente assembly non aggiornato uno (o più) nella directory dell'applicazione di simulatore. Tali assembly potrebbero esiste perché Apple iOS simulator aggiunge e aggiorna i file, ma non li elimina. In questo caso quindi la soluzione più semplice consiste nel selezionare "Reimposta e contenuto e le impostazioni in corso" dal menu di simulatore.   

> [!WARNING]
> In questo rimuoverà tutti i file, applicazioni e i dati dal simulatore.   Successivo tentativo di eseguire l'applicazione, Visual Studio per Mac verrà distribuito nel simulatore e non sarà presente alcun assembly precedente, non essere aggiornato a causa dell'arresto anomalo.

## <a name="simulator-hangs-during-application-installation"></a>Simulatore si blocca durante l'installazione dell'applicazione

Questa situazione può verificarsi quando i nomi delle applicazioni includono un '.' (punto) nel nome.
Ciò non è consentita come nome del file eseguibile in CFBundleExecutable - anche se possibile funziona in molti altri casi (ad esempio i dispositivi).

 \* "Il valore non può includere qualsiasi estensione del nome." - [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Errore: "Non è supportato il tipo di attributo personalizzato 0x43" quando si fa doppio clic su file con estensione xib

Ciò è causato dal tentativo di aprire file con estensione xib quando le variabili di ambiente vengono impostate in modo non corretto. Ciò non dovrebbe accadere con il normale utilizzo di Visual Studio per Mac/Xamarin.iOS e riaprire Visual Studio per Mac da /Applications dovrebbe risolvere il problema.

Quando si tenta di aggiornare il software e questo messaggio di errore viene visualizzato,. messaggio di posta elettronica *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Applicazione viene eseguita nel simulatore, ma non riesce sul dispositivo

Questo problema può manifestarsi in diversi moduli e non sempre produce un errore di coerenza. Se l'applicazione contiene un xib, assicurarsi che il **Build Action** con l'estensione xib viene impostata su **InterfaceDefinition**. Questo è l'azione di compilazione predefinito per .xibs.

Per controllare l'azione di compilazione, fare clic con il pulsante destro sul file con estensione xib, scegliere **azione di compilazione**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: Dati non sono disponibili per la codifica 437

Quando si include librerie di terze parti 3rd nell'app xamarin. IOS, è possibile ottenere un errore nel modulo "System. NotSupportedException: Non sono disponibili dati per la codifica 437" quando si tenta di compilare ed eseguire l'app. Ad esempio, le librerie, ad esempio `Ionic.Zip.ZipFile`, possono generare questa eccezione durante l'operazione.

Questo problema può essere risolto, aprire le opzioni per il progetto xamarin. IOS, passare a **compilazione iOS** > **internazionalizzazione** e controllando il **occidentale** internazionalizzazione.
