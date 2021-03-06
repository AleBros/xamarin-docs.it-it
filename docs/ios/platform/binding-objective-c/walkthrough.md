---
title: 'Procedura dettagliata: associazione di una libreria Objective-C di iOS'
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'associazione Novell. iOS per una libreria Objective-C esistente, InfColorPicker. Vengono trattati argomenti come la compilazione di una libreria Objective-C statica, l'associazione e l'uso dell'associazione in un'applicazione Novell. iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 67b760a58628950caa33fe9009c5023c8696691c
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946317"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Procedura dettagliata: associazione di una libreria Objective-C di iOS

_Questo articolo fornisce una procedura dettagliata per la creazione di un'associazione Novell. iOS per una libreria Objective-C esistente, InfColorPicker. Vengono trattati argomenti come la compilazione di una libreria Objective-C statica, l'associazione e l'uso dell'associazione in un'applicazione Novell. iOS._

Quando si utilizza iOS, è possibile che si verifichino casi in cui si desidera utilizzare una libreria Objective-C di terze parti. In questi casi, è possibile usare un _progetto di binding_ Novell. iOS per creare un' [ C# associazione](~/cross-platform/macios/binding/overview.md) che consentirà di utilizzare la libreria nelle applicazioni Novell. iOS.

In genere nell'ecosistema iOS è possibile trovare le librerie in 3 varianti:

