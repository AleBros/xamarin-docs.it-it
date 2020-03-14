---
title: Nozioni fondamentali su iOS designer
description: Questa guida presenta le Xamarin Designer per iOS. Viene illustrato come utilizzare la finestra di progettazione iOS per disporre visivamente i controlli, come accedere a tali controlli nel codice e come modificare le proprietà.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/31/2018
ms.openlocfilehash: 6b02a0f8476cf47ca6df279653095fe0845b36c9
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304702"
---
# <a name="ios-designer-basics"></a>Nozioni fondamentali su iOS designer

_Questa guida presenta le Xamarin Designer per iOS. Viene illustrato come utilizzare la finestra di progettazione iOS per disporre visivamente i controlli, come accedere a tali controlli nel codice e come modificare le proprietà._

Il Xamarin Designer per iOS è una finestra di progettazione dell'interfaccia visiva simile ai Interface Builder e Android Designer di Xcode. Alcune delle numerose funzionalità includono la perfetta integrazione con Visual Studio per Windows e Mac, la modifica del trascinamento della selezione, un'interfaccia per la configurazione dei gestori di eventi e la possibilità di eseguire il rendering di controlli personalizzati.

## <a name="requirements"></a>Requisiti

IOS designer è disponibile in Visual Studio per Mac e Visual Studio 2017 e versioni successive in Windows. In Visual Studio per Windows, iOS designer richiede una connessione a un host di compilazione Mac configurato correttamente, ma non è necessario che Xcode sia in esecuzione.

Questa guida presuppone una certa familiarità con i contenuti trattati nelle [guide Introduzione](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Funzionamento di iOS designer

Questa sezione descrive come la finestra di progettazione iOS semplifica la creazione di un'interfaccia utente e la connessione al codice.

IOS Designer consente agli sviluppatori di progettare visivamente l'interfaccia utente di un'applicazione. Come descritto nella Guida [introduttiva agli storyboard](~/ios/user-interface/storyboards/index.md) , uno storyboard descrive le schermate (controller di visualizzazione) che costituiscono un'app, gli elementi dell'interfaccia (visualizzazioni) posizionati su tali controller e il flusso di navigazione generale dell'app. 

Un controller di visualizzazione è costituito da due parti: una rappresentazione visiva in iOS designer C# e una classe associata:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Un controller di visualizzazione in iOS designer](introduction-images/1-storyboardwithviewcontroller-vsmac.png "Un controller di visualizzazione in iOS designer")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Codice per un controller di visualizzazione](introduction-images/2-viewcontrollercode-vsmac.png "Codice per un controller di visualizzazione")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Un controller di visualizzazione in iOS designer](introduction-images/1-storyboardwithviewcontroller-vs.png "Un controller di visualizzazione in iOS designer")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Codice per un controller di visualizzazione](introduction-images/2-viewcontrollercode-vs.png "Codice per un controller di visualizzazione")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Nello stato predefinito, un controller di visualizzazione non fornisce alcuna funzionalità; deve essere popolata con i controlli. Questi controlli vengono inseriti nella visualizzazione del controller di visualizzazione, l'area rettangolare che contiene tutto il contenuto della schermata. La maggior parte dei controller di visualizzazione contiene controlli comuni, come pulsanti, etichette e campi di testo, come illustrato nella schermata seguente, che mostra un controller di visualizzazione contenente un pulsante: 

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Un controller di visualizzazione contenente un pulsante](introduction-images/3-viewcontrollerwithbutton-vsmac.png "Un controller di visualizzazione contenente un pulsante")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Un controller di visualizzazione contenente un pulsante](introduction-images/3-viewcontrollerwithbutton-vs.png "Un controller di visualizzazione contenente un pulsante")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Alcuni controlli, ad esempio le etichette contenenti testo statico, possono essere aggiunti al controller di visualizzazione e lasciati da soli. Tuttavia, più spesso, i controlli devono essere personalizzati a livello di codice. Ad esempio, il pulsante aggiunto sopra dovrebbe eseguire un'operazione quando viene toccato, quindi è necessario aggiungere un gestore eventi nel codice.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per accedere e modificare il pulsante nel codice, è necessario che disponga di un identificatore univoco. Specificare un identificatore univoco selezionando il pulsante, aprendo il **riquadro delle proprietà**e impostando il campo **nome** su un valore come "submitButton":

