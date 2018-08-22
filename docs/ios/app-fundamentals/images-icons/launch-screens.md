---
title: Avviare le schermate per le app xamarin. IOS
description: Questo articolo illustra come creare un'app schermata di avvio veloce per tutti i dispositivi iOS, con qualsiasi risoluzione e l'orientamento, utilizzare uno Storyboard unificati singolo.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2018
ms.openlocfilehash: 40b8c38e89e96223bbf657ff06356d9fb2e9d9b3
ms.sourcegitcommit: e64c3c10d6a36b3b031d6d4dbff7af74ab2b7f21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40251220"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Avviare le schermate per le app xamarin. IOS

_Questo articolo illustra come creare un'app schermata di avvio veloce per tutti i dispositivi iOS, con qualsiasi risoluzione e l'orientamento, utilizzare uno Storyboard unificati singolo._

Prima di iOS 8, creazione di una schermata di avvio per un'app iOS richiedeva allo sviluppatore di specificare un asset di immagine per ognuna delle diverse tipologie di dispositivi e le soluzioni in cui è stato possibile eseguire l'app. Dopo il rilascio di iOS 8, tuttavia, è stato possibile usare un unico Storyboard unificati per creare una schermata di avvio che siano corrette in tutti i casi.

Questa breve procedura dettagliata viene descritto come creare una schermata di avvio con uno Storyboard forniti per impostazione predefinita in un nuovo progetto o con uno Storyboard aggiunti manualmente a un progetto esistente. Viene quindi illustrato come usare iOS Designer per aggiungere una visualizzazione immagine e un'etichetta allo Storyboard, impostare vincoli su tali viste e per verificare che lo Storyboard sembra corretto per diversi dispositivi e gli orientamenti.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>La gestione delle schermate di avvio con gli storyboard

In iOS 8 (e versioni successive), lo sviluppatore può creare uno Storyboard unificati speciali per fornire la schermata avvio anziché utilizzare uno o più immagini di avvio statico. Quando si crea un avvio di uno Storyboard in iOS Designer, usare le classi di dimensioni e il Layout automatico per definire il layout diversi per gli ambienti di visualizzazione diverse. Usando le classi di dimensioni e il Layout automatico, lo sviluppatore può creare una schermata di avvio singolo che sembra valida in tutti i dispositivi e visualizzare gli ambienti.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In Visual Studio per Mac, creare un nuovo progetto selezionando **File > nuova soluzione** e quindi scegliendo **App visualizzazione singola**: 

    ![La finestra Nuovo progetto con App visualizzazione singola selezionato](launch-screens-images/launch01.png)

    - Per impostazione predefinita, un nuovo progetto include un **LaunchScreen.storyboard** file che definisce l'interfaccia della schermata di avvio. 
    - Per aggiungere invece uno Storyboard di schermo avviare a un progetto esistente, fare clic sul nome del progetto nel **riquadro della soluzione** e scegliere **Aggiungi > Nuovo File...**  e quindi selezionare **schermata avvio**:

    ![Finestra del nuovo File con iOS schermata avvio selezionata](launch-screens-images/launch01b.png)

    - Denominare il file **LaunchScreen** o un altro nome di propria scelta.

2. Configurare il progetto per utilizzare lo Storyboard appropriato per la schermata di avvio:

    - Fare doppio clic il **Info. plist** del file nei **riquadro della soluzione** per aprirlo e modificarlo.
    - Nel **immagini di avvio** sezione, assicurarsi che **schermata avvio** è impostato sul nome dello Storyboard appropriato:

    ![Il selettore di schermata di avvio nel file Info. plist](launch-screens-images/launch02.png)

    - Per impostazione predefinita, un nuovo progetto è configurato per utilizzare **LaunchScreen.storyboard** come la schermata di avvio.