- Come file di libreria statica precompilata con `.a` estensione insieme alle relative intestazioni (file con estensione h). Ad esempio, [la libreria di analisi di Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- Come Framework precompilato. Si tratta semplicemente di una cartella contenente la libreria statica, le intestazioni e talvolta risorse aggiuntive con `.framework` estensione. Ad esempio, [la libreria AdMob di Google](https://developers.google.com/admob/ios/download).
- Come solo file di codice sorgente. Ad esempio, una libreria che contiene solo `.m` e `.h` file Objective C.

Nel primo e secondo scenario è già presente una libreria statica CocoaTouch precompilata, quindi in questo articolo verrà illustrato il terzo scenario. Tenere presente che prima di iniziare a creare un'associazione, controllare sempre la licenza fornita con la libreria per assicurarsi di poterla associare.

Questo articolo fornisce una procedura dettagliata per la creazione di un progetto di associazione usando il progetto open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-c come esempio, ma tutte le informazioni contenute in questa guida possono essere adattate per l'uso con qualsiasi libreria Objective-c di terze parti. La libreria InfColorPicker offre un controller di visualizzazione riutilizzabile che consente all'utente di selezionare un colore in base alla rappresentazione HSB, rendendo la selezione di colore più intuitiva.

[![](walkthrough-images/run01.png "Example of the InfColorPicker library running on iOS")](walkthrough-images/run01.png#lightbox)

Verranno illustrati tutti i passaggi necessari per utilizzare questa particolare API Objective-C in Novell. iOS:

- Innanzitutto, verrà creata una libreria statica Objective-C con Xcode.
- Quindi, questa libreria statica verrà associata a Novell. iOS.
- A questo punto, indicare in che modo objective è in grado di ridurre il carico di lavoro generando automaticamente alcune delle definizioni API necessarie per l'associazione Novell. iOS.
- Infine, si creerà un'applicazione Novell. iOS che usa l'associazione.

Nell'applicazione di esempio viene illustrato come usare un delegato forte per la comunicazione tra l'API InfColorPicker e C# il codice. Dopo aver visto come usare un delegato forte, viene illustrato come usare i delegati vulnerabili per eseguire le stesse attività.

## <a name="requirements"></a>Requisiti

Questo articolo presuppone che l'utente abbia familiarità con Xcode e il linguaggio Objective-C ed è stata letta la documentazione relativa al [binding di Objective-c](~/cross-platform/macios/binding/index.md) . Per completare i passaggi presentati sono inoltre necessari gli elementi seguenti:

- **Xcode e iOS SDK** : Xcode di Apple e la versione più recente dell'API iOS devono essere installate e configurate nel computer dello sviluppatore.
- **[Strumenti da riga di comando Xcode](#Installing_the_Xcode_Command_Line_Tools)** : è necessario installare gli strumenti da riga di comando Xcode per la versione attualmente installata di Xcode (vedere di seguito per i dettagli di installazione).
- **Visual Studio per Mac o Visual Studio** : la versione più recente di Visual Studio per Mac o Visual Studio deve essere installata e configurata nel computer di sviluppo. Per lo sviluppo di un'applicazione Novell. iOS è necessario un Mac Apple e, quando si usa Visual Studio, è necessario essere connessi a [un host di compilazione Novell. iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **La versione più recente di Objective Sharpie** : una copia corrente dello strumento Objective Sharpe scaricato da [qui](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Se è già stato installato Objective Sharpie, è possibile aggiornarlo alla versione più recente usando il `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Installazione degli strumenti da riga di comando Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Come indicato in precedenza, verranno utilizzati gli strumenti da riga di comando Xcode (in particolare `make` e `lipo`) in questa procedura dettagliata. Il `make` comando è un'utilità UNIX molto comune che consente di automatizzare la compilazione di librerie e programmi eseguibili usando un _makefile_ che specifica come deve essere compilato il programma. Il `lipo` comando è un'utilità della riga di comando OS X per la creazione di file con più architetture. verranno combinati più file di `.a` in un unico file che può essere usato da tutte le architetture hardware.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Come indicato in precedenza, si utilizzeranno gli strumenti da riga di comando Xcode nell' **host di compilazione Mac** (in particolare `make` e `lipo`) in questa procedura dettagliata. Il `make` comando è un'utilità UNIX molto comune che consente di automatizzare la compilazione di librerie e programmi eseguibili utilizzando un _makefile_ per specificare come compilare il programma. Il `lipo` comando è un'utilità della riga di comando OS X per la creazione di file con più architetture. verranno combinati più file di `.a` in un unico file che può essere usato da tutte le architetture hardware.

-----

Secondo la compilazione di Apple [dalla riga di comando con](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) la documentazione delle domande frequenti su Xcode, in OS X 10,9 e versioni successive, il riquadro **download** della finestra di dialogo **Preferenze** di Xcode non supporta più gli strumenti da riga di comando per il download.

Per installare gli strumenti, è necessario usare uno dei metodi seguenti:

- **Installare Xcode** : quando si installa Xcode, viene fornito in bundle con tutti gli strumenti da riga di comando. Negli shim OS X 10,9 (installati in `/usr/bin`), è in grado di eseguire il mapping di qualsiasi strumento incluso in `/usr/bin` allo strumento corrispondente all'interno di Xcode. Ad esempio, il comando `xcrun`, che consente di trovare o eseguire qualsiasi strumento all'interno di Xcode dalla riga di comando.
- **Applicazione Terminal** : dall'applicazione Terminal, è possibile installare gli strumenti da riga di comando eseguendo il comando `xcode-select --install`:
  - Avviare l'applicazione Terminal.
  - Digitare `xcode-select --install` e premere **invio**, ad esempio:

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - Verrà richiesto di installare gli strumenti da riga di comando, fare clic sul pulsante **Install (installa** ):[![](walkthrough-images/xcode01.png "Installazione degli strumenti da riga di comando")](walkthrough-images/xcode01.png#lightbox)

  - Gli strumenti verranno scaricati e installati dai server Apple:[![](walkthrough-images/xcode02.png "Download degli strumenti")](walkthrough-images/xcode02.png#lightbox)

- **Download per gli sviluppatori Apple** : il pacchetto degli strumenti da riga di comando è disponibile nella pagina Web [download per sviluppatori Apple](https://developer.apple.com/downloads/index.action) . Accedere con l'ID Apple, quindi cercare e scaricare gli strumenti da riga di comando:[![](walkthrough-images/xcode03.png "Ricerca degli strumenti da riga di comando")](walkthrough-images/xcode03.png#lightbox)

Con gli strumenti da riga di comando installati, è possibile continuare con la procedura dettagliata.

## <a name="walkthrough"></a>Procedura dettagliata

In questa procedura dettagliata verranno illustrati i passaggi seguenti:

- **[Creare una libreria statica](#Creating_A_Static_Library)** : questo passaggio prevede la creazione di una libreria statica del codice Objective-C **InfColorPicker** . La libreria statica avrà l'estensione del file `.a` e verrà incorporata nell'assembly .NET del progetto di libreria.
- **[Creare un progetto di binding Novell. iOS](#Create_a_Xamarin.iOS_Binding_Project)** : dopo avere creato una libreria statica, verrà usato per creare un progetto di binding Novell. iOS. Il progetto di binding è costituito dalla libreria statica appena creata e dai metadati sotto forma C# di codice in cui viene illustrato come usare l'API Objective-C. Questi metadati sono comunemente definiti definizioni API. Per semplificare la creazione delle definizioni dell'API, si userà **[Objective a scopo](#Using_Objective_Sharpie)** .
- **[Normalizzare le definizioni dell'API](#Normalize_the_API_Definitions)** : Objective Sharpie fa un ottimo lavoro per aiutarci, ma non può eseguire tutte le operazioni. Verranno illustrate alcune modifiche che è necessario apportare alle definizioni API prima di poterle usare.
- **[Usare la libreria di binding](#Using_the_Binding)** . verrà infine creata un'applicazione Novell. iOS per illustrare come usare il nuovo progetto di associazione creato.

Ora che sono stati illustrati i passaggi necessari, è possibile passare al resto della procedura dettagliata.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Creazione di una libreria statica

Se si esamina il codice per InfColorPicker in GitHub:

[![](walkthrough-images/image02.png "Inspect the code for InfColorPicker in Github")](walkthrough-images/image02.png#lightbox)

Nel progetto è possibile visualizzare le tre directory seguenti:

- **InfColorPicker** : questa directory contiene il codice Objective-C per il progetto.
- **PickerSamplePad** : questa directory contiene un progetto iPad di esempio.
- **PickerSamplePhone** : questa directory contiene un progetto iPhone di esempio.

Scaricare il progetto InfColorPicker da [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) e decomprimerlo nella directory scelta. Aprendo la destinazione Xcode per `PickerSamplePhone` progetto, nella finestra di progettazione Xcode viene visualizzata la struttura del progetto seguente:

[![](walkthrough-images/image03.png "The project structure in the Xcode Navigator")](walkthrough-images/image03.png#lightbox)

Questo progetto ottiene il riutilizzo del codice aggiungendo direttamente il codice sorgente di InfColorPicker (nella casella rossa) a ogni progetto di esempio. Il codice per il progetto di esempio si trova all'interno della casella blu. Poiché questo particolare progetto non fornisce una libreria statica, è necessario creare un progetto Xcode per compilare la libreria statica.

Il primo passaggio consiste nell'aggiungere il codice sorgente InfoColorPicker nella libreria statica. A tale scopo, procedere come segue:

1. Avviare Xcode.
2. Dal menu **file** selezionare **nuovo** > **progetto...** :

    [![](walkthrough-images/image04.png "Starting a new project")](walkthrough-images/image04.png#lightbox)
3. Selezionare **Framework & Library**, il modello **Cocoa Touch static library** e fare clic sul pulsante **Avanti** :

    [![](walkthrough-images/image05.png "Select the Cocoa Touch Static Library template")](walkthrough-images/image05.png#lightbox)

4. Immettere `InfColorPicker` per il **nome del progetto** e fare clic sul pulsante **Avanti** :

    [![](walkthrough-images/image06.png "Enter InfColorPicker for the Project Name")](walkthrough-images/image06.png#lightbox)
5. Selezionare un percorso in cui salvare il progetto e fare clic sul pulsante **OK** .
6. A questo punto è necessario aggiungere l'origine dal progetto InfColorPicker al progetto di libreria statica. Poiché il file **InfColorPicker. h** esiste già nella libreria statica (per impostazione predefinita), Xcode non ne consente la sovrascrittura. Dal **Finder**, passare al codice sorgente InfColorPicker nel progetto originale che è stato decompresso da GitHub, copiare tutti i file InfColorPicker e incollarli nel nuovo progetto di libreria statica:

    [![](walkthrough-images/image12.png "Copy all of the InfColorPicker files")](walkthrough-images/image12.png#lightbox)

7. Tornare a Xcode, fare clic con il pulsante destro del mouse sulla cartella **InfColorPicker** e selezionare **Aggiungi file a "InfColorPicker..."** :

    [![](walkthrough-images/image08.png "Adding files")](walkthrough-images/image08.png#lightbox)

8. Nella finestra di dialogo Aggiungi file passare al file di codice sorgente InfColorPicker appena copiato, selezionarli tutti e fare clic sul pulsante **Aggiungi** :

    [![](walkthrough-images/image09.png "Select all and click the Add button")](walkthrough-images/image09.png#lightbox)

9. Il codice sorgente verrà copiato nel progetto:

    [![](walkthrough-images/image10.png "The source code will be copied into the project")](walkthrough-images/image10.png#lightbox)

10. Dallo strumento di navigazione del progetto Xcode selezionare il file **InfColorPicker. m** e impostare come commento le ultime due righe (a causa del modo in cui la libreria è stata scritta, questo file non viene usato):

    [![](walkthrough-images/image14.png "Editing the InfColorPicker.m file")](walkthrough-images/image14.png#lightbox)

11. È ora necessario controllare se sono presenti Framework necessari per la libreria. È possibile trovare queste informazioni nel file Leggimi o aprendo uno dei progetti di esempio forniti. Questo esempio USA `Foundation.framework`, `UIKit.framework`e `CoreGraphics.framework` aggiungerli.

12. Selezionare la **destinazione InfColorPicker > le fasi di compilazione** ed espandere la sezione **link Binary with Libraries** :

    [![](walkthrough-images/image16b.png "Expand the Link Binary With Libraries section")](walkthrough-images/image16b.png#lightbox)

13. Usare il pulsante **+** per aprire la finestra di dialogo che consente di aggiungere i Framework dei frame necessari elencati sopra:

    [![](walkthrough-images/image16c.png "Add the required frames frameworks listed above")](walkthrough-images/image16c.png#lightbox)

14. La sezione **link Binary with Libraries** dovrebbe avere un aspetto simile all'immagine seguente:

    [![](walkthrough-images/image16d.png "The Link Binary With Libraries section")](walkthrough-images/image16d.png#lightbox)

A questo punto ci siamo avvicinati, ma non è ancora stato fatto. La libreria statica è stata creata, ma è necessario compilarla per creare un file binario FAT che includa tutte le architetture necessarie per il dispositivo iOS e il simulatore iOS.

### <a name="creating-a-fat-binary"></a>Creazione di un file binario Fat

Tutti i dispositivi iOS hanno processori basati sull'architettura ARM sviluppate nel tempo. Ogni nuova architettura ha aggiunto nuove istruzioni e altri miglioramenti mantenendo al tempo stesso la compatibilità con le versioni precedenti. i dispositivi iOS hanno set di istruzioni ARMv6, ARMv7, armv7s e ARM64, sebbene [ARMv6 non sia più usato](~/ios/deploy-test/compiling-for-different-devices.md). Il simulatore iOS non è alimentato da ARM ed è invece un simulatore con tecnologia x86 e x86_64. Ciò significa che le librerie devono essere fornite per ogni set di istruzioni.

Una libreria FAT è `.a` file contenente tutte le architetture supportate.

La creazione di un file binario FAT è un processo in tre passaggi:

- Compilare una versione di ARM 7 & ARM64 della libreria statica.
- Compilare una versione x86 e x84_64 della libreria statica.
- Usare lo strumento da riga di comando `lipo` per combinare le due librerie statiche in una.

Sebbene questi tre passaggi siano piuttosto semplici, potrebbe essere necessario ripeterli in futuro quando la libreria Objective-C riceve gli aggiornamenti o se sono necessarie correzioni di bug. Se si decide di automatizzare questi passaggi, sarà possibile semplificare la manutenzione e il supporto futuri del progetto di binding iOS.

Sono disponibili molti strumenti per automatizzare tali attività: uno script della shell, [rake](https://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)e make. Quando si installano gli strumenti da riga di comando Xcode, viene installato anche `make`, in modo che sia il sistema di compilazione che verrà usato per questa procedura dettagliata. Di seguito è riportato un **makefile** che è possibile usare per creare una libreria condivisa multiarchitettura che funzionerà su un dispositivo iOS e il simulatore per qualsiasi libreria:

<!--markdownlint-disable MD010 -->
```makefile
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
<!--markdownlint-enable MD010 -->

Immettere i comandi **makefile** nell'editor di testo normale scelto e aggiornare le sezioni con **il nome** del progetto con il nome del progetto. È inoltre importante assicurarsi di incollare esattamente le istruzioni riportate sopra, con le schede contenute nelle istruzioni mantenute.

Salvare il file con il nome **makefile** nello stesso percorso della libreria statica InfColorPicker Xcode creata in precedenza:

[![](walkthrough-images/lib00.png "Save the file with the name Makefile")](walkthrough-images/lib00.png#lightbox)

Aprire l'applicazione Terminal nel Mac e passare al percorso del makefile. Digitare `make` nel terminale, premere **invio** e il **makefile** verrà eseguito:

[![](walkthrough-images/lib01.png "Sample makefile output")](walkthrough-images/lib01.png#lightbox)

Quando si esegue make, viene visualizzata una grande quantità di scorrimento del testo in base a. Se tutto funziona correttamente, verranno visualizzate le parole **compilazione riuscita** e i file di `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` e `libInfColorPickerSDK.a` verranno copiati nello stesso percorso del **makefile**:

[![](walkthrough-images/lib02.png "The libInfColorPicker-armv7.a, libInfColorPicker-i386.a and libInfColorPickerSDK.a files generated by the Makefile")](walkthrough-images/lib02.png#lightbox)

È possibile confermare le architetture all'interno del file binario Fat usando il comando seguente:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Verrà visualizzato quanto segue:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

A questo punto, abbiamo completato il primo passaggio dell'associazione iOS creando una libreria statica usando Xcode e gli strumenti da riga di comando Xcode `make` e `lipo`. Passiamo al passaggio successivo e utilizzeremo **Objective-Sharpie** per automatizzare la creazione dei binding API.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Creare un progetto di binding Novell. iOS

Prima di poter usare **Objective-Sharpie** per automatizzare il processo di associazione, è necessario creare un progetto di binding Novell. iOS per ospitare le definizioni delle API (che useremo **Objective-Sharpie** per la compilazione) e creare il C# binding per noi.

Eseguire le operazioni seguenti:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Visual Studio per Mac.
1. Scegliere **nuovo** > soluzione dal menu **file** **...** :

    ![](walkthrough-images/bind01.png "Starting a new solution")

1. Nella finestra di dialogo nuova soluzione selezionare **libreria** > **progetto di binding iOS**:

    ![](walkthrough-images/bind02.png "Select iOS Binding Project")

1. Fare clic sul pulsante **Avanti**.

1. Immettere "InfColorPickerBinding" come **nome del progetto** e fare clic sul pulsante **Crea** per creare la soluzione:

    ![](walkthrough-images/bind02a.png "Enter InfColorPickerBinding as the Project Name")

Verrà creata la soluzione e verranno inclusi due file predefiniti:

![](walkthrough-images/bind03.png "The solution structure in the Solution Explorer")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Avviare Visual Studio.

1. Dal menu **file** selezionare **nuovo** > **progetto...** :

    ![Avvio di un nuovo progetto](walkthrough-images/bind01vs.png "Avvio di un nuovo progetto")

1. Nella finestra di dialogo nuovo progetto selezionare **Visual C# > iPhone & iPad > libreria di binding iOS (Novell)** :

    [![selezionare la libreria associazioni iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Immettere "InfColorPickerBinding" come **nome** e fare clic sul pulsante **OK** per creare la soluzione.

Verrà creata la soluzione e verranno inclusi due file predefiniti:

![](walkthrough-images/bind03vs.png "The solution structure in the Solution Explorer")

-----

- **ApiDefinition.cs** -questo file conterrà i contratti che definiscono il modo in C#cui verrà eseguito il wrapper dell'API Objective-C.
- **Structs.cs** : questo file conterrà le strutture o i valori di enumerazione necessari per le interfacce e i delegati.

Si lavoreranno con questi due file più avanti nella procedura dettagliata. Prima di tutto, è necessario aggiungere la libreria InfColorPicker al progetto di binding.

### <a name="including-the-static-library-in-the-binding-project"></a>Inclusione della libreria statica nel progetto di associazione

Ora che è pronto il progetto di associazione di base, è necessario aggiungere la libreria binaria FAT creata in precedenza per la libreria **InfColorPicker** .

Per aggiungere la libreria, attenersi alla procedura seguente:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic con il pulsante destro del mouse sulla cartella **riferimenti nativi** nel riquadro della soluzione e scegliere **Aggiungi riferimenti nativi**:

    ![](walkthrough-images/bind04a.png "Add Native References")

1. Passare al file binario Fat creato in precedenza (`libInfColorPickerSDK.a`) e premere il pulsante **Apri** :

    ![](walkthrough-images/bind05.png "Select the libInfColorPickerSDK.a file")
1. Il file verrà incluso nel progetto:

    ![](walkthrough-images/bind04.png "Including a file")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Copiare il `libInfColorPickerSDK.a` dall' **host di compilazione Mac** e incollarlo nel progetto di binding.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi > elemento esistente...** :

    ![](walkthrough-images/bind04vs.png "Adding an existing file")

1. Passare alla `libInfColorPickerSDK.a` e premere il pulsante **Aggiungi** :

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. Il file verrà incluso nel progetto.

-----

Quando viene aggiunto **un** file al progetto, Novell. iOS imposta automaticamente l' **azione di compilazione** del file su **ObjcBindingNativeLibrary**e crea un file speciale denominato `libInfColorPickerSDK.linkwith.cs`.

Questo file contiene l'attributo `LinkWith` che indica a Novell. iOS come gestire la libreria statica appena aggiunta. Il contenuto di questo file è illustrato nel frammento di codice seguente:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

L'attributo `LinkWith` identifica la libreria statica per il progetto e alcuni flag importanti del linker.

L'operazione successiva consiste nel creare le definizioni API per il progetto InfColorPicker. Ai fini di questa procedura dettagliata, si userà Objectivey per generare il file **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Uso di Sharpe Objective

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Objective Sharpie è uno strumento da riga di comando (fornito da Novell) che può semplificare la creazione delle definizioni necessarie per associare una libreria Objective-C di C#terze parti a. In questa sezione si userà Objectivey per creare il **ApiDefinition.cs** iniziale per il progetto InfColorPicker.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Objective Sharpie è uno strumento da riga di comando (fornito da Novell) che può semplificare la creazione delle definizioni necessarie per associare una libreria Objective-C di C#terze parti a. In questa sezione si userà Objectivee Objectivey nell'host di **compilazione Mac** per creare il **ApiDefinition.cs** iniziale per il progetto InfColorPicker.

-----

Per iniziare, scaricare il file di installazione di Objective Sharpie, come descritto in questa [Guida](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Eseguire il programma di installazione e seguire tutte le istruzioni visualizzate nella procedura guidata di installazione per installare Objective Sharpie nel computer di sviluppo.

Dopo aver installato l'obiettivo Sharpie, avviare l'app Terminal e immettere il comando seguente per ottenere assistenza su tutti gli strumenti che fornisce per supportare l'associazione:

```bash
sharpie -help
```

Se si esegue il comando precedente, verrà generato l'output seguente:

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

Ai fini di questa procedura dettagliata, verranno usati gli strumenti di Sharpie seguenti:

- **Xcode** : questo strumento fornisce informazioni sull'installazione corrente di Xcode e sulle versioni delle API iOS e Mac installate. Queste informazioni verranno usate in un secondo momento durante la generazione dei binding.
- **Bind** : questo strumento verrà usato per analizzare i file con **estensione h** nel progetto InfColorPicker nei file **ApiDefinition.cs** e **StructsAndEnums.cs** iniziali.

Per ottenere informazioni sulla guida su uno specifico strumento di Sharpie, immettere il nome dello strumento e l'opzione `-help`. Ad esempio, `sharpie xcode -help` restituisce l'output seguente:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, -help           Show detailed help
  -v, -verbose        Be verbose with output

Xcode Options:
  -sdks               List all available Xcode SDKs. Pass -verbose for more
                        details.
  -sdkpath SDK        Output the path of the SDK
  -frameworks SDK     List all available framework directories in a given SDK.
```

Prima di poter avviare il processo di binding, è necessario ottenere informazioni sugli SDK installati correnti immettendo il comando seguente nel terminale `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

Dal precedente è possibile vedere che nel computer è installato `iphoneos9.3` SDK. Con queste informazioni, è possibile analizzare il progetto InfColorPicker `.h` i file nella **ApiDefinition.cs** iniziale e `StructsAndEnums.cs` per il progetto InfColorPicker.

Immettere il comando seguente nell'app Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] -scope [full-path-to-project]/InfColorPicker/InfColorPicker [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Dove `[full-path-to-project]` è il percorso completo della directory in cui si trova il file di progetto **InfColorPicker** Xcode nel computer e [iPhone-OS] è l'SDK di iOS installato, come indicato dal comando `sharpie xcode -sdks`. Si noti che in questo esempio è stato passato **\*. h** come parametro, che include *tutti* i file di intestazione in questa directory. in genere, è consigliabile evitare di eseguire questa operazione, ma leggere attentamente i file di intestazione per trovare il file con **estensione h** di primo livello che fa riferimento a tutti gli altri file rilevanti e passarlo solo a Objective Sharpe. 

> [!TIP] 
> Per l'argomento `-scope`, passare la cartella che contiene le intestazioni che si desidera associare. Senza l'argomento `-scope`, l'obiettivo Sharpie tenterà di generare binding per qualsiasi intestazione iOS SDK importata, ad esempio `#import <UIKit.h>`, ottenendo un file di definizioni molto grande che genererà errori durante la compilazione del progetto di associazione. Con il set di argomenti di `-scope`, l'obiettivo Sharpie non genererà binding per le intestazioni esterne alla cartella con ambito. 

Nel terminale verrà generato l' [output](walkthrough-images/os05.png) seguente:

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

E i file **InfColorPicker.enums.cs** e **InfColorPicker.cs** verranno creati nella directory:

[![](walkthrough-images/os06.png "The InfColorPicker.enums.cs and InfColorPicker.cs files")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Aprire entrambi i file nel progetto di Binding creato in precedenza. Copiare il contenuto del file **InfColorPicker.cs** e incollarlo nel file **ApiDefinition.cs** , sostituendo il blocco di codice `namespace ...` esistente con il contenuto del file **InfColorPicker.cs** (lasciando inalterate le istruzioni `using`):

![](walkthrough-images/os07.png "The InfColorPickerControllerDelegate file")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Aprire entrambi i file nel progetto di Binding creato in precedenza. Copiare il contenuto del file **InfColorPicker.cs** (dall'host di **compilazione Mac**) e incollarlo nel file **ApiDefinition.cs** , sostituendo il blocco di codice `namespace ...` esistente con il contenuto del file **InfColorPicker.cs** (lasciando inalterate le istruzioni `using`).

-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizzare le definizioni delle API

Obiettivo Sharpie a volte presenta un problema di traduzione `Delegates`, quindi è necessario modificare la definizione dell'interfaccia `InfColorPickerControllerDelegate` e sostituire la riga di `[Protocol, Model]` con quanto segue:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

In modo che la definizione abbia un aspetto simile al seguente:

[![](walkthrough-images/os11.png "The definition")](walkthrough-images/os11.png#lightbox)

A questo punto, viene eseguita la stessa operazione con il contenuto del file di `InfColorPicker.enums.cs`, copiando e incollando gli elementi nel file di `StructsAndEnums.cs` lasciando inalterate le istruzioni `using`:

[![](walkthrough-images/os09.png "The contents the StructsAndEnums.cs file ")](walkthrough-images/os09.png#lightbox)

È anche possibile notare che l'obiettivo Sharpie ha annotato l'associazione con `[Verify]` attributi. Questi attributi indicano che è necessario verificare che l'obiettivo Sharpie abbia fatto la corretta operazione confrontando l'associazione con la dichiarazione C/Objective-C originale (che verrà fornita in un commento sopra la dichiarazione associata). Una volta verificate le associazioni, è necessario rimuovere l'attributo Verify. Per ulteriori informazioni, vedere la guida alla [Verifica](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

A questo punto, il progetto di associazione deve essere completo e pronto per la compilazione. Si creerà il progetto di binding e si assicurerà che non si siano verificati errori:

[Compilare il progetto di associazione e verificare che non siano presenti errori](walkthrough-images/os12.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

A questo punto, il progetto di associazione deve essere completo e pronto per la compilazione. Si creerà il progetto di binding e si assicurerà che non si siano verificati errori.

-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Utilizzo dell'associazione

Seguire questa procedura per creare un'applicazione iPhone di esempio per usare la libreria di binding iOS creata in precedenza:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. **Creare un progetto Novell. iOS** : aggiungere un nuovo progetto Novell. iOS denominato **InfColorPickerSample** alla soluzione, come illustrato nelle schermate seguenti:

    ![](walkthrough-images/use01.png "Adding a Single View App")

    ![](walkthrough-images/use01a.png "Setting the Identifier")

1. **Aggiungere un riferimento al progetto di associazione** : aggiornare il progetto **InfColorPickerSample** in modo che disponga di un riferimento al progetto **InfColorPickerBinding** :

    ![](walkthrough-images/use02.png "Adding Reference to the Binding Project")

1. **Creare l'interfaccia utente di iPhone** : fare doppio clic sul file **file mainstoryboard. Storyboard** nel progetto **InfColorPickerSample** per modificarlo nella finestra di progettazione di iOS. Aggiungere un **pulsante** alla visualizzazione e chiamarlo `ChangeColorButton`, come illustrato di seguito:

    ![](walkthrough-images/use03.png "Adding a Button to the view")

1. **Aggiungere InfColorPickerView. xib** -la libreria InfColorPicker Objective-C include un file con **estensione XIB** . Novell. iOS non includerà questo **. xib** nel progetto di associazione, che causerà errori di run-time nell'applicazione di esempio. La soluzione alternativa consiste nell'aggiungere il file con **estensione XIB** al progetto Novell. iOS. Selezionare il progetto Novell. iOS, fare clic con il pulsante destro del mouse e scegliere **aggiungi > Aggiungi file**e aggiungere il file con **estensione XIB** come illustrato nello screenshot seguente:

    ![](walkthrough-images/use04.png "Add the InfColorPickerView.xib")

1. Quando viene richiesto, copiare il file con **estensione XIB** nel progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. **Creare un progetto Novell. iOS** : aggiungere un nuovo progetto Novell. iOS denominato **InfColorPickerSample** usando il modello di **app visualizzazione singola** :

    [progetto ![app iOS (Novell)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![selezionare il modello](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Aggiungere un riferimento al progetto di associazione** : aggiornare il progetto **InfColorPickerSample** in modo che disponga di un riferimento al progetto **InfColorPickerBinding** :

    ![](walkthrough-images/use02vs.png "Add Reference to the Binding Project")

1. **Creare l'interfaccia utente di iPhone** : fare doppio clic sul file **file mainstoryboard. Storyboard** nel progetto **InfColorPickerSample** per modificarlo nella finestra di progettazione di iOS. Aggiungere un **pulsante** alla visualizzazione e chiamarlo `ChangeColorButton`, come illustrato di seguito:

    ![](walkthrough-images/use03vs.png "Create the iPhone User Interface")

1. **Aggiungere InfColorPickerView. xib** -la libreria InfColorPicker Objective-C include un file con **estensione XIB** . Novell. iOS non includerà questo **. xib** nel progetto di associazione, che causerà errori di run-time nell'applicazione di esempio. La soluzione alternativa consiste nell'aggiungere il file con **estensione XIB** al progetto Novell. iOS dall'host di **compilazione Mac**. Selezionare il progetto Novell. iOS, fare clic con il pulsante destro del mouse e scegliere **aggiungi** > **elemento esistente**e aggiungere il file **. xib** .

-----

Verranno ora esaminati rapidamente i protocolli in Objective-C e il modo in cui vengono gestiti nel binding e C# nel codice.

### <a name="protocols-and-xamarinios"></a>Protocolli e Novell. iOS

In Objective-C, un protocollo definisce i metodi (o messaggi) che possono essere usati in determinate circostanze. Concettualmente, sono molto simili alle interfacce in C#. Una delle differenze principali tra un protocollo Objective-C e C# un'interfaccia è che i protocolli possono avere metodi facoltativi che non devono essere implementati da una classe. Objective-C usa la parola chiave @optional viene usata per indicare quali metodi sono facoltativi. Per ulteriori informazioni sui protocolli [, vedere eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** dispone di un protocollo di questo tipo, illustrato nel frammento di codice seguente:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Questo protocollo viene usato da **InfColorPickerController** per informare i client che l'utente ha selezionato un nuovo colore e che il **InfColorPickerController** è terminato. L'obiettivo Sharpie ha eseguito il mapping di questo protocollo, come illustrato nel frammento di codice seguente:

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

Quando viene compilata la libreria di associazione, Novell. iOS creerà una classe base astratta denominata `InfColorPickerControllerDelegate`, che implementa questa interfaccia con i metodi virtuali.

È possibile implementare questa interfaccia in un'applicazione Novell. iOS in due modi:

- Il **delegato sicuro** : l'uso di un delegato sicuro C# comporta la creazione di una classe che esegue le sottoclassi `InfColorPickerControllerDelegate` ed esegue l'override dei metodi appropriati. **InfColorPickerController** utilizzerà un'istanza di questa classe per comunicare con i relativi client.
- **Delegato debole** : un delegato debole è una tecnica leggermente diversa che prevede la creazione di un metodo pubblico in una classe, ad esempio `InfColorPickerSampleViewController`, e quindi l'esposizione del metodo al protocollo di `InfColorPickerDelegate` tramite un attributo `Export`.

I delegati Strong forniscono IntelliSense, indipendenza dai tipi e incapsulamento migliore. Per questi motivi, è consigliabile usare delegati sicuri, in cui è possibile, anziché un delegato vulnerabile.

In questa procedura dettagliata verranno illustrate entrambe le tecniche: implementare prima un delegato sicuro e quindi spiegare come implementare un delegato vulnerabile.

### <a name="implementing-a-strong-delegate"></a>Implementazione di un delegato sicuro

Terminare l'applicazione Novell. iOS usando un delegato sicuro per rispondere al messaggio di `colorPickerControllerDidFinish:`:

**Sottoclasse InfColorPickerControllerDelegate** : aggiungere una nuova classe al progetto denominato `ColorSelectedDelegate`. Modificare la classe in modo che disponga del codice seguente:

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

Novell. iOS eseguirà il binding del delegato Objective-C creando una classe base astratta denominata `InfColorPickerControllerDelegate`. Sottoclassare questo tipo ed eseguire l'override del metodo `ColorPickerControllerDidFinish` per accedere al valore della proprietà `ResultColor` di `InfColorPickerController`.

**Creare un'istanza di ColorSelectedDelegate** : il gestore eventi richiederà un'istanza del tipo di `ColorSelectedDelegate` creato nel passaggio precedente. Modificare la classe `InfColorPickerSampleViewController` e aggiungere la variabile di istanza seguente alla classe:

```csharp
ColorSelectedDelegate selector;
```

**Inizializzare la variabile ColorSelectedDelegate** : per assicurarsi che `selector` sia un'istanza valida, aggiornare il metodo `ViewDidLoad` in `ViewController` in modo che corrisponda al frammento di codice seguente:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**Implementare il metodo HandleTouchUpInsideWithStrongDelegate** -Next implementare il gestore eventi per quando l'utente tocca **ColorChangeButton**. Modificare `ViewController`e aggiungere il metodo seguente:

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

Per prima cosa si ottiene un'istanza di `InfColorPickerController` tramite un metodo statico e si fa in modo che tale istanza sia in grado di riconoscere il delegato forte tramite la proprietà `InfColorPickerController.Delegate`. Questa proprietà è stata generata automaticamente per US da Objective Sharpie. Infine, viene chiamato `PresentModallyOverViewController` per visualizzare la `InfColorPickerSampleViewController.xib` di visualizzazione in modo che l'utente possa selezionare un colore.

**Eseguire l'applicazione** : a questo punto l'operazione è stata eseguita con tutto il codice. Se si esegue l'applicazione, dovrebbe essere possibile modificare il colore di sfondo della `InfColorColorPickerSampleView` come illustrato nelle schermate seguenti:

[![](walkthrough-images/run01.png "Running the Application")](walkthrough-images/run01.png#lightbox)

Congratulazioni! A questo punto è stata creata e associata una libreria Objective-C da usare in un'applicazione Novell. iOS. Successivamente, si apprenderà come usare i delegati vulnerabili.

### <a name="implementing-a-weak-delegate"></a>Implementazione di un delegato vulnerabile

Anziché sottoclassare una classe associata al protocollo Objective-C per un delegato particolare, Novell. iOS consente anche di implementare i metodi di protocollo in qualsiasi classe che deriva da `NSObject`, decorare i metodi con l'`ExportAttribute`e quindi fornire i selettori appropriati. Quando si accetta questo approccio, si assegna un'istanza della classe alla proprietà `WeakDelegate` anziché alla proprietà `Delegate`. Un delegato debole offre la flessibilità necessaria per portare la classe Delegate a una gerarchia di ereditarietà diversa. Vediamo come implementare e usare un delegato debole nell'applicazione Novell. iOS.

**Crea gestore eventi per TouchUpInside** : consente di creare un nuovo gestore eventi per l'evento `TouchUpInside` del pulsante cambia colore di sfondo. Questo gestore riempirà lo stesso ruolo del gestore `HandleTouchUpInsideWithStrongDelegate` creato nella sezione precedente, ma userà un delegato debole anziché un delegato forte. Modificare la classe `ViewController`e aggiungere il metodo seguente:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Aggiornare ViewDidLoad** : è necessario modificare `ViewDidLoad` in modo che usi il gestore eventi appena creato. Modificare `ViewController` e modificare `ViewDidLoad` in modo simile al frammento di codice seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Gestire colorPickerControllerDidFinish: Message** -al termine dell'`ViewController`, iOS invierà il messaggio `colorPickerControllerDidFinish:` al `WeakDelegate`. È necessario creare un C# metodo in grado di gestire questo messaggio. A tale scopo, viene creato un C# metodo che viene quindi decorato con l'`ExportAttribute`. Modificare `ViewController`e aggiungere il metodo seguente alla classe:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Eseguire l'applicazione. Dovrebbe ora comportarsi esattamente come in precedenza, ma usa un delegato debole anziché il delegato forte. A questo punto è stata completata questa procedura dettagliata. A questo punto è necessario comprendere come creare e utilizzare un progetto di binding Novell. iOS.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il processo di creazione e uso di un progetto di binding Novell. iOS. In primo luogo è stato illustrato come compilare una libreria Objective-C esistente in una libreria statica. È stato quindi illustrato come creare un progetto di binding Novell. iOS e come usare Objective Sharpie per generare le definizioni API per la libreria Objective-C. È stato illustrato come aggiornare e modificare le definizioni delle API generate per renderle appropriate per l'utilizzo pubblico. Al termine del progetto di binding Novell. iOS, si è passati a usare tale binding in un'applicazione Novell. iOS, con particolare attenzione all'uso di delegati sicuri e delegati vulnerabili.

## <a name="related-links"></a>Collegamenti correlati

- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Dettagli associazione](~/cross-platform/macios/binding/overview.md)
- [Guida di riferimento ai tipi di binding](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin per sviluppatori Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Linee guida per la progettazione di Framework](https://msdn.microsoft.com/library/ms229042.aspx)
