---
title: Avvio rapido di storyboard
description: Recupero delle interfacce utente con storyboard macOS compilazione avviata.
ms.topic: article
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 559179d2618ea41bf50362f2e5eb2aa735464b33
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="starting-a-new-storyboard-based-project"></a>Avvio di un nuovo Storyboard progetto basato su

Come una rapida introduzione all'uso di storyboard per definire l'interfaccia utente dell'applicazione un Xamarin.Mac, iniziamo un nuovo progetto Xamarin.Mac. Selezionare **Mac** > **App** > **App Cocoa** e fare clic sul pulsante **Avanti**:

[ ![](quickstart-images/qs01.png "Aggiunta di una nuova App Cocoa")](quickstart-images/qs01.png)

Utilizzare il **nome App** di `MacStoryboard` e fare clic su di **Avanti** pulsante:

[ ![](quickstart-images/qs02.png "Impostazione del nome dell'applicazione")](quickstart-images/qs02.png)

Utilizzare il valore predefinito **nome progetto** e **Nome soluzione** e fare clic su di **crea** pulsante:

[ ![](quickstart-images/qs03.png "I nomi di progetto e soluzione")](quickstart-images/qs03.png)

Nel **Esplora**, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore del Xcode di interfaccia:

[ ![](quickstart-images/qs04.png "Modifica lo storyboard in Xcode")](quickstart-images/qs04.png)

Come può notare in precedenza, il valore predefinito di Storyboard definisce sia la barra dei Menu dell'applicazione e la relativa finestra principale è View Controller e visualizzazione. Per l'applicazione di esempio, verrà creata un'interfaccia utente che dispone di una funzione main _visualizzazione contenuto_ su un lato e un _visualizzazione controllo_ al secondo.

A tale scopo, è necessario prima rimuovere il valore predefinito, visualizzazione Controller e vista che viene fornito con lo Storyboard per selezionarlo nel generatore di interfaccia e premendo il **eliminare** chiave:

[ ![](quickstart-images/qs05.png "La rimozione del controller di visualizzazione predefinito")](quickstart-images/qs05.png)

Digitare quindi `split` nel **filtro** area, selezionare il Controller di visualizzazione di divisione verticale e trascinarlo il _area di progettazione_:

[ ![](quickstart-images/qs06.png "Ricerca per il controller di visualizzazione divisa")](quickstart-images/qs06.png)

Si noti che il controller viene incluso automaticamente figlio due controller di visualizzazione e le viste correlate, reti cablate-fino ai lati sinistro e destro della visualizzazione suddivisa. Per associare la visualizzazione suddivisa per la finestra padre, premere il **controllo** chiave fare clic con il Controller di finestra (vale a dire il cerchio blu nel frame del Controller finestra) e trascinare una linea al Controller di visualizzazione di divisione. Selezionare **contenuto della finestra** dalla finestra popup:

[ ![](quickstart-images/qs07.png "Impostazione di windows, visualizzazione contenuto")](quickstart-images/qs07.png)

Verrà associata all'elemento del due interfaccia tra loro tramite un Segue:

[ ![](quickstart-images/qs08.png "La Segue tra la finestra e il contenuto")](quickstart-images/qs08.png)

Si desidera inserire una visualizzazione di testo nel lato sinistro della visualizzazione suddivisa e fare in modo automaticamente riempire l'area disponibile quando si ridimensiona la finestra o la visualizzazione suddivisa. Trascinare una visualizzazione di testo nella parte superiore View Controller collegato alla visualizzazione divisa e fare clic su di **Pin** automaticamente il vincolo di layout (la seconda icona da destra nella parte inferiore dell'area di progettazione).

[ ![](quickstart-images/qs09.png "Configurazione dei vincoli di")](quickstart-images/qs09.png)

Da qui si potrà fare clic su tutti e quattro le **cursore ai** icone il rettangolo di selezione nella parte superiore di Popover vincoli e scegliere il **aggiungere vincoli di 4** nella parte inferiore per aggiungere i vincoli necessari.

Se si tornare a Visual Studio per Mac ed eseguire il progetto, vengono ridimensionate si noti che la visualizzazione di testo viene ridimensionato automaticamente per riempire il lato sinistro della visualizzazione suddivisa come la finestra o la divisione:

[ ![](quickstart-images/qs10.png "Un esempio di app in esecuzione")](quickstart-images/qs10.png)

Poiché verrà utilizzato il lato destro della visualizzazione suddivisa come un'area di controllo, si desidera avere dimensioni inferiori e consentono di comprimere. Tornare a Xcode e modificare la visualizzazione per il lato destro selezionandolo nell'area di progettazione e scegliendo il **dimensioni controllo**. Da qui è possibile immettere un **larghezza** di `250`:

[ ![](quickstart-images/qs11.png "Impostazione della larghezza")](quickstart-images/qs11.png)

Impostare un livello più elevato successivo selezionare l'elemento di divisione che rappresenta il lato destro, **tenendo priorità** e fare clic sul **utente può comprimere** casella di controllo:

[ ![](quickstart-images/qs12.png "Modifica della priorità di azienda")](quickstart-images/qs12.png)

