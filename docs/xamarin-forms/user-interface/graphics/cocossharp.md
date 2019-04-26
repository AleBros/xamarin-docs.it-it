---
title: Uso di CocosSharp in xamarin. Forms
description: CocosSharp può essere utilizzato per aggiungere la forma esatta, l'immagine e il rendering del testo a un'applicazione per la visualizzazione avanzata
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 55e5627a492636efaa6eeb6b8c302d143b08e2cc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400704"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Uso di CocosSharp in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)

_CocosSharp può essere utilizzato per aggiungere la forma esatta, l'immagine e il rendering del testo a un'applicazione per la visualizzazione avanzata_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evoluzione 2016: Cocos # in xamarin. Forms**

## <a name="overview"></a>Panoramica

CocosSharp è una tecnologia potente e flessibile per la visualizzazione di grafica, la lettura dell'input tocco, la riproduzione di audio e la gestione dei contenuti. Questa guida illustra come aggiungere CocosSharp a un'applicazione xamarin. Forms. Viene descritto come il seguente:

* [Che cos'è CocosSharp?](#what)
* [Aggiunta di pacchetti CocosSharp Nuget](#nuget)
* [Procedura dettagliata: Aggiunta di CocosSharp in un'app xamarin. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>Che cos'è CocosSharp?

[CocosSharp](~/graphics-games/cocossharp/index.md) è un motore di gioco open source che è disponibile nella piattaforma Xamarin.
CocosSharp è una libreria di runtime efficiente che include le funzionalità seguenti:

* Per il rendering di immagini usando il [CCSprite classe](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* Forme per il rendering tramite il [classe CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* Ogni fotogramma per la logica usando il [CCNode.Schedule (metodo)](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* Gestione dei contenuti (caricamento e scaricamento delle risorse, ad esempio file con estensione png) usando il [CCTextureCache classe](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* Le animazioni utilizzando il [classe CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

Obiettivo principale di CocosSharp consiste nel semplificare la creazione di giochi 2D multipiattaforma; Tuttavia, può essere anche un'aggiunta importante per le applicazioni Xamarin Form. Poiché i giochi richiedono in genere un rendering efficiente e un controllo preciso su oggetti visivi, CocosSharp è utilizzabile per aggiungere potenti funzionalità di visualizzazione e gli effetti alle applicazioni non di gioco.

Xamarin. Forms si basa su sistemi dell'interfaccia utente nativi specifici della piattaforma. Ad esempio, [ `Button`s](xref:Xamarin.Forms.Button) vengono visualizzati in modo diverso in iOS e Android e persino potrebbe differire dalle versioni del sistema operativo. Al contrario, CocosSharp non usare oggetti visivi specifici della piattaforma, in modo che tutti gli oggetti visivi sembrano identici in tutte le piattaforme. Naturalmente, la risoluzione e le proporzioni differiscono tra i dispositivi e ciò può influire sul modo in cui CocosSharp esegue il rendering dei rispettivi oggetti visivi. Questi dettagli verranno descritte più avanti in questa Guida.

Per ulteriori informazioni sono reperibili nel [CocosSharp sezione](~/graphics-games/cocossharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Aggiunta di pacchetti CocosSharp Nuget

Prima di usare CocosSharp, gli sviluppatori devono effettuare alcune aggiunte al progetto xamarin. Forms.
Questa guida si presuppone che un progetto xamarin. Forms con un iOS, Android e .NET Standard progetto di libreria.
Tutto il codice verrà scritto nel progetto della libreria .NET Standard. Tuttavia, è necessario aggiungere le librerie per i progetti iOS e Android.

Il pacchetto Nuget di CocosSharp contiene tutti gli oggetti necessari per creare oggetti CocosSharp.
Il pacchetto nuget CocosSharp.Forms include il `CocosSharpView` classe, che viene usata per ospitare CocosSharp in xamarin. Forms.
Aggiungere il **CocosSharp.Forms** NuGet e **CocosSharp** verranno aggiunti automaticamente anche.
A tale scopo, fare clic sui <span class="UIItem">pacchetti</span> cartella nel progetto di libreria .NET Standard e selezionare <span class="UIItem">Aggiungi pacchetti... </span>. Immettere il termine di ricerca <span class="UIItem">CocosSharp.Forms</span>, selezionare <span class="UIItem">CocosSharp per xamarin. Forms</span>, quindi fare clic su <span class="UIItem">Add Package</span>.

![](cocossharp-images/image1.png "Aggiungi finestra di dialogo di pacchetti")

Entrambe **CocosSharp** e **CocosSharp.Forms** verranno aggiunti i pacchetti NuGet al progetto:

![](cocossharp-images/image2.png "Cartella dei pacchetti")

Ripetere i passaggi precedenti per progetti specifici della piattaforma (ad esempio iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Procedura dettagliata: Aggiunta di CocosSharp in un'app xamarin. Forms

Seguire questi passaggi per aggiungere una visualizzazione semplice di CocosSharp in un'app xamarin. Forms:

1. [Creazione di un Xamarin Forms pagina](#1)
1. [Aggiunta di un CocosSharpView](#2)
1. [Creazione di GameScene](#3)
1. [Aggiunta di un cerchio](#4)
1. [L'interazione con CocosSharp](#5)

Dopo che è stato aggiunto correttamente una vista di CocosSharp in un'app xamarin. Forms, visitare il [CocosSharp documentazione](~/graphics-games/cocossharp/index.md) per altre informazioni sulla creazione del contenuto con CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Creazione di un Xamarin Forms pagina

CocosSharp possono essere ospitati in qualsiasi contenitore di xamarin. Forms. In questo esempio per questa pagina viene utilizzata una pagina denominata `HomePage`. `HomePage` viene divisa in metà da un `Grid` per mostrare come xamarin. Forms CocosSharp può eseguire il rendering e contemporaneamente nella stessa pagina.

Innanzitutto, impostare la pagina in modo che contenga una `Grid` e due `Button` istanze:


```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

In iOS il `HomePage` viene visualizzato come illustrato nell'immagine seguente:

![](cocossharp-images/image3.png "Screenshot della home page")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. Aggiunta di un CocosSharpView

Il `CocosSharpView` classe viene utilizzata per incorporare CocosSharp in un'app xamarin. Forms. Poiché `CocosSharpView` eredita le [deriva](xref:Xamarin.Forms.View) (classe), offre un'interfaccia familiare per il layout e può essere utilizzato all'interno di contenitori di layout, ad esempio [Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid). Aggiungere un nuovo `CocosSharpView` al progetto completando il `CreateTopHalf` metodo:


```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

Inizializzazione CocosSharp non è immediato, quindi, registrare un evento quando il `CocosSharpView` ha completato la sua creazione. Eseguire questa operazione nel `HandleViewCreated` metodo:


```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

Il `HandleViewCreated` metodo ha due informazioni importanti che abbiamo verranno analizzati. Il primo è il `GameScene` (classe), che verrà creato nella sezione successiva. È importante notare che l'app non verrà compilato finché il `GameScene` viene creato e `gameScene` riferimento a un'istanza viene risolta.

Il secondo dettaglio importante è il `DesignResolution` proprietà, che definisce l'area visibile del gioco per gli oggetti CocosSharp. Il `DesignResolution` proprietà da esaminare dopo aver creato `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Creazione di GameScene

Il `GameScene` classe eredita da di CocosSharp `CCScene`. `GameScene` è il primo punto in cui lo gestiamo esclusivamente con CocosSharp. Il codice contenuto `GameScene` funzionerà in qualsiasi app CocosSharp, se lo si trova all'interno di un progetto xamarin. Forms o No.

Il `CCScene` classe è l'oggetto visivo radice di tutti i rendering CocosSharp. Qualsiasi oggetto CocosSharp visibile deve essere contenuto all'interno di un `CCScene`. In particolare, gli oggetti visivi devono essere aggiunto al `CCLayer` istanze e quelle `CCLayer` istanze devono essere aggiunto a un `CCScene`.

Il grafico seguente può essere utile visualizzare una gerarchia di CocosSharp tipica:

![](cocossharp-images/image4.png "Tipica gerarchia CocosSharp")

Un solo `CCScene` possono essere attivi contemporaneamente. La maggior parte dei giochi usano più `CCLayer` istanze per ordinare il contenuto, ma l'applicazione utilizza solo uno. Analogamente, la maggior parte dei giochi utilizzano più oggetti visivi, ma sarà ne abbiamo solo uno nell'app. Più dettagliata e approfondita di CocosSharp gerarchia visiva è reperibile nella [procedura dettagliata di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md).

Inizialmente il `GameScene` classe sarà quasi vuota, ci limiteremo a creare in modo da soddisfare il riferimento in `HomePage`. Aggiungere una nuova classe al progetto della libreria .NET Standard denominato `GameScene`. Il controller deve ereditare dal `CCScene` classe come indicato di seguito:


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

A questo punto `GameScene` è definito, è possibile tornare a `HomePage` e aggiungere un campo:


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

È ora possibile compilare il progetto ed eseguirlo per vedere CocosSharp in esecuzione. È ancora stato aggiunto nulla al nostro `GameScene,` quindi nella metà superiore della pagina è nero, il colore predefinito di una scena CocosSharp:

![](cocossharp-images/image5.png "GameScene vuoto")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Aggiunta di un cerchio

L'app è attualmente un'istanza del motore di CocosSharp, visualizzazione di un oggetto vuoto in esecuzione `CCScene`. Successivamente, verrà aggiunto un oggetto visivo: un cerchio. Il `CCDrawNode` classe può essere utilizzata per disegnare un'ampia gamma di forme geometriche, come descritto nel [disegno della geometria con CCDrawNode Guida](~/graphics-games/cocossharp/ccdrawnode.md).

Aggiungere un cerchio al nostro `GameScene` classe e crearne un'istanza nel costruttore come illustrato nel codice seguente:


```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

A questo punto l'esecuzione dell'app Mostra un cerchio sul lato sinistro dell'area di visualizzazione CocosSharp:

![](cocossharp-images/image6.png "Cerchio in GameScene")


#### <a name="understanding-designresolution"></a>Understanding DesignResolution

Ora che viene visualizzato un oggetto visivo CocosSharp, è possibile analizzare il `DesignResolution` proprietà.

Il `DesignResolution` rappresenta la larghezza e altezza dell'area di CocosSharp per posizionare e ridimensionare gli oggetti. La risoluzione effettiva dell'area viene misurata in *pixel* mentre il `DesignResolution` viene misurato nel mondo *unità*. Il diagramma seguente illustra la risoluzione delle varie parti della visualizzazione come visualizzato in un iPhone 5 con una risoluzione dello schermo pari a 640 x 1136 pixel:

![](cocossharp-images/image7.png "iPhone 5s la risoluzione di progettazione")

Il diagramma precedente visualizza le dimensioni in pixel all'esterno della schermata di testo di colore nero. Unità vengono visualizzate all'interno del diagramma nel testo bianco. Ecco alcuni dettagli importanti visualizzati sopra:

* L'origine dello schermo CocosSharp è in basso a sinistra. Spostandosi quindi verso destra aumenta il valore di X, nonché spostando verso l'alto il valore Y. Si noti che il valore Y è invertito rispetto ad alcuni altri motori di layout 2D, in cui (0,0) è in alto a sinistra dell'area di disegno.
* Il comportamento predefinito di CocosSharp consiste nel mantenere le proporzioni della relativa visualizzazione. Poiché la prima riga della griglia è maggiore dell'altezza, CocosSharp non riempie l'intera larghezza della cella, come mostrato dal rettangolo bianco punteggiato. Questo comportamento può essere modificato, come descritto nel [Guida per la gestione di più risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md).
* In questo esempio, si manterrà CocosSharp un'area di visualizzazione di 100 unità di larghezza e altezza indipendentemente dalle dimensioni e le proporzioni del dispositivo. Ciò significa che codice può presupporre che X = 100 è rappresentato dal associato all'estrema destra di CocosSharp visualizzare, layout mantenendo coerenti in tutti i dispositivi.


#### <a name="ccdrawnode-details"></a>Dettagli CCDrawNode

L'app semplice Usa il `CCDrawNode` classe per disegnare un cerchio. Questa classe può essere molto utile per le app aziendali poiché viene fornita per il rendering basato su vettore geometry – una funzionalità non presente in xamarin. Forms. Oltre ai cerchi, il `CCDrawNode` classe può essere utilizzata per disegnare rettangoli, spline, linee e poligoni personalizzati. `CCDrawNode` è anche facile da utilizzare poiché non richiede l'uso dei file di immagine (ad esempio con estensione png). Una discussione più dettagliata di CCDrawNode è reperibile nella [disegno della geometria con CCDrawNode Guida](~/graphics-games/cocossharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. L'interazione con CocosSharp

Gli elementi visivi CocosSharp (come `CCDrawNode`) ereditare il `CCNode` classe. `CCNode` fornisce due proprietà che può essere usata per posizionare un oggetto rispetto al padre: `PositionX` e `PositionY`. Il codice Usa attualmente queste due proprietà per posizionare il centro del cerchio, come illustrato nel frammento di codice seguente:


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

È importante notare che oggetti CocosSharp sono posizionati per valori di posizione espliciti, anziché la maggior parte delle visualizzazioni di xamarin. Forms, che vengono automaticamente posizionati in base al comportamento dei relativi controlli di layout del padre.

Si aggiungerà codice per consentire all'utente di fare clic su uno dei due pulsanti per spostare a sinistra o a destra del cerchio di 10 unità (non in pixel, poiché consente di disegnare il controllo circle nello spazio CocosSharp world unit). Prima di tutto si creerà due metodi pubblici nel `GameScene` classe:


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

Successivamente, si aggiungerà i gestori per i due pulsanti nel `HomePage` per rispondere alla selezione. Al termine, nostro `CreateBottomHalf` metodo contiene il codice seguente:


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

Il cerchio CocosSharp ora consente di spostarsi in risposta ai clic. È possibile osservare anche nettamente i limiti dell'area di disegno CocosSharp spostando il cerchio sufficiente a sinistra o destra:

![](cocossharp-images/image8.png "GameScene con lo spostamento di cerchio")

## <a name="summary"></a>Riepilogo

Questa guida illustra come aggiungere CocosSharp a un xamarin. Forms esistenti del progetto, come creare l'interazione tra xamarin. Forms e CocosSharp e vengono illustrate varie considerazioni quando si creano i layout in CocosSharp.

Il motore di gioco CocosSharp offre numerose funzionalità e la profondità, in modo che questa guida interessa solo alcune delle operazioni che è possibile eseguire CocosSharp. Gli sviluppatori interessati a altre informazioni su CocosSharp possono trovare molti articoli nel [CocosSharp sezione](~/graphics-games/cocossharp/index.md).



## <a name="related-links"></a>Collegamenti correlati

- [API di CocosSharp](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
