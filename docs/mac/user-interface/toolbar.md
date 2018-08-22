---
title: Barre degli strumenti di xamarin. Mac
description: Questo articolo descrive l'utilizzo con le barre degli strumenti in un'applicazione xamarin. Mac. Viene descritto come creare e mantenere le barre degli strumenti in Xcode e Interface Builder, esporre al codice e l'utilizzo a livello di codice.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 06faaf16ffd0adc64063bfa5a264c1895b9ca9cb
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251025"
---
# <a name="toolbars-in-xamarinmac"></a>Barre degli strumenti di xamarin. Mac

_Questo articolo descrive l'utilizzo con le barre degli strumenti in un'applicazione xamarin. Mac. Viene descritto come creare e mantenere le barre degli strumenti in Xcode e Interface Builder, esporre al codice e l'utilizzo a livello di codice._

Gli sviluppatori di xamarin. Mac che collabora con Visual Studio per Mac hanno accesso per gli stessi controlli dell'interfaccia utente disponibili per gli sviluppatori macOS che lavorano con Xcode, tra cui il controllo della barra degli strumenti. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Interface Builder di Xcode per creare e gestire gli elementi della barra degli strumenti. Questi elementi della barra degli strumenti possono anche essere creati in c#.

Barre degli strumenti in macOS vengono aggiunti alla sezione superiore di una finestra e consentono di accedere facilmente ai comandi correlati alle relative funzionalità. Le barre degli strumenti può essere nascosti, visualizzati o personalizzate dagli utenti di un'applicazione e può presentare gli elementi della barra degli strumenti in vari modi.

Questo articolo illustra le nozioni di base dell'utilizzo di barre degli strumenti e gli elementi della barra degli strumenti in un'applicazione xamarin. Mac. 

Prima di continuare, leggere il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo, in particolare il [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) sezioni, ovvero come illustra i concetti chiave e le tecniche che verranno usate in questa Guida.

Anche esaminare la [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) documento. Viene spiegato il `Register` e `Export` attributi usati per la connessione di classi c# alle classi di Objective-C.

## <a name="introduction-to-toolbars"></a>Introduzione alle barre degli strumenti

Qualsiasi finestra in un'applicazione macOS può includere una barra degli strumenti:

![Una finestra di esempio con una barra degli strumenti](toolbar-images/info01.png "una finestra di esempio con una barra degli strumenti")

Le barre degli strumenti forniscono un modo semplice per gli utenti dell'applicazione per accedere rapidamente alle importanti o funzionalità comunemente usate. Ad esempio, un'applicazione di modifica del documento potrebbe fornire elementi della barra degli strumenti per impostare il colore del testo, la modifica il tipo di carattere o la stampa del documento corrente.

Le barre degli strumenti possono visualizzare gli elementi in tre modi:

1. **Icona e testo** 

     ![Una barra degli strumenti con le icone e testo](toolbar-images/info02.png "una barra degli strumenti con le icone e testo")

2. **Icona solo** 

     ![Una barra degli strumenti icona sola](toolbar-images/info03.png "un solo sull'icona della barra degli strumenti")

3. **Solo testo** 

     ![Una barra degli strumenti di solo testo](toolbar-images/info04.png "una barra degli strumenti di solo testo")

Passare dalla modalità a altra facendo clic sulla barra degli strumenti e selezionando una modalità di visualizzazione dal menu di scelta rapida:

![Il menu di scelta rapida per una barra degli strumenti](toolbar-images/info05.png "il menu di scelta rapida per una barra degli strumenti")

Usare lo stesso menu da visualizzare la barra degli strumenti in una dimensione minore:

![Una barra degli strumenti con le icone piccole](toolbar-images/info06.png "una barra degli strumenti con le icone piccole")

Il menu di scelta consente inoltre di personalizzare la barra degli strumenti:

![La finestra di dialogo utilizzato per personalizzare una barra degli strumenti](toolbar-images/info07.png "la finestra di dialogo utilizzato per personalizzare una barra degli strumenti")

