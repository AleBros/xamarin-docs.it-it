---
title: Windows in xamarin. Mac
description: Questo articolo descrive l'uso delle finestre e pannelli in un'applicazione xamarin. Mac. Descrive la creazione di windows e pannelli in Xcode e Interface Builder, caricarli dai file con estensione xib e storyboard e utilizzarli a livello di codice.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: b60b8a6a7c56347d6abf71f8c5149ddd556d3da8
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251126"
---
# <a name="windows-in-xamarinmac"></a>Windows in xamarin. Mac

_Questo articolo descrive l'uso delle finestre e pannelli in un'applicazione xamarin. Mac. Descrive la creazione di windows e pannelli in Xcode e Interface Builder, caricarli dai file con estensione xib e storyboard e utilizzarli a livello di codice._

Quando si usa c# e .NET in un'applicazione xamarin. Mac, si ha accesso a Windows la stessa e i pannelli che uno sviluppatore che lavora in *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire i pannelli e Windows (oppure crearle direttamente nel codice c#).

Base il suo scopo, un'applicazione xamarin. Mac può presentare uno o più Windows nella schermata per la gestione e coordinano le informazioni viene visualizzata e funziona con. Le funzioni dell'entità di una finestra sono:

1. Per fornire un'area in cui le visualizzazioni e i controlli possono essere inseriti e gestiti.
2. Per accettare e rispondere agli eventi in risposta all'interazione dell'utente con la tastiera e mouse.