3. Aggiungere un'immagine per il **assets. xcassets** catalogo di Asset in modo che sia disponibile per l'uso nella schermata di avvio. Per altre informazioni, vedere il [aggiunta di immagini da un catalogo Asset immagine impostata](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sezione il [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) Guida.

4. Aprire **LaunchScreen.storyboard** per la modifica facendo doppio clic su esso nel **riquadro della soluzione**.

5. Scegliere un dispositivo e l'orientamento su cui si desidera visualizzare in anteprima lo Storyboard di schermata avvio veloce in iOS Designer. Aprire il pannello di selezione dispositivo sulla barra degli strumenti in basso e selezionare **iPhone 4S** e **verticale**.

    ![Barra degli strumenti di selezione il dispositivo](launch-screens-images/launch05.png)

    - Si noti che se si seleziona un dispositivo e l'orientamento viene modificato solo come iOS Designer Visualizza l'anteprima di progettazione. Indipendentemente dalla selezione effettuata qui, aggiunti di recente i vincoli vengono applicati in tutti i dispositivi e gli orientamenti, a meno che il **modifica tratti** pulsante è stato usato per specificare in caso contrario. 

6. Impostare il **sfondo** colori della visualizzazione principale del Controller di visualizzazione. Selezionare la vista facendo clic al centro il Controller di visualizzazione e modificare il colore di sfondo usando il **riquadro delle proprietà**:

    ![Una singola visualizzazione con un colore di sfondo di colore viola](launch-screens-images/launch06.png)

7. Aggiungere un **Visualizza immagine** alla schermata di avvio e impostare la relativa origine **immagine**:

    - Trascinare un' **Visualizza immagine** dalle **riquadro casella degli strumenti** al centro della visualizzazione.
    - Con il **visualizzazione immagine** selezionato, nella **Widget** sezione del **riquadro delle proprietà** impostare il **immagine** proprietà immagine già impostato aggiungere il **assets. xcassets** catalogo di Asset. Riposizionare e ridimensionare le **visualizzazione immagine** in base alle necessità:
    
    ![Visualizzazione di un'immagine con il relativo set di proprietà immagine](launch-screens-images/launch07.png)

8. Aggiungere un **Label** sotto il **visualizzazione immagine** e usare il **riquadro delle proprietà** per impostare i relativi attributi: 

    ![Un'etichetta con il relativo set di colori e testo](launch-screens-images/launch08.png)

9. Passare alla modalità di modifica del vincolo con il pulsante destro nel **sulla barra degli strumenti vincoli**:
    
    ![Il pulsante modalità di modifica vincoli](launch-screens-images/launch09.png)

10. Aggiungere vincoli per il **visualizzazione immagine**, impostando l'altezza e larghezza e centro orizzontalmente e verticalmente:

    ![Visualizzazione di un'immagine con vincoli di layout](launch-screens-images/launch10.png)

    - Per altre informazioni su come aggiungere i vincoli, vedere [Layout automatico con la finestra di progettazione di Xamarin per iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Aggiungere vincoli per il **etichetta**, centrato orizzontalmente, assegnargli un'altezza e larghezza e il posizionamento è fissa distanza verticale dalle **visualizzazione immagine**:

    ![Un'etichetta con vincoli di layout](launch-screens-images/launch11.png)

12. Altri dispositivi e gli orientamenti per verificare che la progettazione sia come previsto in tutti gli scenari di test. In casi in cui è necessario apportare per un dispositivo specifico o un orientamento regolazioni, usare il **modifica tratti** pulsante per aggiungere vincoli per le classi di dimensioni specifiche:

    ![Schermata di avvio viene eseguito il rendering come un iPhone X con orientamento orizzontale](launch-screens-images/launch12.png)

13. Salvare le modifiche allo Storyboard. Eseguire l'app in un dispositivo o simulatore e schermata di avvio sarà visibile come sta avviando l'app.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Creare un nuovo progetto. In Visual Studio, selezionare **File > Nuovo > progetto > Visual c# > iPhone & iPad > App iOS (Xamarin)**:

    ![Finestra Nuovo progetto, con App iOS (Xamarin) selezionata](launch-screens-images/launch01.w157.png)

    Selezionare il **App visualizzazione singola** modello e quindi fare clic su **OK**:

    ![Modello di App visualizzazione singola](launch-screens-images/launch01-2.w157.png)

2. Se **risorse > LaunchScreen.xib** presente nella **Esplora soluzioni**, eliminarlo facendo clic sul file e scegliendo **Elimina**. Questo file verrà sostituito con uno Storyboard nel passaggio successivo.

3. Creare uno Storyboard da utilizzare come schermata di avvio. Nel **Esplora soluzioni**, fare clic sul progetto e scegliere **Aggiungi > Nuovo elemento...**  seguita da **Storyboard vuoto**. Nome di questo Storyboard **LaunchScreen.storyboard** e fare clic su **Add**:

    ![Finestra Aggiungi nuovo elemento, con uno Storyboard vuoto selezionato](launch-screens-images/launch03.w157.png)

4. Configurare il progetto deve utilizzare **LaunchScreen.storyboard** come dello Storyboard, schermata di avvio:

    - Fare doppio clic sul file **Info.plist** in **Esplora soluzioni** per aprirlo e modificarlo. 
    - Nel **asset visivi** scheda, impostare **schermata avvio veloce** al **LaunchScreen**.

    ![Il selettore di schermata di avvio nel file Info. plist](launch-screens-images/launch04-vs.png)

5. Aggiungere un'immagine a un catalogo di Asset nel progetto in modo che sia disponibile per l'uso nella schermata di avvio:

    - Nel **Esplora soluzioni**, fare clic su **cataloghi Asset** e selezionare **Aggiungi catalogo Asset**. Assegnare un nome per il nuovo catalogo Asset **asset**:

    ![Finestra Aggiungi nuovo elemento, con il catalogo di Asset selezionata](launch-screens-images/launch05.w157.png)

    - Aggiungere un nuovo Set di immagini per il **asset** catalogo di Asset, come descritto nel [aggiunta di immagini da un catalogo Asset immagine Set](~/ios/app-fundamentals/images-icons/displaying-an-image.md) sezione del [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) Guida.

6. Aprire **LaunchScreen.storyboard** per la modifica facendo doppio clic su esso nel **Esplora soluzioni**.

    - Per modificare un file Storyboard, Visual Studio richiede una connessione attiva a un host di compilazione Mac. Vedere le [connessione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) Guida per i dettagli.

7. Scegliere un dispositivo e l'orientamento su cui si desidera visualizzare in anteprima lo Storyboard di schermata avvio veloce in iOS Designer. Aprire il pannello di selezione dispositivo sulla barra degli strumenti in basso e selezionare **iPhone 4S** e **verticale**: 
 
    ![Barra degli strumenti di selezione il dispositivo](launch-screens-images/launch07-vs.png)

    - Si noti che se si seleziona un dispositivo e l'orientamento viene modificato solo come iOS Designer Visualizza l'anteprima di progettazione. Indipendentemente dalla selezione effettuata qui, aggiunti di recente i vincoli vengono applicati in tutti i dispositivi e gli orientamenti, a meno che il **modifica tratti** pulsante è stato usato per specificare in caso contrario. 

8. Aggiungere un **Controller di visualizzazione** allo Storyboard trascinando uno dalle **della casella degli strumenti** nell'area di progettazione: 

    ![Un oggetto vuoto Controller di visualizzazione aggiunto all'area di progettazione](launch-screens-images/launch08-vs.png)

9. Impostare il **sfondo** colori della visualizzazione principale del Controller di visualizzazione. Selezionare la vista facendo clic al centro il Controller di visualizzazione e modificare il colore di sfondo mediante il il **finestra proprietà**:
    
    ![Una singola visualizzazione con un colore di sfondo di colore viola](launch-screens-images/launch09-vs.png)

10. Aggiungere un **Visualizza immagine** alla schermata di avvio e impostare la relativa origine **immagine**:

    - Trascinare un' **Visualizza immagine** dalle **della casella degli strumenti** al centro della visualizzazione.
    - Con il **Visualizza immagine** ancora selezionato, nel **Widget** sezione del **finestra proprietà** impostare il **immagine** proprietà per impostare l'immagine già aggiunto per il **asset** catalogo di Asset. Riposizionare e ridimensionare le **visualizzazione immagine** in base alle necessità:
    
    ![Visualizzazione di un'immagine con il relativo set di proprietà immagine](launch-screens-images/launch10-vs.png)

11. Aggiungere un **Label** sotto il **immagine Visualizza**:

    - Trascinare un **Label** dal **della casella degli strumenti** nell'area di progettazione, posizionandolo sotto il **visualizzazione immagine**.
    - Impostare gli attributi per il **etichetta** usando la **finestra proprietà**:

    ![Un'etichetta con il relativo set di colori e testo](launch-screens-images/launch11-vs.png) 

12. Passare alla modalità di modifica del vincolo con il pulsante destro nel **sulla barra degli strumenti vincoli**:
    
    ![Il pulsante modalità di modifica vincoli](launch-screens-images/launch12-vs.png) 

13. Aggiungere vincoli per il **visualizzazione immagine**, impostando l'altezza e larghezza e centro orizzontalmente e verticalmente:

    ![Visualizzazione di un'immagine con vincoli di layout](launch-screens-images/launch13-vs.png) 

    - Per informazioni sull'aggiunta di vincoli, vedere [Layout automatico con la finestra di progettazione di Xamarin per iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Aggiungere vincoli per il **etichetta**, centrato orizzontalmente, assegnargli un'altezza e larghezza e il posizionamento è fissa distanza verticale dalle **visualizzazione immagine**:
    
    ![Un'etichetta con vincoli di layout](launch-screens-images/launch14-vs.png) 

15. Altri dispositivi e gli orientamenti per verificare che la progettazione sia come previsto in tutti gli scenari di test. In casi in cui è necessario apportare per un dispositivo specifico o un orientamento regolazioni, usare il **modifica tratti** pulsante per aggiungere vincoli per le classi di dimensioni specifiche:

    ![Schermata di avvio viene eseguito il rendering come un iPhone X con orientamento orizzontale](launch-screens-images/launch15-vs.png) 

16. Salvare le modifiche allo Storyboard. Eseguire l'app in un dispositivo o simulatore e schermata di avvio sarà visibile come sta avviando l'app.

-----

> [!NOTE]
> Uno Storyboard usato come una schermata di avvio veloce _devono_ includono solo semplici incorporati, gli elementi dell'interfaccia utente e **non è possibile** non tutti i calcoli o derivare da una classe personalizzata.

Per altre informazioni sulla creazione di una schermata di avvio con uno Storyboard unificati, vedere la [schermate di avvio dinamico](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) sezione il [Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) Guida.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrazione da avviare storyboard di schermata

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Quando si aggiorna un'app esistente per usare gli storyboard per le schermate di avvio, fare clic il **nome progetto** nel **Esplora soluzioni** e selezionare **Add**  >  **Nuovo File...** . Selezionare **iOS** > **schermata avvio veloce** e fare clic sui **New** pulsante:

![](launch-screens-images/storyboard02.png "Selezionare una schermata di avvio di iOS")

Successivamente, fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo. Sotto **schermata avvio**, selezionare il nuovo file Storyboard creato in precedenza.

![](launch-screens-images/storyboard09.png "Selezionare il nuovo file Storyboard creato in precedenza")


Per usare il nuovo Storyboard come una schermata di avvio, eseguire le operazioni seguenti:

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Scorrere fino al **universale immagini di avvio** sezione dell'editor, aprire il **schermata avvio** elenco a discesa e selezionare il nome dello storyboard creato in precedenza: 

    ![](launch-screens-images/storyboard08.png "Impostazione della schermata di avvio allo storyboard")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare clic sul nome del progetto nel **Esplora soluzioni** e selezionare **Add** > **nuovo File...** : 

    ![](launch-screens-images/image012.png "Aggiungere nuovo file")
2. Immettere un nome per la schermata di avvio, quindi scegliere il **Add** pulsante: 

    ![](launch-screens-images/image013.png "Immettere un nome per la schermata di avvio")
3. Nel **Esplora soluzioni**, doppio clic sul file storyboard appena creata per aprirlo e modificarlo.
4. Verificare che il **classe di dimensioni** è impostata su **qualsiasi: qualsiasi** e il **Visualizza come** è **generico**: 

    ![](launch-screens-images/image016.png "Assicurarsi che la classe di dimensioni è impostata su qualsiasi: qualsiasi e la visualizzazione è generico")
5. Schermata di avvio da classi di dimensioni, gli elementi dell'interfaccia utente semplici assembly (ad esempio `UIImageView`) e le immagini che sono stati inclusi nel bundle dell'applicazione: 

    ![](launch-screens-images/image017.png "Assembly la schermata di avvio in iOS Designer")
6. Salvare le modifiche allo Storyboard.

-----

## <a name="related-links"></a>Collegamenti correlati

- [Schermate di avvio dinamico (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Nozioni di base su iOS Designer](~/ios/user-interface/designer/index.md)
- [Aggiunta di immagini di un'immagine di catalogo di Asset impostata](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Layout automatico con la finestra di progettazione di Xamarin per iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Human Interface Guidelines di: Schermata di avvio](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