Quando si configura una barra degli strumenti in Interface Builder di Xcode, uno sviluppatore può fornire elementi della barra degli strumenti aggiuntivi che non fanno parte della configurazione predefinita. Gli utenti dell'applicazione possono quindi personalizzare la barra degli strumenti, aggiunta e rimozione di questi elementi predefiniti in base alle esigenze. Naturalmente, è possibile reimpostare la barra degli strumenti per la configurazione predefinita.

Barra degli strumenti si connette automaticamente al **vista** menu che consente agli utenti di nasconderlo, viene visualizzato e personalizzarlo:

![Elementi correlati sulla barra degli strumenti nel menu Visualizza](toolbar-images/info08.png "elementi correlati sulla barra degli strumenti nel menu Visualizza")

Vedere le [funzionalità incorporata di Menu](~/mac/user-interface/menu.md) informazioni più dettagliate.

Inoltre, se la barra degli strumenti è configurato correttamente in Interface Builder, l'applicazione verrà mantenuto automaticamente le personalizzazioni della barra degli strumenti tra più lanci dell'applicazione.

Le sezioni successive di questa guida viene descritto come creare e gestire le barre degli strumenti con Interface Builder di Xcode e come utilizzarle nel codice.

## <a name="setting-a-custom-main-window-controller"></a>L'impostazione di un controller di finestra personalizzati principale

Per esporre gli elementi dell'interfaccia utente al codice c# tramite Outlet e azioni, l'app xamarin. Mac devono utilizzare un controller di finestra personalizzati:

