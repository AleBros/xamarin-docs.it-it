---
title: Storyboard in xamarin. Mac-avvio rapido
description: Questo documento fornisce un'introduzione rapida alla creazione di interfacce utente con gli storyboard in xamarin. Mac macOS. Viene descritto come creare un elemento segue e creare una finestra Preferenze.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7f7d23a01a3c3c6567d6bab45d0abbfb078fb512
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112572"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Storyboard in xamarin. Mac-avvio rapido

Come una rapida introduzione all'uso degli storyboard per definire l'interfaccia utente di un'app xamarin. Mac, è possibile avviare un nuovo progetto xamarin. Mac. Selezionare **Mac** > **App** > **App Cocoa** e fare clic sul pulsante **Avanti**:

[![](quickstart-images/qs01.png "Aggiunge una nuova App Cocoa")](quickstart-images/qs01.png#lightbox)

Usare il **nome dell'App** dei `MacStoryboard` e fare clic sul **successivo** pulsante:

[![](quickstart-images/qs02.png "Impostare il nome dell'App")](quickstart-images/qs02.png#lightbox)

Usare il valore predefinito **nome progetto** e **Nome soluzione** e fare clic sui **Create** pulsante:

[![](quickstart-images/qs03.png "I nomi di progetto e soluzione")](quickstart-images/qs03.png#lightbox)

Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder di Xcode:

[![](quickstart-images/qs04.png "Modifica lo storyboard in Xcode")](quickstart-images/qs04.png#lightbox)

Come può notare, il valore predefinito dello Storyboard definisce barra dei Menu dell'app sia la finestra principale con il Controller di visualizzazione e visualizzazione. Per l'app di esempio, si userà come la creazione di un'interfaccia utente che dispone di una funzione main _visualizzazione contenuto_ sul lato "uno" e un _visualizzazione Inspector_ nella seconda.

A tale scopo, è necessario innanzitutto rimuovere il Controller di visualizzazione predefinito e visualizzazione fornita con lo Storyboard per selezionarlo in Interface Builder e premendo il **eliminare** chiave:

[![](quickstart-images/qs05.png "Rimuovere il controller di visualizzazione predefinito")](quickstart-images/qs05.png#lightbox)

Successivamente, digitare `split` nella **filtro** area, selezionare il Controller doppia visualizzazione verticale e trascinarla nel _nell'area di progettazione_:

[![](quickstart-images/qs06.png "Cercare il controller di visualizzazione suddivisa")](quickstart-images/qs06.png#lightbox)

Si noti che il controller incluso automaticamente figlio due controller di visualizzazione (e le visualizzazioni correlate), reti cablate-fino ai lati sinistro e destro della visualizzazione suddivisa. Per associare la doppia visualizzazione alla relativa finestra padre, premere il **controllo** chiave fare clic con il Controller di finestra (cerchio blu nel frame di finestra del Controller) e trascinare una linea per il Controller doppia visualizzazione. Selezionare **contenuto della finestra** dal popup della:

[![](quickstart-images/qs07.png "L'impostazione di visualizzazione di contenuto di windows")](quickstart-images/qs07.png#lightbox)

Verrà associata all'elemento del due interfaccia tra loro tramite un elemento Segue:

[![](quickstart-images/qs08.png "L'elemento Segue tra la finestra e il contenuto")](quickstart-images/qs08.png#lightbox)

Si vuole posizionare una visualizzazione di testo nel lato sinistro della visualizzazione suddivisa ed è riempire automaticamente l'area disponibile quando si ridimensiona la finestra o la doppia visualizzazione. Trascinare una visualizzazione di testo in alto View Controller collegato alla visualizzazione divisa e scegliere il **Pin** auto vincolo layout (la seconda icona da destra nella parte inferiore dell'area di progettazione).

[![](quickstart-images/qs09.png "Configurare i vincoli")](quickstart-images/qs09.png#lightbox)

A questo punto si saranno fare clic su tutti e quattro le **cursore ai** icone tutto il rettangolo di selezione finestra all'inizio del Popover vincoli e fare clic sui **Aggiungi 4 vincoli** nella parte inferiore per aggiungere i vincoli necessari.

Se è tornare a Visual Studio per Mac e si esegue il progetto, si noti che la visualizzazione di testo viene ridimensionato automaticamente per riempire il lato sinistro della visualizzazione suddivisa come la finestra o la divisione vengono ridimensionati:

[![](quickstart-images/qs10.png "Un esempio di app in esecuzione")](quickstart-images/qs10.png#lightbox)

Poiché che si prevede di usare il lato destro della visualizzazione suddivisa come un'area di controllo, è opportuno avere dimensioni inferiori e consentirne l'essere compressi. Tornare a Xcode e modificare la visualizzazione per il lato destro selezionandolo nell'area di progettazione e fare clic sulla **controllo dimensioni**. A questo punto immettere un **larghezza** di `250`:

[![](quickstart-images/qs11.png "Impostazione della larghezza")](quickstart-images/qs11.png#lightbox)

Selezionare avanti l'elemento di divisione che rappresenta il lato destro, impostare un livello più elevato **priorità tenendo** e fare clic sul **utente può comprimere** casella di controllo:

[![](quickstart-images/qs12.png "Modifica della priorità dell'azienda")](quickstart-images/qs12.png#lightbox)

Se è tornare a Visual Studio per Mac e si esegue il progetto a questo punto, si noti che il lato destro mantiene il sistema sono più piccolo delle dimensioni e la finestra viene ridimensionata:

[![](quickstart-images/qs13.png "Un esempio di app in esecuzione")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>La definizione di una presentazione Segue

Si intende layout il lato destro della visualizzazione divisa per agire come un controllo per le proprietà del testo selezionato. Si saranno trascinare alcuni controlli alla visualizzazione nella parte inferiore al layout di interfaccia utente della finestra di ispezione. Per il controllo ultimo, vogliamo visualizzare nel popover che consente all'utente di selezionare da quattro stili di carattere predefiniti.

Aggiungiamo un pulsante per il controllo e un Controller di visualizzazione nell'area di progettazione. È possibile ridimensionare il Controller di visualizzazione per la dimensione che si desidera nostro Popover e aggiungere i quattro pulsanti. Successivamente, verrà **controllo** chiave fare clic sul pulsante nella visualizzazione controllo e trascinare al Controller di visualizzazione che rappresenta nel popover:

[![](quickstart-images/qs14.png "Trascinamento per la creazione di un elemento segue nuovo")](quickstart-images/qs14.png#lightbox)

Dal menu a comparsa, si selezionerà **nel Popover**: 

[![](quickstart-images/qs15.png "Selezione del tipo di elemento segue")](quickstart-images/qs15.png#lightbox)

Infine, abbiamo selezionare l'elemento Segue nell'area di progettazione e impostare il **preferito Edge** al **sinistra**. Quindi, verrà trascinato una riga dal **visualizzazione ancoraggio** al pulsante che intendiamo popover da collegare alle:

[![](quickstart-images/qs16.png "Trascinamento per la creazione di un elemento segue nuovo")](quickstart-images/qs16.png#lightbox)

Se è tornare a Visual Studio per Mac, eseguire l'app e fare clic sui **None** verrà visualizzato sul pulsante della finestra di ispezione popover:

[![](quickstart-images/qs17.png "Un esempio di elemento segue in esecuzione")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Creazione di App preferenze

App macOS più standard offrono una _finestra di dialogo preferenza_ che consente all'utente di definire diverse opzioni che consentono di controllare vari aspetti dell'app, ad esempio gli account utente o l'aspetto.

Per definire una finestra di dialogo standard di preferenza, trascinare un Controller di visualizzazione della scheda nell'area di progettazione:

[![](quickstart-images/qs18.png "Modifica lo storyboard in Xcode")](quickstart-images/qs18.png#lightbox)

Anche in questo caso, questa includerà automaticamente con l'elemento figlio di due controller di visualizzazione associati. Ad esempio i migliori risultati, aggiungeremo un'etichetta a ogni vista che verrà center TestExecution:

[![](quickstart-images/qs19.png "L'impostazione di vincoli")](quickstart-images/qs19.png#lightbox)

Successivamente, si vuole visualizzare la finestra Preferenze quando l'utente seleziona il **preferenze...**  voce di menu. Dalla barra dei Menu, selezionare la voce di menu Preferenze **controllo** chiave fare clic e trascinare una linea per i Controller di visualizzazione della scheda:

[![](quickstart-images/qs20.png "Trascinamento per la creazione di un elemento segue")](quickstart-images/qs20.png#lightbox)

Dalla finestra popup, si selezionerà **modale** per visualizzare questa finestra come finestra di dialogo modale:

[![](quickstart-images/qs21.png "Selezione del tipo di elemento segue")](quickstart-images/qs21.png#lightbox)

Se si salvano le modifiche, tornare a Visual Studio per Mac, eseguire l'app e selezionare il **preferenze...**  voce di menu, nostro preferenze nuovo dialogo verrà visualizzato:

[![](quickstart-images/qs22.png "Un esempio di elemento segue in esecuzione")](quickstart-images/qs22.png#lightbox)

È possibile notare che ciò non è simile a un'app macOS standard, la finestra di dialogo delle preferenze. Per risolvere questo problema, includere due file di immagine dell'App xamarin. Mac `Resources` cartella di **Esplora soluzioni** e tornare alla Interface Builder di Xcode.

Selezionare il Controller di visualizzazione della scheda e un commutatore relativi **stile** al **sulla barra degli strumenti**: 

[![](quickstart-images/qs23.png "Impostazione dello stile della barra della scheda")](quickstart-images/qs23.png#lightbox)

Selezionare ogni scheda e assegnarle un **etichetta** e selezionare una delle immagini per rappresentare lo:

[![](quickstart-images/qs24.png "Configurazione di ogni scheda in Xcode")](quickstart-images/qs24.png#lightbox)

Se si salvano le modifiche, tornare a Visual Studio per Mac, eseguire l'app e selezionare il **preferenze...**  voce di menu, la finestra di dialogo verrà ora visualizzato come un'app macOS standard:

[![](quickstart-images/qs25.png "Un esempio di finestra Preferenze in esecuzione")](quickstart-images/qs25.png#lightbox)

Per altre informazioni, vedere la [utilizzo delle immagini](~/mac/app-fundamentals/image.md), [menu](~/mac/user-interface/menu.md), [Windows](~/mac/user-interface/window.md) e [finestre di dialogo](~/mac/user-interface/dialog.md) documentazione.

## <a name="related-links"></a>Collegamenti correlati

- [MacStoryboard (esempio)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
