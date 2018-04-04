---
title: Barre degli strumenti
description: In questo articolo descrive l'utilizzo con le barre degli strumenti in un'applicazione Xamarin.Mac. Vengono illustrate la creazione e gestione barre degli strumenti Xcode e interfaccia generatore esporle al codice e l'utilizzo a livello di codice.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 729c5c69d80c52047585d1026d7c675f3267f34e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="toolbars"></a>Barre degli strumenti

_In questo articolo descrive l'utilizzo con le barre degli strumenti in un'applicazione Xamarin.Mac. Vengono illustrate la creazione e gestione barre degli strumenti Xcode e interfaccia generatore esporle al codice e l'utilizzo a livello di codice._

Gli sviluppatori di Xamarin.Mac utilizzo di Visual Studio per Mac hanno accesso per gli stessi controlli dell'interfaccia utente disponibili per gli sviluppatori macOS funziona con Xcode, tra cui il controllo barra degli strumenti. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare Generatore di Xcode interfaccia per creare e gestire gli elementi della barra degli strumenti. Questi elementi della barra degli strumenti possono anche essere creati in c#.

Barre degli strumenti in macOS vengono aggiunti alla sezione superiore di una finestra e consentono di accedere a comandi relativi alle funzionalità. Barre degli strumenti possono essere nascosti, visualizzati o personalizzate con gli utenti di un'applicazione e può presentare gli elementi della barra degli strumenti in vari modi.

Questo articolo vengono illustrate le nozioni di base dell'utilizzo di barre degli strumenti e gli elementi della barra degli strumenti in un'applicazione Xamarin.Mac. 

Prima di continuare, leggere la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) sezioni, come vengono illustrati i concetti chiave e tecniche che verranno utilizzate in questa Guida.

