---
title: Nozioni fondamentali sulla progettazione di iOS
description: Questa guida illustra la finestra di progettazione di Xamarin per iOS. Viene illustrato come usare iOS Designer per visivamente il layout dei controlli, come accedere a tali controlli nel codice e come modificare le proprietà.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/31/2018
ms.openlocfilehash: e9c2a42b9108c04f18252a410d40dbc03013f6dd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123752"
---
# <a name="ios-designer-basics"></a>Nozioni fondamentali sulla progettazione di iOS

_Questa guida illustra la finestra di progettazione di Xamarin per iOS. Viene illustrato come usare iOS Designer per visivamente il layout dei controlli, come accedere a tali controlli nel codice e come modificare le proprietà._

La finestra di progettazione di Xamarin per iOS è una finestra di progettazione interfaccia visiva grafica, simile a Interface Builder di Xcode e Android Designer. Alcune delle numerose funzionalità includono integrazione perfetta con Visual Studio per Mac e Visual Studio 2015 e 2017, la modifica di trascinamento e rilascio, un'interfaccia per l'impostazione dei gestori eventi e la possibilità di eseguire il rendering di controlli personalizzati.

## <a name="requirements"></a>Requisiti

IOS Designer è disponibile in Visual Studio per Mac e in Visual Studio 2015 e 2017 in Windows. In Visual Studio 2015 o 2017, iOS Designer richiede una connessione a un host di compilazione Mac configurato correttamente, anche se è necessario Xcode non sia in esecuzione.