Se si tornare a Visual Studio per Mac ed esegue il progetto a questo punto, si noti che il lato destro mantiene sono minori dimensioni e la finestra viene ridimensionata:

[ ![](quickstart-images/qs13.png "Un esempio di app in esecuzione")](quickstart-images/qs13.png)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definire la definizione di una presentazione

È possibile passare al layout il lato destro della visualizzazione divisa per agire come un controllo per le proprietà del testo selezionato. Si verranno trascinare alcuni controlli vista inferiore per l'interfaccia utente del controllo di layout. Per l'ultimo controllo, si desidera visualizzare un popover che consente all'utente di selezionare da quattro stili di carattere predefiniti.

Verrà aggiunto un pulsante per il controllo e un Controller di visualizzazione nell'area di progettazione. È necessario ridimensionare il Controller di visualizzazione per la dimensione che si desidera il nostro Popover e aggiungere i quattro pulsanti. Successivamente, ti invieremo **controllo** chiave fare clic sul pulsante nella visualizzazione controllo e trascinare il Controller di visualizzazione che rappresenterà il nostro popover:

[ ![](quickstart-images/qs14.png "Trascinamento per creare un nuovo segue")](quickstart-images/qs14.png)

Dal menu di scelta rapida, si selezionerà **Popover**: 

[ ![](quickstart-images/qs15.png "Selezione del tipo segue")](quickstart-images/qs15.png)

Infine, viene selezionare la Segue nell'area di progettazione e impostare il **preferito Edge** a **sinistra**. È possibile trascinare quindi una riga dal **visualizzazione ancoraggio** al pulsante da popover a:

[ ![](quickstart-images/qs16.png "Trascinamento per creare un nuovo segue")](quickstart-images/qs16.png)

Se si tornare a Visual Studio per Mac, eseguire l'app e fare clic su di **Nessuno** pulsante del controllo, popover verrà visualizzato:

[ ![](quickstart-images/qs17.png "Un esempio del segue in esecuzione")](quickstart-images/qs17.png)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Creazione di App preferenze

Forniscono la maggior parte delle App macOS un _finestra di dialogo Preferenze_ che consente all'utente di definire diverse opzioni che consentono di controllare vari aspetti dell'app, ad esempio gli account utente o l'aspetto.

Per definire una finestra di dialogo standard di preferenza, trascinare una scheda visualizzazione Controller nell'area di progettazione:

[ ![](quickstart-images/qs18.png "Modifica lo storyboard in Xcode")](quickstart-images/qs18.png)

Nuovamente, questo verrà fornito automaticamente con figlio due controller di visualizzazione associato. Ad esempio i migliori risultati, si aggiungerà un'etichetta a ogni vista che verrà center all'interno:

[ ![](quickstart-images/qs19.png "Impostazione di vincoli")](quickstart-images/qs19.png)

Successivamente, si desidera visualizzare la finestra Preferenze quando l'utente seleziona il **preferenze...**  voce di menu. Dalla barra dei Menu, selezionare la voce di menu Preferenze **controllo** chiave clic e trascinare una linea per la scheda visualizzazione Controller:

[ ![](quickstart-images/qs20.png "Trascinamento per creare un segue")](quickstart-images/qs20.png)

Dalla finestra popup, si selezionerà **modale** per visualizzare questa finestra come una finestra di dialogo modale:

[ ![](quickstart-images/qs21.png "Selezione del tipo segue")](quickstart-images/qs21.png)

Se si salvano le modifiche, tornare a Visual Studio per Mac, eseguire l'app e selezionare il **preferenze...**  voce di menu, il nostro nuove preferenze verrà visualizzata una finestra di dialogo:

[ ![](quickstart-images/qs22.png "Un esempio del segue in esecuzione")](quickstart-images/qs22.png)

È possibile notare che questo non sembra una finestra di dialogo delle preferenze di app macOS standard. Per risolvere questo problema, includere due file di immagine dell'app Xamarin.Mac `Resources` cartella la **Esplora** e tornare al generatore di Xcode interfaccia.

Selezionare la scheda Visualizza Controller e il commutatore relativo **stile** a **barra degli strumenti**: 

[ ![](quickstart-images/qs23.png "Impostare lo stile della barra della scheda")](quickstart-images/qs23.png)

Selezionare ogni scheda e assegnarle un **etichetta** e selezionare una delle immagini per rappresentare tale:

[ ![](quickstart-images/qs24.png "Configurazione di ogni scheda in Xcode")](quickstart-images/qs24.png)

Se si salvano le modifiche, tornare a Visual Studio per Mac, eseguire l'app e selezionare il **preferenze...**  voce di menu, la finestra di dialogo verrà visualizzato come un'app macOS standard:

[ ![](quickstart-images/qs25.png "Un esempio di finestra Preferenze in esecuzione")](quickstart-images/qs25.png)

Per ulteriori informazioni, vedere il nostro [utilizzo delle immagini](~/mac/app-fundamentals/image.md), [menu](~/mac/user-interface/menu.md), [Windows](~/mac/user-interface/window.md) e [finestre di dialogo](~/mac/user-interface/dialog.md) documentazione.

## <a name="related-links"></a>Collegamenti correlati

- [MacStoryboard (esempio)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida sull'interfaccia umane di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
