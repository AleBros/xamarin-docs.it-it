---
title: Un'app Xamarin.Mac sandboxing
description: Questo articolo descrive il sandboxing un'applicazione Xamarin.Mac per la versione in App Store. Copre tutti gli elementi che costituiscono il sandboxing, ad esempio directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni imposizione del kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a02d7639975de092b05f31bacedd6bde4c9392f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787909"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Un'app Xamarin.Mac sandboxing

_Questo articolo descrive il sandboxing un'applicazione Xamarin.Mac per la versione in App Store. Copre tutti gli elementi che costituiscono il sandboxing, ad esempio directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni imposizione del kernel._

## <a name="overview"></a>Panoramica

Quando si lavora con c# e .NET in un'applicazione Xamarin.Mac, è la stessa capacità di sandbox un'applicazione come avviene quando si lavora con Objective-C o Swift.

[![Un esempio di app in esecuzione](sandboxing-images/intro01.png "un esempio di app in esecuzione")](sandboxing-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo di sandboxing in un'applicazione Xamarin.Mac e tutti gli elementi che costituiscono il sandboxing: directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni imposizione del kernel. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile dare un'occhiata il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` gli attributi utilizzato per associare le classi c# Objective-C e agli oggetti dell'interfaccia utente gli elementi.

## <a name="about-the-app-sandbox"></a>Su Sandbox dell'App

La App Sandbox fornisce forte difesa contro i danni che possono essere causato da un'applicazione dannosa in esecuzione su un Mac, limitando l'accesso alle risorse di sistema di un'applicazione.

Un'applicazione non creata mediante sandbox dispone dei diritti dell'utente che esegue l'app e può accedere o eseguire alcuna operazione che l'utente può. Se l'app contiene delle vulnerabilità di sicurezza (o qualsiasi framework in uso), un pirata informatico può sfruttare tali punti di debolezza potenzialmente e usare l'app per assumere il controllo del Mac in fase di esecuzione in.

Per limitare l'accesso alle risorse di ogni applicazione, un'app creata mediante sandbox fornisce una linea di difesa contro il furto, danni o dannosi da parte di un'applicazione in esecuzione sul computer dell'utente.

Sandbox dell'App è una tecnologia di controllo di accesso incorporata macOS (applicata a livello di kernel) che fornisce una strategia duplice:

1. La App Sandbox consente agli sviluppatori descrivere _come_ un'applicazione interagisce con il sistema operativo e, in questo modo, viene concessa solo i diritti di accesso che sono necessari per svolgere il lavoro e non sono più presenti.
2. La App Sandbox consente all'utente di concedere ulteriore accesso al sistema tramite l'apertura e salvare le finestre di dialogo, trascinare e rilasciare operazioni e le interazioni utente più comuni senza problemi.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparazione all'implementazione della Sandbox di App

Gli elementi della App Sandbox che verranno descritte in dettaglio nell'articolo sono come segue:

- Directory contenitore
- Diritti
- Autorizzazioni utente determinato
- Separazione dei privilegi
- Imposizione del kernel

Dopo aver compreso questi dettagli, sarà possibile creare un piano per l'adozione della App Sandbox Xamarin.Mac nell'applicazione in uso.

In primo luogo, è necessario stabilire se l'applicazione è un buon candidato per il sandboxing (la maggior parte delle applicazioni sono). Successivamente, è necessario risolvere eventuali incompatibilità dell'API e determinare quali elementi della App Sandbox sarà necessaria. Esaminare infine, usare la separazione dei privilegi per ottimizzare è il livello di protezione dell'applicazione.

Quando l'adozione della Sandbox di App, alcuni percorsi del file system che utilizza l'applicazione sarà diversi. In particolare, l'applicazione disporrà di una Directory contenitore che verrà utilizzato per il file di supporto dell'applicazione, database, cache e qualsiasi altro file che non sono documenti degli utenti. MacOS sia Xcode forniscono supporto per eseguire la migrazione di questi tipi di file dai percorsi precedenti al contenitore.

## <a name="sandboxing-quick-start"></a>Avvio rapido di sandboxing

In questa sezione si creerà una semplice app Xamarin.Mac che usa una visualizzazione Web, che richiede una connessione di rete è limitata in sandboxing, a meno che specificamente richiesto, ad esempio dell'introduzione della Sandbox di App.

Si sarà verificare che l'applicazione è effettivamente creata mediante sandbox e informazioni su come individuare e risolvere gli errori più comuni App Sandbox.

### <a name="creating-the-xamarinmac-project"></a>Creazione del progetto Xamarin.Mac

Di seguito, eseguire il comando seguente per creare il progetto di esempio:

1. Avviare Visual Studio per Mac e fare clic su di **nuova soluzione...** account".
2. Dal **nuovo progetto** nella finestra di dialogo **Mac** > **App** > **Cocoa App**: 

    [![Creazione di una nuova App Cocoa](sandboxing-images/sample01.png "la creazione di una nuova App Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Fare clic su di **Avanti** pulsante, immettere `MacSandbox` per il nome del progetto e fare clic su di **crea** pulsante: 

    [![Immettere il nome dell'app](sandboxing-images/sample02.png "immettendo il nome dell'app")](sandboxing-images/sample02-large.png#lightbox)
4. Nel **soluzione riempimento**, fare doppio clic su di **Main** file per aprirlo e modificarlo in Xcode: 

    [![La modifica di storyboard principale](sandboxing-images/sample03.png "la modifica di storyboard principale")](sandboxing-images/sample03-large.png#lightbox)
5. Trascinare un **visualizzazione Web** nella finestra, ridimensionarlo per riempire l'area di contenuto e impostarlo in modo da aumentare e ridurre con la finestra: 

    [![Aggiunta di una visualizzazione web](sandboxing-images/sample04.png "aggiunta di una visualizzazione web")](sandboxing-images/sample04-large.png#lightbox)
6. Creare una presa di corrente per la visualizzazione web chiamata `webView`: 

    [![Creazione di nuovi punti vendita](sandboxing-images/sample05.png "la creazione di nuovi punti vendita")](sandboxing-images/sample05-large.png#lightbox)
7. Tornare a Visual Studio per Mac e fare doppio clic su di **ViewController.cs** file nel **soluzione riempimento** per aprirlo e modificarlo.
8. Aggiungere la seguente istruzione using: `using WebKit;`
9. Rendere il `ViewDidLoad` metodo aspetto simile al seguente: 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. Salvare le modifiche.

Eseguire l'applicazione si e verificare che il sito Web Apple viene visualizzato nella finestra come segue:

[![Visualizzazione di un'applicazione di esempio esecuzione](sandboxing-images/sample06.png "che mostra un'app di esempio esecuzione")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>La firma e il provisioning dell'app

Prima è possibile abilitare il Sandbox dell'App, è necessario eseguire il provisioning e firmare l'applicazione Xamarin.Mac.

Consente di eseguire le operazioni seguenti:

1. Accedere al portale per sviluppatori di Apple: 

    [![Registrazione al portale per sviluppatori Apple](sandboxing-images/sign01.png "registrazione al portale per sviluppatori di Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Selezionare **certificati, profili & identificatori**: 

    [![Selezione di certificati, identificatori e profili](sandboxing-images/sign02.png "Selezione di certificati, identificatori e profili")](sandboxing-images/sign02-large.png#lightbox)
3. In **App Mac**selezionare **identificatori**: 

    [![Selezione di identificatori](sandboxing-images/sign03.png "selezionando gli identificatori")](sandboxing-images/sign03-large.png#lightbox)
4. Creare un nuovo ID per l'applicazione: 

    [![Creazione di un nuovo ID di App](sandboxing-images/sign04.png "la creazione di un nuovo ID di App")](sandboxing-images/sign04-large.png#lightbox)
5. In **i profili di Provisioning**selezionare **sviluppo**: 

    [![Selezione di sviluppo](sandboxing-images/sign05.png "selezionando sviluppo")](sandboxing-images/sign05-large.png#lightbox)
6. Creare un nuovo profilo e selezionare **lo sviluppo di App Mac**: 

    [![Creazione di un nuovo profilo](sandboxing-images/sign06.png "la creazione di un nuovo profilo")](sandboxing-images/sign06-large.png#lightbox)
7. Selezionare l'ID dell'App creati in precedenza: 

    [![Selezionare l'ID App](sandboxing-images/sign07.png "selezionando l'ID dell'App")](sandboxing-images/sign07-large.png#lightbox)
8. Selezionare gli sviluppatori per questo profilo: 

    [![Gli sviluppatori di aggiungere](sandboxing-images/sign08.png "agli sviluppatori di aggiunta")](sandboxing-images/sign08-large.png#lightbox)
9. Selezionare i computer per questo profilo: 

    [![Selezionare i computer consentiti](sandboxing-images/sign09.png "selezionato i computer consentiti")](sandboxing-images/sign09-large.png#lightbox)
10. Assegnare un nome di profilo: 

    [![Assegnando un nome di profilo](sandboxing-images/sign10.png "assegnando un nome di profilo")](sandboxing-images/sign10-large.png#lightbox)
11. Fare clic su di **eseguita** pulsante.

> [!IMPORTANT]
> In alcuni casi potrebbe essere necessario scaricare il nuovo profilo di Provisioning dal portale per sviluppatori di Apple direttamente e fare doppio clic per l'installazione. Inoltre, si potrebbe essere necessario arrestare e riavviare Visual Studio per Mac affinché sia in grado di accedere al nuovo profilo.

Successivamente, è necessario caricare il nuovo ID App e il profilo nel computer di sviluppo. Di seguito, eseguire le operazioni seguenti:

1. Avviare Xcode e poi selezionare **preferenze** dal **Xcode** menu: 

    ![Modifica di account in Xcode](sandboxing-images/sign11.png "modifica di account in Xcode")
2. Fare clic su di **visualizzare i dettagli...**  pulsante: 

    ![Fare clic sul pulsante Visualizza dettagli](sandboxing-images/sign12.png "facendo clic sul pulsante Visualizza dettagli")
3. Fare clic su di **aggiornamento** pulsante (nell'angolo inferiore sinistro).
4. Fare clic su di **eseguita** pulsante.

Successivamente, è necessario selezionare il nuovo ID App e profilo di Provisioning nel progetto Xamarin.Mac. Di seguito, eseguire le operazioni seguenti:

1. Nel **soluzione riempimento**, fare doppio clic su di **Info. plist** file per aprirlo e modificarlo.
2. Verificare che il **identificatore Bundle** corrisponde l'ID App creato in precedenza (esempio: `com.appracatappra.MacSandbox`): 

    [![Modifica l'identificatore Bundle](sandboxing-images/sign13.png "modifica l'identificatore Bundle")](sandboxing-images/sign13-large.png#lightbox)
3. Successivamente, fare doppio clic sul **Entitlements.plist** file e verificare il nostro **iCloud archivio chiave-valore** e **iCloud contenitori** corrispondono l'ID App creato in precedenza (esempio: `com.appracatappra.MacSandbox`): 

    [![Modifica del file Entitlements.plist](sandboxing-images/sign17.png "la modifica del file Entitlements.plist")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche.
4. Nel **soluzione riempimento**, fare doppio clic sul file di progetto per aprire le relative opzioni per la modifica:  

    ![Editign opzioni della soluzione](sandboxing-images/sign14.png "Editign opzioni della soluzione")
5. Selezionare **Mac firma**, quindi controllare **firmare il pacchetto di applicazione** e **firmare il pacchetto di installazione**. In **profilo di Provisioning**, selezionare quello creato in precedenza: 

    ![L'impostazione del profilo di provisioning](sandboxing-images/sign15.png "l'impostazione del profilo di provisioning")
6. Fare clic su di **eseguita** pulsante.

> [!IMPORTANT]
> Potrebbe essere necessario chiudere e riavviare Visual Studio per Mac eseguire questa operazione in modo che riconosca il nuovo ID dell'App e profilo di Provisioning che è stata installata mediante Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Risoluzione dei problemi di provisioning

A questo punto è consigliabile eseguire l'applicazione e assicurarsi che tutto ciò che viene firmato e configurato in modo corretto. Se l'app continui a essere eseguita come prima, tutto ciò che è valido. In caso di errore, è possibile che venga visualizzato una finestra di dialogo simile a quello seguente:

[![Un esempio di finestra di dialogo problema di provisioning](sandboxing-images/sign16.png "un esempio di finestra di dialogo problema di provisioning")](sandboxing-images/sign16-large.png#lightbox)

Ecco le cause più comuni di provisioning e la firma di problemi:

- L'ID Bundle dell'App non corrisponde all'ID di App del profilo selezionato.
- L'ID per sviluppatori non corrisponde all'ID di sviluppatore del profilo selezionato.
- L'UUID del Mac testato in non è registrata come parte del profilo selezionato.

Nel caso di un problema, correggere il problema sul portale per sviluppatori di Apple, aggiornare i profili in Xcode ed eseguire una compilazione pulita in Visual Studio per Mac.

### <a name="enable-the-app-sandbox"></a>Abilitare il Sandbox dell'App

Per attivare Sandbox dell'App, selezionando la casella di controllo nelle opzioni di progetti. Seguire questa procedura:

1. Nel **soluzione riempimento**, fare doppio clic su di **Entitlements.plist** file per aprirlo e modificarlo.
2. Controllare gli **abilitare i diritti** e **abilitare Sandboxing App**: 

    [![Modifica i diritti e l'abilitazione di sandboxing](sandboxing-images/sign17.png "i diritti di modifica e l'abilitazione di sandboxing")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche.

A questo punto, è stata attivata la Sandbox di App, ma non è stato fornito l'accesso di rete necessarie per la visualizzazione Web. Se si esegue ora l'applicazione, è necessario ottenere una finestra vuota:

[![Visualizzazione stato bloccato l'accesso a Internet](sandboxing-images/sample08.png "visualizzazione stato bloccato l'accesso a Internet")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Verifica che l'applicazione è in modalità sandbox

A parte la risorsa di blocco, sono disponibili tre modi principali per stabilire se un'applicazione Xamarin.Mac è stato correttamente creato mediante sandbox:

1. In Finder, verificare i contenuti del `~/Library/Containers/` cartella - se l'applicazione è in modalità sandbox, sarà presente una cartella denominata come identificatore Bundle dell'app (esempio: `com.appracatappra.MacSandbox`): 

    [![Aprire il bundle dell'app](sandboxing-images/sample09.png "apertura bundle dell'app")](sandboxing-images/sample09-large.png#lightbox)
2. Il sistema visualizza l'app come creata mediante sandbox in Monitoraggio attività:
    - Avviare Monitoraggio attività (in `/Applications/Utilities`). 
    - Scegliere **vista** > **colonne** e assicurarsi che il **Sandbox** voce di menu selezionata.
    - Verificare che la colonna Sandbox legge `Yes` per l'applicazione: 

    [![L'app in Monitoraggio attività di verifica](sandboxing-images/sample10.png "l'app in Monitoraggio attività di controllo")](sandboxing-images/sample10-large.png#lightbox)
3. Verificare che l'applicazione binaria è in modalità sandbox:
    - Avviare l'app Terminal.
    - Passare alle applicazioni `bin` directory.
    - Eseguire questo comando: `codesign -dvvv --entitlements :- executable_path` (dove `executable_path` è il percorso all'applicazione): 

    [![Verifica l'app nella riga di comando](sandboxing-images/sample11.png "verifica l'app nella riga di comando")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Debug di un'app creata mediante sandbox

Il debugger si connette alle App Xamarin.Mac tramite TCP, il che significa che per impostazione predefinita quando si abilita il sandboxing, non è in grado di connettersi all'app, pertanto se si tenta di eseguire l'app senza le autorizzazioni appropriate abilitate, viene visualizzato un errore *"Impossibile connettersi a il debugger"*. 

[![Impostare le opzioni necessarie](sandboxing-images/debug01.png "impostando le opzioni necessarie")](sandboxing-images/debug01-large.png#lightbox)

Il **consentire connessioni in uscita rete (Client)** autorizzazione è necessaria per il debugger, l'abilitazione di questo consente il debug normalmente. Poiché non è possibile eseguire il debug senza di esso, è stata aggiornata la `CompileEntitlements` di destinazione per `msbuild` per aggiungere automaticamente l'autorizzazione per i diritti per qualsiasi applicazione in cui è in modalità sandbox per il debug solo le compilazioni. Build di rilascio devono usare i diritti specificati nel file dei diritti, senza modificato.

### <a name="resolving-an-app-sandbox-violation"></a>Risoluzione di una violazione della Sandbox di App

Se un Xamarin.Mac creata mediante sandbox applicazione ha tentato di accedere a una risorsa che ha non consentire esplicitamente, si verifica una violazione di Sandbox di App. Ad esempio, la visualizzazione Web non è più in grado di visualizzare il sito Web di Apple.

L'origine più comune di violazioni di Sandbox App si verifica quando le impostazioni di diritti specificate in Visual Studio per Mac non soddisfano i requisiti dell'applicazione. Nuovamente, eseguire il backup per questo esempio, la connessione di rete mancante dei diritti che consentono di mantenere la visualizzazione Web da in corso.

#### <a name="discovering-app-sandbox-violations"></a>Individuazione di violazioni di App Sandbox

Se si ritiene che si verifica una violazione della Sandbox App Xamarin.Mac nell'applicazione in uso, il modo più rapido per individuare il problema è usando il **Console** app.

Seguire questa procedura:

1. Compilare l'app in questione ed eseguirlo da Visual Studio per Mac.
2. Aprire il **Console** applicazione (da `/Applications/Utilties/`).
3. Selezionare **tutti i messaggi** nella barra laterale e immettere `sandbox` nella ricerca: 

    [![Un esempio di un problema di sandboxing nella console di](sandboxing-images/resolve01.png "un esempio di un problema di sandboxing nella console di")](sandboxing-images/resolve01-large.png#lightbox)

Per l'applicazione di esempio precedente, è possibile vedere che sta bloccando il Kernal il `network-outbound` traffico a causa della Sandbox di App, poiché non è stato richiesto tale diritto.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Correzione di App Sandbox violazioni ai diritti

Ora che abbiamo visto come individuare le violazioni di Sandboxing App, di seguito viene illustrato come può essere risolto modificando i diritti dell'applicazione.

Seguire questa procedura:

1. Nel **soluzione riempimento**, fare doppio clic su di **Entitlements.plist** file per aprirlo e modificarlo.
2. Nel **diritti** sezione controllo di **consentire connessioni in uscita rete (Client)** casella di controllo: 

    [![Modifica i diritti](sandboxing-images/sign17.png "i diritti di modifica")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche all'applicazione.

Se è eseguire le operazioni precedenti per l'applicazione di esempio, quindi compilare ed eseguirlo, il contenuto web a questo punto verrà visualizzato come previsto.

## <a name="the-app-sandbox-in-depth"></a>La App Sandbox approfondita

I meccanismi di controllo di accesso forniti dalla App Sandbox sono pochi e semplici da comprendere. Tuttavia, App Sandbox verranno adottate da ciascuna applicazione consiste in base ai requisiti dell'app e univoci.

Dato lo sforzo per proteggere l'applicazione Xamarin.Mac da sfruttata da codice dannoso, è necessario solo una singola vulnerabilità nell'applicazione di (o una delle librerie o Framework che utilizza) per ottenere il controllo delle interazioni dell'app con il System.

La Sandbox di App è stata progettata per impedire l'acquisizione (o limitare il danno che può causare), consentendo di specificare le interazioni previsto dell'applicazione con il sistema. Il sistema consente solo l'accesso per le risorse necessarie per svolgere il lavoro dell'applicazione e nient'altro.

Quando si progetta la Sandbox di App, è possibile che si sta progettando per uno scenario peggiore. Se l'applicazione viene compromesso da codice dannoso, è limitato per l'accesso solo i file e le risorse nell'ambiente sandbox dell'app.

### <a name="entitlements-and-system-resource-access"></a>Accesso alle risorse di sistema e i diritti

Come abbiamo visto sopra, un'applicazione Xamarin.Mac che non è stata creata mediante sandbox è concedere i diritti completi e dell'utente che esegue l'app. Se compromesso da malware, un'app non protette può fungere da un agente per un comportamento dannoso, con un'ampia comprese potenziali per dannose.

Abilitando la Sandbox di App, rimuovere tutto tranne un set minimo di privilegi, viene quindi riabilitare una necessità solo su uso dei diritti dell'app Xamarin.Mac. 

Modificare le risorse dell'applicazione App Sandbox modificando il relativo **Entitlements.plist** file e selezionando o selezionando i diritti richiesti dalle caselle a discesa Editor:

[![Modifica i diritti](sandboxing-images/sign17.png "i diritti di modifica")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>La directory contenitore e l'accesso al file system

Quando l'applicazione Xamarin.Mac adotta Sandbox dell'App, ha accesso nelle posizioni seguenti:

- **La Directory contenitore App** -alla prima esecuzione, il sistema operativo viene creata una speciale _Directory contenitore_ qualora tutte le relative risorse, solo che possono accedere. L'app avrà accesso in lettura/scrittura a questa directory.
- **Directory dell'App gruppo contenitore** -l'app può essere concesso l'accesso a uno o più _contenitori gruppo_ che sono condivisi tra le applicazioni nello stesso gruppo.
- **File specificato dall'utente** -l'applicazione ottiene automaticamente l'accesso ai file che sono in modo esplicito aperto o trascinati e rilasciati sull'applicazione dall'utente.
- **Elementi correlati** -con i diritti appropriati, l'applicazione può avere accesso a un file con lo stesso nome ma con un'estensione diversa. Ad esempio, un documento che è stato salvato sia come un `.txt` file e un `.pdf`.
- **Le directory temporanee, lo strumento da riga di comando directory e posizioni specifiche di world leggibile** -app con vari livelli di accesso ai file in altri percorsi ben definiti come specificato dal sistema.

#### <a name="the-app-container-directory"></a>La directory contenitore di app

Directory di contenitore di App dell'applicazione un Xamarin.Mac presenta le caratteristiche seguenti:

- È in un percorso nascosto nella directory principale dell'utente (in genere `~Library/Containers`) ed è possibile accedervi con la `NSHomeDirectory` funzione (vedere sotto) all'interno dell'applicazione. Poiché è nella directory Home, ogni utente otterrà i propri contenitore per l'app.
- L'app è illimitato accesso in lettura/scrittura alla Directory di contenitore e tutte le sottodirectory e file in esso contenuti.
- La maggior parte delle API di ricerca percorso del macOS sono rispetto al contenitore dell'app. Ad esempio, il contenitore avrà un proprio **libreria** (accessibili tramite `NSLibraryDirectory`), **il supporto dell'applicazione** e **preferenze** nelle sottodirectory.
- macOS stabilisce e applica la connessione tra e app e il relativo contenitore tramite la firma del codice. È anche un altro app eseguito un tentativo di effettuare lo spoofing dell'app usando il relativo **identificatore Bundle**, non sarà in grado di accedere al contenitore a causa di firma codice.
- Il contenitore non è disponibile per i file generati dall'utente. È invece per i file utilizzati dall'applicazione, ad esempio database, cache o altri tipi di dati specifici.
- Per _raccolta immagini_ tipi di applicazioni (ad esempio di app di foto di Apple), il contenuto dell'utente verrà inserito il contenitore.

> [!IMPORTANT]
> Sfortunatamente, Xamarin.Mac non hanno 100% API coverage ancora (a differenza di xamarin. IOS), di conseguenza il `NSHomeDirectory` API non è stata mappata nella versione corrente di Xamarin.Mac.

Come soluzione alternativa temporanea, è possibile utilizzare il codice seguente:

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>La directory contenitore gruppo di app

A partire da macOS Mac 10.7.5 (e successive), un'applicazione può utilizzare il `com.apple.security.application-groups` il diritto di accedere a un contenitore condiviso che è comune a tutte le applicazioni nel gruppo. È possibile utilizzare questo contenitore condiviso per il contenuto di affiancate non utente, ad esempio database o altri tipi di file di supporto (ad esempio, le cache).

I contenitori di gruppo vengono aggiunti automaticamente al contenitore Sandbox ogni dell'app (se fanno parte di un gruppo) e sono archiviati `~/Library/Group Containers/<application-group-id>`. L'ID del gruppo _deve_ iniziano con l'ID del Team di sviluppo e un periodo, ad esempio:

```plist
<team-id>.com.company.<group-name>
```

Per ulteriori informazioni, vedere Apple [aggiunta di un'applicazione a un gruppo di applicazioni](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) in [il diritto di riferimento sulle chiavi](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Accesso Powerbox e file di sistema di fuori del contenitore di app

Un'applicazione Xamarin.Mac creata mediante sandbox possa accedere a percorsi del file system all'esterno del relativo contenitore nei modi seguenti:

- Direzione specifiche dell'utente (tramite Apri e salva finestre di dialogo o altri metodi, ad esempio trascinamento della selezione).
- Con i diritti per i percorsi di file specifico del sistema (ad esempio `/bin` o `/usr/lib`).
* Quando il percorso del file system è in determinate directory che sono world leggibile (ad esempio condivisione).

_Powerbox_ è il macOS tecnologia di sicurezza che interagisce con l'utente per espandere i diritti di accesso file dell'app Xamarin.Mac creata mediante sandbox. Powerbox non dispone di API, ma viene attivato in modo trasparente quando l'app chiama un `NSOpenPanel` o `NSSavePanel`. Tramite i diritti che è impostato per l'applicazione Xamarin.Mac Powerbox accedere.

Quando un'app creata mediante sandbox Visualizza Open o finestra di dialogo Salva, la finestra è presentata da Powerbox (e non AppKit) e pertanto dispone di accesso a file o directory in cui l'utente ha accesso a.

Quando un utente seleziona una directory o file da aprire o salvare una finestra di dialogo (o trascina uno sull'icona dell'applicazione), Powerbox aggiunge il percorso associato alla sandbox dell'app.

Inoltre, il sistema consenta automaticamente le informazioni seguenti per un'app creata mediante sandbox:

- Connessione a un metodo di input del sistema.
- Chiamare i servizi selezionati dall'utente dal **servizi** menu (solo per i servizi contrassegnati come _sicuri per le app sandbox_ dal provider di servizi).
- Scegliere i file aperti dall'utente di **Apri recente** menu.
- Usare la copia e incolla tra le altre applicazioni.
- Lettura di file da percorsi world leggibile seguenti:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Leggere e scrivere file nelle directory create da `NSTemporaryDirectory`.

Per impostazione predefinita, i file aperta o salvata in un'app Xamarin.Mac creata mediante sandbox per rimangano accessibili finché non termina con l'app (a meno che il file era aperto quando si chiude l'app). File aperti verranno automaticamente ripristinati sandbox dell'applicazione tramite la funzionalità di ripristino macOS al successivo che avvio dell'app.

Per garantire la persistenza per i file che si trovano all'esterno di contenitore dell'App un Xamarin.Mac, utilizzare i segnalibri con ambito di protezione (vedere sotto).

#### <a name="related-items"></a>Elementi correlati

La App Sandbox consente a un'app accedere a elementi correlati che presentano lo stesso nome di file, ma estensioni diverse. La funzionalità dispone di due parti: a) un elenco di estensioni correlate nell'app `Info.plst` file, il codice b) per indicare la sandbox l'app verrà essere svolte con questi file.

Esistono due scenari in cui questo comportamento è normale:

1. L'app deve essere in grado di salvare una versione diversa del file (con una nuova estensione). Ad esempio, l'esportazione di un `.txt` file in un `.pdf` file. Per gestire questa situazione, è necessario utilizzare un `NSFileCoordinator` per accedere al file. Verrà chiamato il `WillMove(fromURL, toURL)` metodo prima di tutto, spostare il file di estensione per il nuovo e quindi chiamare `ItemMoved(fromURL, toURL)`.
2. L'applicazione deve aprire un file principale con un'estensione e diversi file di supporto con estensioni diverse. Ad esempio, un film e un file di sottotitoli. Utilizzare un `NSFilePresenter` per ottenere l'accesso al file secondario. Fornire il file principale per il `PrimaryPresentedItemURL` proprietà e il file secondario per il `PresentedItemURL` proprietà. Quando viene aperto il file principale, chiamare il `AddFilePresenter` metodo la `NSFileCoordinator` classe per registrare il file secondario.

In del entrambi gli scenari, l'app **Info. plist** file necessario dichiarare i tipi di documento che è possibile aprire l'app. Per qualsiasi tipo di file, aggiungere il `NSIsRelatedItemType` (con un valore di `YES`) alla relativa voce nel `CFBundleDocumentTypes` matrice.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Aprire e salvare il comportamento di finestra di dialogo con le app create mediante sandbox

I seguenti limiti vengono posizionati nel `NSOpenPanel` e `NSSavePanel` momento della chiamata da un'app Xamarin.Mac creata mediante sandbox:

- Non è possibile richiamare a livello di codice il **OK** pulsante.
- A livello di codice non è possibile modificare la selezione di un utente in un `NSOpenSavePanelDelegate`.

Inoltre, le seguenti modifiche di ereditarietà sono presenti:

- **App non creata mediante sandbox** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>I segnalibri con ambito di protezione e accesso alle risorse permanente

Come indicato in precedenza, un'applicazione creata mediante sandbox Xamarin.Mac può accedere a un file o una risorsa all'esterno del relativo contenitore tramite l'interazione diretta dell'utente (come fornita dal PowerBox). Tuttavia, accesso a queste risorse non automaticamente mantenere avvia app o di sistema viene riavviato.

Utilizzando _Security-Scoped segnalibri_, un'applicazione creata mediante sandbox Xamarin.Mac possibile mantenere l'intenzione dell'utente e mantenere l'accesso specificato risorse dopo un'app riavviare.

#### <a name="security-scoped-bookmark-types"></a>Tipi di sicurezza con ambito di segnalibro

Quando si utilizzano i segnalibri Security-Scoped e accesso alle risorse permanente, esistono due casi sistine:

- **Un segnalibro App-Scoped fornisce l'accesso permanente a una cartella o il file specificato dall'utente.** 

    Se, ad esempio, consente l'utilizzo di aprire un documento esterno per la modifica dell'applicazione Xamarin.Mac sandbox (utilizzando un `NSOpenPanel`), l'app è possibile creare un segnalibro App-Scoped in modo che possa accedere nuovamente in futuro lo stesso file.
- **Un segnalibro Document-Scoped fornisce l'accesso permanente un documento specifico in un file secondario.** 

Ad esempio, un'applicazione modifica video che crea un file di progetto che ha accesso alle singole immagini, video clip e i file audio in un secondo momento verranno combinati in un unico filmato.

Quando l'utente importa un file di risorse nel progetto (tramite un `NSOpenPanel`), l'applicazione crea un segnalibro Document-Scoped per l'elemento che viene archiviato nel progetto, in modo che il file sia sempre accessibile all'app.

Un segnalibro Document-Scoped possono essere risolti da qualsiasi applicazione che è possibile aprire i dati di segnalibro e il documento stesso. Supporta la portabilità, consentendo all'utente di inviare i file di progetto a un altro utente e che tutti i segnalibri funzionano anche per tali.

> [!IMPORTANT]
> Un segnalibro Document-Scoped può _solo_ puntare a un singolo file e non una cartella e il file non può trovarsi in una posizione utilizzata dal sistema (ad esempio `/private` o `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Utilizzo di segnalibri con ambito di protezione

Utilizzando dei tipi di Security-Scoped segnalibro, è necessario eseguire la procedura seguente:

1. **Impostare i diritti appropriati nell'app Xamarin.Mac che richiede l'utilizzo di segnalibri Security-Scoped** -For App-Scoped segnalibri, impostare il `com.apple.security.files.bookmarks.app-scope` chiave diritto `true`. Per i segnalibri Document-Scoped, impostare il `com.apple.security.files.bookmarks.document-scope` chiave diritto `true`.
2. **Creare un segnalibro Security-Scoped** -questo scopo si per qualsiasi file o cartella in cui l'utente ha fornito l'accesso a (tramite `NSOpenPanel` ad esempio), che l'app dovrà accedere permanente per. Utilizzare il `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` metodo la `NSUrl` classe per creare il segnalibro.
3. **Risolvere il segnalibro Security-Scoped** : quando l'applicazione deve accedere nuovamente alla risorsa (ad esempio, dopo il riavvio) è necessario risolvere il segnalibro a un URL con ambito di protezione. Utilizzare il `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` metodo la `NSUrl` classe per risolvere il segnalibro.
4. **Notificare in modo esplicito il sistema che si desidera l'accesso al file dall'URL Security-Scoped** : questo passaggio deve essere eseguita subito dopo l'ottenimento dell'URL Security-Scoped precedente o quando si desidera successivamente riottenere l'accesso alla risorsa dopo averlo comunque rilasciata non di accesso. Chiamare il `StartAccessingSecurityScopedResource ()` metodo la `NSUrl` classe per avviare l'accesso a un URL Security-Scoped.
5. **Notificare in modo esplicito il sistema che aver completato l'accesso al file dall'URL Security-Scoped** -appena possibile, è necessario informare il sistema quando l'app non è più necessario l'accesso al file (ad esempio, se l'utente chiude). Chiamare il `StopAccessingSecurityScopedResource ()` metodo il `NSUrl` alla classe di interrompere l'accesso a un URL Security-Scoped.

Dopo che si avrà accesso a una risorsa, è necessario tornare al passaggio 4 per stabilire nuovamente l'accesso. Se l'app Xamarin.Mac viene riavviato, è necessario tornare al passaggio 3 e risolvere il segnalibro.

> [!IMPORTANT]
> Tentativo di rilasciare l'accesso alle risorse URL Security-Scoped causerà un'app Xamarin.Mac a perdite di risorse del Kernel. Di conseguenza, l'app non saranno in grado di aggiungere i percorsi del file system al contenitore finché viene riavviato.

### <a name="the-app-sandbox-and-code-signing"></a>La Sandbox di App e la firma del codice

Dopo aver abilitato la Sandbox di App e abilitare i requisiti specifici per l'app Xamarin.Mac (tramite diritti), è necessario codificare firmare il progetto per il sandboxing diventino effettive. È necessario eseguire poiché i diritti necessari per il Sandboxing App sono collegati alla firma dell'app di firma del codice. 

macOS impone un collegamento tra contenitore un'applicazione e la firma di codice, in questo modo di che altre applicazioni non possono accedere tale contenitore, anche se esegue lo spoofing App ID pacchetto. Questo meccanismo funziona nel modo seguente:

1. Quando il sistema crea il contenitore dell'app, imposta un _elenco di controllo di accesso_ (ACL) in tale contenitore. La voce di controllo di accesso iniziale nell'elenco contiene l'app _designato requisito_ (ripristino di emergenza), che descrive come future versioni dell'app può essere riconosciuta (quando è stato aggiornato).
2. Ogni volta che viene avviata un'app con lo stesso ID Bundle, il sistema verifica che firma codice dell'applicazione corrispondente ai requisiti di designato specificato in una delle voci ACL del contenitore. Se il sistema non trova una corrispondenza, si impedisce l'avvio di app.

Il codice funziona firma modi seguenti:

1. Prima di creare il progetto Xamarin.Mac, ottenere un certificato di sviluppo, un certificato di distribuzione e un certificato di ID dello sviluppatore dal portale per sviluppatori di Apple.
2. Quando si distribuisce l'app Xamarin.Mac il Mac App Store, viene firmato con una firma di codice di Apple.

Durante il test e debug, sarà possibile usare una versione dell'applicazione Xamarin.Mac è firmato (che verrà utilizzata per creare il contenitore di App). In un secondo momento, se si desidera testare o installare la versione dall'App Store di Apple, verrà firmato con la firma di Apple e non verrà avviata (perché non è presente la stessa firma del codice del contenitore di App originale). In questo caso, si otterrà una segnalazione di arresto anomalo simile al seguente:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Per risolvere questo problema, è necessario modificare la voce ACL in modo da puntare alla versione Apple firmata dell'app.

Per ulteriori informazioni sulla creazione e download necessari per il Sandboxing i profili di Provisioning, vedere il [firma e il Provisioning dell'App](#Signing_and_Provisioning_the_App) sezione precedente.

#### <a name="adjusting-the-acl-entry"></a>Modificare la voce ACL

Per consentire la versione firmata Apple app Xamarin.Mac per l'esecuzione, eseguire le operazioni seguenti:

1. Aprire l'app Terminal (in `/Applications/Utilities`).
2. Aprire una finestra di ricerca per la versione firmata Apple app Xamarin.Mac.
3. Tipo `asctl container acl add -file ` nella finestra del terminale.
4. Trascinare l'icona dell'app Xamarin.Mac dalla finestra di ricerca nella finestra del terminale.
5. Il percorso completo del file verrà essere aggiunto al comando nella finestra Terminal.
6. Premere **invio** per eseguire il comando.

ACL del contenitore contiene ora i requisiti del codice designato per entrambe le versioni dell'app Xamarin.Mac e macOS sarà consentito eseguire entrambe le versioni.

#### <a name="display-a-list-of-acl-code-requirements"></a>Visualizzare un elenco di requisiti del codice ACL

È possibile visualizzare un elenco dei requisiti di codice in ACL un contenitore effettuando le seguenti operazioni:

1. Aprire l'app Terminal (in `/Applications/Utilities`).
2. Digitare `asctl container acl list -bundle <container-name>`.
3. Premere **invio** per eseguire il comando.

Il `<container-name>` è in genere l'identificatore Bundle per l'applicazione Xamarin.Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Progettazione di un'applicazione Xamarin.Mac per la Sandbox di App

È un flusso di lavoro comune da seguire quando si progetta un'applicazione Xamarin.Mac per la Sandbox di App. Ciò premesso, le specifiche dell'implementazione di sandboxing in un'applicazione sta per essere univoco per la funzionalità dell'applicazione specificata.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Sei passaggi per l'adozione della Sandbox di App

Progettazione di un'applicazione di Xamarin.Mac per la Sandbox di App in genere costituito dai passaggi seguenti:

1. Determinare se l'app è appropriato per il sandboxing.
2. Progettare una strategia di sviluppo e distribuzione.
3. Risolvere eventuali incompatibilità dell'API.
4. Applicare i diritti di Sandbox App necessari al progetto Xamarin.Mac.
5. Aggiungere la separazione dei privilegi utilizzando XPC.
6. Implementare una strategia di migrazione.

> [!IMPORTANT]
> È necessario non solo sandbox l'eseguibile principale si bundle dell'app, ma anche ogni helper incluso app o lo strumento disponibile in tale bundle. Questo è obbligatorio per tutte le app distribuite da Mac App Store e, se possibile, è necessario eseguire per qualsiasi altro tipo di distribuzione di app.

Per un elenco di tutti i file binari eseguibili nel bundle dell'app un Xamarin.Mac, digitare il comando seguente in Terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Dove `[Your-App-Bundle]` è il nome e percorso del pacchetto dell'applicazione.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinare se un'app Xamarin.Mac è adatta per il sandboxing

La maggior parte delle App Xamarin.Mac sono completamente compatibili con la Sandbox di App e di conseguenza, adatto per il sandboxing. Se l'applicazione richiede un comportamento che non consente la Sandbox di App, è consigliabile un approccio alternativo.

Se l'app richiede una delle seguenti situazioni, non è compatibile con l'ambiente App Sandbox:

- **Servizi di autorizzazione** -con Sandbox App, è possibile utilizzare con le funzioni descritte in [di riferimento per C servizi autorizzazione](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **Accessibilità API** -non è possibile sandbox le App per l'accesso facilitato, ad esempio gli screen reader o controllare altre applicazioni.
- **Inviare eventi Apple App arbitrario** -se l'applicazione richiede l'invio di eventi di Apple a un'app sconosciuta, arbitraria, non può essere creata mediante sandbox. Per un elenco noto di App chiamato, l'applicazione può ancora essere creata mediante sandbox e i diritti necessario includere l'elenco di app chiamato.
- **Invia informazioni dizionari nelle notifiche distribuite ad altre attività** -con Sandbox App, è possibile includere un `userInfo` dizionario durante la registrazione per un `NSDistributedNotificationCenter` oggetto per altre attività di messaggistica.
- **Caricare le estensioni del Kernel** -il caricamento di estensioni del Kernel non è consentito dalla Sandbox dell'App.
- **Simulazione dell'Input dell'utente nelle finestre di dialogo Salva e Apri** : a livello di codice la modifica di aprire o salvare le finestre di dialogo per simulare o modificare l'input dell'utente non è consentito dalla Sandbox dell'App.
- **Accesso o impostazione delle preferenze in altre app** -modifica le impostazioni delle altre App non è consentita dalla Sandbox dell'App.
- **Configurazione delle impostazioni di rete** -modifica le impostazioni di rete non è consentita dalla Sandbox dell'App.
- **Chiusura di altre app** -alla Sandbox di App non consente l'utilizzo `NSRunningApplication` per terminare le altre app.

### <a name="resolving-api-incompatibilities"></a>Risoluzione dei problemi di incompatibilità API

Quando si progetta un'applicazione Xamarin.Mac Sandbox dell'App, è possibile riscontrare problemi di incompatibilità con l'utilizzo di alcune API macOS.

Ecco alcuni problemi e operazioni che è possibile eseguire per risolverli comuni:

- **Apertura, salvataggio e rilevamento documenti** : se si siano gestendo i documenti utilizzando qualsiasi tecnologia diverso da `NSDocument`, è necessario passare a esso a causa del supporto incorporato per la Sandbox di App. `NSDocument` funziona con PowerBox e fornisce il supporto per la conservazione dei documenti all'interno del sandbox se l'utente li sposta in Finder automaticamente.
- **Mantenere l'accesso al File di risorse di sistema** : se il Xamarin.Mac applicazione dipende dal permanente accesso alle risorse all'esterno del relativo contenitore, utilizzare i segnalibri con ambito di protezione per mantenere l'accesso.
- **Creare un elemento di account di accesso per un'App** -con Sandbox App, è possibile creare un elemento tramite l'account di accesso `LSSharedFileList` né è possibile modificare lo stato dei servizi di avvio utilizzando `LSRegisterURL`. Utilizzare il `SMLoginItemSetEnabled` funzionano come descritto in mele [aggiunta di elementi di account di accesso con il Framework di gestione del servizio](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentazione.
- **Accesso ai dati utente** : se si utilizza, ad esempio funzioni POSIX `getpwuid` per ottenere una home directory dell'utente da servizi di directory, è consigliabile utilizzare i simboli Cocoa o di base, ad esempio `NSHomeDirectory`.
- **Accesso alle preferenze di altre app** - perché App Sandbox indirizza il percorso per individuare l'API al contenitore dell'app, Modifica preferenze avviene all'interno del contenitore e l'accesso a un altro preferenze di App in non è consentito. 
- **Utilizzando i Video incorporati HTML5 nelle visualizzazioni Web** -se l'app Xamarin.Mac Usa WebKit per riprodurre il video HTML5 incorporati, è necessario anche collegare l'app su framework AV Foundation. La App Sandbox impedirà CoreMedia play in questi video in caso contrario.

### <a name="applying-required-app-sandbox-entitlements"></a>L'applicazione dei diritti App Sandbox necessari

È necessario modificare i diritti per qualsiasi applicazione Xamarin.Mac che si desidera eseguire l'ambiente sandbox App e controllare il **abilitare Sandboxing App** casella di controllo.

Basato sulle funzionalità dell'app, potrebbe essere necessario abilitare altri diritti per accedere a funzionalità del sistema operativo o risorse. App Sandboxing offrono risultati ottimali quando si riduce i diritti di cui che richiesta per il livello minimo richiesto per eseguire l'app in modo casuale solo abilitare i diritti.

Per determinare quali diritti richiede un'app Xamarin.Mac, eseguire le operazioni seguenti:

1. Abilitare il Sandbox dell'App ed eseguire l'app Xamarin.Mac.
2. Eseguire mediante le funzionalità dell'App.
3. Aprire l'applicazione Console (disponibile in `/Applications/Utilities`) e cercare `sandboxd` le violazioni di **tutti i messaggi** log.
4. Per ogni `sandboxd` violazione, risolvere il problema utilizzando il contenitore di app anziché altri percorsi del file system o applicare i diritti di Sandbox di App per consentire l'accesso alle funzionalità del sistema operativo con restrizioni.
5. Eseguire di nuovo e provare di nuovo tutte le funzionalità di app Xamarin.Mac.
6. Ripetere l'operazione finché tutti `sandboxd` violazioni sono stati risolte.

### <a name="add-privilege-separation-using-xpc"></a>Aggiungere la separazione dei privilegi utilizzando XPC

Quando si sviluppa un'app Xamarin.Mac per la Sandbox di App, esaminare i comportamenti dell'applicazione nei termini di privilegi e l'accesso, quindi scegliere di separare le operazioni ad alto rischio nei propri servizi XPC.

Per ulteriori informazioni, vedere Apple [creazione di servizi XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) e [daemon e i servizi di Guida per programmatori](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implementare una strategia di migrazione

Se si rilascia una nuova versione in modalità sandbox di un'applicazione Xamarin.Mac che non era precedentemente in modalità sandbox, è necessario garantire che gli utenti dispongano di un percorso di aggiornamento smooth. 

 Per informazioni dettagliate su come implementare un manifesto di migrazione di contenitore, leggere Apple [la migrazione di un'applicazione a una Sandbox](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentazione.

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato a un'analisi approfondita sandboxing un'applicazione Xamarin.Mac. In primo luogo, si ha creato un'applicazione di Xamarin.Mac semplicemente per visualizzare le nozioni di base della Sandbox dell'App. Successivamente, è stato illustrato come risolvere le violazioni di sandbox. Quindi, abbiamo presentato un'analisi approfondita esaminerà Sandbox dell'App e, infine, abbiamo esaminato progettazione di un'applicazione Xamarin.Mac per la Sandbox di App.



## <a name="related-links"></a>Collegamenti correlati

- [Pubblicazione nell'App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Sulle App Sandbox](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemi noti di Sandboxing App](https://developer.apple.com/library/content/qa/qa1773/_index.html)
