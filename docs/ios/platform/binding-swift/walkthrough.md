---
title: 'Procedura dettagliata: associare una libreria Swift di iOS'
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'associazione Novell. iOS per un Framework Swift esistente, Gigya. Vengono trattati argomenti come la compilazione di un Framework Swift, l'associazione e l'uso dell'associazione in un'applicazione Novell. iOS.
ms.prod: xamarin
ms.assetid: B563ADE9-D0E3-4BC3-A288-66D2296BE706
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: b650f86a1bba62d5db7463875de3398db9c33842
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292592"
---
# <a name="walkthrough-bind-an-ios-swift-library"></a>Procedura dettagliata: associare una libreria Swift di iOS

Novell consente agli sviluppatori di dispositivi mobili di creare esperienze per dispositivi mobili native multipiattaforma C#usando Visual Studio e. È possibile usare i componenti di iOS Platform SDK predefiniti. In molti casi, tuttavia, si vogliono usare anche gli SDK di terze parti sviluppati per la piattaforma, che Novell consente di eseguire tramite binding. Per incorporare un Framework Objective-C di terze parti nell'applicazione Novell. iOS, è necessario creare un'associazione Novell. iOS per poterla usare nelle applicazioni.

La piattaforma iOS, oltre ai linguaggi e agli strumenti nativi, è in continua evoluzione e Swift è una delle aree più dinamiche del mondo di sviluppo iOS. Sono disponibili diversi SDK di terze parti, che sono già stati migrati da Objective-C a Swift e presentano nuove problemi. Anche se il processo di associazione Swift è simile a Objective-C, richiede passaggi aggiuntivi e impostazioni di configurazione per compilare ed eseguire correttamente un'applicazione Novell. iOS accettabile per AppStore.

Lo scopo di questo documento è quello di definire un approccio di alto livello per risolvere questo scenario e fornire una guida dettagliata con un semplice esempio.

## <a name="background"></a>Background