Questa guida presuppone una familiarità con il contenuto illustrato nel [Guida introduttiva descrive](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Funzionamento di iOS Designer

Questa sezione descrive come iOS Designer facilita la creazione di un'interfaccia utente e la connessione al codice.

IOS Designer consente agli sviluppatori di progettare visivamente un'interfaccia utente. Come descritto nel [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md) Guida storyboard descrive gli schermi (controller di visualizzazione) che costituiscono un'app, gli elementi dell'interfaccia (visualizzazioni) inseriti in tali controller di visualizzazione e flusso di spostamento globale dell'app . 

Un controller di visualizzazione è costituito da due parti: una rappresentazione visiva in iOS Designer e una classe c# associata:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Un controller di visualizzazione in iOS Designer](introduction-images/1-storyboardwithviewcontroller-vsmac.png "un controller di visualizzazione in iOS Designer")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Il codice per un controller di visualizzazione](introduction-images/2-viewcontrollercode-vsmac.png "il codice per un controller di visualizzazione")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Un controller di visualizzazione in iOS Designer](introduction-images/1-storyboardwithviewcontroller-vs.png "un controller di visualizzazione in iOS Designer")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Il codice per un controller di visualizzazione](introduction-images/2-viewcontrollercode-vs.png "il codice per un controller di visualizzazione")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Nello stato predefinito, un controller di visualizzazione non fornisce alcuna funzionalità; si deve essere compilato con i controlli. Questi controlli vengono posizionati nella vista del controller di visualizzazione, l'area rettangolare che include tutto il contenuto della schermata. La maggior parte dei controller di visualizzazione contengono controlli comuni, ad esempio pulsanti, etichette e i campi di testo, come illustrato nello screenshot seguente, che mostra un controller di visualizzazione che contiene un pulsante: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Un controller di visualizzazione che contiene un pulsante](introduction-images/3-viewcontrollerwithbutton-vsmac.png "un controller di visualizzazione che contiene un pulsante")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Un controller di visualizzazione che contiene un pulsante](introduction-images/3-viewcontrollerwithbutton-vs.png "un controller di visualizzazione che contiene un pulsante")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Alcuni controlli, ad esempio le etichette che contiene il testo statico, possono essere aggiunti al controller di visualizzazione e verrà lasciate invariata. Tuttavia, molto spesso, i controlli devono essere personalizzati a livello di codice. Ad esempio, il pulsante aggiunto in precedenza deve eseguire un'operazione quando si tocca, pertanto un gestore eventi deve essere aggiunta nel codice.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per accedere e modificare il pulsante nel codice, è necessario avere un identificatore univoco. Fornisce un identificatore univoco, selezionando il pulsante, aprire il **riquadro delle proprietà**e l'impostazione relativa **nome** campo su un valore, ad esempio "Pulsanteinvia":

[![Impostazione del nome del pulsante nel riquadro delle proprietà](introduction-images/4-settingbuttonname-vsmac.png "impostazione del nome del pulsante nel riquadro delle proprietà")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per accedere e modificare il pulsante nel codice, è necessario avere un identificatore univoco. Fornisce un identificatore univoco, selezionando il pulsante, aprire il **finestra delle proprietà**e l'impostazione relativa **nome** campo su un valore, ad esempio "Pulsanteinvia":

[![Impostazione del nome del pulsante nella finestra proprietà](introduction-images/4-settingbuttonname-vs.png "impostazione del nome del pulsante nella finestra proprietà")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Ora che il pulsante ha un nome, è possibile accedervi nel codice. Ma come funziona?

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Nel **riquadro della soluzione**, il passaggio a **ViewController.cs** e fare clic sull'indicatore della divulgazione rivela che il controller di visualizzazione `ViewController` intervalli di definizione di classe due file, ognuno dei quali contiene un [classe parziale](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definizione:

[![I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "i due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Nel **Esplora soluzioni**, il passaggio a **ViewController.cs** e fare clic sull'indicatore della divulgazione rivela che il controller di visualizzazione `ViewController` definizione di classe si estende su due file, ognuno dei che contiene un [classe parziale](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definizione:

[![I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "i due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** deve essere popolato con codice personalizzato correlato al `ViewController` classe. In questo file, il `ViewController` classe può rispondere ai dispositivi iOS di vari metodi del ciclo di vita del controller di visualizzazione, personalizzare l'interfaccia utente e rispondere all'input, ad esempio pulsante tocca dell'utente.

- **ViewController.designer.cs** è un file generato, creato da iOS Designer per eseguire il mapping dell'interfaccia costruito in modo visivo al codice. Poiché le modifiche apportate a questo file verranno sovrascritto, non deve essere modificato. Le dichiarazioni di proprietà in questo file rendono possibili per il codice nel `ViewController` classe per l'accesso, da **nome**, controlla i set di backup in iOS Designer. Apertura **ViewController.designer.cs** rivela il codice seguente:

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

Il `SubmitButton` dichiarazione di proprietà si connette l'intera `ViewController` classe - non solo il **ViewController.designer.cs** file – al pulsante definito nello storyboard. Poiché **ViewController.cs** definizione di parte del `ViewController` (classe), dispone dell'accesso a `SubmitButton`.

Lo screenshot seguente illustra che IntelliSense ora riconosce il `SubmitButton` fa riferimento nel **ViewController.cs**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![IntelliSense riconoscendo il riferimento Pulsanteinvia](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense riconoscendo il riferimento Pulsanteinvia")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IntelliSense riconoscendo il riferimento Pulsanteinvia](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense riconoscendo il riferimento Pulsanteinvia")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

In questa sezione ha illustrato come creare un pulsante in iOS Designer e accedere a tale pulsante nel codice.

Il resto di questo documento offre una panoramica ulteriore di iOS Designer.

## <a name="ios-designer-basics"></a>Nozioni fondamentali sulla progettazione di iOS

Questa sezione vengono illustrate le parti di iOS Designer e viene fornita una panoramica delle funzionalità.

### <a name="launching-the-ios-designer"></a>L'avvio di iOS Designer

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Progetti xamarin. IOS creati con Visual Studio per Mac include uno storyboard. Per visualizzare il contenuto di uno storyboard, fare doppio clic sul file storyboard nel **riquadro della soluzione**:

[![Apre uno storyboard in iOS Designer](introduction-images/7-storyboardopen-vsmac.png "apre uno storyboard in iOS Designer")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La maggior parte dei progetti xamarin. IOS creati con Visual Studio 2015 o 2017 includono uno storyboard. Per visualizzare il contenuto di uno storyboard, fare doppio clic sul file storyboard nel **Esplora soluzioni**:

[![Apre uno storyboard in iOS Designer](introduction-images/7-storyboardopen-vs.png "apre uno storyboard in iOS Designer")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>le funzionalità di progettazione di iOS

IOS Designer include sei sezioni principali:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Le sezioni di iOS Designer](introduction-images/8-sixpartsofiosdesigner-vsmac.png "sezioni di iOS Designer")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Area di progettazione** : area di lavoro principale della finestra di progettazione iOS. Nell'area del documento, in questo modo la costruzione visual di interfacce utente.
2. **I vincoli sulla barra degli strumenti** : consente il passaggio tra modalità di modifica e modalità di modifica di vincoli, due diversi modi per posizionare gli elementi in un'interfaccia utente di frame.
3. **Casella degli strumenti** : sono elencati controller, gli oggetti, controlli, le visualizzazioni di dati, i riconoscitori di movimento, windows e le barre che può essere trascinato nell'area di progettazione e aggiunta a un'interfaccia utente.
4. **Riquadro delle proprietà** : Mostra le proprietà per il controllo selezionato, tra cui identità, gli stili di visualizzazione, accessibilità, layout e comportamento.
5. **Struttura documento** : Mostra la struttura di controlli che compongono il layout per l'interfaccia in fase di modifica. Facendo clic su un elemento nell'albero viene selezionata in iOS Designer e Mostra le proprietà nel **riquadro delle proprietà**. Questo è utile per la selezione di un controllo specifico in un'interfaccia utente profondamente annidato.
6. **In basso sulla barra degli strumenti** – contiene le opzioni per modificare la modalità di visualizzazione del file con estensione xib o storyboard, incluso dispositivo, l'orientamento e zoom in iOS Designer.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Le sezioni di iOS Designer](introduction-images/8-sixpartsofiosdesigner-vs.png "sezioni di iOS Designer")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Area di progettazione** : area di lavoro principale della finestra di progettazione iOS. Nell'area del documento, in questo modo la costruzione visual di interfacce utente.
2. **I vincoli sulla barra degli strumenti** : consente il passaggio tra modalità di modifica e modalità di modifica di vincoli, due diversi modi per posizionare gli elementi in un'interfaccia utente di frame.
3. **Casella degli strumenti** : sono elencati controller, gli oggetti, controlli, le visualizzazioni di dati, i riconoscitori di movimento, windows e le barre che può essere trascinato nell'area di progettazione e aggiunta a un'interfaccia utente.
4. **Finestra delle proprietà** : Mostra le proprietà per il controllo selezionato, tra cui identità, gli stili di visualizzazione, accessibilità, layout e comportamento.
5. **Struttura documento** : Mostra la struttura di controlli che compongono il layout per l'interfaccia in fase di modifica. Facendo clic su un elemento nell'albero viene selezionata in iOS Designer e Mostra le proprietà nel **finestra proprietà**. Questo è utile per la selezione di un controllo specifico in un'interfaccia utente profondamente annidato.
6. **In basso sulla barra degli strumenti** – contiene le opzioni per modificare la modalità di visualizzazione del file con estensione xib o storyboard, incluso dispositivo, l'orientamento e zoom in iOS Designer.

-----

### <a name="design-workflow"></a>Progettazione del flusso di lavoro

#### <a name="adding-a-control-to-the-interface"></a>Aggiunta di un controllo all'interfaccia

Per aggiungere un controllo a un'interfaccia, trascinarlo dal **casella degli strumenti** e rilasciarlo nell'area di progettazione. Quando si aggiunge o posizionamento di un controllo, le linee guida verticali e orizzontali evidenziano le posizioni di layout di uso comune, ad esempio centro verticale, orizzontale center e i margini:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
 
![Nell'area di progettazione, linee guida per evidenziare le posizioni di layout utilizzate](introduction-images/9-layoutguides-vsmac.png "nell'area di progettazione, linee guida per evidenziare le posizioni di layout di uso comune")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Nell'area di progettazione, linee guida per evidenziare le posizioni di layout utilizzate](introduction-images/9-layoutguides-vs.png "nell'area di progettazione, linee guida per evidenziare le posizioni di layout di uso comune")

-----

La linea blu punteggiata nell'esempio precedente offre linee guida visual allineamento orizzontale center per facilitare il posizionamento del pulsante.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Comandi di menu di scelta rapida

È disponibile nell'area di progettazione sia in un menu di scelta rapida di **struttura documento**. Questo menu sono disponibili comandi per il controllo selezionato e l'elemento padre, ciò è utile quando si lavora con le visualizzazioni in una gerarchia annidata:

[![Menu di scelta rapida nell'area di progettazione](introduction-images/10-contextmenudesignsurface-vsmac.png "il menu di scelta rapida nell'area di progettazione")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Sulla barra degli strumenti di vincoli

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
 
[![Barra degli strumenti di vincoli](introduction-images/11-constraintstoolbar-vsmac.png "la barra degli strumenti di vincoli")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti di vincoli](introduction-images/11-constraintstoolbar-vs.png "la barra degli strumenti di vincoli")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

La barra degli strumenti di vincoli è stata aggiornata e ora è costituito da due controlli: modalità di modifica frame / vincolo modifica attiva/disattiva modalità e i vincoli di aggiornamento / frame pulsante Aggiorna.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modalità di modifica frame / attiva/disattiva modalità di modifica vincolo

Nelle versioni precedenti di iOS Designer, fare clic su una vista già selezionato nell'area di progettazione attivata/disattivata tra modalità di modifica e la modalità di modifica vincolo frame. A questo punto, un interruttore nella barra degli strumenti vincoli passa tra le modalità di modifica.

- Modalità di modifica frame:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Pulsante modalità di modifica frame](introduction-images/12a-frameeditingmode-vsmac.png "Frame pulsante modalità modifica")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Pulsante modalità di modifica frame](introduction-images/12a-frameeditingmode-vs.png "Frame pulsante modalità modifica")

-----

- Modalità di modifica di vincoli:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Pulsante modalità di modifica di vincoli](introduction-images/12b-constrainteditingmode-vsmac.png "pulsante modalità Modifica vincolo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Pulsante modalità di modifica di vincoli](introduction-images/12b-constrainteditingmode-vs.png "pulsante modalità Modifica vincolo")

-----

#### <a name="update-constraints--update-frames-button"></a>Aggiorna vincoli / frame pulsante Aggiorna

I vincoli di aggiornamento / aggiornamento posizionato sul pulsante a destra della pagina modalità di modifica di fotogrammi / attiva/disattiva modalità di modifica di vincoli.

- In modalità di modifica frame, facendo clic su questo pulsante consente di regolare i frame degli eventuali elementi selezionati in modo che corrisponda i vincoli.
- In modalità di modifica vincolo, facendo clic su questo pulsante consente di regolare i vincoli degli eventuali elementi selezionati in modo che corrisponda i frame.

### <a name="bottom-toolbar"></a>Barra degli strumenti inferiore

Barra degli strumenti inferiore consente di selezionare il dispositivo, l'orientamento e zoom consente di visualizzare un file con estensione xib o uno storyboard in iOS Designer:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti inferiore, utilizzato per selezionare un dispositivo e l'orientamento per l'area di progettazione](introduction-images/13-bottomtoolbar-vsmac.png "barra degli strumenti inferiore, utilizzato per selezionare un dispositivo e l'orientamento per l'area di progettazione")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti inferiore, utilizzato per selezionare un dispositivo e l'orientamento per l'area di progettazione](introduction-images/13-bottomtoolbar-vs.png "barra degli strumenti inferiore, utilizzato per selezionare un dispositivo e l'orientamento per l'area di progettazione")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo e l'orientamento

Se viene espansa, la barra degli strumenti inferiore vengono visualizzati tutti i dispositivi, gli orientamenti e/o gli adattamenti applicabili al documento corrente. Facendo clic su essi viene modificata la visualizzazione visualizzata nell'area di progettazione. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti inferiore, espande per visualizzare i dispositivi e gli orientamenti](introduction-images/14-bottomtoolbarexpanded-vsmac.png "barra degli strumenti inferiore, espanso per visualizzare i dispositivi e gli orientamenti")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti inferiore, espande per visualizzare i dispositivi e gli orientamenti](introduction-images/14-bottomtoolbarexpanded-vs.png "barra degli strumenti inferiore, espanso per visualizzare i dispositivi e gli orientamenti")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Si noti che se si seleziona un dispositivo e l'orientamento viene modificato solo come iOS Designer Visualizza l'anteprima di progettazione. Indipendentemente dalla selezione corrente, aggiunti di recente i vincoli vengono applicati in tutti i dispositivi e gli orientamenti, a meno che il **modifica tratti** pulsante è stato usato per specificare in caso contrario.

Quando [classi di dimensioni](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) vengono [abilitata](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), il **modifica tratti** pulsante verrà visualizzato nella barra degli strumenti inferiore espanso.  Facendo clic sui **modifica tratti** pulsante Visualizza le opzioni per la creazione di una variazione di interfaccia in base alla classe di dimensioni rappresentata dal dispositivo selezionato e l'orientamento. Considerare gli esempi seguenti:

- Se **iPhone sa** / **verticale**, è selezionata, popover verranno fornite opzioni per creare una variazione di interfaccia per la larghezza compact, classe di dimensioni di altezza regolari. 
- Se **iPad Pro 9,7"** / **panorama** / **schermo** è selezionata, popover verranno fornite opzioni per creare un'interfaccia variante per la larghezza regolare, una classe di dimensioni di altezza regolari.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Barra degli strumenti inferiore viene utilizzato per variare un'interfaccia dalla classe di dimensioni](introduction-images/15-edittraitsbutton-vsmac.png "barra degli strumenti inferiore viene utilizzato per variare un'interfaccia dalla classe di dimensioni")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra degli strumenti inferiore viene utilizzato per variare un'interfaccia dalla classe di dimensioni](introduction-images/15-edittraitsbutton-vs.png "barra degli strumenti inferiore viene utilizzato per variare un'interfaccia dalla classe di dimensioni")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controlli zoom

L'area di progettazione supporta lo zoom tramite numerosi controlli:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
 
![I controlli zoom nella barra degli strumenti inferiore](introduction-images/16-zoomcontrols-vsmac.png "i controlli zoom nella barra degli strumenti inferiore")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![I controlli zoom nella barra degli strumenti inferiore](introduction-images/16-zoomcontrols-vs.png "i controlli zoom nella barra degli strumenti inferiore")

-----

I controlli includono quanto segue:

1. Adatta alla finestra
2. Fare zoom indietro
3. Fare zoom avanti
4. Dimensioni effettive (le dimensioni in pixel 1:1)

Questi controlli regolano lo zoom nella finestra di progettazione. Non influiscono sull'interfaccia utente dell'applicazione in fase di esecuzione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

### <a name="properties-pad"></a>Riquadro delle proprietà

Usare la **riquadro delle proprietà** per modificare l'identità, gli stili di visualizzazione, accessibilità e comportamento di un controllo. Lo screenshot seguente illustra il **riquadro delle proprietà** opzioni per un pulsante:

[![Il riquadro delle proprietà per un pulsante](introduction-images/17-buttonpropertiespad-vsmac.png "il riquadro delle proprietà per un pulsante")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Sezioni di riquadro della proprietà

Il **riquadro delle proprietà** contiene tre sezioni:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Finestra Proprietà

Usare la **finestra proprietà** per modificare l'identità, gli stili di visualizzazione, accessibilità e comportamento di un controllo. Lo screenshot seguente illustra il **finestra proprietà** opzioni per un pulsante:

[![Finestra delle proprietà per un pulsante](introduction-images/17-buttonpropertieswindow-vs.png "la finestra proprietà per un pulsante")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Sezioni di finestra delle proprietà

Il **finestra proprietà** contiene tre sezioni:

-----

1.  **Widget** – proprietà principali del controllo, ad esempio nome, classe, proprietà di stile e così via. Le proprietà per la gestione del contenuto del controllo vengono in genere posizionate qui.
2.  **Layout** : proprietà di cui tenere traccia di posizione e dimensioni del controllo, inclusi i vincoli e i frame, sono elencate di seguito.
3.  **Gli eventi** : eventi e gestori eventi specificati di seguito. È utile per la gestione degli eventi, ad esempio il tocco, tocco, trascinamento e così via. Gli eventi possono anche essere gestiti direttamente nel codice.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Modifica delle proprietà nel riquadro delle proprietà

Oltre alla modifica visiva nella finestra di progettazione, iOS Designer supporta la modifica delle proprietà nel **riquadro delle proprietà**. La modifica di proprietà disponibili basata sul controllo selezionato, come illustrato schermate riportate di seguito:

[![Pulsante delle proprietà](introduction-images/18a-buttonpropertiespad-vsmac.png "sul pulsante delle proprietà")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Visualizzare le proprietà del controller](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "consente di visualizzare le proprietà del controller")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Modifica delle proprietà nella finestra proprietà

Oltre alla modifica visiva nella finestra di progettazione, iOS Designer supporta la modifica delle proprietà nel **finestra proprietà**. La modifica di proprietà disponibili basata sul controllo selezionato, come illustrato schermate riportate di seguito:

[![Pulsante delle proprietà](introduction-images/18a-buttonpropertieswindow-vs.png "sul pulsante delle proprietà")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Visualizzare le proprietà del controller](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "consente di visualizzare le proprietà del controller")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> Sezione identità del riquadro delle proprietà nell'ora viene illustrato un **modulo** campo. È necessario inserire in questa sezione solo quando si interagisce con le classi di Swift. Usarlo per immettere un nome di modulo per le classi Swift, ovvero con spazio dei nomi.

#### <a name="default-values"></a>Valori predefiniti

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Molte proprietà del **riquadro delle proprietà** mostrano alcun valore o un valore predefinito. Tuttavia, il codice dell'applicazione può ancora modificare questi valori. Il **riquadro delle proprietà** non mostra i valori impostati nel codice.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Molte proprietà del **finestra proprietà** mostrano alcun valore o un valore predefinito. Tuttavia, il codice dell'applicazione può ancora modificare questi valori. Il **finestra proprietà** non mostra i valori impostati nel codice.

-----

#### <a name="event-handlers"></a>Gestori eventi

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per specificare i gestori di eventi personalizzati per vari eventi, usare il **eventi** scheda della finestra di **riquadro delle proprietà**. Ad esempio, nella schermata seguente, un `HandleClick` gestisca il pulsante **Touch in** evento:

[![Il riquadro delle proprietà, con un gestore eventi per un pulsante impostata](introduction-images/19-buttonpropertiespadevents-vsmac.png "il riquadro delle proprietà, con un gestore eventi impostati per un pulsante")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per specificare i gestori di eventi personalizzati per vari eventi, usare il **eventi** scheda della finestra di **finestra proprietà**. Ad esempio, nella schermata seguente, un `HandleClick` gestisca il pulsante **Touch in** evento:

[![Finestra proprietà con un gestore eventi per un pulsante impostata](introduction-images/19-buttonpropertieswindowevents-vs.png "la finestra proprietà con un gestore eventi impostati per un pulsante")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Dopo che è stato specificato un gestore eventi, è necessario aggiungere un metodo lo stesso nome per la classe di controller di visualizzazione corrispondente. In caso contrario, un `unrecognized selector` eccezione si verifica quando viene toccato il pulsante:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Un'eccezione di selettore non riconosciuto](introduction-images/20-unrecognizedselector-vsmac.png "un'eccezione di selettore non riconosciuto")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Si noti che dopo un gestore eventi sono stato specificato nella **riquadro delle proprietà**, iOS Designer verrà immediatamente aprire il file di codice corrispondente e offrono inserire la dichiarazione del metodo. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Un'eccezione di selettore non riconosciuto](introduction-images/20-unrecognizedselector-vs.png "un'eccezione di selettore non riconosciuto")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Per un esempio che usa i gestori di eventi personalizzati, vedere la [Hello, iOS: Guida introduttiva](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Visualizzazione struttura

IOS Designer può anche visualizzare la gerarchia dell'interfaccia di controlli in una struttura. La struttura è disponibile selezionando il **struttura documento** scheda, come illustrato di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![La struttura del documento](introduction-images/21-buttonoutlineview-vsmac.png "la struttura del documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![La struttura del documento](introduction-images/21-buttonoutlineview-vs.png "la struttura del documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

Il controllo selezionato nella visualizzazione della struttura resta sincronizzato con il controllo selezionato nella finestra di progettazione.  Questa funzionalità è utile per la selezione di un elemento da una gerarchia di interfaccia eccessivamente annidato.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Tornare a Xcode

È possibile usare iOS Designer e Interface Builder di Xcode in modo intercambiabile. Per aprire un file con estensione xib o uno storyboard in Interface Builder di Xcode, fare clic sul file e selezionare **Apri con > Interface Builder Xcode**, come illustrato nello screenshot seguente:

[![Apertura di uno storyboard di Interface Builder Xcode](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "apertura di uno storyboard di Interface Builder Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Dopo aver apportato le modifiche in Interface Builder di Xcode, salvare il file e tornare a Visual Studio per Mac. Le modifiche verranno sincronizzate per il progetto xamarin. IOS.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

## <a name="xib-support"></a>supporto xib

IOS Designer supporta la creazione, modifica e gestione dei file con estensione xib. Questi sono file XML che rappresentare una singola personalizzato visualizzazioni che possono essere aggiunto alla gerarchia di visualizzazione di un'applicazione. Un file con estensione xib rappresenta in genere l'interfaccia per una singola visualizzazione o una schermata in un'applicazione, mentre uno storyboard rappresenta molte schermate e le transizioni tra di essi.

Esistono molti opinioni intorno al quale soluzione: file con estensione xib, storyboard o codice – risulta più adatta per la creazione e la gestione di un'interfaccia utente. In realtà, non c'è alcuna soluzione perfetta ed è sempre opportuno considerare lo strumento migliore per il processo in questione. Ciò premesso, i file xib sono in genere più potenti quando utilizzato per rappresentare una visualizzazione personalizzata necessaria in più posizioni in un'app, ad esempio una cella di visualizzazione tabella personalizzata. 

Per ulteriori informazioni sull'uso di file con estensione xib sono reperibili nelle soluzioni seguenti:

- [Usando il modello di visualizzazione con estensione xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Creazione di un TableViewCell personalizzato usando un xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Creazione di una schermata di avvio usando un xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Per altre informazioni sull'uso degli storyboard, vedere la [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md).

In questo e altri guide relative alla progettazione di iOS, vedere l'utilizzo degli storyboard come l'approccio standard per la creazione di interfacce utente, dal momento che la maggior parte delle xamarin. IOS nuovi modelli di progetto forniscono uno storyboard per impostazione predefinita.

## <a name="summary"></a>Riepilogo

Questa guida fornita un'introduzione a iOS Designer, che descrive le funzionalità e gli strumenti che per la progettazione di interfacce utente accattivanti offre la struttura.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [procedura dettagliata di controlli manipolati iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Panoramica della finestra di progettazione Android](~/android/user-interface/android-designer/index.md)
- [Classi e metodi parziali](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Approfondire la finestra di progettazione di Xamarin per iOS - evolvere 2014 (video)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Uso di iOS Designer per creare una schermata di avvio (video)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
