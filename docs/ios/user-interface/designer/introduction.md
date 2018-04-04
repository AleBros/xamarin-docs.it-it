---
title: Nozioni fondamentali sulla progettazione di iOS
description: Questa guida illustra la finestra di progettazione di Xamarin per iOS. Viene illustrato come utilizzare la finestra di progettazione iOS per visivamente il layout dei controlli, come accedere a questi controlli nel codice e come modificare le proprietà.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/31/2018
ms.openlocfilehash: 7e36a402619813214e821f3060e053d76c99cfb7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="ios-designer-basics"></a>Nozioni fondamentali sulla progettazione di iOS

_Questa guida illustra la finestra di progettazione di Xamarin per iOS. Viene illustrato come utilizzare la finestra di progettazione iOS per visivamente il layout dei controlli, come accedere a questi controlli nel codice e come modificare le proprietà._

La finestra di progettazione di Xamarin per iOS è una finestra di progettazione interfaccia visiva, simile a Generatore di interfaccia del Xcode e la finestra di progettazione Android. Alcune delle numerose funzionalità includono integrazione con Visual Studio per Mac e Visual Studio 2015 e 2017, la modifica di trascinamento e rilascio, un'interfaccia per l'impostazione dei gestori eventi e la possibilità di eseguire il rendering di controlli personalizzati.

## <a name="requirements"></a>Requisiti

IOS progettazione è disponibile in Visual Studio per Mac e in Visual Studio 2015 e 2017 in Windows. In Visual Studio 2015 o 2017, la finestra di progettazione iOS richiede una connessione a un host di compilazione Mac configurato correttamente, anche se non è in esecuzione Xcode.

