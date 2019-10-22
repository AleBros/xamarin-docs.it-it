---
title: Barre degli strumenti in Novell. Mac
description: Questo articolo descrive l'uso delle barre degli strumenti in un'applicazione Novell. Mac. Viene illustrata la creazione e la gestione delle barre degli strumenti in Xcode e Interface Builder, l'esposizione al codice e l'utilizzo a livello di codice.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: cd2490bfad880d128f5eaeebd4aac58ad3a4d8fa
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70772716"
---
# <a name="toolbars-in-xamarinmac"></a>Barre degli strumenti in Novell. Mac

_Questo articolo descrive l'uso delle barre degli strumenti in un'applicazione Novell. Mac. Viene illustrata la creazione e la gestione delle barre degli strumenti in Xcode e Interface Builder, l'esposizione al codice e l'utilizzo a livello di codice._

Gli sviluppatori Novell. Mac che lavorano con Visual Studio per Mac hanno accesso agli stessi controlli dell'interfaccia utente disponibili per gli sviluppatori macOS che lavorano con Xcode, incluso il controllo Toolbar. Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare Interface Builder di Xcode per creare e gestire gli elementi della barra degli strumenti. È anche possibile creare questi elementi della barra C#degli strumenti in.

Le barre degli strumenti in macOS vengono aggiunte alla sezione superiore di una finestra e consentono di accedere facilmente ai comandi correlati alle relative funzionalità. Le barre degli strumenti possono essere nascoste, visualizzate o personalizzate dagli utenti di un'applicazione e possono presentare elementi della barra degli strumenti in vari modi.

Questo articolo illustra le nozioni di base sull'uso delle barre degli strumenti e degli elementi della barra degli strumenti in un'applicazione Novell. Mac. 

Prima di continuare, leggere l'articolo relativo ad [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) le sezioni relative a Interface Builder, [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti e le tecniche principali che verranno usati in questa guida.

