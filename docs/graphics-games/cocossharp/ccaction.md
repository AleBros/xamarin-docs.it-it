---
title: Animazione di CCAction
description: "La classe CCAction semplifica l'aggiunta di animazioni CocosSharp giochi. Tali animazioni utilizzabile per implementare la funzionalità o per aggiungere polacco."
ms.topic: article
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 2852cf0e141e8239cee8dbe580576f4571c919a3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="animating-with-ccaction"></a>Animazione di CCAction

_La classe CCAction semplifica l'aggiunta di animazioni CocosSharp giochi. Tali animazioni utilizzabile per implementare la funzionalità o per aggiungere polacco._

`CCAction` è una classe di base che può essere usata per animare oggetti CocosSharp. Questa guida illustra incorporato `CCAction` implementazioni per le attività comuni, ad esempio il posizionamento, scalabilità e la rotazione. Esamina inoltre come creare le implementazioni personalizzate ereditando dalla `CCAction`.

Questa guida viene utilizzato un progetto denominato **ActionProject** che [può essere scaricata qui](https://developer.xamarin.com/samples/mobile/CCAction). Questa Guida usa la `CCDrawNode` (classe), come illustrato nel [geometria di disegno con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) Guida.


# <a name="running-the-actionproject"></a>Esecuzione di ActionProject

**ActionProject** è una soluzione CocosSharp che può essere compilata per iOS e Android. Che svolge entrambi come un esempio di codice per l'utilizzo di `CCAction` classe e come una demo in tempo reale di comune `CCAction` implementazioni.

Quando si esegue, ActionProject Visualizza tre `CCLabel` istanze a sinistra dello schermo e un oggetto visivo creato da due `CCDrawNode` istanze per visualizzare le varie azioni:

![](ccaction-images/image1.png "ActionProject Visualizza tre istanze CCLabel a sinistra dello schermo e un oggetto visivo disegnate da due istanze di CCDrawNode per visualizzazione le varie azioni")

Le etichette a sinistra indicano il tipo di `CCAction` verrà creato per una maggiore precisione sullo schermo. Per impostazione predefinita, il **posizione** valore è selezionato, determinando un `CCMove` azione viene creata e applicata per il cerchio rosso:

![](ccaction-images/image2.gif "Il valore della posizione è selezionato, risultante in un'azione CCMove viene creato e applicato per il cerchio rosso")

Fare clic le etichette a sinistra per modificare il tipo di `CCAction` viene eseguita sul cerchio. Ad esempio, facendo clic su di **posizione** etichetta deve scorrere i diversi valori che possono essere modificati:

![](ccaction-images/image3.gif "Fare clic sull'etichetta di posizione deve scorrere i diversi valori che possono essere modificati")


# <a name="common-variable-changing-ccactions"></a>CCActions Modifica variabile comuni 

Il **ActionProject** utilizza il seguente `CCAction`-eredità, che fanno parte di CocosSharp:

 - `CCMoveTo` : Modifica una `CCNode` dell'istanza `Position` proprietà
 - `CCScaleTo` : Modifica una `CCNode` dell'istanza `Scale` proprietà
 - `CCRotateTo` : Modifica una `CCNode` dell'istanza `Rotation` proprietà

Questa guida si riferisce a queste azioni come *Modifica variabile*, il che significa che queste influiscono direttamente sulla variabile del `CCNode` che vengono aggiunti a. Altri tipi di azioni sono detti *interpolazione* azioni, che vengono descritti più avanti in questa Guida.

Il **ActionProject** illustra che lo scopo di queste azioni consiste nella modifica di una variabile nel tempo. In particolare, questi `CCActions` costruttori accettano due argomenti: tempo da eseguire e il valore da assegnare. Il frammento di codice seguente viene illustrato come creare i tre tipi di azioni:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

Una volta creata l'azione, viene aggiunto a un CCNode come indicato di seguito:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` Avvia il `CCAction` il comportamento dell'istanza che verrà eseguita automaticamente la logica frame dopo cornice fino al completamento.

Ognuno dei tipi elencati in precedenza termina con la parola *a* vale a dire che il `CCAction` modificherà il `CCNode` in modo che il valore dell'argomento rappresenta lo stato finale quando ha terminato l'azione. Ad esempio, la creazione di un `CCMoveTo` con una posizione di X = 100 e Y = 200 comporta il `CCNode` dell'istanza `Position` viene impostata su X = 100, Y = 200 alla fine del tempo specificato, indipendentemente dal `CCNode` posizione iniziale dell'istanza.

Ogni "classe a" dispone anche di una versione "Da", che verrà aggiunto il valore dell'argomento per il valore corrente nel `CCNode`. Ad esempio, la creazione di un `CCMoveBy` con una posizione di X = 100 e Y = 200 comporterà la `CCNode` istanza spostato nelle unità di destra 100 e 200 unità rispetto alla posizione in cui si trovava quando l'azione è stata avviata.


# <a name="easing-actions"></a>Azioni di interpolazione

Per impostazione predefinita, verranno eseguite azioni di modifica variabile *l'interpolazione lineare* : l'azione verrà spostato verso il valore desiderato a una velocità costante. Se nell'interpolazione *posizione* in modo lineare, l'oggetto mobile verrà immediatamente avviare e arrestare lo spostamento all'inizio e alla fine dell'azione e la velocità verrà rimangono costante durante l'esecuzione dell'azione. 

L'interpolazione lineare non è meno jarring e aggiunge un elemento di polacco, in modo CocosSharp offre un'ampia gamma di azioni che possono essere utilizzate per modificare le azioni di modifica variabile di interpolazione.

Nel **ActionProject** esempio, è possibile passare tra questi tipi di azioni interpolazione facendo clic sulla seconda etichetta (che per impostazione predefinita  **<None>** ):

![](ccaction-images/image4.gif "L'utente può passare tra questi tipi di azioni interpolazione facendo clic sulla seconda etichetta")

Le azioni di interpolazione sono particolarmente efficace perché questi non sono legati a qualsiasi azione di impostazione di variabile particolare. Ciò significa che la stessa azione di interpolazione può essere utilizzata per assegnare posizione, rotazione, ridimensionamento o azioni personalizzate (come verrà illustrato più avanti in questa Guida).

Azioni interpolazione eseguire il wrapping di operazioni di impostazione di variabile (a condizione che l'azione di impostazione di variabile eredita da `CCFiniteTimeAction`) mediante l'accettazione di un'azione di impostazione di variabile come argomento nei propri costruttori.

Ad esempio, se le etichette sono impostate su **posizione**, **CCEaseElastic**, quindi il codice seguente viene eseguito quando viene rilevato un tocco (si noti che il codice è stato omesso per evidenziare le righe pertinenti):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Come mostrato dall'applicazione, l'interpolazione stesso esatto può essere applicata a altre operazioni di impostazione di variabile, ad esempio `CCRotateTo`:

![](ccaction-images/image5.gif "Interpolazione stesso esatto può essere applicata ad altre azioni di impostazione di variabile, ad esempio CCRotateTo")


# <a name="easing-in-out-and-inout"></a>In, Out e InOut di interpolazione

Tutte le azioni interpolazione hanno `In`, `Out`, o `InOut` aggiunto al tipo di interpolazione. Questi termini si riferiscono a quando viene applicata l'interpolazione: `In` significa interpolazione applicata all'inizio, `Out` significa alla fine, e `InOut` significa sia all'inizio e fine.

Un `In` interpolazione azione influirà il modo in cui una variabile viene applicata l'intera interpolazione (entrambi all'inizio e alla fine), ma in genere le caratteristiche più facilmente riconoscibile dell'azione di interpolazione avrà luogo all'inizio. Analogamente, `Out` interpolazione azioni sono caratterizzate dal relativo comportamento alla fine di interpolazione. Ad esempio, `CCEaseBounceOut` verrà creato un oggetto saranno rimbalzati alla fine dell'azione.


## <a name="out"></a>Out

`Out` in genere interpolazione applica le modifiche più evidenti alla fine di interpolazione. Ad esempio, `CCEaseExponentialOut` rallenterà il tasso di variazione della variabile di modifica quando si avvicina il valore di destinazione:

![](ccaction-images/image6.gif "CCEaseExponentialOut rallenterà il tasso di variazione della variabile di modifica quando si avvicina il valore di destinazione")


## <a name="in"></a>In

`In` interpolazione in genere si applica la modifica più significativa apportata all'inizio dell'interpolazione. Ad esempio, `CCEaseExponentialIn` sposterà più lenta all'inizio dell'azione:

![](ccaction-images/image7.gif "CCEaseExponentialIn sposterà più lenta all'inizio dell'azione")


## <a name="inout"></a>InOut

`InOut` in genere si applica le modifiche più evidenti sia all'inizio e fine. `InOut` interpolazione è in genere simmetrica. Ad esempio, `CCEaseExponentialInOut` sposterà lenta all'inizio e alla fine dell'azione:

![](ccaction-images/image8.gif "CCEaseExponentialInOut sposterà lenta all'inizio e alla fine dell'azione")


# <a name="implementing-a-custom-ccaction"></a>Implementazione di un CCAction personalizzato

Tutte le classi che è stata illustrata finora sono inclusi in CocosSharp per fornire funzionalità comuni. Custom `CCAction` le implementazioni possono fornire una maggiore flessibilità. Ad esempio, un `CCAction` che controlla il rapporto di riempimento di una barra di esperienza può essere utilizzato in modo che la barra di esperienza aumenta in modo uniforme ogni volta che l'utente guadagna esperienza.

`CCAction` le implementazioni in genere richiedono due classi:

 - `CCFiniteTimeAction` implementazione: la classe di azione di tempo finito è responsabile per l'azione di avvio. È la classe che viene creata un'istanza e uno aggiunti direttamente a un `CCNode` o a un'azione di interpolazione. È necessario creare un'istanza e restituire un `CCFiniteTimeActionState`, che eseguirà gli aggiornamenti.
 - `CCFiniteTimeActionState` implementazione: la classe di stato di tempo finito azione è responsabile per l'aggiornamento delle variabili di azione. È necessario implementare una funzione di aggiornamento, che assegna il valore di destinazione in base a un valore di ora. Questa classe non fa riferimento in modo esplicito di fuori del `CCFiniteTimeAction` che lo crea. Funziona semplicemente "dietro le quinte".

**ActionProject** fornisce un oggetto personalizzato `CCFiniteTimeAction` chiamato implementazione `LineWidthAction,` che consente di regolare la larghezza della riga gialla disegnata sopra il cerchio rosso. Si noti che è il processo solo per creare un'istanza e restituire un `LineWidthState` istanza:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Come indicato in precedenza, il `LineWidthState` esegue il lavoro dell'assegnazione della linea `Width` proprietà in base a quanto `time` superata:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

Il LineWidthAction può essere combinato con qualsiasi azione di interpolazione per modificare la larghezza di riga in vari modi, come illustrato nell'animazione seguente:

![](ccaction-images/image9.gif "Il LineWidthAction può essere combinato con qualsiasi azione di interpolazione per modificare la larghezza di riga in vari modi, come illustrato in questa animazione")


## <a name="interpolation-and-the-update-method"></a>Il metodo di aggiornamento e di interpolazione

Logica sola a parte l'archiviazione dei valori nelle classi precedente, è disponibile il `LineWidthState.Update` metodo. Il `startWidth` variabile archivia la larghezza della destinazione `LineNode` all'inizio dell'azione e `deltaWidth` variabile archivia quanto il valore verrà modificato nel corso dell'azione.

Sostituendo il `time` variabile con un valore pari a 0, possiamo vedere che la destinazione `LineNode` sarà nella posizione inizia:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

Analogamente, possiamo vedere che la destinazione `LineNode` sarà la destinazione specificata, sostituendo la variabile di tempo con un valore pari a 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

Il `time` valore sarà in genere compreso tra 0 e 1, ma non sempre - e `Update` implementazioni non devono presupporre che questi limiti. Alcuni metodi di interpolazione (ad esempio `CCEaseBackIn` e `CCEaseBackOut`) fornirà un valore di ora compresi nell'intervallo da 0 a 1.


# <a name="conclusion"></a>Conclusione

Interpolazione e interpolazione sono una parte essenziale della creazione di un gioco ordinato, in particolare durante la creazione di interfacce utente. Questa guida viene illustrato come utilizzare `CCActions` per interpolare valori standard, ad esempio posizione e la rotazione, nonché i valori personalizzati. Il `LineWidthState` e `LineWidthAction` classi viene illustrato come implementare un'azione personalizzata.

## <a name="related-links"></a>Collegamenti correlati

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Per un esempio completo](https://developer.xamarin.com/samples/mobile/CCAction/)
