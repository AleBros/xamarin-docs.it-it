---
title: Storyboard in Novell. Mac-Avvio rapido
description: Questo documento fornisce un'introduzione introduttiva alla creazione di interfacce utente macOS con storyboard in Novell. Mac. Viene descritto come creare un segue e creare una finestra delle preferenze.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/02/2017
ms.openlocfilehash: 498f73d6e5942fe305dc19effc83b9d976d86ac5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291909"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Storyboard in Novell. Mac-Avvio rapido

Come introduzione rapida all'uso degli storyboard per definire l'interfaccia utente di un'app Novell. Mac, iniziamo un nuovo progetto Novell. Mac. Selezionare **Mac** > **App** > **App Cocoa** e fare clic sul pulsante **Avanti**:

[![](quickstart-images/qs01.png "Aggiunta di una nuova app Cocoa")](quickstart-images/qs01.png#lightbox)

Usare il **nome** dell' `MacStoryboard` app e fare clic sul pulsante **Avanti** :

[![](quickstart-images/qs02.png "Impostazione del nome dell'app")](quickstart-images/qs02.png#lightbox)

Usare il nome del **progetto** e il **nome della soluzione** predefiniti e fare clic sul pulsante **Crea** :

[![](quickstart-images/qs03.png "Nome del progetto e della soluzione")](quickstart-images/qs03.png#lightbox)

Nella **Esplora soluzioni**fare doppio clic sul `Main.storyboard` file per aprirlo per la modifica nella Interface Builder di Xcode:

[![](quickstart-images/qs04.png "Modifica dello storyboard in Xcode")](quickstart-images/qs04.png#lightbox)

Come si può notare, lo storyboard predefinito definisce sia la barra dei menu dell'app che la finestra principale con il controller di visualizzazione e la visualizzazione. Per questa applicazione di esempio, verrà creata un'interfaccia utente con una _visualizzazione contenuto_ principale su un lato e una _visualizzazione Inspector_ nel secondo.

A tale scopo, è necessario prima rimuovere il controller di visualizzazione e la visualizzazione predefiniti forniti con lo storyboard selezionando il controller in Interface Builder e premendo il tasto **Canc** :

[![](quickstart-images/qs05.png "Rimozione del controller di visualizzazione predefinito")](quickstart-images/qs05.png#lightbox)

Digitare `split` quindi nell'area **filtro** , selezionare il controller di visualizzazione Split verticale e trascinarlo nella _area di progettazione_:

[![](quickstart-images/qs06.png "Ricerca del controller Split View")](quickstart-images/qs06.png#lightbox)

Si noti che il controller include automaticamente due controller di visualizzazione figlio (e le visualizzazioni correlate), cablati fino ai lati sinistro e destro della visualizzazione divisa. Per collegare la visualizzazione divisa alla relativa finestra padre, premere il tasto **CTRL** , fare clic sul controller della finestra (il cerchio blu nel frame del controller della finestra) e trascinare una riga sul controller di visualizzazione divisa. Selezionare il **contenuto della finestra** dal popup:

[![](quickstart-images/qs07.png "Impostazione della visualizzazione contenuto di Windows")](quickstart-images/qs07.png#lightbox)

In questo modo i due elementi di interfaccia vengono collegati insieme usando un segue:

[![](quickstart-images/qs08.png "Segue tra la finestra e il contenuto")](quickstart-images/qs08.png#lightbox)

Si vuole inserire una visualizzazione di testo sul lato sinistro della visualizzazione divisa e fare in modo che riempia automaticamente l'area disponibile quando la finestra o la visualizzazione divisa viene ridimensionata. Trascinare una visualizzazione di testo sul controller di visualizzazione superiore collegato alla visualizzazione divisa, quindi fare clic sul vincolo di layout automatico del **pin** , ovvero la seconda icona da destra nella parte inferiore del area di progettazione.

[![](quickstart-images/qs09.png "Configurazione dei vincoli")](quickstart-images/qs09.png#lightbox)

Da qui si fa clic su tutte e quattro le icone i **-Beam** intorno al rettangolo di delimitazione nella parte superiore del riquadro dei vincoli e si fa clic sul pulsante **Aggiungi 4 vincoli** nella parte inferiore per aggiungere i vincoli richiesti.

Se si torna a Visual Studio per Mac ed eseguire il progetto, si noterà che la visualizzazione di testo viene ridimensionata automaticamente in modo da riempire il lato sinistro della visualizzazione divisa quando la finestra o la suddivisione viene ridimensionata:

[![](quickstart-images/qs10.png "Esempio di app in esecuzione")](quickstart-images/qs10.png#lightbox)

Poiché si utilizzerà il lato destro della visualizzazione divisa come area di controllo, si vuole che abbia una dimensione inferiore e ne consenta la compressione. Tornare a Xcode e modificare la visualizzazione per il lato destro selezionandolo nel Area di progettazione e facendo clic sul **controllo dimensioni**. Da qui immettere una **larghezza** `250`:

[![](quickstart-images/qs11.png "Impostazione della larghezza")](quickstart-images/qs11.png#lightbox)

Selezionare quindi l'elemento diviso che rappresenta il lato destro, impostare una **priorità di contenimento** superiore e fare clic sulla casella di controllo l' **utente può comprimere** :

[![](quickstart-images/qs12.png "Modifica della priorità di contenimento")](quickstart-images/qs12.png#lightbox)

Se si torna a Visual Studio per Mac e si esegue il progetto ora, si noti che il lato destro mantiene le dimensioni più piccole e la finestra viene ridimensionata:

[![](quickstart-images/qs13.png "Esempio di app in esecuzione")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definizione di una presentazione segue

Il layout del lato destro della visualizzazione divisa verrà impostato in modo da fungere da controllo per le proprietà del testo selezionato. I controlli verranno trascinati nella visualizzazione inferiore per il layout dell'interfaccia utente del controllo. Per l'ultimo controllo, si desidera visualizzare un oggetto popopov che consente all'utente di effettuare una selezione da quattro stili di carattere predefiniti.

Al Area di progettazione verrà aggiunto un pulsante per il controllo e un controller di visualizzazione. Il controller di visualizzazione verrà ridimensionato in modo da ottenere le dimensioni desiderate per l'utente e aggiungere quattro pulsanti. A questo punto, è possibile fare clic sul pulsante nella visualizzazione Inspector e trascinarlo sul controller di visualizzazione che rappresenterà **il codice.**

[![](quickstart-images/qs14.png "Trascinamento per creare un nuovo segue")](quickstart-images/qs14.png#lightbox)

Nel menu di scelta rapida selezionare **popover**: 

[![](quickstart-images/qs15.png "Selezione del tipo di segue")](quickstart-images/qs15.png#lightbox)

Infine, selezionare il segue nel Area di progettazione e impostare il **bordo preferito** su **Left**. Quindi, si trascina una linea dalla visualizzazione di **ancoraggio** al pulsante a cui si vuole collegare il popopover:

[![](quickstart-images/qs16.png "Trascinamento per creare un nuovo segue")](quickstart-images/qs16.png#lightbox)

Se si torna alla Visual Studio per Mac, eseguire l'app e fare clic sul pulsante **None (nessuno** ) nel controllo. verrà visualizzato il seguente:

[![](quickstart-images/qs17.png "Esempio di segue in esecuzione")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Creazione di preferenze per le app

La maggior parte delle app macOS standard fornisce una _finestra di dialogo di preferenza_ che consente all'utente di definire diverse opzioni che controllano vari aspetti dell'app, ad esempio l'aspetto o gli account utente.

Per definire una finestra di dialogo preferenziale standard, trascinare prima di tutto un controller di visualizzazione scheda nel Area di progettazione:

[![](quickstart-images/qs18.png "Modifica dello storyboard in Xcode")](quickstart-images/qs18.png#lightbox)

Anche in questo caso, verranno assegnati automaticamente due controller di visualizzazione figlio. Ad esempio, si aggiungerà un'etichetta a ogni visualizzazione che verrà centrata al suo interno:

[![](quickstart-images/qs19.png "Impostazione dei vincoli")](quickstart-images/qs19.png#lightbox)

Successivamente, si desidera visualizzare la finestra preferenze quando l'utente seleziona la voce di menu **Preferenze..** .. Dalla barra dei menu, selezionare la voce di menu Preferenze, **controllare** la chiave-fare clic e trascinare una riga sul controller di visualizzazione scheda:

[![](quickstart-images/qs20.png "Trascinamento per creare un segue")](quickstart-images/qs20.png#lightbox)

Dalla finestra popup, selezionare **modale** per visualizzare questa finestra come finestra di dialogo modale:

[![](quickstart-images/qs21.png "Selezione del tipo di segue")](quickstart-images/qs21.png#lightbox)

Se si salvano le modifiche, tornare alla Visual Studio per Mac, eseguire l'app e selezionare la voce di menu **Preferenze..** ., verrà visualizzata la finestra di dialogo nuove preferenze:

[![](quickstart-images/qs22.png "Esempio di segue in esecuzione")](quickstart-images/qs22.png#lightbox)

Si potrebbe notare che questa non è simile a una finestra di dialogo preferenza app macOS standard. Per risolvere questo problema, includere due file di immagine nella `Resources` cartella dell'app Novell. Mac nella **Esplora soluzioni** e tornare al Interface Builder di Xcode.

Selezionare il controller di visualizzazione a schede e cambiare **lo stile** sulla **barra degli strumenti**: 

[![](quickstart-images/qs23.png "Impostazione dello stile della barra scheda")](quickstart-images/qs23.png#lightbox)

Selezionare ogni scheda e assegnarle un' **etichetta** e selezionare una delle immagini per rappresentarla:

[![](quickstart-images/qs24.png "Configurazione di ogni scheda in Xcode")](quickstart-images/qs24.png#lightbox)

Se si salvano le modifiche, tornare alla Visual Studio per Mac, eseguire l'app e selezionare la voce di menu **Preferenze..** .. la finestra di dialogo verrà ora visualizzata come un'app MacOS standard:

[![](quickstart-images/qs25.png "Esempio di finestra delle preferenze in esecuzione")](quickstart-images/qs25.png#lightbox)

Per ulteriori informazioni, vedere la documentazione [utilizzo di immagini](~/mac/app-fundamentals/image.md), [menu](~/mac/user-interface/menu.md), [finestre](~/mac/user-interface/window.md) e finestre di [dialogo](~/mac/user-interface/dialog.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
