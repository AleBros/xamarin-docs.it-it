---
title: 'Procedura dettagliata: Binding di una libreria Objective-C in iOS'
description: Questo articolo include procedure dettagliate pratiche di creazione di un'associazione di xamarin. IOS per una libreria Objective-C esistente, InfColorPicker. Vengono trattati argomenti come la compilazione di una libreria Objective-C statica, associazione e utilizzando l'associazione in un'applicazione xamarin. IOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 2897129779b698eae60338d44f9af19b6a2761bc
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252361"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Procedura dettagliata: Binding di una libreria Objective-C in iOS

_Questo articolo include procedure dettagliate pratiche di creazione di un'associazione di xamarin. IOS per una libreria Objective-C esistente, InfColorPicker. Vengono trattati argomenti come la compilazione di una libreria Objective-C statica, associazione e utilizzando l'associazione in un'applicazione xamarin. IOS._

Quando si lavora in iOS, potrebbero verificarsi casi in cui si vuole usare una libreria Objective-C di terze parti. In questi casi, è possibile usare di xamarin. IOS _progetto di associazione_ per creare un [ C# associazione](~/cross-platform/macios/binding/overview.md) che consentirà di usare la libreria nelle applicazioni xamarin. IOS.

In genere all'interno dell'ecosistema di iOS è possibile trovare le librerie di 3 tipi:

* Come file di libreria statica precompilato con `.a` estensione insieme alle relative intestazioni (file con estensione h). Ad esempio, [Library di Analitica di Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Come Framework precompilata. Si tratta semplicemente una cartella che contiene la libreria statica, intestazioni e le risorse aggiuntive in alcuni casi con `.framework` estensione. Ad esempio, [libreria AdMob di Google](https://developers.google.com/admob/ios/download).
* Come file di codice sorgente. Ad esempio, una libreria che contiene solo `.m` e `.h` file Objective C.

Nello scenario di prima e secondo già sarà presente una libreria statica CocoaTouch precompilata in modo che in questo articolo esamineremo il terzo scenario. Tenere presente che prima di iniziare a creare un'associazione, verificare sempre la licenza fornita con la libreria per verificare che sia possibile eseguirne l'associazione.

Questo articolo fornisce una procedura dettagliata di creazione di un progetto di associazione tramite open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C del progetto ad esempio, tuttavia tutte le informazioni in questa guida possono essere adattate per l'uso con qualsiasi libreria di terze parti Objective-C. La libreria InfColorPicker fornisce un controller di visualizzazione riutilizzabili che consente all'utente di selezionare un colore di base nella relativa rappresentazione HSB, rendendo più semplice la selezione del colore.

[![](walkthrough-images/run01.png "Esempio della libreria InfColorPicker che eseguono iOS")](walkthrough-images/run01.png#lightbox)

Illustreremo tutti i passaggi necessari per l'utilizzo di questa particolare API Objective-C in xamarin. ios:

- In primo luogo, si creerà una libreria statica di Objective-C con Xcode.
- Quindi, eseguiremo l'associazione la libreria statica con xamarin. IOS.
- Successivamente, Mostra come obiettivo Sharpie può ridurre il carico di lavoro tramite la generazione automatica di alcuni (ma non tutti) delle definizioni di API necessari necessari per l'associazione di xamarin. IOS.
- Infine, si creerà un'applicazione xamarin. IOS che usa l'associazione.

L'applicazione di esempio illustra come usare un delegato sicuro per la comunicazione tra l'API InfColorPicker e il C# codice. Dopo che abbiamo visto come utilizzare un delegato sicuro, illustreremo come usare i delegati deboli per eseguire le stesse attività.

## <a name="requirements"></a>Requisiti

Questo articolo si presuppone che si abbia familiarità con Xcode e la lingua in Objective-C e si dispone di lettura nostri [Binding Objective-C](~/cross-platform/macios/binding/index.md) documentazione. Inoltre, è necessario quanto segue per completare la procedura descritta:

-  **Xcode e iOS SDK** -Apple Xcode e l'API di iOS più recenti devono essere installati e configurati nei computer dello sviluppatore.
-  **[Strumenti da riga di comando Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -per la versione attualmente installata di Xcode (vedere di seguito per i dettagli di installazione) è necessario installare gli strumenti della riga di comando Xcode.
-  **Visual Studio per Mac o Visual Studio** -la versione più recente di Visual Studio per Mac o Visual Studio deve essere installata e configurata nel computer di sviluppo. Un Mac Apple è necessario per lo sviluppo di un'applicazione xamarin. IOS e quando si usa Visual Studio è necessario essere connessi a [host di compilazione un xamarin. IOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **La versione più recente dell'obiettivo Sharpie** -scaricata da una copia corrente dello strumento obiettivo Sharpie [qui](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Se hai già Sharpie obiettivo installato, è possibile aggiornarlo alla versione più recente tramite il `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Installazione degli strumenti della riga di comando Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)


Come indicato in precedenza, verrà usato Xcode strumenti da riga di comando (in particolare `make` e `lipo`) in questa procedura dettagliata. Il `make` comando è un'utilità Unix molto comune che consente di automatizzare la compilazione di programmi eseguibili e librerie con un _makefile_ che specifica come deve essere compilati automaticamente. Il `lipo` comando è un'utilità della riga di comando OS X per la creazione di architettura a più file, verrà combinare più `.a` file in un unico file che può essere usato da tutte le architetture hardware.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Come indicato in precedenza, verrà usato strumenti da riga di comando Xcode nel **Host di compilazione Mac** (in particolare `make` e `lipo`) in questa procedura dettagliata. Il `make` comando è un'utilità Unix molto comune che consente di automatizzare la compilazione di programmi eseguibili e librerie con un _makefile_ su Specifica come compilare il programma. Il `lipo` comando è un'utilità della riga di comando OS X per la creazione di architettura a più file, verrà combinare più `.a` file in un unico file che può essere usato da tutte le architetture hardware.


-----

In base a Apple [compilazione dalla riga di comando con domande frequenti su Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentazione, in OS X 10.9 e versioni successive, il **Scarica** riquadro di Xcode **preferenze** finestra di dialogo non supporta più gli strumenti da riga di comando effettuare il download.

È necessario usare uno dei metodi seguenti per installare gli strumenti:

- **Installa Xcode** : quando si installa Xcode, viene fornito in dotazione con tutti gli strumenti da riga di comando. In OS X 10.9 shim (installato nella `/usr/bin`), può eseguire il mapping di qualsiasi strumento incluso in `/usr/bin` per lo strumento corrispondente all'interno di Xcode. Ad esempio, il `xcrun` comando, che consente di individuare o eseguire qualsiasi strumento all'interno di Xcode dalla riga di comando.
- **L'applicazione Terminal** -dall'applicazione Terminal, è possibile installare gli strumenti da riga di comando eseguendo il `xcode-select --install` comando:
    - Avviare l'applicazione Terminal.
    - Tipo di `xcode-select --install` e premere **invio**, ad esempio:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Verrà richiesto per installare gli strumenti da riga di comando, scegliere il **installare** pulsante:   [![](walkthrough-images/xcode01.png "Installare gli strumenti da riga di comando")](walkthrough-images/xcode01.png#lightbox)

    - Gli strumenti verranno scaricati e installati dai server Apple:   [![](walkthrough-images/xcode02.png "Download degli strumenti")](walkthrough-images/xcode02.png#lightbox)

- **Download per sviluppatori Apple** -il pacchetto di strumenti da riga di comando è disponibile la [download per sviluppatori Apple](https://developer.apple.com/downloads/index.action) pagina web. Accedere con l'ID Apple, quindi cercare e scaricare gli strumenti da riga di comando: [![](walkthrough-images/xcode03.png "Trovare gli strumenti da riga di comando")](walkthrough-images/xcode03.png#lightbox)

Con gli strumenti da riga di comando installata, siamo pronti per continuare con la procedura dettagliata.

## <a name="walkthrough"></a>Procedura dettagliata

In questa procedura dettagliata verranno trattati i passaggi seguenti:

- **[Creare una libreria statica](#Creating_A_Static_Library)**  -questo passaggio prevede la creazione di una libreria statica delle **InfColorPicker** codice Objective-C. La libreria statica avrà il `.a` estensione di file e verranno incorporate nell'assembly .NET con il progetto di libreria.
- **[Creare un progetto di associazione di xamarin. IOS](#Create_a_Xamarin.iOS_Binding_Project)**  -dopo aver ottenuto una libreria statica, si userà per creare un progetto di associazione di xamarin. IOS. Il progetto di associazione è costituita da libreria statica appena creato e metadati sotto forma di C# codice di cui viene illustrato come utilizzare l'API di Objective-C. Questi metadati sono noto come le definizioni delle API. Useremo **[obiettivo Sharpie](#Using_Objective_Sharpie)** per consentire agli utenti con le definizioni delle API di creazione.
- **[Le definizioni delle API di normalizzare](#Normalize_the_API_Definitions)**  : obiettivo Sharpie compie un utilissimo lavoro di aiutarci a, ma non è in grado di eseguire tutti gli elementi. Illustreremo alcune modifiche che è necessario apportare alle definizioni di API prima di poter essere usati.
- **[Usare la libreria di Binding](#Using_the_Binding)**  -infine, si creerà un'applicazione xamarin. IOS per mostrare come usare il progetto di associazione appena creata.

A questo punto è comprendere quali passaggi sono coinvolti, è possibile passare al resto della procedura dettagliata.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Creazione di una libreria statica

Se si osserva il codice per InfColorPicker in Github:

[![](walkthrough-images/image02.png "Esaminare il codice per InfColorPicker in Github")](walkthrough-images/image02.png#lightbox)

È possibile osservare le seguenti directory nel progetto:

- **InfColorPicker** -questa directory contiene il codice Objective-C per il progetto.
- **PickerSamplePad** -questa directory contiene un progetto di esempio iPad.
- **PickerSamplePhone** -questa directory contiene un progetto di iPhone di esempio.

È possibile scaricare il progetto InfColorPicker [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) e decomprimerlo nella directory del nostro scelta. Aprendo la destinazione di Xcode per `PickerSamplePhone` progetto, viene visualizzata la struttura di progetto seguente nello strumento di navigazione di Xcode:

[![](walkthrough-images/image03.png "Struttura del progetto in Xcode lo strumento di spostamento")](walkthrough-images/image03.png#lightbox)

Questo progetto consente di ottenere il riutilizzo del codice aggiungendo direttamente il codice sorgente InfColorPicker (nella casella rossa) in ogni progetto di esempio. Il codice per il progetto di esempio è all'interno della casella blu. Poiché questo progetto specifico non fornirci una libreria statica, è necessario per creare un progetto Xcode per compilare la libreria statica.

Il primo passaggio è per noi aggiungere il codice sorgente InfoColorPicker nella libreria statica. A tale scopo è possibile eseguire le operazioni seguenti:

1. Avviare Xcode.
2. Dal **File** dal menu **New** > **progetto...** :

    [![](walkthrough-images/image04.png "Avvia un nuovo progetto")](walkthrough-images/image04.png#lightbox)
3. Selezionare **Framework & libreria**, il **Cocoa Touch libreria statica** modello, quindi scegliere il **Avanti** pulsante:

    [![](walkthrough-images/image05.png "Selezionare il modello di Cocoa Touch libreria statica")](walkthrough-images/image05.png#lightbox)

4. Immettere `InfColorPicker` per il **nome progetto** e fare clic sui **successivo** pulsante:

    [![](walkthrough-images/image06.png "Immettere InfColorPicker per il nome del progetto")](walkthrough-images/image06.png#lightbox)
5. Selezionare un percorso per salvare il progetto e scegliere il **OK** pulsante.
6. A questo punto è necessario aggiungere l'origine dal progetto InfColorPicker al nostro progetto di libreria statica. Poiché il **InfColorPicker.h** file esiste già nella nostra libreria statica (per impostazione predefinita), Xcode non ci consentirà di sovrascrivere. Dal **Finder**passare al codice sorgente InfColorPicker nel progetto originale che è stato decompresso da GitHub, copiare tutti i file InfColorPicker e incollarli nel nuovo progetto di libreria statica:

    [![](walkthrough-images/image12.png "Copiare tutti i file InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Tornare a Xcode, fare clic con il pulsante destro sul **InfColorPicker** cartella e selezionare **aggiungere file a "In corso InfColorPicker"** :

    [![](walkthrough-images/image08.png "Aggiunta di file")](walkthrough-images/image08.png#lightbox)

8. Nella finestra di dialogo Aggiungi file passare al file del codice sorgente InfColorPicker che abbiamo appena copiate, selezionarli tutti e fare clic sui **Add** pulsante:

    [![](walkthrough-images/image09.png "Seleziona tutto e fare clic sul pulsante Aggiungi")](walkthrough-images/image09.png#lightbox)

9. Il codice sorgente verrà copiato nel nostro progetto:

    [![](walkthrough-images/image10.png "Il codice sorgente verrà copiato nel progetto")](walkthrough-images/image10.png#lightbox)

10. Lo strumento di navigazione progetto Xcode, selezionare la **InfColorPicker.m** file e impostare come commento le ultime due righe (a causa della modalità di questa libreria è stato scritto, questo file non viene utilizzato):

    [![](walkthrough-images/image14.png "Modifica del file InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. È ora necessario controllare se esistono eventuali Framework richiesta dalla libreria. È possibile trovare queste informazioni nel file Leggimi o aprendo uno dei progetti di esempio forniti. Questo esempio viene usato `Foundation.framework`, `UIKit.framework`, e `CoreGraphics.framework` Aggiungiamo quindi li.

12. Selezionare il **InfColorPicker destinazione > Build Phases** ed espandere le **Link Binary With Libraries** sezione:

    [![](walkthrough-images/image16b.png "Espandere la sezione Link Binary With Libraries")](walkthrough-images/image16b.png#lightbox)

13. Usare la **+** pulsante per aprire la finestra di dialogo che consente di aggiungere i framework necessari frame elencate in precedenza:

    [![](walkthrough-images/image16c.png "Aggiungere che i framework necessari frame elencati sopra")](walkthrough-images/image16c.png#lightbox)

14. Il **Link Binary With Libraries** sezione dovrebbe ora essere simile all'immagine seguente:

    [![](walkthrough-images/image16d.png "La sezione Link Binary With Libraries")](walkthrough-images/image16d.png#lightbox)

A questo punto siamo Chiudi, ma non è ancora fatto. La libreria statica è stata creata, ma è necessario compilarlo per creare una file System Fat binario che include tutte le architetture obbligatorie per i dispositivi iOS e il simulatore iOS.

### <a name="creating-a-fat-binary"></a>Creazione di un file binario Fat

Tutti i dispositivi iOS hanno processori con tecnologia architettura ARM che sono stati sviluppati nel corso del tempo. Ogni nuova architettura aggiunti altri miglioramenti e nuove istruzioni pur mantenendo la compatibilità. i dispositivi iOS hanno armv6, armv7, armv7s, set di istruzioni arm64 – Sebbene [armv6 non più utilizzato](~/ios/deploy-test/compiling-for-different-devices.md). Il simulatore iOS non è basata su ARM ed è invece un x86 e x86_64 con tecnologia simulatore. Ciò significa che è necessario specificare le raccolte per ogni set di istruzioni.

È una libreria Fat `.a` file che contiene tutte le architetture supportate.

Creazione di una file System fat binario è un processo in tre passaggi:

- Compilare una versione 7 di ARM e ARM64 della libreria statica.
- Compilare una versione x86 e x84_64 della libreria statica.
- Usare il `lipo` strumento da riga di comando per combinare le due librerie statiche in una sola.

Mentre questi tre passaggi sono piuttosto semplici, potrebbe essere necessario ripeterli in seguito quando la libreria Objective-C riceve gli aggiornamenti o se si richiedono le correzioni di bug. Se si decide di automatizzare questi passaggi, semplificherà le operazioni di manutenzione future e il supporto del progetto di associazione di iOS.

Sono disponibili molti strumenti per automatizzare attività quali: uno script della shell, [rake](http://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/), e [rendere](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Quando vengono installati gli strumenti da riga di comando Xcode, `make` viene installato anche, in modo che il sistema di compilazione che verrà usato per questa procedura dettagliata. Di seguito è riportato un **Makefile** che è possibile usare per creare una libreria condivisa multi-architettura che funzionerà su un dispositivo iOS e il simulatore per qualsiasi libreria:

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

Immettere il **Makefile** comandi nell'editor di testo normale di propria scelta e aggiornare le sezioni con **YOUR-PROJECT-NAME** con il nome del progetto. È anche importante garantire che non è incollare le istruzioni riportate sopra esattamente, con le schede all'interno di istruzioni mantenute.

Salvare il file con nome **Makefile** nello stesso percorso della libreria statica Xcode InfColorPicker creato in precedenza:

[![](walkthrough-images/lib00.png "Salvare il file con il nome del Makefile")](walkthrough-images/lib00.png#lightbox)

Aprire l'applicazione di terminale nel Mac e passare al percorso del makefile utilizzato. Tipo di `make` nel terminale, premere **invio** e il **Makefile** verrà eseguito:

[![](walkthrough-images/lib01.png "Makefile di esempio di output")](walkthrough-images/lib01.png#lightbox)

Quando si esegue marca, verrà visualizzato molto scorrimento in base al testo. Se tutto ciò che funzionasse correttamente, verrà visualizzata la dicitura **compilazione ha avuto esito positivo** e il `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` e `libInfColorPickerSDK.a` verranno copiati i file nello stesso percorso il **Makefile**:

[![](walkthrough-images/lib02.png "I file libInfColorPicker armv7.a, libInfColorPicker i386.a e libInfColorPickerSDK.a generati da Makefile")](walkthrough-images/lib02.png#lightbox)

È possibile confermare le architetture all'interno del file binario Fat usando il comando seguente:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Verrà visualizzato quanto segue:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

A questo punto, abbiamo completato il primo passaggio dell'associazione iOS tramite la creazione di una libreria statica con Xcode e gli strumenti da riga di comando Xcode `make` e `lipo`. È possibile procedere al passaggio successivo e utilizzare **obiettivo Sharpie** per automatizzare la creazione delle associazioni API per noi.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Creazione di xamarin. IOS Project di associazione

Per poter usare **obiettivo Sharpie** per automatizzare il processo di associazione, è necessario creare un progetto di associazione di xamarin. IOS per ospitare le definizioni delle API (che verrà usato **obiettivo Sharpie** per consentire agli utenti compilazione) e creare il C# di associazione per noi.

È possibile eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Visual Studio per Mac.
1. Dal **File** dal menu **New** > **soluzione...** :

    ![](walkthrough-images/bind01.png "Avvio di una nuova soluzione")

1. Dalla finestra di dialogo nuova soluzione, selezionare **Library** > **associazione progetto iOS**:

    ![](walkthrough-images/bind02.png "Selezionare il progetto di Binding iOS")

1. Scegliere il **successivo** pulsante.

1. Immettere "InfColorPickerBinding" come il **nome progetto** e fare clic sui **crea** pulsante per creare la soluzione:

    ![](walkthrough-images/bind02a.png "Immettere InfColorPickerBinding come nome del progetto")

Verrà creata la soluzione e due file predefiniti verranno inclusi:

![](walkthrough-images/bind03.png "La struttura della soluzione in Esplora soluzioni")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Avviare Visual Studio.

1. Dal **File** dal menu **New** > **progetto...** :

    ![Avvia un nuovo progetto](walkthrough-images/bind01vs.png "avvia un nuovo progetto")

1. Dalla finestra di dialogo Nuovo progetto, selezionare **Visual C# > iPhone & iPad > iOS (Xamarin) libreria di binding**:

    [![Selezionare la libreria di binding iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Immettere "InfColorPickerBinding" come il **Name** e fare clic sui **OK** pulsante per creare la soluzione.

Verrà creata la soluzione e due file predefiniti verranno inclusi:

![](walkthrough-images/bind03vs.png "La struttura della soluzione in Esplora soluzioni")

-----

- **ApiDefinition.cs** -questo file conterrà i contratti che definiscono come verrà eseguito in Objective-C dell'API C#.
- **Structs.cs** : questo file conterrà eventuali strutture o valori di enumerazione che sono necessari per le interfacce e delegati.

Si lavorerà con questi due file in un secondo momento nella procedura dettagliata. In primo luogo, è necessario aggiungere la libreria InfColorPicker al progetto di associazione.

### <a name="including-the-static-library-in-the-binding-project"></a>Tra cui la libreria statica nel progetto di associazione

Ora che abbiamo l'associazione di progetto di base pronto, è necessario aggiungere la libreria Fat binari creati in precedenza per il **InfColorPicker** libreria.

Seguire questi passaggi per aggiungere la libreria:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic sui **riferimenti nativi** cartella nel riquadro della soluzione e selezionare **aggiungere i riferimenti nativi**:

    ![](walkthrough-images/bind04a.png "Aggiungere i riferimenti nativi")

1. Passare al Fat binari sono stati apportati in precedenza (`libInfColorPickerSDK.a`) e premere la **Open** pulsante:

    ![](walkthrough-images/bind05.png "Select the libInfColorPickerSDK.a file")
1. Il file verrà incluso nel progetto:

    ![](walkthrough-images/bind04.png "Inclusione di un file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copia il `libInfColorPickerSDK.a` dalle **Mac Build Host** e incollarlo nel progetto di associazione.

1. Pulsante destro del mouse sul progetto e scegliere **Aggiungi > elemento esistente...** :

    ![](walkthrough-images/bind04vs.png "Aggiunta di un file esistente")

1. Passare al `libInfColorPickerSDK.a` e premere la **Add** pulsante:

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. Il file verrà incluso nel progetto.

-----

Quando la **1).a** file viene aggiunto al progetto xamarin. IOS imposterà automaticamente la **azione di compilazione** del file da **ObjcBindingNativeLibrary**e creare un file speciale chiamato `libInfColorPickerSDK.linkwith.cs`.


Questo file contiene il `LinkWith` attributo che indica a xamarin. IOS come handle alla libreria statica viene semplicemente aggiunto. Il contenuto di questo file è illustrato nel frammento di codice seguente:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

Il `LinkWith` attributo identifica la libreria statica per il progetto e alcuni flag del linker importanti.


La prossima cosa che dobbiamo fare è creare le definizioni di API per il progetto InfColorPicker. Ai fini di questa procedura dettagliata, si userà Sharpie obiettivo per generare il file **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Usando Sharpie obiettivo

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)


Sharpie obiettivo è una riga di comando dello strumento (fornito da Xamarin) che consentono di creare le definizioni necessarie per eseguire l'associazione di una libreria Objective-C di terze parti 3rd a C#. In questa sezione si userà Sharpie descrive come creare iniziale **ApiDefinition.cs** per il progetto InfColorPicker.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Sharpie obiettivo è una riga di comando dello strumento (fornito da Xamarin) che consentono di creare le definizioni necessarie per eseguire l'associazione di una libreria Objective-C di terze parti 3rd a C#. In questa sezione si userà Sharpie obiettivo nel nostro **Host di compilazione Mac** per creare iniziale **ApiDefinition.cs** per il progetto InfColorPicker.


-----

Per iniziare, è possibile scaricare il file di programma di installazione Sharpie obiettivo come descritto in dettaglio in questo [Guida](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Eseguire il programma di installazione e seguire tutte le istruzioni sullo schermo l'installazione guidata per installare Sharpie obiettivo del nostro computer di sviluppo.

Dopo aver ottenuto correttamente Sharpie obiettivo installato, è possibile avviare l'app Terminal e immettere il comando seguente per ottenere informazioni su tutti gli strumenti che fornisce per assistere nell'associazione:

```bash
sharpie -help
```

Se si esegue il comando riportato sopra, verrà generato l'output seguente:

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

Ai fini di questa procedura dettagliata, verranno usati i seguenti strumenti Sharpie obiettivo:

- **xcode** : strumenti ci offre informazioni sulle versioni di iOS e Mac API che sono state installate e la versione installata di Xcode. Si userà queste informazioni in un secondo momento quando si genera il binding.
- **associare** -si userà questo strumento per analizzare le **. h** i file nel progetto InfColorPicker in iniziale **ApiDefinition.cs** e **StructsAndEnums.cs** file.

Per ottenere informazioni su uno strumento Sharpie obiettivo specifico, immettere il nome dello strumento e `-help` opzione. Ad esempio, `sharpie xcode -help` restituisce l'output seguente:

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

Da quanto sopra, possiamo vedere che abbiamo il `iphoneos9.3` SDK installato sul computer in uso. Con queste informazioni posto, siamo pronti analizzare il progetto InfColorPicker `.h` i file in iniziale **ApiDefinition.cs** e `StructsAndEnums.cs` per il progetto InfColorPicker.

Immettere il comando seguente nell'app Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

In cui `[full-path-to-project]` è il percorso completo alla directory in cui la **InfColorPicker** file di progetto Xcode si trova nel nostro computer e [iphone-os] è iOS SDK che sono state installate, come indicato dal `sharpie xcode -sdks` comando. Si noti che in questo esempio è stato passato  **\*h** come parametro, che include *tutti i* i file di intestazione in questa directory, in genere è consigliabile non eseguire questa operazione, ma invece leggere con attenzione il file di intestazione per trovare il primo livello **h** file che fa riferimento a tutti gli altri file rilevanti e semplicemente passarlo alla Sharpie obiettivo.

Quanto segue [output](walkthrough-images/os05.png) verrà generato nel terminale:

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

E il **InfColorPicker.enums.cs** e **InfColorPicker.cs** verranno creati file nella nostra directory:

[![](walkthrough-images/os06.png "I file InfColorPicker.enums.cs e InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)


Aprire entrambi i file nel progetto di associazione creati in precedenza. Copiare il contenuto del **InfColorPicker.cs** del file e incollarlo nella **ApiDefinition.cs** file, sostituendo l'oggetto esistente `namespace ...` blocco di codice con il contenuto del  **InfColorPicker.cs** file (lasciando il `using` istruzioni intatte):

![](walkthrough-images/os07.png "The InfColorPickerControllerDelegate file")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Aprire entrambi i file nel progetto di associazione creati in precedenza. Copiare il contenuto del **InfColorPicker.cs** file (dal **Host di compilazione Mac**) e incollarlo nella **ApiDefinition.cs** file, sostituendo l'oggetto esistente `namespace ...` blocco di codice con il contenuto del **InfColorPicker.cs** file (lasciando il `using` istruzioni intatte).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizzare le definizioni di API

Sharpie obiettivo è a volte la conversione di un problema `Delegates`, quindi sarà necessario modificare la definizione del `InfColorPickerControllerDelegate` l'interfaccia e sostituire il `[Protocol, Model]` riga con il codice seguente:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
In modo che la definizione sarà simile a:

[![](walkthrough-images/os11.png "La definizione")](walkthrough-images/os11.png#lightbox)

Successivamente, viene eseguita la stessa operazione con il contenuto del `InfColorPicker.enums.cs` file, copia e Incolla nel `StructsAndEnums.cs` file lasciando il `using` intatte le istruzioni:

[![](walkthrough-images/os09.png "Il contenuto di StructsAndEnums.cs file ")](walkthrough-images/os09.png#lightbox)

È anche possibile trovare che descrive come Sharpie ha annotate l'associazione con `[Verify]` attributi. Questi attributi indicano che è necessario verificare che obiettivo Sharpie ha l'aspetto corretto confrontando l'associazione con la dichiarazione di C/Objective-C originale (che verrà fornita in un commento sopra la dichiarazione di associazione). Dopo avere verificato i binding, è necessario rimuovere l'attributo di verifica. Per altre informazioni, vedere la [Verify](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) Guida.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)


A questo punto, il progetto di associazione deve essere completo e pronto per la compilazione. È possibile compilare il progetto di associazione e assicurarsi che si superi la senza errori:

[Compilare il progetto di associazione e assicurarsi che non siano presenti errori](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


A questo punto, il progetto di associazione deve essere completo e pronto per la compilazione. È possibile compilare il progetto di associazione e assicurarsi che si superi la senza errori.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Uso dell'associazione

Seguire questi passaggi per creare un'applicazione iPhone di esempio da usare iOS che associazione libreria creata in precedenza:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. **Creare il progetto xamarin. IOS** -aggiungere un nuovo progetto xamarin. IOS chiamato **InfColorPickerSample** alla soluzione, come illustrato negli screenshot seguenti:

    ![](walkthrough-images/use01.png "Aggiunta di un'App visualizzazione singola")

    ![](walkthrough-images/use01a.png "Impostazione dell'identificatore")

1. **Aggiungi riferimento al progetto di associazione** -aggiornamento di **InfColorPickerSample** del progetto in modo che includa un riferimento al **InfColorPickerBinding** progetto:

    ![](walkthrough-images/use02.png "Aggiunta di riferimenti al progetto di associazione")

1. **Creare l'interfaccia utente di iPhone** -fare doppio clic sui **Mainstoryboard** del file nei **InfColorPickerSample** progetto modificarla in iOS Designer. Aggiungere un **sul pulsante** alla visualizzazione e chiamarlo `ChangeColorButton`, come illustrato di seguito:

    ![](walkthrough-images/use03.png "Aggiunta di un pulsante alla visualizzazione")

1. **Aggiungere il InfColorPickerView.xib** -il InfColorPicker Objective-C library include un **xib** file. Xamarin. IOS non verranno inclusi in questo **xib** nel progetto di binding, causando errori di run-time nella nostra applicazione di esempio. La soluzione alternativa consiste nell'aggiungere il **xib** file al progetto xamarin. IOS. Selezionare il progetto xamarin. IOS, pulsante destro del mouse e selezionare **Aggiungi > Aggiungi file**e aggiungere il **xib** file come illustrato nello screenshot seguente:

    ![](walkthrough-images/use04.png "Aggiungere il InfColorPickerView.xib")

1. Quando viene richiesto, copiare il **xib** file nel progetto.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Creare il progetto xamarin. IOS** -aggiungere un nuovo progetto xamarin. IOS denominato **InfColorPickerSample** utilizzando le **App visualizzazione singola** modello:

    [![progetto di App (Xamarin) di iOS](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Seleziona modello](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Aggiungi riferimento al progetto di associazione** -aggiornamento di **InfColorPickerSample** del progetto in modo che includa un riferimento al **InfColorPickerBinding** progetto:

    ![](walkthrough-images/use02vs.png "Aggiungi riferimento al progetto di associazione")

1. **Creare l'interfaccia utente di iPhone** -fare doppio clic sui **Mainstoryboard** del file nei **InfColorPickerSample** progetto modificarla in iOS Designer. Aggiungere un **sul pulsante** alla visualizzazione e chiamarlo `ChangeColorButton`, come illustrato di seguito:

    ![](walkthrough-images/use03vs.png "Creare l'interfaccia utente di iPhone")

1. **Aggiungere il InfColorPickerView.xib** -il InfColorPicker Objective-C library include un **xib** file. Xamarin. IOS non verranno inclusi in questo **xib** nel progetto di binding, causando errori di run-time nella nostra applicazione di esempio. La soluzione alternativa consiste nell'aggiungere il **xib** file per il progetto xamarin. IOS da nostri **Host di compilazione Mac**. Selezionare il progetto xamarin. IOS, pulsante destro del mouse e selezionare **Add** > **elemento esistente...** e aggiungere i **xib** file.

-----

Successivamente, diamo un'occhiata protocolli in Objective-C e come abbiamo gestirle nell'associazione e C# codice.

### <a name="protocols-and-xamarinios"></a>Protocolli e xamarin. IOS

In Objective-C, un protocollo definisce i metodi (o i messaggi) che può essere utilizzato in alcune circostanze. Concettualmente, sono molto simili alle interfacce in C#. La principale differenza tra un protocollo Objective-C e un oggetto C# interfaccia è che i protocolli possono avere metodi facoltativi - i metodi che non deve implementare una classe. Objective-C Usa la @optional parola chiave viene usata per indicare quali metodi sono facoltativi. Per altre informazioni sui protocolli, vedere [gli eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** dispone di un tale protocollo, illustrato nel frammento di codice riportato di seguito:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Questo protocollo viene utilizzato dai **InfColorPickerController** per segnalare ai client che l'utente ha selezionato un nuovo colore e che il **InfColorPickerController** viene completata. Sharpie obiettivo mappato questo protocollo, come illustrato nel frammento di codice seguente:

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

Quando viene compilata la libreria di binding, xamarin. IOS verrà creata una classe base astratta denominata `InfColorPickerControllerDelegate`, che implementa questa interfaccia con metodi virtuali.

Esistono due modi, che è possibile implementare questa interfaccia in un'applicazione xamarin. ios:

- **Sicuro delegare** -usando un delegato sicuro prevede la creazione di un C# classe che rappresenta una sottoclasse `InfColorPickerControllerDelegate` ed esegue l'override dei metodi appropriati. **InfColorPickerController** userà un'istanza di questa classe per comunicare con i relativi client.
- **Delegato debole** -un delegato debole è una tecnica leggermente diversa che prevede la creazione di un metodo pubblico in una classe (, ad esempio `InfColorPickerSampleViewController`) e quindi nell'esposizione di tale metodo per il `InfColorPickerDelegate` protocollo tramite un `Export` attributo.

Sicuro i delegati offrono Intellisense, indipendenza dai tipi e la miglior incapsulamento. Per questi motivi, è consigliabile usare i delegati sicuri in cui è possibile, invece di un delegato debole.

In questa procedura dettagliata verranno trattati entrambe le tecniche: prima di tutto che implementa un delegato sicuro e che quindi spiega come implementare un delegato debole.

### <a name="implementing-a-strong-delegate"></a>Implementazione di un delegato sicuro

Completare l'applicazione xamarin. IOS tramite un delegato sicuro a cui per rispondere il `colorPickerControllerDidFinish:` messaggio:

**Sottoclasse InfColorPickerControllerDelegate** -aggiungere una nuova classe al progetto denominato `ColorSelectedDelegate`. Modificare la classe in modo che includa il codice seguente:

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

Xamarin. IOS verrà associato il delegato di Objective-C mediante la creazione di una classe base astratta denominata `InfColorPickerControllerDelegate`. Sottoclasse questo tipo ed eseguire l'override di `ColorPickerControllerDidFinish` metodo per accedere al valore della `ResultColor` proprietà di `InfColorPickerController`.

**Creare un'istanza di ColorSelectedDelegate** -il gestore dell'evento sarà necessaria un'istanza del `ColorSelectedDelegate` tipo creato nel passaggio precedente. Modificare la classe `InfColorPickerSampleViewController` e aggiungere la variabile di istanza seguente alla classe:

```csharp
ColorSelectedDelegate selector;
```

**Inizializzare la variabile ColorSelectedDelegate** : per verificare che `selector` è un valore valido dell'istanza, il metodo di aggiornamento `ViewDidLoad` in `ViewController` in modo che corrisponda il frammento di codice seguente:

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

È prima di tutto ottenere un'istanza di `InfColorPickerController` tramite un metodo statico e assicurarsi di conoscere il delegato sicuro tramite la proprietà dell'istanza `InfColorPickerController.Delegate`. Questa proprietà è stata generata automaticamente per noi da Sharpie obiettivo. Infine viene chiamato `PresentModallyOverViewController` per mostrare la visualizzazione `InfColorPickerSampleViewController.xib` in modo che l'utente può selezionare un colore.

**Eseguire l'applicazione** : A questo punto abbiamo finiti con tutto il codice. Se si esegue l'applicazione, dovrebbe essere possibile modificare il colore di sfondo di `InfColorColorPickerSampleView` come illustrato negli screenshot seguenti:

[![](walkthrough-images/run01.png "Esecuzione dell'applicazione")](walkthrough-images/run01.png#lightbox)

La procedura è stata completata. A questo punto si è stata creata e associata a una libreria Objective-C per l'uso in un'applicazione xamarin. IOS. Quindi, cerchiamo di comprendere l'uso dei delegati deboli.

### <a name="implementing-a-weak-delegate"></a>Implementazione di un delegato debole

Crea una sottoclasse di una classe associata al protocollo Objective-C per un determinato delegato, invece di xamarin. IOS consente inoltre di implementare i metodi del protocollo in qualsiasi classe che deriva da `NSObject`, associando al metodo con il `ExportAttribute`e quindi Fornisce i selettori appropriati. Quando si adotta questo approccio, si assegna un'istanza della classe per il `WeakDelegate` proprietà anziché al `Delegate` proprietà. Un delegato vulnerabile offre la flessibilità necessaria per sfruttare la classe delegata verso il basso di una gerarchia di ereditarietà diversi. Vediamo come implementare e utilizzare un delegato debole nella nostra applicazione xamarin. IOS.

**Creare un gestore eventi per TouchUpInside** -è possibile creare un nuovo gestore eventi per il `TouchUpInside` evento del pulsante Cambia colore di sfondo. Questo gestore di questo tipo esaurisca lo stesso ruolo del `HandleTouchUpInsideWithStrongDelegate` gestore che abbiamo creato nella sezione precedente, ma userà un delegato debole anziché un delegato sicuro. Modificare la classe `ViewController`e aggiungere il metodo seguente:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Aggiornare ViewDidLoad** -è necessario modificare `ViewDidLoad` in modo che utilizzi il gestore eventi appena creato. Modificare `ViewController` e modificare `ViewDidLoad` simile a quella di frammento di codice seguente:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Gestire il colorPickerControllerDidFinish: Messaggio** : se il `ViewController` viene completato, iOS invierà il messaggio `colorPickerControllerDidFinish:` per il `WeakDelegate`. È necessario creare un C# metodo in grado di gestire questo messaggio. A tale scopo, viene creato un C# (metodo) e quindi per decorare con il `ExportAttribute`. Modifica `ViewController`e aggiungere il metodo seguente alla classe:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Eseguire l'applicazione. Ora dovrebbe funzionare esattamente come in precedenza, ma usa un delegato debole anziché il delegato sicuro. A questo punto è stata completata questa procedura dettagliata. È stata acquisita una conoscenza di come creare e utilizzare un progetto di associazione di xamarin. IOS.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato il processo di creazione e utilizzo di un progetto di associazione di xamarin. IOS. Prima di tutto è stato illustrato come compilare una libreria Objective-C esistente in una libreria statica. Quindi viene illustrato come creare un progetto di associazione di xamarin. IOS e come usare Sharpie descrive come generare le definizioni di API per la libreria Objective-C. È stato illustrato come aggiornare e perfezionare le definizioni di API generate per fare in modo appropriato per l'uso pubblico. Dopo che è stato completato il progetto di associazione di xamarin. IOS, è stato all'utilizzo di quell'associazione in un'applicazione xamarin. IOS, con particolare attenzione sull'uso di delegati forti e deboli delegati.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di associazione (esempio)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Informazioni dettagliate sull'associazione](~/cross-platform/macios/binding/overview.md)
- [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin per sviluppatori Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Linee guida per la progettazione di Framework](https://msdn.microsoft.com/library/ms229042.aspx)
