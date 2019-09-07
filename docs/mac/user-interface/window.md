---
title: Windows in Novell. Mac
description: Questo articolo illustra l'uso di finestre e pannelli in un'applicazione Novell. Mac. Viene descritta la creazione di finestre e pannelli in Xcode e Interface Builder, il caricamento da storyboard e file con estensione XIB e l'uso a livello di codice.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: df623efcc1da617ac6b700b42d3ac058dea817ca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772646"
---
# <a name="windows-in-xamarinmac"></a>Windows in Novell. Mac

_Questo articolo illustra l'uso di finestre e pannelli in un'applicazione Novell. Mac. Viene descritta la creazione di finestre e pannelli in Xcode e Interface Builder, il caricamento da storyboard e file con estensione XIB e l'uso a livello di codice._

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere alle stesse finestre e ai pannelli che uno sviluppatore utilizza in *Objective-C* e *Xcode* . Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire le finestre e i pannelli (oppure, facoltativamente, crearli direttamente nel C# codice).

A seconda del suo scopo, un'applicazione Novell. Mac può presentare una o più finestre sullo schermo per gestire e coordinare le informazioni che Visualizza e funziona con. Le funzioni principali di una finestra sono:

1. Per fornire un'area in cui le visualizzazioni e i controlli possono essere posizionati e gestiti.
2. Per accettare e rispondere agli eventi in risposta all'interazione dell'utente con la tastiera e il mouse.

È possibile utilizzare Windows in uno stato non modale, ad esempio un editor di testo che può disporre di più documenti aperti contemporaneamente, o modale, ad esempio una finestra di dialogo di esportazione che deve essere rilasciata prima che l'applicazione possa continuare.

I pannelli sono un tipo speciale di finestra (una sottoclasse della `NSWindow` classe base), che in genere funge da funzione ausiliaria in un'applicazione, ad esempio le finestre di utilità come gli ispettori del formato testo e la selezione colori di sistema.

[![](window-images/intro01.png "Modifica di una finestra in Xcode")](window-images/intro01.png#lightbox)

In questo articolo verranno illustrate le nozioni di base per l'utilizzo di Windows e dei pannelli in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` i comandi e `Export` usati per collegare le C# classi a. Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introduzione a Windows

Come indicato in precedenza, una finestra fornisce un'area in cui le visualizzazioni e i controlli possono essere inseriti e gestiti e rispondono agli eventi in base all'interazione dell'utente (tramite la tastiera o il mouse).

In base a Apple, esistono cinque tipi principali di Windows in un'app macOS:

- **Finestra del documento** : una finestra del documento contiene dati utente basati su file, ad esempio un foglio di calcolo o un documento di testo.
- **Finestra app** : una finestra dell'app è la finestra principale di un'applicazione non basata su documenti (ad esempio l'app Calendar in un Mac).
- **Panel** : un pannello è mobile sopra altre finestre e fornisce gli strumenti o i controlli che gli utenti possono usare durante l'apertura dei documenti. In alcuni casi, un pannello può essere traslucido, ad esempio quando si lavora con grafica di grandi dimensioni.
- **Finestra** di dialogo: viene visualizzata una finestra di dialogo in risposta a un'azione dell'utente che in genere consente agli utenti di completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima che possa essere chiusa. (Vedere [utilizzo delle finestre di dialogo](~/mac/user-interface/dialog.md))
- **Avvisi** : un avviso è un tipo speciale di finestra di dialogo che viene visualizzato quando si verifica un problema grave, ad esempio un errore, o come un avviso, ad esempio la preparazione dell'eliminazione di un file. Poiché un avviso è una finestra di dialogo, richiede anche una risposta utente prima che possa essere chiusa. (Vedere [uso degli avvisi](~/mac/user-interface/alert.md))

Per altre informazioni, vedere la sezione [About Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) di Apple

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Finestre principale, chiave e inattiva

Windows in un'applicazione Novell. Mac può apparire e comportarsi in modo diverso in base alla modalità di interazione dell'utente. Il primo documento o la finestra dell'app attualmente concentrata sull'attenzione dell'utente è detta _finestra principale_. Nella maggior parte dei casi questa finestra sarà anche la _finestra chiave_ , ovvero la finestra che sta attualmente accettando l'input dell'utente. Tuttavia, questo non è sempre il caso, ad esempio, un selettore di colori potrebbe essere aperto ed essere la finestra chiave con cui l'utente interagisce per modificare lo stato di un elemento nella finestra del documento (che sarebbe ancora la finestra principale).

Le finestre principale e chiave (se sono separate) sono sempre attive, le finestre _inattive_ sono finestre aperte che non sono in primo piano. Ad esempio, un'applicazione editor di testo potrebbe avere più di un documento aperto alla volta, ma solo la finestra principale sarebbe attiva, tutte le altre sarebbero inattive. 

Per altre informazioni, vedere la sezione [About Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) di Apple

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Denominazione di Windows

Una finestra può visualizzare una barra del titolo e, quando viene visualizzato il titolo, è in genere il nome dell'applicazione, il nome del documento su cui si sta lavorando o la funzione della finestra (ad esempio Inspector). Alcune applicazioni non visualizzano una barra del titolo perché sono riconoscibili e non funzionano con i documenti.

Apple suggerisce le linee guida seguenti:

- Usare il nome dell'applicazione per il titolo di una finestra principale, non di un documento. 
- Assegnare un nome a una `untitled`nuova finestra del documento. Per il primo nuovo documento, non aggiungere un numero al titolo (ad esempio `untitled 1`). Se l'utente crea un nuovo documento prima del salvataggio e la titolazione del primo, chiamare `untitled 2`tale `untitled 3`finestra, e così via.

Per altre informazioni, vedere la sezione [Naming Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) delle [linee guida per l'interfaccia umana di Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows a schermo intero

In macOS la finestra di un'applicazione può andare a schermo intero, inclusa la barra dei menu dell'applicazione, che può essere rivelata spostando il cursore nella parte superiore dello schermo, per fornire un'interazione libera di distrazione con il relativo contenuto.

Apple suggerisce le linee guida seguenti:

- Determinare se è opportuno che una finestra venga visualizzata a schermo intero. Le applicazioni che forniscono brevi interazioni (ad esempio un calcolatore) non devono fornire una modalità schermo intero.
- Mostra la barra degli strumenti se è richiesta dall'attività a schermo intero. In genere la barra degli strumenti è nascosta in modalità schermo intero.
- La finestra a schermo intero dovrebbe avere tutte le funzionalità necessarie agli utenti per completare l'attività.
- Se possibile, evitare l'interazione del Finder mentre l'utente si trova in una finestra a schermo intero.
- Sfrutta i vantaggi dello spazio dello schermo migliorato senza spostare lo stato attivo dall'attività principale.

Per altre informazioni, vedere la sezione [Windows a schermo intero](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) di Apple

<a name="Panels" />

### <a name="panels"></a>Pannelli

Un pannello è una finestra ausiliaria che contiene i controlli e le opzioni che influiscono sul documento o sulla selezione attiva (ad esempio, la selezione colori di sistema):

[![](window-images/panel01.png "Pannello colori")](window-images/panel01.png#lightbox)

I pannelli possono essere _specifici dell'app_ o _a livello_. I pannelli specifici dell'app si spostano nella parte superiore delle finestre del documento dell'applicazione e scompaiono quando l'applicazione è in background. I pannelli a livello, ad esempio il pannello dei **tipi di carattere** , sono fluttuati in tutte le finestre aperte, indipendentemente dall'applicazione. 

Apple suggerisce le linee guida seguenti:

- In generale, utilizzare un pannello standard, i pannelli trasparenti devono essere utilizzati solo sporadicamente e per le attività a elevato utilizzo di grafica.
- Si consiglia di usare un pannello per consentire agli utenti di accedere facilmente a controlli o informazioni importanti che influiscono direttamente sulle attività.
- Nascondere e mostrare i pannelli come richiesto.
- I pannelli devono sempre includere la barra del titolo.
- I pannelli non devono includere un pulsante di riduzione a icona attivo.

#### <a name="inspectors"></a>Controlli

La maggior parte delle applicazioni macOS moderne presenta controlli e opzioni ausiliari che interessano il documento o la selezione attiva come _ispettori_ che fanno parte della finestra principale (ad esempio l'app **pagine** mostrata sotto), anziché usare finestre del pannello:

[![](window-images/panel02.png "Controllo di esempio")](window-images/panel02.png#lightbox)

Per altre informazioni, vedere la sezione [pannelli](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) di Apple e l'app di esempio [MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector) per un'implementazione completa di un' **interfaccia di controllo** in un'app Novell. Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Creazione e gestione di Windows in Xcode

Quando si crea una nuova applicazione Novell. Mac Cocoa, per impostazione predefinita si ottiene una finestra vuota standard. Questa finestra è definita in un `.storyboard` file incluso automaticamente nel progetto. Per modificare la progettazione di Windows, nella **Esplora soluzioni**fare doppio clic sul `Main.storyboard` file:

[![](window-images/edit01.png "Selezione dello storyboard principale")](window-images/edit01.png#lightbox)

Verrà aperta la progettazione della finestra nella Interface Builder di Xcode:

[![](window-images/edit02.png "Modifica dell'interfaccia utente in Xcode")](window-images/edit02.png#lightbox)

In **controllo attributi**sono disponibili diverse proprietà che è possibile utilizzare per definire e controllare la finestra:

- **Title** : testo che verrà visualizzato nella barra di titolo della finestra.
- **Salvataggio** automatico: questa è la _chiave_ che verrà usata per l'identificazione della finestra quando la posizione e le impostazioni vengono salvate automaticamente.
- **Barra del titolo** : la finestra Visualizza una barra del titolo.
- **Barra degli strumenti e titolo unificato** : se la finestra include una barra degli strumenti, deve essere parte della barra del titolo.
- **Visualizzazione contenuto con dimensioni complete** : consente all'area del contenuto della finestra di trovarsi sotto la barra del titolo.
- **Shadow** : la finestra ha un'ombreggiatura.
- Le finestre con trama **con trama possono** usare effetti (ad esempio, vivacità) e possono essere spostate trascinando in un punto qualsiasi del corpo.
- **Close** : la finestra dispone di un pulsante Chiudi.
- **Riduci a icona** -la finestra dispone di un pulsante Riduci a icona.
- **Ridimensiona** : la finestra dispone di un controllo di ridimensionamento.
- **Pulsante della barra degli strumenti** : la finestra dispone di un pulsante della barra degli strumenti Nascondi/Mostra.
- **Restorable** -è la posizione e le impostazioni della finestra salvate e ripristinate automaticamente.
- **Visibile all'avvio** : è la finestra visualizzata automaticamente quando il `.xib` file viene caricato.
- **Nascondi su Disattiva** : è la finestra nascosta quando l'applicazione entra in background.
- **Rilascia alla chiusura** : la finestra viene rimossa dalla memoria quando viene chiusa.
- **Visualizza sempre le descrizioni comandi** : le descrizioni comandi vengono visualizzate costantemente.
- **Ricalcola il ciclo di visualizzazione** : è l'ordine di visualizzazione ricalcolato prima che la finestra venga disegnata.
- **Spazi**, **Exposé** e **Cycling** -tutti definiscono il comportamento della finestra in questi ambienti MacOS. 
- **Schermo intero** : determina se questa finestra può accedere alla modalità schermo intero. 
- **Animazione** : controlla il tipo di animazione disponibile per la finestra.
- **Aspetto** : controlla l'aspetto della finestra. Per il momento c'è un solo aspetto, Aqua.

Per altri dettagli, vedere [Introduzione a Apple per Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) e la documentazione su [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) .

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Impostazione delle dimensioni e della posizione predefinite

Per impostare la posizione iniziale della finestra e controllarne le dimensioni, passare al controllo **dimensioni**:

[![](window-images/edit07.png "Dimensioni e posizione predefinite")](window-images/edit07.png#lightbox)

Da qui è possibile impostare le dimensioni iniziali della finestra, assegnarle una dimensione minima e massima, impostare la posizione iniziale sullo schermo e controllare i bordi attorno alla finestra.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Impostazione di un controller della finestra principale personalizzato

Per poter creare Outlet e azioni per esporre gli elementi dell'interfaccia utente C# al codice, l'app Novell. Mac dovrà usare un controller di finestra personalizzato.

Seguire questa procedura:

1. Aprire lo storyboard dell'app nella Interface Builder di Xcode.
2. Selezionare la `NSWindowController` nella area di progettazione.
3. Passare alla visualizzazione **Identity Inspector** e immettere `WindowController` come nome della **classe**: 

    [![](window-images/windowcontroller01.png "Impostazione del nome della classe")](window-images/windowcontroller01.png#lightbox)
4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Un `WindowController.cs` file verrà aggiunto al progetto nella **Esplora soluzioni** Visual Studio per Mac: 

    [![](window-images/windowcontroller02.png "Selezione del controller Windows")](window-images/windowcontroller02.png#lightbox)
6. Riaprire lo storyboard nella Interface Builder di Xcode.
7. Il `WindowController.h` file sarà disponibile per l'uso: 

    [![](window-images/windowcontroller03.png "Modifica del file WindowController. h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Aggiunta di elementi dell'interfaccia utente

Per definire il contenuto di una finestra, trascinare i controlli da **libreria Inspector** nell' **editor di interfaccia**. Per altre informazioni sull'uso di Interface Builder per creare e abilitare i controlli, vedere l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentazione.

È ad esempio possibile trascinare una barra degli strumenti dal **controllo libreria** alla finestra dell' **editor di interfaccia**:

[![](window-images/edit03.png "Selezione di una barra degli strumenti dalla libreria")](window-images/edit03.png#lightbox)

Trascinare quindi in una **visualizzazione di testo** e ridimensionarlo per riempire l'area sotto la barra degli strumenti:

[![](window-images/edit04.png "Aggiunta di una visualizzazione di testo")](window-images/edit04.png#lightbox)

Poiché si desidera che la **visualizzazione di testo** venga compattata e cresca Man mano che le dimensioni della finestra cambiano, passare all'editor dei **vincoli** e aggiungere i vincoli seguenti:

[![](window-images/edit05.png "Modifica di vincoli")](window-images/edit05.png#lightbox)

Facendo clic sul pulsante per i **-Beam rossi** nella parte superiore dell'editor e scegliendo **Aggiungi 4 vincoli**, viene indicato alla visualizzazione di testo di attenersi alle coordinate X, Y e di aumentare o ridurre orizzontalmente e verticalmente quando la finestra viene ridimensionata.

Infine, si esporrà la **visualizzazione di testo** per il codice usando un' **uscita** (assicurandosi `ViewController.h` di selezionare il file):

[![](window-images/edit06.png "Configurazione di un Outlet")](window-images/edit06.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Per altre informazioni sull'uso di **Outlet** e **azioni**, vedere la documentazione di [Outlet e](~/mac/get-started/hello-mac.md#outlets-and-actions) azioni.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flusso di lavoro finestra standard

Per tutte le finestre create e usate nell'applicazione Novell. Mac, il processo è fondamentalmente uguale a quello ottenuto in precedenza:

1. Per le nuove finestre che non sono aggiunte automaticamente al progetto, aggiungere una nuova definizione di finestra al progetto. Questo argomento verrà illustrato in dettaglio di seguito.
1. Fare doppio clic sul `Main.storyboard` file per aprire la finestra progettazione per la modifica in Interface Builder di Xcode.
1. Trascinare una nuova finestra nella progettazione dell'interfaccia utente e associare la finestra alla finestra principale usando _gli elementi segue_ . per altre informazioni, vedere la sezione [gli elementi segue](~/mac/platform/storyboards/indepth.md#Segues) della documentazione relativa all' [utilizzo degli storyboard](~/mac/platform/storyboards/indepth.md) .
1. Impostare le proprietà della finestra obbligatorie in **controllo attributi** e il **controllo dimensioni**.
1. Trascinare i controlli necessari per compilare l'interfaccia e configurarli nel **controllo attributo**.
1. Usare il **controllo delle dimensioni** per gestire il ridimensionamento per gli elementi dell'interfaccia utente.
1. Esporre gli elementi dell'interfaccia utente della C# finestra al codice tramite **Outlet** e **azioni**.
1. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora che è stata creata una finestra di base, verranno esaminati i processi tipici usati da un'applicazione Novell. Mac quando si lavora con Windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Visualizzazione della finestra predefinita

Per impostazione predefinita, una nuova applicazione Novell. Mac visualizzerà automaticamente la finestra definita nel `MainWindow.xib` file quando viene avviata:

[![](window-images/display01.png "Una finestra di esempio in esecuzione")](window-images/display01.png#lightbox)

Poiché è stata modificata la progettazione di tale finestra, ora include una barra degli strumenti e un controllo di **visualizzazione di testo** predefiniti. La sezione `Info.plist` seguente del file è responsabile della visualizzazione di questa finestra:

[![](window-images/display00.png "Modifica di info. plist")](window-images/display00.png#lightbox)

L'elenco a discesa dell' **interfaccia principale** viene usato per selezionare lo storyboard che verrà usato come interfaccia utente principale dell'app ( `Main.storyboard`in questo caso).

Un controller di visualizzazione viene aggiunto automaticamente al progetto per controllare le finestre principali visualizzate, insieme alla relativa visualizzazione principale. Viene definito nel `ViewController.cs` file e collegato al **proprietario del file** in Interface Builder sotto il controllo di **identità**:

[![](window-images/display02.png "Impostazione del proprietario del file")](window-images/display02.png#lightbox)

Per la finestra, si vuole avere un titolo di `untitled` quando si apre per la prima volta, quindi si esegue l'override del `ViewWillAppear` metodo `ViewController.cs` in per ottenere un aspetto simile al seguente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> Si sta impostando il valore della `Title` proprietà della finestra `ViewWillAppear` nel metodo anziché il `ViewDidLoad` metodo perché, mentre la vista potrebbe essere caricata in memoria, non è ancora stata creata un'istanza completa di. Se si è tentato di accedere `Title` alla proprietà `ViewDidLoad` nel metodo, si otterrebbe un' `null` eccezione poiché la finestra non è stata ancora costruita e collegata alla proprietà.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Chiusura di una finestra a livello di codice

In alcuni casi potrebbe essere necessario chiudere a livello di codice una finestra in un'applicazione Novell. Mac, tranne che l'utente fa clic sul pulsante **Chiudi** della finestra o utilizzando una voce di menu. MacOS offre due modi diversi per chiudere un `NSWindow` oggetto a livello `PerformClose` di `Close`codice: e.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

La chiamata `PerformClose` al metodo di `NSWindow` un oggetto simula l'utente facendo clic sul pulsante **Chiudi** di una finestra, evidenziando brevemente il pulsante e quindi chiudendo la finestra.

Se l'applicazione implementa l' `NSWindow` `WillClose` evento, verrà generato prima della chiusura della finestra. Se l'evento restituisce `false`, la finestra non verrà chiusa. Se la finestra non dispone di un pulsante **Chiudi** o non può essere chiusa per un motivo qualsiasi, il sistema operativo emetterà il suono dell'avviso.

Ad esempio:

```csharp
MyWindow.PerformClose(this);
```

Tenta di chiudere l' `MyWindow` `NSWindow` istanza di. Se l'operazione ha esito positivo, la finestra verrà chiusa. in caso contrario, il segnale acustico verrà emesso e rimarrà aperto.

<a name="Close" />

### <a name="close"></a>Chiudi

La chiamata `Close` al metodo di `NSWindow` un non simula l'utente che fa clic sul pulsante **Chiudi** di una finestra, evidenziando brevemente il pulsante, chiude semplicemente la finestra.

Non è necessario che la finestra sia visibile per la chiusura e che `NSWindowWillCloseNotification` venga inviata una notifica nel centro notifiche predefinito per la chiusura della finestra.

Il `Close` metodo differisce da due modi importanti rispetto al `PerformClose` metodo:

1. Non tenta di generare l' `WillClose` evento.
2. Non simula l'utente facendo clic sul pulsante **Chiudi** per evidenziare brevemente il pulsante.

Ad esempio:

```csharp
MyWindow.Close();
```

Chiude l' `MyWindow` `NSWindow` istanza.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenuto di Windows modificato

In MacOS, Apple ha fornito un modo per informare l'utente che il contenuto di una finestra (`NSWindow`) è stato modificato dall'utente e deve essere salvato. Se la finestra contiene contenuto modificato, nel widget **Close** verrà visualizzato un piccolo punto nero:

[![](window-images/close01.png "Finestra con il marcatore modificato")](window-images/close01.png#lightbox)

Se l'utente tenta di chiudere la finestra o di uscire dall'app Mac mentre sono presenti modifiche non salvate al contenuto della finestra, è necessario presentare una finestra di [dialogo](~/mac/user-interface/dialog.md) o un [foglio modale](~/mac/user-interface/dialog.md) e consentire all'utente di salvare prima le modifiche:

[![](window-images/close02.png "Un foglio di salvataggio visualizzato quando la finestra è chiusa")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Contrassegno di una finestra modificata

Per contrassegnare una finestra come con contenuto modificato, usare il codice seguente:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

Dopo aver salvato la modifica, cancellare il flag modificato usando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Salvataggio delle modifiche prima della chiusura di una finestra

Per controllare che l'utente chiuda una finestra e consenta loro di salvare in anticipo il contenuto modificato, sarà necessario creare una sottoclasse di ed `WindowShouldClose` eseguire l'override del `NSWindowDelegate` relativo metodo. Ad esempio:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
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

                // Take action based on result
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

Usare il codice seguente per allegare un'istanza di questo delegato alla finestra:

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Salvataggio delle modifiche prima della chiusura dell'app

Infine, l'app Novell. Mac dovrebbe verificare se una delle finestre contiene contenuto modificato e consentire all'utente di salvare le modifiche prima di uscire. A tale scopo, modificare `AppDelegate.cs` il file, eseguire l'override del `ApplicationShouldTerminate` metodo e renderlo simile al seguente:

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

La maggior parte delle applicazioni Mac basate su documenti possono modificare più documenti nello stesso momento. Un editor di testo, ad esempio, può disporre di più file di testo aperti per la modifica nello stesso momento. Per impostazione predefinita, la nuova applicazione Novell. Mac dispone di un menu **file** con un **nuovo** elemento collegato automaticamente all' `newDocument:` **azione**.

Si attiverà questo nuovo elemento e si consente all'utente di aprire più copie della finestra principale per modificare più documenti in una sola volta.

Modificare `AppDelegate.cs` il file e aggiungere la proprietà calcolata seguente:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Questa operazione verrà usata per tenere traccia del numero di file non salvati, in modo che sia possibile inviare commenti e suggerimenti all'utente (per le linee guida di Apple descritte in precedenza).

Aggiungere quindi il metodo seguente:

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

Questo codice consente di creare una nuova versione del controller della finestra, caricare la nuova finestra, renderla la finestra principale e chiave e imposta titolo. A questo punto, se si esegue l'applicazione e si sceglie **nuovo** dal menu **file** , verrà aperta e visualizzata una nuova finestra dell'Editor:

[![](window-images/display04.png "È stata aggiunta una nuova finestra senza titolo")](window-images/display04.png#lightbox)

Se si apre il menu di **Windows** , è possibile osservare che l'applicazione tiene traccia e gestisce automaticamente le finestre aperte:

[![](window-images/display05.png "Menu di Windows")](window-images/display05.png#lightbox)

Per ulteriori informazioni sull'utilizzo di menu in un'applicazione Novell. Mac, vedere la documentazione relativa all' [utilizzo dei menu](~/mac/user-interface/menu.md) .

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Recupero della finestra attualmente attiva

In un'applicazione Novell. Mac in grado di aprire più finestre (documenti), in alcuni casi è necessario ottenere la finestra corrente in primo piano (la finestra della chiave). Il codice seguente restituirà la finestra del tasto:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Può essere chiamato in qualsiasi classe o metodo che deve accedere alla finestra della chiave corrente. Se nessuna finestra è attualmente aperta, verrà restituito `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Accesso a tutte le finestre delle app

In alcuni casi è possibile che sia necessario accedere a tutte le finestre attualmente aperte dall'app Novell. Mac. Ad esempio, per verificare se un file che l'utente vuole aprire è già aperto in una finestra di uscita.

`NSApplication.SharedApplication` Mantiene una`Windows` proprietà che contiene una matrice di tutte le finestre aperte nell'app. È possibile eseguire l'iterazione su questa matrice per accedere a tutte le finestre correnti dell'app. Ad esempio:

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

Nel codice di esempio viene eseguita il cast di ogni finestra restituita `ViewController` alla classe personalizzata nell'app e il test del valore di una `Path` proprietà personalizzata con il percorso di un file che l'utente vuole aprire. Se il file è già aperto, la finestra verrà visualizzata in primo piano.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Regolazione delle dimensioni della finestra nel codice

In alcuni casi l'applicazione deve ridimensionare una finestra nel codice. Per ridimensionare e riposizionare una finestra, modificare `Frame` la proprietà. Quando si modificano le dimensioni di una finestra, in genere è necessario modificare anche l'origine, in modo da tenere la finestra nella stessa posizione a causa del sistema di coordinate di macOS.

A differenza di iOS, in cui l'angolo superiore sinistro rappresenta (0,0), macOS usa un sistema di coordinate matematico in cui rappresenta l'angolo inferiore sinistro dello schermo (0,0). In iOS le coordinate aumentano quando ci si sposta verso il basso verso destra. In macOS le coordinate aumentano di valore verso l'alto verso destra. 

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
> Quando si modificano le dimensioni e la posizione di Windows nel codice, è necessario assicurarsi di rispettare le dimensioni minime e massime impostate in Interface Builder. Questa operazione non verrà rispettata automaticamente e sarà possibile rendere la finestra più grande o inferiore a questi limiti.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Monitoraggio delle modifiche alle dimensioni della finestra

In alcuni casi è necessario monitorare le modifiche apportate alle dimensioni di una finestra all'interno dell'app Novell. Mac. Ad esempio, per ricreare il contenuto per adattarlo alle nuove dimensioni.

Per monitorare le modifiche alle dimensioni, assicurarsi innanzitutto di avere assegnato una classe personalizzata per il controller di finestra nella Interface Builder di Xcode. Ad esempio, `MasterWindowController` nell'esempio seguente:

[![](window-images/resize01.png "Controllo di identità")](window-images/resize01.png#lightbox)

Modificare quindi la classe del controller della finestra personalizzata ed eseguire `DidResize` il monitoraggio dell'evento nella finestra del controller per ricevere una notifica delle modifiche apportate alle dimensioni Live. Ad esempio:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Facoltativamente, è possibile usare l' `DidEndLiveResize` evento per ricevere una notifica solo dopo che l'utente ha terminato la modifica delle dimensioni della finestra. Di seguito è riportato un esempio:

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

## <a name="setting-a-windows-title-and-represented-file"></a>Impostazione del titolo di una finestra e del file rappresentato

Quando si utilizzano finestre che rappresentano documenti, `NSWindow` dispone di `DocumentEdited` una proprietà che, se `true` impostata su, consente di visualizzare un piccolo punto nel pulsante Chiudi per fornire all'utente un'indicazione che indica che il file è stato modificato e deve essere salvato prima della chiusura.

Modificare `ViewController.cs` il file e apportare le modifiche seguenti:

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

Viene anche monitorato l' `WillClose` evento nella finestra e viene verificato lo stato `DocumentEdited` della proprietà. `true` Se è necessario fornire all'utente la possibilità di salvare le modifiche al file. Se si esegue l'app e si immette un testo, verrà visualizzato il punto:

[![](window-images/file01.png "Finestra modificata")](window-images/file01.png#lightbox)

Se si tenta di chiudere la finestra, viene generato un avviso:

[![](window-images/file02.png "Visualizzazione di una finestra di dialogo Salva")](window-images/file02.png#lightbox)

Se si sta caricando un documento da un file, è possibile impostare il titolo della finestra sul nome del file usando il `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` metodo (dato che `path` è una stringa che rappresenta il file aperto). Inoltre, è possibile impostare l'URL del file usando il `window.RepresentedUrl = url;` metodo.

Se l'URL fa riferimento a un tipo di file noto dal sistema operativo, la relativa icona verrà visualizzata nella barra del titolo. Se l'utente fa clic con il pulsante destro del mouse sull'icona, verrà visualizzato il percorso del file.

Modificare il `AppDelegate.cs` file e aggiungere il metodo seguente:

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

A questo punto, se si esegue l'app, selezionare **Apri..** . dal menu **file** , selezionare un file di testo dalla finestra di dialogo **Apri** e aprirlo:

[![](window-images/file03.png "Finestra di dialogo Apri")](window-images/file03.png#lightbox)

Il file verrà visualizzato e il titolo verrà impostato con l'icona del file:

[![](window-images/file04.png "Contenuto di un file caricato")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Aggiunta di una nuova finestra a un progetto

A parte la finestra del documento principale, un'applicazione Novell. Mac potrebbe dover visualizzare altri tipi di finestre per l'utente, ad esempio le preferenze o i pannelli di controllo.

Per aggiungere una nuova finestra, procedere come segue:

1. Nella **Esplora soluzioni**fare doppio clic sul `Main.storyboard` file per aprirlo per la modifica nella Interface Builder di Xcode.
2. Trascinare un nuovo **controller della finestra** dalla **libreria** e rilasciarlo nella **area di progettazione**:

    [![](window-images/new01.png "Selezione di un nuovo controller di finestra nella libreria")](window-images/new01.png#lightbox)
3. In **Identity Inspector**immettere `PreferencesWindow` per l' **ID storyboard**: 

    [![](window-images/new02.png "Impostazione dell'ID storyboard")](window-images/new02.png#lightbox)
4. Progettare l'interfaccia: 

    [![](window-images/new03.png "Progettazione dell'interfaccia utente")](window-images/new03.png#lightbox)
5. Aprire il menu dell'app`MacWindows`(), selezionare **Preferenze...** , fare clic su CTRL e trascinare nella nuova finestra: 

    [![](window-images/new05.png "Creazione di un segue")](window-images/new05.png#lightbox)
6. Scegliere **Mostra** dal menu popup.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se eseguiamo il codice e selezioni le **Preferenze...** dal **menu applicazione**, verrà visualizzata la finestra:

[![](window-images/new04.png "Menu di preferenze di esempio")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Utilizzo dei pannelli

Come indicato all'inizio di questo articolo, un pannello esegue il float sopra le altre finestre e fornisce gli strumenti o i controlli che gli utenti possono usare durante l'apertura dei documenti. 

Analogamente a qualsiasi altro tipo di finestra che si crea e si utilizza nell'applicazione Novell. Mac, il processo è fondamentalmente lo stesso:

1. Aggiungere una nuova definizione di finestra al progetto.
2. Fare doppio clic sul `.xib` file per aprire la finestra progettazione per la modifica in Interface Builder di Xcode.
3. Impostare le proprietà della finestra obbligatorie in **controllo attributi** e il **controllo dimensioni**.
4. Trascinare i controlli necessari per compilare l'interfaccia e configurarli nel **controllo attributo**.
5. Usare il **controllo delle dimensioni** per gestire il ridimensionamento per gli elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della C# finestra al codice tramite **Outlet** e **azioni**.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Nel **controllo attributi**sono disponibili le seguenti opzioni specifiche per i pannelli:

[![](window-images/panel03.png "Controllo attribute")](window-images/panel03.png#lightbox)

- **Stile** : consente di modificare lo stile del pannello da: Pannello normale (simile a una finestra standard), il pannello utilità (ha una barra del titolo inferiore), il pannello HUD (è traslucido e la barra del titolo fa parte dello sfondo).
- **Non attivato** : determina che nel pannello diventa la finestra principale.
- Modale **del documento:** se il documento è modale, il pannello verrà spostato sopra le finestre dell'applicazione; in caso contrario, viene eseguito il float.

Per aggiungere un nuovo pannello, procedere come segue:

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file.** ..
2. Nella finestra di dialogo nuovo file selezionare **Novell. Mac** > **Cocoa con controller**:

    [![](window-images/panels00.png "Aggiunta di un nuovo controller di finestra")](window-images/panels00.png#lightbox)
3. Immettere `DocumentPanel` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic sul `DocumentPanel.xib` file per aprirlo per la modifica in Interface Builder: 

    [![](window-images/new02.png "Modifica del pannello")](window-images/new02.png#lightbox)
5. Eliminare la finestra esistente e trascinare un pannello da **controllo libreria** nell'editor di **interfaccia**: 

    [![](window-images/panels01.png "Eliminazione della finestra esistente")](window-images/panels01.png#lightbox)
6. Collegare il pannello alla**presa**della**finestra** -  **proprietaria** - del file: 

    [![](window-images/panels02.png "Trascinamento per collegare il pannello")](window-images/panels02.png#lightbox)
7. Passare al **controllo di identità** e impostare la classe del pannello su `DocumentPanel`: 

    [![](window-images/panels03.png "Impostazione della classe del pannello")](window-images/panels03.png#lightbox)
8. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.
9. Modificare il `DocumentPanel.cs` file e modificare la definizione della classe nel modo seguente: 

    `public partial class DocumentPanel : NSPanel`
10. Salvare le modifiche apportate al file.

Modificare il `AppDelegate.cs` file e fare in `DidFinishLaunching` modo che il metodo sia simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Se si esegue l'applicazione, verrà visualizzato il pannello:

[![](window-images/panels04.png "Pannello in un'app in esecuzione")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Le finestre del pannello sono state deprecate da Apple e devono essere sostituite con le **interfacce del controllo**. Per un esempio completo di creazione di un **controllo** in un'app Novell. Mac, vedere l'app di esempio [MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso di finestre e pannelli in un'applicazione Novell. Mac. Abbiamo visto i diversi tipi e usi di Windows e dei pannelli, come creare e gestire finestre e pannelli nel Interface Builder di Xcode e come usare Windows e i pannelli nel C# codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [MacInspector (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Uso dei menu](~/mac/user-interface/menu.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
