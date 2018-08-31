---
title: Usando lo strumento della Pipeline MonoGame
description: Lo strumento della Pipeline MonoGame consente di creare e gestire progetti di contenuto MonoGame. I file nei progetti di contenuto sono elaborati dallo strumento MonoGame Pipeline e restituiti come file .xnb utilizzabili nelle applicazioni CocosSharp e MonoGame.
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 347cb7e9d417f97cb6e8d78e67b1c76a378cd188
ms.sourcegitcommit: 7ffbecf4a44c204a3fce2a7fb6a3f815ac6ffa21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "34783297"
---
# <a name="using-the-monogame-pipeline-tool"></a>Usando lo strumento della Pipeline MonoGame

_Lo strumento della Pipeline MonoGame consente di creare e gestire progetti di contenuto MonoGame. I file nei progetti di contenuto sono elaborati dallo strumento MonoGame Pipeline e restituiti come file .xnb utilizzabili nelle applicazioni CocosSharp e MonoGame._

Lo strumento della Pipeline MonoGame offre un ambiente semplice da usare per la conversione di file di contenuto in **.xnb** file utilizzabili nelle applicazioni CocosSharp e MonoGame. Per informazioni sulla pipeline di contenuto e per quale motivo sono utili per lo sviluppo di giochi, vedere [questa introduzione sulle pipeline di contenuti](~/graphics-games/cocossharp/content-pipeline/introduction.md)

Questa procedura dettagliata verrà trattati gli argomenti seguenti:

 - Installazione dello strumento di Pipeline MonoGame
 - Creazione di un progetto CocosSharp
 - Creazione di un progetto content.
 - L'elaborazione dei file dello strumento Pipeline MonoGame
 - Uso di file in fase di esecuzione

Questa procedura dettagliata Usa un progetto CocosSharp per illustrare la modalità **.xnb** file possono essere caricati e usati in un'applicazione. Gli utenti di MonoGame anche sarà in grado di fare riferimento a questa procedura dettagliata come CocosSharp e MonoGame usano entrambi lo stesso **.xnb** file di contenuto.

L'app finita visualizzerà un sprite singolo visualizzazione di una trama da un **.xnb** file e una singola etichetta di visualizzazione di un tipo di carattere sprite da un' **.xnb** file:

![](walkthrough-images/image1.png "L'app finita visualizzerà un sprite singolo visualizzazione di una trama da un file .xnb")


## <a name="monogame-pipeline-tool-discussion"></a>Discussione dello strumento di Pipeline MonoGame

Lo strumento di Pipeline MonoGame è disponibile in Windows, OS X e Linux. Questa procedura dettagliata verrà eseguito lo strumento in Windows, ma può essere seguito su Mac e Linux nonché. Per informazioni su come ottenere lo strumento impostato su Linux o Max, vedere [questa pagina](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/).

È in grado di creare contenuto per le applicazioni iOS anche quando in esecuzione su Windows, pertanto agli sviluppatori di utilizzare lo strumento della Pipeline MonoGame [Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md) potranno continuare a sviluppare su Windows.


## <a name="installing-the-monogame-pipeline-tool"></a>Installazione dello strumento di Pipeline MonoGame

Verrà avviato mediante l'installazione di MonoGame, che include la Pipeline di contenuti MonoGame. Si noti che la Pipeline di contenuti MonoGame è un download distinto per Mac. Tutti i programmi di installazione MonoGame è reperibile nella [pagina dei download MonoGame](http://www.monogame.net/downloads/). È possibile scaricare MonoGame per Visual Studio, ma una volta installato lo sviluppatore può utilizzare MonoGame in Visual Studio per Mac troppo:

![](walkthrough-images/image2.png "Scaricare MonoGame per Visual Studio, ma una volta installata anche l'uso di developer MonoGame in Visual Studio per Mac")

Al termine del download, verrà eseguito tramite il programma di installazione e accettare le opzioni predefinite. Al termine dell'esecuzione il programma di installazione, lo strumento di Pipeline MonoGame è installato e sono reperibili nella ricerca dal menu Start:

![](walkthrough-images/image3.png "Al termine, il programma di installazione viene installato lo strumento di Pipeline MonoGame e sono reperibili nella ricerca dal menu Start")

Avviare lo strumento della Pipeline MonoGame:

![](walkthrough-images/image4.png "Avviare lo strumento della Pipeline MonoGame")

Lo strumento della Pipeline MonoGame è in esecuzione, è possibile iniziare a rendere i nostri progetti giochi e contenuti.


## <a name="creating-an-empty-cocossharp-project"></a>Creazione di un progetto CocosSharp vuoto