Questa guida si presuppone una certa familiarità con il contenuto incluso nel [Getting Started Guide](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Funzionamento della finestra di progettazione iOS

In questa sezione viene descritto come la finestra di progettazione iOS facilita la creazione di un'interfaccia utente e connetterlo al codice.

Finestra di progettazione iOS consente agli sviluppatori di progettare un'interfaccia utente. Come descritto nel [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md) guide uno storyboard vengono descritte le schermate (view controller) che costituiscono un'app, gli elementi dell'interfaccia (visualizzazioni) posizionati in tali controller di visualizzazione e del flusso di navigazione complessiva dell'applicazione . 

Un controller di visualizzazione dispone di due parti: una rappresentazione visiva nella finestra di progettazione iOS e una classe c# associata:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Un controller di visualizzazione nella finestra di progettazione iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "un controller di visualizzazione nella finestra di progettazione iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Il codice per un controller di visualizzazione](introduction-images/2-viewcontrollercode-vsmac.png "il codice per un controller di visualizzazione")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Un controller di visualizzazione nella finestra di progettazione iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "un controller di visualizzazione nella finestra di progettazione iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Il codice per un controller di visualizzazione](introduction-images/2-viewcontrollercode-vs.png "il codice per un controller di visualizzazione")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Nello stato predefinito, un controller di visualizzazione non fornisce alcuna funzionalità; è necessario popolare con i controlli. Questi controlli vengono inseriti nella vista del controller di visualizzazione, l'area rettangolare che include tutto il contenuto della schermata. La maggior parte dei controller di visualizzazione contenere controlli comuni, ad esempio pulsanti, etichette e i campi di testo, come illustrato nella schermata seguente, che mostra un controller di visualizzazione che contiene un pulsante: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Un controller di visualizzazione che contiene un pulsante](introduction-images/3-viewcontrollerwithbutton-vsmac.png "un controller di visualizzazione che contiene un pulsante")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Un controller di visualizzazione che contiene un pulsante](introduction-images/3-viewcontrollerwithbutton-vs.png "un controller di visualizzazione che contiene un pulsante")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Alcuni controlli, ad esempio le etichette che contiene testo statico, possono essere aggiunti al controller di visualizzazione e lasciare invariato. Tuttavia, molto spesso, i controlli devono essere personalizzati a livello di codice. Ad esempio, il pulsante aggiunto in precedenza deve eseguire un'operazione quando si tocca, pertanto un gestore eventi deve essere aggiunta nel codice.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per accedere e modificare il pulsante nel codice, è necessario disporre di un identificatore univoco. Fornisce un identificatore univoco, selezionare il pulsante, aprire il **proprietà riempimento**e l'impostazione relativa **nome** campo su un valore, ad esempio "SubmitButton":

[![Impostazione del nome del pulsante nel riquadro proprietà](introduction-images/4-settingbuttonname-vsmac.png "impostando il nome di un pulsante nel riquadro proprietà")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per accedere e modificare il pulsante nel codice, è necessario disporre di un identificatore univoco. Fornisce un identificatore univoco, selezionare il pulsante, aprire il **finestra proprietà**e l'impostazione relativa **nome** campo su un valore, ad esempio "SubmitButton":

[![Impostazione del nome del pulsante nella finestra proprietà](introduction-images/4-settingbuttonname-vs.png "impostando il nome di un pulsante nella finestra proprietà")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Ora che il pulsante ha un nome, è possibile accedervi nel codice. Ma questo funzionamento?

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Nel **soluzione riempimento**, la navigazione a **ViewController.cs** e facendo clic sull'indicatore della diffusione rivela che il controller di visualizzazione `ViewController` intervalli di definizione di classe due file, ognuno dei quali contiene un [classe parziale](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definizione:

[![I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "i due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Nel **Esplora**, la navigazione a **ViewController.cs** e facendo clic sull'indicatore della diffusione rivela che il controller di visualizzazione `ViewController` definizione di classe si estende su due file, ognuno dei che contiene un [classe parziale](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definizione:

[![I due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "i due file che costituiscono la classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** deve essere popolato con codice personalizzato correlato il `ViewController` classe. In questo file, la `ViewController` classe può rispondere a iOS vari metodi di visualizzazione del controller del ciclo di vita, personalizzazione dell'interfaccia utente e rispondere all'input, ad esempio pulsante scelte dell'utente.

- **ViewController.designer.cs** è un file generato, creato mediante la finestra di progettazione per eseguire il mapping al codice dell'interfaccia costruita visivamente iOS. Poiché le modifiche a questo file verranno sovrascritto, non deve essere modificato. Dichiarazioni di proprietà in questo file rendono possibile per il codice nel `ViewController` classe per l'accesso, da **nome**, controlla i set di backup nella finestra di progettazione iOS. Apertura **ViewController.designer.cs** indica il codice seguente:

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

Il `SubmitButton` dichiarazione di proprietà si connette l'intero `ViewController` classe, non solo il **ViewController.designer.cs** file per il pulsante definito nello storyboard. Poiché **ViewController.cs** definisce una parte di `ViewController` (classe), dispone dell'accesso a `SubmitButton`.

Nella schermata seguente viene illustrato che IntelliSense riconosce ora il `SubmitButton` fa riferimento nella **ViewController.cs**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![IntelliSense riconosce il riferimento SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "riconoscendo il riferimento SubmitButton IntelliSense")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![IntelliSense riconosce il riferimento SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "riconoscendo il riferimento SubmitButton IntelliSense")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

In questa sezione è illustrato come creare un pulsante nella finestra di progettazione iOS e accedere a tale pulsante nel codice.

Il resto di questo documento fornisce una panoramica di ulteriore della finestra di progettazione iOS.

## <a name="ios-designer-basics"></a>Nozioni fondamentali sulla progettazione di iOS

In questa sezione vengono illustrate le parti della finestra di progettazione iOS e viene fornita una panoramica delle funzionalità.

### <a name="launching-the-ios-designer"></a>Avviare la finestra di progettazione iOS

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Progetti di xamarin. IOS creati con Visual Studio per Mac includono uno storyboard. Per visualizzare il contenuto di uno storyboard, fare doppio clic sul file .storyboard nel **soluzione riempimento**:

[![Aprire uno storyboard nella finestra di progettazione iOS](introduction-images/7-storyboardopen-vsmac.png "aprire uno storyboard nella finestra di progettazione iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

La maggior parte dei progetti di xamarin. IOS creati con Visual Studio 2015 o 2017 includono uno storyboard. Per visualizzare il contenuto di uno storyboard, fare doppio clic sul file .storyboard nel **Esplora**:

[![Aprire uno storyboard nella finestra di progettazione iOS](introduction-images/7-storyboardopen-vs.png "aprire uno storyboard nella finestra di progettazione iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>funzionalità di progettazione iOS

Finestra di progettazione iOS ha sei sezioni principali:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Nelle sezioni della finestra di progettazione iOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "sezioni della finestra di progettazione iOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Area di progettazione** : area di lavoro principale della finestra di progettazione iOS. Nell'area del documento, in questo modo la costruzione di interfacce utente visual.
2. **Barra degli strumenti vincoli** : consente di alternare frame modalità di modifica e modalità di modifica di vincoli, due diversi modi per posizionare gli elementi in un'interfaccia utente.
3. **Casella degli strumenti** : sono elencati i controller, gli oggetti, controlli, le visualizzazioni dati, i riconoscitori di movimento, windows e le barre che può essere trascinato nell'area di progettazione e aggiunto a un'interfaccia utente.
4. **Proprietà riempimento** : Visualizza le proprietà per il controllo selezionato, tra cui identità, gli stili di visualizzazione, accessibilità, layout e comportamento.
5. **Struttura documento** : viene illustrato l'albero di controlli che compongono il layout per l'interfaccia in fase di modifica. Facendo clic su un elemento nell'albero viene selezionata nella finestra di progettazione iOS e Mostra le proprietà nel **proprietà riempimento**. Ciò è utile per la selezione di un controllo specifico in un'interfaccia utente profondamente annidato.
6. **Barra degli strumenti inferiore** – contiene le opzioni per la modifica come la finestra di progettazione iOS consente di visualizzare il file .storyboard o .xib, inclusi i dispositivi, l'orientamento e zoom.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nelle sezioni della finestra di progettazione iOS](introduction-images/8-sixpartsofiosdesigner-vs.png "sezioni della finestra di progettazione iOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Area di progettazione** : area di lavoro principale della finestra di progettazione iOS. Nell'area del documento, in questo modo la costruzione di interfacce utente visual.
2. **Barra degli strumenti vincoli** : consente di alternare frame modalità di modifica e modalità di modifica di vincoli, due diversi modi per posizionare gli elementi in un'interfaccia utente.
3. **Casella degli strumenti** : sono elencati i controller, gli oggetti, controlli, le visualizzazioni dati, i riconoscitori di movimento, windows e le barre che può essere trascinato nell'area di progettazione e aggiunto a un'interfaccia utente.
4. **Finestra proprietà** : Visualizza le proprietà per il controllo selezionato, tra cui identità, gli stili di visualizzazione, accessibilità, layout e comportamento.
5. **Struttura documento** : viene illustrato l'albero di controlli che compongono il layout per l'interfaccia in fase di modifica. Facendo clic su un elemento nell'albero viene selezionata nella finestra di progettazione iOS e Mostra le proprietà nel **finestra proprietà**. Ciò è utile per la selezione di un controllo specifico in un'interfaccia utente profondamente annidato.
6. **Barra degli strumenti inferiore** – contiene le opzioni per la modifica come la finestra di progettazione iOS consente di visualizzare il file .storyboard o .xib, inclusi i dispositivi, l'orientamento e zoom.

-----

### <a name="design-workflow"></a>Progettazione del flusso di lavoro

#### <a name="adding-a-control-to-the-interface"></a>Aggiunta di un controllo per l'interfaccia

Per aggiungere un controllo a un'interfaccia, trascinarla dal **della casella degli strumenti** e rilasciarlo nell'area di progettazione. Quando si aggiunge o posizionare un controllo, le linee guida verticale e orizzontale evidenzia posizioni di layout utilizzate, ad esempio i margini, center orizzontale e verticale center:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
 
![Nell'area di progettazione, linee guida per evidenziare le posizioni di layout di uso comune](introduction-images/9-layoutguides-vsmac.png "nell'area di progettazione, linee guida per evidenziare le posizioni di layout di uso comune")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Nell'area di progettazione, linee guida per evidenziare le posizioni di layout di uso comune](introduction-images/9-layoutguides-vs.png "nell'area di progettazione, linee guida per evidenziare le posizioni di layout di uso comune")

-----

La linea blu punteggiata nell'esempio precedente offre un'indicazione visual allineamento orizzontale center agevola il posizionamento del pulsante.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

#### <a name="context-menu-commands"></a>Comandi del menu contestuale

È disponibile nell'area di progettazione e in un menu di scelta rapida di **struttura documento**. Questo menu sono disponibili comandi per il controllo selezionato e il relativo elemento padre, che è utile quando si lavora con le viste in una gerarchia annidata:

[![Menu di scelta rapida nell'area di progettazione](introduction-images/10-contextmenudesignsurface-vsmac.png "menu di scelta rapida nell'area di progettazione")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

### <a name="constraints-toolbar"></a>Barra degli strumenti di vincoli

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
 
[![La barra degli strumenti vincoli](introduction-images/11-constraintstoolbar-vsmac.png "la barra degli strumenti di vincoli")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![La barra degli strumenti vincoli](introduction-images/11-constraintstoolbar-vs.png "la barra degli strumenti di vincoli")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

La barra degli strumenti vincoli è stata aggiornata e ora costituito da due controlli: il frame di modalità di modifica / vincolo modifica attiva/disattiva modalità e i vincoli di aggiornamento / frame pulsante Aggiorna.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modalità di modifica frame / attiva/disattiva modalità di modifica di vincoli

Nelle versioni precedenti di iOS progettazione, fare clic su una vista già selezionato nell'area di progettazione attivata/disattivata tra modalità di modifica e la modalità di modifica vincolo frame. A questo punto, un interruttore nella barra degli strumenti vincoli passa tra le modalità di modifica.

- Modalità di modifica frame:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Pulsante modalità di modifica frame](introduction-images/12a-frameeditingmode-vsmac.png "Frame pulsante modalità modifica")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Pulsante modalità di modifica frame](introduction-images/12a-frameeditingmode-vs.png "Frame pulsante modalità modifica")

-----

- Modalità di modifica vincolo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Pulsante modalità di modifica vincolo](introduction-images/12b-constrainteditingmode-vsmac.png "pulsante modalità Modifica vincolo")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Pulsante modalità di modifica vincolo](introduction-images/12b-constrainteditingmode-vs.png "pulsante modalità Modifica vincolo")

-----

#### <a name="update-constraints--update-frames-button"></a>Aggiorna vincoli / frame pulsante Aggiorna

I vincoli di aggiornamento / aggiornamento frame risiede pulsante a destra del riquadro di modalità di modifica / attiva/disattiva modalità di modifica di vincoli.

- Nel frame di modalità di modifica, fare clic su questo pulsante consente di regolare i frame di tutti gli elementi selezionati in modo che corrisponda i vincoli.
- In modalità di modifica di vincoli, fare clic su questo pulsante consente di regolare i vincoli di tutti gli elementi selezionati in modo che corrisponda i frame.

### <a name="bottom-toolbar"></a>Barra degli strumenti nella parte inferiore

Barra degli strumenti inferiore consente di selezionare il dispositivo, l'orientamento e zoom consente di visualizzare un file dello storyboard o .xib nella finestra di progettazione iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Barra degli strumenti inferiore, usato per selezionare un dispositivo e l'orientamento per l'area di progettazione](introduction-images/13-bottomtoolbar-vsmac.png "barra degli strumenti inferiore, usato per selezionare un dispositivo e l'orientamento per l'area di progettazione")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Barra degli strumenti inferiore, usato per selezionare un dispositivo e l'orientamento per l'area di progettazione](introduction-images/13-bottomtoolbar-vs.png "barra degli strumenti inferiore, usato per selezionare un dispositivo e l'orientamento per l'area di progettazione")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo e l'orientamento

Se viene espansa, barra degli strumenti inferiore vengono visualizzati tutti i dispositivi, gli orientamenti e/o adattamenti applicabili al documento corrente. Fare clic su essi viene modificata la visualizzazione nell'area di progettazione. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Barra degli strumenti inferiore espansa per visualizzare i dispositivi e gli orientamenti](introduction-images/14-bottomtoolbarexpanded-vsmac.png "barra degli strumenti inferiore, espanso per visualizzare i dispositivi e gli orientamenti")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Barra degli strumenti inferiore espansa per visualizzare i dispositivi e gli orientamenti](introduction-images/14-bottomtoolbarexpanded-vs.png "barra degli strumenti inferiore, espanso per visualizzare i dispositivi e gli orientamenti")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Si noti che la selezione di un dispositivo e l'orientamento modifica solo come finestra di progettazione iOS Visualizza l'anteprima di progettazione. Indipendentemente dalla selezione corrente, aggiunta di vincoli vengono applicati a tutti i dispositivi e gli orientamenti, a meno che il **modificare tratti** pulsante è stato utilizzato per specificare in caso contrario.

Quando [dimensioni classi](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) sono [abilitato](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), **modificare tratti** pulsante verrà visualizzato nella barra degli strumenti inferiore espanso.  Fare clic su di **modificare tratti** pulsante Visualizza le opzioni per la creazione di una variante di interfaccia in base alla classe dimensioni rappresentata dal dispositivo selezionato e l'orientamento. Considerare gli esempi seguenti:

- Se **iPhone SE** / **verticale**, è selezionata, popover verranno fornite opzioni per creare una variante di interfaccia per la larghezza compact, classe di dimensioni di altezza regolare. 
- Se **iPad Pro 9.7"** / **orizzontale** / **schermo** è selezionata, popover verranno fornite opzioni per creare una variante di interfaccia per la larghezza della normale, classe di dimensioni di altezza regolare.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Barra degli strumenti inferiore viene utilizzato per modificare un'interfaccia dalla classe di dimensioni](introduction-images/15-edittraitsbutton-vsmac.png "barra degli strumenti inferiore viene utilizzato per modificare un'interfaccia dalla classe di dimensione")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Barra degli strumenti inferiore viene utilizzato per modificare un'interfaccia dalla classe di dimensioni](introduction-images/15-edittraitsbutton-vs.png "barra degli strumenti inferiore viene utilizzato per modificare un'interfaccia dalla classe di dimensione")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controlli zoom

Finestra di progettazione supporta lo zoom tramite diversi controlli:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
 
![I controlli zoom nella barra degli strumenti inferiore](introduction-images/16-zoomcontrols-vsmac.png "i controlli zoom sulla barra degli strumenti nella parte inferiore")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![I controlli zoom nella barra degli strumenti inferiore](introduction-images/16-zoomcontrols-vs.png "i controlli zoom sulla barra degli strumenti nella parte inferiore")

-----

I controlli di seguito:

1. Adatta alla
2. Fare zoom indietro
3. Fare zoom avanti
4. Dimensioni effettive (dimensioni in pixel di 1:1)

Questi controlli regolano lo zoom nella finestra di progettazione. Non influiscono l'interfaccia utente dell'applicazione in fase di esecuzione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

### <a name="properties-pad"></a>Proprietà riempimento

Utilizzare il **proprietà riempimento** per modificare l'identità, gli stili di visualizzazione, accessibilità e comportamento di un controllo. Nella schermata seguente viene illustrato il **proprietà riempimento** opzioni per un pulsante:

[![Il riquadro proprietà per un pulsante](introduction-images/17-buttonpropertiespad-vsmac.png "il riquadro proprietà per un pulsante")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Nelle sezioni di proprietà riempimento

Il **proprietà riempimento** contiene tre sezioni:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="properties-window"></a>Finestra Proprietà

Utilizzare il **finestra proprietà** per modificare l'identità, gli stili di visualizzazione, accessibilità e comportamento di un controllo. Nella schermata seguente viene illustrato il **finestra proprietà** opzioni per un pulsante:

[![Finestra proprietà per un pulsante](introduction-images/17-buttonpropertieswindow-vs.png "la finestra proprietà per un pulsante")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Nelle sezioni di proprietà finestra

Il **finestra proprietà** contiene tre sezioni:

-----

1.  **Widget** – proprietà principali del controllo, ad esempio nome, classe, le proprietà di stile e così via. Le proprietà per la gestione del contenuto del controllo vengono in genere posizionate qui.
2.  **Layout** : tenere traccia di posizione e dimensioni del controllo, incluso i vincoli e frame, le proprietà sono elencate di seguito.
3.  **Eventi** : eventi e gestori eventi specificati. Utile per la gestione degli eventi, ad esempio tocco tocco, trascinamento, e così via. Gli eventi possono anche essere gestiti direttamente nel codice.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

#### <a name="editing-properties-in-the-properties-pad"></a>Modifica delle proprietà nel riquadro proprietà

Oltre alla modifica visiva nell'area di progettazione, la finestra di progettazione iOS supporta la modifica delle proprietà nel **proprietà riempimento**. La modifica di proprietà disponibili in base al controllo selezionato, come illustrato schermate riportate di seguito:

[![Pulsante proprietà](introduction-images/18a-buttonpropertiespad-vsmac.png "pulsante delle proprietà")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Visualizzare le proprietà del controller](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "consente di visualizzare le proprietà del controller")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="editing-properties-in-the-properties-window"></a>Modifica delle proprietà nella finestra proprietà

Oltre alla modifica visiva nell'area di progettazione, la finestra di progettazione iOS supporta la modifica delle proprietà nel **finestra proprietà**. La modifica di proprietà disponibili in base al controllo selezionato, come illustrato schermate riportate di seguito:

[![Pulsante proprietà](introduction-images/18a-buttonpropertieswindow-vs.png "pulsante delle proprietà")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Visualizzare le proprietà del controller](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "consente di visualizzare le proprietà del controller")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> Sezione relativa all'identità dell'ora il riquadro proprietà sono visualizzati un **modulo** campo. È necessario compilare in questa sezione solo quando si interagisce con le classi Agile. Utilizzarlo per immettere un nome di modulo per le classi Agile, ovvero con spazio dei nomi.

#### <a name="default-values"></a>Valori predefiniti

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Molte proprietà nel **proprietà riempimento** mostra alcun valore o un valore predefinito. Tuttavia, il codice dell'applicazione può ancora modificare questi valori. Il **proprietà riempimento** non vengono visualizzati i valori impostati nel codice.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Molte proprietà nel **finestra proprietà** mostra alcun valore o un valore predefinito. Tuttavia, il codice dell'applicazione può ancora modificare questi valori. Il **finestra proprietà** non vengono visualizzati i valori impostati nel codice.

-----

#### <a name="event-handlers"></a>Gestori eventi

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per specificare i gestori di eventi personalizzati per i vari eventi, utilizzare il **eventi** scheda della finestra il **proprietà riempimento**. Ad esempio, nella schermata seguente, un `HandleClick` metodo gestisce il pulsante **tocco interno** evento:

[![Il riquadro proprietà, con un gestore eventi per un pulsante impostata](introduction-images/19-buttonpropertiespadevents-vsmac.png "il riquadro proprietà, con un gestore eventi per un pulsante impostata")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per specificare i gestori di eventi personalizzati per i vari eventi, utilizzare il **eventi** scheda della finestra di **finestra proprietà**. Ad esempio, nella schermata seguente, un `HandleClick` metodo gestisce il pulsante **tocco interno** evento:

[![Nella finestra Proprietà, utilizzando un gestore eventi per un pulsante impostata](introduction-images/19-buttonpropertieswindowevents-vs.png "la finestra proprietà con un gestore eventi per un pulsante impostata")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Dopo che è stato specificato un gestore eventi, aggiungere un metodo con lo stesso nome per la classe controller vista corrispondente. In caso contrario, un `unrecognized selector` eccezione si verifica quando si tocca il pulsante:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Un'eccezione non riconosciuto selettore](introduction-images/20-unrecognizedselector-vsmac.png "un'eccezione di selettore non riconosciuto")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Si noti che dopo un gestore eventi sono stato specificato nel **proprietà riempimento**, iOS progettazione verrà immediatamente aprire il file di codice corrispondente e offrire inserire la dichiarazione del metodo. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Un'eccezione non riconosciuto selettore](introduction-images/20-unrecognizedselector-vs.png "un'eccezione di selettore non riconosciuto")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Per un esempio di utilizzo di gestori di eventi personalizzati, vedere il [Hello, iOS Guida introduttiva](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Visualizzazione struttura

La finestra di progettazione iOS può anche visualizzare la gerarchia dell'interfaccia di controlli come una struttura. La struttura è disponibile se si seleziona il **struttura documento** scheda, come illustrato di seguito:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![La struttura del documento](introduction-images/21-buttonoutlineview-vsmac.png "la struttura del documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![La struttura del documento](introduction-images/21-buttonoutlineview-vs.png "la struttura del documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

Il controllo selezionato nella visualizzazione struttura rimane sincronizzato con il controllo selezionato nell'area di progettazione.  Questa funzionalità è utile per la selezione di un elemento da una gerarchia di annidamento di interfaccia.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="revert-to-xcode"></a>Ripristinare Xcode

È possibile utilizzare indifferentemente iOS progettazione e il generatore di interfaccia Xcode. Per aprire uno storyboard o un file .xib in Xcode interfaccia generatore, fare clic sul file e selezionare **Apri con > Xcode interfaccia generatore**, come illustrato nella schermata riportata di seguito:

[![Apertura di uno storyboard in Xcode interfaccia generatore](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "apertura di uno storyboard in Xcode interfaccia generatore")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Dopo aver apportato le modifiche in Generatore di interfaccia di Xcode, salvare il file e tornare a Visual Studio per Mac. Le modifiche verranno sincronizzate con il progetto xamarin. IOS.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

## <a name="xib-support"></a>supporto .xib

Finestra di progettazione iOS supporta la creazione, modifica e la gestione dei file .xib. Questi sono file XML che rappresenta solo personalizzato visualizzazioni che possono essere aggiunti alla gerarchia di visualizzazione di un'applicazione. Un file .xib rappresenta in genere l'interfaccia per una singola visualizzazione o una schermata in un'applicazione, mentre uno storyboard rappresenta molti schermate e le transizioni tra di essi.

Esistono molti opinioni sulle soluzioni – .xib file storyboard o codice – adatto per la creazione e gestione di un'interfaccia utente. In realtà, non c'è alcuna soluzione ideale ed è sempre opportuno considerare lo strumento migliore per il processo in questione. Ciò premesso, .xib file sono in genere più efficaci quando utilizzata per rappresentare una visualizzazione personalizzata necessaria in più posizioni in un'applicazione, ad esempio una cella di visualizzazione di tabelle personalizzati. 

Per ulteriori informazioni sull'utilizzo dei file .xib è reperibile nelle soluzioni seguenti:

- [Utilizzando il modello di visualizzazione .xib](https://developer.xamarin.com/recipes/ios/general/templates/using_the_ios_view_xib_template/)
- [Creazione di un TableViewCell personalizzato utilizzando un .xib](https://developer.xamarin.com/recipes/ios/content_controls/tables/custom-tableviewcell/)
- [Creazione di una schermata di avvio utilizzando un .xib](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)

Per ulteriori informazioni sull'utilizzo degli storyboard, consultare il [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md).

Questa e altre guide di progettazione iOS fanno riferimento all'utilizzo degli storyboard come l'approccio standard per la creazione di interfacce utente, poiché per impostazione predefinita, la maggior parte delle xamarin nuovi modelli di progetto forniscono uno storyboard.

## <a name="summary"></a>Riepilogo

Questa guida fornita un'introduzione alla progettazione, che descrive le funzionalità e gli strumenti che per la progettazione di interfacce utente accattivanti offre la struttura di iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [procedura dettagliata controlli possa iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Panoramica della finestra di progettazione Android](~/android/user-interface/android-designer/index.md)
- [Classi e metodi parziali](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Vengono descritti nella finestra di progettazione di Xamarin per iOS - 2014 evolvere (video)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Utilizzando la finestra di progettazione iOS per creare una schermata di avvio (video)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
