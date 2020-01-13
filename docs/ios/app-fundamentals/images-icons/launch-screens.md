---
title: Schermate di avvio per le app Xamarin.iOS
description: Questo articolo illustra come creare una schermata di avvio dell'app per tutti i dispositivi iOS, con qualsiasi risoluzione e orientamento, usando un singolo storyboard unificato.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: 3adabbac3f56747093aa8a3def85869a92d33600
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010495"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Schermate di avvio per le app Xamarin.iOS

_Questo articolo illustra come creare una schermata di avvio dell'app per tutti i dispositivi iOS, con qualsiasi risoluzione e orientamento, usando un singolo storyboard unificato._

Prima di iOS 8, per creare una schermata di avvio per un'app per iOS, è necessario che lo sviluppatore fornisca un asset di immagine per ognuno dei diversi fattori di forma e risoluzione del dispositivo in cui l'app può essere eseguita. Dal rilascio di iOS 8, tuttavia, è possibile usare un singolo storyboard unificato per creare una schermata di avvio che sembra corretta in tutti i casi.

In questa breve procedura dettagliata viene descritto come creare una schermata di avvio con uno storyboard fornito per impostazione predefinita in un nuovo progetto o con uno storyboard aggiunto manualmente a un progetto esistente. Viene quindi illustrato come usare la finestra di progettazione iOS per aggiungere una visualizzazione immagine e un'etichetta allo storyboard, per impostare vincoli su tali viste e per verificare che lo storyboard appaia corretto per diversi dispositivi e orientamenti.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Gestione delle schermate di avvio con gli storyboard

In iOS 8 (e versioni successive) lo sviluppatore può creare uno speciale storyboard unificato per fornire la schermata di avvio anziché usare una o più immagini di avvio statiche. Quando si crea uno storyboard di avvio in iOS designer, usare le classi di dimensioni e il layout automatico per definire layout diversi per diversi ambienti di visualizzazione. Con le classi di dimensioni e il layout automatico, lo sviluppatore può creare una singola schermata di avvio con un aspetto corretto su tutti i dispositivi e gli ambienti di visualizzazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. In Visual Studio per Mac creare un nuovo progetto selezionando **File > nuova soluzione** e scegliendo **app visualizzazione singola**: 

    ![Finestra nuovo progetto con app visualizzazione singola selezionata](launch-screens-images/launch01.png)

    - Per impostazione predefinita, un nuovo progetto include un file **LaunchScreen. Storyboard** che definisce l'interfaccia della schermata di avvio. 
    - Per aggiungere invece uno storyboard della schermata di avvio a un progetto esistente, fare clic con il pulsante destro del mouse sul nome del progetto nella **riquadro della soluzione** e scegliere **Aggiungi > nuovo file** e quindi selezionare **schermata di avvio**:

    ![La finestra nuovo file con la schermata di avvio di iOS selezionata](launch-screens-images/launch01b.png)

    - Denominare il file **LaunchScreen** o un altro nome a scelta.

2. Configurare il progetto in modo da usare lo storyboard appropriato per la schermata di avvio:

    - Fare doppio clic sul file **info. plist** nel **riquadro della soluzione** per aprirlo per la modifica.
    - Nella sezione **Launch images (immagini di avvio** ) assicurarsi che la **schermata di avvio** sia impostata sul nome dello storyboard appropriato:

    ![Selettore di schermata di avvio in info. plist](launch-screens-images/launch02.png)

    - Per impostazione predefinita, un nuovo progetto è configurato per l'uso di **LaunchScreen. Storyboard** come schermata di avvio.