Il passaggio successivo consiste nel creare un progetto CocosSharp. È importante creare innanzitutto il progetto CocosSharp in modo che è possibile salvare il progetto contenuto nella struttura della cartella creata dal progetto CocosSharp. Per comprendere la struttura di un progetto CocosSharp, esaminiamo il [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), che sarà necessario usare in questa Guida. Tuttavia, se si dispone di un progetto CocosSharp esistente che si desidera aggiungere contenuto al, è possibile usare tale progetto anziché BouncingGame.

Dopo aver creato il progetto, verrà eseguito in modo da verificare che la compilazione e che abbiamo tutto quello che impostato in modo corretto:

![](walkthrough-images/image5.png "Dopo aver creato il progetto, eseguire per verificare che la compilazione e che tutti gli elementi configurati correttamente")


## <a name="creating-a-content-project"></a>Creazione di un progetto content.

Ora che abbiamo un progetto di gioco, è possibile creare un progetto di MonoGame Pipeline. A tale scopo, selezionare lo strumento di Pipeline MonoGame **File > New...**  e passare alla cartella del contenuto del progetto. Per Android, la cartella si trova in **[progetto root]\BouncingGame.Android\Assets\Content\**. Per iOS, la cartella si trova in **[progetto root]\BouncingGame.iOS\Content\**.

Modifica il **nome File** al **ContentProject** e fare clic sul **Salva** pulsante:

![](walkthrough-images/image8.png "Modificare il nome del File in ContentProject e fare clic sul pulsante Salva")

Dopo aver creato il progetto, lo strumento della Pipeline MonoGame verranno visualizzate informazioni relative al progetto quando radice **ContentProject** elemento è selezionato:

![](walkthrough-images/image9.png "Dopo aver creato il progetto, lo strumento della Pipeline MonoGame verranno visualizzate informazioni relative al progetto quando viene selezionato l'elemento ContentProject radice")

È possibile esaminare alcune delle opzioni più importanti per il progetto del contenuto.


### <a name="output-folder"></a>Cartella di output

Questa è la cartella (relativo al progetto contenuto stesso) in cui l'output **.xnb** verranno salvati i file. Per semplicità, si userà la stessa cartella per contenere l'input e i file di output. In altre parole verranno modificate le **cartella di Output** essere **.\**  :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>Piattaforma

Definisce la piattaforma di destinazione per il contenuto. Si noti che si tratta **Windows** per impostazione predefinita, pertanto è opportuno sostituirla con la nostra piattaforma di destinazione che è **Android** (o iOS se in seguito insieme a un progetto iOS).

![](walkthrough-images/image11.png "Si noti che si tratta di Windows per impostazione predefinita, pertanto modificare questa piattaforma di destinazione che è Android o iOS, se in seguito insieme a un progetto iOS")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>L'elaborazione dei file dello strumento Pipeline MonoGame

Successivamente, aggiungeremo il contenuto al nostro **ContentProject**. Per questo progetto, che saranno aggiunti i file nella radice del progetto, ma i progetti di grandi dimensioni verranno in genere organizzare i contenuti in cartelle.

Aggiungiamo due file al progetto:

 - Oggetto **PNG** file che verrà usato per disegnare un sprite. Questo file può [scaricato da qui](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true).
 - Oggetto **.spritefont** file che verrà usato per disegnare testo sullo schermo. Lo strumento della Pipeline del contenuto supporta la creazione di nuovi file .spritefont, in modo che nessun file da scaricare.


### <a name="adding-a-png-file"></a>Aggiunta di un file con estensione png

Per aggiungere un **PNG** file al progetto, è possibile verrà innanzitutto copiarla nella stessa directory del progetto di pipeline, con la **.mgcb** estensione.

![](walkthrough-images/image12.png "Aggiungere un file con estensione png al progetto")

Successivamente, si aggiungerà il file al progetto di pipeline. A tale scopo nello strumento di Pipeline MonoGame selezionare **Modifica > Aggiungi elemento...** , selezionare il **ball.png** del file e fare clic su **Open**. Il file sarà ora parte del progetto del contenuto e, se selezionata, verrà visualizzate le relative proprietà:

![](walkthrough-images/image13.png "Il file sarà ora parte del progetto del contenuto e, se selezionata, verrà visualizzate le relative proprietà")

Descritto in modo da lasciare tutti i valori impostate sui valori predefiniti perché non sono necessarie per caricare il file .xnb in CocosSharp modifiche. È possibile creare il file selezionando il **compilazione > compilare** opzione di menu che consente di avviare una compilazione e visualizzare l'output sulla compilazione. È possibile verificare che la compilazione funzionava correttamente controllando il **contenuto** per una nuova cartella **ball.xnb** file:

![](walkthrough-images/image14.png "Verificare che la compilazione ha funzionato correttamente controllando la cartella del contenuto di un nuovo file ball.xnb")