Anche un quadro di [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento. Viene spiegato il `Register` e `Export` gli attributi utilizzati per la connessione di classi c# alle classi Objective-C.

## <a name="introduction-to-toolbars"></a>Introduzione alle barre degli strumenti

Tutte le finestre in un'applicazione macOS possono includere una barra degli strumenti:

![Una finestra di esempio con una barra degli strumenti](toolbar-images/info01.png "una finestra di esempio con una barra degli strumenti")

Barre degli strumenti forniscono un modo semplice per gli utenti dell'applicazione di accedere rapidamente a importanti o le funzionalità di uso comune. Ad esempio, un'applicazione di modifica del documento potrebbe fornire elementi della barra degli strumenti per l'impostazione del colore del testo, la modifica del tipo di carattere o la stampa del documento corrente.

Barre degli strumenti è possibile visualizzare gli elementi in tre modi:

1. **Icona e testo** 

     ![Una barra degli strumenti con icone e testo](toolbar-images/info02.png "una barra degli strumenti con icone e testo")

2. **Icona solo** 

     ![Una barra degli strumenti solo icona](toolbar-images/info03.png "una barra degli strumenti solo icona")

3. **Solo testo** 

     ![Una barra degli strumenti di solo testo](toolbar-images/info04.png "una barra degli strumenti di solo testo")

Passare dalla modalità a altra facendo clic sulla barra degli strumenti e selezionando una modalità di visualizzazione dal menu di scelta rapida:

![Menu di scelta rapida per una barra degli strumenti](toolbar-images/info05.png "menu di scelta rapida per una barra degli strumenti")

Utilizzare lo stesso menu per visualizzare la barra degli strumenti in una dimensione più piccola:

![Una barra degli strumenti con icone piccole](toolbar-images/info06.png "una barra degli strumenti con icone piccole")

Il menu consente inoltre di personalizzare la barra degli strumenti:

![La finestra di dialogo consentono di personalizzare una barra degli strumenti](toolbar-images/info07.png "la finestra di dialogo consentono di personalizzare una barra degli strumenti")

Quando si configura una barra degli strumenti in interfaccia generatore del Xcode, uno sviluppatore può fornire elementi della barra degli strumenti aggiuntivi che non fanno parte della configurazione predefinita. Gli utenti dell'applicazione possono quindi personalizzare la barra degli strumenti, aggiunta e rimozione di questi elementi predefiniti in base alle esigenze. Naturalmente, è possibile reimpostare la barra degli strumenti per la configurazione predefinita.

La barra degli strumenti si connette automaticamente al **vista** menu che consente agli utenti di nasconderlo, visualizzarlo e personalizzarlo:

![Elementi correlati sulla barra degli strumenti del menu Visualizza](toolbar-images/info08.png "elementi correlati sulla barra degli strumenti del menu Visualizza")

Vedere il [funzionalità incorporate di Menu](~/mac/user-interface/menu.md) documentazione per maggiori dettagli.

Inoltre, se la barra degli strumenti è configurata correttamente in Generatore di interfaccia, l'applicazione verrà automaticamente mantenere le personalizzazioni della barra degli strumenti più avvii dell'applicazione.

Le sezioni successive di questa guida viene descritto come creare e gestire le barre degli strumenti con generatore del Xcode di interfaccia e come utilizzarle nel codice.

## <a name="setting-a-custom-main-window-controller"></a>L'impostazione di un controller personalizzato finestra principale

Per esporre elementi dell'interfaccia utente al codice c# tramite punti vendita e le azioni, l'app Xamarin.Mac deve utilizzare un controller di finestra personalizzati:

1. Aprire storyboard dell'app in Generatore del Xcode di interfaccia.
2. Selezionare il controller di finestra nell'area di progettazione.
3. Passare il **controllo di identità** e immettere "WindowController" come il **nome classe**: 

    [![Impostazione di un nome di classe personalizzata per il controller finestra](toolbar-images/windowcontroller01.png "impostazione di un nome di classe personalizzata per il controller di finestra")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Oggetto **WindowController.cs** file verrà aggiunto al progetto nel **soluzione riempimento** in Visual Studio per Mac: 

    ![Selezione WindowController.cs nel riquadro soluzione](toolbar-images/windowcontroller02.png "selezionando WindowController.cs nel riquadro soluzione")

6. Riaprire lo storyboard in Generatore del Xcode di interfaccia.
7. Il **WindowController.h** file sarà disponibile per l'utilizzo: 

    [![Il file WindowController.h](toolbar-images/windowcontroller03.png "WindowController.h il file")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Creazione e gestione di barre degli strumenti in Xcode

Barre degli strumenti vengono create e gestite con generatore del Xcode di interfaccia. Per aggiungere una barra degli strumenti a un'applicazione, modificare uno storyboard principale dell'applicazione (in questo caso **Main**) facendo doppio clic su esso nel **soluzione riempimento**:

![Apertura di Main nel riquadro soluzione](toolbar-images/edit01.png "apertura Main nel riquadro soluzione")

Nel **controllo libreria**, immettere "strumento" nel **casella di ricerca** per renderne più semplice visualizzare tutti gli elementi della barra degli strumenti disponibili:

![Il controllo di libreria, filtrato per mostrare gli elementi della barra degli strumenti](toolbar-images/edit02.png "il controllo di libreria, filtrato per mostrare gli elementi della barra degli strumenti")

Trascinare una barra degli strumenti nella finestra del **Editor dell'interfaccia**. Barra degli strumenti selezionata, configurare il comportamento impostando le proprietà nel **attributi controllo**:

![Il controllo degli attributi per una barra degli strumenti](toolbar-images/edit04.png "il controllo degli attributi per una barra degli strumenti")

Sono disponibili le proprietà seguenti:

1. **Visualizzazione** -controlla se la barra degli strumenti consente di visualizzare le icone, testo o entrambi
2. **Visibile all'avvio** -se selezionata, la barra degli strumenti è visibile per impostazione predefinita.
3. **Personalizzabile** -se selezionata, gli utenti possono modificare e personalizzare la barra degli strumenti.
4. **Separatore** -se selezionata, una linea orizzontale sottile separa la barra degli strumenti dal contenuto della finestra.
5. **Dimensioni** -imposta le dimensioni della barra degli strumenti
6. **Salvataggio automatico** -se selezionata, l'applicazione verrà mantenuto le modifiche alla configurazione di un utente della barra degli strumenti tra applicazioni.

Selezionare il **salvataggio** opzione e lasciare tutte le altre proprietà delle impostazioni predefinite. 

Dopo aver aperto la barra degli strumenti di **interfaccia gerarchia**, visualizzare la finestra di dialogo di personalizzazione selezionando un elemento della barra degli strumenti:

![Personalizzazione della barra degli](toolbar-images/edit05.png "Personalizzazione barra degli strumenti")

Utilizzare questa finestra di dialogo per impostare le proprietà per gli elementi che fanno già parte della barra degli strumenti, per progettare la barra degli strumenti predefinita per l'applicazione, e per fornire elementi della barra degli strumenti aggiuntivi per un utente di selezionare quando si personalizza la barra degli strumenti. Per aggiungere elementi alla barra degli strumenti, trascinarli dal **controllo libreria**:

![Il controllo della libreria](toolbar-images/edit06.png "il controllo della libreria")

Aggiungere gli elementi della barra degli strumenti seguenti:

- **Immagine della barra degli strumenti** -un elemento della barra degli strumenti con un'immagine personalizzata come icona.
- **Barra degli strumenti flessibile spazio** -spazio flessibile per giustificare gli elementi della barra degli strumenti successiva. Ad esempio, uno o più elementi della barra degli strumenti seguita da un elemento della barra degli strumenti spazio flessibile e aggiunta di un altro elemento della barra degli strumenti l'ultimo elemento sul lato destro della barra degli strumenti.
- **Lo spazio della barra degli strumenti** -fisso uno spazio tra gli elementi sulla barra degli strumenti
- **Separatore della barra degli strumenti** -visibile separatore tra due o più elementi della barra degli strumenti per il raggruppamento
- **Personalizzazione della barra degli strumenti** -consente agli utenti di personalizzare la barra degli strumenti
- **Stampa della barra degli strumenti** -consente agli utenti di stampare il documento aperto
- **Mostra i colori della barra degli strumenti** -Visualizza la selezione di colori di sistema standard: 

     ![La selezione di colori di sistema](toolbar-images/edit07.png "la selezione di colori di sistema")

- **Tipo di carattere della barra degli strumenti Mostra** -consente di visualizzare la finestra di dialogo tipo di carattere di sistema standard: 

     ![Il selettore del tipo di carattere](toolbar-images/edit08.png "il selettore del tipo di carattere")

> [!IMPORTANT]
> Come verrà visualizzato in un secondo momento, molti controlli dell'interfaccia utente Cocoa standard, ad esempio campi di ricerca, controlli segmentati e dispositivi di scorrimento orizzontale possono anche essere aggiunti a una barra degli strumenti.

### <a name="adding-an-item-to-a-toolbar"></a>Aggiunta di un elemento a una barra degli strumenti

Per aggiungere un elemento a una barra degli strumenti, selezionare la barra degli strumenti di **interfaccia gerarchia** e fare clic su uno dei relativi elementi, causando la finestra di dialogo di personalizzazione da visualizzare. Trascinare quindi un nuovo elemento dal **controllo libreria** per il **consentiti gli elementi della barra degli strumenti** area:

![Gli elementi della barra degli strumenti è consentito nella finestra di dialogo di personalizzazione della barra degli strumenti](toolbar-images/add01.png "di elementi di barra degli strumenti di consentiti nella finestra di dialogo di personalizzazione della barra degli strumenti")

Per assicurarsi che un nuovo elemento fa parte della barra degli strumenti predefinita, trascinarlo in modo che il **elementi della barra degli strumenti predefiniti** area: 

![Riordinamento di un elemento della barra degli strumenti mediante il trascinamento di](toolbar-images/add02.png "riordinare un elemento della barra degli strumenti mediante il trascinamento")

Per riordinare gli elementi della barra degli strumenti predefinita, è possibile trascinarli a sinistra o destra.

Successivamente, utilizzare il **attributi controllo** per impostare le proprietà predefinite per l'elemento:

![Personalizzazione di un elemento della barra degli strumenti utilizzando il controllo degli attributi](toolbar-images/add03.png "personalizzazione di un elemento della barra degli strumenti utilizzando il controllo degli attributi")

Sono disponibili le proprietà seguenti:

- **Nome immagine** -immagine da utilizzare come icona per l'elemento
- **Etichetta** -il testo da visualizzare per l'elemento nella barra degli strumenti
- **Etichetta tavolozza** -il testo da visualizzare per l'elemento nel **consentiti gli elementi della barra degli strumenti** area
- **Tag** -un identificatore univoco facoltativo che consente di identificare l'elemento di codice.
- **Identificatore** -definisce il tipo di elemento della barra degli strumenti. Selezionare un elemento della barra degli strumenti nel codice, è possibile utilizzare un valore personalizzato.
- **Selezionabile** -se l'opzione è selezionata, l'elemento verrà utilizzato come un pulsante di attivazione/disattivazione.

> [!IMPORTANT]
> Aggiungere un elemento per il **consentiti gli elementi della barra degli strumenti** area ma non la barra degli strumenti predefinita per fornire opzioni di personalizzazione per gli utenti. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Aggiunta di altri controlli dell'interfaccia utente a una barra degli strumenti

Ricerca di elementi dell'interfaccia utente Cocoa diversi, ad esempio campi e controlli segmentati possono anche essere aggiunti a una barra degli strumenti.

Per provare questa, aprire la barra degli strumenti di **interfaccia gerarchia** e selezionare un elemento della barra degli strumenti per aprire la finestra di dialogo di personalizzazione. Trascinare un **campo ricerca** dal **controllo libreria** per il **consentiti gli elementi della barra degli strumenti** area:

![Tramite la finestra di dialogo di personalizzazione della barra degli strumenti](toolbar-images/add05.png "tramite la finestra di dialogo di personalizzazione della barra degli strumenti")

Da qui, utilizzare il generatore di interfaccia per configurare il campo di ricerca e averlo esposto al codice tramite un'azione o di uscita.

## <a name="built-in-toolbar-item-support"></a>Supporto dell'elemento della barra degli strumenti

Diversi elementi dell'interfaccia utente Cocoa interagiscono con gli elementi della barra degli strumenti standard per impostazione predefinita. Ad esempio, trascinare un **visualizzazione testo** nella finestra dell'applicazione e posizionarlo in modo da riempire l'area del contenuto:

[![Aggiunta di una visualizzazione di testo per l'applicazione](toolbar-images/edit09.png "aggiunta di una visualizzazione di testo all'applicazione")](toolbar-images/edit09-large.png#lightbox)

Salvare il documento, torna a Visual Studio per Mac per la sincronizzazione con Xcode, eseguire l'applicazione, immettere il testo, selezionarlo e fare clic su di **colori** della barra degli strumenti. Si noti che la visualizzazione del testo automaticamente funziona con la selezione colori:

![Funzionalità della barra degli strumenti con un pulsante di selezione visualizzazione e il colore del testo](toolbar-images/edit10.png "funzionalità della barra degli strumenti con un pulsante di selezione visualizzazione e il colore del testo")

## <a name="using-images-with-toolbar-items"></a>Utilizzo di immagini con gli elementi della barra degli strumenti

Utilizzando un **immagine della barra degli strumenti**, aggiunta un'immagine bitmap di **risorse** cartella (e assegnato a un'azione di compilazione **Bundle di risorse**) possono essere visualizzati sulla barra degli strumenti come un'icona:

1. In Visual Studio per Mac, nel **soluzione riempimento**, fare doppio clic su di **risorse** cartella e selezionare **Aggiungi** > **Aggiungi file** .
2. Dal **Aggiungi file** finestra di dialogo casella, passare alle immagini desiderate, selezionarli e fare clic su di **aprire** pulsante: 

    [![Selezione di immagini per aggiungere](toolbar-images/edit11.png "selezione di immagini da aggiungere")](toolbar-images/edit11-large.png#lightbox)

3. Selezionare **copia**, controllare **utilizzare la stessa operazione per tutti i file selezionati**, fare clic su **OK**:

    ![Selezionando l'azione di copia per le immagini aggiunte](toolbar-images/edit12.png "selezionando l'azione di copia per le immagini aggiunte")

4. Nel **soluzione riempimento**, fare doppio clic su **MainWindow.xib** per aprirlo in Xcode.

5. Selezionare la barra degli strumenti di **interfaccia gerarchia** e fare clic su uno dei relativi elementi per aprire la finestra di dialogo di personalizzazione.

6. Trascinare un **immagine della barra degli strumenti** dal **controllo libreria** alla barra degli strumenti **consentiti gli elementi della barra degli strumenti** area: 

    ![Un elemento della barra degli strumenti aggiunti all'area gli elementi della barra degli strumenti consentito](toolbar-images/edit14.png "elemento immagine della barra degli strumenti aggiunti all'area consentita gli elementi della barra degli strumenti")

7. Nel **attributi controllo**, selezionare l'immagine che è stato appena aggiunto in Visual Studio per Mac: 

    ![Impostazione di un'immagine personalizzata per un elemento della barra degli strumenti](toolbar-images/edit15.png "l'impostazione di un'immagine personalizzata per un elemento della barra degli strumenti")

8. Impostare il **etichetta** "Cestino" e **etichetta tavolozza** cancellare "documento": 

    ![Impostare la barra degli strumenti elemento tavolozza ed etichetta](toolbar-images/edit16.png "impostando la barra degli strumenti elemento tavolozza ed etichetta")

9. Trascinare un **separatore della barra degli strumenti** dal **controllo libreria** alla barra degli strumenti **consentiti gli elementi della barra degli strumenti** area: 

    [![Un separatore della barra degli strumenti aggiunti all'area gli elementi della barra degli strumenti consentito](toolbar-images/edit17.png "un separatore della barra degli strumenti aggiunti all'area consentita gli elementi della barra degli strumenti")](toolbar-images/edit17-large.png#lightbox)

10. Trascinare l'elemento separatore e l'elemento "Cestino" il **elementi della barra degli strumenti predefiniti** area e impostare l'ordine della barra degli strumenti elementi da sinistra a destra nel modo seguente (i colori, tipi di carattere, separatore, Cestino, spazio flessibile, stampa): 

    ![Gli elementi della barra degli strumenti predefiniti](toolbar-images/edit18.png "gli elementi della barra degli strumenti predefinita")

11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Eseguire l'applicazione per verificare che la nuova barra degli strumenti viene visualizzata per impostazione predefinita:

![Una barra degli strumenti con gli elementi predefiniti personalizzati](toolbar-images/edit19.png "una barra degli strumenti con gli elementi predefiniti personalizzati")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Esposizione di elementi della barra degli strumenti con punti vendita e le azioni

Per accedere a una barra degli strumenti o della barra degli strumenti nel codice, deve essere collegato a una presa di corrente o un'azione:

1. Nel **soluzione riempimento**, fare doppio clic su **Main** per aprirlo in Xcode.
2. Verificare che la classe personalizzata "WindowController" è stato assegnato al controller nella finestra principale di **controllo di identità**:

    [![Tramite il controllo di identità per impostare una classe personalizzata per il controller finestra](toolbar-images/edit20a.png "utilizzando il controllo di identità per impostare una classe personalizzata per il controller di finestra")](toolbar-images/edit20a-large.png#lightbox)

3. Successivamente, selezionare l'elemento della barra degli strumenti nel **interfaccia gerarchia**: 

    ![Selezionando l'elemento della barra degli strumenti nella gerarchia delle interfacce](toolbar-images/edit20.png "selezionando l'elemento della barra degli strumenti nella gerarchia delle interfacce")  

4. Aprire il **visualizzazione Assistente**, selezionare il **WindowController.h** file e trascinamento dell'elemento della barra degli strumenti di controllo il **WindowController.h** file.
5. Impostare il **connessione** tipo **azione**, immettere "trashDocument" per il **nome**, fare clic sul **Connetti** pulsante: 

    [![Configurazione di un'azione per un elemento della barra degli strumenti](toolbar-images/edit23.png "configurazione di un'azione per un elemento della barra degli strumenti")](toolbar-images/edit23-large.png#lightbox)

6. Esporre il **visualizzazione testo** come sbocco chiamato "documentEditor" **ViewController.h** file: 

    [![Configurazione di una presa di corrente per la visualizzazione del testo](toolbar-images/edit24.png "configurazione una presa di corrente per la visualizzazione del testo")](toolbar-images/edit24-large.png#lightbox)

7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

In Visual Studio per Mac, modificare il **ViewController.cs** file e aggiungere il codice seguente:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Successivamente, modificare il **WindowController.cs** file e aggiungere il codice seguente alla fine della `WindowController` classe:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Quando si esegue l'applicazione, il **Cestino** sarà attivo della barra degli strumenti:

![Una barra degli strumenti con un elemento del Cestino di active](toolbar-images/edit25.png "una barra degli strumenti con un elemento del Cestino di active")

Si noti che il **Cestino** della barra degli strumenti può ora essere usato per eliminare il testo.

## <a name="disabling-toolbar-items"></a>La disattivazione degli elementi della barra degli strumenti

Per disabilitare un elemento in una barra degli strumenti, creare un oggetto personalizzato `NSToolbarItem` classe ed eseguire l'override di `Validate` metodo. Quindi, in Generatore di interfaccia, assegnare il tipo personalizzato per l'elemento che si desidera abilitare o disabilitare.

Per creare un oggetto personalizzato `NSToolbarItem` classe del mouse sul progetto e scegliere **Aggiungi** > **nuovo File...** . Selezionare **generale** > **classe vuota**, immettere "ActivatableItem" per il **nome**, fare clic su di **New** pulsante: 

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

Fare doppio clic su **Main** per aprirlo in Xcode. Selezionare il **Cestino** della barra degli strumenti create in precedenza e modificarne la classe in "ActivatableItem" nel **controllo di identità**:

![L'impostazione di una classe personalizzata per un elemento della barra degli strumenti](toolbar-images/custom02.png "l'impostazione di una classe personalizzata per un elemento della barra degli strumenti")

Creare una presa chiamata `trashItem` per il **Cestino** della barra degli strumenti. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode. Infine, aprire **MainWindow.cs** e aggiornare il `AwakeFromNib` metodo come segue:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Eseguire l'applicazione e si noti che il **Cestino** elemento è stato disabilitato nella barra degli strumenti:

![Una barra degli strumenti con un elemento inattivo Cestino](toolbar-images/custom03.png "una barra degli strumenti con un elemento inattivo Cestino")

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di barre degli strumenti e gli elementi della barra degli strumenti in un'applicazione Xamarin.Mac un'analisi approfondita. Descritto come creare e gestire le barre degli strumenti generatore di interfaccia di Xcode, alcuni controlli dell'interfaccia utente automaticamente funzionano con gli elementi della barra degli strumenti, come utilizzare le barre degli strumenti nel codice c# e come abilitare e disabilitare gli elementi della barra degli strumenti.


## <a name="related-links"></a>Collegamenti correlati

- [MacToolbar (esempio)](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Linee guida dell'interfaccia umana per le barre degli strumenti](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introduzione alle barre degli strumenti](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
