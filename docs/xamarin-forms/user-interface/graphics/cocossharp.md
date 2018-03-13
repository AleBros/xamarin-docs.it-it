---
title: Utilizzo di CocosSharp in xamarin. Forms
description: "CocosSharp può essere utilizzato per aggiungere forme precisa, immagine e il rendering del testo a un'applicazione per la visualizzazione avanzata"
ms.topic: article
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/03/2016
ms.openlocfilehash: 395defa300da7b8f68746162d877a4fdb17ded9e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="using-cocossharp-in-xamarinforms"></a>Utilizzo di CocosSharp in xamarin. Forms

_CocosSharp può essere utilizzato per aggiungere forme precisa, immagine e il rendering del testo a un'applicazione per la visualizzazione avanzata_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evoluzione 2016: Cocos # in xamarin. Forms**

## <a name="overview"></a>Panoramica

CocosSharp è una tecnologia potente e flessibile per la visualizzazione della grafica, la lettura dell'input tocco, la riproduzione di audio e la gestione dei contenuti. Questa guida illustra come aggiungere CocosSharp a un'applicazione di xamarin. Forms. Vengono illustrate le operazioni seguenti:

* [Che cos'è CocosSharp?](#what)
* [Aggiunta di pacchetti CocosSharp Nuget](#nuget)
* [Procedura dettagliata: Aggiunta di CocosSharp a un'app xamarin. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>Che cos'è CocosSharp?

[CocosSharp](~/graphics-games/cocossharp/index.md) è un motore di giochi Apri origine che è disponibile sulla piattaforma Xamarin.
CocosSharp è una libreria di runtime efficiente che include le funzionalità seguenti:

* Immagine per il rendering tramite il [CCSprite classe](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* Forma per il rendering utilizzando il [CCDrawNode classe](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* Logica di ogni fotogramma usando il [CCNode.Schedule (metodo)](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* Gestione dei contenuti (caricamento e scaricamento di risorse, ad esempio file con estensione png) utilizzando il [CCTextureCache classe](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* Le animazioni utilizzando il [CCAction classe](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

Obiettivo principale del CocosSharp è per semplificare la creazione di giochi 2D multipiattaforma; Tuttavia, può essere anche un'aggiunta importante per le applicazioni di modulo di Xamarin. Poiché giochi richiedono in genere rendering efficiente e un controllo preciso gli oggetti visivi, CocosSharp consente di aggiungere applicazioni non gioco visualizzazione potente ed effetti.

Xamarin. Forms si basa su sistemi dell'interfaccia utente nativi specifico della piattaforma. Ad esempio, [ `Button`s](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) vengono visualizzati in modo diverso in iOS e Android e possono anche differire dalla versione del sistema operativo. Al contrario, CocosSharp Usa eventuali oggetti visivi specifici della piattaforma, quindi tutti gli oggetti visivi sono identici in tutte le piattaforme. Naturalmente, risoluzione e le proporzioni differiscono tra i dispositivi e ciò può influire sulle modalità di rendering rispettivi oggetti visivi CocosSharp. Questi dettagli verranno descritta più avanti in questa Guida.

Per ulteriori informazioni, vedere il [CocosSharp sezione](~/graphics-games/cocossharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Aggiunta di pacchetti CocosSharp Nuget

Prima di utilizzare CocosSharp, gli sviluppatori devono eseguire alcune aggiunte al progetto xamarin. Forms.
Questa guida si presuppone che un progetto xamarin. Forms con un iOS, Android e PCL progetto.
Tutto il codice verrà scritto nel progetto libreria di classi Portabile. Tuttavia, è necessario aggiungere librerie per i progetti iOS e Android.

Il pacchetto CocosSharp Nuget contiene tutti gli oggetti necessari per creare oggetti CocosSharp.
Il pacchetto nuget CocosSharp.Forms include il `CocosSharpView` (classe), che viene utilizzato per l'host CocosSharp in xamarin. Forms.
Aggiungere il **CocosSharp.Forms** NuGet e **CocosSharp** verrà aggiunto automaticamente anche.
A tale scopo, fare clic sulla libreria di classi Portabile <span class="UIItem">pacchetti</span> cartella e selezionare <span class="UIItem">Aggiungi pacchetti... </span>. Immettere il termine di ricerca <span class="UIItem">CocosSharp.Forms</span>selezionare <span class="UIItem">CocosSharp per xamarin. Forms</span>, quindi fare clic su <span class="UIItem">Aggiungi pacchetto</span>.

![](cocossharp-images/image1.png "Aggiungere una finestra di dialogo di pacchetti")

Entrambi **CocosSharp** e **CocosSharp.Forms** pacchetti NuGet verranno aggiunto al progetto:

![](cocossharp-images/image2.png "Cartella di pacchetti")

Ripetere i passaggi precedenti per i progetti specifici della piattaforma (ad esempio iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Procedura dettagliata: Aggiunta di CocosSharp a un'app xamarin. Forms

Seguire questi passaggi per aggiungere una semplice visualizzazione CocosSharp a un'app xamarin. Forms:

1. [Creazione di un Xamarin pagina form](#1)
1. [Aggiunta di un CocosSharpView](#2)
1. [Creazione di GameScene](#3)
1. [Aggiunta di un cerchio](#4)
1. [L'interazione con CocosSharp](#5)

Dopo aver aggiunto correttamente una vista CocosSharp a un'app xamarin. Forms, visitare il [CocosSharp documentazione](~/graphics-games/cocossharp/index.md) per ulteriori informazioni sulla creazione di contenuto con CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Creazione di un Xamarin pagina form

CocosSharp possono essere ospitati in qualsiasi contenitore di xamarin. Forms. Questo esempio per questa pagina viene utilizzata una pagina denominata `HomePage`. `HomePage` è suddiviso in metà da un `Grid` per mostrare come xamarin. Forms CocosSharp può essere eseguito il rendering e contemporaneamente nella stessa pagina.

Innanzitutto, impostare la pagina in modo che contenga un `Grid` e due `Button` istanze:


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

In iOS, il `HomePage` viene visualizzata come illustrato nella figura seguente:

![](cocossharp-images/image3.png "Schermata Home Page")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. Aggiunta di un CocosSharpView

La `CocosSharpView` classe viene utilizzata per incorporare CocosSharp in un'app xamarin. Forms. Poiché `CocosSharpView` eredita il [Xamarin.Forms.View](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) (classe), fornisce un'interfaccia utente nota per il layout e può essere utilizzato all'interno di contenitori di layout, ad esempio [Xamarin.Forms.Grid](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). Aggiungere un nuovo `CocosSharpView` al progetto completando il `CreateTopHalf` metodo:


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

L'inizializzazione CocosSharp non è immediato, pertanto registrerà un evento quando il `CocosSharpView` ha completato la sua creazione. Eseguire questa operazione nel `HandleViewCreated` metodo:


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

Il `HandleViewCreated` dispone di due informazioni importanti che si saranno esaminando. Il primo è il `GameScene` (classe), che verrà creato nella sezione successiva. È importante notare che l'app non verrà compilato finché il `GameScene` viene creato e `gameScene` istanza riferimento viene risolto.

Il secondo dettaglio importante è il `DesignResolution` proprietà, che definisce l'area visibile del gioco CocosSharp oggetti. Il `DesignResolution` verrà da esaminare proprietà dopo la creazione di `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Creazione di GameScene

Il `GameScene` classe eredita da del CocosSharp `CCScene`. `GameScene` è il primo punto in cui si gestiscono esclusivamente CocosSharp. Il codice contenuto `GameScene` funzionerà in qualsiasi app CocosSharp, se si trova all'interno di un progetto xamarin. Forms o non.

La `CCScene` classe è la radice di tutto il rendering CocosSharp visual. Qualsiasi oggetto CocosSharp visibile deve essere contenuta in un `CCScene`. In particolare, è necessario aggiungere gli oggetti visivi per `CCLayer` istanze e quelli `CCLayer` istanze devono essere aggiunto a un `CCScene`.

Nel grafico seguente consente di visualizzare una gerarchia CocosSharp tipico:

![](cocossharp-images/image4.png "Gerarchia CocosSharp tipica")

Un solo `CCScene` possono essere attive contemporaneamente. La maggior parte dei giochi utilizzano più `CCLayer` istanze per il contenuto di ordinamento, ma l'applicazione utilizza solo uno. Analogamente, la maggior parte dei giochi usare più oggetti visivi, ma è necessario solo uno nella nostra app. Più dettagliata di discussione sul CocosSharp gerarchia visiva, vedere il [procedura dettagliata di gioco saranno rimbalzati](~/graphics-games/cocossharp/first-game/index.md).

Inizialmente il `GameScene` classe sarà quasi vuota: ci limiteremo a creare in modo da soddisfare il riferimento in `HomePage`. Aggiungere una nuova classe per la libreria di classi Portabile denominato `GameScene`. È necessario ereditare la `CCScene` classe come indicato di seguito:


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
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

È ora possibile compilare il progetto ed eseguirlo per vedere CocosSharp in esecuzione. Non è ancora stato aggiunto nulla per il nostro `GameScene,` pertanto la parte superiore della pagina è nero: il colore predefinito della scena CocosSharp:

![](cocossharp-images/image5.png "GameScene vuoto")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Aggiunta di un cerchio

L'app è attualmente un'istanza in esecuzione del motore di CocosSharp, visualizzazione di un oggetto vuoto `CCScene`. Successivamente, verrà aggiunto un oggetto visivo: un cerchio. Il `CCDrawNode` classe può essere utilizzata per disegnare una varietà di forme geometriche, come descritto nel [geometria di disegno con CCDrawNode Guida](~/graphics-games/cocossharp/ccdrawnode.md).

Aggiungere un cerchio e il nostro `GameScene` classe e un'istanza nel costruttore come illustrato nel codice seguente:


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

Esecuzione dell'applicazione ora mostra un cerchio sul lato sinistro dell'area di visualizzazione CocosSharp:

![](cocossharp-images/image6.png "Cerchio in GameScene")


#### <a name="understanding-designresolution"></a>Informazioni sui DesignResolution

Ora che viene visualizzato un oggetto visivo di CocosSharp, sarà possibile esaminare la `DesignResolution` proprietà.

Il `DesignResolution` rappresenta la larghezza e altezza dell'area CocosSharp per posizionare e ridimensionare gli oggetti. La risoluzione effettiva dell'area è misurata in *pixel* mentre il `DesignResolution` viene misurato nel mondo *unità*. Il diagramma seguente illustra la risoluzione delle varie parti della visualizzazione come visualizzato in un iPhone 5 con una risoluzione dello schermo di x 1136 640 pixel:

![](cocossharp-images/image7.png "iPhone 5s progettazione risoluzione")

Nel diagramma precedente consente di visualizzare le dimensioni in pixel all'esterno della schermata di testo di colore nero. Unità vengono visualizzate all'interno del diagramma in testo bianco. Ecco alcuni dettagli importanti visualizzati di sopra:

* L'origine della visualizzazione CocosSharp è in basso a sinistra. Spostamento a destra il valore di X e spostando verso l'alto il valore Y. Si noti che il valore Y è invertito rispetto ad altri motori di layout 2D, dove (0,0) è in alto a sinistra dell'area di disegno.
* Il comportamento predefinito di CocosSharp consiste nel mantenere le proporzioni della relativa visualizzazione. Poiché la prima riga nella griglia è maggiore dell'altezza, CocosSharp non riempie l'intera larghezza della cella, come illustrato dal rettangolo bianco punteggiato. Questo comportamento può essere modificato, come descritto nel [Guida di gestione di più risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md).
* In questo esempio, si manterrà CocosSharp proporzioni del dispositivo o un'area di visualizzazione di 100 unità di larghezza e altezza indipendentemente dalle dimensioni. Ciò significa che i codice può presupporre che rappresenta la X = 100 associata all'estrema destra del CocosSharp visualizzare, layout mantenendo coerenti in tutti i dispositivi.


#### <a name="ccdrawnode-details"></a>Dettagli CCDrawNode

La semplice app viene utilizzata la `CCDrawNode` classe per disegnare un cerchio. Questa classe può essere molto utile per le applicazioni di business poiché viene fornita per il rendering della geometria basata su vettore – una funzionalità presente xamarin. Forms. Oltre a cerchi, la `CCDrawNode` classe può essere utilizzata per disegnare rettangoli, spline, linee e poligoni personalizzati. `CCDrawNode` è anche facile da usare in quanto non richiede l'utilizzo dei file di immagine (ad esempio con estensione png). Una descrizione più dettagliata di CCDrawNode è reperibile nel [geometria di disegno con CCDrawNode Guida](~/graphics-games/cocossharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. L'interazione con CocosSharp

Gli elementi visivi CocosSharp (ad esempio `CCDrawNode`) ereditare la `CCNode` classe. `CCNode` fornisce due proprietà che può essere usata per posizionare un oggetto rispetto al padre: `PositionX` e `PositionY`. Il codice attualmente Usa queste due proprietà per posizionare il centro del cerchio, come illustrato nel frammento di codice seguente:


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

È importante notare che gli oggetti CocosSharp sono posizionati tramite valori di posizione espliciti, anziché la maggior parte delle visualizzazioni di xamarin. Forms, che vengono automaticamente posizionati in base al comportamento dei controlli di layout padre.

Verrà aggiunto il codice per consentire all'utente di fare clic su uno dei due pulsanti per spostare il cerchio a sinistra o a destra di 10 unità (non in pixel, poiché il cerchio disegna nello spazio di unità world CocosSharp). Innanzitutto si creerà due metodi pubblici di `GameScene` classe:


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

Successivamente, aggiungeremo gestori per i due pulsanti nel `HomePage` per rispondere alle selezioni. Al termine, il nostro `CreateBottomHalf` metodo contiene il codice seguente:


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

Il cerchio CocosSharp ora consente di spostare in risposta ai clic. Si noterà anche chiaramente i limiti dell'area di disegno CocosSharp spostando il cerchio abbastanza verso sinistra o destra:

![](cocossharp-images/image8.png "GameScene con spostamento cerchio")

## <a name="summary"></a>Riepilogo

Questa guida viene illustrato come aggiungere CocosSharp a un xamarin. Forms esistente del progetto, come creare l'interazione tra xamarin. Forms e CocosSharp e vengono illustrate varie considerazioni durante la creazione di layout in CocosSharp.

Il motore di giochi CocosSharp offre numerose funzionalità e la profondità, questa guida interessa solo delle operazioni che è possibile eseguire CocosSharp. Gli sviluppatori interessati a leggere altre informazioni sulle CocosSharp disponibili numerosi articoli nella [CocosSharp sezione](~/graphics-games/cocossharp/index.md).



## <a name="related-links"></a>Collegamenti correlati

- [API CocosSharp](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