Vedere anche la sezione [esporre C# classi/metodi a Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) . Vengono illustrati gli attributi `Register` e `Export` utilizzati per C# connettere le classi alle classi Objective-C.

## <a name="introduction-to-toolbars"></a>Introduzione alle barre degli strumenti

Qualsiasi finestra in un'applicazione macOS può includere una barra degli strumenti:

![Finestra di esempio con una barra degli strumenti](toolbar-images/info01.png "Finestra di esempio con una barra degli strumenti")

Le barre degli strumenti offrono agli utenti dell'applicazione un modo semplice per accedere rapidamente alle funzionalità importanti o usate di frequente. Ad esempio, un'applicazione per la modifica di documenti può fornire elementi della barra degli strumenti per impostare il colore del testo, modificare il tipo di carattere o stampare il documento corrente.

Le barre degli strumenti possono visualizzare gli elementi in tre modi:

1. **Icona e testo** 

     ![Barra degli strumenti con icone e testo](toolbar-images/info02.png "Barra degli strumenti con icone e testo")

2. **Solo icona** 

     ![Barra degli strumenti solo icona](toolbar-images/info03.png "Barra degli strumenti solo icona")

3. **Solo testo** 

     ![Barra degli strumenti solo testo](toolbar-images/info04.png "Barra degli strumenti solo testo")

Passare da una modalità all'altra facendo clic con il pulsante destro del mouse sulla barra degli strumenti e selezionando una modalità di visualizzazione dal menu contestuale:

![Menu contestuale per una barra degli strumenti](toolbar-images/info05.png "Menu contestuale per una barra degli strumenti")

Utilizzare lo stesso menu per visualizzare la barra degli strumenti a una dimensione più piccola:

![Barra degli strumenti con icone piccole](toolbar-images/info06.png "Barra degli strumenti con icone piccole")

Il menu consente inoltre di personalizzare la barra degli strumenti:

![Finestra di dialogo utilizzata per personalizzare una barra degli strumenti](toolbar-images/info07.png "Finestra di dialogo utilizzata per personalizzare una barra degli strumenti")

Quando si configura una barra degli strumenti nel Interface Builder di Xcode, uno sviluppatore può fornire elementi aggiuntivi della barra degli strumenti che non fanno parte della configurazione predefinita. Gli utenti dell'applicazione possono quindi personalizzare la barra degli strumenti, aggiungendo e rimuovendo tali elementi predefiniti, se necessario. Naturalmente, è possibile ripristinare la configurazione predefinita della barra degli strumenti.

La barra degli strumenti si connette automaticamente al menu **Visualizza** , che consente agli utenti di nasconderlo, visualizzarlo e personalizzarlo:

![Elementi correlati alla barra degli strumenti nel menu Visualizza](toolbar-images/info08.png "Elementi correlati alla barra degli strumenti nel menu Visualizza")

Per ulteriori informazioni, vedere la documentazione della [funzionalità di menu incorporata](~/mac/user-interface/menu.md) .

Inoltre, se la barra degli strumenti è configurata correttamente in Interface Builder, l'applicazione mantiene automaticamente le personalizzazioni della barra degli strumenti tra più avvii dell'applicazione.

Nelle sezioni successive di questa guida viene descritto come creare e gestire le barre degli strumenti con Interface Builder di Xcode e come utilizzarle nel codice.

## <a name="setting-a-custom-main-window-controller"></a>Impostazione di un controller della finestra principale personalizzato

Per esporre gli elementi dell' C# interfaccia utente al codice tramite Outlet e azioni, l'app Novell. Mac deve usare un controller di finestra personalizzato:

1. Aprire lo storyboard dell'app nella Interface Builder di Xcode.
2. Selezionare il controller della finestra nell'area di progettazione.
3. Passare a **Identity Inspector** e immettere "WindowController" come nome della **classe**: 

    [![Impostazione di un nome di classe personalizzato per il controller della finestra](toolbar-images/windowcontroller01.png "Impostazione di un nome di classe personalizzato per il controller della finestra")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Un file **WindowController.cs** verrà aggiunto al progetto nella **riquadro della soluzione** Visual Studio per Mac: 

    ![Selezione di WindowController.cs nel riquadro della soluzione](toolbar-images/windowcontroller02.png "Selezione di WindowController.cs nel riquadro della soluzione")

6. Riaprire lo storyboard nella Interface Builder di Xcode.
7. Il file **WindowController. h** sarà disponibile per l'uso: 

    [![Il file WindowController. h](toolbar-images/windowcontroller03.png "Il file WindowController. h")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Creazione e gestione delle barre degli strumenti in Xcode

Le barre degli strumenti vengono create e gestite con la Interface Builder di Xcode. Per aggiungere una barra degli strumenti a un'applicazione, modificare lo storyboard principale dell'app (in questo caso **Main. Storyboard**) facendo doppio clic su di essa nella **riquadro della soluzione**:

![Apertura di Main. Storyboard nel riquadro della soluzione](toolbar-images/edit01.png "Apertura di Main. Storyboard nel riquadro della soluzione")

In **Library Inspector**immettere "Tool" nella **casella di ricerca** per semplificare la visualizzazione di tutti gli elementi disponibili della barra degli strumenti:

![Controllo libreria, filtrato per mostrare gli elementi della barra degli strumenti](toolbar-images/edit02.png "Controllo libreria, filtrato per mostrare gli elementi della barra degli strumenti")

Trascinare una barra degli strumenti sulla finestra dell' **editor di interfaccia**. Con la barra degli strumenti selezionata, configurare il comportamento impostando le proprietà nel **controllo attributi**:

![Controllo attributi per una barra degli strumenti](toolbar-images/edit04.png "Controllo attributi per una barra degli strumenti")

Sono disponibili le proprietà seguenti:

1. **Display** : controlla se la barra degli strumenti Visualizza icone, testo o entrambi
2. **Visibile all'avvio** : se selezionato, la barra degli strumenti è visibile per impostazione predefinita.
3. **Personalizzabile** : se questa opzione è selezionata, gli utenti possono modificare e personalizzare la barra degli strumenti.
4. **Separatore** : se questa opzione è selezionata, una linea orizzontale sottile separa la barra degli strumenti dal contenuto della finestra.
5. **Size** : imposta la dimensione della barra degli strumenti
6. **Salvataggio automatico** : se questa opzione è selezionata, l'applicazione renderà permanente le modifiche di configurazione della barra degli strumenti dell'utente tra l'avvio dell'applicazione.

Selezionare l'opzione **salvataggio automatico** e lasciare le impostazioni predefinite per tutte le altre proprietà. 

Dopo aver aperto la barra degli strumenti nella **gerarchia dell'interfaccia**, visualizzare la finestra di dialogo di personalizzazione selezionando un elemento della barra degli strumenti:

![Personalizzazione della barra degli strumenti](toolbar-images/edit05.png "Personalizzazione della barra degli strumenti")

Utilizzare questa finestra di dialogo per impostare le proprietà per gli elementi che fanno già parte della barra degli strumenti, per progettare la barra degli strumenti predefinita per l'applicazione e per fornire ulteriori elementi della barra degli strumenti che un utente deve selezionare durante la personalizzazione della barra degli strumenti. Per aggiungere elementi alla barra degli strumenti, trascinarli da **controllo libreria**:

![Controllo libreria](toolbar-images/edit06.png "Controllo libreria")

È possibile aggiungere i seguenti elementi della barra degli strumenti:

- **Elemento barra degli strumenti immagine** : una voce della barra degli strumenti con un'immagine personalizzata come icona.
- **Elemento barra degli strumenti spazio flessibile** : spazio flessibile usato per giustificare gli elementi successivi della barra degli strumenti. Ad esempio, uno o più elementi della barra degli strumenti seguiti da un elemento barra degli strumenti spazio flessibile e un altro elemento della barra degli strumenti aggiungeranno l'ultimo elemento sul lato destro della barra degli strumenti.
- **Elemento barra degli strumenti spazio** -spazio fisso tra gli elementi sulla barra degli strumenti
- **Elemento barra degli strumenti separatore** : un separatore visibile tra due o più elementi della barra degli strumenti per il raggruppamento
- **Personalizza elemento barra degli strumenti** : consente agli utenti di personalizzare la barra degli strumenti
- **Elemento della barra degli strumenti di stampa** : consente agli utenti di stampare il documento aperto
- **Mostra elemento della barra degli strumenti colori** -Visualizza la selezione colori del sistema standard: 

     ![Selezione colori di sistema](toolbar-images/edit07.png "Selezione colori di sistema")

- **Mostra elemento della barra degli strumenti carattere** -Visualizza la finestra di dialogo standard del tipo di carattere: 

     ![Selettore del tipo di carattere](toolbar-images/edit08.png "Selettore del tipo di carattere")

> [!IMPORTANT]
> Come si verificherà in un secondo momento, molti controlli dell'interfaccia utente di Cocoa standard, ad esempio campi di ricerca, controlli segmentati e cursori orizzontali, possono essere aggiunti anche a una barra degli strumenti.

### <a name="adding-an-item-to-a-toolbar"></a>Aggiunta di un elemento a una barra degli strumenti

Per aggiungere un elemento a una barra degli strumenti, selezionare la barra degli strumenti nella **gerarchia dell'interfaccia** e fare clic su uno degli elementi, facendo in modo che venga visualizzata la finestra di dialogo di personalizzazione. Trascinare quindi un nuovo elemento da **controllo libreria** all'area elementi della **barra degli strumenti consentiti** :

![Gli elementi consentiti della barra degli strumenti nella finestra di dialogo di personalizzazione](toolbar-images/add01.png "Gli elementi consentiti della barra degli strumenti nella finestra di dialogo di personalizzazione")

Per assicurarsi che un nuovo elemento faccia parte della barra degli strumenti predefinita, trascinarlo nell'area **elementi della barra degli strumenti predefiniti** : 

![Riordinamento di un elemento della barra degli strumenti mediante trascinamento](toolbar-images/add02.png "Riordinamento di un elemento della barra degli strumenti mediante trascinamento")

Per riordinare gli elementi predefiniti della barra degli strumenti, trascinarli a sinistra o a destra.

Usare quindi il **controllo attributi** per impostare le proprietà predefinite per l'elemento:

![Personalizzazione di un elemento della barra degli strumenti tramite il controllo attributi](toolbar-images/add03.png "Personalizzazione di un elemento della barra degli strumenti tramite il controllo attributi")

Sono disponibili le proprietà seguenti:

- **Nome immagine** : immagine da usare come icona per l'elemento
- **Etichetta** -testo da visualizzare per l'elemento nella barra degli strumenti
- **Etichetta tavolozza** -testo da visualizzare per l'elemento nell'area **elementi della barra degli strumenti consentiti**
- **Tag** : identificatore univoco facoltativo che consente di identificare l'elemento nel codice.
- **Identificatore** : definisce il tipo di elemento della barra degli strumenti. Per selezionare un elemento della barra degli strumenti nel codice, è possibile utilizzare un valore personalizzato.
- **Selezionabile** : se questa opzione è selezionata, l'elemento fungerà da pulsante di abilitazione/disattivazione.

> [!IMPORTANT]
> Aggiungere un elemento all'area **elementi della barra degli strumenti consentiti** , ma non alla barra degli strumenti predefinita, per fornire opzioni di personalizzazione per gli utenti. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Aggiunta di altri controlli dell'interfaccia utente a una barra degli strumenti

È anche possibile aggiungere diversi elementi dell'interfaccia utente di Cocoa, ad esempio campi di ricerca e controlli segmentati, a una barra degli strumenti.

Per provare questa operazione, aprire la barra degli strumenti nella **gerarchia dell'interfaccia** e selezionare una voce della barra degli strumenti per aprire la finestra di dialogo di personalizzazione. Trascinare un **campo di ricerca** dal **controllo libreria** all'area **elementi della barra degli strumenti consentiti** :

![Uso della finestra di dialogo di personalizzazione della barra degli strumenti](toolbar-images/add05.png "Uso della finestra di dialogo di personalizzazione della barra degli strumenti")

Da qui, usare Interface Builder per configurare il campo di ricerca ed esporlo al codice tramite un'azione o un'uscita.

## <a name="built-in-toolbar-item-support"></a>Supporto elementi della barra degli strumenti incorporati

Per impostazione predefinita, diversi elementi dell'interfaccia utente Cocoa interagiscono con gli elementi della barra degli strumenti Ad esempio, trascinare una **visualizzazione di testo** nella finestra dell'applicazione e posizionarla in modo da riempire l'area di contenuto:

[![Aggiunta di una visualizzazione di testo all'applicazione](toolbar-images/edit09.png "Aggiunta di una visualizzazione di testo all'applicazione")](toolbar-images/edit09-large.png#lightbox)

Salvare il documento, tornare a Visual Studio per Mac per sincronizzarlo con Xcode, eseguire l'applicazione, immettere testo, selezionarlo e fare clic sull'elemento della barra degli strumenti **colori** . Si noti che la visualizzazione di testo funziona automaticamente con la selezione colori:

![Funzionalità della barra degli strumenti incorporata con una visualizzazione di testo e una selezione colori](toolbar-images/edit10.png "Funzionalità della barra degli strumenti incorporata con una visualizzazione di testo e una selezione colori")

## <a name="using-images-with-toolbar-items"></a>Utilizzo di immagini con elementi della barra degli strumenti

Utilizzando un **elemento della barra degli strumenti immagine**, qualsiasi immagine bitmap aggiunta alla cartella **risorse** (e in base a un'azione di compilazione della **risorsa bundle**) può essere visualizzata sulla barra degli strumenti come icona:

1. In Visual Studio per Mac, nella **riquadro della soluzione**, fare clic con il pulsante destro del mouse sulla cartella **risorse** e scegliere **Aggiungi**  > **Aggiungi file**.
2. Nella finestra di dialogo **Aggiungi file** individuare le immagini desiderate, selezionarle e fare clic sul pulsante **Apri** : 

    [![Selezione di immagini da aggiungere](toolbar-images/edit11.png "Selezione di immagini da aggiungere")](toolbar-images/edit11-large.png#lightbox)

3. Selezionare **copia**, selezionare **Usa la stessa azione per tutti i file selezionati**e fare clic su **OK**:

    ![Selezione dell'azione di copia per le immagini aggiunte](toolbar-images/edit12.png "Selezione dell'azione di copia per le immagini aggiunte")

4. Nella **riquadro della soluzione**fare doppio clic su **MainWindow. xib** per aprirlo in Xcode.

5. Selezionare la barra degli strumenti nella **gerarchia dell'interfaccia** e fare clic su uno degli elementi per aprire la finestra di dialogo di personalizzazione.

6. Trascinare un **elemento della barra degli strumenti immagine** dal **controllo libreria** all'area **elementi della barra degli strumenti consentiti** della barra degli strumenti: 

    ![Elemento della barra degli strumenti immagine aggiunto all'area elementi della barra degli strumenti consentiti](toolbar-images/edit14.png "Elemento della barra degli strumenti immagine aggiunto all'area elementi della barra degli strumenti consentiti")

7. Nel **controllo attributi**selezionare l'immagine appena aggiunta in Visual Studio per Mac: 

    ![Impostazione di un'immagine personalizzata per un elemento della barra degli strumenti](toolbar-images/edit15.png "Impostazione di un'immagine personalizzata per un elemento della barra degli strumenti")

8. Impostare l' **etichetta** su "Cestino" e l' **etichetta della tavolozza** su "Cancella documento": 

    ![Impostazione dell'etichetta dell'elemento della barra degli strumenti e dell'etichetta tavolozza](toolbar-images/edit16.png "Impostazione dell'etichetta dell'elemento della barra degli strumenti e dell'etichetta tavolozza")

9. Trascinare un **elemento della barra degli strumenti separatore** dal **controllo libreria** all'area elementi della barra degli strumenti **consentiti** della barra degli strumenti: 

    [![Elemento barra degli strumenti separatore aggiunto all'area elementi della barra degli strumenti consentiti](toolbar-images/edit17.png "Elemento barra degli strumenti separatore aggiunto all'area elementi della barra degli strumenti consentiti")](toolbar-images/edit17-large.png#lightbox)

10. Trascinare l'elemento separatore e l'elemento "Cestino" sull'area **elementi della barra degli strumenti predefinita** e impostare l'ordine degli elementi della barra degli strumenti da sinistra a destra, come indicato di seguito (colori, tipi di carattere, separatore, cestino, spazio flessibile, stampa): 

    ![Elementi predefiniti della barra degli strumenti](toolbar-images/edit18.png "Elementi predefiniti della barra degli strumenti")

11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Eseguire l'applicazione per verificare che la nuova barra degli strumenti sia visualizzata per impostazione predefinita:

![Barra degli strumenti con elementi predefiniti personalizzati](toolbar-images/edit19.png "Barra degli strumenti con elementi predefiniti personalizzati")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Esposizione degli elementi della barra degli strumenti con Outlet e azioni

Per accedere a una barra degli strumenti o a un elemento della barra degli strumenti nel codice, è necessario collegarlo a un'uscita o a un'azione:

1. Nella **riquadro della soluzione**fare doppio clic su **Main. Storyboard** per aprirlo in Xcode.
2. Verificare che la classe personalizzata "WindowController" sia stata assegnata al controller della finestra principale nel controllo di **identità**:

    [![Uso di Identity Inspector per impostare una classe personalizzata per il controller della finestra](toolbar-images/edit20a.png "Uso di Identity Inspector per impostare una classe personalizzata per il controller della finestra")](toolbar-images/edit20a-large.png#lightbox)

3. Selezionare quindi l'elemento della barra degli strumenti nella **gerarchia dell'interfaccia**: 

    ![Selezione dell'elemento della barra degli strumenti nella gerarchia dell'interfaccia](toolbar-images/edit20.png "Selezione dell'elemento della barra degli strumenti nella gerarchia dell'interfaccia")  

4. Aprire la **visualizzazione assistente**, selezionare il file **WindowController. h** e trascinare il controllo dalla voce della barra degli strumenti al file **WindowController. h** .
5. Impostare tipo di **connessione** su **azione**, immettere "trashDocument" come **nome**e fare clic sul pulsante **Connetti** : 

    [![Configurazione di un'azione per un elemento della barra degli strumenti](toolbar-images/edit23.png "Configurazione di un'azione per un elemento della barra degli strumenti")](toolbar-images/edit23-large.png#lightbox)

6. Esporre la **visualizzazione di testo** come un Outlet denominato "documentEditor" nel file **ViewController. h** : 

    [![Configurazione di un outlet per la visualizzazione di testo](toolbar-images/edit24.png "Configurazione di un outlet per la visualizzazione di testo")](toolbar-images/edit24-large.png#lightbox)

7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

In Visual Studio per Mac modificare il file **ViewController.cs** e aggiungere il codice seguente:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Modificare quindi il file **WindowController.cs** e aggiungere il codice seguente alla fine della classe `WindowController`:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Quando si esegue l'applicazione, l'elemento della barra degli strumenti del **Cestino** sarà attivo:

![Barra degli strumenti con un elemento del cestino attivo](toolbar-images/edit25.png "Barra degli strumenti con un elemento del cestino attivo")

Si noti che è ora possibile usare l'elemento della barra degli strumenti del **Cestino** per eliminare il testo.

## <a name="disabling-toolbar-items"></a>Disabilitazione degli elementi della barra degli strumenti

Per disabilitare un elemento su una barra degli strumenti, creare una classe di `NSToolbarItem` personalizzata ed eseguire l'override del metodo `Validate`. Quindi, in Interface Builder assegnare il tipo personalizzato all'elemento che si desidera abilitare o disabilitare.

Per creare una classe di `NSToolbarItem` personalizzata, fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi**  > **nuovo file.** Selezionare **generale**  > **classe vuota**, immettere "ActivatableItem" come **nome**e fare clic sul pulsante **nuovo** : 

![Aggiunta di una classe vuota in Visual Studio per Mac](toolbar-images/custom01.png "Aggiunta di una classe vuota in Visual Studio per Mac")

Modificare quindi il file **ActivatableItem.cs** per leggere come segue:

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

Fare doppio clic su **Main. Storyboard** per aprirlo in Xcode. Selezionare l'elemento della barra degli strumenti del **Cestino** creato in precedenza e impostare la relativa classe su "ActivatableItem" in **Identity Inspector**:

![Impostazione di una classe personalizzata per un elemento della barra degli strumenti](toolbar-images/custom02.png "Impostazione di una classe personalizzata per un elemento della barra degli strumenti")

Creare un Outlet denominato `trashItem` per l'elemento della barra degli strumenti del **Cestino** . Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode. Infine, aprire **MainWindow.cs** e aggiornare il metodo `AwakeFromNib` per leggere come indicato di seguito:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Eseguire l'applicazione e notare che l'elemento del **Cestino** è ora disabilitato nella barra degli strumenti:

![Barra degli strumenti con un elemento cestino inattivo](toolbar-images/custom03.png "Barra degli strumenti con un elemento cestino inattivo")

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso delle barre degli strumenti e degli elementi della barra degli strumenti in un'applicazione Novell. Mac. Viene descritto come creare e gestire le barre degli strumenti in Interface Builder di Xcode, come alcuni controlli dell'interfaccia utente funzionano automaticamente con gli elementi della barra degli strumenti, C# come usare le barre degli strumenti nel codice e come abilitare e disabilitare gli elementi della barra degli strumenti.

## <a name="related-links"></a>Collegamenti correlati

- [MacToolbar (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactoolbar)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Linee guida per l'interfaccia umana per le barre degli strumenti](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introduzione alle barre degli strumenti](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