Swift è stato introdotto inizialmente da Apple nel 2014 ed è ora disponibile nella versione 5,1 con l'adozione da parte di Framework di terze parti che crescono rapidamente. Sono disponibili alcune opzioni per l'associazione di un Framework Swift e questo documento descrive l'approccio con l'intestazione di interfaccia generata da Objective-C. L'intestazione viene creata automaticamente dagli strumenti di Xcode quando viene creato un Framework e viene usata come modo per comunicare dal mondo gestito al mondo Swift.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario:

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started#installing-objective-sharpie)
- [Interfaccia](https://docs.microsoft.com/appcenter/test-cloud/) della riga di comando AppCenter (facoltativo)

## <a name="build-a-native-library"></a>Creare una libreria nativa

Il primo passaggio consiste nel creare un Framework Swift nativo con l'intestazione Objective-C abilitata. Il Framework viene in genere fornito da uno sviluppatore di terze parti e include l'intestazione incorporata nel pacchetto nella directory seguente: **\<frameworkname >. Framework/Headers/\<frameworkname >-Swift. h**.

Questa intestazione espone le interfacce pubbliche, che verranno usate per creare i metadati di binding Novell. iOS e C# generare classi che espongono i membri Swift Framework. Se l'intestazione non esiste o presenta un'interfaccia pubblica incompleta (ad esempio, non sono visibili classi/membri) sono disponibili due opzioni:

- Aggiornare il codice sorgente Swift per generare l'intestazione e contrassegnare i membri obbligatori con `@objc` attributo
- Creazione di un Framework proxy in cui è possibile controllare l'interfaccia pubblica e il proxy di tutte le chiamate al Framework sottostante

In questa esercitazione, il secondo approccio viene descritto in quanto dispone di un numero minore di dipendenze dal codice sorgente di terze parti, che non è sempre disponibile. Un altro motivo per evitare il primo approccio è il lavoro aggiuntivo necessario per supportare le modifiche future del Framework. Quando si inizia ad aggiungere modifiche al codice sorgente di terze parti, si è responsabili del supporto di tali modifiche e potenzialmente di Unione con tutti gli aggiornamenti futuri.

Ad esempio, in questa esercitazione viene creata un'associazione per [Gigya Swift SDK](https://developers.gigya.com/display/GD/Swift+SDK) :

1. Aprire Xcode e creare un nuovo Framework Swift, che sarà un proxy tra il codice Novell. iOS e il Framework Swift di terze parti. Fare clic su **File > nuovo progetto >** e seguire i passaggi della procedura guidata:

    ![progetto di creazione Framework di Xcode](walkthrough-images/xcode-create-framework-project.png)

    ![progetto Framework nome Xcode](walkthrough-images/xcode-name-framework-project.png)

1. Scaricare [Gigya Framework](https://developers.gigya.com/display/GD/Swift+SDK) dal sito Web per sviluppatori e decomprimerlo. Al momento della stesura della scrittura, la versione più recente è [Gigya Swift SDK 1.0.9](https://downloads.gigya.com/predownload?fileName=Swift-Core-framework-1.0.9.zip)

1. Selezionare il **SwiftFrameworkProxy** da Esplora file di progetto e quindi selezionare la scheda generale.

1. Trascinare e rilasciare il pacchetto **Gigya. Framework** nell'elenco di Framework e librerie di Xcode nella scheda Generale selezionare l'opzione **Copia elementi se necessario** durante l'aggiunta del Framework:

    ![Framework di copia Xcode](walkthrough-images/xcode-copy-framework.png)

    Verificare che il Framework Swift sia stato aggiunto al progetto; in caso contrario, le opzioni seguenti non saranno disponibili.

1. Verificare che l'opzione **non incorporare** sia selezionata, che verrà controllata in seguito manualmente:

    [opzione ![Xcode donotembed](walkthrough-images/xcode-donotembed-option.png)](walkthrough-images/xcode-donotembed-option.png#lightbox)

1. Assicurarsi che l'opzione impostazioni di compilazione **includa sempre le librerie standard SWIFT**, che include le librerie Swift con il Framework è impostato su No. In seguito verrà controllata manualmente, che Swift dylib sono incluse nel pacchetto finale:

    [opzione ![Xcode always Embed](walkthrough-images/xcode-alwaysembedfalse-option.png)](walkthrough-images/xcode-alwaysembedfalse-option.png#lightbox)

1. Verificare che l'opzione **Abilita bitcode** sia impostata su **No**. Al momento, Novell. iOS non include bitcode mentre Apple richiede che tutte le librerie supportino le stesse architetture:

    [opzione ![Xcode Enable bitcode false](walkthrough-images/xcode-enablebitcodefalse-option.png)](walkthrough-images/xcode-enablebitcodefalse-option.png#lightbox)

    È possibile verificare che l'opzione bitcode del Framework risultante sia disabilitata eseguendo il comando del terminale seguente sul Framework:

    ```bash
    otool -l SwiftFrameworkProxy.framework/SwiftFrameworkProxy | grep __LLVM
    ```

    L'output deve essere vuoto. in caso contrario, si vuole rivedere le impostazioni del progetto per la configurazione specifica.

1. Verificare che l'opzione **nome intestazione dell'interfaccia generata da Objective-C** sia abilitata e specifichi un nome di intestazione. Il nome predefinito è **\<frameworkname >-Swift. h**:

    [opzione ![Xcode objectice-c Enabled](walkthrough-images/xcode-objcheaderenabled-option.png)](walkthrough-images/xcode-objcheaderenabled-option.png#lightbox)

1. Esporre i metodi desiderati e contrassegnarli con `@objc` attributo e applicare regole aggiuntive definite di seguito. Se si compila il Framework senza questo passaggio, l'intestazione Objective-C generata sarà vuota e Novell. iOS non sarà in grado di accedere ai membri Swift Framework. Esporre la logica di inizializzazione per Gigya Swift SDK sottostante creando un nuovo file Swift **SwiftFrameworkProxy. Swift** e definendo il codice seguente:

    ```swift
    import Foundation
    import UIKit
    import Gigya

    @objc(SwiftFrameworkProxy)
    public class SwiftFrameworkProxy : NSObject {

        @objc
        public func initFor(apiKey: String) -> String {
            Gigya.sharedInstance().initFor(apiKey: apiKey)
            let gigyaDomain = Gigya.sharedInstance().config.apiDomain
            let result = "Gigya initialized with domain: \(gigyaDomain)"
            return result
        }
    }
    ```

    Di seguito sono riportate alcune note importanti sul codice riportato sopra:

    - Importare il modulo Gigya da Gigya SDK di terze parti originale e ora può accedere a qualsiasi membro del Framework.
    - Contrassegnare la classe SwiftFrameworkProxy con l'attributo `@objc` che specifica un nome; in caso contrario, verrà generato un nome univoco illeggibile, ad esempio `_TtC19SwiftFrameworkProxy19SwiftFrameworkProxy`. Il nome del tipo deve essere definito chiaramente perché verrà usato in seguito in base al nome.
    - Ereditare la classe proxy da `NSObject`; in caso contrario, non verrà generata nel file di intestazione Objective-C.
    - Contrassegnare tutti i membri da esporre come `public`.

1. Modificare la configurazione della build dello schema da **debug** a **Release**. A tale scopo, aprire la finestra di dialogo **Xcode > Target > Edit Scheme** , quindi impostare l'opzione di **configurazione Build** su **Release**:

    ![schema di modifica di Xcode](walkthrough-images/xcode-edit-scheme.png)

    ![versione dello schema di modifica di Xcode](walkthrough-images/xcode-edit-scheme-release.png)

1. A questo punto, il Framework è pronto per la creazione. Compilare il Framework per le architetture del simulatore e dei dispositivi e quindi combinare gli output come singolo pacchetto del Framework. Identificare le versioni dell'SDK installate per compilare il codice sorgente usando lo strumento **xcodebuild** :

    ```bash
    xcodebuild -showsdks
    ```

    L'output sarà simile al seguente:

    ```bash
    iOS SDKs:
            iOS 13.2                        -sdk iphoneos13.2
    iOS Simulator SDKs:
            Simulator - iOS 13.2            -sdk iphonesimulator13.2
    macOS SDKs:
            DriverKit 19.0                  -sdk driverkit.macosx19.0
            macOS 10.15                     -sdk macosx10.15
    tvOS SDKs:
            tvOS 13.2                       -sdk appletvos13.2
    tvOS Simulator SDKs:
            Simulator - tvOS 13.2           -sdk appletvsimulator13.2
    watchOS SDKs:
            watchOS 6.1                     -sdk watchos6.1
    watchOS Simulator SDKs:
            Simulator - watchOS 6.1         -sdk watchsimulator6.1
    ```

    Selezionare una versione desiderata per iOS SDK e iOS Simulator SDK, in questo caso versione 13,2 ed eseguire la compilazione con il comando seguente

    ```bash
    xcodebuild -sdk iphonesimulator13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    xcodebuild -sdk iphoneos13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    ```

    > [!TIP]
    > Se è presente un'area di lavoro anziché Project, compilare l'area di lavoro e specificare la destinazione come parametro obbligatorio. Si desidera inoltre specificare una directory di output perché per le aree di lavoro questa directory sarà diversa da quella per le compilazioni di progetto.

    > [!TIP]
    > È anche possibile usare [lo script Helper](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L3-L14) per compilare il Framework per tutte le architetture applicabili o semplicemente compilarlo dal simulatore di commutazione Xcode e dal dispositivo nel selettore di destinazione.

1. Sono disponibili due Framework Swift, uno per ogni piattaforma, che vengono combinati come un singolo pacchetto da incorporare in un progetto di binding Novell. iOS in un secondo momento. Per creare un Framework FAT, che combina entrambe le architetture, è necessario eseguire i passaggi seguenti. Il pacchetto del Framework è semplicemente una cartella in modo da poter eseguire tutti i tipi di operazioni, ad esempio l'aggiunta, la rimozione e la sostituzione dei file:

    - Passare alla cartella di output di compilazione con le sottocartelle **Release-iPhoneOS** e **Release-iphonesimulator** e copiare uno dei Framework come versione iniziale dell'output finale (Framework FAT).

        ```bash
        cp -R "Release-iphoneos" "Release-fat"
        ```

    - Combinare i moduli di un'altra compilazione con i moduli Fat Framework

        ```bash
        cp -R "Release-iphonesimulator/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/" "Release-fat/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/"
        ```

    - Combinare la configurazione di iPhoneOS + iphonesimulator come Framework Fat

        ```bash
        lipo -create -output "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphoneos/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphonesimulator/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

    - Verificare i risultati

        ```bash
        lipo -info "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

        L'output deve eseguire il rendering degli elementi seguenti, riflettendo il nome del Framework e le architetture incluse:

        ```bash
        Architectures in the fat file: Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy are: x86_64 arm64
        ```

    > [!TIP]
    > Se si vuole supportare una sola piattaforma (ad esempio, si sta compilando un'app, che può essere eseguita solo su un dispositivo), è possibile ignorare il passaggio per creare la libreria FAT e usare il Framework di output dalla compilazione del dispositivo precedente.

    > [!TIP]
    > È anche possibile usare [lo script Helper](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L16-L24) per creare il Framework FAT, che consente di automatizzare tutti i passaggi precedenti.

## <a name="prepare-metadata"></a>Preparare i metadati

A questo punto, è necessario che il Framework con l'intestazione dell'interfaccia generata da Objective-C sia pronto per essere utilizzato da un'associazione Novell. iOS.  Il passaggio successivo consiste nel preparare le interfacce di definizione API, che vengono usate da un progetto di associazione C# per generare classi. Queste definizioni possono essere create manualmente o automaticamente dallo strumento [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) e dal file di intestazione generato. Usare Sharpie per generare i metadati:

1. Scaricare lo strumento più recente dell' [obiettivo Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) dal sito Web di download ufficiale e installarlo seguendo la procedura guidata. Al termine dell'installazione, è possibile verificarlo eseguendo il comando Sharpie:

    ```bash
    sharpie -v
    ```

1. Generare i metadati usando Sharpie e il file di intestazione Objective-C generato automaticamente:

    ```bash
    sharpie bind --sdk=iphoneos13.2 --output="XamarinApiDef" --namespace="Binding" --scope="Release-fat/SwiftFrameworkProxy.framework/Headers/" "Release-fat/SwiftFrameworkProxy.framework/Headers/SwiftFrameworkProxy-Swift.h"
    ```

    L'output riflette i file di metadati generati: **ApiDefinitions.cs** e **StructsAndEnums.cs**. Salvare questi file per il passaggio successivo per includerli in un progetto di binding Novell. iOS insieme ai riferimenti nativi:

    ```bash
    Parsing 1 header files...
    Binding...
        [write] ApiDefinitions.cs
        [write] StructsAndEnums.cs
    ```

    Lo strumento genererà C# i metadati per ogni membro Objective-C esposto, che sarà simile al codice seguente. Come si può notare, potrebbe essere definito manualmente perché presenta un formato leggibile e un mapping semplice dei membri:

    ```csharp
    [Export ("initForApiKey:")]
    string InitForApiKey (string apiKey);
    ```

    > [!TIP]
    > Il nome del file di intestazione può essere diverso se sono state modificate le impostazioni di Xcode predefinite per il nome dell'intestazione. Per impostazione predefinita, ha il nome di un progetto con il suffisso **-Swift** . È sempre possibile controllare il file e il relativo nome passando alla cartella intestazioni del pacchetto del Framework.

    > [!TIP]
    > Come parte del processo di automazione, è possibile usare [lo script Helper](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L35) per generare automaticamente i metadati dopo la creazione del Framework FAT.

## <a name="build-a-binding-library"></a>Compilazione di una libreria di associazione

Il passaggio successivo consiste nel creare un progetto di binding Novell. iOS usando il modello di associazione di Visual Studio, aggiungere i metadati necessari, i riferimenti nativi e quindi compilare il progetto per produrre una libreria utilizzabile:

1. Aprire Visual Studio per Mac e creare un nuovo progetto di libreria di binding Novell. iOS, assegnargli un nome, in questo caso SwiftFrameworkProxy. Binding e completare la procedura guidata. Il modello di binding Novell. iOS si trova nel percorso seguente: libreria **> ios >** libreria di associazione:

    ![libreria di creazione binding di Visual Studio](walkthrough-images/visualstudio-create-binding-library.png)

1. Eliminare i file di metadati esistenti **ApiDefinition.cs** e **structs.cs** perché verranno sostituiti completamente con i metadati generati dallo strumento Objective Sharpie.
1. Copiare i metadati generati da Sharpie in uno dei passaggi precedenti, selezionare la seguente azione di compilazione nella finestra Proprietà: **ObjBindingApiDefinition** per il file **ApiDefinitions.cs** e **ObjBindingCoreSource** per il file **StructsAndEnums.cs** :

    ![metadati della struttura del progetto di Visual Studio](walkthrough-images/visualstudio-project-structure-metadata.png)

    I metadati stessi descrivono ogni classe Objective-C esposta e membro C# usando il linguaggio. È possibile visualizzare la definizione di intestazione Objective-C originale insieme alla C# dichiarazione:

    ```csharp
    // @interface SwiftFrameworkProxy : NSObject
    [BaseType (typeof(NSObject))]
    interface SwiftFrameworkProxy
    {
        // -(NSString * _Nonnull)initForApiKey:(NSString * _Nonnull)apiKey __attribute__((objc_method_family("none"))) __attribute__((warn_unused_result));
        [Export ("initForApiKey:")]
        string InitForApiKey (string apiKey);
    }
    ```

    Anche se si tratta di un C# codice valido, non viene usato così com'è, ma viene usato dagli strumenti Novell. iOS per C# generare classi basate su questa definizione di metadati. Di conseguenza, anziché l'interfaccia SwiftFrameworkProxy si ottiene una C# classe con lo stesso nome, di cui è possibile creare un'istanza tramite il codice Novell. iOS. Questa classe ottiene metodi, proprietà e altri membri definiti dai metadati, che vengono chiamati in C# modo.

1. Aggiungere il riferimento nativo al Framework FAT precedente generato e ogni dipendenza di tale Framework. In questo caso, aggiungere i riferimenti nativi SwiftFrameworkProxy e Gigya Framework al progetto di associazione:

    - Per aggiungere riferimenti a framework nativi, aprire il Finder e passare alla cartella con i Framework. Trascinare e rilasciare i Framework sotto il percorso dei riferimenti nativi nell'Esplora soluzioni. In alternativa, è possibile usare l'opzione del menu di scelta rapida nella cartella riferimenti nativi e fare clic su **Aggiungi riferimento nativo** per cercare i Framework e aggiungerli:

    ![riferimenti nativi della struttura del progetto di Visual Studio](walkthrough-images/visualstudio-project-structure-nativerefs.png)

    - Aggiornare le proprietà di ogni riferimento nativo e selezionare tre opzioni importanti:

        - Imposta collegamento intelligente = true
        - Imposta Force Load = false
        - Set elenco di Framework utilizzati per creare i Framework originali. In questo caso ogni Framework dispone solo di due dipendenze: Foundation e UIKit. Impostarlo sul campo Frameworks:

        ![opzioni del proxy di Visual Studio nativeref](walkthrough-images/visualstudio-nativeref-proxy-options.png)

        Se sono presenti flag aggiuntivi del linker da specificare, impostarli nel campo flag del linker. In questo caso, mantenerlo vuoto.

    - Quando necessario, specificare flag del linker aggiuntivi. Se la libreria che si sta associando espone solo le API Objective-C ma internamente USA Swift, è possibile che si verifichino problemi come:

        ```Console
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftQuartzCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCoreImage
        ```

        Nelle proprietà del progetto di binding per la libreria nativa è necessario aggiungere i valori seguenti ai flag del linker:

        ```Linker
        L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphonesimulator/ -L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphoneos -Wl,-rpath -Wl,@executable_path/Frameworks
        ```

        Le prime due opzioni (le `-L ...` ) indicano al compilatore nativo dove trovare le librerie Swift. Il compilatore nativo ignorerà le librerie che non hanno l'architettura corretta, il che significa che è possibile passare il percorso sia per le librerie di simulatore che per le librerie di dispositivi allo stesso tempo, in modo che funzioni sia per le compilazioni di simulatore che di dispositivi ( i percorsi sono corretti solo per iOS; per tvOS e watchos, è necessario aggiornarli. Uno svantaggio è che questo approccio richiede che Xcode sia corretto in/Application/Xcode.app, se il consumer della libreria di binding dispone di Xcode in una posizione diversa, non funzionerà. La soluzione alternativa consiste nell'aggiungere queste opzioni negli argomenti mTouch aggiuntivi nelle opzioni di compilazione iOS del progetto eseguibile (`--gcc_flags -L... -L...`). La terza opzione fa in modo che il linker nativo memorizzi il percorso delle librerie Swift nell'eseguibile, in modo che il sistema operativo possa trovarle.

1. L'azione finale consiste nel compilare la libreria e verificare che non siano presenti errori di compilazione. Spesso si noterà che i metadati delle associazioni prodotti da Objective Sharpie verranno annotati con l'attributo `[Verify]` . Questi attributi indicano che è necessario verificare che Objective Sharpie abbia fatto la corretta operazione confrontando l'associazione con la dichiarazione Objective-C originale (che verrà fornita in un commento sopra la dichiarazione associata). Per ulteriori informazioni sui membri contrassegnati con l'attributo, vedere [il collegamento seguente](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify). Una volta compilato, il progetto può essere utilizzato da un'applicazione Novell. iOS.

## <a name="consume-the-binding-library"></a>Utilizzare la libreria di associazione

Il passaggio finale consiste nell'utilizzare la libreria di binding Novell. iOS in un'applicazione Novell. iOS. Creare un nuovo progetto Novell. iOS, aggiungere un riferimento alla libreria di associazione e attivare Gigya Swift SDK:

1. Creare il progetto Novell. iOS. È possibile usare l' **app iOS > > app visualizzazione singola** come punto di partenza:

    ![nuovo app di Visual Studio](walkthrough-images/visualstudio-app-new.png)

1. Aggiungere un riferimento al progetto di associazione al progetto di destinazione o. dll creato in precedenza. Considerare la libreria di associazione come una normale libreria Novell. iOS:

    ![refs app di Visual Studio](walkthrough-images/visualstudio-app-refs.png)

1. Aggiornare il codice sorgente dell'app e aggiungere la logica di inizializzazione al ViewController primario, che attiva Gigya SDK

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
        var proxy = new SwiftFrameworkProxy();
        var result = proxy.InitForApiKey("APIKey");
        System.Diagnostics.Debug.WriteLine(result);
    }
    ```

1. Creare un pulsante con un nome **btnLogin** e aggiungere il gestore di clic del pulsante seguente per attivare un flusso di autenticazione:

    ```csharp
    private void btnLogin_Tap(object sender, EventArgs e)
    {
        _proxy.LoginWithProvider(GigyaSocialProvidersProxy.Instagram, this, (result, error) =>
        {
            // process your login result here
        });
    }
    ```

1. Eseguire l'app nell'output di debug dovrebbe essere visualizzata la riga seguente: `Gigya initialized with domain: us1.gigya.com`. Fare clic sul pulsante per attivare il flusso di autenticazione:

    [risultato del proxy ![Swift](walkthrough-images/swiftproxy-result.png)](walkthrough-images/swiftproxy-result.png#lightbox)

Congratulazioni! È stata creata un'app Novell. iOS e una libreria di binding, che usa un Framework Swift. L'applicazione precedente verrà eseguita correttamente in iOS 12.2 + perché a partire da questa versione di iOS [Apple ha introdotto la stabilità ABI](https://swift.org/blog/swift-5-1-released/) e ogni iOS che inizia 12.2 + include le librerie di runtime Swift, che possono essere usate per eseguire l'applicazione compilata con Swift 5.1 +. Se è necessario aggiungere il supporto per le versioni precedenti di iOS, è necessario eseguire alcuni altri passaggi:

1. Per aggiungere il supporto per iOS 12,1 e versioni precedenti, è necessario distribuire dylib Swift specifici usati per compilare il Framework. Usare il pacchetto NuGet [Novell. iOS. SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) per elaborare e copiare le librerie necessarie con l'IPA. Aggiungere il riferimento NuGet al progetto di destinazione e ricompilare l'applicazione. Non sono necessari altri passaggi, il pacchetto NuGet installerà attività specifiche, che vengono eseguite con il processo di compilazione, identificano i dylib Swift necessari e li impacchettano con l'IPA finale.

1. Per inviare l'app all'App Store si vuole usare Xcode e l'opzione Distribuisci, che aggiornerà il file IPA e la cartella **SwiftSupport** dylib, in modo che venga accettata da AppStore:

    ○ Archiviare l'app. Dal menu Visual Studio per Mac selezionare **compila > archivia per la pubblicazione**:

    ![Archivio di Visual Studio per la pubblicazione](walkthrough-images/visualstudio-archiveforpublishing.png)

    Questa azione compila il progetto e lo raggiunge con la libreria, accessibile da Xcode per la distribuzione.

    ○ Viene distribuito tramite Xcode. Aprire Xcode e passare all'opzione di menu **libreria > di Windows** :

    ![archivi di Visual Studio](walkthrough-images/visualstudio-archives.png)

    Selezionare l'archivio creato nel passaggio precedente e fare clic sul pulsante Distribuisci app. Seguire la procedura guidata per caricare l'applicazione in AppStore.

1. Questo passaggio è facoltativo, ma è importante verificare che l'app possa essere eseguita in iOS 12,1 e versioni precedenti, oltre a 12,2. È possibile eseguire questa operazione con la guida del Framework Test Cloud e UITest. Creare un progetto UITest e un test dell'interfaccia utente di base, che esegue l'app:

    - Creare un progetto UITest e configurarlo per l'applicazione Novell. iOS:

        ![nuovo Visual Studio UITest](walkthrough-images/visualstudio-uitest-new.png)

        > [!TIP]
        > È possibile trovare altre informazioni su come creare un progetto UITest e configurarlo per l'app tramite [il collegamento seguente](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

    - Creare un test di base per eseguire l'app e usare alcune delle funzionalità di Swift SDK. Questo test attiva l'app, tenta di accedere e quindi preme il pulsante Annulla:

        ```csharp
        [Test]
        public void HappyPath()
        {
            app.WaitForElement(StatusLabel);
            app.WaitForElement(LoginButton);
            app.Screenshot("App loaded.");
            Assert.AreEqual(app.Query(StatusLabel).FirstOrDefault().Text, "Gigya initialized with domain: us1.gigya.com");

            app.Tap(LoginButton);
            app.WaitForElement(GigyaWebView);
            app.Screenshot("Login activated.");

            app.Tap(CancelButton);
            app.WaitForElement(LoginButton);
            app.Screenshot("Login cancelled.");
        }
        ```

        > [!TIP]
        > Per altre informazioni su UITests Framework e sull'automazione dell'interfaccia utente, vedere [il collegamento seguente](https://docs.microsoft.com/appcenter/test-cloud/uitest/).

    - Creare un'app iOS in App Center, creare una nuova esecuzione dei test con un nuovo set di dispositivi per eseguire il test:

        ![nuovo Visual Studio App Center](walkthrough-images/visualstudio-appcenter-new.png)

        > [!TIP]
        > Per altre informazioni su AppCenter Test Cloud, vedere [il collegamento seguente](https://docs.microsoft.com/appcenter/test-cloud/).

    - Installare l'interfaccia della riga di comando di AppCenter

        ```bash
        npm install -g appcenter-cli
        ```

        > [!IMPORTANT]
        > Verificare che il nodo v 6.3 o versione successiva sia installato

    - Eseguire il test usando il comando seguente. Assicurarsi anche che la riga di comando di AppCenter sia attualmente connessa.

        ```bash
        appcenter test run uitest --app "Mobile-Customer-Advisory-Team/SwiftBinding.iOS" --devices a7e7cb50 --app-path "Xamarin.SingleView.ipa" --test-series "master" --locale "en_US" --build-dir "Xamarin/Xamarin.SingleView.UITests/bin/Debug/"
        ```

    - Verificare il risultato. Nel portale di AppCenter passare all' **App > test > esecuzioni dei test**:

        ![risultato di Visual Studio AppCenter UITest](walkthrough-images/visualstudio-appcenter-uitest-result.png)

        E selezionare l'esecuzione dei test desiderata e verificare il risultato:

        ![esecuzione di Visual Studio AppCenter UITest](walkthrough-images/visualstudio-appcenter-uitest-runs.png)

È stata sviluppata un'applicazione Novell. iOS di base che usa un Framework Swift nativo tramite una libreria di binding Novell. iOS. Nell'esempio viene fornito un modo semplice per utilizzare il framework selezionato e in un'applicazione reale verrà richiesto di esporre più API e di preparare i metadati per queste API. Lo script per generare metadati semplificherà le future modifiche alle API del Framework.

## <a name="related-links"></a>Collegamenti correlati

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Verifica dei metadati di Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [Binding di Objective-C Framework](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Gigya iOS SDK (Swift Framework)](https://developers.gigya.com/display/GD/Swift+SDK)
- [Stabilità 5,1 ABI Swift](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automazione di Novell UITest](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Configurazione di Novell. iOS UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Test Cloud AppCenter](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Repository del progetto di esempio](https://github.com/alexeystrakh/xamarin-binding-swift-framework)
