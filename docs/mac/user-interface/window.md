---
title: WINDOWS
description: In questo articolo viene descritto l'utilizzo con windows e i pannelli in un'applicazione Xamarin.Mac. Descrive la creazione di windows e pannelli in Xcode e interfaccia generatore, caricarli dallo storyboard e i file .xib e l'utilizzo a livello di codice.
ms.topic: article
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4b8de30cecb738fecb13616a3b796c0b4fa5a51a
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="windows"></a>WINDOWS

_In questo articolo viene descritto l'utilizzo con windows e i pannelli in un'applicazione Xamarin.Mac. Descrive la creazione di windows e pannelli in Xcode e interfaccia generatore, caricarli dallo storyboard e i file .xib e l'utilizzo a livello di codice._

Quando si lavora con c# e .NET in un'applicazione Xamarin.Mac, disporre dell'accesso per le stesse finestre e pannelli che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le finestre e pannelli (o, facoltativamente, crearli direttamente nel codice c#).

In base alle finalità, un'applicazione Xamarin.Mac può presentare una o più finestre visualizzate per gestire e coordinare le informazioni vengano visualizzate e funziona con. Le funzioni dell'entità di una finestra sono:

1. Per fornire un'area in cui le visualizzazioni e i controlli possono essere inseriti e gestiti.
2. Per accettare e rispondere agli eventi in risposta all'interazione dell'utente sia la tastiera e mouse.