3. Aggiungere un'immagine al catalogo delle **risorse assets. xcassets** in modo che sia disponibile per l'uso nella schermata di avvio. Per ulteriori informazioni, vedere la sezione [aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) di [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

4. Aprire **LaunchScreen. Storyboard** per la modifica facendo doppio clic su di esso nel **riquadro della soluzione**.

5. Scegliere un dispositivo e un orientamento per visualizzare l'anteprima dello storyboard della schermata di avvio in iOS designer. Aprire il pannello di selezione del dispositivo sulla barra degli strumenti in basso e selezionare **iPhone 4S** e **verticale**.

    ![Barra degli strumenti selezione dispositivo](launch-screens-images/launch05.png)

    - Si noti che la selezione di un dispositivo e l'orientamento cambiano solo il modo in cui iOS designer Visualizza l'anteprima della progettazione. Indipendentemente dalla selezione effettuata, i vincoli appena aggiunti vengono applicati a tutti i dispositivi e gli orientamenti, a meno che non sia stato usato il pulsante **modifica tratti** per specificare diversamente. 

6. Imposta il colore di **sfondo** della visualizzazione principale del controller di visualizzazione. Selezionare la visualizzazione facendo clic al centro del controller di visualizzazione e modificare il colore di sfondo usando il **riquadro delle proprietà**:

    ![Una singola visualizzazione con un colore di sfondo viola](launch-screens-images/launch06.png)

7. Aggiungere una **visualizzazione immagine** alla schermata di avvio e impostare l' **immagine**di origine:

    - Trascinare una **visualizzazione immagine** dal **riquadro Casella degli strumenti** al centro della visualizzazione.
    - Con la **visualizzazione immagine** selezionata, nella sezione **widget** del **riquadro delle proprietà** impostare la proprietà **immagine** sul set di immagini già aggiunto al catalogo asset **. xcassets** asset. Riposizionare e ridimensionare la **visualizzazione immagine** come richiesto:
    
    ![Visualizzazione immagine con la relativa proprietà Image impostata](launch-screens-images/launch07.png)

8. Aggiungere un' **etichetta** sotto la **visualizzazione immagine** e usare la **riquadro delle proprietà** per impostarne gli attributi: 

    ![Etichetta con il testo e il set di colori](launch-screens-images/launch08.png)

9. Passare alla modalità di modifica dei vincoli usando il pulsante destro della **barra degli strumenti vincoli**:
    
    ![Pulsante modalità di modifica vincoli](launch-screens-images/launch09.png)

10. Aggiungere vincoli alla **visualizzazione immagine**, impostarne l'altezza e la larghezza e centrarla orizzontalmente e verticalmente:

    ![Visualizzazione immagini con vincoli di layout](launch-screens-images/launch10.png)

    - Per altri dettagli su come aggiungere vincoli, vedere [layout automatico con il Xamarin designer per iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Aggiungere vincoli all' **etichetta**, centrarla orizzontalmente, assegnarle un'altezza e una larghezza e posizionarla a una distanza fissa verticalmente dalla visualizzazione dell' **immagine**:

    ![Etichetta con vincoli di layout](launch-screens-images/launch11.png)

12. Testare altri dispositivi e orientamenti per verificare che la progettazione appaia come previsto in tutti gli scenari. Nei casi in cui è necessario apportare modifiche per un dispositivo o un orientamento specifico, usare il pulsante **modifica tratti** per aggiungere i vincoli per classi di dimensioni specifiche:

    ![Schermata di avvio sottoposta a rendering come iPhone X con orientamento orizzontale](launch-screens-images/launch12.png)

13. Salvare le modifiche apportate allo storyboard. Eseguire l'app in un simulatore o un dispositivo e la schermata di avvio sarà visibile durante l'avvio dell'app.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Creare un nuovo progetto. In Visual Studio selezionare **File > nuovo > progetto > Visual C# > iPhone & iPad > app iOS (Novell)** :

    ![La finestra nuovo progetto con app iOS (Novell) selezionata](launch-screens-images/launch01.w157.png)

    Selezionare il modello **app visualizzazione singola** , quindi fare clic su **OK**:

    ![Modello app visualizzazione singola](launch-screens-images/launch01-2.w157.png)

2. Se nel **Esplora soluzioni**esiste una **risorsa > LaunchScreen. xib** , eliminarla facendo clic con il pulsante destro del mouse sul file e scegliendo **Elimina**. Il file verrà sostituito con uno storyboard nel passaggio successivo.

3. Creare uno storyboard da usare come schermata di avvio. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > nuovo elemento...** seguito da **storyboard vuoto**. Assegnare a questo storyboard il nome **LaunchScreen. Storyboard** e fare clic su **Aggiungi**:

    ![Finestra Aggiungi nuovo elemento con storyboard vuoto selezionato](launch-screens-images/launch03.w157.png)

4. Configurare il progetto per l'uso di **LaunchScreen. Storyboard** come storyboard della schermata di avvio:

    - Fare doppio clic sul file **Info.plist** in **Esplora soluzioni** per aprirlo e modificarlo. 
    - Nella scheda **Asset visivi** impostare **schermata di avvio** su **LaunchScreen**.

    ![Selettore di schermata di avvio in info. plist](launch-screens-images/launch04-vs.png)

5. Aggiungere un'immagine a un catalogo asset nel progetto in modo che sia disponibile per l'uso nella schermata di avvio:

    - Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse su **cataloghi asset** e scegliere **Aggiungi catalogo asset**. Assegnare un nome al nuovo **asset del catalogo asset**:

    ![Finestra Aggiungi nuovo elemento con catalogo asset selezionato](launch-screens-images/launch05.w157.png)

    - Aggiungere un nuovo set di **Immagini al catalogo asset asset** , come descritto nella sezione [aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) di [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

6. Aprire **LaunchScreen. Storyboard** per la modifica facendo doppio clic su di esso nel **Esplora soluzioni**.

    - Per modificare un file storyboard, Visual Studio richiede una connessione attiva a un host di compilazione Mac. Per informazioni dettagliate, vedere la Guida [connessione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) .

7. Scegliere un dispositivo e un orientamento per visualizzare l'anteprima dello storyboard della schermata di avvio in iOS designer. Aprire il pannello di selezione del dispositivo sulla barra degli strumenti in basso e selezionare **iPhone 4S** e **verticale**: 

    ![Barra degli strumenti selezione dispositivo](launch-screens-images/launch07-vs.png)

    - Si noti che la selezione di un dispositivo e l'orientamento cambiano solo il modo in cui iOS designer Visualizza l'anteprima della progettazione. Indipendentemente dalla selezione effettuata, i vincoli appena aggiunti vengono applicati a tutti i dispositivi e gli orientamenti, a meno che non sia stato usato il pulsante **modifica tratti** per specificare diversamente. 

8. Per aggiungere un **controller di visualizzazione** allo storyboard, trascinarne uno dalla **casella degli strumenti** nell'area di progettazione: 

    ![Un controller di visualizzazione vuoto è stato aggiunto all'area di progettazione](launch-screens-images/launch08-vs.png)

9. Imposta il colore di **sfondo** della visualizzazione principale del controller di visualizzazione. Selezionare la visualizzazione facendo clic al centro del controller di visualizzazione e modificare il colore di sfondo utilizzando la **finestra Proprietà**:
    
    ![Una singola visualizzazione con un colore di sfondo viola](launch-screens-images/launch09-vs.png)

10. Aggiungere una **visualizzazione immagine** alla schermata di avvio e impostare l' **immagine**di origine:

    - Trascinare una **visualizzazione immagine** dalla **casella degli strumenti** al centro della visualizzazione.
    - Con la **visualizzazione immagini** ancora selezionata, nella sezione **widget** della **finestra Proprietà** impostare la proprietà **immagine** sul set di immagini già **aggiunto al catalogo asset asset** . Riposizionare e ridimensionare la **visualizzazione immagine** come richiesto:
    
    ![Visualizzazione immagine con la relativa proprietà Image impostata](launch-screens-images/launch10-vs.png)

11. Aggiungere un' **etichetta** sotto la **visualizzazione immagine**:

    - Trascinare un' **etichetta** dalla **casella degli strumenti** nell'area di progettazione, inserendola sotto la **visualizzazione immagine**.
    - Impostare gli attributi per l' **etichetta** utilizzando la **finestra Proprietà**:

    ![Etichetta con il testo e il set di colori](launch-screens-images/launch11-vs.png) 

12. Passare alla modalità di modifica dei vincoli usando il pulsante destro della **barra degli strumenti vincoli**:
    
    ![Pulsante modalità di modifica vincoli](launch-screens-images/launch12-vs.png) 

13. Aggiungere vincoli alla **visualizzazione immagine**, impostarne l'altezza e la larghezza e centrarla orizzontalmente e verticalmente:

    ![Visualizzazione immagini con vincoli di layout](launch-screens-images/launch13-vs.png) 

    - Per informazioni sull'aggiunta di vincoli, vedere [layout automatico con il Xamarin designer per iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Aggiungere vincoli all' **etichetta**, centrarla orizzontalmente, assegnarle un'altezza e una larghezza e posizionarla a una distanza fissa verticalmente dalla visualizzazione dell' **immagine**:
    
    ![Etichetta con vincoli di layout](launch-screens-images/launch14-vs.png) 

15. Testare altri dispositivi e orientamenti per verificare che la progettazione appaia come previsto in tutti gli scenari. Nei casi in cui è necessario apportare modifiche per un dispositivo o un orientamento specifico, usare il pulsante **modifica tratti** per aggiungere i vincoli per classi di dimensioni specifiche:

    ![Schermata di avvio sottoposta a rendering come iPhone X con orientamento orizzontale](launch-screens-images/launch15-vs.png) 

16. Salvare le modifiche apportate allo storyboard. Eseguire l'app in un simulatore o un dispositivo e la schermata di avvio sarà visibile durante l'avvio dell'app.

-----

> [!NOTE]
> Uno storyboard usato come schermata di avvio _deve_ includere solo elementi dell'interfaccia utente incorporati semplici e **non può** eseguire calcoli o derivare da una classe personalizzata.

Per ulteriori informazioni sulla creazione di una schermata di avvio con uno storyboard unificato, vedere la sezione [schermate di avvio dinamico](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) della Guida per gli [storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="migrating-to-launch-screen-storyboards"></a>Migrazione agli storyboard della schermata di avvio

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Quando si aggiorna un'app esistente per usare gli storyboard per le schermate di avvio, fare clic con il pulsante destro del mouse sul **nome del progetto** nella **Esplora soluzioni** e scegliere **Aggiungi** > **nuovo file.** Selezionare **iOS** > **schermata di avvio** e fare clic sul pulsante **nuovo** :

![](launch-screens-images/storyboard02.png "Select an iOS Launch Screen")

Fare quindi doppio clic sul file `Info.plist` nel **Esplora soluzioni** per aprirlo per la modifica. In **schermata di avvio**selezionare il nuovo file storyboard creato in precedenza.

![](launch-screens-images/storyboard09.png "Select the new Storyboard file created above")

Per usare il nuovo storyboard come schermata di avvio, seguire questa procedura:

1. Fare doppio clic sul file `Info.plist` nel **Esplora soluzioni** per aprirlo per la modifica.
2. Scorrere fino alla sezione **Immagini di avvio universale** dell'editor, aprire l'elenco a discesa **Avvia schermata** e selezionare il nome dello storyboard creato in precedenza: 

    ![](launch-screens-images/storyboard08.png "Setting the launch screen to the storyboard")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni** e scegliere **Aggiungi** > **nuovo file...** : 

    ![](launch-screens-images/image012.png "Add new file")
2. Immettere un nome per la schermata di avvio e fare clic sul pulsante **Aggiungi** : 

    ![](launch-screens-images/image013.png "Enter a name for the launch screen")
3. Nella **Esplora soluzioni**fare doppio clic sul file dello storyboard appena creato per aprirlo per la modifica.
4. Verificare che la **classe Size** sia impostata su **any: any** e la **vista come** **generica**: 

    ![](launch-screens-images/image016.png "Ensure that the Size Class is set to any:any and the View As is Generic")
5. Assemblare la schermata di avvio da classi di dimensioni, elementi dell'interfaccia utente semplici (ad esempio `UIImageView`) e immagini incluse nel bundle dell'applicazione: 

    ![](launch-screens-images/image017.png "Assembly the launch screen in the iOS Designer")
6. Salvare le modifiche apportate allo storyboard.

-----

## <a name="related-links"></a>Collegamenti correlati

- [Schermate di avvio dinamico (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Nozioni di base su iOS Designer](~/ios/user-interface/designer/index.md)
- [Aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Layout automatico con il Xamarin Designer per iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Linee guida per l'interfaccia umana: schermata di avvio](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