[![Impostazione del nome di un pulsante nella riquadro delle proprietà](introduction-images/4-settingbuttonname-vsmac.png "Impostazione del nome di un pulsante nella riquadro delle proprietà")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per accedere e modificare il pulsante nel codice, è necessario che disponga di un identificatore univoco. Fornire un identificatore univoco selezionando il pulsante, aprendo la **finestra Proprietà**e impostando il campo **nome** su un valore come "submitButton":

[![Impostazione del nome di un pulsante nella finestra Proprietà](introduction-images/4-settingbuttonname-vs.png "Impostazione del nome di un pulsante nella finestra Proprietà")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Ora che il pulsante ha un nome, è possibile accedervi nel codice. Ma come funziona?

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Nel **riquadro della soluzione**, passando a **ViewController.cs** e facendo clic sull'indicatore di divulgazione si rivela che la definizione della classe `ViewController` del controller di visualizzazione si estende su due file, ognuno dei quali contiene una definizione di [classe parziale](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Nel **Esplora soluzioni**, passando a **ViewController.cs** e facendo clic sull'indicatore di divulgazione si rivela che la definizione della classe `ViewController` del controller di visualizzazione si estende su due file, ognuno dei quali contiene una definizione di [classe parziale](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** deve essere popolato con codice personalizzato correlato alla classe `ViewController`. In questo file la classe `ViewController` può rispondere a diversi metodi del ciclo di vita del controller di visualizzazione iOS, personalizzare l'interfaccia utente e rispondere all'input dell'utente, ad esempio i rubinetti dei pulsanti.

- **ViewController.designer.cs** è un file generato, creato dalla finestra di progettazione iOS per eseguire il mapping dell'interfaccia visivamente costruita al codice. Poiché le modifiche apportate a questo file verranno sovrascritte, non devono essere modificate. Le dichiarazioni di proprietà in questo file consentono al codice nella classe `ViewController` di accedere, per **nome**, ai controlli impostati nella finestra di progettazione di iOS. L'apertura di **ViewController.designer.cs** rivela il codice seguente:

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

La dichiarazione di proprietà `SubmitButton` connette l'intera classe `ViewController`, non solo il file **ViewController.designer.cs** , al pulsante definito nello storyboard. Poiché **ViewController.cs** definisce parte della classe `ViewController`, ha accesso a `SubmitButton`.

Lo screenshot seguente illustra che IntelliSense ora riconosce il riferimento `SubmitButton` in **ViewController.cs**:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![IntelliSense che riconosce il riferimento SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense che riconosce il riferimento SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IntelliSense che riconosce il riferimento SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense che riconosce il riferimento SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

Questa sezione ha illustrato come creare un pulsante in iOS designer e accedere a tale pulsante nel codice.

Il resto di questo documento fornisce un'ulteriore Panoramica di iOS designer.

## <a name="ios-designer-basics"></a>Nozioni fondamentali su iOS designer

In questa sezione vengono presentate le parti di iOS designer e viene fornita una panoramica delle relative funzionalità.

### <a name="launching-the-ios-designer"></a>Avvio di iOS designer

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

I progetti Novell. iOS creati con Visual Studio per Mac includono uno storyboard. Per visualizzare il contenuto di uno storyboard, fare doppio clic sul file. Storyboard nel **riquadro della soluzione**:

[![Uno storyboard aperto in iOS designer](introduction-images/7-storyboardopen-vsmac.png "Uno storyboard aperto in iOS designer")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

La maggior parte dei progetti Novell. iOS creati con Visual Studio include uno storyboard. Per visualizzare il contenuto di uno storyboard, fare doppio clic sul file. Storyboard nel **Esplora soluzioni**:

[![Uno storyboard aperto in iOS designer](introduction-images/7-storyboardopen-vs.png "Uno storyboard aperto in iOS designer")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>funzionalità di iOS designer

IOS designer è costituito da sei sezioni principali:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Sezioni di iOS designer](introduction-images/8-sixpartsofiosdesigner-vsmac.png "Sezioni di iOS designer")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Area di progettazione** : area di lavoro principale della finestra di progettazione iOS. Mostrata nell'area del documento, consente la costruzione visiva di interfacce utente.
2. **Barra degli strumenti vincoli** : consente di passare dalla modalità di modifica del frame alla modalità di modifica del vincolo e viceversa, due modi diversi per posizionare gli elementi in un'interfaccia utente.
3. **Casella degli strumenti** : elenca i controller, gli oggetti, i controlli, le visualizzazioni dati, i riconoscitori di movimento, le finestre e le barre che possono essere trascinati nell'area di progettazione e aggiunti a un'interfaccia utente.
4. **Riquadro delle proprietà** : Mostra le proprietà per il controllo selezionato, inclusi l'identità, gli stili di visualizzazione, l'accessibilità, il layout e il comportamento.
5. **Struttura documento** : Mostra l'albero dei controlli che compongono il layout per l'interfaccia modificata. Quando si fa clic su un elemento nell'albero, questo viene selezionato nella finestra di progettazione iOS e ne vengono visualizzate le proprietà nella **riquadro delle proprietà**. Questa operazione è utile per la selezione di un controllo specifico in un'interfaccia utente profondamente nidificata.
6. **Barra degli strumenti inferiore** : contiene le opzioni per modificare il modo in cui iOS designer Visualizza il file con estensione storyboard o XIB, inclusi il dispositivo, l'orientamento e lo zoom.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Sezioni di iOS designer](introduction-images/8-sixpartsofiosdesigner-vs.png "Sezioni di iOS designer")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Area di progettazione** : area di lavoro principale della finestra di progettazione iOS. Mostrata nell'area del documento, consente la costruzione visiva di interfacce utente.
2. **Barra degli strumenti vincoli** : consente di passare dalla modalità di modifica del frame alla modalità di modifica del vincolo e viceversa, due modi diversi per posizionare gli elementi in un'interfaccia utente.
3. **Casella degli strumenti** : elenca i controller, gli oggetti, i controlli, le visualizzazioni dati, i riconoscitori di movimento, le finestre e le barre che possono essere trascinati nell'area di progettazione e aggiunti a un'interfaccia utente.
4. **Finestra Proprietà** : Mostra le proprietà per il controllo selezionato, tra cui identità, stili visivi, accessibilità, layout e comportamento.
5. **Struttura documento** : Mostra l'albero dei controlli che compongono il layout per l'interfaccia modificata. Quando si fa clic su un elemento nell'albero, questo viene selezionato nella finestra di progettazione iOS e ne vengono visualizzate le proprietà nella **finestra Proprietà**. Questa operazione è utile per la selezione di un controllo specifico in un'interfaccia utente profondamente nidificata.
6. **Barra degli strumenti inferiore** : contiene le opzioni per modificare il modo in cui iOS designer Visualizza il file con estensione storyboard o XIB, inclusi il dispositivo, l'orientamento e lo zoom.

-----

### <a name="design-workflow"></a>Flusso di lavoro di progettazione

#### <a name="adding-a-control-to-the-interface"></a>Aggiunta di un controllo all'interfaccia

Per aggiungere un controllo a un'interfaccia, trascinarlo dalla **casella degli strumenti** e rilasciarlo nell'area di progettazione. Quando si aggiunge o si posiziona un controllo, le linee guida verticali e orizzontali evidenziano le posizioni di layout usate di frequente, ad esempio il centro verticale, il centro orizzontale e i margini:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![Nell'area di progettazione le linee guida evidenziano le posizioni di layout usate di frequente](introduction-images/9-layoutguides-vsmac.png "Nell'area di progettazione le linee guida evidenziano le posizioni di layout usate di frequente")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Nell'area di progettazione le linee guida evidenziano le posizioni di layout usate di frequente](introduction-images/9-layoutguides-vs.png "Nell'area di progettazione le linee guida evidenziano le posizioni di layout usate di frequente")

-----

La linea blu punteggiata nell'esempio precedente fornisce una linea guida per l'allineamento orizzontale del centro per facilitare la posizione dei pulsanti.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Comandi del menu di scelta rapida

È disponibile un menu di scelta rapida nell'area di progettazione e nella **struttura del documento**. Questo menu include i comandi per il controllo selezionato e il relativo elemento padre, che risulta utile quando si utilizzano le viste in una gerarchia nidificata:

[![Menu di scelta rapida nell'area di progettazione](introduction-images/10-contextmenudesignsurface-vsmac.png "Menu di scelta rapida nell'area di progettazione")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Barra degli strumenti vincoli

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti dei conformatori](introduction-images/11-constraintstoolbar-vsmac.png "Barra degli strumenti vincoli")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti dei conformatori](introduction-images/11-constraintstoolbar-vs.png "Barra degli strumenti vincoli")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

La barra degli strumenti dei vincoli è stata aggiornata e ora è costituita da due controlli: la modalità di modifica del frame o la modalità di modifica dei vincoli e il pulsante Aggiorna vincoli/Aggiorna frame.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modalità di modifica del frame/attivazione della modalità di modifica del vincolo

Nelle versioni precedenti di iOS designer, facendo clic su una visualizzazione già selezionata nell'area di progettazione, è stata attivata la modalità di modifica del frame e la modalità di modifica del vincolo. A questo punto, un controllo Attiva/Disattiva nella barra degli strumenti vincoli passa da una modalità di modifica all'altra.

- Modalità di modifica frame:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![Pulsante modalità di modifica frame](introduction-images/12a-frameeditingmode-vsmac.png "Pulsante modalità di modifica frame")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Pulsante modalità di modifica frame](introduction-images/12a-frameeditingmode-vs.png "Pulsante modalità di modifica frame")

-----

- Modalità di modifica vincoli:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![Pulsante modalità di modifica vincoli](introduction-images/12b-constrainteditingmode-vsmac.png "Pulsante modalità di modifica vincoli")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Pulsante modalità di modifica vincoli](introduction-images/12b-constrainteditingmode-vs.png "Pulsante modalità di modifica vincoli")

-----

#### <a name="update-constraints--update-frames-button"></a>Pulsante Aggiorna vincoli/Aggiorna frame

Il pulsante Aggiorna i vincoli/Aggiorna frame si trova a destra dell'interruttore della modalità di modifica del frame o della modalità di modifica del vincolo.

- In modalità di modifica frame, se si fa clic su questo pulsante, i frame di tutti gli elementi selezionati vengono modificati in base ai vincoli.
- In modalità di modifica vincoli, se si fa clic su questo pulsante, i vincoli di tutti gli elementi selezionati vengono modificati in base ai relativi frame.

### <a name="bottom-toolbar"></a>Barra degli strumenti inferiore

La barra degli strumenti inferiore consente di selezionare il dispositivo, l'orientamento e lo zoom usati per visualizzare uno storyboard o un file con estensione XIB in iOS designer:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti inferiore, utilizzata per selezionare un dispositivo e l'orientamento per l'area di progettazione](introduction-images/13-bottomtoolbar-vsmac.png "Barra degli strumenti inferiore, utilizzata per selezionare un dispositivo e l'orientamento per l'area di progettazione")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti inferiore, utilizzata per selezionare un dispositivo e l'orientamento per l'area di progettazione](introduction-images/13-bottomtoolbar-vs.png "Barra degli strumenti inferiore, utilizzata per selezionare un dispositivo e l'orientamento per l'area di progettazione")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo e orientamento

Se espanso, nella barra degli strumenti inferiore vengono visualizzati tutti i dispositivi, gli orientamenti e/o gli adattamenti applicabili al documento corrente. Fare clic su di essi per modificare la visualizzazione visualizzata nell'area di progettazione. 

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti inferiore, espansa per mostrare i dispositivi e gli orientamenti](introduction-images/14-bottomtoolbarexpanded-vsmac.png "Barra degli strumenti inferiore, espansa per mostrare i dispositivi e gli orientamenti")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti inferiore, espansa per mostrare i dispositivi e gli orientamenti](introduction-images/14-bottomtoolbarexpanded-vs.png "Barra degli strumenti inferiore, espansa per mostrare i dispositivi e gli orientamenti")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Si noti che la selezione di un dispositivo e l'orientamento cambiano solo come la finestra di progettazione iOS Visualizza l'anteprima della progettazione. Indipendentemente dalla selezione corrente, i vincoli appena aggiunti vengono applicati a tutti i dispositivi e gli orientamenti, a meno che non sia stato usato il pulsante **modifica tratti** per specificare diversamente.

Quando le [classi di dimensioni](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) sono [abilitate](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), il pulsante **modifica tratti** verrà visualizzato nella barra degli strumenti inferiore espansa.  Facendo clic sul pulsante **modifica tratti** vengono visualizzate le opzioni per la creazione di una variante dell'interfaccia in base alla classe di dimensioni rappresentata dal dispositivo selezionato e dall'orientamento. Si considerino gli esempi seguenti:

- Se l'opzione **iPhone** se / **Portrait**è selezionata, il metodo popopov fornirà le opzioni per creare una variazione dell'interfaccia per la classe Compact Width, regular Height size. 
- Se è selezionata l'opzione **iPad Pro 9,7 "**  / **Landscape** / **schermo intero** , il metodo popopov fornirà le opzioni per creare una variazione dell'interfaccia per la classe Regular Width, regular Height size.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti inferiore utilizzata per variare un'interfaccia in base alla classe di dimensioni](introduction-images/15-edittraitsbutton-vsmac.png "Barra degli strumenti inferiore utilizzata per variare un'interfaccia in base alla classe di dimensioni")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti inferiore utilizzata per variare un'interfaccia in base alla classe di dimensioni](introduction-images/15-edittraitsbutton-vs.png "Barra degli strumenti inferiore utilizzata per variare un'interfaccia in base alla classe di dimensioni")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controlli zoom

L'area di progettazione supporta lo zoom tramite diversi controlli:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![Controlli zoom nella barra degli strumenti inferiore](introduction-images/16-zoomcontrols-vsmac.png "Controlli zoom nella barra degli strumenti inferiore")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Controlli zoom nella barra degli strumenti inferiore](introduction-images/16-zoomcontrols-vs.png "Controlli zoom nella barra degli strumenti inferiore")

-----

I controlli includono quanto segue:

1. Adatta alla pagina
2. Eseguire lo zoom indietro
3. Eseguire lo zoom avanti
4. Dimensioni effettive (dimensioni di 1:1 pixel)

Questi controlli regolano lo zoom sull'area di progettazione. Non influiscono sull'interfaccia utente dell'applicazione in fase di esecuzione.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

### <a name="properties-pad"></a>riquadro delle proprietà

Usare il **riquadro delle proprietà** per modificare l'identità, gli stili visivi, l'accessibilità e il comportamento di un controllo. Lo screenshot seguente illustra le opzioni di **riquadro delle proprietà** per un pulsante:

[![riquadro delle proprietà per un pulsante](introduction-images/17-buttonpropertiespad-vsmac.png "riquadro delle proprietà per un pulsante")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Sezioni riquadro delle proprietà

Il **riquadro delle proprietà** contiene tre sezioni:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Finestra Proprietà

Utilizzare la **finestra Proprietà** per modificare l'identità, gli stili visivi, l'accessibilità e il comportamento di un controllo. Lo screenshot seguente illustra le opzioni della **finestra Proprietà** per un pulsante:

[![Finestra proprietà per un pulsante](introduction-images/17-buttonpropertieswindow-vs.png "Finestra proprietà per un pulsante")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Sezioni della finestra Proprietà

La **finestra Proprietà** contiene tre sezioni:

-----

1. **Widget** : proprietà principali del controllo, ad esempio nome, classe, proprietà di stile e così via. Le proprietà per la gestione del contenuto del controllo vengono in genere inserite qui.
2. **Layout** : le proprietà che tengono traccia della posizione e delle dimensioni del controllo, inclusi vincoli e frame, sono elencate qui.
3. **Eventi** : gli eventi e i gestori eventi sono specificati qui. Utile per la gestione di eventi quali tocco, tocco, trascinamento e così via. Gli eventi possono anche essere gestiti direttamente nel codice.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Modifica delle proprietà nell'riquadro delle proprietà

Oltre alla modifica visiva nell'area di progettazione, iOS Designer supporta la modifica delle proprietà nell' **riquadro delle proprietà**. Le proprietà disponibili cambiano in base al controllo selezionato, come illustrato negli screenshot seguenti:

[![Proprietà pulsante](introduction-images/18a-buttonpropertiespad-vsmac.png "Proprietà pulsante")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Visualizza Proprietà controller](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "Visualizza Proprietà controller")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Modifica delle proprietà nella finestra Proprietà

Oltre alla modifica visiva nell'area di progettazione, iOS Designer supporta la modifica delle proprietà nella **finestra Proprietà**. Le proprietà disponibili cambiano in base al controllo selezionato, come illustrato negli screenshot seguenti:

[![Proprietà pulsante](introduction-images/18a-buttonpropertieswindow-vs.png "Proprietà pulsante")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Visualizza Proprietà controller](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "Visualizza Proprietà controller")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> Nella sezione Identity del riquadro delle proprietà viene ora visualizzato un campo **modulo** . È necessario compilare questa sezione solo quando si interagiscono con le classi Swift. Usarlo per immettere un nome di modulo per le classi Swift, che sono spazi dei nomi.

#### <a name="default-values"></a>Valori predefiniti

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Molte proprietà della **riquadro delle proprietà** non visualizzano alcun valore o un valore predefinito. Tuttavia, il codice dell'applicazione può comunque modificare questi valori. Il **riquadro delle proprietà** non Mostra i valori impostati nel codice.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Molte proprietà nella **finestra Proprietà** non mostrano alcun valore o un valore predefinito. Tuttavia, il codice dell'applicazione può comunque modificare questi valori. La **finestra Proprietà** non Mostra i valori impostati nel codice.

-----

#### <a name="event-handlers"></a>Gestori eventi

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per specificare i gestori eventi personalizzati per vari eventi, usare la scheda **eventi** della **riquadro delle proprietà**. Nello screenshot seguente, ad esempio, un metodo `HandleClick` gestisce il tocco del pulsante **all'interno** dell'evento:

[![Riquadro delle proprietà, con un gestore eventi impostato per un pulsante](introduction-images/19-buttonpropertiespadevents-vsmac.png "Riquadro delle proprietà, con un gestore eventi impostato per un pulsante")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per specificare i gestori eventi personalizzati per vari eventi, utilizzare la scheda **eventi** della **finestra Proprietà**. Nello screenshot seguente, ad esempio, un metodo `HandleClick` gestisce il tocco del pulsante **all'interno** dell'evento:

[![Finestra proprietà con un gestore eventi impostato per un pulsante](introduction-images/19-buttonpropertieswindowevents-vs.png "Finestra proprietà con un gestore eventi impostato per un pulsante")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Una volta specificato un gestore eventi, è necessario aggiungere un metodo con lo stesso nome alla classe del controller di visualizzazione corrispondente. In caso contrario, si verificherà un'eccezione `unrecognized selector` quando viene toccato il pulsante:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Eccezione selettore non riconosciuta](introduction-images/20-unrecognizedselector-vsmac.png "Eccezione selettore non riconosciuta")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Si noti che dopo che nel **riquadro delle proprietà**è stato specificato un gestore eventi, iOS designer aprirà immediatamente il file di codice corrispondente e offrirà l'offerta per inserire la dichiarazione del metodo. 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Eccezione selettore non riconosciuta](introduction-images/20-unrecognizedselector-vs.png "Eccezione selettore non riconosciuta")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Per un esempio in cui vengono usati i gestori eventi personalizzati, vedere la [guida introduzione Hello, iOS](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>visualizzazione Struttura

IOS designer può anche visualizzare la gerarchia di controlli di un'interfaccia come una struttura. Il contorno è disponibile selezionando la scheda **struttura documento** , come illustrato di seguito:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Struttura del documento](introduction-images/21-buttonoutlineview-vsmac.png "Struttura del documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Struttura del documento](introduction-images/21-buttonoutlineview-vs.png "Struttura del documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

Il controllo selezionato nella visualizzazione struttura rimane sincronizzato con il controllo selezionato nell'area di progettazione.  Questa funzionalità è utile per la selezione di un elemento da una gerarchia di interfaccia profondamente nidificata.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Ripristinare Xcode

È possibile utilizzare iOS designer e Xcode Interface Builder in modo intercambiabile. Per aprire uno storyboard o un file con estensione XIB in Xcode Interface Builder, fare clic con il pulsante destro del mouse sul file e scegliere **Apri con > Xcode Interface Builder**, come illustrato nella schermata seguente:

[![Apertura di uno storyboard in Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Apertura di uno storyboard in Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Dopo aver apportato le modifiche in Xcode Interface Builder, salvare il file e tornare al Visual Studio per Mac. Le modifiche vengono sincronizzate con il progetto Novell. iOS.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>Ripristinare Xcode

È possibile usare iOS designer e Xcode Interface Builder in modo intercambiabile, tuttavia Xcode Interface Builder è disponibile solo su Mac. Per aprire un file storyboard o XIB in Xcode Interface Builder in un Mac, aprire la soluzione che contiene il progetto Novell. iOS in [Visual Studio per Mac](/visualstudio/mac/), fare clic con il pulsante destro del mouse sul file e selezionare **Apri con > Xcode Interface Builder**, come illustrato nella schermata seguente:

[![Apertura di uno storyboard in Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Apertura di uno storyboard in Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Dopo aver apportato le modifiche in Xcode Interface Builder, salvare il file e tornare al Visual Studio per Mac. Le modifiche vengono sincronizzate con il progetto Novell. iOS.

-----

## <a name="xib-support"></a>supporto di. xib

IOS Designer supporta la creazione, la modifica e la gestione dei file con estensione XIB. Si tratta di file XML che rappresentare una singole visualizzazioni personalizzate che possono essere aggiunte alla gerarchia di visualizzazione di un'applicazione. Un file con estensione XIB rappresenta in genere l'interfaccia per una singola vista o schermata di un'applicazione, mentre uno storyboard rappresenta molte schermate e le transizioni tra di esse.

Sono disponibili molte opinioni sulla soluzione, ovvero file con estensione XIB, storyboard o codice, che funzionano meglio per la creazione e la gestione di un'interfaccia utente. In realtà, non esiste una soluzione perfetta ed è sempre opportuno considerare lo strumento migliore per il processo. Ciò premesso, i file con estensione XIB sono in genere più potenti se usati per rappresentare una visualizzazione personalizzata necessaria in più posizioni in un'app, ad esempio una cella di visualizzazione tabella personalizzata. 

Altre informazioni sull'uso dei file con estensione XIB sono disponibili nelle seguenti ricette:

- [Uso del modello view. xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Creazione di un TableViewCell personalizzato usando un XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Creazione di una schermata di avvio usando un XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Per ulteriori informazioni sull'utilizzo degli storyboard, fare riferimento all' [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md).

Questa e altre guide correlate a iOS designer fanno riferimento all'uso degli storyboard come approccio standard per la creazione di interfacce utente, poiché la maggior parte dei modelli di progetto Novell. iOS forniscono uno storyboard per impostazione predefinita.

## <a name="summary"></a>Riepilogo

Questa guida fornisce un'introduzione a iOS designer, che descrive le funzionalità e delinea gli strumenti che offre per la progettazione di interfacce utente accattivanti.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [Procedura dettagliata per i controlli iOS designable](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Panoramica di Android Designer](~/android/user-interface/android-designer/index.md)
- [Classi e metodi parziali](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Approfondimento sul Xamarin Designer per iOS-evolve 2014 (video)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Uso di iOS designer per creare una schermata di avvio (video)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