Windows può essere utilizzato in uno stato non modale (ad esempio un editor di testo che può avere più documenti aperti contemporaneamente) o modale (ad esempio una finestra di dialogo di esportazione che deve essere chiusa prima di continua l'applicazione).

I pannelli sono un tipo speciale di finestra (una sottoclasse della base `NSWindow` classe), utilizzati in genere per una funzione ausiliaria in un'applicazione, ad esempio windows utilità quali controlli di formato di testo e alla selezione dei colori di sistema.

[![](window-images/intro01.png "La modifica di una finestra in Xcode")](window-images/intro01.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo di Windows e i pannelli in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introduzione a Windows

Come descritto in precedenza, una finestra è disponibile un'area in cui le visualizzazioni e i controlli possono essere inseriti e gestiti e risponde a eventi basati sull'interazione dell'utente (tramite mouse o tastiera).

In base a Apple, esistono cinque tipi principali di Windows in un macOS App:

- **Finestra documento** -una finestra del documento contiene dati basati su file utente, ad esempio un foglio di calcolo o di un documento di testo.
- **Finestra dell'app** -una finestra dell'app è la finestra principale di un'applicazione che non è basato su documento (ad esempio, l'app del calendario in un Mac).
- **Pannello** : un pannello mobile sopra altre finestre e fornisce gli strumenti o aprire i controlli che gli utenti possono lavorare con mentre sono documenti. In alcuni casi, può essere trasparente un pannello (ad esempio quando si utilizzano grafici di grandi dimensioni).
- **Finestra di dialogo** -una finestra di dialogo viene visualizzata in risposta a un'azione dell'utente e in genere fornisce agli utenti di modi di completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima di chiuderla. (Vedere [utilizzo di finestre di dialogo](~/mac/user-interface/dialog.md))
- **Avvisi** -un avviso è un tipo speciale di finestra di dialogo visualizzata quando si verifica un problema grave (ad esempio un errore) o come un avviso (ad esempio, la preparazione dell'eliminazione di un file). Poiché un avviso è una finestra di dialogo, è necessario inoltre una risposta dell'utente prima di chiuderla. (Vedere [utilizzo degli avvisi](~/mac/user-interface/alert.md))

Per ulteriori informazioni, vedere il [informazioni su Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sezione di Apple [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main, chiave e finestre non attive

Windows in un'applicazione Xamarin.Mac possibile aspetto e comportamento in modo diverso in base a come l'utente è attualmente l'interazione con essi. La finestra di App che attualmente lo stato attivo dell'attenzione dell'utente o al documento innanzitutto viene chiamato il _finestra principale_. Nella maggior parte dei casi questa finestra sarà anche il _finestra chiave_ (la finestra che attualmente accetta l'input dell'utente). Ma non sempre è il caso, ad esempio, una selezione colori Impossibile sia aperto e la finestra di chiave che interagisce con l'utente di modificare lo stato di un elemento nella finestra del documento (che sarebbe comunque la finestra principale).

La principale e le finestre di chiave (se sono separati) sono sempre attive, _finestre non attive_ vengono aperte le finestre non in primo piano. Ad esempio, un'applicazione di editor di testo può avere più di un documento aperto in un momento, solo la finestra principale è attiva, tutti gli altri sarebbero inattivi. 

Per ulteriori informazioni, vedere il [informazioni su Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sezione di Apple [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Denominazione di Windows

Una finestra è possibile visualizzare una barra del titolo e quando viene visualizzato il titolo, in genere è il nome dell'applicazione, il nome del documento in fase di lavorazione o la funzione della finestra (ad esempio controllo). Alcune applicazioni non visualizzano una barra del titolo, perché sono riconoscibili dalla vista e non funzionano con i documenti.

Apple Suggerire le linee guida seguenti:

- Utilizzare il nome dell'applicazione per il titolo di una finestra principale, non di documento. 
- Nome di una nuova finestra del documento `untitled`. Per il nuovo documento, non aggiungere un numero per il titolo (ad esempio `untitled 1`). Se l'utente crea un altro documento prima di salvare e titolazione il primo, chiamare tale finestra `untitled 2`, `untitled 3`e così via.

Per ulteriori informazioni, vedere il [denominazione Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) sezione di Apple [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows a schermo intero

In macOS, finestra di un'applicazione può passare nascondere schermo tutti gli elementi inclusi la barra dei Menu di applicazione (che può essere protetta spostando il cursore nella parte superiore dello schermo) per fornire distrazione libero interazione è contenuto.

Apple suggerisce le linee guida seguenti:

- Determinare se è utile per una finestra di visualizzazione a schermo intero. Le applicazioni che forniscono le interazioni breve (ad esempio una calcolatrice) non devono fornire una modalità schermo intero.
- Mostra la barra degli strumenti se richiede l'attività a schermo intero. In genere, la barra degli strumenti è nascosta in modalità schermo intero.
- La finestra a schermo intero è necessario tutti gli utenti le funzionalità necessarie per completare l'attività.
- Se possibile, evitare l'interazione di ricerca, mentre l'utente è in una finestra a schermo intero.
- Sfruttare lo spazio sullo schermo maggiore senza spostando l'attenzione dalle attività principale.

Per ulteriori informazioni, vedere il [schermo Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) sezione di Apple [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Pannelli

Un pannello è una finestra ausiliaria che contiene i controlli e le opzioni che influiscono sulla selezione (ad esempio, alla selezione dei colori di sistema) o il documento attivo:

[![](window-images/panel01.png "Un pannello del colore")](window-images/panel01.png#lightbox)

Possono essere pannelli _specifico dell'App_ o _a livello di sistema_. Pannelli specifico dell'App mobile nella parte superiore delle finestre di documento dell'applicazione e scompaiono quando l'applicazione è in background. Pannelli a livello di sistema (ad esempio il **tipi di carattere** pannello), float, nella parte superiore di tutte le finestre aperte indipendentemente dall'applicazione. 

Apple suggerisce le linee guida seguenti:

- In generale, usare un pannello standard, i pannelli transparent devono essere utilizzati solo quando strettamente necessario e per le attività grafiche.
- È consigliabile usare un pannello dare agli utenti di accedere facilmente ai controlli importanti o informazioni che influisce direttamente sulla propria attività.
- Nascondere e mostrare i pannelli in base alle esigenze.
- Pannelli devono sempre includere sulla barra del titolo.
- Pannelli non devono includere un pulsante di riduzione a icona attivo.

#### <a name="inspectors"></a>controlli

Più moderne applicazioni macOS presentano ausiliari controlli e le opzioni che influiscono sul documento attivo o la selezione come _controlli_ che fanno parte della finestra principale (ad esempio il **pagine** app mostrata sotto), anziché utilizzare pannello Windows:

[![](window-images/panel02.png "Un controllo di esempio")](window-images/panel02.png#lightbox)

Per ulteriori informazioni, vedere il [pannelli](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) e nel [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) app di esempio per un'implementazione completa di un **Controllo interfaccia** in un'app Xamarin.Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Creazione e gestione di Windows in Xcode

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita è ottenere una finestra vuota, standard. Questo windows è definito in un `.storyboard` file automaticamente incluso nel progetto. Per modificare la progettazione di windows, nel **Esplora**, fare doppio clic il `Main.storyboard` file:

[![](window-images/edit01.png "Selezione storyboard principale")](window-images/edit01.png#lightbox)

La progettazione della finestra verrà aperta in Generatore del Xcode di interfaccia:

[![](window-images/edit02.png "Modifica l'interfaccia utente in Xcode")](window-images/edit02.png#lightbox)

Nel **controllo attributo**, esistono diverse proprietà che è possibile utilizzare per definire e controllare la finestra:

- **Titolo** -questo è il testo che verrà visualizzato nella barra del titolo della finestra.
- **Salvataggio automatico** -si tratta di _chiave_ che verrà usato per l'ID di finestra quando è posizione e le impostazioni vengono salvate automaticamente.
- **Barra del titolo** -una barra del titolo di visualizzare la finestra.
- **Titolo e barra degli strumenti unificati** - se la finestra include una barra degli strumenti deve essere parte della barra del titolo.
- **Dimensioni di visualizzazione del contenuto completo** -consente l'area del contenuto della finestra per essere sotto la barra del titolo.
- **Shadow** -finestra dispone di un'ombreggiatura.
- **Trama** -trama windows possono utilizzare effetti (ad esempio vitalità) e possono essere spostate trascinando un punto qualsiasi sulla loro corpo.
- **Chiudere** -finestra dispone di un pulsante Chiudi.
- **Ridurre al minimo** -finestra dispone di un pulsante Riduci a icona.
- **Ridimensionare** -finestra dispone di un controllo di ridimensionamento.
- **Pulsante della barra degli strumenti** -finestra dispone di un pulsante della barra degli strumenti Mostra/Nascondi.
- **Ripristinare** -è posizione e le impostazioni, viene salvato e ripristinato automaticamente la finestra.
- **Visibile all'avvio** -finestra verrà visualizzata automaticamente quando il `.xib` viene caricato alcun file.
- **Nascondere su disattivazione** -viene nascosta la finestra quando l'applicazione passerà in background.
- **Rilasciare chiuso quando** -è la finestra eliminata dalla memoria quando viene chiuso.
- **Sempre visualizzare le descrizioni comandi** -sono le descrizioni comandi visualizzate costantemente.
- **Ricalcola il ciclo di visualizzazione** -viene ricalcolato prima la finestra viene disegnata l'ordine di visualizzazione.
- **Spazi**, **Exposé** e **ciclo** -definiscono il comportamento di finestra in questi ambienti macOS. 
- **Schermo** -determina se questa finestra è possibile immettere la modalità schermo intero. 
- **Animazione** -controlla i tipi di animazione disponibili per la finestra.
- **Aspetto** -controlla l'aspetto della finestra. Per ora è disponibile solo un aspetto, azzurro.

Vedere Apple [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) e [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) documentazione per maggiori dettagli.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Impostazione delle dimensioni predefinite e percorso

Per impostare la posizione iniziale della finestra e della dimensione del controllo, passare il **dimensioni controllo**:

[![](window-images/edit07.png "Le dimensioni e posizione predefinite")](window-images/edit07.png#lightbox)

Da qui è possibile impostare le dimensioni iniziali della finestra, assegnargli una dimensione minima e massima, impostare il percorso iniziale sullo schermo e i bordi intorno alla finestra di controllo.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>L'impostazione di un Controller personalizzato finestra principale

Per essere in grado di creare i punti vendita e le azioni per esporre elementi dell'interfaccia utente in codice c#, l'app Xamarin.Mac dovrà essere utilizzando un Controller di finestra personalizzati.

Seguire questa procedura:

1. Aprire Storyboard dell'app in Generatore di interfaccia del Xcode.
2. Selezionare il `NSWindowController` nell'area di progettazione.
3. Passare il **controllo di identità** consente di visualizzare e `WindowController` come il **nome classe**: 

    [![](window-images/windowcontroller01.png "L'impostazione del nome di classe")](window-images/windowcontroller01.png#lightbox)
4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Oggetto `WindowController.cs` file verrà aggiunto al progetto nel **Esplora** in Visual Studio per Mac: 

    [![](window-images/windowcontroller02.png "Selezionare il controller di windows")](window-images/windowcontroller02.png#lightbox)
6. Riaprire lo Storyboard in Generatore di interfaccia del Xcode.
7. Il `WindowController.h` file sarà disponibile per l'utilizzo: 

    [![](window-images/windowcontroller03.png "Modifica del file WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Aggiunta di elementi dell'interfaccia utente

Per definire il contenuto di una finestra, trascinare i controlli dal **controllo libreria** sul **Editor dell'interfaccia**. Consultare il nostro [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentazione per ulteriori informazioni sull'utilizzo di generatore di interfaccia per creare e abilitare i controlli.

Ad esempio, trascinare una barra degli strumenti dal **controllo libreria** nella finestra nel **Editor dell'interfaccia**:

[![](window-images/edit03.png "Selezione di una barra degli strumenti dalla libreria")](window-images/edit03.png#lightbox)

Trascinare quindi un **visualizzazione testo** e ridimensionarlo per riempire l'area sotto la barra degli strumenti:

[![](window-images/edit04.png "Aggiunta di una visualizzazione di testo")](window-images/edit04.png#lightbox)

Poiché si desidera che il **visualizzazione testo** per compattare e man mano che le modifiche delle dimensioni della finestra, si passa al **Editor vincoli di** e aggiungere i vincoli seguenti:

[![](window-images/edit05.png "La modifica di vincoli")](window-images/edit05.png#lightbox)

Facendo clic il per **rossi si-raggi di** nella parte superiore dell'editor e facendo clic su **aggiungere vincoli 4**, si definiranno la visualizzazione del testo soffermeremo coordinate determinato X, Y ed espansione o riduzione orizzontalmente e verticalmente come la finestra viene ridimensionata.

Infine, si espongono i **visualizzazione testo** al codice che usa un **presa** (assicurandosi di selezionare il `ViewController.h` file):

[![](window-images/edit06.png "Configurazione di una presa di corrente")](window-images/edit06.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Per ulteriori informazioni sull'utilizzo di **prese** e **azioni**, consultare il nostro [presa e azione](~/mac/get-started/hello-mac.md#Outlets_and_Actions) documentazione.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flusso di lavoro di finestra standard

Per le finestre che creare e utilizzare con l'applicazione Xamarin.Mac, il processo è fondamentalmente identico cosa effettuate in precedenza:

1. Per nuove finestre che non sono predefiniti aggiunti automaticamente al progetto, aggiungere una nuova definizione di finestra per il progetto. In questo argomento verrà descritte in dettaglio di seguito.
2. Fare doppio clic su di `Main.storyboard` file da aprire la progettazione della finestra per la modifica in Generatore del Xcode di interfaccia.
3. Trascinare una nuova finestra nella progettazione dell'interfaccia utente e associare la finestra nella finestra principale utilizzando _Segues_ (per ulteriori informazioni, vedere il [Segues](~/mac/platform/storyboards/indepth.md#Segues) sezione del nostro [utilizzo storyboard](~/mac/platform/storyboards/indepth.md) documentazione).
3. Impostare le proprietà necessarie finestra **controllo attributo** e **dimensioni controllo**.
4. Trascinare i controlli necessari per compilare un'interfaccia e configurarli nel **controllo attributo**.
5. Utilizzare il **dimensioni controllo** per gestire il ridimensionamento di elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della finestra di codice c# tramite **prese** e **azioni**.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora che è disponibile una finestra di base creata, vedremo durante i processi tipiche un Xamarin.Mac applicazione esegue quando si lavora con windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>La visualizzazione della finestra predefinita

Per impostazione predefinita, una nuova applicazione Xamarin.Mac visualizzerà automaticamente la finestra definita nel `MainWindow.xib` file quando viene avviata:

[![](window-images/display01.png "Una finestra di esempio in esecuzione")](window-images/display01.png#lightbox)

Poiché è stata modificata la progettazione di tale finestra precedente, ora include un valore predefinito della barra degli strumenti e **visualizzazione testo** controllo. La seguente sezione di `Info.plist` file è responsabile per la visualizzazione di questa finestra:

[![](window-images/display00.png "Modifica Info. plist")](window-images/display00.png#lightbox)

Il **interfaccia principale** elenco a discesa consente di selezionare lo Storyboard che verrà utilizzato come l'app principale dell'interfaccia utente (in questo caso `Main.storyboard`).

Un Controller di visualizzazione viene automaticamente aggiunto al progetto per controllare Windows principale che viene visualizzato (insieme alla relativa visualizzazione primaria). In cui è definito il `ViewController.cs` file e collegato al **proprietario del File** in Generatore di interfaccia nel **controllo di identità**:

[![](window-images/display02.png "Impostare il proprietario del file")](window-images/display02.png#lightbox)

Per la finestra, si desidera venga dispone di un titolo di `untitled` quando dell'apertura andiamo sostituzione il `ViewWillAppear` metodo il `ViewController.cs` per essere simile al seguente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Si sta impostando il valore della finestra `Title` proprietà nel `ViewWillAppear` anziché il `ViewDidLoad` metodo perché, mentre la vista può essere caricata in memoria, non non è ancora completamente crearne un'istanza. Se si è tentato di accedere il `Title` proprietà nel `ViewDidLoad` metodo si otterrebbe un `null` eccezione poiché la finestra non è stata costruita e cablate-fino alla proprietà ancora.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Chiusura di una finestra a livello di codice

Talvolta potrebbe essere che si desidera chiudere a livello di codice di una finestra in un'applicazione Xamarin.Mac, diverso da con l'utente, fare clic su della finestra **chiudere** pulsante o utilizzando una voce di menu. macOS fornisce due modi diversi per chiudere un `NSWindow` a livello di programmazione: `PerformClose` e `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

La chiamata di `PerformClose` metodo di un `NSWindow` simula il clic dell'utente della finestra **Chiudi** pulsante momentaneamente selezionando il pulsante e quindi chiudere la finestra.

Se l'applicazione implementa il `NSWindow`del `WillClose` evento, verrà generato prima della chiusura di finestra. Se l'evento restituisce `false`, quindi la finestra non verrà chiuso. Se la finestra non dispone di un **Chiudi** pulsante o non può essere chiusa per qualsiasi motivo, il sistema operativo genererà il segnale acustico di avviso.

Ad esempio:

```csharp
MyWindow.PerformClose(this);
```

Tenta di chiudere il `MyWindow` `NSWindow` istanza. Se eseguita correttamente, verrà chiusa la finestra, in caso contrario verrà creato il segnale acustico di avviso e verrà eseguito il rimarrà aperto.

<a name="Close" />

### <a name="close"></a>Chiudi

La chiamata di `Close` metodo di un `NSWindow` non simula il clic dell'utente della finestra **Chiudi** pulsante evidenziando temporaneamente il pulsante, semplicemente chiude la finestra.

Una finestra non deve essere visibile per essere chiuso e un `NSWindowWillCloseNotification` notifica verrà registrata l'area di notifica predefinito per la finestra viene chiusa.

Il `Close` metodo differisce da due aspetti importanti di `PerformClose` metodo:

1. Non tenta di generare il `WillClose` evento.
2. Non viene simulata l'utente facendo clic di **Chiudi** pulsante evidenziando temporaneamente il pulsante.

Ad esempio:

```csharp
MyWindow.Close();
```

Per chiudere la `MyWindow` `NSWindow` istanza.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenuto modificato Windows

In macOS, Apple ha fornito un modo per informare l'utente che il contenuto di una finestra (`NSWindow`) è stato modificato dall'utente e deve essere salvata. Se la finestra contiene contenuto modificato, verrà visualizzato un piccolo punto nero nel relativo **Chiudi** widget:

[![](window-images/close01.png "Una finestra con il marcatore modificato")](window-images/close01.png#lightbox)

Se l'utente tenta di chiudere la finestra o uscire dall'App Mac mentre sono presenti modifiche non salvate modifiche al contenuto della finestra, è necessario presentare un [la finestra di dialogo](~/mac/user-interface/dialog.md) o [foglio modale](~/mac/user-interface/dialog.md) e consentire all'utente di salvare le modifiche prima:

[![](window-images/close02.png "Salvataggio foglio viene visualizzato quando la finestra è chiusa")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Contrassegno di una finestra come modificata

Per contrassegnare una finestra come la modifica del contenuto, utilizzare il codice seguente:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

E dopo la modifica è stata salvata, cancellare il flag modificato utilizzando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Salvare le modifiche prima di chiudere una finestra

Per controllare la chiusura di una finestra e consentendo loro l'utente di salvare il contenuto modificato in anticipo, è necessario creare una sottoclasse di `NSWindowDelegate` ed eseguire l'override relativo `WindowShouldClose` metodo. Ad esempio:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWidowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWidowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on resu;t
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

Per collegare un'istanza del delegato per la finestra, utilizzare il codice seguente:

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Salvare le modifiche prima di chiudere l'App

Infine, Xamarin.Mac App deve verificare se una delle finestre contengono contenuto modificato e consentire all'utente di salvare le modifiche prima di uscire. A tale scopo, modificare il `AppDelegate.cs` file, eseguire l'override di `ApplicationShouldTerminate` metodo e renderlo simile al seguente:

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>Utilizzo di più finestre

Maggior parte delle applicazioni Mac basato su documento è possibile modificare più documenti allo stesso tempo. Ad esempio, un editor di testo può essere più file di testo aperto per la modifica allo stesso tempo. Per impostazione predefinita, la nuova applicazione Xamarin.Mac ha un **File** menu con un **New** elemento automaticamente reti cablate-fino al `newDocument:` **azione**.

Verrà per attivare questo nuovo elemento e consentire all'utente di aprire più copie della finestra principale di modifica contemporanea di più documenti.

Modifica il nostro `AppDelegate.cs` file e aggiungere le seguenti proprietà calcolati:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Si userà per tenere traccia del numero di file non salvati in modo è possibile fornire feedback all'utente (per le linee guida di Apple come descritto in precedenza).

Successivamente, aggiungere il metodo seguente:

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Questo codice crea una nuova versione di questo Controller di finestra, carica la nuova finestra, rende della serie principale e finestra chiave e imposta il titolo. Se si esegue l'applicazione e selezionare **New** dal **File** menu una nuova finestra dell'editor verrà aperto e visualizzata:

[![](window-images/display04.png "È stata aggiunta una nuova finestra senza nome")](window-images/display04.png#lightbox)

Se si apre il **Windows** menu, è possibile visualizzare l'applicazione viene automaticamente rilevamento e la gestione di nostri finestre aperte:

[![](window-images/display05.png "Il menu di Windows")](window-images/display05.png#lightbox)

Per ulteriori informazioni sull'utilizzo dei menu in un'applicazione Xamarin.Mac, consultare il nostro [uso dei menu](~/mac/user-interface/menu.md) documentazione.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Ottenere la finestra attualmente attiva

In un'applicazione Xamarin.Mac che è possibile aprire più finestre (documenti), vi sono casi quando è necessario ottenere la finestra corrente, in primo piano (la chiave finestra). Il codice seguente restituisce la finestra di chiave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Può essere chiamato in qualsiasi classe o metodo che deve accedere alla finestra corrente, key. Se non è aperta alcuna finestra, verrà restituito `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>L'accesso a tutte le finestre di App

Talvolta potrebbe essere in cui è necessario accedere a tutte le finestre che l'app Xamarin.Mac ha attualmente aperto. Ad esempio, per vedere se un file che l'utente sta tentando di aprire è già aperto in una finestra esistente.

Il `NSApplication.SharedApplication` gestisce un `Windows` proprietà che contiene una matrice di tutte le finestre aperte nell'app. È possibile scorrere la matrice per accedere a tutti di windows corrente dell'applicazione. Ad esempio:

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Nell'esempio di codice ci stiamo cast ogni finestra restituito per l'oggetto personalizzato `ViewController` classe nella nostra app e la verifica del valore di un oggetto personalizzato `Path` proprietà rispetto al percorso di un file in cui l'utente desidera aprire. Se il file è già aperto, tale finestra viene portata in primo piano.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Regolare le dimensioni della finestra nel codice

Vi sono casi quando l'applicazione deve ridimensionare una finestra nel codice. Per ridimensionare e riposizionare una finestra è regolare del `Frame` proprietà. Per regolare la dimensione di una finestra, in genere è necessario anche regolare di origine, per mantenere la finestra nello stesso percorso a causa di sistema di coordinate del macOS.

A differenza di iOS in cui l'angolo superiore sinistro rappresenta (0,0), macOS utilizza un sistema di coordinate matematici in cui l'angolo inferiore sinistro della schermata rappresenta (0,0). In iOS le coordinate aumentare quando si sposta verso il basso verso destra. In Mac OS, le coordinate aumentano in valore verso l'alto a destra. 

Esempio di codice seguente ridimensiona una finestra:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Quando si modifica una dimensione di windows e una posizione nel codice, è necessario assicurarsi che rispettare le dimensioni minime e massime che sono stati impostati nel generatore di interfaccia. Questo non verrà automaticamente eseguito e sarà in grado di visualizzare la finestra di dimensioni maggiori o minori rispetto a questi limiti.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Monitoraggio delle modifiche alle dimensioni di finestra

Talvolta potrebbe essere in cui è necessario monitorare le modifiche in dimensioni di una finestra all'interno dell'app Xamarin.Mac. Ad esempio, per ridisegnare il contenuto per adattarli al nuovo formato.

Per monitorare le modifiche delle dimensioni, assicurarsi innanzitutto di avere assegnato una classe personalizzata per il Controller di finestra in Generatore del Xcode di interfaccia. Ad esempio, `MasterWindowController` nell'esempio seguente:

[![](window-images/resize01.png "Il controllo di identità")](window-images/resize01.png#lightbox)

Successivamente, modificare la classe Controller finestra personalizzati e il monitor di `DidResize` eventi nella finestra del Controller per ricevere notifica delle modifiche delle dimensioni in tempo reale. Ad esempio:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Facoltativamente, è possibile utilizzare il `DidEndLiveResize` eventi per ricevere notifiche solo dopo che l'utente ha completato la modifica delle dimensioni della finestra. Di seguito è riportato un esempio:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>L'impostazione di una finestra del titolo e rappresentato File

Quando si utilizza windows che rappresentano i documenti, `NSWindow` ha un `DocumentEdited` proprietà che, se impostata su `true` Visualizza un punto piccolo pulsante Chiudi per consentire all'utente un'indicazione che il file è stato modificato e deve essere salvato prima della chiusura.

Modifica il nostro `ViewController.cs` file e apportare le modifiche seguenti:

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

Ci stiamo anche il monitoraggio di `WillClose` evento nella finestra e verificare lo stato del `DocumentEdited` proprietà. Se è `true` è necessario consentire all'utente la possibilità di salvare le modifiche al file. Se si esegue l'app e immettere del testo, verrà visualizzato il punto:

[![](window-images/file01.png "Una finestra di modifica")](window-images/file01.png#lightbox)

Se si tenta di chiudere la finestra, è visualizzato un avviso:

[![](window-images/file02.png "Visualizzazione di un salvataggio finestra di dialogo")](window-images/file02.png#lightbox)

Se si sta caricando un documento da un file è possibile impostare il titolo della finestra per il file denominato utilizzando il `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` metodo (dato che `path` è una stringa che rappresenta il file viene aperto). Inoltre, è possibile impostare l'URL del file utilizzando il `window.RepresentedUrl = url;` metodo.

Se l'URL fa riferimento a un tipo di file noto dal sistema operativo, verrà visualizzata la icona nella barra del titolo. Se il clic destro sull'icona, verrà visualizzato il percorso del file.

Modifica il `AppDelegate.cs` file e aggiungere il metodo seguente:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

Se Esegui l'app, selezionare **Apri...**  dal **File** menu, seleziona un file di testo dal **aprire** finestra di dialogo casella e aprirlo:

[![](window-images/file03.png "La finestra di dialogo Apri")](window-images/file03.png#lightbox)

Verrà visualizzato il file e il titolo verrà impostato con l'icona del file:

[![](window-images/file04.png "Il contenuto di un file caricato")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Aggiunta di una nuova finestra in un progetto

A parte la finestra principale del documento, un'applicazione Xamarin.Mac potrebbe essere necessario visualizzare altri tipi di windows per l'utente, ad esempio le preferenze o pannelli di controllo.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore del Xcode di interfaccia.
2. Trascinare una nuova **finestra Controller** dal **libreria** e rilasciarla sul **area di progettazione**:

    [![](window-images/new01.png "Selezione di un nuovo Controller di finestra nella libreria")](window-images/new01.png#lightbox)
3. Nel **controllo di identità**, immettere `PreferencesWindow` per il **ID Storyboard**: 

    [![](window-images/new02.png "Impostazione dell'ID di storyboard")](window-images/new02.png#lightbox)
5. Progettare l'interfaccia: 

    [![](window-images/new03.png "Progettazione dell'interfaccia utente")](window-images/new03.png#lightbox)
6. Aprire il Menu di App (`MacWindows`), selezionare **preferenze...** , CTRL + clic e trascinare la nuova finestra: 

    [![](window-images/new05.png "Creazione di un segue")](window-images/new05.png#lightbox)
7. Selezionare **Mostra** dal menu di scelta rapida.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue il codice e selezionare il **preferenze...**  dal **Menu applicazione**, verrà visualizzata la finestra:

[![](window-images/new04.png "Un menu Preferenze di esempio")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Utilizzo di pannelli

Come indicato all'inizio di questo articolo, un pannello mobile sopra altre finestre e fornisce gli strumenti o i controlli che gli utenti possono lavorare con mentre sono aperti documenti. 

Analogamente a qualsiasi altro tipo di finestra è creare e utilizzare Xamarin.Mac nell'applicazione in uso, il processo è fondamentalmente lo stesso:

1. Aggiungere una nuova definizione di finestra per il progetto.
2. Fare doppio clic su di `.xib` file da aprire la progettazione della finestra per la modifica in Generatore del Xcode di interfaccia.
2. Impostare le proprietà necessarie finestra **controllo attributo** e **dimensioni controllo**.
4. Trascinare i controlli necessari per compilare un'interfaccia e configurarli nel **controllo attributo**.
5. Utilizzare il **dimensioni controllo** per gestire il ridimensionamento di elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della finestra di codice c# tramite **prese** e **azioni**.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Nel **controllo attributo**, sono disponibili le opzioni seguenti specifiche ai pannelli:

[![](window-images/panel03.png "Il controllo di attributo")](window-images/panel03.png#lightbox)

- **Stile** -consente di modificare lo stile del pannello da: pannello normale (simile a una finestra standard), utilità pannello (include una barra del titolo più piccolo), pannello HUD (è semitrasparente e la barra del titolo fa parte dello sfondo).
- **Non attivare** -determina il pannello diventa la finestra di chiave.
- **Documento modale** -se documento modale, il pannello solo resterà sopra le finestre dell'applicazione, in caso contrario spostarla sopra tutti.


Per aggiungere un nuovo pannello, eseguire le operazioni seguenti:

1. Nel **Esplora**del mouse sul progetto e scegliere **Aggiungi** > **nuovo File...** .
2. Nella finestra di dialogo Nuovo File, selezionare **Xamarin.Mac** > **Cocoa finestra con Controller**:

    [![](window-images/panels00.png "Aggiunge un nuovo controller di finestra")](window-images/panels00.png#lightbox)
3. Immettere `DocumentPanel` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic su di `DocumentPanel.xib` file per aprirlo e modificarlo in Generatore di interfaccia: 

    [![](window-images/new02.png "Modifica il pannel")](window-images/new02.png#lightbox)
5. Eliminare la finestra esistente e trascinare un pannello dal **controllo libreria** nell'il **Editor dell'interfaccia**: 

    [![](window-images/panels01.png "Eliminare la finestra esistente")](window-images/panels01.png#lightbox)
6. Collegare il pannello fino al **proprietario del File*-**finestra*- **presa**: 

    [![](window-images/panels02.png "Trascinamento in transito verso l'alto")](window-images/panels02.png#lightbox)
7. Passare il **controllo di identità** e impostare la classe del pannello `DocumentPanel`: 

    [![](window-images/panels03.png "L'impostazione di classe del pannello")](window-images/panels03.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.
7. Modificare il `DocumentPanel.cs` file e modificare la definizione di classe per le operazioni seguenti: 

    `public partial class DocumentPanel : NSPanel`
8. Salvare le modifiche apportate al file.

Modificare il `AppDelegate.cs` file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Se si esegue l'applicazione, verrà visualizzato il pannello:

[![](window-images/panels04.png "Il pannello in un'app in esecuzione")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Pannello di Windows sono state deprecate da Apple e deve essere sostituito con **interfacce di controllo**. Per un esempio completo di creazione di un **controllo** in un'app Xamarin.Mac, consultare il nostro [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) app di esempio.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di Windows e i pannelli in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato i diversi tipi e utilizza delle finestre e pannelli, come creare e gestire Windows e i pannelli in interfaccia generatore del Xcode e come utilizzare i pannelli e di Windows nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (esempio)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Uso dei menu](~/mac/user-interface/menu.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
