---
title: Aprire schermate
description: In questo articolo viene illustrato come creare un'app schermata di avvio per tutti i dispositivi iOS, per qualsiasi risoluzione e l'orientamento, utilizzando un unico Storyboard unificata.
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/19/2018
ms.openlocfilehash: 48dc2e7a270c4e12c4e3dc9d1e2ce14fb0d41249
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="launch-screens"></a>Aprire schermate

_In questo articolo viene illustrato come creare un'app schermata di avvio per tutti i dispositivi iOS, per qualsiasi risoluzione e l'orientamento, utilizzando un unico Storyboard unificata._

Prima di iOS 8, creazione di una schermata di avvio per un'app iOS richiedeva allo sviluppatore di specificare un asset di immagine per ognuno dei vari fattori di forma dispositivo e le soluzioni in cui è possibile eseguire l'app. Dopo la versione di iOS 8, tuttavia, è stato possibile utilizzare un unico Storyboard unificata per creare una schermata di avvio che funzioni correttamente in tutti i casi.

Questa breve procedura dettagliata viene descritto come creare una schermata di avvio con uno Storyboard forniti per impostazione predefinita in un nuovo progetto o uno Storyboard aggiunto manualmente a un progetto esistente. Viene quindi illustrato come utilizzare la finestra di progettazione iOS per aggiungere lo Storyboard, per impostare i vincoli su tali viste e per verificare che lo Storyboard siano corretto per vari dispositivi e gli orientamenti di visualizzazione di un'immagine e un'etichetta.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>La gestione delle schermate di avvio con storyboard

In iOS 8 (e versioni successive), lo sviluppatore può creare uno Storyboard unificata speciale per fornire la schermata di avvio anziché utilizzare una o più immagini di avvio statico. Quando si crea un avvio Storyboard nella finestra di progettazione iOS, è possibile utilizzare le classi di dimensioni e Layout automatico per definire layout diversi per gli ambienti di visualizzazione diverse. Tramite le classi di dimensioni e Layout automatico, lo sviluppatore può creare una schermata di avvio singolo che l'aspetto ottimale in tutti i dispositivi e visualizzare gli ambienti.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In Visual Studio per Mac, creare un nuovo progetto selezionando **File > nuova soluzione** e scegliendo **singola vista App**: 

    ![La finestra Nuovo progetto, con una singola visualizzazione App selezionato](launch-screens-images/launch01.png)

    - Per impostazione predefinita, un nuovo progetto include un **LaunchScreen.storyboard** file che definisce l'interfaccia della schermata di avvio. 
    - Per aggiungere invece uno Storyboard schermata di avvio per un progetto esistente, fare clic sul nome del progetto nel **soluzione riempimento** e scegliere **Aggiungi > Nuovo File...**  e quindi selezionare **schermata avvio**:

    ![La finestra Nuovo File con iOS avviare schermata selezionata](launch-screens-images/launch01b.png)

    - Nome del file **LaunchScreen** o un altro nome di propria scelta.

2. Configurare il progetto per utilizzare lo Storyboard appropriato per la schermata di avvio:

    - Fare doppio clic su di **Info. plist** file nel **soluzione riempimento** per aprirlo e modificarlo.
    - Nel **immagini di avvio** sezione, assicurarsi che **schermata avvio** è impostato sul nome dello Storyboard appropriato:

    ![Il selettore di schermata di avvio in Info. plist](launch-screens-images/launch02.png)

    - Per impostazione predefinita, un nuovo progetto è configurato per utilizzare **LaunchScreen.storyboard** come la schermata di avvio.

