---
title: Sandboxing di un'app Novell. Mac
description: Questo articolo illustra il sandboxing di un'applicazione Novell. Mac per il rilascio nell'App Store. Copre tutti gli elementi che entrano in sandbox, ad esempio le directory dei contenitori, i diritti, le autorizzazioni definite dall'utente, la separazione dei privilegi e l'imposizione del kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 4558a9bd19810f8759010861d8a2e4b8cab09c56
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70770296"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Sandboxing di un'app Novell. Mac

_Questo articolo illustra il sandboxing di un'applicazione Novell. Mac per il rilascio nell'App Store. Copre tutti gli elementi che entrano in sandbox, ad esempio le directory dei contenitori, i diritti, le autorizzazioni definite dall'utente, la separazione dei privilegi e l'imposizione del kernel._

## <a name="overview"></a>Panoramica

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, si ha la stessa capacità di sandbox di un'applicazione come si fa quando si usa Objective-C o SWIFT.

[![Esempio di app in esecuzione](sandboxing-images/intro01.png "Esempio di app in esecuzione")](sandboxing-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base per l'uso della sandbox in un'applicazione Novell. Mac e di tutti gli elementi che vengono inseriti in sandbox: directory del contenitore, diritti, autorizzazioni definite dall'utente, separazione dei privilegi e imposizione del kernel. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando gli attributi `Register` e `Export` usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

## <a name="about-the-app-sandbox"></a>Informazioni sulla sandbox dell'app

Il sandbox dell'app offre una difesa forte contro i danni che possono essere causati da un'applicazione dannosa eseguita su un Mac limitando l'accesso a un'applicazione per le risorse di sistema.

Un'applicazione non in modalità sandbox dispone dei diritti completi dell'utente che esegue l'app e può accedere o eseguire qualsiasi operazione che può essere eseguita dall'utente. Se l'app contiene buchi di sicurezza (o qualsiasi Framework che sta usando), un pirata informatico può sfruttare tali debolezze e usare l'app per assumere il controllo del Mac su cui è in esecuzione.

Limitando l'accesso alle risorse in base alle singole applicazioni, un'app in modalità sandbox fornisce una linea di difesa contro furti, danneggiamenti o intenti dannosi sulla parte di un'applicazione in esecuzione nel computer dell'utente.

Il sandbox dell'app è una tecnologia di controllo degli accessi integrata in macOS (applicata a livello di kernel) che offre una duplice strategia:

1. Il sandbox dell'app consente allo sviluppatore di descrivere il _modo_ in cui un'applicazione interagirà con il sistema operativo e, in questo modo, vengono concessi solo i diritti di accesso necessari per svolgere il processo e non più.
2. Il sandbox dell'app consente all'utente di concedere facilmente ulteriori accessi al sistema tramite le finestre di dialogo Apri e Salva, le operazioni di trascinamento della selezione e altre interazioni utente comuni.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparazione per implementare sandbox dell'app

Gli elementi della sandbox dell'app che verranno descritti in dettaglio nell'articolo sono i seguenti:

- Directory contenitore
- Diritti
- Autorizzazioni definite dall'utente
- Separazione dei privilegi
- Imposizione del kernel

Dopo aver compreso questi dettagli, sarà possibile creare un piano per l'adozione dell'app sandbox nell'applicazione Novell. Mac.

In primo luogo, è necessario determinare se l'applicazione è un buon candidato per il sandboxing (la maggior parte delle applicazioni è). Successivamente, sarà necessario risolvere eventuali incompatibilità API e determinare quali elementi della sandbox dell'app sono necessari. Infine, esaminare l'uso della separazione dei privilegi per massimizzare il livello di difesa dell'applicazione.

Quando si adotta la sandbox dell'app, alcuni file system percorsi usati dall'applicazione saranno diversi. In particolare, l'applicazione avrà una directory contenitore che verrà usata per i file di supporto dell'applicazione, i database, le cache e tutti gli altri file che non sono documenti utente. MacOS e Xcode forniscono supporto per eseguire la migrazione di questi tipi di file dalle rispettive posizioni legacy al contenitore.

## <a name="sandboxing-quick-start"></a>Guida introduttiva al sandboxing

In questa sezione verrà creata una semplice app Novell. Mac che usa una visualizzazione Web, che richiede una connessione di rete limitata in modalità sandbox, a meno che non sia specificamente richiesta, come esempio di introduzione all'app sandbox.

Si verificherà che l'applicazione sia effettivamente sandbox e si apprenderà come risolvere gli errori comuni di sandbox app.

### <a name="creating-the-xamarinmac-project"></a>Creazione del progetto Novell. Mac

Per creare il progetto di esempio, eseguire le operazioni seguenti:

1. Avviare Visual Studio per Mac e fare clic sulla **nuova soluzione.** account".
2. Nella finestra di dialogo **nuovo progetto** selezionare **Mac**  > **app**  > **Cocoa app**:

    [![Creazione di una nuova app Cocoa](sandboxing-images/sample01.png "Creazione di una nuova app Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Fare clic sul pulsante **Avanti** , immettere `MacSandbox` per il nome del progetto e fare clic sul pulsante **Crea** :

    [![Immissione del nome dell'app](sandboxing-images/sample02.png "Immissione del nome dell'app")](sandboxing-images/sample02-large.png#lightbox)
4. Nella **riquadro della soluzione**fare doppio clic sul file **Main. Storyboard** per aprirlo per la modifica in Xcode:

    [![Modifica dello storyboard principale](sandboxing-images/sample03.png "Modifica dello storyboard principale")](sandboxing-images/sample03-large.png#lightbox)
5. Trascinare una **visualizzazione Web** sulla finestra, ridimensionarla per riempire l'area di contenuto e impostarla in modo da espanderla e compattarla con la finestra:

    [![Aggiunta di una visualizzazione Web](sandboxing-images/sample04.png "Aggiunta di una visualizzazione Web")](sandboxing-images/sample04-large.png#lightbox)
6. Creare un outlet per la visualizzazione Web denominata `webView`:

    [![Creazione di un nuovo outlet](sandboxing-images/sample05.png "Creazione di un nuovo outlet")](sandboxing-images/sample05-large.png#lightbox)
7. Tornare a Visual Studio per Mac e fare doppio clic sul file **ViewController.cs** nel **riquadro della soluzione** per aprirlo per la modifica.
8. Aggiungere la seguente istruzione using: `using WebKit;`
9. Rendere il `ViewDidLoad` metodo simile al seguente:

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Salvare le modifiche.

Eseguire l'applicazione e verificare che il sito Web Apple venga visualizzato nella finestra come indicato di seguito:

[![Visualizzazione di un'esecuzione di app di esempio](sandboxing-images/sample06.png "Visualizzazione di un'esecuzione di app di esempio")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Firma e provisioning dell'app

Prima di poter abilitare la sandbox dell'app, è prima di tutto necessario effettuare il provisioning e firmare l'applicazione Novell. Mac.

Consente di eseguire le operazioni seguenti:

1. Accedere al portale per sviluppatori Apple:

    [![Accesso al portale per sviluppatori Apple](sandboxing-images/sign01.png "Accesso al portale per sviluppatori Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Selezionare **certificati, identificatori & profili**:

    [![Selezione di certificati, identificatori & profili](sandboxing-images/sign02.png "Selezione di certificati, identificatori & profili")](sandboxing-images/sign02-large.png#lightbox)
3. In **app Mac**selezionare **identificatori**:

    [![Selezione degli identificatori](sandboxing-images/sign03.png "Selezione degli identificatori")](sandboxing-images/sign03-large.png#lightbox)
4. Creare un nuovo ID per l'applicazione:

    [![Creazione di un nuovo ID app](sandboxing-images/sign04.png "Creazione di un nuovo ID app")](sandboxing-images/sign04-large.png#lightbox)
5. In **profili di provisioning**selezionare **sviluppo**:

    [![Selezione dello sviluppo](sandboxing-images/sign05.png "Selezione dello sviluppo")](sandboxing-images/sign05-large.png#lightbox)
6. Crea un nuovo profilo e seleziona **sviluppo di app Mac**:

    [![Creazione di un nuovo profilo](sandboxing-images/sign06.png "Creazione di un nuovo profilo")](sandboxing-images/sign06-large.png#lightbox)
7. Selezionare l'ID app creato in precedenza:

    [![Selezione dell'ID app](sandboxing-images/sign07.png "Selezione dell'ID app")](sandboxing-images/sign07-large.png#lightbox)
8. Selezionare gli sviluppatori per questo profilo:

    [![Aggiunta di sviluppatori](sandboxing-images/sign08.png "Aggiunta di sviluppatori")](sandboxing-images/sign08-large.png#lightbox)
9. Selezionare i computer per questo profilo:

    [![Selezione dei computer consentiti](sandboxing-images/sign09.png "Selezione dei computer consentiti")](sandboxing-images/sign09-large.png#lightbox)
10. Assegnare un nome al profilo:

    [![Assegnazione di un nome al profilo](sandboxing-images/sign10.png "Assegnazione di un nome al profilo")](sandboxing-images/sign10-large.png#lightbox)
11. Fare clic sul pulsante **fine** .

> [!IMPORTANT]
> In alcuni casi potrebbe essere necessario scaricare direttamente il nuovo profilo di provisioning dal portale per sviluppatori di Apple e fare doppio clic su di esso per installarlo. Potrebbe anche essere necessario arrestare e riavviare Visual Studio per Mac prima che possa accedere al nuovo profilo.

Successivamente, è necessario caricare il nuovo ID app e il profilo nel computer di sviluppo. Eseguire le operazioni seguenti:

1. Avviare Xcode e selezionare **Preferenze** dal menu **Xcode** :

    ![Modifica di account in Xcode](sandboxing-images/sign11.png "Modifica di account in Xcode")
2. Fare clic sul pulsante **Visualizza dettagli...** :

    ![Clic sul pulsante Visualizza dettagli](sandboxing-images/sign12.png "Clic sul pulsante Visualizza dettagli")
3. Fare clic sul pulsante di **aggiornamento** nell'angolo in basso a sinistra.
4. Fare clic sul pulsante **fine** .

Successivamente, è necessario selezionare il nuovo ID app e il profilo di provisioning nel progetto Novell. Mac. Eseguire le operazioni seguenti:

1. Nel **riquadro della soluzione**fare doppio clic sul file **info. plist** per aprirlo per la modifica.
2. Verificare che l' **identificatore del bundle** corrisponda all'ID app creato in precedenza (ad esempio: `com.appracatappra.MacSandbox`):

    [![Modifica dell'identificatore del bundle](sandboxing-images/sign13.png "Modifica dell'identificatore del bundle")](sandboxing-images/sign13-large.png#lightbox)
3. Fare quindi doppio clic sul file **titles. plist** e assicurarsi che l' **Archivio chiave-valore di iCloud** e i **contenitori iCloud** corrispondano tutti all'ID app creato in precedenza (ad esempio: `com.appracatappra.MacSandbox`):

    [![Modifica del file con estensione plist dei diritti](sandboxing-images/sign17.png "Modifica del file con estensione plist dei diritti")](sandboxing-images/sign17-large.png#lightbox)
4. Salvare le modifiche.
5. Nella **riquadro della soluzione**fare doppio clic sul file di progetto per aprirne le opzioni per la modifica:

    ![Editign le opzioni della soluzione](sandboxing-images/sign14.png "Editign le opzioni della soluzione")
6. Selezionare **firma Mac**, quindi selezionare firma **il bundle dell'applicazione** e **firmare il pacchetto del programma di installazione**. In **profilo di provisioning**selezionare quello creato in precedenza:

    ![Impostazione del profilo di provisioning](sandboxing-images/sign15.png "Impostazione del profilo di provisioning")
7. Fare clic sul pulsante **fine** .

> [!IMPORTANT]
> Potrebbe essere necessario chiudere e riavviare Visual Studio per Mac per fare in modo che riconosca il nuovo ID app e il profilo di provisioning installato da Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Risoluzione dei problemi di provisioning

A questo punto è consigliabile provare a eseguire l'applicazione e verificare che tutti gli elementi siano firmati e sottoposti a provisioning correttamente. Se l'app viene ancora eseguita come prima, tutto è valido. In caso di errore, è possibile che venga visualizzata una finestra di dialogo simile alla seguente:

[![Finestra di dialogo del problema di provisioning di esempio](sandboxing-images/sign16.png "Finestra di dialogo del problema di provisioning di esempio")](sandboxing-images/sign16-large.png#lightbox)

Di seguito sono riportate le cause più comuni dei problemi di provisioning e firma:

- L'ID bundle dell'app non corrisponde all'ID app del profilo selezionato.
- L'ID sviluppatore non corrisponde all'ID sviluppatore del profilo selezionato.
- L'UUID del Mac sottoposto a test non è registrato come parte del profilo selezionato.

Nel caso di un problema, correggere il problema nel portale Apple Developer, aggiornare i profili in Xcode ed eseguire una compilazione pulita in Visual Studio per Mac.

### <a name="enable-the-app-sandbox"></a>Abilitare sandbox app

Per abilitare la sandbox dell'app, selezionare una casella di controllo nelle opzioni dei progetti. Procedere come descritto di seguito:

1. Nel **riquadro della soluzione**fare doppio clic sul file **titles. plist** per aprirlo per la modifica.
2. Selezionare **Abilita diritti** e **Abilita sandboxing app**:

    [![Modifica dei diritti e abilitazione della sandbox](sandboxing-images/sign17.png "Modifica dei diritti e abilitazione della sandbox")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche.

A questo punto, l'app sandbox è stata abilitata, ma non è stato fornito l'accesso alla rete necessario per la visualizzazione Web. Se si esegue ora l'applicazione, è necessario ottenere una finestra vuota:

[![Visualizzazione del blocco dell'accesso Web](sandboxing-images/sample08.png "Visualizzazione del blocco dell'accesso Web")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Verifica per verificare se l'app è in modalità sandbox

A parte il comportamento di blocco delle risorse, esistono tre modi principali per stabilire se un'applicazione Novell. Mac è stata creata in modalità sandbox:

1. In Finder verificare il contenuto della cartella `~/Library/Containers/`: se l'app è in modalità sandbox, sarà presente una cartella denominata come l'identificatore del bundle dell'app, ad esempio: `com.appracatappra.MacSandbox`:

    [![Apertura del bundle dell'app](sandboxing-images/sample09.png "Apertura del bundle dell'app")](sandboxing-images/sample09-large.png#lightbox)
2. Il sistema Visualizza l'app in modalità sandbox in Monitoraggio attività:
    - Avviare Monitoraggio attività (in `/Applications/Utilities`).
    - Scegliere **Visualizza** **colonne**  >  e verificare che la voce di menu **sandbox** sia selezionata.
    - Verificare che la colonna sandbox legga `Yes` per l'applicazione:

    [![Controllo dell'app in Monitoraggio attività](sandboxing-images/sample10.png "Controllo dell'app in Monitoraggio attività")](sandboxing-images/sample10-large.png#lightbox)
3. Verificare che il file binario dell'app sia in modalità sandbox:
    - Avviare l'app Terminal.
    - Passare alla directory `bin` applicazioni.
    - Eseguire questo comando: `codesign -dvvv --entitlements :- executable_path` (dove `executable_path` è il percorso dell'applicazione):

    [![Verifica dell'app dalla riga di comando](sandboxing-images/sample11.png "Verifica dell'app dalla riga di comando")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Debug di un'app in modalità sandbox

Il debugger si connette alle app Novell. Mac tramite TCP. il che significa che, per impostazione predefinita, quando si Abilita il sandboxing, non è in grado di connettersi all'app, pertanto se si tenta di eseguire l'app senza le autorizzazioni corrette abilitate, viene ricevuto un errore *"Impossibile connettersi al debugger"* .

[![Impostazione delle opzioni obbligatorie](sandboxing-images/debug01.png "Impostazione delle opzioni obbligatorie")](sandboxing-images/debug01-large.png#lightbox)

L'autorizzazione **Consenti connessioni di rete (client) in uscita** è quella necessaria per il debugger. questa operazione consente di eseguire il debug in modo normale. Poiché non è possibile eseguire il debug senza di esso, è stata aggiornata la destinazione `CompileEntitlements` per `msbuild` per aggiungere automaticamente tale autorizzazione ai diritti per qualsiasi app creata in modalità sandbox solo per le compilazioni di debug. Le build di rilascio devono usare i diritti specificati nel file dei diritti, senza modifiche.

### <a name="resolving-an-app-sandbox-violation"></a>Risoluzione della violazione di una sandbox dell'app

Si verifica una violazione sandbox dell'app se un'applicazione Novell. Mac in modalità sandbox ha tentato di accedere a una risorsa che non è consentita in modo esplicito. La visualizzazione Web, ad esempio, non è più in grado di visualizzare il sito Web Apple.

L'origine più comune delle violazioni sandbox dell'app si verifica quando le impostazioni dei diritti specificate in Visual Studio per Mac non corrispondono ai requisiti dell'applicazione. Ancora una volta, tornando all'esempio, i diritti di connessione di rete mancanti che impediscono il funzionamento della visualizzazione Web.

#### <a name="discovering-app-sandbox-violations"></a>Individuazione delle violazioni sandbox dell'app

Se si sospetta che si verifichi una violazione della sandbox dell'app nell'applicazione Novell. Mac, il modo più rapido per individuare il problema consiste nell'usare l'app **console** .

Procedere come descritto di seguito:

1. Compilare l'app in questione ed eseguirla da Visual Studio per Mac.
2. Aprire l'applicazione **console** (da `/Applications/Utilties/`).
3. Selezionare **tutti i messaggi** nella barra laterale e immettere `sandbox` nella ricerca:

    [![Esempio di un problema di sandboxing nella console](sandboxing-images/resolve01.png "Esempio di un problema di sandboxing nella console")](sandboxing-images/resolve01-large.png#lightbox)

Per l'app di esempio precedente, è possibile notare che kernal blocca il traffico `network-outbound` a causa della sandbox dell'app, perché non è stato richiesto.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Correzione delle violazioni sandbox dell'app con diritti

Ora che abbiamo visto come trovare le violazioni di sandboxing delle app, vediamo come risolverle regolando i diritti dell'applicazione.

Procedere come descritto di seguito:

1. Nel **riquadro della soluzione**fare doppio clic sul file **titles. plist** per aprirlo per la modifica.
2. Nella sezione **diritti** selezionare la casella di controllo **Consenti connessioni di rete in uscita (client)** :

    [![Modifica dei diritti](sandboxing-images/sign17.png "Modifica dei diritti")](sandboxing-images/sign17-large.png#lightbox)
3. Salvare le modifiche apportate all'applicazione.

Se si eseguono le operazioni descritte in precedenza per l'app di esempio, quindi la si compila ed Esegui, il contenuto Web verrà visualizzato come previsto.

## <a name="the-app-sandbox-in-depth"></a>Approfondimento sulla sandbox dell'app

I meccanismi di controllo degli accessi offerti dalla sandbox dell'app sono pochi e facili da comprendere. Tuttavia, il modo in cui l'app sandbox verrà adottata da ogni app è univoca e basata sui requisiti dell'app.

Dato il massimo sforzo necessario per proteggere l'applicazione Novell. Mac dal codice dannoso, è necessario che sia presente una sola vulnerabilità nell'app, o in una delle librerie o nei Framework utilizzati, per ottenere il controllo delle interazioni dell'app con il sistema.

Il sandbox dell'app è stato progettato per impedire l'acquisizione (o limitare il danno che può causare) consentendo di specificare le interazioni desiderate dell'applicazione con il sistema. Il sistema concederà solo l'accesso alla risorsa richiesta dall'applicazione per svolgere il proprio lavoro e nient'altro.

Quando si progetta per la sandbox dell'app, si sta progettando uno scenario peggiore. Se l'applicazione viene compromessa da codice dannoso, si limita ad accedere solo ai file e alle risorse nell'ambiente sandbox dell'app.

### <a name="entitlements-and-system-resource-access"></a>Diritti e accesso alle risorse di sistema

Come illustrato sopra, un'applicazione Novell. Mac a cui non è stato eseguito il sandbox riceve i diritti completi e l'accesso dell'utente che esegue l'app. Se compromessa da codice dannoso, un'app non protetta può fungere da agente per comportamenti ostili, con un'ampia gamma di potenziali rischi.

Abilitando la sandbox dell'app, si rimuove tutti i privilegi tranne un set minimo di privilegi, che vengono quindi riabilitati solo in base alla necessità usando i diritti dell'app Novell. Mac.

Per modificare le risorse della sandbox dell'applicazione, modificare il file con **estensione plist** e controllare o selezionare i diritti richiesti dalle caselle a discesa degli editor:

[![Modifica dei diritti](sandboxing-images/sign17.png "Modifica dei diritti")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Directory del contenitore e accesso file system

Quando l'applicazione Novell. Mac adotta la sandbox dell'app, può accedere ai percorsi seguenti:

- **Directory del contenitore dell'app** : alla prima esecuzione, il sistema operativo crea una directory speciale del _contenitore_ in cui tutte le relative risorse sono disponibili, a cui solo può accedere. L'app avrà accesso completo in lettura/scrittura alla directory.
- **Directory del contenitore del gruppo di app** : è possibile concedere all'app l'accesso a uno o più _contenitori di gruppi_ condivisi tra le app nello stesso gruppo.
- **File specificati dall'utente** : l'applicazione ottiene automaticamente l'accesso ai file che vengono aperti o trascinati in modo esplicito e rilasciati nell'applicazione dall'utente.
- **Elementi correlati** : con i diritti appropriati, l'applicazione può accedere a un file con lo stesso nome ma con un'estensione diversa. Ad esempio, un documento che è stato salvato come file di `.txt` e `.pdf`.
- **Directory temporanee, directory degli strumenti da riga di comando e posizioni specifiche leggibili in tutto il mondo** : l'app dispone di diversi livelli di accesso ai file in altri percorsi ben definiti, come specificato dal sistema.

#### <a name="the-app-container-directory"></a>Directory del contenitore di app

Una directory del contenitore dell'applicazione Novell. Mac presenta le caratteristiche seguenti:

- Si trova in una posizione nascosta nella home directory dell'utente (in genere `~Library/Containers`) ed è possibile accedervi con la funzione `NSHomeDirectory` (vedere di seguito) all'interno dell'applicazione. Poiché si trova nella Home Directory, ogni utente otterrà il proprio contenitore per l'app.
- L'app dispone di accesso in lettura/scrittura illimitato alla directory del contenitore e a tutte le relative sottodirectory e file al suo interno.
- La maggior parte delle API di ricerca dei percorsi di macOS sono relative al contenitore dell'app. Ad esempio, il contenitore avrà la propria **libreria** (a cui si accede tramite `NSLibraryDirectory`), il **supporto dell'applicazione** e le sottodirectory delle **Preferenze** .
- macOS stabilisce e impone la connessione tra l'app e il relativo contenitore tramite la firma del codice. Anche se un'altra app prova a eseguire lo spoofing dell'app usando l' **identificatore del bundle**, non sarà in grado di accedere al contenitore a causa della firma del codice.
- Il contenitore non è per i file generati dall'utente. È invece per i file usati dall'applicazione, ad esempio database, cache o altri tipi specifici di dati.
- Per i tipi di app da _scarpe_ (ad esempio, app Foto di Apple), il contenuto dell'utente viene inserito nel contenitore.

> [!IMPORTANT]
> Sfortunatamente, Novell. Mac non dispone ancora della copertura API del 100% (a differenza di Novell. iOS), di conseguenza non è stato eseguito il mapping dell'API `NSHomeDirectory` nella versione corrente di Novell. Mac.

Come soluzione temporanea, è possibile usare il codice seguente:

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>Directory del contenitore del gruppo di app

A partire da Mac macOS 10.7.5 (e versioni successive), un'applicazione può usare il diritto `com.apple.security.application-groups` per accedere a un contenitore condiviso comune a tutte le applicazioni del gruppo. È possibile usare questo contenitore condiviso per contenuti non utente, ad esempio database o altri tipi di file di supporto, ad esempio le cache.

I contenitori di gruppo vengono aggiunti automaticamente al contenitore sandbox di ogni app, se fanno parte di un gruppo, e vengono archiviati in `~/Library/Group Containers/<application-group-id>`. L'ID del gruppo _deve_ iniziare con l'ID del team di sviluppo e un punto, ad esempio:

```plist
<team-id>.com.company.<group-name>
```

Per ulteriori informazioni, vedere la pagina relativa all' [aggiunta di un'applicazione a un gruppo di applicazioni](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) in riferimento alla [chiave diritto](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Accesso a PowerBox e file system all'esterno del contenitore di app

Un'applicazione Novell. Mac in modalità sandbox può accedere a posizioni file system all'esterno del relativo contenitore nei modi seguenti:

- Alla direzione specifica dell'utente (tramite finestre di dialogo Apri e Salva o altri metodi, ad esempio il trascinamento della selezione).
- Utilizzando i diritti per percorsi di file system specifici, ad esempio `/bin` o `/usr/lib`.
- Quando il file system percorso si trova in alcune directory che sono leggibili in tutto il mondo, ad esempio la condivisione.

_PowerBox_ è la tecnologia di sicurezza MacOS che interagisce con l'utente per espandere i diritti di accesso ai file dell'app Novell. Mac in modalità sandbox. Powerbox non dispone di API, ma viene attivato in modo trasparente quando l'app chiama un `NSOpenPanel` o `NSSavePanel`. L'accesso PowerBox viene abilitato tramite i diritti impostati per l'applicazione Novell. Mac.

Quando un'app in modalità sandbox Visualizza una finestra di dialogo Apri o Salva, la finestra viene presentata da PowerBox (e non da AppKit) e pertanto ha accesso a qualsiasi file o directory a cui l'utente ha accesso.

Quando un utente seleziona un file o una directory dalla finestra di dialogo Apri o Salva oppure lo trascina sull'icona dell'app, PowerBox aggiunge il percorso associato alla sandbox dell'app.

Inoltre, il sistema consente automaticamente le operazioni seguenti a un'app creata mediante sandbox:

- Connessione a un metodo di input di sistema.
- Chiamare i servizi selezionati dall'utente dal menu **Servizi** (solo per i servizi contrassegnati come _sicuri per le app sandbox_ dal provider di servizi).
- Apri file scegliere dall'utente dal menu **Apri recenti** .
- Usare copia & incollare tra le altre applicazioni.
- Leggi i file dalle seguenti posizioni leggibili in tutto il mondo:
  - `/bin`
  - `/sbin`
  - `/usr/bin`
  - `/usr/lib`
  - `/usr/sbin`
  - `/usr/share`
  - `/System`
- Leggere e scrivere i file nelle directory create da `NSTemporaryDirectory`.

Per impostazione predefinita, i file aperti o salvati da un'app Novell. Mac in modalità sandbox rimangono accessibili fino a quando l'app non viene terminata, a meno che il file non sia ancora aperto quando l'app si chiude. I file aperti verranno ripristinati automaticamente nella sandbox dell'app tramite la funzionalità di riattivazione macOS al successivo avvio dell'app.

Per rendere persistente i file che si trovano all'esterno del contenitore di un'app Novell. Mac, usare i segnalibri con ambito di sicurezza (vedere di seguito).

#### <a name="related-items"></a>Elementi correlati

Il sandbox dell'app consente a un'app di accedere a elementi correlati con lo stesso nome file, ma estensioni diverse. La funzionalità è costituita da due parti: a) un elenco di estensioni correlate nel codice del file di `Info.plst` dell'app, b) per indicare alla sandbox cosa verrà eseguita dall'app con questi file.

Esistono due scenari in cui è opportuno:

1. L'app deve essere in grado di salvare una versione diversa del file (con una nuova estensione). Ad esempio, l'esportazione di un file di `.txt` in un file di `.pdf`. Per gestire questa situazione, è necessario usare un `NSFileCoordinator` per accedere al file. Per prima cosa si chiamerà il metodo `WillMove(fromURL, toURL)`, si sposta il file nella nuova estensione e quindi si chiama `ItemMoved(fromURL, toURL)`.
2. L'app deve aprire un file principale con un'estensione e diversi file di supporto con estensioni diverse. Ad esempio, un filmato e un file di sottotitoli. Usare un `NSFilePresenter` per accedere al file secondario. Fornire il file principale alla proprietà `PrimaryPresentedItemURL` e il file secondario alla proprietà `PresentedItemURL`. Quando viene aperto il file principale, chiamare il metodo `AddFilePresenter` della classe `NSFileCoordinator` per registrare il file secondario.

In entrambi gli scenari, il file **info. plist** dell'app deve dichiarare i tipi di documento che possono essere aperti dall'app. Per qualsiasi tipo di file, aggiungere la `NSIsRelatedItemType` (con un valore di `YES`) alla relativa voce nella matrice di `CFBundleDocumentTypes`.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Comportamento della finestra di dialogo Apri e Salva con le app create mediante sandbox

I limiti seguenti vengono inseriti nel `NSOpenPanel` e `NSSavePanel` quando vengono chiamati da un'app Novell. Mac in modalità sandbox:

- Non è possibile richiamare il pulsante **OK** a livello di codice.
- Non è possibile modificare a livello di codice la selezione di un utente in un `NSOpenSavePanelDelegate`.

Sono inoltre disponibili le seguenti modifiche di ereditarietà:

- **App non in modalità sandbox**  -  `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Segnalibri con ambito di sicurezza e accesso alle risorse persistente

Come indicato in precedenza, un'applicazione Novell. Mac in modalità sandbox può accedere a un file o a una risorsa all'esterno del relativo contenitore mediante l'interazione diretta dell'utente (fornita da PowerBox). Tuttavia, l'accesso a queste risorse non viene reso automaticamente persistente tra i lanci delle app o i riavvii del sistema.

Con i _segnalibri con ambito di sicurezza_, un'applicazione Novell. Mac in modalità sandbox può mantenere la finalità dell'utente e mantenere l'accesso alle risorse specificate dopo il riavvio di un'app.

#### <a name="security-scoped-bookmark-types"></a>Tipi di segnalibro con ambito di sicurezza

Quando si usano segnalibri con ambito di sicurezza e l'accesso alle risorse persistente, sono disponibili due casi d'uso della Sistina:

- **Un segnalibro con ambito di app fornisce accesso permanente a una cartella o a un file specificato dall'utente.**

    Ad esempio, se l'applicazione Novell. Mac in modalità sandbox consente a di aprire un documento esterno per la modifica (usando un `NSOpenPanel`), l'app può creare un segnalibro con ambito di app in modo da poter accedere nuovamente allo stesso file in futuro.
- **Un segnalibro con ambito documento fornisce un documento specifico accesso permanente a un file secondario.**

Ad esempio, un'applicazione di modifica video che consente di creare un file di progetto che ha accesso alle singole immagini, clip video e file audio che verranno combinati in un unico film.

Quando l'utente importa un file di risorse nel progetto (tramite un `NSOpenPanel`), l'app crea un segnalibro con ambito documento per l'elemento archiviato nel progetto, in modo che il file sia sempre accessibile all'app.

Un segnalibro con ambito documento può essere risolto da qualsiasi applicazione in grado di aprire i dati del segnalibro e il documento stesso. Questo supporta la portabilità, consentendo all'utente di inviare i file di progetto a un altro utente e di usare anche tutti i segnalibri.

> [!IMPORTANT]
> Un segnalibro con ambito documento può puntare _solo_ a un singolo file e non a una cartella e tale file non può trovarsi in un percorso utilizzato dal sistema, ad esempio `/private` o `/Library`.

#### <a name="using-security-scoped-bookmarks"></a>Uso di segnalibri con ambito di sicurezza

Per usare uno dei due tipi di segnalibro con ambito di protezione, è necessario eseguire i passaggi seguenti:

1. **Impostare i diritti appropriati nell'app Novell. Mac che deve usare i segnalibri con ambito di sicurezza** . per i segnalibri con ambito di app, impostare la chiave di diritti `com.apple.security.files.bookmarks.app-scope` su `true`. Per i segnalibri con ambito documento, impostare la chiave di autorizzazione `com.apple.security.files.bookmarks.document-scope` su `true`.
2. **Creare un segnalibro con ambito di sicurezza** : questa operazione viene eseguita per qualsiasi file o cartella a cui l'utente ha concesso l'accesso (ad esempio, tramite `NSOpenPanel`), che l'app dovrà avere accesso permanente a. Usare il metodo `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` della classe `NSUrl` per creare il segnalibro.
3. **Risolvere il segnalibro con ambito di sicurezza** : quando l'app deve accedere di nuovo alla risorsa, ad esempio dopo il riavvio, sarà necessario risolvere il segnalibro in un URL con ambito di sicurezza. Usare il metodo `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` della classe `NSUrl` per risolvere il segnalibro.
4. **Notificare in modo esplicito al sistema che si vuole accedere al file dall'URL con ambito di sicurezza** . questo passaggio deve essere eseguito immediatamente dopo aver ottenuto l'URL con ambito di sicurezza precedente o, quando si vuole riottenere l'accesso alla risorsa dopo avere rinunciare all'accesso. Chiamare il metodo `StartAccessingSecurityScopedResource ()` della classe `NSUrl` per iniziare ad accedere a un URL con ambito di sicurezza.
5. **Notificare in modo esplicito al sistema che l'utente ha eseguito l'accesso al file dall'URL con ambito di protezione** : appena possibile, è necessario informare il sistema quando l'app non necessita più dell'accesso al file, ad esempio se l'utente la chiude. Chiamare il metodo `StopAccessingSecurityScopedResource ()` della classe `NSUrl` per arrestare l'accesso a un URL con ambito di sicurezza.

Dopo aver ceduto l'accesso a una risorsa, è necessario tornare al passaggio 4 per ristabilire l'accesso. Se l'app Novell. Mac viene riavviata, è necessario tornare al passaggio 3 e risolvere di nuovo il segnalibro.

> [!IMPORTANT]
> Se non si rilascia l'accesso alle risorse URL con ambito di sicurezza, un'app Novell. Mac perderà le risorse del kernel. Di conseguenza, l'app non sarà più in grado di aggiungere percorsi di file system al relativo contenitore fino a quando non viene riavviato.

### <a name="the-app-sandbox-and-code-signing"></a>Sandbox dell'app e firma del codice

Dopo aver abilitato l'app sandbox e aver abilitato i requisiti specifici per l'app Novell. Mac (tramite diritti), è necessario firmare il progetto affinché il sandboxing venga applicato. È necessario eseguire la firma del codice perché i diritti necessari per il sandboxing dell'app sono collegati alla firma dell'app.

macOS impone un collegamento tra il contenitore di un'app e la firma del codice, in questo modo nessun'altra applicazione può accedere a tale contenitore anche se esegue lo spoofing dell'ID bundle delle app. Questo meccanismo funziona nel modo seguente:

1. Quando il sistema crea il contenitore dell'app, viene impostato un _elenco di controllo di accesso_ (ACL) in tale contenitore. La voce di controllo di accesso iniziale nell'elenco contiene il _requisito designato_ dell'app (Dr), che descrive il modo in cui le versioni future dell'app possono essere riconosciute (dopo l'aggiornamento).
2. Ogni volta che viene avviata un'app con lo stesso ID bundle, il sistema verifica che la firma del codice dell'app corrisponda ai requisiti designati specificati in una delle voci nell'ACL del contenitore. Se il sistema non trova una corrispondenza, impedisce l'avvio dell'app.

La firma del codice funziona nei modi seguenti:

1. Prima di creare il progetto Novell. Mac, ottenere un certificato di sviluppo, un certificato di distribuzione e un certificato ID sviluppatore dal portale Apple Developer.
2. Quando Mac App Store distribuisce l'app Novell. Mac, viene firmato con una firma del codice Apple.

Quando si esegue il test e il debug, si userà una versione dell'applicazione Novell. Mac firmata (che verrà usata per creare il contenitore dell'app). In seguito, se si vuole testare o installare la versione dall'App Store di Apple, viene firmata con la firma Apple e non verrà avviata (perché non ha la stessa firma del codice del contenitore dell'app originale). In questa situazione si otterrà una segnalazione di arresto anomalo simile alla seguente:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Per risolvere questo problema, è necessario modificare la voce ACL in modo che punti alla versione firmata Apple dell'app.

Per altre informazioni su come creare e scaricare i profili di provisioning necessari per il sandboxing, vedere la sezione relativa alla [firma e al provisioning dell'app](#Signing_and_Provisioning_the_App) precedente.

#### <a name="adjusting-the-acl-entry"></a>Modifica della voce ACL

Per consentire l'esecuzione della versione firmata Apple dell'app Novell. Mac, seguire questa procedura:

1. Aprire l'app Terminal (in `/Applications/Utilities`).
2. Aprire una finestra del Finder per la versione con firma Apple dell'app Novell. Mac.
3. Digitare `asctl container acl add -file` nella finestra del terminale.
4. Trascinare l'icona dell'app Novell. Mac dalla finestra di ricerca e rilasciarla nella finestra del terminale.
5. Il percorso completo del file verrà aggiunto al comando nel terminale.
6. Premere **invio** per eseguire il comando.

L'elenco di controllo di accesso del contenitore contiene ora i requisiti di codice designati per entrambe le versioni dell'app Novell. Mac e macOS consentirà l'esecuzione di entrambe le versioni.

#### <a name="display-a-list-of-acl-code-requirements"></a>Visualizza un elenco di requisiti del codice ACL

È possibile visualizzare un elenco dei requisiti del codice nell'ACL di un contenitore eseguendo le operazioni seguenti:

1. Aprire l'app Terminal (in `/Applications/Utilities`).
2. Digitare `asctl container acl list -bundle <container-name>`.
3. Premere **invio** per eseguire il comando.

Il `<container-name>` è in genere l'identificatore del bundle per l'applicazione Novell. Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Progettazione di un'app Novell. Mac per l'app sandbox

Quando si progetta un'app Novell. Mac per il sandbox dell'app, è necessario seguire un flusso di lavoro comune. Ciò premesso, le specifiche di implementazione della sandboxing in un'applicazione saranno univoche per la funzionalità dell'app specificata.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Sei passaggi per l'adozione della sandbox dell'app

La progettazione di un'app Novell. Mac per la sandbox dell'app in genere è costituita dai passaggi seguenti:

1. Determinare se l'app è adatta per la sandboxing.
2. Progettare una strategia di sviluppo e distribuzione.
3. Risolvere eventuali incompatibilità dell'API.
4. Applicare i diritti di sandbox dell'app richiesti al progetto Novell. Mac.
5. Aggiungere la separazione dei privilegi con XPC.
6. Implementare una strategia di migrazione.

> [!IMPORTANT]
> È necessario non solo eseguire la sandbox del file eseguibile principale nel bundle dell'app, ma anche ogni app helper o strumento incluso in tale bundle. Questa operazione è necessaria per qualsiasi app distribuita da Mac App Store e, se possibile, deve essere eseguita per qualsiasi altra forma di distribuzione di app.

Per un elenco di tutti i file binari eseguibili in un bundle dell'app Novell. Mac, digitare il comando seguente nel terminale:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Dove `[Your-App-Bundle]` è il nome e il percorso del bundle dell'applicazione.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinare se un'app Novell. Mac è adatta per la sandboxing

La maggior parte delle app Novell. Mac è completamente compatibile con l'app sandbox e pertanto è adatta per la sandboxing. Se l'app richiede un comportamento non consentito dall'app sandbox, è consigliabile prendere in considerazione un approccio alternativo.

Se l'app richiede uno dei comportamenti seguenti, non è compatibile con l'app sandbox:

- **Servizi di autorizzazione** : con l'app sandbox non è possibile usare le funzioni descritte in informazioni di [riferimento su servizi di autorizzazione C](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **API di accessibilità** : non è possibile eseguire la sandbox di app assistive quali utilità per la lettura dello schermo o app che controllano altre applicazioni.
- **Inviare eventi Apple a app arbitrarie** : se l'app richiede l'invio di eventi Apple a un'app arbitraria sconosciuta, non è possibile eseguire la sandbox. Per un elenco noto di App denominate, l'app può comunque essere creata tramite sandbox e i diritti dovranno includere l'elenco delle app chiamate.
- **Inviare dizionari di informazioni utente in notifiche distribuite ad altre attività** : con l'app sandbox non è possibile includere un dizionario `userInfo` durante la pubblicazione in un oggetto `NSDistributedNotificationCenter` per la messaggistica di altre attività.
- **Caricare le estensioni del kernel** : il caricamento delle estensioni del kernel non è consentito dalla sandbox dell'app.
- **Simulare l'input dell'utente nelle finestre di dialogo di apertura e salvataggio** : la modifica a livello di codice delle finestre di dialogo Apri o Salva per simulare o modificare l'input dell'utente non è consentita dalla sandbox dell'app.
- **Accesso o impostazione delle preferenze in altre app** : la modifica delle impostazioni di altre app non è consentita dalla sandbox dell'app.
- **Configurazione delle impostazioni di rete** : la modifica delle impostazioni di rete non è consentita dalla sandbox dell'app.
- **Terminazione di altre app** : la sandbox dell'app impedisce l'uso di `NSRunningApplication` per terminare altre app.

### <a name="resolving-api-incompatibilities"></a>Risoluzione delle incompatibilità API

Quando si progetta un'app Novell. Mac per la sandbox dell'app, è possibile che si verifichino incompatibilità con l'uso di alcune API macOS.

Di seguito sono riportati alcuni problemi comuni che è possibile eseguire per risolverli:

- **Apertura, salvataggio e rilevamento dei documenti** : se si gestiscono documenti con qualsiasi tecnologia diversa da `NSDocument`, è necessario passare a tale documento a causa del supporto incorporato per la sandbox dell'app. `NSDocument` funziona automaticamente con PowerBox e fornisce il supporto per mantenere i documenti all'interno della sandbox se vengono spostati dall'utente in Finder.
- **Mantenere l'accesso alle risorse del file System** : se l'app Novell. Mac dipende dall'accesso permanente alle risorse esterne al contenitore, usare i segnalibri con ambito di sicurezza per mantenere l'accesso.
- **Creare un elemento di accesso per un'app** : con l'app sandbox, non è possibile creare un elemento di accesso usando `LSSharedFileList` né modificare lo stato dei servizi di avvio usando `LSRegisterURL`. Usare la funzione `SMLoginItemSetEnabled` come descritto in mele [aggiunta di elementi di accesso tramite la documentazione del Framework di gestione dei servizi](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) .
- **Accesso ai dati utente** : se si usano funzioni POSIX come `getpwuid` per ottenere la home directory dell'utente dai servizi directory, provare a usare i simboli Cocoa o Core Foundation, ad esempio `NSHomeDirectory`.
- **Accesso alle preferenze di altre app** : poiché la sandbox dell'app indirizza le API di ricerca del percorso al contenitore dell'app, la modifica delle preferenze viene eseguita all'interno del contenitore e l'accesso alle preferenze di un'altra app non è consentito.
- **Uso del video HTML5 embedded in visualizzazioni Web** : se l'app Novell. Mac usa WebKit per riprodurre video HTML5 incorporati, è necessario collegare l'app anche a AV Foundation Framework. Il sandbox dell'app impedisce a CoreMedia di riprodurre questi video in caso contrario.

### <a name="applying-required-app-sandbox-entitlements"></a>Applicazione dei diritti di sandbox app richiesti

Sarà necessario modificare i diritti per qualsiasi applicazione Novell. Mac che si vuole eseguire nell'app sandbox e selezionare la casella di controllo **Abilita sandboxing dell'app** .

In base alla funzionalità dell'app, potrebbe essere necessario abilitare altri diritti per accedere alle funzionalità o alle risorse del sistema operativo. Il sandboxing delle app funziona meglio quando si riducono i diritti richiesti al minimo necessario per eseguire l'app, in modo da abilitare solo i diritti in modo casuale.

Per determinare i diritti richiesti da un'app Novell. Mac, seguire questa procedura:

1. Abilitare l'app sandbox ed eseguire l'app Novell. Mac.
2. Eseguire le funzionalità dell'app.
3. Aprire l'app console (disponibile in `/Applications/Utilities`) e cercare `sandboxd` violazioni nel registro **tutti i messaggi** .
4. Per ogni violazione di `sandboxd`, risolvere il problema usando il contenitore dell'app anziché altri percorsi di file system o applicare diritti sandbox per l'app per consentire l'accesso alle funzionalità limitate del sistema operativo.
5. Eseguire nuovamente e testare di nuovo tutte le funzionalità dell'app Novell. Mac.
6. Ripetere fino a quando non sono state risolte tutte le violazioni del `sandboxd`.

### <a name="add-privilege-separation-using-xpc"></a>Aggiungere la separazione dei privilegi con XPC

Quando si sviluppa un'app Novell. Mac per la sandbox dell'app, esaminare i comportamenti dell'app in termini di privilegi e accesso, quindi considerare la possibilità di separare le operazioni ad alto rischio nei propri servizi XPC.

Per ulteriori informazioni, vedere la pagina relativa alla creazione di servizi e daemon di [Servizi XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) e alla [Guida alla programmazione dei servizi](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)di Apple.

### <a name="implement-a-migration-strategy"></a>Implementare una strategia di migrazione

Se si sta rilasciando una nuova versione di un'applicazione Novell. Mac in modalità sandbox che in precedenza non era sandbox, è necessario assicurarsi che gli utenti correnti dispongano di un percorso di aggiornamento uniforme.

 Per informazioni dettagliate su come implementare un manifesto di migrazione del contenitore, vedere la documentazione relativa [alla migrazione di un'app in una sandbox di](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) Apple.

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio la sandboxing di un'applicazione Novell. Mac. In primo luogo, è stata creata un'app Novell. Mac semplicemente per visualizzare le nozioni di base della sandbox dell'app. Successivamente, è stato illustrato come risolvere le violazioni della sandbox. È stata quindi esaminata in dettaglio l'app sandbox e infine è stata esaminata la progettazione di un'app Novell. Mac per il sandbox dell'app.

## <a name="related-links"></a>Collegamenti correlati

- [Pubblicazione nell'App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Informazioni su sandbox app](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemi comuni di sandboxing delle app](https://developer.apple.com/library/content/qa/qa1773/_index.html)
