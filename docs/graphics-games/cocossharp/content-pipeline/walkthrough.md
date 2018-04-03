---
title: Utilizzando il MonoGame PipelineTool
description: Lo strumento di Pipeline MonoGame viene utilizzato per creare e gestire i progetti contenuti MonoGame. I file nei progetti di contenuto vengono elaborati dallo strumento di Monogame Pipeline e restituiti come file .xnb per le applicazioni CocosSharp e MonoGame.
ms.topic: article
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 37505b166488230be9d0e0690e415852506664f1
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="using-the-monogame-pipeline-tool"></a>Utilizzando lo strumento di Pipeline MonoGame

_Lo strumento di Pipeline MonoGame viene utilizzato per creare e gestire i progetti contenuti MonoGame. I file nei progetti di contenuto vengono elaborati dallo strumento di Monogame Pipeline e restituiti come file .xnb per le applicazioni CocosSharp e MonoGame._

Lo strumento di Pipeline MonoGame offre un ambiente facile da usare per la conversione di file di contenuto in **.xnb** file da usare in applicazioni CocosSharp e MonoGame. Per informazioni sul contenuto pipeline e perché sono utili per lo sviluppo di giochi, vedere [utile consultare questa introduzione nella pipeline del contenuto](~/graphics-games/cocossharp/content-pipeline/introduction.md)

Questa procedura dettagliata illustra le operazioni seguenti:

 - Installazione dello strumento di Pipeline MonoGame
 - Creazione di un progetto CocosSharp
 - Creazione di un progetto di contenuto
 - L'elaborazione dei file nello strumento di Pipeline MonoGame
 - Utilizzo dei file in fase di esecuzione

Questa procedura dettagliata Usa un progetto CocosSharp per illustrare come **.xnb** file possono essere caricati e usati in un'applicazione. Gli utenti di MonoGame saranno anche possibile fare riferimento a questa procedura dettagliata CocosSharp e MonoGame utilizzano lo stesso **.xnb** file di dati.

L'applicazione finita visualizzerà un singolo sprite la visualizzazione di una trama da una **.xnb** file e una singola etichetta, la visualizzazione di un tipo di carattere sprite da un **.xnb** file:

![](walkthrough-images/image1.png "L'applicazione finita visualizzerà un singolo sprite la visualizzazione di una trama da un file .xnb")


## <a name="monogame-pipeline-tool-discussion"></a>Discussione MonoGame strumento di Pipeline

Lo strumento di Pipeline MonoGame è disponibile in Windows, OS X e Linux. Questa procedura dettagliata, lo strumento viene eseguito in Windows, ma può essere seguito nel Mac e Linux nonché. Per informazioni su come ottenere lo strumento di impostare su Max o Linux, vedere [questa pagina](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/).

È in grado di creare contenuto per le applicazioni iOS anche quando viene eseguito Windows, in questo caso agli sviluppatori che utilizzano lo strumento di Pipeline MonoGame [Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md) sarà possibile continuare a sviluppare in Windows.


## <a name="installing-the-monogame-pipeline-tool"></a>Installazione dello strumento di Pipeline MonoGame

Verrà avviato con l'installazione di MonoGame, che include la Pipeline di contenuto MonoGame. Si noti che la Pipeline di contenuto MonoGame è un download separato per Mac. Tutti i programmi di installazione MonoGame possono trovarsi nel [pagina dei download MonoGame](http://www.monogame.net/downloads/). È possibile scaricare MonoGame per Visual Studio, ma una volta installato lo sviluppatore può utilizzare MonoGame in Visual Studio per Mac troppo:

![](walkthrough-images/image2.png "Scaricare MonoGame per Visual Studio, ma una volta installato l'utilizzo di sviluppatore MonoGame in Visual Studio per Mac troppo")

Una volta scaricata, viene eseguito tramite il programma di installazione e accettare le impostazioni predefinite. Una volta completato il programma di installazione, lo strumento di Pipeline MonoGame è installato ed è reperibile nella ricerca menu Start:

![](walkthrough-images/image3.png "Una volta completato il programma di installazione, viene installato lo strumento di Pipeline MonoGame ed è reperibile nella ricerca menu Start")

Avviare lo strumento di Pipeline MonoGame:

![](walkthrough-images/image4.png "Avviare lo strumento di Pipeline MonoGame")

Lo strumento di Pipeline MonoGame in esecuzione, è possibile avviare rendere i progetti di giochi e il contenuto.


## <a name="creating-an-empty-cocossharp-project"></a>Creazione di un progetto CocosSharp vuoto