3. Aggiungere un'immagine di **Assets.xcassets** catalogo di Asset in modo che sia disponibile per l'utilizzo nella schermata di avvio. Per ulteriori informazioni, vedere il [aggiunta di immagini per un Asset immagine cataloghi](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sezione il [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) Guida.

4. Aprire **LaunchScreen.storyboard** per la modifica facendo doppio clic su esso nel **riempimento soluzione**.

5. Scegliere un dispositivo e l'orientamento su cui visualizzare l'anteprima dello Storyboard schermata avvio nella finestra di progettazione iOS. Aprire il pannello di selezione del dispositivo sulla barra degli strumenti nella parte inferiore e selezionare **iPhone 4S** e **verticale**.

    ![La barra degli strumenti di selezione dispositivo](launch-screens-images/launch05.png)

    - Si noti che solo la selezione di un dispositivo e l'orientamento modifica come finestra di progettazione iOS Visualizza l'anteprima di progettazione. Indipendentemente dal valore selezionato qui, aggiunta di vincoli vengono applicati a tutti i dispositivi e gli orientamenti, a meno che il **modificare tratti** pulsante è stato utilizzato per specificare in caso contrario. 

6. Impostare il **Background** colore di visualizzazione principale vista del Controller. Selezionare la visualizzazione facendo clic al centro il Controller di visualizzazione e modificare il colore di sfondo mediante la **proprietà riempimento**:

    ![Una singola visualizzazione con un colore di sfondo di colore viola](launch-screens-images/launch06.png)

7. Aggiungere un **visualizzazione immagine** la schermata di avvio e impostare la relativa origine **immagine**:

    - Trascinare un **visualizzazione immagine** dal **riempimento della casella degli strumenti** al centro della vista.
    - Con il **visualizzazione immagine** selezionato, nel **Widget** sezione del **proprietà riempimento** impostare il **immagine** proprietà immagine già impostata aggiungere il **Assets.xcassets** catalogo di Asset. Riposizionare e ridimensionare il **visualizzazione immagine** come richiesto:
    
    ![Visualizzazione di un'immagine con il relativo set di proprietà immagine](launch-screens-images/launch07.png)

8. Aggiungere un **etichetta** sotto il **visualizzazione immagine** e utilizzare il **proprietà riempimento** per impostare i relativi attributi: 

    ![Un'etichetta con il relativo set di colori e testo](launch-screens-images/launch08.png)

9. Passare alla modalità di modifica del vincolo utilizzando il pulsante destro il **barra degli strumenti vincoli**:
    
    ![Pulsante modalità di modifica di vincoli](launch-screens-images/launch09.png)

10. Aggiungere vincoli per il **visualizzazione immagine**, imposta l'altezza e larghezza e centro orizzontalmente e verticalmente:

    ![Visualizzazione di un'immagine con vincoli di layout](launch-screens-images/launch10.png)

    - Per ulteriori informazioni su come aggiungere i vincoli, vedere [Layout automatico con la finestra di progettazione Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Aggiungere vincoli per il **etichetta**centro orizzontalmente, assegnandogli un'altezza e larghezza e posizionarla fisse verticalmente distanza il **visualizzazione immagine**:

    ![Un'etichetta con vincoli di layout](launch-screens-images/launch11.png)

12. Altri dispositivi e gli orientamenti per verificare che la progettazione funzioni come previsto in tutti gli scenari di test. Nei casi in cui devono essere eseguiti per un dispositivo specifico oppure un orientamento regolazioni, utilizzare il **modificare tratti** pulsante per aggiungere vincoli per le classi di dimensioni specifiche:

    ![Schermata di avvio sottoposto a rendering come un iPhone X con orientamento orizzontale](launch-screens-images/launch12.png)

13. Salvare le modifiche allo Storyboard. Eseguire l'app in un dispositivo o simulatore, e la schermata di avvio saranno visibile come sta avviando l'app.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Creare un nuovo progetto. In Visual Studio, selezionare **File > Nuovo > progetto**, quindi scegliere **singola vista App (iPhone)**:
    
    ![La finestra Nuovo progetto, con una singola visualizzazione App (iPhone) selezionata](launch-screens-images/launch01-vs.png)

    - Denominare il progetto, scegliere un percorso e selezionare **OK**.

2. Se **risorse > LaunchScreen.xib** esiste nel **Esplora**, eliminarlo facendo clic sul file e scegliendo **eliminare**. Questo file verrà sostituito con uno Storyboard nel passaggio successivo.

3. Creare uno Storyboard da utilizzare come schermata di avvio. Nel **Esplora**, pulsante destro del mouse sul progetto e scegliere **Aggiungi > Nuovo elemento...**  seguito da **Storyboard vuoto**. Nome di questo Storyboard **LaunchScreen.storyboard** e fare clic su **Aggiungi**:

    ![La finestra Aggiungi nuovo elemento con Storyboard vuota selezionata](launch-screens-images/launch03-vs.png)

4. Configurare il progetto deve utilizzare **LaunchScreen.storyboard** come dello Storyboard, schermata avvio:

    - Fare doppio clic sul file **Info.plist** in **Esplora soluzioni** per aprirlo e modificarlo. 
    - Nel **asset visivi** scheda, impostare **schermata avvio** a **LaunchScreen**.

    ![Il selettore di schermata di avvio in Info. plist](launch-screens-images/launch04-vs.png)

5. Aggiungere un'immagine a un catalogo di Asset nel progetto in modo che sia disponibile per l'utilizzo nella schermata di avvio:

    - Nel **Esplora**, fare clic su **Asset cataloghi** e selezionare **aggiungere catalogo di Asset**. Nome di questo nuovo catalogo di Asset **asset**:

    ![Finestra Aggiungi nuovo elemento del catalogo di Asset selezionata](launch-screens-images/launch05-vs.png)

    - Aggiungere un nuovo Set di immagini per il **asset** catalogo di Asset, come descritto nel [aggiunta di immagini per un Asset immagine cataloghi](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sezione del [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) Guida.

6. Aprire **LaunchScreen.storyboard** per la modifica facendo doppio clic su esso nel **Esplora**.

    - Per modificare un file di Storyboard, Visual Studio richiede una connessione attiva a un host di compilazione Mac. Vedere il [connessione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) Guida per i dettagli.

7. Scegliere un dispositivo e l'orientamento su cui visualizzare l'anteprima dello Storyboard schermata avvio nella finestra di progettazione iOS. Aprire il pannello di selezione del dispositivo sulla barra degli strumenti nella parte inferiore e selezionare **iPhone 4S** e **verticale**: 
 
    ![La barra degli strumenti di selezione dispositivo](launch-screens-images/launch07-vs.png)

    - Si noti che solo la selezione di un dispositivo e l'orientamento modifica come finestra di progettazione iOS Visualizza l'anteprima di progettazione. Indipendentemente dal valore selezionato qui, aggiunta di vincoli vengono applicati a tutti i dispositivi e gli orientamenti, a meno che il **modificare tratti** pulsante è stato utilizzato per specificare in caso contrario. 

8. Aggiungere un **View Controller** allo Storyboard trascinando uno dal **della casella degli strumenti** nell'area di progettazione: 

    ![Un oggetto vuoto View Controller aggiunto all'area di progettazione](launch-screens-images/launch08-vs.png)

9. Impostare il **Background** colore di visualizzazione principale vista del Controller. Selezionare la visualizzazione facendo clic al centro il Controller di visualizzazione e modificare il colore di sfondo mediante l'il **finestra proprietà**:
    
    ![Una singola visualizzazione con un colore di sfondo di colore viola](launch-screens-images/launch09-vs.png)

10. Aggiungere un **visualizzazione immagine** la schermata di avvio e impostare la relativa origine **immagine**:

    - Trascinare un **visualizzazione immagine** dal **della casella degli strumenti** al centro della vista.
    - Con il **visualizzazione immagine** ancora selezionato, nel **Widget** sezione del **finestra proprietà** impostare il **immagine** proprietà al Set di immagini già aggiunto il **asset** catalogo di Asset. Riposizionare e ridimensionare il **visualizzazione immagine** come richiesto:
    
    ![Visualizzazione di un'immagine con il relativo set di proprietà immagine](launch-screens-images/launch10-vs.png)

11. Aggiungere un **etichetta** sotto il **immagine vista**:

    - Trascinare un **etichetta** dal **della casella degli strumenti** nell'area di progettazione, posizionarlo sotto il **visualizzazione immagine**.
    - Impostare gli attributi per il **etichetta** utilizzando il **finestra proprietà**:

    ![Un'etichetta con il relativo set di colori e testo](launch-screens-images/launch11-vs.png) 

12. Passare alla modalità di modifica del vincolo utilizzando il pulsante destro il **barra degli strumenti vincoli**:
    
    ![Pulsante modalità di modifica di vincoli](launch-screens-images/launch12-vs.png) 

13. Aggiungere vincoli per il **visualizzazione immagine**, imposta l'altezza e larghezza e centro orizzontalmente e verticalmente:

    ![Visualizzazione di un'immagine con vincoli di layout](launch-screens-images/launch13-vs.png) 

    - Per informazioni sull'aggiunta di vincoli, vedere [Layout automatico con la finestra di progettazione Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Aggiungere vincoli per il **etichetta**centro orizzontalmente, assegnandogli un'altezza e larghezza e posizionarla fisse verticalmente distanza il **visualizzazione immagine**:
    
    ![Un'etichetta con vincoli di layout](launch-screens-images/launch14-vs.png) 

15. Altri dispositivi e gli orientamenti per verificare che la progettazione funzioni come previsto in tutti gli scenari di test. Nei casi in cui devono essere eseguiti per un dispositivo specifico oppure un orientamento regolazioni, utilizzare il **modificare tratti** pulsante per aggiungere vincoli per le classi di dimensioni specifiche:

    ![Schermata di avvio sottoposto a rendering come un iPhone X con orientamento orizzontale](launch-screens-images/launch15-vs.png) 

16. Salvare le modifiche allo Storyboard. Eseguire l'app in un dispositivo o simulatore, e la schermata di avvio saranno visibile come sta avviando l'app.

-----

> [!NOTE]
> **Nota**: Storyboard A utilizzato come una schermata di avvio _deve_ includono solo semplici incorporati, gli elementi dell'interfaccia utente e **Impossibile** non tutti i calcoli o derivare da una classe personalizzata.

Per ulteriori informazioni sulla creazione di una schermata di avvio con uno Storyboard unificata, vedere il [schermate di avvio dinamico](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) sezione la [unificata storyboard](~/ios/user-interface/storyboards/unified-storyboards.md) Guida.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrazione da avviare storyboard di schermata

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Quando si aggiorna un'app esistente per l'utilizzo di storyboard per le schermate relativo avvio, fare clic destro la **nome progetto** nel **Esplora soluzioni** e selezionare **Aggiungi**  >  **Nuovo File...** . Selezionare **iOS** > **schermata avvio** e fare clic su di **New** pulsante:

![](launch-screens-images/storyboard02.png "Selezionare una schermata di avvio di iOS")

Successivamente, fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo. In **schermata avvio**, selezionare il nuovo file di Storyboard creato in precedenza.

![](launch-screens-images/storyboard09.png "Selezionare il nuovo file di Storyboard creato in precedenza")


Per utilizzare il nuovo Storyboard come una schermata di avvio, eseguire le operazioni seguenti:

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Scorrere il **Universal immagini di avvio** sezione dell'editor, aprire il **schermata avvio** elenco a discesa e selezionare il nome dello storyboard creato in precedenza: 

    ![](launch-screens-images/storyboard08.png "Impostazione della schermata di avvio allo storyboard")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare clic sul nome del progetto nel **Esplora** e selezionare **Aggiungi** > **nuovo File...** : 

    ![](launch-screens-images/image012.png "Aggiungi nuovo file")
2. Immettere un nome per la schermata di avvio, quindi scegliere il **Aggiungi** pulsante: 

    ![](launch-screens-images/image013.png "Immettere un nome per la schermata di avvio")
3. Nel **Esplora**, doppio clic sul file di storyboard appena creato per aprirlo e modificarlo.
4. Verificare che il **dimensioni classe** è impostato su **qualsiasi: qualsiasi** e **vista come** è **generico**: 

    ![](launch-screens-images/image016.png "Verificare che la classe di dimensione è impostata su qualsiasi: qualsiasi e la visualizzazione è generico")
5. Schermata di avvio da classi di dimensione, elementi dell'interfaccia utente semplici di assembly (ad esempio `UIImageView`) e le immagini incluse nel pacchetto dell'applicazione: 

    ![](launch-screens-images/image017.png "Schermata di avvio nella finestra di progettazione iOS assembly")
6. Salvare le modifiche allo Storyboard.

-----

## <a name="related-links"></a>Collegamenti correlati

- [Schermate di avvio dinamico (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Nozioni di base su iOS Designer](~/ios/user-interface/designer/index.md)
- [Aggiunta di immagini a un'immagine di catalogo di Asset impostato](~/ios/app-fundamentals/images-icons/displaying-an-image.md#asset-catalogs)
- [Layout automatico con la finestra di progettazione Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Linee guida dell'interfaccia umana: Schermata di avvio](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