1. Aprire storyboard dell'app in Interface Builder di Xcode.
2. Selezionare il controller di finestra nella finestra di progettazione.
3. Passare al **Identity Inspector** e immettere "WindowController" come il **Class Name**: 

    [![Impostazione di un nome di classe personalizzata per il controller di finestra](toolbar-images/windowcontroller01.png "impostando un nome di classe personalizzata per il controller di finestra")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Oggetto **WindowController.cs** file verrà aggiunto al progetto nel **riquadro della soluzione** in Visual Studio per Mac: 

    ![Selezione WindowController.cs nel riquadro della soluzione](toolbar-images/windowcontroller02.png "selezionando WindowController.cs nel riquadro della soluzione")

6. Riaprire lo storyboard di Interface Builder di Xcode.
7. Il **WindowController.h** file sarà disponibile per l'uso: 

    [![Il file WindowController.h](toolbar-images/windowcontroller03.png "WindowController.h The file")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Creare e gestire le barre degli strumenti in Xcode

Le barre degli strumenti vengono create e gestite con Interface Builder di Xcode. Per aggiungere una barra degli strumenti a un'applicazione, modificare uno storyboard principale dell'app (in questo caso **Main. Storyboard**) facendo doppio clic su esso nel **riquadro della soluzione**:

![Aprire Main. Storyboard nel riquadro della soluzione](toolbar-images/edit01.png "aprire Main. Storyboard nel riquadro della soluzione")

Nel **Library Inspector**, immettere "strumento" nel **casella di ricerca** per renderne più semplice visualizzare tutti gli elementi disponibile sulla barra degli strumenti:

![Il controllo della libreria, filtrati per mostrare gli elementi della barra degli strumenti](toolbar-images/edit02.png "The Library Inspector, filtrati per mostrare gli elementi della barra degli strumenti")

Trascinare una barra degli strumenti nella finestra di **Interface Editor**. Con la barra degli strumenti selezionata, configurare il comportamento impostando le proprietà nel **Attributes Inspector**:

![Attributes Inspector per una barra degli strumenti](toolbar-images/edit04.png "finestra di ispezione degli attributi per una barra degli strumenti")

Sono disponibili le proprietà seguenti:

1. **Visualizzazione** -controlla se la barra degli strumenti vengono visualizzate le icone, testo o entrambi
2. **Visibile al momento del lancio** -se selezionato, la barra degli strumenti è visibile per impostazione predefinita.
3. **Personalizzabile** -se selezionato, gli utenti possono modificare e personalizzare la barra degli strumenti.
4. **Separatore** -se selezionato, una linea orizzontale sottile separa la barra degli strumenti dal contenuto della finestra.
5. **Dimensioni** -imposta la dimensione della barra degli strumenti
6. **Salvataggio automatico** -se selezionato, l'applicazione verrà mantenute le modifiche alla configurazione di un utente della barra degli strumenti tra avvii delle applicazioni.

Selezionare il **salvataggio automatico** opzione e lasciare tutte le altre proprietà delle impostazioni predefinite. 

Dopo aver aperto la barra degli strumenti di **gerarchia delle interfacce**, visualizzare la finestra di dialogo di personalizzazione selezionando un elemento della barra degli strumenti:

![Personalizzando la barra degli strumenti](toolbar-images/edit05.png "personalizzando la barra degli strumenti")

Utilizzare questa finestra di dialogo per impostare le proprietà per gli elementi che fanno già parte della barra degli strumenti alla barra degli strumenti predefinita per l'applicazione, progettazione e per fornire elementi della barra degli strumenti aggiuntivi per un utente di selezionare quando si personalizza la barra degli strumenti. Per aggiungere elementi alla barra degli strumenti, trascinarli dal **Library Inspector**:

![Library Inspector](toolbar-images/edit06.png "Library Inspector")

Possono essere aggiunti gli elementi della barra degli strumenti seguenti:

- **Immagine della barra degli strumenti** -un elemento della barra degli strumenti con un'immagine personalizzata come icona.
- **Barra degli strumenti flessibili spazio** -flessibile dello spazio utilizzato per giustificare gli elementi della barra degli strumenti successiva. Ad esempio, uno o più elementi della barra degli strumenti seguita da un elemento della barra degli strumenti flessibili spazio e l'ultimo elemento sul lato destro della barra degli strumenti di aggiunta di un altro elemento della barra degli strumenti.
- **Lo spazio della barra degli strumenti** -fissa di spazio tra gli elementi nella barra degli strumenti
- **Separatore della barra degli strumenti** -visibile separatore tra due o più elementi della barra degli strumenti, per il raggruppamento
- **Personalizzazione della barra degli strumenti** -consente agli utenti di personalizzare la barra degli strumenti
- **Stampa della barra degli strumenti** -consente agli utenti di stampare il documento aperto
- **Mostra i colori della barra degli strumenti** -Visualizza la selezione di colori di sistema standard: 

     ![La selezione di colori di sistema](toolbar-images/edit07.png "la selezione di colori di sistema")

- **Mostra barra degli strumenti del tipo di carattere** -consente di visualizzare la finestra di dialogo tipo di carattere di sistema standard: 

     ![Il selettore del tipo di carattere](toolbar-images/edit08.png "il selettore del tipo di carattere")

> [!IMPORTANT]
> Verranno visualizzate in un secondo momento, molti controlli dell'interfaccia utente Cocoa standard, ad esempio i campi di ricerca, controlli segmentati e i dispositivi di scorrimento orizzontale possono anche essere aggiunte a una barra degli strumenti.

### <a name="adding-an-item-to-a-toolbar"></a>Aggiunta di un elemento a una barra degli strumenti

Per aggiungere un elemento a una barra degli strumenti, selezionare la barra degli strumenti di **gerarchia delle interfacce** e fare clic su uno dei relativi elementi, causando la finestra di dialogo di personalizzazione da visualizzare. Trascinare quindi un nuovo elemento dal **Library Inspector** per il **consentiti gli elementi della barra degli strumenti** area:

![Gli elementi della barra degli strumenti è consentita nella finestra di dialogo di personalizzazione della barra degli strumenti](toolbar-images/add01.png "The consentiti gli elementi della barra della finestra di dialogo di personalizzazione della barra degli strumenti")

Per assicurarsi che un nuovo elemento fa parte della barra degli strumenti predefinita, trascinarlo in modo che il **elementi della barra degli strumenti predefiniti** area: 

![Riordinare un elemento della barra degli strumenti, trascinare](toolbar-images/add02.png "riordinare un elemento della barra degli strumenti mediante il trascinamento")

Per riordinare gli elementi della barra degli strumenti predefinita, è possibile trascinarli a sinistra o destra.

Successivamente, usare il **Attributes Inspector** per impostare le proprietà predefinite per l'elemento:

![Personalizzazione di un elemento della barra degli strumenti utilizzando Attributes Inspector](toolbar-images/add03.png "personalizzazione di un elemento della barra degli strumenti utilizzando Attributes Inspector")

Sono disponibili le proprietà seguenti:

- **Nome immagine** -immagine da usare come icona per l'elemento
- **Etichetta** -il testo da visualizzare per l'elemento nella barra degli strumenti
- **Etichetta tavolozza** -il testo da visualizzare per l'elemento nel **consentiti gli elementi della barra degli strumenti** area
- **Tag** -un identificatore univoco facoltativo che consente di identificare l'elemento nel codice.
- **Identificatore** -definisce il tipo di elemento della barra degli strumenti. Un valore personalizzato utilizzabile per selezionare un elemento della barra degli strumenti nel codice.
- **Selezionabile** -se l'opzione è selezionata, l'elemento si comporta come un pulsante di attivazione/disattivazione.

> [!IMPORTANT]
> Aggiungere un elemento per il **consentiti gli elementi della barra degli strumenti** area, ma non la barra degli strumenti predefinita per fornire le opzioni di personalizzazione per gli utenti. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Aggiunta di altri controlli dell'interfaccia utente a una barra degli strumenti

Diversi elementi dell'interfaccia utente di Cocoa, ad esempio cercare i campi e controlli segmentati possono anche essere aggiunti a una barra degli strumenti.

A questo scopo, aprire la barra degli strumenti di **gerarchia delle interfacce** e selezionare un elemento della barra degli strumenti per aprire la finestra di dialogo di personalizzazione. Trascinare un **campo di ricerca** dalle **Library Inspector** per il **consentiti gli elementi della barra degli strumenti** area:

![Usando la finestra di dialogo di personalizzazione della barra degli strumenti](toolbar-images/add05.png "usando la finestra di dialogo di personalizzazione della barra degli strumenti")

A questo punto, usare Interface Builder per configurare il campo di ricerca e lo si espone al codice tramite un'azione o di un outlet.

## <a name="built-in-toolbar-item-support"></a>Supporto dell'elemento della barra degli strumenti

Diversi elementi dell'interfaccia utente Cocoa interagiscono con gli elementi della barra degli strumenti standard per impostazione predefinita. Ad esempio, trascinare un **TextView** sulla finestra dell'applicazione e posizionarla a riempire l'area del contenuto:

[![Aggiunta di una visualizzazione di testo per l'applicazione](toolbar-images/edit09.png "aggiunta di una visualizzazione di testo per l'applicazione")](toolbar-images/edit09-large.png#lightbox)

Salvare il documento, torna a Visual Studio per Mac per la sincronizzazione con Xcode, eseguire l'applicazione, immettere il testo, selezionarlo e fare clic sui **colori** della barra degli strumenti. Si noti che la visualizzazione di testo funziona automaticamente con la selezione colori:

![Funzionalità della barra degli strumenti con una selezione di colori e visualizzazione di testo](toolbar-images/edit10.png "funzionalità della barra degli strumenti con una selezione di colori e visualizzazione di testo")

## <a name="using-images-with-toolbar-items"></a>Uso di immagini con gli elementi della barra degli strumenti

Usando un **immagine della barra degli strumenti**, aggiungere qualsiasi immagine bitmap le **risorse** cartella (e ha un'azione di compilazione **Bundle di risorse**) possono essere visualizzati sulla barra degli strumenti ridotta a icona:

1. In Visual Studio per Mac, nel **riquadro della soluzione**, fare doppio clic sui **risorse** cartella e selezionare **Add** > **Aggiungi file** .
2. Dal **Add Files** finestra di dialogo casella passare alle immagini desiderate, selezionarli e fare clic sui **Open** pulsante: 

    [![Selezione di immagini da aggiungere](toolbar-images/edit11.png "scelta delle immagini da aggiungere")](toolbar-images/edit11-large.png#lightbox)

3. Selezionare **copia**, controllare **Usa la stessa azione per tutti i file selezionati**, fare clic su **OK**:

    ![Selezionando l'azione di copia per le immagini aggiuntive](toolbar-images/edit12.png "selezionando l'azione di copia per le immagini di aggiunta")

4. Nel **riquadro della soluzione**, fare doppio clic su **MainWindow.xib** per aprirlo in Xcode.

5. Selezionare la barra degli strumenti di **gerarchia delle interfacce** e fare clic su uno dei relativi elementi per aprire la finestra di dialogo di personalizzazione.

6. Trascinare un' **immagine della barra degli strumenti** dalle **Library Inspector** alla barra degli strumenti **consentiti gli elementi della barra degli strumenti** area: 

    ![Un elemento della barra degli strumenti immagine aggiunti all'area di consentito degli elementi della barra degli strumenti](toolbar-images/edit14.png "un'immagine della barra degli strumenti elemento aggiunto all'area di consentito degli elementi della barra degli strumenti")

7. Nel **Attributes Inspector**, selezionare l'immagine che è stato appena aggiunto in Visual Studio per Mac: 

    ![Impostazione di un'immagine personalizzata per un elemento della barra degli strumenti](toolbar-images/edit15.png "impostazione di un'immagine personalizzata per un elemento della barra degli strumenti")

8. Impostare il **etichetta** "Nel Cestino" e il **tavolozza etichetta** "Cancellare documento": 

    ![Impostare la barra degli strumenti elemento etichetta ed etichetta tavolozza](toolbar-images/edit16.png "impostando la barra degli strumenti elemento tavolozza ed etichetta")

9. Trascinare un **separatore della barra degli strumenti** dalle **Library Inspector** alla barra degli strumenti **consentiti gli elementi della barra degli strumenti** area: 

    [![Un elemento della barra degli strumenti separatore aggiunti all'area di consentito degli elementi della barra degli strumenti](toolbar-images/edit17.png "un separatore della barra degli strumenti aggiunti all'area di consentito degli elementi della barra degli strumenti")](toolbar-images/edit17-large.png#lightbox)

10. Trascinare l'elemento separatore e l'elemento "Cestino" per il **elementi della barra degli strumenti predefiniti** area e impostare l'ordine della barra degli strumenti elementi da sinistra a destra nel modo seguente (i colori, tipi di carattere, separatore, nel Cestino, spazio flessibile, stampa): 

    ![Gli elementi della barra degli strumenti predefiniti](toolbar-images/edit18.png "gli elementi della barra degli strumenti predefinita")

11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Eseguire l'applicazione per verificare che la nuova barra degli strumenti viene visualizzata per impostazione predefinita:

![Un oggetto toolbar con elementi predefiniti personalizzati](toolbar-images/edit19.png "un oggetto toolbar con elementi predefiniti personalizzati")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Esporre gli elementi della barra degli strumenti con Outlet e azioni

Per accedere a una barra degli strumenti o un elemento della barra degli strumenti nel codice, deve essere collegato a una presa di corrente o un'azione:

1. Nel **riquadro della soluzione**, fare doppio clic su **Main. Storyboard** per aprirlo in Xcode.
2. Assicurarsi che la classe personalizzata "WindowController" è stato assegnato per il controller di finestra principale di **Identity Inspector**:

    [![Usando il controllo di identità per impostare una classe personalizzata per il controller di finestra](toolbar-images/edit20a.png "usando il controllo di identità per impostare una classe personalizzata per il controller di finestra")](toolbar-images/edit20a-large.png#lightbox)

3. Successivamente, selezionare l'elemento della barra degli strumenti nel **gerarchia delle interfacce**: 

    ![Selezionando l'elemento della barra degli strumenti nella gerarchia dell'interfaccia](toolbar-images/edit20.png "selezionando l'elemento della barra degli strumenti nella gerarchia dell'interfaccia")  

4. Aprire il **visualizzazione Assistant**, selezionare il **WindowController.h** file e trascinarlo dall'elemento della barra degli strumenti per il **WindowController.h** file.
5. Impostare il **connessione** digitare per **azione**, immettere "trashDocument" per il **nome**, fare clic sul **Connect** pulsante: 

    [![Configurazione di un'azione per un elemento della barra degli strumenti](toolbar-images/edit23.png "configurazione di un'azione per un elemento della barra degli strumenti")](toolbar-images/edit23-large.png#lightbox)

6. Esporre il **TextView** come un outlet chiamato "documentEditor" nel **viewcontroller. H** file: 

    [![Configurazione di un outlet per la visualizzazione di testo](toolbar-images/edit24.png "la configurazione di un outlet per la visualizzazione di testo")](toolbar-images/edit24-large.png#lightbox)

7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

In Visual Studio per Mac, modificare il **ViewController.cs** file e aggiungere il codice seguente:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Successivamente, modificare il **WindowController.cs** file e aggiungere il codice seguente alla fine del `WindowController` classe:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Quando si esegue l'applicazione, il **Cestino** della barra degli strumenti sarà attivo:

![Una barra degli strumenti con un elemento nel Cestino di active](toolbar-images/edit25.png "una barra degli strumenti con un elemento nel Cestino di active")

Si noti che il **Cestino** della barra degli strumenti può ora essere usato per eliminare il testo.

## <a name="disabling-toolbar-items"></a>La disattivazione degli elementi della barra degli strumenti

Per disabilitare un elemento in una barra degli strumenti, creare una classe personalizzata `NSToolbarItem` classe ed eseguire l'override di `Validate` (metodo). Quindi, in Interface Builder, assegnare al tipo personalizzato per l'elemento che si desidera abilitare o disabilitare.

Per creare una classe personalizzata `NSToolbarItem` classe, pulsante destro del mouse sul progetto e selezionare **Add** > **nuovo File...** . Selezionare **generali** > **classe vuota**, immettere "ActivatableItem" per il **nome**, fare clic sul **nuovo** pulsante: 

![Aggiunta di una classe vuota in Visual Studio per Mac](toolbar-images/custom01.png "aggiunta di una classe vuota in Visual Studio per Mac")

Successivamente, modificare il **ActivatableItem.cs** file come segue:

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

Fare doppio clic su **Main. Storyboard** per aprirlo in Xcode. Selezionare il **Cestino** della barra degli strumenti creata in precedenza e modificarne la classe in "ActivatableItem" nel **Identity Inspector**:

![L'impostazione di una classe personalizzata per un elemento della barra degli strumenti](toolbar-images/custom02.png "l'impostazione di una classe personalizzata per un elemento della barra degli strumenti")

Creazione di un outlet chiamato `trashItem` per il **Cestino** della barra degli strumenti. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode. Aprire infine **MainWindow.cs** e aggiornare il `AwakeFromNib` metodo come segue:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Eseguire l'applicazione e si noti che il **Cestino** elemento è disabilitato sulla barra degli strumenti:

![Una barra degli strumenti con un elemento inattivo nel Cestino](toolbar-images/custom03.png "una barra degli strumenti con un elemento inattivo nel Cestino")

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di barre degli strumenti e gli elementi della barra degli strumenti in un'applicazione xamarin. Mac. Descritto come creare e gestire le barre degli strumenti in Interface Builder di Xcode, alcuni controlli dell'interfaccia utente automaticamente funzionano con gli elementi della barra degli strumenti, come lavorare con le barre degli strumenti nel codice c# e come abilitare e disabilitare gli elementi della barra degli strumenti.


## <a name="related-links"></a>Collegamenti correlati

- [MacToolbar (esempio)](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Linee guida dell'interfaccia umana per le barre degli strumenti](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introduzione alle barre degli strumenti](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
