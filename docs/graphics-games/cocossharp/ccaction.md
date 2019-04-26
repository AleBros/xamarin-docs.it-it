---
title: Animazione con CCAction
description: La classe CCAction semplifica l'aggiunta di animazioni ai giochi CocosSharp. Queste animazioni utilizzabile per implementare funzionalità o per aggiungere il polacco.
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: c486bb2e78579360e0f935219cd82958fedee34b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357705"
---
# <a name="animating-with-ccaction"></a>Animazione con CCAction

_La classe CCAction semplifica l'aggiunta di animazioni ai giochi CocosSharp. Queste animazioni utilizzabile per implementare funzionalità o per aggiungere il polacco._

`CCAction` è una classe di base che può essere usata per animare oggetti CocosSharp. Questa guida illustra incorporati `CCAction` implementazioni per le attività comuni, ad esempio il posizionamento, il ridimensionamento e rotazione. Descrive anche come creare implementazioni personalizzate ereditando da `CCAction`.

Questa Guida usa un progetto denominato **ActionProject** quale [può essere scaricato da qui](https://developer.xamarin.com/samples/mobile/CCAction). Questa Guida usa la `CCDrawNode` classe, che viene trattato nel [disegno geometria con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) Guida.


## <a name="running-the-actionproject"></a>Esegue il ActionProject

**ActionProject** è una soluzione di CocosSharp che può essere compilata per iOS e Android. Viene usata sia come un esempio di codice per l'uso di `CCAction` classi e come demo in tempo reale di common `CCAction` implementazioni.

Quando si esegue, ActionProject Visualizza tre `CCLabel` istanze a sinistra della schermata e un oggetto visivo creato in base al due `CCDrawNode` istanze per visualizzare le varie azioni:

![](ccaction-images/image1.png "ActionProject Visualizza tre istanze CCLabel a sinistra della schermata e un oggetto visivo disegnato da due istanze CCDrawNode per visualizzare le varie azioni")

Le etichette a sinistra indicano il tipo di `CCAction` verrà creata quando toccando sullo schermo. Per impostazione predefinita, il **posizione** valore è selezionato, causando un `CCMove` azione viene creata e applicata per il cerchio rosso:

![](ccaction-images/image2.gif "Il valore di posizione è selezionato, generando un'azione CCMove viene creato e applicato per il cerchio rosso")

Scegliendo le etichette a sinistra viene modificato il tipo di `CCAction` viene eseguita sul cerchio. Ad esempio, facendo clic sui **posizione** etichetta deve scorrere i diversi valori che possono essere modificati:

![](ccaction-images/image3.gif "Fare clic sull'etichetta di posizione deve scorrere i diversi valori che possono essere modificati")

## <a name="common-variable-changing-ccaction-classes"></a>Classi di CCAction comuni la modifica variabile

Il **ActionProject** utilizza il seguente `CCAction`-eredità delle classi che fanno parte di CocosSharp:

 - `CCMoveTo` -Viene modificato un `CCNode` dell'istanza `Position` proprietà
 - `CCScaleTo` -Viene modificato un `CCNode` dell'istanza `Scale` proprietà
 - `CCRotateTo` -Viene modificato un `CCNode` dell'istanza `Rotation` proprietà

Questa guida fa riferimento a queste azioni come *Modifica variabile*, il che significa che cui influisce direttamente sulla variabile del `CCNode` che vengono aggiunti a. Altri tipi di azioni sono dette *interpolazione* azioni che vengono analizzate più avanti in questa Guida.

Il **ActionProject** dimostra che lo scopo di queste azioni consiste nel modificare una variabile nel tempo. In particolare, questi `CCActions` costruttori accettano due argomenti: tempo da eseguire e il valore da assegnare. Il frammento di codice seguente viene illustrato come vengono creati tre tipi di azioni:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

Dopo aver creato l'azione, viene aggiunto a un CCNode come indicato di seguito:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` Avvia il `CCAction` il comportamento dell'istanza che verrà eseguita automaticamente la logica una cornice dopo fino al completamento.

Ognuno dei tipi elencati in precedenza termina con la parola *al* vale a dire il `CCAction` modificherà il `CCNode` in modo che il valore dell'argomento rappresenta lo stato finale quando ha terminato l'azione. Ad esempio, la creazione di un `CCMoveTo` con una posizione di X = Y e 100 = 200 risultati nel `CCNode` dell'istanza `Position` viene impostata su X = 100, Y = 200 alla fine del tempo specificato, indipendentemente dal `CCNode` posizione iniziale dell'istanza.

Ogni "A" classe ha anche una versione "Da", che consentirà di aggiungere il valore dell'argomento per il valore corrente nella `CCNode`. Ad esempio, la creazione di un `CCMoveBy` con una posizione di X = Y e 100 = 200 comporterà il `CCNode` istanza spostata alle unità di destra 100 e 200 unità dalla posizione in cui è stato avviato l'azione.


## <a name="easing-actions"></a>Azioni di interpolazione

Per impostazione predefinita, verranno eseguite azioni di modifica variabile *interpolazione lineare* : l'azione verrà spostato verso il valore desiderato a una velocità costante. Se interpolazione *posizione* in modo lineare, l'oggetto mobile verrà immediatamente avviare e arrestare lo spostamento all'inizio e alla fine dell'azione e la velocità verrà mantenuto costante come l'azione viene eseguita. 

L'interpolazione non lineare è meno jarring e aggiunge un elemento di polacco, in modo CocosSharp offre un'ampia gamma di azioni che possono essere utilizzate per modificare azioni di modifica variabile di interpolazione.

Nel **ActionProject** esempio, è possibile passare tra questi tipi di interpolazione azioni facendo clic sull'etichetta della seconda (che per impostazione predefinita **<None>**):

![](ccaction-images/image4.gif "L'utente può passare tra questi tipi di interpolazione azioni facendo clic sull'etichetta della seconda")

Le azioni di interpolazione sono particolarmente potente perché questi non sono legati a qualsiasi azione variabile-impostazione particolare. Ciò significa che la stessa azione di interpolazione può essere utilizzata per assegnare posizione, rotazione, ridimensionamento o azioni personalizzate (come verrà illustrato più avanti in questa Guida).

Azioni interpolazione eseguire il wrapping di operazioni di impostazione di variabile (purché l'azione di impostazione di variabile eredita da `CCFiniteTimeAction`) mediante l'accettazione di un'azione di impostazione di variabile come argomento nei propri costruttori.

Ad esempio, se le etichette sono impostate su **posizione**, **CCEaseElastic**, quindi il codice seguente viene eseguito quando viene rilevato un tocco (si noti che il codice è stato omesso per evidenziare le righe pertinenti):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Come mostrato dall'applicazione, l'interpolazione stessa esatta è applicabile alle altre azioni di impostazione di variabile, ad esempio `CCRotateTo`:

![](ccaction-images/image5.gif "L'interpolazione stessa esatta è applicabile alle altre azioni di impostazione di variabile, ad esempio CCRotateTo")


## <a name="easing-in-out-and-inout"></a>In, Out e InOut di interpolazione

Disporre di tutte le azioni di interpolazione `In`, `Out`, o `InOut` aggiunto al tipo di interpolazione. Questi termini si riferiscono a quando viene applicata l'interpolazione: `In` interpolazione verrà applicato all'inizio, `Out` significa che alla fine, e `InOut` significa che sia all'inizio e fine.

Un `In` interpolazione azione influirà sul modo in tutta l'interpolazione intera, sia all'inizio e alla fine, viene applicata una variabile, ma in genere le caratteristiche più facilmente riconoscibile dell'azione di interpolazione verrà eseguita la ricerca dall'inizio. Analogamente, `Out` interpolazione azioni sono caratterizzate da loro comportamento alla fine di interpolazione. Ad esempio, `CCEaseBounceOut` comporterà un oggetto rimbalza alla fine dell'azione.


### <a name="out"></a>Out

`Out` in genere di interpolazione applica le modifiche più evidenti alla fine di interpolazione. Ad esempio, `CCEaseExponentialOut` rallenterà il tasso di variazione della variabile di modifica quando si avvicina il valore di destinazione:

![](ccaction-images/image6.gif "CCEaseExponentialOut rallenterà il tasso di variazione della variabile di modifica quando si avvicina il valore di destinazione")


### <a name="in"></a>In

`In` interpolazione a livello generale si applica la modifica più evidente all'inizio dell'interpolazione. Ad esempio, `CCEaseExponentialIn` sposterà più lentamente all'inizio dell'azione:

![](ccaction-images/image7.gif "CCEaseExponentialIn sposterà più lentamente all'inizio dell'azione")


### <a name="inout"></a>InOut

`InOut` si applica in genere le modifiche più evidenti, sia all'inizio e fine. `InOut` è in genere simmetrica di interpolazione. Ad esempio, `CCEaseExponentialInOut` sposterà lentamente all'inizio e alla fine dell'azione:

![](ccaction-images/image8.gif "CCEaseExponentialInOut sposterà lentamente all'inizio e alla fine dell'azione")


## <a name="implementing-a-custom-ccaction"></a>Implementazione di un CCAction personalizzato

Tutte le classi illustrati finora sono inclusi in CocosSharp per fornire funzionalità comuni. Custom `CCAction` implementazioni possono fornire una maggiore flessibilità. Ad esempio, un `CCAction` che controlla il rapporto riempito di un indicatore di esperienza può essere utilizzato in modo che la barra di esperienza senza problemi aumenta ogni volta che l'utente potrà esperienza.

`CCAction` le implementazioni in genere richiedono due classi:

 - `CCFiniteTimeAction` implementazione: la classe di azione temporale finito è responsabile dell'avvio dell'azione. È la classe che viene creata un'istanza e di essere aggiunti direttamente a un `CCNode` o a un'azione di interpolazione. È necessario creare un'istanza e restituiscono un `CCFiniteTimeActionState`, che eseguirà gli aggiornamenti.
 - `CCFiniteTimeActionState` implementazione: la classe di stato azione temporale finito è responsabile dell'aggiornamento di variabili di azione. È necessario implementare una funzione di aggiornamento, che assegna il valore di destinazione in base al valore di ora. Questa classe non fa in modo esplicito all'esterno del `CCFiniteTimeAction` che lo crea. Funziona semplicemente "dietro le quinte".

**ActionProject** fornisce una classe personalizzata `CCFiniteTimeAction` implementazione denominata `LineWidthAction,` che consente di regolare la larghezza della riga di colore gialla disegnata sopra il cerchio rosso. Si noti che l'unico processo consiste nel creare un'istanza e restituiscono un `LineWidthState` istanza:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Come indicato in precedenza, il `LineWidthState` esegue le operazioni di assegnazione della linea `Width` proprietà in base alla quantità `time` trascorso:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

È possibile combinare la LineWidthAction con qualsiasi azione interpolazione per modificare lo spessore della linea in vari modi, come illustrato nell'animazione seguente:

![](ccaction-images/image9.gif "Il LineWidthAction può essere combinato con qualsiasi azione interpolazione per modificare lo spessore della linea in vari modi, come illustrato in questa animazione")


### <a name="interpolation-and-the-update-method"></a>L'interpolazione e il metodo di aggiornamento

Il solo per la logica, oltre all'archiviazione di valori in classi precedenti, si trova nel `LineWidthState.Update` (metodo). Il `startWidth` variabile archivia la larghezza della destinazione `LineNode` all'inizio dell'azione e il `deltaWidth` variabile archivia quanto il valore verrà modificato nel corso dell'azione.

Sostituendo la `time` variabile con un valore pari a 0, è possibile osservare che la destinazione `LineNode` saranno nella posizione iniziale:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

Analogamente, è possibile osservare che la destinazione `LineNode` saranno la destinazione specificata, sostituendo la variabile di tempo con un valore pari a 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

Il `time` valore sarà in genere compreso tra 0 e 1, ma non sempre - e `Update` implementazioni non devono presupporre che questi limiti. Alcuni metodi di interpolazione (ad esempio `CCEaseBackIn` e `CCEaseBackOut`) fornirà un valore di ora di fuori dell'intervallo da 0 a 1.


## <a name="conclusion"></a>Conclusione

L'interpolazione e interpolazione sono una parte essenziale della creazione di un gioco raffinato, soprattutto durante la creazione di interfacce utente. Questa guida illustra come usare `CCActions` per eseguire l'interpolazione dei valori standard, ad esempio posizione e la rotazione, nonché i valori personalizzati. Il `LineWidthState` e `LineWidthAction` classi viene illustrato come implementare un'azione personalizzata.

## <a name="related-links"></a>Collegamenti correlati

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Esempio completo](https://developer.xamarin.com/samples/mobile/CCAction/)
