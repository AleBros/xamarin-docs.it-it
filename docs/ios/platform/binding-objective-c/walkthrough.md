---
title: 'Procedura dettagliata: Associazione di una libreria Objective-C per iOS'
description: Questo articolo fornisce procedure dettagliate pratiche di creazione di un'associazione di xamarin per una libreria di Objective-C, InfColorPicker esistente. Vengono trattati argomenti come la compilazione di una libreria statica di Objective-C, associazione e usando l'associazione in un'applicazione di xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: e4619f5b1d3f888b2557cf894aaa83106504766f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Procedura dettagliata: Associazione di una libreria Objective-C per iOS

_Questo articolo fornisce procedure dettagliate pratiche di creazione di un'associazione di xamarin per una libreria di Objective-C, InfColorPicker esistente. Vengono trattati argomenti come la compilazione di una libreria statica di Objective-C, associazione e usando l'associazione in un'applicazione di xamarin. IOS._

Quando si lavora su iOS, potrebbero verificarsi casi in cui si desidera utilizzare una libreria di terze parti Objective-C. In questi casi, è possibile utilizzare un xamarin _associazione progetto_ per creare un [c# associazione](~/cross-platform/macios/binding/overview.md) che consente di utilizzare la libreria nelle applicazioni di xamarin. IOS.

In genere all'interno dell'ecosistema di iOS è possibile trovare le librerie di 3 tipi:

* In un file di libreria statica precompilata con `.a` estensione insieme alle relative intestazioni (file con estensione h). Ad esempio, [libreria Analitica di Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Come un Framework precompilato. Questo è solo una cartella contenente la libreria statica, le intestazioni e risorse aggiuntive in alcuni casi con `.framework` estensione. Ad esempio, [AdMob libreria Google](https://developers.google.com/admob/ios/download).
* Come file di codice sorgente. Ad esempio, una libreria che contiene solo `.m` e `.h` file Objective C.

Nello scenario di prima e secondo già sarà presente una libreria statica di CocoaTouch precompilata pertanto in questo articolo esamineremo nel terzo scenario. Tenere presente che prima di iniziare a creare un'associazione, verificare sempre la licenza fornita con la libreria per assicurarsi che sia possibile eseguirne l'associazione.

Questo articolo fornisce una procedura dettagliata della creazione di un progetto di associazione utilizzando la open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C il progetto come un esempio, tuttavia tutte le informazioni in questa guida possono essere adattate per l'uso con i libreria di terze parti Objective-C. La libreria InfColorPicker fornisce un controller di visualizzazione riutilizzabili che consente all'utente di selezionare un colore in base a una rappresentazione HSB, rendendo più semplice la selezione dei colori.

[![](walkthrough-images/run01.png "Esempio della libreria InfColorPicker in esecuzione in iOS")](walkthrough-images/run01.png#lightbox)

Tratteremo tutte le misure necessarie per utilizzare questa particolare API Objective-C in xamarin. ios:

- In primo luogo, si creerà una libreria statica Objective-C usando Xcode.
- Quindi, si sarà associa questa libreria statica con xamarin. IOS.
- Successivamente, indicato come obiettivo Sharpie può ridurre il carico di lavoro tramite la generazione automatica di alcuni (ma non tutte) delle definizioni di API necessarie necessari per l'associazione di xamarin. IOS.
- Infine, si creerà un'applicazione di xamarin che utilizza l'associazione.

L'applicazione di esempio verrà illustrato come utilizzare un delegato sicuro per la comunicazione tra l'API InfColorPicker e il codice c#. Dopo che abbiamo visto come utilizzare un delegato sicuro, ci occuperemo di come utilizzare deboli delegati per eseguire le stesse attività.

## <a name="requirements"></a>Requisiti

In questo articolo si presuppone che si abbia familiarità con Xcode e la lingua in Objective-C e avere letto il nostro [associazione Objective-C](~/cross-platform/macios/binding/index.md) documentazione. Inoltre, è necessario completare i passaggi presentati:

-  **Xcode e iOS SDK** -Xcode di Apple e l'API di iOS più recente devono essere installati e configurati nel computer dello sviluppatore.
-  **[Gli strumenti da riga di comando Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -strumenti della riga di comando Xcode deve essere installati per la versione attualmente installata di Xcode (vedere di seguito per i dettagli di installazione).
-  **Visual Studio per Mac o Visual Studio** -la versione più recente di Visual Studio per Mac o Visual Studio deve essere installata e configurata nel computer di sviluppo. Un Mac Apple è necessario per lo sviluppo di un'applicazione di xamarin. IOS, e quando si utilizza Visual Studio è necessario essere connessi a [un xamarin host di compilazione](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **La versione più recente dell'obiettivo Sharpie** -una copia corrente dello strumento Sharpie obiettivo scaricato da [qui](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Se si dispone già di Sharpie obiettivo installato, è possibile aggiornare, la versione più recente tramite il `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>L'installazione di strumenti della riga di comando Xcode

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


Come descritto in precedenza, verrà usato strumenti da riga di comando Xcode (in particolare `make` e `lipo`) in questa procedura dettagliata. Il `make` comando è un'utilità Unix molto comune per automatizza la compilazione di programmi eseguibili e le librerie con un _makefile_ che specifica come il programma deve essere compilato. Il `lipo` comando è un'utilità della riga di comando di OS X per la creazione di file multi-architettura; verranno combinati più `.a` file in un file che può essere utilizzato da tutte le architetture hardware.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Come descritto in precedenza, verrà usato strumenti da riga di comando Xcode sul **Host di compilazione Mac** (in particolare `make` e `lipo`) in questa procedura dettagliata. Il `make` comando è un'utilità Unix molto comune per automatizza la compilazione di programmi eseguibili e le librerie con un _makefile_ su come compilare il programma specifica. Il `lipo` comando è un'utilità della riga di comando di OS X per la creazione di file multi-architettura; verranno combinati più `.a` file in un file che può essere utilizzato da tutte le architetture hardware.


-----

In base a Apple [compilazione dalla riga di comando con domande frequenti su Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentazione, in OS X 10.9 e versioni successive, il **Scarica** riquadro di Xcode **preferenze** finestra di dialogo non supporta più strumenti da riga di comando di download.

È necessario utilizzare uno dei metodi seguenti per installare gli strumenti:

- **Installare Xcode** - quando si installa Xcode, viene fornito in dotazione con tutti gli strumenti da riga di comando. In OS X 10.9 shim (installato `/usr/bin`), può eseguire il mapping di qualsiasi strumento incluso in `/usr/bin` allo strumento corrispondente all'interno di Xcode. Ad esempio, il `xcrun` comando, che consente di trovare o eseguire qualsiasi strumento all'interno di Xcode dalla riga di comando.
- **L'applicazione Terminal** -dall'applicazione Terminal, è possibile installare gli strumenti da riga di comando eseguendo il `xcode-select --install` comando:
    - Avviare l'applicazione Terminal.
    - Tipo `xcode-select --install` e premere **invio**, ad esempio:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Verrà richiesto di installare gli strumenti da riga di comando, fare clic su di **installare** pulsante: [ ![ ] (walkthrough-images/xcode01.png "l'installazione degli strumenti della riga di comando")](walkthrough-images/xcode01.png#lightbox)

    - Gli strumenti verranno scaricati e installati dal server di Apple: [ ![ ] (walkthrough-images/xcode02.png "scaricare gli strumenti")](walkthrough-images/xcode02.png#lightbox)

- **Download per gli sviluppatori di Apple** -pacchetto di strumenti della riga di comando è disponibile il [download per gli sviluppatori di Apple]() pagina web. Accedere con il proprio ID Apple, quindi cercare e scaricare gli strumenti da riga di comando: [ ![ ] (walkthrough-images/xcode03.png "trovare gli strumenti da riga di comando")](walkthrough-images/xcode03.png#lightbox)

Con installati gli strumenti della riga di comando, si è pronti per continuare con la procedura dettagliata.

## <a name="walkthrough"></a>Procedura dettagliata

In questa procedura dettagliata, verranno descritte le seguenti operazioni:

- **[Creare una libreria statica](#Creating_A_Static_Library)**  -questo passaggio prevede la creazione di una libreria statica del **InfColorPicker** codice Objective-C. La libreria statica avrà il `.a` estensione di file e verranno incorporate nell'assembly .NET del progetto di libreria.
- **[Creare un progetto di associazione di xamarin](#Create_a_Xamarin.iOS_Binding_Project)**  -una volta che una libreria statica, verrà usato per creare un progetto di associazione di xamarin. IOS. Il progetto di associazione è costituito dalla libreria statica che appena creato e i metadati sotto forma di codice c# che illustra come utilizzare l'API di Objective-C. Questi metadati sono considerato come le definizioni di API. Useremo  **[obiettivo Sharpie](#Using_Objective_Sharpie)**  per consentire agli utenti con le definizioni di API di creazione.
- **[Normalizzare le definizioni di API](#Normalize_the_API_Definitions)**  : Sharpie obiettivo è molto utile di contribuito a, ma non tutti gli elementi. Parleremo alcune modifiche che è necessario apportare alle definizioni di API prima di poter essere usati.
- **[Utilizzare la libreria di associazione](#Using_the_Binding)**  -infine, si creerà un'applicazione di xamarin. IOS in cui viene illustrato come utilizzare il progetto di associazione appena creata.

Ora che sappiamo quali passaggi sono necessari, passiamo al resto della procedura dettagliata.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Creazione di una libreria statica

Se è esaminare il codice per InfColorPicker in Github:

[![](walkthrough-images/image02.png "Esaminare il codice per InfColorPicker in Github")](walkthrough-images/image02.png#lightbox)

Possiamo vedere le seguenti tre directory del progetto:

- **InfColorPicker** -questa directory contiene il codice Objective-C per il progetto.
- **PickerSamplePad** -questa directory contiene un progetto di esempio iPad.
- **PickerSamplePhone** -questa directory contiene un progetto di esempio iPhone.

Consente di scaricare il progetto di InfColorPicker da [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) e decomprimerlo nella directory della scelta. Aprendo la destinazione di Xcode per `PickerSamplePhone` progetto, è vedere la seguente struttura di progetto in Xcode selezione:

[![](walkthrough-images/image03.png "La struttura del progetto nello strumento di navigazione Xcode")](walkthrough-images/image03.png#lightbox)

Questo progetto consente di ottenere il riutilizzo del codice aggiungendo direttamente il codice sorgente InfColorPicker (nella casella rossa) in ogni progetto di esempio. Il codice per il progetto di esempio è all'interno della casella blu. Poiché questo progetto non fornire commenti con una libreria statica, è necessario per noi creare un progetto Xcode per compilare la libreria statica.

Il primo passaggio è riservata aggiungere il codice sorgente InfoColorPicker in libreria statica. Per ottenere questo risultato si eseguire le operazioni seguenti:

1. Avviare Xcode.
2. Dal **File** menu selezionare **New** > **progetto...** :

    [![](walkthrough-images/image04.png "Avvio di un nuovo progetto")](walkthrough-images/image04.png#lightbox)
3. Selezionare **Framework & libreria**, **Cocoa tocco libreria statica** modello, quindi scegliere il **Avanti** pulsante:

    [![](walkthrough-images/image05.png "Selezionare il modello di libreria statica tocco Cocoa")](walkthrough-images/image05.png#lightbox)
4. Immettere `InfColorPicker` per il **nome progetto** e fare clic su di **Avanti** pulsante:

    [![](walkthrough-images/image06.png "Immettere InfColorPicker per il nome del progetto")](walkthrough-images/image06.png#lightbox)
5. Selezionare un percorso per salvare il progetto e scegliere il **OK** pulsante.
6. È necessario aggiungere l'origine dal progetto InfColorPicker al progetto libreria statica. Poiché il **InfColorPicker.h** file esiste già nel nostro libreria statica (per impostazione predefinita), Xcode non permetterà di sovrascrivere il file. Dal **Finder**passare al codice sorgente InfColorPicker nel progetto originale che è stato decompresso da GitHub, copiare tutti i file InfColorPicker e incollarli nel nuovo progetto di libreria statica:

    [![](walkthrough-images/image12.png "Copiare tutti i file InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Tornare a Xcode, fare clic con il pulsante destro sul **InfColorPicker** cartella e selezionare **aggiungere file a "InfColorPicker …"**:

    [![](walkthrough-images/image08.png "Aggiunta di file")](walkthrough-images/image08.png#lightbox)

8. Nella finestra di dialogo Aggiungi file, passare al file di codice sorgente InfColorPicker in cui è stati copiati, selezionarle tutte e fare clic su di **Aggiungi** pulsante:

    [![](walkthrough-images/image09.png "Seleziona tutto e fare clic sul pulsante Aggiungi")](walkthrough-images/image09.png#lightbox)

9. Il codice sorgente verrà copiato nel progetto:

    [![](walkthrough-images/image10.png "Il codice di origine verrà copiato nel progetto")](walkthrough-images/image10.png#lightbox)

10. La struttura di progetto Xcode, selezionare il **InfColorPicker.m** file e impostare come commento le ultime due righe (a causa della modalità di questa libreria è stato scritto, questo file non viene utilizzato):

    [![](walkthrough-images/image14.png "Modifica del file InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. È ora necessario controllare se esistono alcun Framework richiesto dalla libreria. È possibile trovare queste informazioni nel file Leggimi o aprendo una dei progetti di esempio forniti. Questo esempio viene utilizzato `Foundation.framework`, `UIKit.framework`, e `CoreGraphics.framework` andiamo aggiungerli.

12. Selezionare il **InfColorPicker destinazione > Build Phases** ed espandere il **binario con librerie di collegamento** sezione:

    [![](walkthrough-images/image16b.png "Espandere la sezione binario con librerie di collegamento")](walkthrough-images/image16b.png#lightbox)

13. Utilizzare il  **+**  pulsante per aprire la finestra di dialogo che consente di aggiungere i framework di frame necessari elencati in precedenza:

    [![](walkthrough-images/image16c.png "Aggiungere che i framework di frame necessari elencati sopra")](walkthrough-images/image16c.png#lightbox)

14. Il **binario con librerie di collegamento** sezione dovrebbe essere simile a immagine riportata di seguito:

    [![](walkthrough-images/image16d.png "La sezione binario con librerie di collegamento")](walkthrough-images/image16d.png#lightbox)

A questo punto siamo Chiudi, ma piuttosto non è stata completata. La libreria statica è stata creata, ma è necessario compilarlo per creare una file System Fat binario che include tutte le architetture necessari per il dispositivo iOS e il simulatore iOS.

### <a name="creating-a-fat-binary"></a>Creazione di un file System Fat binario

Tutti i dispositivi iOS dispongono di processori con tecnologia architettura ARM che sono stati sviluppati nel tempo. Ogni nuova architettura aggiunti altri miglioramenti e nuove istruzioni pur mantenendo la compatibilità. Nei dispositivi iOS abbiamo armv6, armv7, armv7s, set di istruzioni arm64 – Sebbene [non utilizziamo non è più armv6](~/ios/deploy-test/compiling-for-different-devices.md). Il simulatore iOS non è alimentato dalle ARM ed è anziché un x86 e simulatore x86_64 acceso. È che si intende per noi è che, è necessario stabilire le librerie in ogni istruzione imposta.

È una libreria Fat `.a` file che contiene tutte le architetture supportate.

La creazione di una file System fat binario è un processo in tre passaggi:

- Compilare una versione 7 ARM e ARM64 della libreria statica.
- Compilare una versione x86 e x84_64 della libreria statica.
- Utilizzare il `lipo` strumento della riga di comando per combinare le due librerie statiche in una sola.

Questi tre passaggi piuttosto semplici, mentre potrebbe essere necessario ripeterli in futuro quando la libreria Objective-C riceve aggiornamenti oppure se sono necessarie correzioni di bug. Se si decide di automatizzare questi passaggi, questa operazione semplifica la manutenzione future e il supporto del progetto di associazione di iOS.

Sono disponibili numerosi strumenti disponibili per automatizzare attività quali - uno script della shell, [inclinazione](http://rake.rubyforge.org/), [xbuild](http://www.mono-project.com/Microsoft.Build), e [rendere](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Quando sono stati installati gli strumenti da riga di comando Xcode, viene installato anche verificare, in modo che sia il sistema di compilazione che verrà utilizzato per questa procedura dettagliata. Ecco un **Makefile** che è possibile utilizzare per creare una libreria condivisa multi-architettura che funzionerà su un dispositivo iOS e il simulatore per qualsiasi libreria:

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

Immettere il **Makefile** comandi nell'editor di testo normale di propria scelta e aggiornare le sezioni con **YOUR--nome del progetto** con il nome del progetto. È anche importante garantire che ci si incollano le istruzioni riportate in precedenza, che sono state mantenute schede all'interno di istruzioni.

Salvare il file con il nome **Makefile** nello stesso percorso della libreria statica Xcode InfColorPicker creato in precedenza:

[![](walkthrough-images/lib00.png "Salvare il file con il nome di Makefile")](walkthrough-images/lib00.png#lightbox)

Aprire l'applicazione Terminal nel Mac e passare al percorso dei Makefile. Tipo `make` nel terminale, premere **invio** e **Makefile** verrà eseguito:

[![](walkthrough-images/lib01.png "Makefile di esempio di output")](walkthrough-images/lib01.png#lightbox)

Quando si esegue marca, verrà visualizzato molta scorrimento dal testo. Se tutto funzioni correttamente, verrà visualizzata la dicitura **compilazione completata** e `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` e `libInfColorPickerSDK.a` verranno copiati i file nello stesso percorso di **Makefile**:

[![](walkthrough-images/lib02.png "I file libInfColorPicker armv7.a, libInfColorPicker i386.a e libInfColorPickerSDK.a generati da Makefile")](walkthrough-images/lib02.png#lightbox)

È possibile verificare le architetture entro il file System Fat binario utilizzando il comando seguente:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Verrà visualizzato quanto segue:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

A questo punto, abbiamo completato il primo passaggio all'associazione iOS tramite la creazione di una libreria statica con Xcode e gli strumenti da riga di comando Xcode `make` e `lipo`. Consente di procedere al passaggio successivo e utilizzare **obiettivo Sharpie** per automatizzare la creazione delle associazioni API per noi.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Creare un progetto di associazione di xamarin

Prima di utilizzare **obiettivo Sharpie** per automatizzare il processo di associazione, è necessario creare un progetto di associazione di xamarin. IOS per ospitare le definizioni di API (che verrà usato **obiettivo Sharpie** per consentire agli utenti compilazione) e creare l'associazione c# ci.

Di seguito, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac.
1. Dal **File** dal menu **New** > **soluzione...** :

    ![](walkthrough-images/bind01.png "Avvio di una nuova soluzione")

1. Nella finestra di dialogo nuova soluzione, selezionare **libreria** > **iOS progetto Binding**:

    ![](walkthrough-images/bind02.png "Selezionare iOS progetto Binding")

1. Fare clic su di **Avanti** pulsante.

1. Immettere "InfColorPickerBinding" come il **nome progetto** e fare clic su di **crea** pulsante per creare la soluzione:

    ![](walkthrough-images/bind02a.png "Immettere InfColorPickerBinding come nome del progetto")

La soluzione verrà creata e verranno inclusi due file predefiniti:

![](walkthrough-images/bind03.png "La struttura della soluzione in Esplora soluzioni")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. Avviare Visual Studio.

1. Dal **File** dal menu **New** > **progetto...** :

    ![](walkthrough-images/bind01vs.png "Avvio di un nuovo progetto")

1. Nella finestra di dialogo Nuovo progetto, selezionare **iOS** > **libreria associazioni**:

    ![](walkthrough-images/bind02vs.png "Selezionare la raccolta di associazioni di iOS")

1. Immettere "InfColorPickerBinding" come il **nome** e fare clic su di **OK** pulsante per creare la soluzione.

La soluzione verrà creata e verranno inclusi due file predefiniti:

![](walkthrough-images/bind03vs.png "La struttura della soluzione in Esplora soluzioni")

-----



- **ApiDefinition.cs** -questo file contiene i contratti che definiscono la modalità dell'API Objective-C verranno incapsulati in c#.
- **Structs.cs** : questo file contiene le strutture o valori di enumerazione che sono necessari per le interfacce e delegati.

Si lavorerà con questi due file in un secondo momento nella procedura dettagliata. In primo luogo, è necessario aggiungere la libreria InfColorPicker al progetto di associazione.

### <a name="including-the-static-library-in-the-binding-project"></a>Inclusa la libreria statica nel progetto di associazione

Ora è disponibile la base progetto Binding pronto, è necessario aggiungere la libreria Fat binario creato in precedenza per il **InfColorPicker** libreria.

Seguire questi passaggi per aggiungere la libreria:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare clic su di **riferimenti nativi** cartella nel riquadro soluzione e scegliere **aggiungere riferimenti nativi**:

    ![](walkthrough-images/bind04a.png "Aggiungere i riferimenti nativi")

1. Passare a Fat binario è eseguite in precedenza (`libInfColorPickerSDK.a`) e premere il **aprire** pulsante:

    ![](walkthrough-images/bind05.png "Selezionare il file libInfColorPickerSDK.a")
1. Il file verrà incluso nel progetto:

    ![](walkthrough-images/bind04.png "Inclusione di un file")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Copia il `libInfColorPickerSDK.a` dal **Host di compilazione Mac** e incollarlo nel progetto di associazione.

1. Pulsante destro del mouse sul progetto e scegliere **Aggiungi > elemento esistente...** :

    ![](walkthrough-images/bind04vs.png "Aggiunta di un file esistente")

1. Passare il `libInfColorPickerSDK.a` e premere il **Aggiungi** pulsante:

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. Il file verrà incluso nel progetto.

-----


Quando il file con estensione viene aggiunto al progetto, xamarin. IOS imposterà automaticamente la **azione di compilazione** del file da **ObjcBindingNativeLibrary**e creare un file speciale denominato `libInfColorPickerSDK.linkwith.cs`.


Questo file contiene il `LinkWith` attributo che indica di xamarin come handle alla libreria statica che abbiamo appena aggiunto. Il contenuto di questo file è illustrato nel frammento di codice seguente:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

Il `LinkWith` attributo identifica la libreria statica per il progetto e di alcuni flag linker importanti.


La successiva operazione che è necessario eseguire consiste nel creare le definizioni di API per il progetto InfColorPicker. Ai fini di questa procedura dettagliata, si utilizzerà Sharpie obiettivo per generare il file **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Utilizzando Sharpie obiettivo

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


Obiettivo Sharpie è una riga di comando dello strumento (fornito da Xamarin) che può agevolare la creazione di definizioni necessarie per associare una raccolta di Objective-C 3rd party a c#. In questa sezione si userà Sharpie obiettivo iniziale creare **ApiDefinition.cs** per il progetto InfColorPicker.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Obiettivo Sharpie è una riga di comando dello strumento (fornito da Xamarin) che può agevolare la creazione di definizioni necessarie per associare una raccolta di Objective-C 3rd party a c#. In questa sezione si userà Sharpie obiettivo nel nostro **Host di compilazione Mac** per creare il primo **ApiDefinition.cs** per il progetto InfColorPicker.


-----

Per iniziare, si scarica file di programma di installazione Sharpie obiettivo come descritto in questo [Guida](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Eseguire il programma di installazione e seguire le istruzioni visualizzate dalla procedura guidata di installazione per installare Sharpie obiettivo il computer di sviluppo.

Dopo che è stato completato il Sharpie obiettivo verrà installato, avviare l'app Terminal e immettere il comando seguente per ottenere informazioni su tutti gli strumenti disponibili per assistere nell'associazione:

```bash
sharpie -help
```

Se si esegue il comando riportato sopra, verrà generato il seguente output:

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

Allo scopo di questa procedura dettagliata, si utilizzerà gli strumenti Sharpie obiettivi seguenti:

- **xcode** - questo strumenti offre informazioni sui nostri installazione Xcode corrente e le versioni di iOS e Mac API che è stato installato. Si utilizzerà queste informazioni in un secondo momento quando si genera il binding.
- **associare** -useremo questo strumento per analizzare il **h** i file del progetto InfColorPicker in iniziale **ApiDefinition.cs** e **StructsAndEnums.cs** file.

Per ottenere informazioni su uno strumento Sharpie obiettivo specifico, immettere il nome dello strumento e `-help` opzione. Ad esempio, `sharpie xcode -help` restituisce il seguente output:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

Prima di poter iniziare il processo di associazione, è necessario ottenere informazioni sui nostri SDK installati corrente immettendo il comando seguente nel terminale `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

In precedenza, possiamo vedere che abbiamo il `iphoneos8.1` SDK installato sul computer in uso. Con queste informazioni sul posto, si è pronti per analizzare il progetto InfColorPicker `.h` file iniziale **ApiDefinition.cs** e `StructsAndEnums.cs` per il progetto InfColorPicker.

Immettere il comando seguente nell'app Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Dove `[full-path-to-project]` è il percorso completo alla directory in cui il **InfColorPicker** file di progetto Xcode si trova sul computer e [iphone-os] è iOS SDK è stato installato, come indicato dal `sharpie xcode -sdks` comando. Si noti che in questo esempio è stato passato  **\*h** come parametro, che include *tutti* i file di intestazione in questa directory, in genere è consigliabile non eseguire questa operazione, ma invece leggere attentamente il i file di intestazione per trovare il primo livello **h** file che fa riferimento a tutti gli altri file rilevanti e di passa a Sharpie obiettivo.

Nell'esempio [output](walkthrough-images/os05.png) verrà generato nei Servizi terminal:

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

E **InfColorPicker.enums.cs** e **InfColorPicker.cs** file verranno creati nella nostra directory:

[![](walkthrough-images/os06.png "I file InfColorPicker.enums.cs e InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


Aprire entrambi i file nel progetto di associazione creati in precedenza. Copiare il contenuto del **InfColorPicker.cs** file e incollarlo nel **ApiDefinition.cs** file, sostituendo esistente `namespace ...` con il contenuto del blocco di codice il  **InfColorPicker.cs** file (se si lascia il `using` istruzioni intatte):

![](walkthrough-images/os07.png "Il file InfColorPickerControllerDelegate")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Aprire entrambi i file nel progetto di associazione creati in precedenza. Copiare il contenuto del **InfColorPicker.cs** file (dal **Host di compilazione Mac**) e incollarlo il **ApiDefinition.cs** file, sostituendo esistente `namespace ...` con il contenuto del blocco di codice il **InfColorPicker.cs** file (lasciando il `using` istruzioni intatte).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizzare le definizioni di API

Obiettivo Sharpie volte ha un problema di conversione `Delegates`, pertanto sarà necessario modificare la definizione del `InfColorPickerControllerDelegate` l'interfaccia e sostituire il `[Protocol, Model]` riga con le operazioni seguenti:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
In modo che la definizione è simile a:

[![](walkthrough-images/os11.png "La definizione")](walkthrough-images/os11.png#lightbox)

Successivamente, si esegue la stessa operazione con il contenuto del `InfColorPicker.enums.cs` file, copia e Incolla nel `StructsAndEnums.cs` file lasciando il `using` intatte istruzioni:

[![](walkthrough-images/os09.png "Il contenuto di StructsAndEnums.cs file ")](walkthrough-images/os09.png#lightbox)

È inoltre possibile che Sharpie obiettivo è annotato con l'associazione `[Verify]` attributi. Questi attributi indicano che è necessario verificare che l'obiettivo Sharpie ha la cosa corretta confrontando l'associazione con la dichiarazione originale di C/Objective-C (che verrà fornita in un commento sopra la dichiarazione di associazione). Dopo aver verificato le associazioni, è necessario rimuovere l'attributo di verifica. Per ulteriori informazioni, vedere il [verificare](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) Guida.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


A questo punto, il progetto di associazione deve essere completo e pronto per la compilazione. Si compila il progetto di associazione e assicurarsi che è risultata senza errori:

[Compilare il progetto di associazione e assicurarsi che non siano presenti errori](walkthrough-images/os12.png)


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


A questo punto, il progetto di associazione deve essere completo e pronto per la compilazione. Si compila il progetto di associazione e assicurarsi che è risultata senza errori.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Uso dell'associazione

Seguire questi passaggi per creare un'applicazione di esempio iPhone da usare iOS che associazione libreria creata in precedenza:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. **Creare il progetto xamarin** -aggiungere un nuovo progetto xamarin denominato **InfColorPickerSample** alla soluzione, come illustrato nelle schermate seguenti:

    ![](walkthrough-images/use01.png "Aggiunta di una visualizzazione singola di App")

    ![](walkthrough-images/use01a.png "Impostare l'identificatore")

1. **Aggiungi riferimento al progetto di associazione** -aggiornamento di **InfColorPickerSample** progetto in modo che contiene un riferimento al **InfColorPickerBinding** progetto:

    ![](walkthrough-images/use02.png "Aggiunta di riferimento al progetto di associazione")

1. **Creare l'interfaccia utente di iPhone** -fare doppio clic sul **Mainstoryboard** file nel **InfColorPickerSample** progetto per modificarla nella finestra di progettazione iOS. Aggiungere un **pulsante** alla visualizzazione e chiamarlo `ChangeColorButton`, come illustrato di seguito:

    ![](walkthrough-images/use03.png "Aggiunta di un pulsante alla visualizzazione")
1. **Aggiungere il InfColorPickerView.xib** -libreria di InfColorPicker Objective-C include un **.xib** file. Xamarin. IOS non includerà questo **.xib** nel progetto di associazione, causando errori di run-time nell'applicazione di esempio. La soluzione alternativa consiste nell'aggiungere il **.xib** file al progetto xamarin. IOS. Selezionare il progetto xamarin. IOS, pulsante destro del mouse e selezionare **Aggiungi > Aggiungi file**e aggiungere il **.xib** file come illustrato nella schermata seguente:

    ![](walkthrough-images/use04.png "Aggiungere il InfColorPickerView.xib")

1. Quando viene richiesto, copiare il **.xib** file nel progetto.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. **Creare il progetto xamarin** -aggiungere un nuovo progetto xamarin denominato **InfColorPickerSample** alla soluzione, come illustrato nella schermata seguente:

    ![](walkthrough-images/use01vs.png "Creare il progetto xamarin")

1. **Aggiungi riferimento al progetto di associazione** -aggiornamento di **InfColorPickerSample** progetto in modo che contiene un riferimento al **InfColorPickerBinding** progetto:

    ![](walkthrough-images/use02vs.png "Aggiungi riferimento al progetto di associazione")

1. **Creare l'interfaccia utente di iPhone** -fare doppio clic sul **Mainstoryboard** file nel **InfColorPickerSample** progetto per modificarla nella finestra di progettazione iOS. Aggiungere un **pulsante** alla visualizzazione e chiamarlo `ChangeColorButton`, come illustrato di seguito:

    ![](walkthrough-images/use03vs.png "Creare l'interfaccia utente di iPhone")

1. **Aggiungere il InfColorPickerView.xib** -libreria di InfColorPicker Objective-C include un **.xib** file. Xamarin. IOS non includerà questo **.xib** nel progetto di associazione, causando errori di run-time nell'applicazione di esempio. La soluzione alternativa consiste nell'aggiungere il **.xib** file al progetto xamarin dal nostro **Host di compilazione Mac**. Selezionare il progetto xamarin. IOS, pulsante destro del mouse e selezionare **Aggiungi** > **elemento esistente...** e aggiungere il **.xib** file.


-----



Successivamente, esaminiamo un rapido protocolli Objective-C e modalità di gestione delle loro associazione e il codice c#.

### <a name="protocols-and-xamarinios"></a>Protocolli e xamarin

In Objective-C, un protocollo definisce i metodi (o i messaggi) che può essere utilizzato in alcune circostanze. Concettualmente, sono molto simili alle interfacce in c#. La principale differenza tra un protocollo Objective-C e un'interfaccia in c# è che i protocolli sono metodi facoltativi - metodi che non deve implementare una classe. Objective-C viene utilizzata la @optional parola chiave viene utilizzata per indicare quali metodi sono facoltativi. Per ulteriori informazioni sui protocolli vedere [protocolli, delegati ed eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** dispone di un tale protocollo, illustrato nel frammento di codice riportato di seguito:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Questo protocollo viene utilizzato da **InfColorPickerController** per informare che l'utente ha selezionato un nuovo colore e che i clienti di **InfColorPickerController** è terminato. Sharpie obiettivo mappate questo protocollo, come illustrato nel frammento di codice seguente:

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Quando viene compilata la libreria di associazione, xamarin. IOS verrà creata una classe base astratta denominata `InfColorPickerControllerDelegate`, che implementa questa interfaccia con metodi virtuali.

Esistono due modi, che è possibile implementare questa interfaccia in un'applicazione di xamarin:

- **Sicuro delegare** -tramite un delegato sicuro comporta la creazione di una classe c# che rappresenta una sottoclasse `InfColorPickerControllerDelegate` ed esegue l'override dei metodi appropriati. **InfColorPickerController** utilizzerà un'istanza di questa classe per comunicare con i relativi client.
- **Delegato debole** -un delegato debole è una tecnica leggermente diversa che comporta la creazione di un metodo pubblico in una classe (ad esempio `InfColorPickerSampleViewController`) e quindi si espone tale metodo per il `InfColorPickerDelegate` protocollo tramite un `Export` attributo.

Delegati sicuri forniscono Intellisense, indipendenza dai tipi e migliore incapsulamento. Per questi motivi, è consigliabile utilizzare delegati sicuri in cui è possibile, invece di un delegato debole.

In questa procedura dettagliata vengono descritte entrambe le tecniche: prima implementazione di un delegato sicuro e quindi in cui viene spiegato come implementare un delegato debole.

### <a name="implementing-a-strong-delegate"></a>Implementazione di un delegato sicuro

Completare l'applicazione di xamarin. IOS tramite un delegato sicuro per rispondere al `colorPickerControllerDidFinish:` messaggio:

**Sottoclasse InfColorPickerControllerDelegate** -aggiungere una nuova classe al progetto denominato `ColorSelectedDelegate`. Modificare la classe in modo che abbia il codice seguente:

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin. IOS verrà associato il delegato Objective-C creando una classe base astratta denominata `InfColorPickerControllerDelegate`. Sottoclasse questo tipo ed eseguire l'override di `ColorPickerControllerDidFinish` metodo per accedere al valore del `ResultColor` proprietà `InfColorPickerController`.

**Creare un'istanza di ColorSelectedDelegate** -il gestore dell'evento sarà necessario un'istanza di `ColorSelectedDelegate` tipo creato nel passaggio precedente. Modificare la classe `InfColorPickerSampleViewController` e aggiungere la variabile di istanza seguente alla classe:

```csharp
ColorSelectedDelegate selector;
```

**Inizializzare la variabile ColorSelectedDelegate** - per garantire che `selector` è un valore valido dell'istanza, il metodo di aggiornamento `ViewDidLoad` in `ViewController` in modo che corrisponda il frammento di codice seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Implementare il metodo HandleTouchUpInsideWithStrongDelegate** -successivamente implementare il gestore eventi per quando l'utente tocca **ColorChangeButton**. Modifica `ViewController`e aggiungere il metodo seguente:

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

È innanzitutto ottenere un'istanza di `InfColorPickerController` tramite un metodo statico e assicurarsi di conoscere il delegato sicuro tramite la proprietà dell'istanza `InfColorPickerController.Delegate`. Questa proprietà è stata generata automaticamente per noi da Sharpie obiettivo. Infine, viene chiamato `PresentModallyOverViewController` per la visualizzazione `InfColorPickerSampleViewController.xib` in modo che l'utente può selezionare un colore.

**Eseguire l'applicazione** : A questo punto è stata completata con tutto il codice. Se si esegue l'applicazione, deve essere in grado di modificare il colore di sfondo di `InfColorColorPickerSampleView` come illustrato nelle schermate seguenti:

[![](walkthrough-images/run01.png "Esecuzione dell'applicazione")](walkthrough-images/run01.png#lightbox)

La procedura è stata completata. A questo punto si correttamente creata e associata a una libreria di Objective-C per l'utilizzo in un'applicazione di xamarin. IOS. Successivamente, esaminiamo sull'utilizzo dei delegati deboli.

### <a name="implementing-a-weak-delegate"></a>Implementazione di un delegato debole

Crea una sottoclasse di una classe associata al protocollo Objective-C per un determinato delegato, invece di xamarin consente anche di implementare i metodi di protocollo in qualsiasi classe che deriva da `NSObject`, aggiungendo i metodi con il `ExportAttribute`e quindi Fornisce i selettori appropriati. Quando si adotta questo approccio, si assegna un'istanza della classe per il `WeakDelegate` proprietà anziché al `Delegate` proprietà. Un delegato vulnerabile offre la flessibilità necessaria per richiedere la classe delegata verso il basso di una gerarchia di ereditarietà diversi. Di seguito viene illustrato come implementare e utilizzare un delegato debole nell'applicazione di xamarin. IOS.

**Creare un gestore eventi per TouchUpInside** -creare un nuovo gestore eventi per il `TouchUpInside` evento del pulsante Cambia colore di sfondo. Questo gestore di questo tipo esaurisca lo stesso ruolo del `HandleTouchUpInsideWithStrongDelegate` gestore che abbiamo creato nella sezione precedente, ma utilizzerà un delegato debole anziché un delegato sicuro. Modificare la classe `ViewController`e aggiungere il metodo seguente:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Aggiornare ViewDidLoad** -è necessario modificare `ViewDidLoad` in modo che utilizzi il gestore eventi che abbiamo appena creato. Modifica `ViewController` e modificare `ViewDidLoad` simile a quella di frammento di codice seguente:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Gestire il colorPickerControllerDidFinish: messaggio** : se il `ViewController` è completata, iOS invierà il messaggio `colorPickerControllerDidFinish:` per il `WeakDelegate`. È necessario creare un metodo c# che può gestire il messaggio. A tale scopo, creare un metodo c# e quindi per decorare con il `ExportAttribute`. Modifica `ViewController`e aggiungere il metodo seguente alla classe:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Eseguire l'applicazione. Ora dovrebbe comportarsi esattamente come in precedenza, ma utilizza un delegato debole anziché il delegato sicuro. A questo punto è stata completata questa procedura dettagliata. È stata acquisita una conoscenza di come creare e utilizzare un progetto di associazione di xamarin. IOS.

## <a name="summary"></a>Riepilogo

In questo articolo viene esaminato in dettaglio il processo di creazione e utilizzo di un progetto di associazione di xamarin. IOS. Prima di tutto è illustrato come compilare una libreria Objective-C esistente in una libreria statica. Quindi trattati come creare un progetto di associazione di xamarin. IOS e come utilizzare Sharpie obiettivo per generare le definizioni di API per la libreria Objective-C. Abbiamo illustrato come aggiornare e modificare le definizioni di API generate per poter essere utilizzate per uso pubblico. Dopo che è stato completato il progetto di associazione di xamarin. IOS, è spostato utilizzo che l'associazione in un'applicazione di xamarin. IOS, con l'obiettivo di utilizzo di delegati sicuri e delegati deboli.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di associazione (esempio)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Dettagli di associazione](~/cross-platform/macios/binding/overview.md)
- [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin per sviluppatori Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Linee guida per la progettazione di Framework](http://msdn.microsoft.com/en-us/library/ms229042.aspx)
