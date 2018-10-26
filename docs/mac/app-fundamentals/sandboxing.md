---
title: Sandboxing un'app xamarin. Mac
description: Questo articolo illustra il sandboxing un'applicazione xamarin. Mac di rilascio di App Store. Viene descritto come tutti gli elementi che costituiscono il sandboxing, ad esempio directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni l'imposizione del kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6bf2f63e944e178d80f76fe363ef24410ff052ce
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123778"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Sandboxing un'app xamarin. Mac

_Questo articolo illustra il sandboxing un'applicazione xamarin. Mac di rilascio di App Store. Viene descritto come tutti gli elementi che costituiscono il sandboxing, ad esempio directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni l'imposizione del kernel._

## <a name="overview"></a>Panoramica

Quando si usa c# e .NET in un'applicazione xamarin. Mac, come avviene quando si lavora con Objective-C o Swift si hanno la stessa capacità di un'applicazione sandbox.

[![Un esempio di app in esecuzione](sandboxing-images/intro01.png "un esempio di app in esecuzione")](sandboxing-images/intro01-large.png#lightbox)

In questo articolo, illustreremo le nozioni di base dell'utilizzo di sandboxing in un'applicazione xamarin. Mac e tutti gli elementi che costituiscono il sandboxing: directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni l'imposizione del kernel. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` attributi utilizzato per impostare backup di classi c# per gli oggetti di Objective-C e interfaccia utente di elementi.

## <a name="about-the-app-sandbox"></a>Su Sandbox App

L'App Sandbox offre difesa avanzata contro il danno che può essere causato da un'applicazione dannosa in esecuzione in un computer Mac, limitando l'accesso alle risorse di sistema di un'applicazione.

Un'applicazione non in modalità sandbox disponga dei diritti completi dell'utente che esegue l'app e può accedere o eseguire alcuna operazione che può essere l'utente. Se l'app contiene aree libere di sicurezza (o qualsiasi framework che utilizza), un pirata informatico potenzialmente può sfruttare tali punti deboli e usare l'app per assumere il controllo del Mac che in cui viene eseguito.

Limitando l'accesso alle risorse in base all'applicazione, un'app sandbox offre una linea di difesa contro il furto, danneggiati o dannosi da parte di un'applicazione in esecuzione sul computer dell'utente.

Sandbox dell'App è una tecnologia di controllo di accesso incorporata in macOS (applicata a livello di kernel) che offre una strategia duplice:

1. App Sandbox consente agli sviluppatori di descrivere _come_ un'applicazione interagirà con il sistema operativo e, in questo modo, viene concesso solo i diritti di accesso che sono necessarie per svolgere il lavoro e non è più.
2. App Sandbox consente all'utente di concedere ulteriore accesso al sistema tramite l'apertura e salvare le finestre di dialogo, trascinare e rilasciare operazioni e le interazioni utente comuni, altri senza problemi.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparazione all'implementazione di App Sandbox

Gli elementi della Sandbox dell'App che verranno trattati in dettaglio nell'articolo sono i seguenti:

- Directory contenitore
- Diritti
- Autorizzazioni impostato dall'utente
- Separazione dei privilegi
- Imposizione del kernel

Dopo aver compreso questi dettagli, è possibile creare un piano per l'adozione della Sandbox dell'App nell'applicazione xamarin. Mac.

In primo luogo, è necessario stabilire se l'applicazione è un buon candidato per il sandboxing (la maggior parte delle applicazioni sono). Successivamente, è necessario risolvere eventuali incompatibilità di API e determinare quali elementi di App Sandbox sarà necessaria. Infine, un'analisi usando la separazione dei privilegi per ottimizzare è a livello di difesa a più livelli dell'applicazione.

Ad adottare la Sandbox dell'App, è possibile che alcuni percorsi del file system usata dall'applicazione saranno diversi. In particolare, l'applicazione sarà disponibile una Directory contenitore che verrà usato per i file di supporto dell'applicazione, database, cache e qualsiasi altro file che non sono documenti degli utenti. MacOS sia Xcode forniscono supporto per eseguire la migrazione di questi tipi di file dai percorsi precedenti al contenitore.

## <a name="sandboxing-quick-start"></a>Avvio rapido di sandboxing

In questa sezione si creerà una semplice app xamarin. Mac che usa una visualizzazione Web (che richiede una connessione di rete che è limitata in sandboxing, a meno che specificamente richiesto) come esempio di Guida introduttiva a Sandbox dell'App.

È possibile verificare che l'applicazione viene effettivamente creata mediante sandbox e informazioni su come diagnosticare e risolvere errori comuni di App Sandbox.

### <a name="creating-the-xamarinmac-project"></a>Creazione del progetto xamarin. Mac

È possibile eseguire il comando seguente per creare il progetto di esempio:

1. Avviare Visual Studio per Mac e fare clic su di **nuova soluzione...** account".
2. Dal **nuovo progetto** finestra di dialogo **Mac** > **App** > **App Cocoa**: 

    [![Crea una nuova App Cocoa](sandboxing-images/sample01.png "crea una nuova App Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Fare clic sui **successivo** pulsante, immettere `MacSandbox` il nome del progetto e fare clic sui **crea** pulsante: 

    [![Immettere il nome dell'app](sandboxing-images/sample02.png "immettendo il nome dell'app")](sandboxing-images/sample02-large.png#lightbox)
4. Nel **riquadro della soluzione**, fare doppio clic il **Main. Storyboard** file per aprirlo e modificarlo in Xcode: 

    [![Modifica dello storyboard principale](sandboxing-images/sample03.png "modifica dello storyboard principale")](sandboxing-images/sample03-large.png#lightbox)
5. Trascinare un **visualizzazione Web** sulla finestra, ridimensionarla per riempire l'area del contenuto e impostarla per aumentare e ridurre con la finestra: 

    [![Aggiunta di una visualizzazione web](sandboxing-images/sample04.png "aggiunta di una visualizzazione web")](sandboxing-images/sample04-large.png#lightbox)
6. Creazione di un outlet per la visualizzazione web chiamato `webView`: 

    [![Creazione di un nuovo negozio](sandboxing-images/sample05.png "creando un nuovo Negozio")](sandboxing-images/sample05-large.png#lightbox)
7. Tornare a Visual Studio per Mac e fare doppio clic il **ViewController.cs** del file nei **riquadro della soluzione** per aprirlo e modificarlo.
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

Eseguire l'applicazione si e assicurarsi che il sito Web di Apple viene visualizzato nella finestra come indicato di seguito:

[![Visualizzazione esecuzione di un'app di esempio](sandboxing-images/sample06.png "visualizzazione esecuzione di un'app di esempio")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Firma e provisioning dell'app

Prima è possibile abilitare la Sandbox dell'App, è innanzitutto necessario effettuare il provisioning e firmare l'applicazione xamarin. Mac.

Consente di eseguire le operazioni seguenti:

1. Accedere al portale per sviluppatori di Apple: 

    [![Accedendo al portale Apple Developer](sandboxing-images/sign01.png "accedendo al portale Apple Developer")](sandboxing-images/sign01-large.png#lightbox)
2. Selezionare **certificati, identificatori e profili**: 

    [![Selezione di certificati, identificatori e profili](sandboxing-images/sign02.png "Selezione di certificati, identificatori e profili")](sandboxing-images/sign02-large.png#lightbox)
3. Sotto **App Mac**, selezionare **identificatori**: 

    [![Selezione di identificatori](sandboxing-images/sign03.png "selezionando gli identificatori")](sandboxing-images/sign03-large.png#lightbox)
4. Creare un nuovo ID per l'applicazione: 

    [![Creazione di un nuovo ID App](sandboxing-images/sign04.png "creando un nuovo ID App")](sandboxing-images/sign04-large.png#lightbox)
5. Sotto **profili di Provisioning**, selezionare **sviluppo**: 

    [![Selezione di sviluppo](sandboxing-images/sign05.png "selezionando lo sviluppo")](sandboxing-images/sign05-large.png#lightbox)
6. Creare un nuovo profilo e selezionare **lo sviluppo di App Mac**: 

    [![Creazione di un nuovo profilo](sandboxing-images/sign06.png "creato un nuovo profilo")](sandboxing-images/sign06-large.png#lightbox)
7. Selezionare l'ID App creato in precedenza: 

    [![Selezione dell'ID App](sandboxing-images/sign07.png "selezione dell'ID App")](sandboxing-images/sign07-large.png#lightbox)
8. Selezionare gli sviluppatori per questo profilo: 

    [![Gli sviluppatori di aggiunta](sandboxing-images/sign08.png "agli sviluppatori di aggiunta")](sandboxing-images/sign08-large.png#lightbox)
9. Selezionare i computer per questo profilo: 

    [![Selezione dei computer consentiti](sandboxing-images/sign09.png "selezione dei computer consentiti")](sandboxing-images/sign09-large.png#lightbox)
10. Assegnare un nome di profilo: 

    [![Assegnando un nome di profilo](sandboxing-images/sign10.png "assegnando un nome di profilo")](sandboxing-images/sign10-large.png#lightbox)
11. Fai clic sul pulsante **Fine**.

> [!IMPORTANT]
> In alcuni casi potrebbe essere necessario scaricare direttamente il nuovo profilo di Provisioning dal portale per sviluppatori di Apple e fare doppio clic per installarlo. È anche necessario arrestare e riavviare Visual Studio per Mac prima sarà in grado di accedere di nuovo profilo.

Successivamente, è necessario caricare il nuovo ID App e il profilo nel computer di sviluppo. È possibile eseguire le operazioni seguenti:

1. Avviare Xcode e selezionare **preferenze** dalle **Xcode** menu: 

    ![Modifica di account in Xcode](sandboxing-images/sign11.png "modifica gli account in Xcode")
2. Fare clic su di **visualizzare i dettagli...**  pulsante: 

    ![Fare clic sul pulsante Visualizza dettagli](sandboxing-images/sign12.png "facendo clic sul pulsante Visualizza i dettagli")
3. Fare clic sui **Aggiorna** pulsante (nell'angolo inferiore sinistro).
4. Fai clic sul pulsante **Fine**.

Successivamente, è necessario selezionare il nuovo ID App e il profilo di Provisioning nel progetto xamarin. Mac. È possibile eseguire le operazioni seguenti:

1. Nel **riquadro della soluzione**, fare doppio clic il **Info. plist** file per aprirlo e modificarlo.
2. Verificare che il **Bundle Identifier** corrisponde l'ID App creato in precedenza (esempio: `com.appracatappra.MacSandbox`): 

    [![Modifica l'identificatore del Bundle](sandboxing-images/sign13.png "modifica l'identificatore del Bundle")](sandboxing-images/sign13-large.png#lightbox)
3. Successivamente, fare doppio clic sul **entitlements. plist** del file e verificare che nostro **Store chiave-valore iCloud** e il **contenitori iCloud** tutti corrispondere l'ID App creato in precedenza (esempio: `com.appracatappra.MacSandbox`): 

    [![Modificare il file entitlements. plist](sandboxing-images/sign17.png "modificando il file entitlements. plist")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche.
4. Nel **riquadro della soluzione**, fare doppio clic sul file di progetto per aprire le relative opzioni per la modifica:  

    ![Editign opzioni della soluzione](sandboxing-images/sign14.png "Editign opzioni della soluzione")
5. Selezionare **firma Mac**, quindi controllare **firmare il bundle dell'applicazione** e **firmare il pacchetto di programma di installazione**. Sotto **profilo di Provisioning**, selezionare quello creato in precedenza: 

    ![Impostazione del profilo di provisioning](sandboxing-images/sign15.png "impostando il profilo di provisioning")
6. Fai clic sul pulsante **Fine**.

> [!IMPORTANT]
> Si potrebbe essere necessario chiudere e riavviare Visual Studio per Mac per ottenerlo in modo che riconosca il nuovo ID App e il profilo di Provisioning installato da Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Risoluzione dei problemi di provisioning

A questo punto si deve provare a eseguire l'applicazione e assicurarsi che tutto ciò che viene firmato e il provisioning in modo corretto. Se l'app continui a essere eseguita come indicato in precedenza, è tutto a posto. In caso di errore, si potrebbe ottenere una finestra di dialogo simile a quello seguente:

[![Ad esempio il provisioning di finestra di dialogo di problema](sandboxing-images/sign16.png "riportato un esempio di finestra di dialogo di problema di provisioning")](sandboxing-images/sign16-large.png#lightbox)

Di seguito sono riportate le cause più comuni del provisioning e problemi relativi alla firma:

- L'ID Bundle dell'App non corrisponde all'ID App del profilo selezionato.
- L'ID per gli sviluppatori non corrisponde all'ID per gli sviluppatori del profilo selezionato.
- L'UUID del Mac sottoposto a test in cui non sia registrato come parte del profilo selezionato.

In caso di problema, correggere il problema nel portale Apple Developer, aggiornare i profili in Xcode ed eseguire una compilazione pulita in Visual Studio per Mac.

### <a name="enable-the-app-sandbox"></a>Abilitare la Sandbox dell'App

Abilitare la Sandbox dell'App selezionando una casella di controllo nelle opzioni di progetti. Seguire questa procedura:

1. Nel **riquadro della soluzione**, fare doppio clic il **entitlements. plist** file per aprirlo e modificarlo.
2. Controllare entrambi **abilitare i diritti** e **abilitare App Sandboxing**: 

    [![Modifica i diritti e l'abilitazione di sandboxing](sandboxing-images/sign17.png "i diritti di modifica e l'abilitazione di sandboxing")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche.

A questo punto, è stata abilitata la App Sandbox ma non è stato fornito l'accesso di rete necessaria per la visualizzazione Web. Se si esegue l'applicazione a questo punto, dovrebbe essere una finestra vuota:

[![Che mostra l'accesso a Internet verrà bloccato](sandboxing-images/sample08.png "che mostra l'accesso a Internet verrà bloccato")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Verifica che l'app è in modalità sandbox

A parte la risorsa di blocco, esistono tre modi principali per indicare se un'applicazione xamarin. Mac è stata correttamente creata mediante sandbox:

1. In Finder, controllare il contenuto del `~/Library/Containers/` cartella - se l'app è in modalità sandbox, esisterà una cartella denominata come identificatore del Bundle dell'app (esempio: `com.appracatappra.MacSandbox`): 

    [![Apertura di bundle dell'app](sandboxing-images/sample09.png "apertura bundle dell'app")](sandboxing-images/sample09-large.png#lightbox)
2. Il sistema considera l'app in modalità sandbox in Monitoraggio attività:
    - Avviare Monitoraggio attività (in `/Applications/Utilities`). 
    - Scegli **View** > **colonne** e assicurarsi che il **Sandbox** voce di menu è selezionata.
    - Assicurarsi che la colonna Sandbox legge `Yes` per l'applicazione: 

    [![L'app in Monitoraggio attività di verifica](sandboxing-images/sample10.png "verifica l'app in Monitoraggio attività")](sandboxing-images/sample10-large.png#lightbox)
3. Verificare che l'app binario è in modalità sandbox:
    - Avviare l'app Terminal.
    - Passare alle applicazioni `bin` directory.
    - Eseguire questo comando: `codesign -dvvv --entitlements :- executable_path` (dove `executable_path` è il percorso all'applicazione): 

    [![Verifica dell'app nella riga di comando](sandboxing-images/sample11.png "verifica l'app nella riga di comando")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Debug di un'app creata mediante sandbox

Il debugger si connette all'App xamarin. Mac tramite TCP, il che significa che per impostazione predefinita quando si abilita il sandboxing, non è in grado di connettersi all'app, in modo che se si prova a eseguire l'app senza le dovute autorizzazioni abilitate, viene visualizzato un errore *"Unable to connect to il debugger"*. 

[![Impostare le opzioni necessarie](sandboxing-images/debug01.png "impostando le opzioni necessarie")](sandboxing-images/debug01-large.png#lightbox)

Il **Consenti rete le connessioni in uscita (Client)** autorizzazione è necessaria affinché il debugger, l'abilitazione di questa una consentirà debug normalmente. Poiché è possibile eseguire il debug senza di essa, abbiamo aggiornato il `CompileEntitlements` di destinazione per `msbuild` per aggiungere automaticamente tale autorizzazione per gli Entitlement per qualsiasi app che è in modalità sandbox per il debug solo le compilazioni. Le build di versione devono usare i diritti specificati nel file dei diritti, senza modificato.

### <a name="resolving-an-app-sandbox-violation"></a>Risolvere una violazione della App Sandbox

Se un sandbox xamarin. Mac applicazione ha tentato di accedere a una risorsa che dispone di non consentire esplicitamente, si verifica una violazione di Sandbox App. Ad esempio, la visualizzazione Web non è più la possibilità di visualizzare il sito Web Apple.

L'origine più comune di violazioni di Sandbox App si verifica quando le impostazioni di Entitlement specificate in Visual Studio per Mac non soddisfano i requisiti dell'applicazione. Anche in questo caso, eseguire il backup all'esempio precedente, la connessione di rete mancanti gli Entitlement che impediscono la visualizzazione Web funzionante.

#### <a name="discovering-app-sandbox-violations"></a>L'individuazione di violazioni di App Sandbox

Se si sospetta una violazione della Sandbox App è in corso nell'applicazione xamarin. Mac, il modo più rapido per individuare il problema consiste nell'usare la **Console** app.

Seguire questa procedura:

1. Compilare l'app in questione ed eseguirla da Visual Studio per Mac.
2. Aprire il **Console** dell'applicazione (dal `/Applications/Utilties/`).
3. Selezionare **tutti i messaggi** nella barra laterale e immettere `sandbox` nella ricerca: 

    [![Un esempio di un problema di sandboxing nella console](sandboxing-images/resolve01.png "un esempio di un problema di sandboxing nella console di")](sandboxing-images/resolve01-large.png#lightbox)

Per l'app di esempio precedente, è possibile vedere che sta bloccando il Kernal il `network-outbound` il traffico a causa della App Sandbox, perché non sono state richieste tale diritto.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Correggere le violazioni degli Entitlement App Sandbox

Ora che abbiamo visto come individuare le violazioni di Sandboxing di App, vediamo come possono essere risolti modificando i diritti dell'applicazione.

Seguire questa procedura:

1. Nel **riquadro della soluzione**, fare doppio clic il **entitlements. plist** file per aprirlo e modificarlo.
2. Sotto il **Entitlement** sezione, verificare il **Consenti rete le connessioni in uscita (Client)** casella di controllo: 

    [![Modifica degli Entitlement](sandboxing-images/sign17.png "modifica degli Entitlement")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche all'applicazione.

Se si esegue l'operazione precedente per l'app di esempio, quindi compilare ed eseguirla, il contenuto web verrà ora visualizzato come previsto.

## <a name="the-app-sandbox-in-depth"></a>App Sandbox approfondita

I meccanismi di controllo di accesso forniti dalla Sandbox dell'App sono pochi e facili da comprendere. Tuttavia, modo che l'ambiente App Sandbox sarà adottata per ogni app è univoco e in base ai requisiti dell'app.

Dato il migliore allo scopo di proteggere l'applicazione xamarin. Mac vengano sfruttate da malware, devono esistere solo una singola vulnerabilità nell'applicazione (o una delle librerie o Framework che utilizza) per ottenere il controllo delle interazioni dell'app con il System.

Sandbox dell'App è stata progettata per impedire l'acquisizione della proprietà (o limitare il danno che può causare), consentendo di specificare le interazioni previsto dell'applicazione con il sistema. Il sistema consentirà solo l'accesso per la risorsa richiesta dall'applicazione per svolgere il lavoro e nient'altro.

Quando si progetta per la Sandbox dell'App, si progetta per uno scenario peggiore. Se l'applicazione viene compromesso da malware, è limitata alla possibilità di accedere solo i file e le risorse nell'ambiente sandbox dell'app.

### <a name="entitlements-and-system-resource-access"></a>I diritti e l'accesso alle risorse di sistema

Come descritto in precedenza, un'applicazione xamarin. Mac che non è stata creata mediante sandbox è concedere i diritti completi e dell'utente che esegue l'app. Se compromesso da malware, un'app non protette può fungere da un agente per un comportamento dannoso, con un livello che vanno potenziali per l'esecuzione di un danno.

Abilitando la App Sandbox, è rimuovere tutto tranne un set minimo di privilegi, che può quindi abilitare di nuovo su base solo necessità usando i diritti dell'app xamarin. Mac. 

Modificare le risorse dell'applicazione App Sandbox modificando relativi **entitlements. plist** file e il controllo o selezionando i diritti necessari dalle caselle a discesa Editor:

[![Modifica degli Entitlement](sandboxing-images/sign17.png "modifica degli Entitlement")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Directory contenitore e accesso al file system

Quando l'applicazione xamarin. Mac adotta Sandbox dell'App, ha accesso ai percorsi seguenti:

- **La Directory di contenitore di App** -alla prima esecuzione, il sistema operativo viene creata una speciale _Directory contenitore_ dove tutte le relative risorse go, solo che possono accedere. L'app avrà accesso completo in lettura/scrittura per questa directory.
- **Directory contenitore gruppo di app** -l'app può essere concesso l'accesso a uno o più _gruppo di contenitori_ che vengono condivise tra le app nello stesso gruppo.
- **Specificato dall'utente file** -l'applicazione ottiene automaticamente l'accesso ai file che sono esplicitamente aperto o trascinate e rilasciate nell'applicazione dall'utente.
- **Gli elementi correlati ai** -con i diritti appropriati, l'applicazione può avere accesso a un file con lo stesso nome ma con un'estensione diversa. Ad esempio, un documento che è stato salvato sia come un `.txt` file e un `.pdf`.
- **Directory temporanee, lo strumento da riga di comando directory e percorsi specifici world leggibile** -l'app dispone di vari livelli di accesso ai file in altri percorsi ben definiti come specificato dal sistema.

#### <a name="the-app-container-directory"></a>La directory di contenitore di app

Directory di contenitore di App di un'applicazione xamarin. Mac ha le caratteristiche seguenti:

- Si trova in una località nascosta nella directory Home dell'utente (in genere `~Library/Containers`) ed è possibile accedervi con il `NSHomeDirectory` funzione (vedere sotto) all'interno dell'applicazione. Poiché si tratta della directory Home, ogni utente otterrà le proprie contenitore per l'app.
- L'app ha accesso in lettura/scrittura illimitato per la Directory contenitore e tutte le sottodirectory e file in esso contenuti.
- La maggior parte delle API di ricerca di percorso di macOS sono rispetto al contenitore dell'app. Ad esempio, il contenitore avrà un proprio **libreria** (accessibile tramite `NSLibraryDirectory`), **supporto delle applicazioni** e **preferenze** nelle sottodirectory.
- macOS stabilisce e applica la connessione tra e app e il relativo contenitore tramite la firma del codice. È anche un'altra app tenta di effettuare lo spoofing dell'app tramite il **identificatore del Bundle**, non sarà in grado di accedere al contenitore a causa di firma codice.
- Il contenitore non è disponibile per i file generati dall'utente. È invece per i file utilizzati dall'applicazione, ad esempio database, cache o altri tipi di dati specifici.
- Per la _shoebox_ tipi di App (ad esempio, app per le foto di Apple), a cui verrà salvato nel contenitore di contenuto dell'utente.

> [!IMPORTANT]
> Sfortunatamente, xamarin. Mac è privo di copertura per le API 100% ancora (a differenza di xamarin. IOS), di conseguenza il `NSHomeDirectory` API non è stato mappato nella versione corrente di xamarin. Mac.

Come soluzione alternativa temporanea, è possibile usare il codice seguente:

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

Inizia con macOS Mac 10.7.5 (e versioni successive), un'applicazione può utilizzare il `com.apple.security.application-groups` diritto di accedere a un contenitore condiviso comune a tutte le applicazioni nel gruppo. È possibile usare questo contenitore condiviso per il contenuto pubblico non utente, ad esempio database o altri tipi di file di supporto (ad esempio, le cache).

I contenitori di gruppi vengono aggiunti automaticamente a Sandbox contenitore ogni app (se sono parte di un gruppo) e vengono archiviati nel `~/Library/Group Containers/<application-group-id>`. L'ID del gruppo _necessario_ iniziano con l'ID Team di sviluppo e un periodo, ad esempio:

```plist
<team-id>.com.company.<group-name>
```

Per altre informazioni, vedere di Apple [aggiunta di un'applicazione a un gruppo di applicazioni](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) nelle [Entitlement chiave riferimento](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Accesso al sistema Powerbox e file all'esterno del contenitore di app

Un'applicazione xamarin. Mac in modalità sandbox può accedere ai percorsi del file system all'esterno del relativo contenitore nei modi seguenti:

- Su indicazione specifici dell'utente (tramite Apri e salva finestre di dialogo o altri metodi, ad esempio trascinamento della selezione).
- Usando i diritti per i percorsi di file specifici del sistema (ad esempio `/bin` o `/usr/lib`).
* Quando il percorso del file system è in determinate directory che sono leggibili (ad esempio condivisione) mondiale.

_Powerbox_ è la tecnologia di protezione che interagisce con l'utente a espandere i diritti di accesso file dell'app xamarin. Mac in modalità sandbox di macOS. Powerbox non dispone di API, ma viene attivato in modo trasparente quando l'app chiama un' `NSOpenPanel` o `NSSavePanel`. Accesso Powerbox viene abilitata tramite i diritti che è impostato per l'applicazione xamarin. Mac.

Quando un'app in modalità sandbox consente di visualizzare un elemento aperto o finestra di dialogo Salva, la finestra viene presentata dal Powerbox (e non AppKit) e ha pertanto accesso a qualsiasi file o directory in cui l'utente può accedere.

Quando un utente seleziona un file o directory da aprire o finestra di dialogo Salva (o trascina uno sull'icona dell'App), Powerbox aggiunge il percorso associato alla sandbox dell'app.

Inoltre, il sistema consente automaticamente il comando seguente per un'app in modalità sandbox:

- Connessione a un metodo di input del sistema.
- Chiamare i servizi selezionati dall'utente dal **Services** menu (solo per i servizi contrassegnati come _sicuri per le app sandbox_ dal provider di servizi).
- Scegliere i file aperti dall'utente di **Apri recenti** menu.
- Usare la copia e incolla tra le altre applicazioni.
- Leggere i file dalle posizioni seguenti world leggibile:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Leggere e scrivere file nelle directory create dal `NSTemporaryDirectory`.

Per impostazione predefinita, i file aperta o salvata in un'app xamarin. Mac in modalità sandbox per rimangano accessibili finché non termina con l'app (a meno che il file era aperto quando si chiude l'app). I file aperti verranno ripristinati automaticamente per la sandbox dell'app tramite la funzionalità di ripresa macOS al successivo avvio dell'app.

Per garantire la persistenza per i file che si trova all'esterno del contenitore di un'App xamarin. Mac, usare i segnalibri con ambito di sicurezza (vedere sotto).

#### <a name="related-items"></a>Elementi correlati

App Sandbox consente a un'app accedere a elementi correlati che presentano lo stesso nome di file, ma le estensioni diverse. La funzionalità è costituito da due parti: a) un elenco di estensioni correlate dell'app `Info.plst` file, codice b) per indicare la sandbox dell'app sarà essere svolte con questi file.

Esistono due scenari in cui ha senso:

1. L'app deve essere in grado di salvare una versione diversa del file (con una nuova estensione). Ad esempio esportando un `.txt` file per un `.pdf` file. Per gestire questa situazione, è necessario usare un `NSFileCoordinator` per accedere al file. Verrà chiamata il `WillMove(fromURL, toURL)` metodo prima di tutto, spostare il file di estensione per il nuovo e quindi chiamare `ItemMoved(fromURL, toURL)`.
2. L'app deve aprire un file con un'estensione principale e diversi file di supporto con estensioni diverse. Ad esempio, un film e un file di sottotitoli in altre lingue. Usare un `NSFilePresenter` per ottenere l'accesso al file secondario. Fornire il file principale per il `PrimaryPresentedItemURL` proprietà e il file secondario per il `PresentedItemURL` proprietà. Quando viene aperto il file principale, chiama il `AddFilePresenter` metodo del `NSFileCoordinator` classe per registrare il file secondario.

In dell'entrambi gli scenari, l'app **Info. plist** file necessario dichiarare i tipi di documento che è possibile aprire l'app. Per qualsiasi tipo di file, aggiungere il `NSIsRelatedItemType` (con il valore `YES`) alla relativa voce nel `CFBundleDocumentTypes` matrice.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Aprire e salvare il comportamento di finestra di dialogo con le app create mediante sandbox

Le seguenti limitazioni vengono inserite nel `NSOpenPanel` e `NSSavePanel` quando chiamarle da un'app xamarin. Mac in modalità sandbox:

- A livello di codice non è possibile richiamare il **OK** pulsante.
- A livello di codice non è possibile modificare la selezione dell'utente in un `NSOpenSavePanelDelegate`.

Inoltre, le modifiche dell'ereditarietà seguenti siano presenti:

- **Non in modalità sandbox App** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>I segnalibri con ambito di protezione e accesso alle risorse permanente

Come indicato in precedenza, un'applicazione xamarin. Mac in modalità sandbox può accedere un file o una risorsa all'esterno del relativo contenitore tramite l'interazione diretta dell'utente (come specificato da PowerBox). Tuttavia, l'accesso a queste risorse non automaticamente rende persistenti tra avvii dell'app o del sistema riavvii.

Usando _segnalibri Security-Scoped_, un'applicazione xamarin. Mac creata mediante sandbox possa conservare le intenzioni dell'utente e mantenere l'accesso specificato dopo un'app per le risorse di riavvio.

#### <a name="security-scoped-bookmark-types"></a>Tipi di segnalibro con ambito di protezione

Quando si lavora con i segnalibri Security-Scoped e accesso alle risorse persistenti, esistono due casi d'uso sistine:

- **Un segnalibro App-Scoped fornisce l'accesso permanente a un file specificato dall'utente o una cartella.** 

    Ad esempio, se l'applicazione in modalità sandbox di xamarin. Mac consente di usare per aprire un documento esterno per la modifica (usando un `NSOpenPanel`), l'app può creare un segnalibro App-Scoped in modo che possa accedere nuovamente in futuro lo stesso file.
- **Un segnalibro Document-Scoped fornisce un documento specifico permanente l'accesso a un file secondario.** 

Ad esempio, un'app di modifica video che crea un file di progetto che ha accesso alle singole immagini, video clip e i file audio in un secondo momento verranno combinati in un singolo filmato.

Quando l'utente importa un file di risorse nel progetto (tramite un `NSOpenPanel`), l'app crea un segnalibro Document-Scoped per l'elemento che viene archiviato nel progetto, in modo che il file è sempre possibile accedere all'app.

Un segnalibro Document-Scoped può essere risolto da qualsiasi applicazione che è possibile aprire i dati di segnalibro e il documento stesso. Questo oggetto supporta la portabilità, consentendo all'utente di inviare i file di progetto in un altro utente e con tutti i segnalibri funziona anche per loro.

> [!IMPORTANT]
> È un segnalibro Document-Scoped _solo_ puntare a un singolo file e non una cartella e file non può trovarsi in una posizione utilizzata dal sistema (ad esempio `/private` o `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Uso dei segnalibri con ambito di protezione

Con entrambi i tipi di segnalibro Security-Scoped, è necessario eseguire la procedura seguente:

1. **Impostare i diritti appropriati nell'app xamarin. Mac che deve usare i segnalibri Security-Scoped** -For App-Scoped segnalibri, impostare il `com.apple.security.files.bookmarks.app-scope` chiave Entitlement `true`. Per i segnalibri Document-Scoped, impostare il `com.apple.security.files.bookmarks.document-scope` chiave Entitlement `true`.
2. **Creare un segnalibro Security-Scoped** -puoi farlo per qualsiasi file o cartella in cui l'utente ha fornito l'accesso a (tramite `NSOpenPanel` , ad esempio), che l'applicazione richiede accesso permanente a. Usare il `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` metodo di `NSUrl` classe per creare il segnalibro.
3. **Risolvere il segnalibro Security-Scoped** : quando l'app deve accedere nuovamente alla risorsa (ad esempio, dopo il riavvio) sarà necessario risolvere il segnalibro a un URL con ambito di protezione. Usare la `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` metodo del `NSUrl` classe per risolvere il segnalibro.
4. **Notificare in modo esplicito il sistema che si desidera accedere al file dall'URL Security-Scoped** : questo passaggio deve essere eseguita immediatamente dopo aver ottenuto l'URL di Security-Scoped precedente o, quando successivamente si vuole ripristinare l'accesso alla risorsa dopo l'assegnazione rilasciata non di accedervi. Chiamare il `StartAccessingSecurityScopedResource ()` metodo di `NSUrl` classe per avviare l'accesso a un URL Security-Scoped.
5. **Notificare in modo esplicito il sistema di che aver accesso al file dall'URL Security-Scoped** -appena possibile, è consigliabile informare il sistema quando l'app non è più necessario accedere al file (ad esempio, se l'utente chiude). Chiamare il `StopAccessingSecurityScopedResource ()` metodo di `NSUrl` alla classe di interrompere l'accesso a un URL Security-Scoped.

Dopo che si avrà accesso a una risorsa, è necessario tornare al passaggio 4 per ristabilire l'accesso. Se viene riavviato l'app xamarin. Mac, è necessario tornare al passaggio 3 e risolvere nuovamente il segnalibro.

> [!IMPORTANT]
> Il mancato rilascio accesso alle risorse URL Security-Scoped causerà un'app xamarin. Mac a verificarsi perdite di risorse del Kernel. Di conseguenza, l'app non saranno in grado di aggiungere posizioni del file system al contenitore fino a quando non viene riavviato.

### <a name="the-app-sandbox-and-code-signing"></a>La firma del codice e App Sandbox

Dopo aver abilitato la App Sandbox e abilitare i requisiti specifici per l'app xamarin. Mac (tramite i diritti), è necessario codificare firmare il progetto per il sandboxing rendere effettive. È necessario eseguire poiché gli Entitlement richiesti per il Sandboxing di App sono collegati alla firma dell'app di firma del codice. 

macOS consente di applicare un collegamento tra contenitore un'app e la relativa firma codice, in questo modo che nessun altra applicazione può accedere tale contenitore, anche se esegue lo spoofing le app ID Bundle. Questo meccanismo funziona nel modo seguente:

1. Quando il sistema crea contenitore dell'app, viene impostata un' _elenco di controllo di accesso_ (ACL) in tale contenitore. La voce di controllo di accesso iniziale nell'elenco include l'app _designati requisito_ (ripristino di emergenza), che descrive la modalità future versioni dell'app può essere riconosciuti (quando è stata aggiornata).
2. Ogni volta che viene avviata un'app con lo stesso ID di Bundle, il sistema verifica che firma codice dell'app corrispondente ai requisiti designato specificato in una delle voci nell'elenco ACL del contenitore. Se il sistema non viene trovata una corrispondenza, che impedisce all'app di avvio.

Code Signing works modi seguenti:

1. Prima di creare il progetto xamarin. Mac, ottenere un certificato di sviluppo, un certificato di distribuzione e un certificato ID sviluppatore dal portale Apple Developer.
2. Quando la Store di App Mac distribuisce l'app xamarin. Mac, è firmato con una firma di codice Apple.

Durante il test e debug, si userà una versione dell'applicazione xamarin. Mac che hai effettuato l'accesso (che verrà utilizzata per creare il contenitore di App). In un secondo momento, se si desidera testare o installare la versione da Store di App di Apple, verrà firmato con la firma di Apple e non verrà avviata (poiché non ha la stessa firma di codice come contenitore dell'App originale). In questo caso, si otterrà un report di arresto anomalo del sistema simile al seguente:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Per risolvere questo problema, è necessario modificare la voce ACL in modo da puntare alla Apple versione firmata dell'app.

Per ulteriori informazioni sulla creazione e il download di profili di Provisioning necessari per il Sandboxing, vedere la [firma e Provisioning dell'App](#Signing_and_Provisioning_the_App) sezione precedente.

#### <a name="adjusting-the-acl-entry"></a>Modificando la voce ACL

Per consentire la versione firmata Apple dell'app xamarin. Mac per l'esecuzione, eseguire le operazioni seguenti:

1. Aprire l'app Terminal (nel `/Applications/Utilities`).
2. Aprire una finestra del Finder per la versione firmata Apple dell'app xamarin. Mac.
3. Tipo `asctl container acl add -file ` nella finestra del terminale.
4. Trascinare l'icona dell'app xamarin. Mac nella finestra del Finder e rilasciarla nella finestra del terminale.
5. Il percorso completo del file verrà aggiunto al comando nel terminale.
6. Premere **invio** per eseguire il comando.

ACL del contenitore contiene ora i requisiti del codice designato per entrambe le versioni dell'app xamarin. Mac e macOS consentirà ora entrambe le versioni per l'esecuzione.

#### <a name="display-a-list-of-acl-code-requirements"></a>Visualizzare un elenco di richieste di codici ACL

È possibile visualizzare un elenco dei requisiti di codice nell'ACL di un contenitore eseguendo queste operazioni:

1. Aprire l'app Terminal (nel `/Applications/Utilities`).
2. Digitare `asctl container acl list -bundle <container-name>`.
3. Premere **invio** per eseguire il comando.

Il `<container-name>` è in genere l'identificatore del Bundle per l'applicazione xamarin. Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Progettazione di un'app xamarin. Mac per la Sandbox App

È un flusso di lavoro comune da seguire quando si progetta un'app xamarin. Mac per la Sandbox dell'App. Ciò premesso, le specifiche dell'implementazione di sandboxing in un'app devono essere univoci per la funzionalità dell'app specificato.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Sei passaggi per l'adozione della Sandbox dell'App

Progettazione di un'app xamarin. Mac per la Sandbox dell'App in genere prevede i passaggi seguenti:

1. Determinare se l'app è appropriato per il sandboxing.
2. Progettare una strategia di sviluppo e distribuzione.
3. Risolvere eventuali incompatibilità di API.
4. Applicare gli Entitlement Sandbox App necessari al progetto xamarin. Mac.
5. Aggiungere la separazione dei privilegi tramite XPC.
6. Implementare una strategia di migrazione.

> [!IMPORTANT]
> È necessario non solo sandbox l'eseguibile principale è bundle dell'app, ma anche ogni helper incluse app o lo strumento in tale bundle. Ciò è obbligatorio per qualsiasi app distribuita da Store di App Mac e, se possibile, deve essere eseguita per qualsiasi altra forma di distribuzione di app.

Per un elenco di tutti i file binari eseguibili nel bundle di un'app xamarin. Mac, digitare il comando seguente nel terminale:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

In cui `[Your-App-Bundle]` è il nome e il percorso al bundle dell'applicazione.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Stabilire se un'app xamarin. Mac è adatta per il sandboxing

La maggior parte delle App xamarin. Mac sono completamente compatibili con Sandbox di App e di conseguenza, adatto per il sandboxing. Se l'app richiede un comportamento che non consente la Sandbox dell'App, è consigliabile un approccio alternativo.

Se l'app richiede uno dei comportamenti seguenti, non è compatibile con Sandbox di App:

- **Servizi di autorizzazione** -con Sandbox App, è possibile lavorare con le funzioni descritte in [riferimenti del linguaggio C servizi autorizzazione](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **Accessibilità API** -non è possibile sandbox App per l'accesso facilitato quali gli screen reader o le app che consentono di controllare altre applicazioni.
- **Inviare eventi Apple per le app arbitrario** -se l'app richiede l'invio di eventi Apple per un'app sconosciuta, arbitraria, non può essere creata mediante sandbox. Per un elenco noto di App chiamato, l'app può ancora essere creata mediante sandbox e gli Entitlement dovrà includere l'elenco di app per chiamata.
- **Inviare i dizionari Info utente nelle notifiche distribuita ad altre attività** -con Sandbox App, non è possibile includere un `userInfo` dizionario durante la registrazione per un `NSDistributedNotificationCenter` oggetto per le altre attività di messaggistica.
- **Caricare le estensioni di Kernel** -il caricamento delle estensioni del Kernel non è consentito dalla Sandbox dell'App.
- **Simulazione dell'Input dell'utente in finestre di dialogo Salva e Apri** : a livello di codice la modifica di aprire o salvare le finestre di dialogo per simulare o modificare input dell'utente non è consentito dalla Sandbox dell'App.
- **Impostazione delle preferenze in altre App o ad accesso** -modifica le impostazioni delle altre App non è consentita dalla Sandbox dell'App.
- **Configurazione delle impostazioni di rete** -modifica di impostazioni di rete non è consentita dalla Sandbox dell'App.
- **Terminare le altre app** -impedisce The Sandbox App usando `NSRunningApplication` per terminare le altre app.

### <a name="resolving-api-incompatibilities"></a>Risoluzione dei problemi di incompatibilità di API

Quando si progetta un'app xamarin. Mac per la Sandbox dell'App, è possibile riscontrare problemi di incompatibilità con l'utilizzo di alcune API di macOS.

Ecco alcuni problemi e operazioni che è possibile eseguire per risolverli più comuni:

- **Apertura, salvataggio e rilevamento dei documenti** : se si siano gestendo i documenti con qualsiasi tecnologia diverso `NSDocument`, deve passare a questo livello a causa il supporto incorporato per la Sandbox dell'App. `NSDocument` funziona con PowerBox e fornisce il supporto per la conservazione dei documenti all'interno di sandbox se l'utente li sposta nel Finder automaticamente.
- **Mantenere l'accesso alle risorse di sistema File** : se l'opzione di xamarin. Mac app dipende da accesso permanente alle risorse all'esterno del relativo contenitore, usare i segnalibri con ambito di protezione per mantenere l'accesso.
- **Creare un elemento account di accesso per un'App** -con Sandbox App, non è possibile creare un elemento tramite l'account di accesso `LSSharedFileList` né è possibile modificare lo stato dei servizi di avvio usando `LSRegisterURL`. Usare la `SMLoginItemSetEnabled` funzionano come descritto in mele [aggiunta di elementi di account di accesso con il Framework di Gestione servizio](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentazione.
- **Accesso ai dati utente** : se si usa ad esempio funzioni POSIX `getpwuid` per ottenere home directory dell'utente da servizi di directory, è consigliabile usare i simboli di Cocoa o componente di base, ad esempio `NSHomeDirectory`.
- **L'accesso alle preferenze di altre app** - perché l'ambiente App Sandbox indirizza percorso per individuare le API al contenitore dell'app, modifica delle preferenze viene eseguita all'interno del contenitore e l'accesso in un altro alle preferenze delle App non consentite. 
- **Usare HTML5 Video incorporato nelle visualizzazioni Web** -se l'app xamarin. Mac Usa WebKit per riprodurre video HTML5 incorporati, è anche necessario collegare l'app con il framework AV Foundation. Sandbox dell'App, non potrà CoreMedia play questi video in caso contrario.

### <a name="applying-required-app-sandbox-entitlements"></a>Applicazione di Entitlement necessari App Sandbox

È necessario modificare i diritti per tutte le applicazioni xamarin. Mac che si vuole eseguire nella Sandbox dell'App e verificare i **abilitare App Sandboxing** casella di controllo.

Basato sulle funzionalità dell'app, potrebbe essere necessario abilitare altri i diritti per accedere a funzionalità del sistema operativo o le risorse. Pertanto, App Sandboxing produce risultati migliori quando è ridurre al minimo gli Entitlement che richiesta per il livello minimo richiesto per eseguire l'app in modo casuale abilitare i diritti.

Per determinare quali gli Entitlement richiede un'app xamarin. Mac, eseguire le operazioni seguenti:

1. Abilitare la Sandbox dell'App ed eseguire l'app xamarin. Mac.
2. Eseguire le funzionalità dell'App.
3. Aprire l'app Console (disponibile nel `/Applications/Utilities`) e cercare `sandboxd` violazioni nel **tutti i messaggi** log.
4. Per ogni `sandboxd` violazione, risolvere il problema tramite l'utilizzo del contenitore di app anziché altri percorsi del file system o applicare i diritti di Sandbox App per abilitare l'accesso alle funzionalità del sistema operativo con restrizioni.
5. Eseguire nuovamente e testare nuovamente tutte le funzionalità di app xamarin. Mac.
6. Ripetere l'operazione finché tutti `sandboxd` le violazioni sono stati risolte.

### <a name="add-privilege-separation-using-xpc"></a>Aggiungere la separazione dei privilegi tramite XPC

Quando si sviluppa un'app xamarin. Mac per la Sandbox dell'App, osservare i comportamenti dell'app nei termini di privilegi e l'accesso, quindi prendere in considerazione la separazione operazioni ad alto rischio nei propri servizi XPC.

Per altre informazioni, vedere di Apple [creazione di servizi XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) e [Guida alla programmazione di servizi e ai daemon](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implementare una strategia di migrazione

Se si sta rilasciando una nuova versione creata mediante sandbox di un'applicazione xamarin. Mac che non è stata precedentemente creata mediante sandbox, è necessario assicurarsi che gli utenti correnti sono un buon percorso di aggiornamento. 

 Per informazioni dettagliate su come implementare un manifesto del contenitore della migrazione, leggere di Apple [migrando un'App a una Sandbox](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentazione.

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato sandboxing un'applicazione xamarin. Mac. In primo luogo, abbiamo creato un'app di xamarin. Mac è sufficiente per mostrare le nozioni di base della Sandbox dell'App. Successivamente, è stato illustrato come risolvere violazioni di sandbox. Quindi, Daremo un'analisi approfondita esaminare Sandbox dell'App e, infine, è stata esaminata la progettazione di un'app xamarin. Mac per la Sandbox dell'App.



## <a name="related-links"></a>Collegamenti correlati

- [Pubblicazione nell'App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Su Sandbox App](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemi comuni di Sandboxing di App](https://developer.apple.com/library/content/qa/qa1773/_index.html)