Il passaggio successivo consiste nel creare un progetto CocosSharp. È importante creare innanzitutto il progetto CocosSharp in modo che è possibile salvare il progetto contenuto nella struttura di cartelle creata dal progetto CocosSharp. Per comprendere la struttura di un progetto CocosSharp, esaminiamo il [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), che verrà utilizzato in questa Guida. Tuttavia, se si dispone di un progetto CocosSharp esistente che si desidera aggiungere contenuto a, è possibile utilizzare tale progetto anziché BouncingGame.

Dopo aver creato il progetto, viene eseguito in modo da verificare che la compilazione e che abbiamo tutto impostato in modo corretto:

![](walkthrough-images/image5.png "Dopo aver creato il progetto, eseguire per verificare che venga generato e che tutti gli elementi configurati correttamente")


## <a name="creating-a-content-project"></a>Creazione di un progetto di contenuto

Ora che è disponibile un progetto di gioco, è possibile creare un progetto di MonoGame Pipeline. A tale scopo, selezionare lo strumento di Pipeline MonoGame **File > Nuovo...**  e passare alla cartella del contenuto del progetto. Per Android, la cartella si trova in **[progetto root]\BouncingGame.Android\Assets\Content\**. Per iOS, la cartella si trova in **[progetto root]\BouncingGame.iOS\Content\**.

Modifica il **nome File** a **ContentProject** e fare clic su di **salvare** pulsante:

![](walkthrough-images/image8.png "Modificare il nome di File in ContentProject e fare clic sul pulsante Salva")

Dopo la creazione del progetto, lo strumento di Pipeline MonoGame visualizzerà informazioni sul progetto quando radice **ContentProject** elemento è selezionato:

![](walkthrough-images/image9.png "Dopo la creazione del progetto, lo strumento di Pipeline MonoGame visualizzerà informazioni sul progetto quando viene selezionato l'elemento ContentProject radice")

Ecco alcune delle opzioni più importanti per il progetto contenuto.


### <a name="output-folder"></a>Cartella di output

Questa è la cartella (relativo al progetto contenuto stesso) in cui l'output **.xnb** verranno salvati i file. Per semplicità, useremo nella stessa cartella per contenere l'input e i file di output. In altre parole verranno modificate le **cartella di Output** da **.\**  :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>Piattaforma

Definisce la piattaforma di destinazione per il contenuto. Si noti che questo è **Windows** per impostazione predefinita, pertanto sarà necessario modificare questa impostazione con la piattaforma di destinazione, ovvero **Android** (o iOS, se in seguito, insieme a un progetto iOS).

![](walkthrough-images/image11.png "Si noti che si tratta di Windows per impostazione predefinita, quindi impostare la piattaforma di destinazione è Android o iOS, se in seguito, insieme a un progetto iOS")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>L'elaborazione dei file nello strumento di Pipeline MonoGame

Successivamente, aggiungeremo il contenuto per il nostro **ContentProject**. Per questo progetto, i file verranno aggiunte alla radice del progetto, ma il relativo contenuto in cartelle in genere organizzazione dei progetti di grandi dimensioni.

Due file verrà aggiunto al progetto:

 - Oggetto **PNG** file che verrà utilizzato per disegnare un sprite. Questo file può [scaricato da qui](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true).
 - Oggetto **.spritefont** file che verrà utilizzato per disegnare testo sullo schermo. Lo strumento di Pipeline contenuto supporta la creazione di nuovi file .spritefont, pertanto non esiste nessun file da scaricare.


### <a name="adding-a-png-file"></a>Aggiunta di un file con estensione png

Per aggiungere un **PNG** file al progetto, si verrà innanzitutto copiarlo nella stessa directory del progetto di pipeline, che ha il **.mgcb** estensione.

![](walkthrough-images/image12.png "Aggiungere al progetto un file con estensione png")

Successivamente, aggiungeremo il file per il progetto di pipeline. A tale scopo, nello strumento di Pipeline MonoGame, selezionare **Modifica > Aggiungi elemento...** , selezionare il **ball.png** file e fare clic su **aprire**. Il file sarà parte del progetto di contenuto e, se selezionata, verrà visualizzate le relative proprietà:

![](walkthrough-images/image13.png "Il file sarà parte del progetto di contenuto e, se selezionata, verrà visualizzate le relative proprietà")

Ti invieremo un corso lasciando tutti i valori impostate sui valori predefiniti non sono necessarie per caricare il file .xnb in CocosSharp modifiche. È possibile compilare il file selezionando il **compilazione > compilare** opzione di menu, che consente di avviare una compilazione e di visualizzare l'output di compilazione. È possibile verificare che la compilazione sia stato risolto correttamente controllando il **contenuto** cartella per un nuovo **ball.xnb** file:

![](walkthrough-images/image14.png "Verificare che la compilazione ha funzionato correttamente controllando la cartella del contenuto di un nuovo file ball.xnb")


### <a name="adding-a-spritefont-file"></a>Aggiunta di un file .spritefont

