---
title: Utilizzo di CocosSharp inXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb2303eb91fe2aa332ed35131baa7f6dd3cfeff5
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129519"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Utilizzo di CocosSharp inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp può essere usato per aggiungere una forma, un'immagine e un rendering del testo precisi a un'applicazione per la visualizzazione avanzata_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016: Cocos # inXamarin.Forms**

## <a name="overview"></a>Panoramica

CocosSharp è una tecnologia potente e flessibile per la visualizzazione di immagini, la lettura di input tocco, la riproduzione di audio e la gestione del contenuto. Questa guida illustra come aggiungere CocosSharp a un' Xamarin.Forms applicazione. Vengono illustrati gli elementi seguenti:

- [Che cos'è CocosSharp?](#what)
- [Aggiunta dei pacchetti NuGet CocosSharp](#nuget)
- [Procedura dettagliata: aggiunta di CocosSharp a un' Xamarin.Forms app](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>Che cos'è CocosSharp?

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) è un motore di gioco open source disponibile nella piattaforma Novell.
CocosSharp è una libreria efficiente in fase di esecuzione che include le seguenti funzionalità:

- Rendering di immagini con la `CCSprite` classe
- Rendering delle forme usando la `CCDrawNode` classe
- Logica di ogni frame che usa la `CCNode.Schedule` classe
- Gestione dei contenuti (caricamento e scaricamento di risorse, ad esempio file con estensione png) con il`CCTextureCache`
- Animazioni con la `CCAction` classe

Lo scopo principale di CocosSharp è semplificare la creazione di giochi 2D multipiattaforma; Tuttavia, può anche essere un'ottima aggiunta alle applicazioni Form Novell. Poiché i giochi richiedono in genere un rendering efficiente e un controllo preciso sugli oggetti visivi, è possibile usare CocosSharp per aggiungere potenti visualizzazioni ed effetti a applicazioni non di gioco.

Xamarin.Formssi basa su sistemi di interfaccia utente nativi, specifici della piattaforma. Ad esempio, [ `Button` i](xref:Xamarin.Forms.Button) vengono visualizzati in modo diverso in iOS e Android e possono anche variare in base alla versione del sistema operativo. Al contrario, CocosSharp non usa oggetti visivi specifici della piattaforma, in modo che tutti gli oggetti visivi siano identici in tutte le piattaforme. Naturalmente, la risoluzione e le proporzioni sono diverse tra i dispositivi e questo può influito sul modo in cui CocosSharp esegue il rendering degli oggetti visivi. Questi dettagli verranno illustrati più avanti in questa guida.

Per informazioni più dettagliate, vedere la [sezione CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Aggiunta dei pacchetti NuGet CocosSharp

Prima di usare CocosSharp, gli sviluppatori devono apportare alcune aggiunte al Xamarin.Forms progetto.
Questa guida presuppone un Xamarin.Forms progetto con un progetto di libreria iOS, Android e .NET standard.
Tutto il codice verrà scritto nel progetto di libreria .NET Standard; Tuttavia, le librerie devono essere aggiunte ai progetti iOS e Android.

Il pacchetto NuGet CocosSharp contiene tutti gli oggetti necessari per creare oggetti CocosSharp.
Il pacchetto NuGet CocosSharp. Forms include la `CocosSharpView` classe, usata per ospitare CocosSharp in Xamarin.Forms .
Aggiungere anche **CocosSharp. Forms** NuGet e **CocosSharp** verranno aggiunti automaticamente.
A tale scopo, fare clic con il pulsante destro del mouse sulla cartella **pacchetti** nel progetto libreria .NET standard e selezionare **Aggiungi pacchetti...**. Immettere il termine di ricerca **CocosSharp. Forms**, selezionare **CocosSharp per Xamarin.Forms **e quindi fare clic su **Aggiungi pacchetto**.

![](cocossharp-images/image1.png "Add Packages Dialog")

Al progetto verranno aggiunti i pacchetti NuGet **CocosSharp** e **CocosSharp. Forms** :

![](cocossharp-images/image2.png "Packages Folder")

Ripetere i passaggi precedenti per i progetti specifici della piattaforma (ad esempio iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Procedura dettagliata: aggiunta di CocosSharp a un' Xamarin.Forms app

Per aggiungere una semplice visualizzazione CocosSharp a un'app, seguire questa procedura Xamarin.Forms :

1. [Creazione di una pagina form Novell](#1)
1. [Aggiunta di un CocosSharpView](#2)
1. [Creazione di GameScene](#3)
1. [Aggiunta di un cerchio](#4)
1. [Interazione con CocosSharp](#5)

Dopo aver aggiunto una visualizzazione CocosSharp a un' Xamarin.Forms app, visitare la documentazione di [CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) per altre informazioni sulla creazione di contenuto con CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. creazione di una pagina Novell Forms

CocosSharp possono essere ospitati in qualsiasi Xamarin.Forms contenitore. Questo esempio per questa pagina usa una pagina denominata `HomePage` . `HomePage`viene suddiviso in metà per un `Grid` per mostrare come è Xamarin.Forms possibile eseguire il rendering simultaneo di CocosSharp nella stessa pagina.

Per prima cosa, configurare la pagina in modo che contenga una `Grid` e due `Button` istanze:

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

In iOS, `HomePage` viene visualizzato come illustrato nell'immagine seguente:

![](cocossharp-images/image3.png "HomePage Screenshot")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. aggiunta di un CocosSharpView

La `CocosSharpView` classe viene usata per incorporare CocosSharp in un' Xamarin.Forms app. Poiché `CocosSharpView` eredita da [ Xamarin.Forms . Visualizza](xref:Xamarin.Forms.View) classe, fornisce un'interfaccia familiare per il layout e può essere usata all'interno di contenitori di layout, ad esempio [ Xamarin.Forms . Griglia](xref:Xamarin.Forms.Grid). Aggiungere un nuovo `CocosSharpView` al progetto completando il `CreateTopHalf` Metodo:

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

L'inizializzazione di CocosSharp non è immediata, quindi registra un evento per quando `CocosSharpView` ha terminato la creazione. Eseguire questa operazione nel `HandleViewCreated` Metodo:

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

Il `HandleViewCreated` metodo ha due dettagli importanti che verranno esaminati. Il primo è la `GameScene` classe, che verrà creata nella sezione successiva. È importante notare che l'app non verrà compilata finché non `GameScene` viene creato e il `gameScene` riferimento all'istanza non viene risolto.

Il secondo dettaglio importante è la `DesignResolution` proprietà, che definisce l'area visibile del gioco per gli oggetti CocosSharp. La `DesignResolution` proprietà verrà esaminata dopo la creazione di `GameScene` .

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. creazione del GameScene

La `GameScene` classe eredita da CocosSharp `CCScene` . `GameScene`è il primo punto in cui si tratta esclusivamente di CocosSharp. Il codice contenuto in `GameScene` funzionerà in qualsiasi app CocosSharp, indipendentemente dal fatto che sia ospitato all'interno di un Xamarin.Forms progetto.

La `CCScene` classe è la radice visiva di tutti i rendering di CocosSharp. Qualsiasi oggetto CocosSharp visibile deve essere contenuto all'interno di un oggetto `CCScene` . In particolare, gli oggetti visivi devono essere aggiunti alle `CCLayer` istanze di e tali `CCLayer` istanze devono essere aggiunte a un `CCScene` .

Il grafico seguente può essere utile per visualizzare una tipica gerarchia di CocosSharp:

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

`CCScene`È possibile attivarne solo una alla volta. La maggior parte dei giochi usa più `CCLayer` istanze per ordinare il contenuto, ma l'applicazione ne usa solo una. Analogamente, la maggior parte dei giochi usa più oggetti visivi, ma ne sarà presente solo una nell'app. Una discussione più dettagliata sulla gerarchia visiva di CocosSharp è disponibile nella [procedura dettagliata di BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inizialmente, la `GameScene` classe sarà quasi vuota. verrà creata solo per soddisfare il riferimento in `HomePage` . Aggiungere una nuova classe al progetto di libreria .NET Standard denominato `GameScene` . Deve ereditare dalla `CCScene` classe come indicato di seguito:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Ora che `GameScene` è definito, è possibile tornare a `HomePage` e aggiungere un campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

È ora possibile compilare il progetto ed eseguirlo per vedere CocosSharp in esecuzione. Non è stato aggiunto alcun elemento al nostro `GameScene,` , quindi la metà superiore della pagina è nera, ovvero il colore predefinito di una scena CocosSharp:

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. aggiunta di un cerchio

L'app dispone attualmente di un'istanza in esecuzione del motore CocosSharp, che visualizza un oggetto vuoto `CCScene` . Successivamente, verrà aggiunto un oggetto visivo: un cerchio. La `CCDrawNode` classe può essere usata per disegnare diverse forme geometriche, come illustrato nella Guida alla geometria di [disegno con CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Aggiungere un cerchio alla `GameScene` classe e crearne un'istanza nel costruttore, come illustrato nel codice seguente:

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

L'esecuzione dell'app mostra ora un cerchio sul lato sinistro dell'area di visualizzazione CocosSharp:

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>Informazioni su DesignResolution

Ora che viene visualizzato un oggetto Visual CocosSharp, è possibile esaminare la `DesignResolution` Proprietà.

L'oggetto `DesignResolution` rappresenta la larghezza e l'altezza dell'area CocosSharp per l'inserimento e il ridimensionamento degli oggetti. La risoluzione effettiva dell'area viene misurata in *pixel* mentre `DesignResolution` è misurata in *unità*internazionali. Il diagramma seguente mostra la risoluzione di diverse parti della vista visualizzate in un iPhone 5 con una risoluzione dello schermo di 640X1136 pixel:

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

Il diagramma precedente Visualizza le dimensioni in pixel all'esterno dello schermo in testo nero. Le unità vengono visualizzate all'interno del diagramma in testo bianco. Di seguito sono riportati alcuni dettagli importanti visualizzati sopra:

- L'origine della visualizzazione CocosSharp si trova in basso a sinistra. Il passaggio a destra aumenta il valore X e lo stato di aumento aumenta il valore Y. Si noti che il valore Y è invertito rispetto ad altri motori di layout 2D, dove (0, 0) è la parte superiore sinistra dell'area di disegno.
- Il comportamento predefinito di CocosSharp è quello di mantenere le proporzioni della relativa visualizzazione. Poiché la prima riga della griglia è più ampia rispetto a quella di altezza, CocosSharp non riempie l'intera larghezza della cella, come illustrato dal rettangolo bianco tratteggiato. Questo comportamento può essere modificato, come descritto nella [Guida gestione di più risoluzioni in CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- In questo esempio, CocosSharp manterrà un'area di visualizzazione di 100 unità di larghezza e altezza indipendentemente dalle dimensioni o dalle proporzioni del dispositivo. Questo significa che il codice può presumere che X = 100 rappresenti il limite di estrema destra della visualizzazione CocosSharp, mantenendo la coerenza del layout in tutti i dispositivi.

#### <a name="ccdrawnode-details"></a>Dettagli CCDrawNode

L'app semplice usa la `CCDrawNode` classe per creare un cerchio. Questa classe può essere molto utile per le app aziendali poiché fornisce il rendering geometrico basato su vettori, una funzionalità mancante da Xamarin.Forms . Oltre ai cerchi, la `CCDrawNode` classe può essere utilizzata per creare rettangoli, spline, linee e poligoni personalizzati. `CCDrawNode`è anche facile da usare perché non richiede l'uso di file di immagine, ad esempio. png. Per una descrizione più dettagliata di CCDrawNode, vedere la sezione [Drawing Geometry with CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. interazione con CocosSharp

Gli elementi visivi CocosSharp (ad esempio `CCDrawNode` ) ereditano dalla `CCNode` classe. `CCNode`in sono disponibili due proprietà che è possibile utilizzare per posizionare un oggetto relativo al padre: `PositionX` e `PositionY` . Il codice usa attualmente queste due proprietà per posizionare il centro del cerchio, come illustrato nel frammento di codice seguente:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

È importante notare che gli oggetti CocosSharp vengono posizionati in base ai valori di posizione espliciti, anziché alla maggior parte delle Xamarin.Forms Visualizzazioni, che vengono posizionati automaticamente in base al comportamento dei controlli del layout padre.

Si aggiungerà il codice per consentire all'utente di fare clic su uno dei due pulsanti per spostare il cerchio verso sinistra o verso destra di 10 unità (non di pixel, perché il cerchio disegna nello spazio dell'unità mondiale CocosSharp). Verranno prima creati due metodi pubblici nella `GameScene` classe:

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

Successivamente, si aggiungeranno i gestori ai due pulsanti in `HomePage` per rispondere ai clic. Al termine, `CreateBottomHalf` il metodo contiene il codice seguente:

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

Il cerchio CocosSharp ora si sposta in risposta ai clic. È anche possibile visualizzare chiaramente i limiti dell'area di disegno CocosSharp spostando il cerchio abbastanza a sinistra o a destra:

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>Riepilogo

Questa guida illustra come aggiungere CocosSharp a un progetto esistente Xamarin.Forms , come creare un'interazione tra Xamarin.Forms e CocosSharp e illustra diverse considerazioni per la creazione di layout in CocosSharp.

Il motore di gioco CocosSharp offre una grande quantità di funzionalità e approfondimenti, quindi questa guida consente di cancellare solo la superficie di ciò che CocosSharp può fare. Gli sviluppatori interessati a leggere altre informazioni su CocosSharp possono trovare molti articoli nell' [Archivio di CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Collegamenti correlati

- [CocosSharpForms (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