Windows può essere usato in uno stato non modale (ad esempio un editor di testo che è possibile avere più documenti aperti in una sola volta) o modale (ad esempio, una finestra di dialogo di esportazione che deve essere chiusa prima che l'applicazione può continuare).

I pannelli sono un tipo speciale di finestra (una sottoclasse della base `NSWindow` classe), che in genere hanno una funzione ausiliaria in un'applicazione, ad esempio le finestre di utilità, ad esempio i controlli di formato di testo e selezione dei colori di sistema.

[![](window-images/intro01.png "Modifica di una finestra in Xcode")](window-images/intro01.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di Windows e i pannelli in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introduzione a Windows

Come indicato in precedenza, una finestra è disponibile un'area in cui le visualizzazioni e i controlli possono essere inseriti e gestiti e risponde agli eventi basati sull'interazione dell'utente (sia tramite mouse o tastiera).

In base a Apple, esistono cinque tipi principali di Windows in un'App macOS:

- **Finestra di documento** -una finestra del documento contiene i dati basati su file utente, ad esempio un foglio di calcolo o un documento di testo.
- **Finestra dell'app** -una finestra dell'app è la finestra principale di un'applicazione non basata su documenti (ad esempio, l'app del calendario in un computer Mac).
- **Pannello** : un pannello viene spostata sopra altre finestre e fornisce gli strumenti o i controlli che gli utenti possono lavorare con i documenti sono aprire. In alcuni casi, può essere semitrasparente un pannello (ad esempio quando utilizzare elementi grafici di grandi dimensioni).
- **Finestra di dialogo** -una finestra di dialogo viene visualizzata in risposta a un'azione dell'utente e in genere fornisce agli utenti di modi possano completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima di chiuderla. (Vedere [uso di finestre di dialogo](~/mac/user-interface/dialog.md))
- **Gli avvisi** : un avviso è un tipo speciale di finestra di dialogo che viene visualizzata quando si verifica un problema grave (ad esempio un errore) o come un avviso (ad esempio, la preparazione dell'eliminazione di un file). Poiché un avviso è una finestra di dialogo, è necessario inoltre una risposta dell'utente prima di chiuderla. (Vedere [utilizzano gli avvisi](~/mac/user-interface/alert.md))

Per altre informazioni, vedere la [su Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main, la chiave e Windows inattivo

Windows in un'applicazione xamarin. Mac possono aspetto e comportamento in modo diverso basato sul modo in cui l'utente è attualmente sull'interazione con essi. Finestra di App che è attualmente punto focale dell'attenzione dell'utente o al documento principale viene chiamato il _la finestra principale_. Nella maggior parte dei casi questa finestra sarà anche il _chiave finestra_ (la finestra che viene attualmente accettato l'input dell'utente). Ma ciò non accade, ad esempio, una selezione colori potrebbe essere aperta e la finestra di chiave che l'utente interagisce con per modificare lo stato di un elemento nella finestra del documento (che sarebbe comunque la finestra principale).

I principali e la chiave di Windows (se vengono mantenuti separati) sono sempre attive, _inattivo Windows_ sono finestre aperte che non sono in primo piano. Ad esempio, un'applicazione di editor di testo è stato possibile avere più di un documento aperto in un momento, solo la finestra principale è attiva, tutti gli altri sarebbe inattivi. 

Per altre informazioni, vedere la [su Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Denominazione di Windows

Una finestra può visualizzare una barra del titolo e il titolo viene visualizzato, sono generalmente il nome dell'applicazione, il nome del documento sta lavorando o la funzione della finestra (ad esempio controllo). Alcune applicazioni non visualizzano una barra del titolo, perché sono riconoscibili da visuale e non funzionano con i documenti.

Apple consiglia le linee guida seguenti:

- Usare il nome dell'applicazione per il titolo di una finestra principale, non di documento. 
- Nome di una nuova finestra del documento `untitled`. Per il primo documento nuovo, non aggiungere un numero per il titolo (ad esempio `untitled 1`). Se l'utente crea un altro documento nuovo prima di salvare e di titolazione il primo, chiamare tale finestra `untitled 2`, `untitled 3`e così via.

Per altre informazioni, vedere la [Windows Naming](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows a schermo intero

In macOS, finestra di un'applicazione può passare occultamento schermo tutto incluso barra dei Menu dell'applicazione (che può essere rivelate spostando il cursore nella parte superiore della schermata) per fornire distrazione interazione gratuito con essa è contenuto.

Le linee guida seguenti suggerite da Apple:

- Determinare se è opportuna per una finestra di visualizzazione a schermo intero. Le applicazioni che forniscono le interazioni breve (ad esempio una calcolatrice) non devono fornire una modalità schermo intero.
- Mostra la barra degli strumenti se è necessario l'attività a schermo intero. In genere la barra degli strumenti viene nascosto in modalità schermo intero.
- La finestra a schermo dovrebbe avere tutti gli utenti le funzionalità necessarie completare l'attività.
- Se possibile, evitare l'interazione Finder mentre l'utente è in una finestra a schermo intero.
- Sfruttare i vantaggi di un aumento dello spazio dello schermo senza spostando l'attenzione dalle attività principali.

Per altre informazioni, vedere la [Windows a schermo intero](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Pannelli

Un pannello è una finestra ausiliaria che contiene i controlli e le opzioni che influiscono sulla selezione (ad esempio, il sistema di selezione colori) o il documento attivo:

[![](window-images/panel01.png "Un pannello colori")](window-images/panel01.png#lightbox)

Possono essere pannelli _specifici dell'App_ oppure _a livello di sistema_. I pannelli specifici dell'App mobile nella parte superiore delle finestre di documento dell'applicazione e scompare quando l'applicazione è in background. Pannelli a livello di sistema (ad esempio la **i tipi di carattere** pannello), float nella parte superiore di tutte le finestre aperte indipendentemente dall'applicazione. 

Le linee guida seguenti suggerite da Apple:

- In generale, usare un pannello standard, i pannelli trasparenti devono essere usati solo quando strettamente necessario e per le attività a grafica intensiva.
- È consigliabile usare un pannello per concedere agli utenti di accedere facilmente ai controlli importanti o informazioni che influisce direttamente sulla loro attività.
- Nascondere e mostrare i pannelli in base alle esigenze.
- Pannelli devono sempre includere sulla barra del titolo.
- I pannelli non devono includere un pulsante Riduci a icona attivo.

#### <a name="inspectors"></a>Controlli

Le applicazioni di macOS più moderne presentano ausiliari controlli e le opzioni che interessano il documento attivo o la selezione come _Inspectors_ che fanno parte della finestra principale (come le **pagine** app illustrato di seguito), invece di usare Windows pannello:

[![](window-images/panel02.png "Un controllo di esempio")](window-images/panel02.png#lightbox)

Per altre informazioni, vedere la [pannelli](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) e il [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) app di esempio per un'implementazione completa di un **Inspector interfaccia** in un'app xamarin. Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Creazione e gestione di Windows in Xcode

Quando si crea una nuova applicazione xamarin. Mac Cocoa, si ottiene una normale finestra vuota, per impostazione predefinita. Questo windows è definito in un `.storyboard` file automaticamente inclusi nel progetto. Per modificare la progettazione di windows, nelle **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file:

[![](window-images/edit01.png "Selezionando lo storyboard principale")](window-images/edit01.png#lightbox)

Verrà aperta la progettazione di finestra in Interface Builder di Xcode:

[![](window-images/edit02.png "Modifica l'interfaccia utente in Xcode")](window-images/edit02.png#lightbox)

Nel **Inspector attributo**, esistono diverse proprietà che è possibile usare per definire e controllare la finestra:

- **Titolo** -si tratta del testo che verrà visualizzato nella barra del titolo della finestra.
- **Salvataggio automatico** -questo è il _chiave_ che verrà usato per l'ID di finestra quando della posizione e le impostazioni vengono salvate automaticamente.
- **Sulla barra del titolo** -la finestra visualizzata una barra del titolo.
- **Titolo e sulla barra degli strumenti unificati** : se la finestra include una barra degli strumenti deve essere parte della barra del titolo.
- **Veduta dimensioni contenuto** -consente l'area del contenuto della finestra essere sotto la barra del titolo.
- **Shadow** -finestra di ha un'ombreggiatura.
- **Trama** -texture windows può utilizzare effetti (ad esempio vitalità) e possono essere spostati trascinando un punto qualsiasi nel corpo.
- **Chiudere** -la finestra dispone di un pulsante Chiudi.
- **Ridurre al minimo** -la finestra dispone di un pulsante Riduci a icona.
- **Ridimensionare** -la finestra dispone di un controllo di ridimensionamento.
- **Pulsante della barra degli strumenti** -la finestra dispone di un pulsante Mostra/Nascondi.
- **Che è possibile ripristinare** -posizione della finestra e impostazioni salvato e ripristinato automaticamente.
- **Visibile in avvio veloce** -è la finestra automaticamente visualizzata quando il `.xib` viene caricato il file.
- **Nascondere nel disattivare** -è la finestra nascosta quando l'applicazione passerà in background.
- **Versione chiuso quando** -è l'intervallo vengono eliminato dalla memoria al termine viene chiuso.
- **Sempre le descrizioni comando visualizzato** -sono le descrizioni comandi visualizzate costantemente.
- **Ricalcola il ciclo di vista** -viene ricalcolata prima che la finestra viene disegnata l'ordine di visualizzazione.
- **Gli spazi**, **atteggiamento** e **ciclo** -definiscono come la finestra si comporta in tali ambienti macOS. 
- **Schermo intero** -determina se questa finestra è possibile immettere la modalità schermo intero. 
- **Animazione** -controlla i tipi di animazione disponibile per la finestra.
- **Aspetto** -controlla l'aspetto della finestra. Per ora è disponibile un solo aspetto, azzurro.

Vedere di Apple [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) e [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) informazioni più dettagliate.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Impostazione delle dimensioni predefinite e posizione

Per impostare la posizione iniziale della finestra e per controllare la dimensione del, passare al **controllo dimensioni**:

[![](window-images/edit07.png "La dimensione predefinita e il percorso")](window-images/edit07.png#lightbox)

Da qui è possibile impostare le dimensioni iniziali della finestra, assegnarvi una dimensione minima e massima, impostare la posizione iniziale nella schermata e controllare i bordi intorno alla finestra.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>L'impostazione di un Controller di finestra personalizzati principale

Per poter creare Outlet e azioni per esporre gli elementi dell'interfaccia utente al codice c#, l'app xamarin. Mac dovrà essere utilizzando un Controller di finestra personalizzati.

Seguire questa procedura:

1. Aprire Storyboard dell'app in Interface Builder di Xcode.
2. Selezionare il `NSWindowController` nell'area di progettazione.
3. Passare al **Identity Inspector** consente di visualizzare e immettere `WindowController` come il **nome classe**: 

    [![](window-images/windowcontroller01.png "Impostazione del nome di classe")](window-images/windowcontroller01.png#lightbox)
4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Oggetto `WindowController.cs` file verrà aggiunto al progetto nel **Esplora soluzioni** in Visual Studio per Mac: 

    [![](window-images/windowcontroller02.png "Quando si seleziona il controller di windows")](window-images/windowcontroller02.png#lightbox)
6. Riaprire lo Storyboard di Interface Builder di Xcode.
7. Il `WindowController.h` file sarà disponibile per l'uso: 

    [![](window-images/windowcontroller03.png "Modifica del file WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Aggiunta di elementi dell'interfaccia utente

Per definire il contenuto di una finestra, trascinare controlli dal **Library Inspector** nel **Interface Editor**. Vedere la [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) per altre informazioni sull'uso di Interface Builder per creare e abilitare i controlli.

Ad esempio, è possibile trascinare una barra degli strumenti dal **Library Inspector** sulla finestra di **Interface Editor**:

[![](window-images/edit03.png "Selezione di una barra degli strumenti dalla raccolta")](window-images/edit03.png#lightbox)

Trascinare quindi un **TextView** e dimensionare in modo da riempire l'area sotto la barra degli strumenti:

[![](window-images/edit04.png "Aggiunta di una visualizzazione di testo")](window-images/edit04.png#lightbox)

Poiché si desidera che il **TextView** per riduzione e aumento delle dimensioni come le modifiche di dimensione della finestra, si passa al **Editor vincoli di** e aggiungere i vincoli seguenti:

[![](window-images/edit05.png "Modifica di vincoli")](window-images/edit05.png#lightbox)

Facendo clic la per **ai rossi** nella parte superiore dell'editor e facendo clic su **Aggiungi 4 vincoli**, si definiranno la visualizzazione di testo per adattarsi a determinato X, Y coordinate e aumentare o ridurre in senso orizzontale e verticale come la finestra viene ridimensionata.

Infine, è possibile esporre il **visualizzazione di testo** al codice che usa un' **Outlet** (assicurandosi di selezionare il `ViewController.h` file):

[![](window-images/edit06.png "Configurazione di un Outlet")](window-images/edit06.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Per altre informazioni sull'uso di **Outlet** e **azioni**, vedere il [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) documentazione.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flusso di lavoro di finestra standard

Per qualsiasi finestra che creano e si lavora in un'applicazione xamarin. Mac, il processo è fondamentalmente uguale quanto è stato appena fatto in precedenza:

1. Per la nuova finestra in cui non è il valore predefinito aggiunto automaticamente al progetto, aggiungere una nuova definizione di finestra per il progetto. Questo argomento verrà discusso in dettaglio di seguito.
2. Fare doppio clic il `Main.storyboard` file da aprire la progettazione della finestra per la modifica in Interface Builder di Xcode.
3. Trascinare una nuova finestra di progettazione dell'interfaccia utente e la finestra di hook in usando la finestra principale _segue_ (per altre informazioni vedere la [segue](~/mac/platform/storyboards/indepth.md#Segues) sezione del nostro [utilizzo degli storyboard](~/mac/platform/storyboards/indepth.md) documentazione).
3. Impostare qualsiasi proprietà della finestra richiesto nel **attributo Inspector** e il **Size Inspector**.
4. Trascinare i controlli necessari per compilare l'interfaccia e configurarle nel **Inspector attributo**.
5. Usare la **controllo dimensioni** per gestire il ridimensionamento di elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della finestra al codice c# tramite **Outlet** e **azioni**.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora che abbiamo una finestra di base creata, esamineremo i processi tipiche un xamarin. Mac applicazione esegue quando si lavora con windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Visualizzazione della finestra predefinita

Per impostazione predefinita, una nuova applicazione xamarin. Mac visualizzerà automaticamente la finestra definita nel `MainWindow.xib` file quando viene avviato:

[![](window-images/display01.png "Una finestra di esempio in esecuzione")](window-images/display01.png#lightbox)

Poiché viene modificata la progettazione di tale finestra precedente, ora include un valore predefinito della barra degli strumenti e **TextView** controllo. La sezione seguente nel `Info.plist` file è responsabile della visualizzazione di questa finestra:

[![](window-images/display00.png "Modifica file Info. plist")](window-images/display00.png#lightbox)

Il **interfaccia principale** elenco a discesa è possibile selezionare lo Storyboard che verrà utilizzato come l'app principale dell'interfaccia utente (in questo caso `Main.storyboard`).

Un Controller di visualizzazione viene automaticamente aggiunto al progetto per controllare che Windows principale che viene visualizzato (insieme alla relativa visualizzazione primaria). In cui è definito il `ViewController.cs` segnalati e associati al **il proprietario del File** in Interface Builder nel **Identity Inspector**:

[![](window-images/display02.png "Impostazione del proprietario del file")](window-images/display02.png#lightbox)

Per la nostra finestra, ci piacerebbe che abbiano un titolo del `untitled` vengono innanzitutto aperti diamo override la `ViewWillAppear` metodo nella `ViewController.cs` simile al seguente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Viene impostato il valore della finestra `Title` proprietà nel `ViewWillAppear` invece del metodo di `ViewDidLoad` metodo perché, anche se la vista può essere caricata in memoria, ne non viene ancora completamente creata un'istanza. Se abbiamo tentato di accedere il `Title` proprietà nel `ViewDidLoad` metodo si otterrebbe un `null` eccezione poiché la finestra non è stata costruita e reti cablate-fino alla proprietà ancora.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Chiusura di una finestra a livello di codice

Potrebbero esserci volte in cui si desiderano a livello di programmazione chiudere una finestra in un'applicazione xamarin. Mac, quello di avere l'utente fare clic sulla finestra **chiudere** pulsante o tramite una voce di menu. macOS offre due modi diversi per chiudere un' `NSWindow` a livello di codice: `PerformClose` e `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Chiama il `PerformClose` metodo di un `NSWindow` simula il clic dell'utente della finestra **Chiudi** pulsante momentaneamente selezionando il pulsante e quindi chiudere la finestra.

Se l'applicazione implementa il `NSWindow`del `WillClose` eventi, verrà generato prima che la finestra viene chiusa. Se l'evento restituisce `false`, quindi la finestra non verrà chiuso. Se la finestra non dispone di un **Chiudi** pulsante o non può essere chiusa per qualsiasi motivo, il sistema operativo genererà il segnale acustico di avviso.

Ad esempio:

```csharp
MyWindow.PerformClose(this);
```

Tenterebbe di chiudere il `MyWindow` `NSWindow` istanza. Se eseguita correttamente, la finestra verrà chiusa, altrimenti il segnale acustico di avviso verrà generato e l'ha resterà aperta.

<a name="Close" />

### <a name="close"></a>Chiudi

Chiama il `Close` metodo di un `NSWindow` non simula il clic dell'utente della finestra **Chiudi** pulsante evidenziando momentaneamente il pulsante, dovrà semplicemente chiudere la finestra.

Non deve essere visibile da chiudere una finestra e un `NSWindowWillCloseNotification` notifica verrà registrata per il centro notifiche predefinite per la finestra viene chiusa.

Il `Close` metodo si differenzia da due aspetti importanti di `PerformClose` metodo:

1. Non provare a generare il `WillClose` evento.
2. Non viene simulata l'utente facendo clic sulle **Chiudi** pulsante, evidenziando momentaneamente il pulsante.

Ad esempio:

```csharp
MyWindow.Close();
```

Per chiudere la `MyWindow` `NSWindow` istanza.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenuto di Windows modificate

In macOS, Apple ha fornito un modo per informare l'utente che il contenuto di una finestra (`NSWindow`) è stato modificato dall'utente e deve essere salvato. Se la finestra contiene contenuto modificato, verrà visualizzato un piccolo punto nero nel relativo **Chiudi** widget:

[![](window-images/close01.png "Una finestra con il marcatore modificato")](window-images/close01.png#lightbox)

Se l'utente tenta di chiudere la finestra o uscire dall'App Mac mentre sono presenti modifiche non salvate le modifiche al contenuto della finestra, è necessario presentare un [finestra di dialogo](~/mac/user-interface/dialog.md) oppure [foglio modale](~/mac/user-interface/dialog.md) e consentire all'utente di salvare le modifiche prima:

[![](window-images/close02.png "Salvataggio foglio da visualizzare quando viene chiusa la finestra")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Contrassegno di una finestra come modificato

Per contrassegnare una finestra come con la modifica del contenuto, usare il codice seguente:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

E dopo aver salvata la modifica, deselezionare il flag modificato utilizzando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Salvataggio delle modifiche prima di chiudere una finestra

Per controllare la presenza all'utente di chiusura di una finestra e consentendo di salvare il contenuto modificato in anticipo, è necessario creare una sottoclasse di `NSWindowDelegate` ed eseguire l'override relativo `WindowShouldClose` (metodo). Ad esempio:

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

Usare il codice seguente per collegare un'istanza del delegato per la finestra:

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Salvataggio delle modifiche prima di chiudere l'App

Infine, l'App Xamarin.Mac deve verificare se uno qualsiasi dei relativi Windows contengono contenuto modificato e consentire all'utente di salvare le modifiche prima di uscire. A tale scopo, modificare il `AppDelegate.cs` file, eseguire l'override di `ApplicationShouldTerminate` (metodo) e renderlo simile al seguente:

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

## <a name="working-with-multiple-windows"></a>Utilizzo di Windows più

La maggior parte delle applicazioni per Mac documento basato modificabili documenti multipli nello stesso momento. Ad esempio, un editor di testo può avere più file di testo aperto per la modifica allo stesso tempo. Per impostazione predefinita, la nuova applicazione xamarin. Mac ha un **File** menu con un **New** elemento automaticamente reti cablate-fino al `newDocument:` **azione**.

Si intende attivare questo nuovo elemento e consentire all'utente di aprire più copie della nostra finestra principale per modificare più documenti in una sola volta.

È possibile modificare il `AppDelegate.cs` file e aggiungere le seguenti proprietà calcolata:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Useremo per tenere traccia del numero di file non salvati in modo da offrire commenti e suggerimenti all'utente (per linee guida di Apple come descritto in precedenza).

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

Questo codice crea una nuova versione del Controller finestra, carica la nuova finestra, rende i principali e finestra chiave e imposta il titolo. Ora, se si esegue l'applicazione e selezionare **New** dalle **File** menu verrà aperta e visualizzata una nuova finestra dell'editor:

[![](window-images/display04.png "È stata aggiunta una nuova finestra senza titolo")](window-images/display04.png#lightbox)

Se si apre la **Windows** menu, è possibile visualizzare l'applicazione viene automaticamente di rilevamento e gestisce la finestre aperte:

[![](window-images/display05.png "Il menu di Windows")](window-images/display05.png#lightbox)

Per altre informazioni sull'utilizzo dei menu in un'applicazione xamarin. Mac, vedere la [funziona con i menu](~/mac/user-interface/menu.md) documentazione.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Ottenere la finestra attualmente attiva

In un'applicazione xamarin. Mac in grado di aprire più finestre (documenti), vi sono casi quando è necessario ottenere la finestra corrente, in primo piano (la finestra di chiavi). Il codice seguente restituirà la chiave finestra:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Può essere chiamato in qualsiasi classe o metodo che deve accedere alla finestra corrente, key. Se nessuna finestra è attualmente aperta, verrà restituito `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>L'accesso a tutte le App Windows

Potrebbero esserci volte in cui è necessario accedere a tutte le finestre che l'app xamarin. Mac ha attualmente aperto. Ad esempio, se un file che l'utente sta tentando di aprire è già aperto in una finestra esistente.

Il `NSApplication.SharedApplication` mantiene un `Windows` proprietà che contiene una matrice di tutte le finestre aperte nell'app. È possibile scorrere la matrice per accedere a tutti di windows corrente dell'app. Ad esempio:

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

Nell'esempio di codice viene eseguito il cast di ogni finestra restituita alla personalizzata `ViewController` classe nella nostra app e la verifica del valore di una classe personalizzata `Path` proprietà rispetto al percorso di un file di cui l'utente vuole aprire. Se il file è già aperto, tale finestra viene portata in primo piano.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Regolare le dimensioni della finestra nel codice

Vi sono casi quando l'applicazione deve ridimensionare una finestra del codice. Per ridimensionare e riposizionare una finestra, è regolare del `Frame` proprietà. Per regolare la dimensione della finestra, è necessario in genere anche regolare relativa origine, per mantenere la finestra nella stessa località a causa di sistema di coordinate di macOS.

A differenza di iOS in cui l'angolo superiore sinistro rappresenta (0,0) macOS utilizza un sistema di coordinate matematici in cui l'angolo inferiore sinistro della schermata rappresenta (0,0). In iOS le coordinate aumentare quando si sposta verso il basso verso destra. In macOS, le coordinate aumentare nel valore verso l'alto a destra. 

Il codice di esempio seguente ridimensiona una finestra:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Quando si modifica una dimensione di windows e una posizione nel codice, è necessario assicurarsi che si rispettino le dimensioni minime e massime che è stato impostato in Interface Builder. Non si saranno valide automaticamente e sarà in grado di eseguire la finestra di dimensioni maggiori o minori rispetto a questi limiti.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Monitoraggio delle modifiche apportate alle dimensioni di finestra

Potrebbero esserci volte in cui è necessario monitorare le modifiche in dimensioni di una finestra all'interno di app xamarin. Mac. Ad esempio, per ridisegnare il contenuto in base alle nuove dimensioni.

Per monitorare le modifiche di dimensione, assicurarsi di avere assegnato una classe personalizzata per il Controller di finestra in Interface Builder di Xcode. Ad esempio, `MasterWindowController` nell'esempio seguente:

[![](window-images/resize01.png "Identity Inspector")](window-images/resize01.png#lightbox)

Successivamente, modificare la classe di Controller di finestra personalizzati e il monitor di `DidResize` evento nella finestra del Controller per ricevere una notifica di modifiche apportate alle dimensioni in tempo reale. Ad esempio:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Facoltativamente, è possibile usare il `DidEndLiveResize` eventi per ricevere notifiche solo dopo che l'utente ha terminato la modifica delle dimensioni della finestra. Di seguito è riportato un esempio:

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

## <a name="setting-a-windows-title-and-represented-file"></a>L'impostazione di una finestra del titolo e rappresentati File

Quando si lavora con windows che rappresentano i documenti `NSWindow` ha un `DocumentEdited` proprietà che, se impostata su `true` Visualizza un punto di piccole dimensioni il pulsante Chiudi per consentire all'utente un'indicazione che il file è stato modificato e deve essere salvato prima della chiusura.

È possibile modificare il `ViewController.cs` file e apportare le modifiche seguenti:

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

Fase di monitoraggio anche il `WillClose` evento nella finestra e verificare lo stato del `DocumentEdited` proprietà. Se si tratta di `true` dobbiamo assegnare all'utente la possibilità di salvare le modifiche apportate al file. Se si esegue l'App nell'app e immettere del testo, verrà visualizzato il punto:

[![](window-images/file01.png "Una finestra modificata")](window-images/file01.png#lightbox)

Se si tenta di chiudere la finestra, è visualizzato un avviso:

[![](window-images/file02.png "Visualizzazione di un salvataggio della finestra")](window-images/file02.png#lightbox)

Se si sta caricando un documento da un file è possibile impostare il titolo della finestra nel file nome utilizzando il `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` metodo (dato che `path` è una stringa che rappresenta il file in fase di apertura). Inoltre, è possibile impostare l'URL del file utilizzando il `window.RepresentedUrl = url;` (metodo).

Se l'URL fa riferimento a un tipo di file noto dal sistema operativo, verrà visualizzato relativa icona nella barra del titolo. Se l'utente con il pulsante destro fa clic sull'icona, verrà visualizzato il percorso del file.

È possibile modificare il `AppDelegate.cs` file e aggiungere il metodo seguente:

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

Se si esegue l'App nell'app, selezionare **aprire...**  dal **File** dal menu file di testo dal **aprire** dialogo casella e aprirlo:

[![](window-images/file03.png "La finestra di dialogo Apri")](window-images/file03.png#lightbox)

Verrà visualizzato il file e il titolo verrà impostato con l'icona del file:

[![](window-images/file04.png "Caricare il contenuto di un file")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Aggiunta di una nuova finestra in un progetto

A parte la finestra del documento principale, un'applicazione xamarin. Mac potrebbe essere necessario visualizzare altri tipi di windows per l'utente, ad esempio le preferenze o i pannelli di controllo.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder di Xcode.
2. Trascinare una nuova **Controller di finestra** dal **libreria** e rilasciarlo sul **nell'area di progettazione**:

    [![](window-images/new01.png "Selezione di un nuovo Controller di finestra nella libreria")](window-images/new01.png#lightbox)
3. Nel **Identity Inspector**, immettere `PreferencesWindow` per il **ID Storyboard**: 

    [![](window-images/new02.png "Impostazione dell'ID di storyboard")](window-images/new02.png#lightbox)
5. Progettare l'interfaccia: 

    [![](window-images/new03.png "Progettazione dell'interfaccia utente")](window-images/new03.png#lightbox)
6. Aprire il Menu dell'App (`MacWindows`), selezionare **preferenze...** , CTRL + clic e trascinare la nuova finestra: 

    [![](window-images/new05.png "Creazione di un elemento segue")](window-images/new05.png#lightbox)
7. Selezionare **mostrare** dal menu a comparsa.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue il codice e selezionare il **preferenze...**  dal **Menu applicazione**, verrà visualizzata la finestra:

[![](window-images/new04.png "Un menu Preferenze di esempio")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Utilizzo dei pannelli

Come affermato all'inizio di questo articolo, un pannello viene spostata sopra altre finestre e fornisce gli strumenti o i controlli che gli utenti possono lavorare con mentre sono aperti documenti. 

Proprio come qualsiasi altro tipo di finestra in cui si crea e usare l'applicazione xamarin. Mac, il processo è fondamentalmente lo stesso:

1. Aggiungere una nuova definizione di finestra per il progetto.
2. Fare doppio clic il `.xib` file da aprire la progettazione della finestra per la modifica in Interface Builder di Xcode.
2. Impostare qualsiasi proprietà della finestra richiesto nel **attributo Inspector** e il **Size Inspector**.
4. Trascinare i controlli necessari per compilare l'interfaccia e configurarle nel **Inspector attributo**.
5. Usare la **controllo dimensioni** per gestire il ridimensionamento di elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della finestra al codice c# tramite **Outlet** e **azioni**.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Nel **Inspector attributo**, sono disponibili le opzioni seguenti specifiche per i pannelli:

[![](window-images/panel03.png "Attribute Inspector")](window-images/panel03.png#lightbox)

- **Stile** -consentono di modificare lo stile del pannello da: pannello regolari (simile a una finestra standard), utilità Panel (include una barra del titolo più piccola), pannello HUD (è semitrasparente e fa parte dello sfondo della barra del titolo).
- **Attivazione in corso non** -determina nel Pannello di diventa la finestra di chiave.
- **Documentare modale** -se documento modale, il pannello solo resterà sopra le finestre dell'applicazione, altrimenti spostarla sopra tutti.


Per aggiungere un nuovo pannello, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sul progetto e selezionare **Add** > **nuovo File...** .
2. Nella finestra di dialogo Nuovo File, selezionare **xamarin. Mac** > **finestra Cocoa con Controller**:

    [![](window-images/panels00.png "Aggiunge un nuovo controller di finestra")](window-images/panels00.png#lightbox)
3. Immettere `DocumentPanel` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic il `DocumentPanel.xib` file per aprirlo e modificarlo in Interface Builder: 

    [![](window-images/new02.png "Modifica il pannel")](window-images/new02.png#lightbox)
5. Eliminare la finestra esistente e trascinare un pannello dal **Library Inspector** nell'il **Interface Editor**: 

    [![](window-images/panels01.png "L'eliminazione di finestra esistente")](window-images/panels01.png#lightbox)
6. Associare il pannello fino al **il proprietario del File*- **finestra*- **Outlet**: 

    [![](window-images/panels02.png "Trascinamento consente di associare il pannello")](window-images/panels02.png#lightbox)
7. Passare al **Identity Inspector** e impostare la classe del pannello `DocumentPanel`: 

    [![](window-images/panels03.png "Classe del Pannello di configurazione")](window-images/panels03.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.
7. Modificare il `DocumentPanel.cs` file e modificare la definizione di classe al seguente: 

    `public partial class DocumentPanel : NSPanel`
8. Salvare le modifiche apportate al file.

Modificare il `AppDelegate.cs` file e apportare le `DidFinishLaunching` metodo aspetto simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Se eseguiamo l'applicazione, verrà visualizzato il pannello:

[![](window-images/panels04.png "Il pannello in un'app in esecuzione")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Pannello di Windows sono state deprecate da Apple e deve essere sostituito con **interfacce di controllo**. Per un esempio completo di creazione di un' **Inspector** in un'app xamarin. Mac, vedere nostro [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) app di esempio.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di Windows e i pannelli in un'applicazione xamarin. Mac. Abbiamo visto i diversi tipi e gli utilizzi di Windows e i pannelli, come creare e gestire Windows e i pannelli in Interface Builder di Xcode e come utilizzare Windows e i pannelli in codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (esempio)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Uso dei menu](~/mac/user-interface/menu.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