Possiamo creare il file .spritefont tramite lo strumento di Pipeline MonoGame. CocosSharp richiede tipi di carattere in un **caratteri** cartella e i modelli di CocosSharp automaticamente crea automaticamente una cartella di tipi di carattere. È possibile aggiungere questa cartella per lo strumento di Pipeline MonoGame selezionando **Modifica > Aggiungi > cartella esistente...** . Individuare il **contenuto** cartella e selezionare il **caratteri** cartella e fare clic su **OK**:

![](walkthrough-images/browsetofonts.png "Passare alla cartella del contenuto e selezionare la cartella di tipi di carattere e fare clic su OK")

Per aggiungere un nuovo file .sprintefont, fare clic sulla cartella dei tipi di carattere e selezionare **Aggiungi > Nuovo elemento...** , selezionare il **SpriteFont descrizione** opzione, immettere il nome **arial 36**, fare clic su **Ok**. CocosSharp richiede molto specifico di denominazione dei file di caratteri: devono essere nel formato [FontType]-[FontSize]. Se un tipo di carattere non corrisponde a questo formato di denominazione non venga caricato da CocosSharp in fase di esecuzione.

![](walkthrough-images/image15.png "Se un tipo di carattere non corrisponde a questo formato di denominazione non venga caricato da CocosSharp in fase di esecuzione")

Il file .spritefont è effettivamente un file XML che può essere modificato in qualsiasi editor di testo, tra cui Visual Studio per Mac. Le variabili più comuni modificate in un file .spritefont sono il `FontName` e `Size` proprietà:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

È possibile aprire il file in qualsiasi editor di testo. Come il nostro **arial 36.spritefont** suggerito dal nome, lasciare il `FontName` come `Arial` ma modificare il `Size` valore `36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>Utilizzo dei file in fase di esecuzione

I file .xnb ora vengono compilati e pronto per l'uso nel progetto. Aggiungere i file a Visual Studio per Mac, quindi verrà aggiunto codice al nostro `GameScene.cs` file da caricare i file e visualizzarli.


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>Aggiunta di file .xnb a Visual Studio per Mac

Prima di tutto i file verrà aggiunto al progetto. In Visual Studio per Mac, è possibile espandere il **BouncingGame.Android** del progetto, espandere il **asset** pulsante destro del mouse nella cartella la **contenuto** cartella e selezionare **Aggiungere > aggiungere i file...** In primo luogo, si selezionerà il **ball.xnb** è stato compilato in precedenza e fare clic su **aprire**. Quindi ripetere i passaggi precedenti, ma è aggiungere il **arial 36.xnb** file. Si selezionerà il **mantenere il file nella sottodirectory corrente** opzione se Visual Studio per Mac chiede come aggiungere il file. Una volta terminato di entrambi i file devono far parte del progetto:

![](walkthrough-images/image20.png "Una volta terminato di entrambi i file devono essere parte del progetto")


### <a name="adding-gamescenecs"></a>Aggiunta di **GameScene.cs**

Verrà creata una classe denominata `GameScene,` che conterrà gli oggetti sprite e testo. A tale scopo, fare clic su di **BouncingGame** (non BouncingGame.Android) del progetto e selezionare **Aggiungi > Nuovo File...** . Selezionare il **generale** categoria, seleziona il **classe vuota** opzione e quindi immettere il nome **GameScene**.

Una volta creata, verrà modificata la `GameScene.cs` file contenente il codice seguente:


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

È non essere che illustrano il codice precedente dall'utilizzo di oggetti visivi CocosSharp CCSprite e CCLabelTtf è trattata nella [BouncingGame Guida](~/graphics-games/cocossharp/bouncing-game.md).

È inoltre necessario aggiungere codice per caricare il nuovo `GameScene`. Per eseguire questa operazione verrà aperto il `GameAppDelegate.cs` file (che si trova nella **BouncingGame** PCL) e modificare il `ApplicationDidFinishLaunching` metodo nel modo seguente:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

Quando si esegue, il gioco sarà simile:

![](walkthrough-images/image1.png "Quando si esegue, il gioco avrà un aspetto simile")


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata è stato illustrato come utilizzare lo strumento di Pipeline MonoGame per creare file .xnb da un file di input con estensione png, nonché come creare un nuovo file .xnb da un file .sprintefont appena creato. È stato inoltre illustrato come definire la struttura CocosSharp progetti per utilizzare file .xnb e su come caricare questi file in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Download di MonoGame](http://www.monogame.net/downloads/)
- [Documentazione MonoGame Pipeline](http://www.monogame.net/documentation/?page=Pipeline)
- [Progetto iniziale di BouncingGame per Android (esempio)](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [Ball.PNG immagine (esempio)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