### <a name="adding-a-spritefont-file"></a>Aggiunta di un file .spritefont

È possibile creare file .spritefont tramite lo strumento della Pipeline MonoGame. CocosSharp richiede tipi di carattere in un **tipi di carattere** cartella e modelli di CocosSharp automaticamente crea automaticamente una cartella di tipi di carattere. È possibile aggiungere questa cartella per lo strumento della Pipeline MonoGame selezionando **Modifica > Aggiungi > cartella esistente...** . Individuare il **contenuto** cartella e selezionare il **tipi di carattere** cartella e fare clic su **OK**:

![](walkthrough-images/browsetofonts.png "Passare alla cartella del contenuto e selezionare la cartella tipi di carattere e fare clic su OK")

Per aggiungere un nuovo file .sprintefont, pulsante destro del mouse sulla cartella tipi di carattere e selezionare **Aggiungi > Nuovo elemento...** , selezionare il **descrizione SpriteFont** opzione, immettere il nome **arial e 36**, fare clic su **Ok**. CocosSharp richiede molto specifica di denominazione dei file del tipo di carattere: devono essere nel formato [FontType]-[FontSize]. Se un tipo di carattere non corrisponde a questo formato di denominazione non venga caricato da CocosSharp in fase di esecuzione.

![](walkthrough-images/image15.png "Se un tipo di carattere non corrisponde a questo formato di denominazione non venga caricato da CocosSharp in fase di esecuzione")

Il file .spritefont è effettivamente un file XML che può essere modificato in qualsiasi editor di testo, tra cui Visual Studio per Mac. Le variabili più comuni modificate in un file .spritefont sono le `FontName` e `Size` proprietà:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

È possibile aprire il file in qualsiasi editor di testo. Come nostro **arial 36.spritefont** suggerito dal nome, verranno mantenute le `FontName` come `Arial` ma modificare il `Size` valore `36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>Uso di file in fase di esecuzione

I file .xnb ora vengono compilati e pronte all'uso nel nostro progetto. Che saranno aggiunti i file a Visual Studio per Mac, quindi si aggiungerà codice al nostro `GameScene.cs` file per caricare i file e visualizzarli.


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>Aggiunta di file .xnb a Visual Studio per Mac

Prima di tutto si aggiungerà i file al progetto. In Visual Studio per Mac, verranno espansi i **BouncingGame.Android** del progetto, espandere il **asset** cartella, pulsante destro del mouse sul **contenuto** cartella, quindi selezionare **Aggiungi > Aggiungi file...** In primo luogo, si selezionerà il **ball.xnb** è stato compilato in precedenza e fare clic su **Open**. Quindi, ripetere i passaggi precedenti, ma aggiungere il **arial 36.xnb** file. Selezioniamo la **mantenere il file nella relativa sottodirectory corrente** se Visual Studio per Mac chiede come aggiungere il file. Una volta terminato di entrambi i file devono far parte del progetto:

![](walkthrough-images/image20.png "Una volta terminato di entrambi i file devono far parte del progetto")


### <a name="adding-gamescenecs"></a>Aggiunta di **GameScene.cs**

Si creerà una classe denominata `GameScene,` che conterrà gli oggetti sprite e testo. A tale scopo, fare clic sui **BouncingGame** (non BouncingGame.Android) del progetto e selezionare **Aggiungi > Nuovo File...** . Selezionare il **generali** categoria, seleziona la **classe vuota** opzione e quindi immettere il nome **GameScene**.

Una volta creato, Modifichiamo il `GameScene.cs` file contenga il codice seguente:


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

È non verrà preso il codice precedente poiché l'uso di CocosSharp oggetti visivi, ad esempio CCSprite e CCLabelTtf viene trattato nel [Guida di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md).

È inoltre necessario aggiungere codice per caricare i nuovi `GameScene`. A tale scopo verrà aperto il `GameAppDelegate.cs` file (che si trova nel **BouncingGame** libreria di classi Portabile) e modificare il `ApplicationDidFinishLaunching` metodo in modo analogo:


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

![](walkthrough-images/image1.png "Quando si esegue, il gioco sarà simile")


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata ha illustrato come utilizzare lo strumento della Pipeline MonoGame per creare file .xnb da un file di input con estensione png, nonché come creare un nuovo file .xnb da un file .sprintefont appena creato. È stato inoltre illustrato come strutturare i progetti di CocosSharp .xnb ai file e su come caricare i file in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Download MonoGame](http://www.monogame.net/downloads/)
- [Documentazione di Pipeline MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
- [Avvio di BouncingGame progetto per Android (esempio)](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [Ball.PNG grafico (esempio)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
