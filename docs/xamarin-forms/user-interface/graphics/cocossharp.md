---
title: Uso di CocosSharp in xamarin. Forms
description: CocosSharp può essere utilizzato per aggiungere la forma esatta, l'immagine e il rendering del testo a un'applicazione per la visualizzazione avanzata
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 8ba9e4b119384db401fc631f58c37a28cd2b8004
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292374"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Uso di CocosSharp in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp può essere usato per aggiungere una forma, un'immagine e un rendering del testo precisi a un'applicazione per la visualizzazione avanzata_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016: Cocos # in Novell. Forms**

## <a name="overview"></a>Panoramica

CocosSharp è una tecnologia potente e flessibile per la visualizzazione di grafica, la lettura dell'input tocco, la riproduzione di audio e la gestione dei contenuti. Questa guida illustra come aggiungere CocosSharp a un'applicazione xamarin. Forms. Viene descritto come il seguente:

- [Che cos'è CocosSharp?](#what)
- [Aggiunta dei pacchetti NuGet CocosSharp](#nuget)
- [Procedura dettagliata: aggiunta di CocosSharp a un'app Novell. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>Che cos'è CocosSharp?

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) è un motore di gioco open source disponibile nella piattaforma Novell.
CocosSharp è una libreria di runtime efficiente che include le funzionalità seguenti:

- Rendering di immagini con la classe `CCSprite`
- Rendering delle forme con la classe `CCDrawNode`
- Logica di ogni frame che usa la classe `CCNode.Schedule`
- Gestione dei contenuti (caricamento e scaricamento di risorse, ad esempio file con estensione png) tramite il `CCTextureCache`
- Animazioni con la classe `CCAction`

Obiettivo principale di CocosSharp consiste nel semplificare la creazione di giochi 2D multipiattaforma; Tuttavia, può essere anche un'aggiunta importante per le applicazioni Xamarin Form. Poiché i giochi richiedono in genere un rendering efficiente e un controllo preciso su oggetti visivi, CocosSharp è utilizzabile per aggiungere potenti funzionalità di visualizzazione e gli effetti alle applicazioni non di gioco.

Xamarin. Forms si basa su sistemi dell'interfaccia utente nativi specifici della piattaforma. Ad esempio, [`Button`s](xref:Xamarin.Forms.Button) vengono visualizzate in modo diverso in iOS e Android e possono anche variare in base alla versione del sistema operativo. Al contrario, CocosSharp non usare oggetti visivi specifici della piattaforma, in modo che tutti gli oggetti visivi sembrano identici in tutte le piattaforme. Naturalmente, la risoluzione e le proporzioni differiscono tra i dispositivi e ciò può influire sul modo in cui CocosSharp esegue il rendering dei rispettivi oggetti visivi. Questi dettagli verranno descritte più avanti in questa Guida.

Per informazioni più dettagliate, vedere la [sezione CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Aggiunta dei pacchetti NuGet CocosSharp

Prima di usare CocosSharp, gli sviluppatori devono effettuare alcune aggiunte al progetto xamarin. Forms.
Questa guida si presuppone che un progetto xamarin. Forms con un iOS, Android e .NET Standard progetto di libreria.
Tutto il codice verrà scritto nel progetto della libreria .NET Standard. Tuttavia, è necessario aggiungere le librerie per i progetti iOS e Android.

Il pacchetto NuGet CocosSharp contiene tutti gli oggetti necessari per creare oggetti CocosSharp.
Il pacchetto NuGet CocosSharp. Forms include la classe `CocosSharpView`, che viene usata per ospitare CocosSharp in Novell. Forms.
Aggiungere anche **CocosSharp. Forms** NuGet e **CocosSharp** verranno aggiunti automaticamente.
A tale scopo, fare clic con il pulsante destro del mouse sulla cartella **pacchetti** nel progetto libreria .NET standard e selezionare **Aggiungi pacchetti...** . Immettere il termine di ricerca **CocosSharp. Forms**, selezionare **CocosSharp per Novell. Forms**, quindi fare clic su **Aggiungi pacchetto**.

![](cocossharp-images/image1.png "Add Packages Dialog")

Al progetto verranno aggiunti i pacchetti NuGet **CocosSharp** e **CocosSharp. Forms** :

![](cocossharp-images/image2.png "Packages Folder")

Ripetere i passaggi precedenti per progetti specifici della piattaforma (ad esempio iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Procedura dettagliata: Aggiunta di CocosSharp in un'app xamarin. Forms

Seguire questi passaggi per aggiungere una visualizzazione semplice di CocosSharp in un'app xamarin. Forms:

1. [Creazione di una pagina form Novell](#1)
1. [Aggiunta di un CocosSharpView](#2)
1. [Creazione di GameScene](#3)
1. [Aggiunta di un cerchio](#4)
1. [Interazione con CocosSharp](#5)

Una volta aggiunta correttamente una visualizzazione CocosSharp a un'app Novell. Forms, vedere la [documentazione di CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) per altre informazioni sulla creazione di contenuto con CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. creazione di una pagina Novell Forms

CocosSharp possono essere ospitati in qualsiasi contenitore di xamarin. Forms. Questo esempio per questa pagina usa una pagina denominata `HomePage`. `HomePage` viene suddivisa a metà da un `Grid` per illustrare il rendering simultaneo di Novell. Forms e CocosSharp nella stessa pagina.

Per prima cosa, configurare la pagina in modo che contenga una `Grid` e due istanze `Button`:

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

![](cocossharp-images/image3.png "HomePage Screenshot")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. aggiunta di un CocosSharpView

La classe `CocosSharpView` viene usata per incorporare CocosSharp in un'app Novell. Forms. Poiché `CocosSharpView` eredita dalla classe [Novell. Forms. View](xref:Xamarin.Forms.View) , fornisce un'interfaccia familiare per il layout e può essere usata all'interno di contenitori di layout, ad esempio [Novell. Forms. Grid](xref:Xamarin.Forms.Grid). Aggiungere una nuova `CocosSharpView` al progetto completando il metodo `CreateTopHalf`:

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

L'inizializzazione di CocosSharp non è immediata, quindi registrare un evento per il completamento della creazione del `CocosSharpView`. Eseguire questa operazione nel metodo `HandleViewCreated`:

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

Il metodo `HandleViewCreated` dispone di due dettagli importanti che verranno esaminati. Il primo è la classe `GameScene`, che verrà creata nella sezione successiva. È importante notare che l'app non verrà compilata finché non viene creato il `GameScene` e il riferimento all'istanza di `gameScene` non viene risolto.

Il secondo dettaglio importante è la proprietà `DesignResolution`, che definisce l'area visibile del gioco per gli oggetti CocosSharp. La proprietà `DesignResolution` verrà esaminata dopo la creazione di `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. creazione del GameScene

La classe `GameScene` eredita da `CCScene`di CocosSharp. `GameScene` è il primo punto in cui si tratta esclusivamente di CocosSharp. Il codice contenuto nel `GameScene` funzionerà in qualsiasi app CocosSharp, indipendentemente dal fatto che sia ospitato all'interno di un progetto Novell. Forms.

La classe `CCScene` è la radice visiva di tutti i rendering di CocosSharp. Qualsiasi oggetto CocosSharp visibile deve essere contenuto all'interno di un `CCScene`. In particolare, gli oggetti visivi devono essere aggiunti alle istanze di `CCLayer` e tali istanze di `CCLayer` devono essere aggiunte a un `CCScene`.

Il grafico seguente può essere utile visualizzare una gerarchia di CocosSharp tipica:

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

È possibile attivarne solo una `CCScene` alla volta. La maggior parte dei giochi usa più istanze di `CCLayer` per ordinare il contenuto, ma l'applicazione ne usa solo una. Analogamente, la maggior parte dei giochi utilizzano più oggetti visivi, ma sarà ne abbiamo solo uno nell'app. Una discussione più dettagliata sulla gerarchia visiva di CocosSharp è disponibile nella [procedura dettagliata di BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inizialmente la classe `GameScene` sarà quasi vuota, ma verrà creata per soddisfare il riferimento in `HomePage`. Aggiungere una nuova classe al progetto di libreria .NET Standard denominato `GameScene`. Deve ereditare dalla classe `CCScene` come indicato di seguito:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Ora che `GameScene` è definito, è possibile tornare `HomePage` e aggiungere un campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

È ora possibile compilare il progetto ed eseguirlo per vedere CocosSharp in esecuzione. Non è stato aggiunto alcun elemento alla `GameScene,`, quindi la metà superiore della pagina è nera, ovvero il colore predefinito di una scena CocosSharp:

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. aggiunta di un cerchio

L'app dispone attualmente di un'istanza in esecuzione del motore CocosSharp, che visualizza un `CCScene`vuoto. Successivamente, verrà aggiunto un oggetto visivo: un cerchio. La classe `CCDrawNode` può essere usata per disegnare diverse forme geometriche, come illustrato nella Guida alla geometria di [disegno con CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Aggiungere un cerchio alla classe `GameScene` e crearne un'istanza nel costruttore, come illustrato nel codice seguente:

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

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>Understanding DesignResolution

Ora che viene visualizzato un oggetto Visual CocosSharp, è possibile esaminare la proprietà `DesignResolution`.

Il `DesignResolution` rappresenta la larghezza e l'altezza dell'area CocosSharp per l'inserimento e il ridimensionamento degli oggetti. La risoluzione effettiva dell'area viene misurata in *pixel* mentre la `DesignResolution` viene misurata in *unità*internazionali. Il diagramma seguente illustra la risoluzione delle varie parti della visualizzazione come visualizzato in un iPhone 5 con una risoluzione dello schermo pari a 640 x 1136 pixel:

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

Il diagramma precedente visualizza le dimensioni in pixel all'esterno della schermata di testo di colore nero. Unità vengono visualizzate all'interno del diagramma nel testo bianco. Ecco alcuni dettagli importanti visualizzati sopra:

- L'origine dello schermo CocosSharp è in basso a sinistra. Spostandosi quindi verso destra aumenta il valore di X, nonché spostando verso l'alto il valore Y. Si noti che il valore Y è invertito rispetto ad alcuni altri motori di layout 2D, in cui (0,0) è in alto a sinistra dell'area di disegno.
- Il comportamento predefinito di CocosSharp consiste nel mantenere le proporzioni della relativa visualizzazione. Poiché la prima riga della griglia è maggiore dell'altezza, CocosSharp non riempie l'intera larghezza della cella, come mostrato dal rettangolo bianco punteggiato. Questo comportamento può essere modificato, come descritto nella [Guida gestione di più risoluzioni in CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- In questo esempio, si manterrà CocosSharp un'area di visualizzazione di 100 unità di larghezza e altezza indipendentemente dalle dimensioni e le proporzioni del dispositivo. Ciò significa che codice può presupporre che X = 100 è rappresentato dal associato all'estrema destra di CocosSharp visualizzare, layout mantenendo coerenti in tutti i dispositivi.

#### <a name="ccdrawnode-details"></a>Dettagli CCDrawNode

L'app semplice usa la classe `CCDrawNode` per creare un cerchio. Questa classe può essere molto utile per le app aziendali poiché viene fornita per il rendering basato su vettore geometry – una funzionalità non presente in xamarin. Forms. Oltre ai cerchi, è possibile usare la classe `CCDrawNode` per creare rettangoli, spline, linee e poligoni personalizzati. `CCDrawNode` è anche facile da usare perché non richiede l'uso di file di immagine, ad esempio. png. Per una descrizione più dettagliata di CCDrawNode, vedere la sezione [Drawing Geometry with CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. interazione con CocosSharp

Gli elementi visivi CocosSharp (ad esempio `CCDrawNode`) ereditano dalla classe `CCNode`. `CCNode` fornisce due proprietà che possono essere utilizzate per posizionare un oggetto relativo al padre: `PositionX` e `PositionY`. Il codice Usa attualmente queste due proprietà per posizionare il centro del cerchio, come illustrato nel frammento di codice seguente:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

È importante notare che oggetti CocosSharp sono posizionati per valori di posizione espliciti, anziché la maggior parte delle visualizzazioni di xamarin. Forms, che vengono automaticamente posizionati in base al comportamento dei relativi controlli di layout del padre.

Si aggiungerà codice per consentire all'utente di fare clic su uno dei due pulsanti per spostare a sinistra o a destra del cerchio di 10 unità (non in pixel, poiché consente di disegnare il controllo circle nello spazio CocosSharp world unit). Verranno prima creati due metodi pubblici nella classe `GameScene`:

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

Successivamente, si aggiungeranno i gestori ai due pulsanti in `HomePage` per rispondere ai clic. Al termine, il metodo `CreateBottomHalf` contiene il codice seguente:

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

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>Riepilogo

Questa guida illustra come aggiungere CocosSharp a un xamarin. Forms esistenti del progetto, come creare l'interazione tra xamarin. Forms e CocosSharp e vengono illustrate varie considerazioni quando si creano i layout in CocosSharp.

Il motore di gioco CocosSharp offre numerose funzionalità e la profondità, in modo che questa guida interessa solo alcune delle operazioni che è possibile eseguire CocosSharp. Gli sviluppatori interessati a leggere altre informazioni su CocosSharp possono trovare molti articoli nell' [Archivio di CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Collegamenti correlati

- [CocosSharpForms (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
