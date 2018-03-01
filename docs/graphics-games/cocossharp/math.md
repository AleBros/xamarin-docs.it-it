---
title: Matematiche 2D con CocosSharp
description: "Questa guida illustra 2D matematica per lo sviluppo di gioco. Utilizza CocosSharp in cui viene illustrato come eseguire attività comuni di sviluppo di giochi e spiega i calcoli dietro queste attività."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 7573ca423c3d9462d400f117c2116209e7c2a410
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="2d-math-with-cocossharp"></a>Matematiche 2D con CocosSharp

_Questa guida illustra 2D matematica per lo sviluppo di gioco. Utilizza CocosSharp in cui viene illustrato come eseguire attività comuni di sviluppo di giochi e spiega i calcoli dietro queste attività._

Per posizionare e spostare gli oggetti con il codice è una parte fondamentale di sviluppo di giochi di tutte le dimensioni. Il posizionamento e spostamento richiedono l'uso di matematica, se un gioco richiede lo spostamento di un oggetto lungo una linea retta, o l'utilizzo di trigonometriche per la rotazione. Questo documento illustra gli argomenti seguenti:

 - Velocità
 - Accelerazione
 - Rotazione di oggetti CocosSharp
 - Con velocità di rotazione

Gli sviluppatori che non dispongono di uno sfondo matematica complessa o che hanno long-dimenticato questi argomenti dall'istituto di istruzione, non è necessario ricorrere a: questo documento verrà suddividere concetti in parti dimensioni effetto e accompagnamento teoriche spiegazioni con esempi pratici. In breve, in questo articolo verrà rispondere alla domanda di studenti di matematica ippiche: "Quando è effettivamente necessario utilizzare questo stuff?"


# <a name="requirements"></a>Requisiti

Anche se questo documento si concentra principalmente sul lato matematico di CocosSharp, esempi di codice presuppongono l'utilizzo di oggetti che ereditano modulo `CCNode`. Inoltre, poiché `CCNode` non include i valori per velocità e l'accelerazione, nel codice si presuppone l'utilizzo delle entità che forniscono valori, ad esempio VelocityX, VelocityY, AccelerationX e AccelerationY. Per ulteriori informazioni sulle entità, vedere la procedura dettagliata su [le entità in CocosSharp](~/graphics-games/cocossharp/entities.md).


# <a name="velocity"></a>Velocità

Sviluppatori di giochi viene usato il termine *velocità* per descrivere come un oggetto viene spostato, in particolare sulla velocità di un elemento viene spostato e la direzione che è mobile. 

Velocità viene definita con due tipi di unità: un'unità di posizione e un'unità di tempo. Ad esempio, velocità di un'automobile è definito come chilometri all'ora o chilometri all'ora. Sviluppatori di giochi spesso utilizzare pixel al secondo per definire la velocità è un oggetto viene spostato. Ad esempio, un punto elenco può spostare una velocità di 300 pixel in un secondo. Vale a dire, se lo spostamento di 300 pixel in un secondo punto elenco, quindi sono stati spostati 600 unità due secondi e le unità di 900 in tre secondi e così via. Più in generale, il valore della velocità *aggiunge* alla posizione di un oggetto (come possiamo vedere sotto).

Anche se è stata utilizzata velocità per illustrare le unità di velocità, la velocità di termini in genere fa riferimento a un valore indipendente dalla direzione, mentre la velocità del termine si riferisce a sia velocità e la direzione. Pertanto, l'assegnazione della velocità di un punto dell'elenco (presupponendo che il punto elenco è una classe che include le proprietà necessarie) potrebbe simile al seguente:


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


## <a name="implementing-velocity"></a>Implementazione di velocità

CocosSharp non implementano velocità, è necessario che gli oggetti che richiedono lo spostamento di implementare i propri logica di spostamento. Sviluppatori di giochi nuova implementazione velocità spesso commettono di rendere loro velocità dipende dalla frequenza dei fotogrammi. Vale a dire, quanto segue *implementazione non corretta* risulteranno per fornire risultati corretti, ma saranno basate su frequenza dei fotogrammi del gioco:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

Se il gioco viene eseguita su una frequenza dei fotogrammi superiore (ad esempio 60 fotogrammi al secondo anziché 30 frame al secondo), l'oggetto verrà visualizzato a muoversi più velocemente rispetto a in esecuzione se a una frequenza dei fotogrammi più lenta. Analogamente, se il gioco è in grado di elaborare i fotogrammi in al livello più alto di una frequenza dei fotogrammi (che può essere causata da processi in background utilizzando le risorse del dispositivo), il gioco apparirà venga rallentata.

Per evitare ciò, velocità viene spesso implementata utilizzando un valore di ora. Ad esempio, se il `seconds` variabile rappresenta il numero o frazione di secondi perché è stata applicata la velocità di ora ultimo, quindi il codice seguente comporta l'oggetto con lo spostamento coerenza indipendentemente dalla frequenza dei fotogrammi:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

È consigliabile che un gioco che viene eseguito con una frequenza inferiore aggiornerà la posizione dei relativi oggetti meno frequentemente. Pertanto, ogni aggiornamento causerà negli oggetti di spostamento superiore rispetto a quello che avrebbero il gioco aggiornavano più frequentemente. Il `seconds` valore account a tale scopo, segnalando il tempo trascorso dall'ultimo aggiornamento.

Per un esempio di come aggiungere basati sul tempo di spostamento, vedere [ricetta relativi all'ora in base a spostamento](https://developer.xamarin.com/recipes/cross-platform/game_development/time_based_movement/).


## <a name="calculating-positions-using-velocity"></a>Il calcolo delle posizioni mediante velocità

Velocità utilizzabile per eseguire stime su un oggetto in cui verrà dopo una certa quantità di tempo o per contribuire a ottimizzare il comportamento degli oggetti senza la necessità di eseguire il gioco. Ad esempio, uno sviluppatore che sta implementando lo spostamento di un punto elenco attivato deve impostare la velocità del punto dopo che viene creata un'istanza. Le dimensioni dello schermo consente di fornire una base per l'impostazione della velocità. Vale a dire, se lo sviluppatore sa che il punto deve spostare l'altezza dello schermo in 2 secondi, quindi la velocità deve essere impostata per l'altezza dello schermo diviso 2. Se la schermata è 800 pixel in altezza, velocità del punto sarebbe impostato su 400 (ovvero 800/2).

Analogamente, la logica di gioco potrebbe essere necessario calcolare il tempo un oggetto necessario per raggiungere la destinazione fornita la velocità. Questo può essere calcolato dividendo la distanza di viaggiare dalla velocità dell'oggetto viaggio. Ad esempio, il codice seguente viene illustrato come assegnare un'etichetta che consente di visualizzare il tempo di testo fino a quando un missilistica raggiunga la destinazione:


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


# <a name="acceleration"></a>Accelerazione

*Accelerazione* è un concetto comune nello sviluppo di gioco e condivide molte analogie con velocità. Accelerazione quantifica se un oggetto è velocizzare o rallentare (come il valore della velocità cambia nel tempo). Accelerazione *aggiunge* alla velocità, esattamente come velocità aggiunge per posizionare. Applicazioni comuni di accelerazione includono gravità, un'automobile velocizzazione e nave spazio relativo thrusters di generazione. 

Simile a velocità, accelerazione è definito in una posizione e le unità di tempo; Tuttavia, l'unità di tempo dell'accelerazione viene definito un *al quadrato* unità, che riflette le accelerazione definita matematicamente. Vale a dire accelerazione gioco è spesso misurata in *pixel al secondo al quadrato*.

Se un oggetto ha un'accelerazione X di 10 unità al secondo al quadrato, che significa che consente di aumentare la velocità, 10 ogni secondo. Se l'avvio da un fermo, dopo un secondo sarà lo spostamento in 10 unità per secondo, dopo due secondi 20 unità di misura per secondo, e così via.

Accelerazione in due dimensioni richiede un componente di X e Y, pertanto può essere assegnato come indicato di seguito:


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


## <a name="acceleration-vs-deceleration"></a>Accelerazione vs. Decelerazione

Anche se accelerazione e decelerazione talvolta si differenzino solo in vocale ogni giorno, non vi è alcuna differenza tra i due tecnica. Gravità è una forza conseguente accelerazione. Se viene generato un oggetto verso l'alto quindi gravità rallenterà il (decelerando), ma una volta che l'oggetto è stato arrestato progressivamente e che nella stessa direzione gravità quindi gravità è velocizzare (accelerazione). Come illustrato di seguito, l'applicazione di un'accelerazione è lo stesso se viene applicato nella stessa direzione o opposto direzione di spostamento. 


## <a name="implementing-acceleration"></a>Implementazione di accelerazione

Accelerazione è simile a velocità in fase di implementazione: non è implementato automaticamente da CocosSharp e accelerazione basata su tempo è l'implementazione desiderata (in contrapposizione accelerazione basata su frame). Pertanto, un'implementazione semplice accelerazione (insieme a velocità) può apparire come:

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

Il codice sopra riportato è ciò che viene definita per un *approssimazione lineare* per l'implementazione di accelerazione. In effetti, implementa accelerazione con un livello di precisione piuttosto chiudere, ma non è un modello di accelerazione perfettamente accurato. Per spiegare il concetto di modalità di implementazione di accelerazione è incluso in precedenza.

L'implementazione seguente è un'applicazione matematicamente precisa di accelerazione e velocità:


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

È la differenza più ovvia per il codice precedente il `halfSecondsSquared` variabile e il relativo utilizzo per applicare l'accelerazione per posizionare. Il motivo matematico non rientra nell'ambito di questa esercitazione, ma gli sviluppatori interessati a calcoli matematici base di questi è possono trovare altre informazioni, vedere [questa discussione sull'integrazione di accelerazione.](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

L'impatto di pratica di `halfSecondSquare` è che l'accelerazione comporteranno matematicamente con precisione e in modo prevedibile indipendentemente dalla frequenza dei fotogrammi. L'approssimazione lineare di accelerazione è soggetto a frequenza dei fotogrammi: minore sarà la frequenza dei fotogrammi scende diventa l'approssimazione meno precisa. Utilizzando `halfSecondsSquared` garanzie che codice comporterà lo stesso indipendentemente dalla frequenza dei fotogrammi.


# <a name="angles-and-rotation"></a>Gli angoli e rotazione

Oggetti visivi, ad esempio `CCSprite` supportano la rotazione tramite un `Rotation` variabile. Può essere assegnato a un valore per impostare la rotazione in gradi. Ad esempio, il codice seguente viene illustrato come eseguire la rotazione un `CCSprite` istanza:


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

L'opzione produce quanto segue:

![](math-images/image1.png "Di conseguenza, questa schermata")

Si noti che la rotazione è 45 gradi in senso orario (che per motivi storici è l'opposto dell'applicazione matematicamente rotazione):

![](math-images/image2.png "Si noti che la rotazione è 45 gradi in senso orario che, per motivi storici è l'opposto dell'applicazione matematicamente rotazione")

In generale la rotazione per CocosSharp e regolare matematiche può essere visualizzata come segue:

![](math-images/image3.png "In generale la rotazione per CocosSharp e regolare matematiche può essere visualizzata simile al seguente")

![](math-images/image4.png "In generale la rotazione per CocosSharp e regolare matematiche può essere visualizzata simile al seguente")

Questa distinzione è importante perché il `System.Math` classe utilizza rotazione in senso antiorario, quindi è necessario invertire gli angoli quando si lavora con gli sviluppatori esperti in questa classe `CCNode` istanze.

Si noti che i diagrammi precedenti visualizzano rotazione in gradi; Tuttavia, alcune funzioni matematiche (ad esempio le funzioni nel `System.Math` dello spazio dei nomi) previsto e restituire valori in *radianti* anziché gradi. Verrà esaminato come eseguire la conversione tra tipi di unità di due più avanti in questa Guida.


## <a name="rotating-to-face-a-direction"></a>Rotazione per affrontare una direzione

Come illustrato in precedenza, `CCSprite` può essere ruotato utilizzando il `Rotation` proprietà. Il `Rotation` proprietà viene fornita da `CCNode` (la classe base per `CCSprite`), ovvero la rotazione può essere applicata a entità che ereditano da `CCNode` anche. 

Alcuni giochi richiedono oggetti da ruotare, in modo da affrontare una destinazione. Gli esempi includono un nemico computer controllato si riprende una destinazione di Windows Media player, o una spedizione spazio volo verso il punto in cui l'utente tocca lo schermo. Tuttavia, un valore di rotazione deve prima essere calcolato in base il percorso dell'entità da ruotare e la posizione di destinazione ad affrontare.

Questo processo richiede un numero di passaggi:

 - Identificazione di *offset* della destinazione. Offset si riferisce alla distanza tra l'entità di rotazione e l'entità di destinazione X e Y.
 - Calcolare l'angolo dall'offset utilizzando la funzione trigonometria arcotangente (descritto in dettaglio di seguito).
 - La modifica di una differenza tra 0 gradi rivolta verso destra e la direzione che fa riferimento l'entità rotazione quando non ruotata.
 - Modifica della differenza tra matematica rotazione (in senso antiorario) e la rotazione CocosSharp (in senso orario).

La funzione seguente (utilizzata deve essere scritto in un'entità) consente di ruotare l'entità in modo che sia una destinazione:


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

Il codice precedente può essere utilizzato per ruotare un'entità in modo che sia rivolto verso il punto in cui l'utente tocca la schermata, come indicato di seguito:


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

Questo codice produce il seguente comportamento:

![](math-images/image5.gif "Questo codice genera questo comportamento")

### <a name="using-atan2-to-convert-offsets-to-angles"></a>Utilizzo di Atan2 per convertire gli offset per gli angoli
`System.Math.Atan2` Consente di convertire un offset a un angolo. Il nome della funzione `Atan2` proviene l'arcotangente di funzione trigonometrica. Il suffisso "2" consentono di distinguere questa funzione dallo standard `Atan` (funzione), che corrisponde strettamente il comportamento dell'arcotangente matematico. Arcotangente è una funzione che restituisce un valore compreso tra -90 e + 90 gradi (o l'equivalente in radianti). Molte applicazioni, inclusi computer giochi, spesso richiedono di 360 gradi di valori, pertanto la `Math` classe include `Atan2` per soddisfare questa esigenza.

Si noti che il codice precedente passa il parametro Y in primo luogo, quindi il parametro X, quando si chiama il `Atan2` metodo. Si tratta con le versioni precedenti dalle consuete X, Y ordinamento delle coordinate di posizione. Per ulteriori informazioni [vedere i documenti Atan2](https://msdn.microsoft.com/en-us/library/system.math.atan2(v=vs.110).aspx).

È inoltre importante notare che il valore restituito da `Atan2` è espresso in radianti, che viene utilizzata per misurare gli angoli di un'altra unità. Questa Guida non riguardano i dettagli di radianti, ma tenere presente che tutte le funzioni trigonometriche il `System.Math` dello spazio dei nomi utilizzare radianti, in modo da tutti i valori devono essere convertiti in gradi prima di essere utilizzati per gli oggetti CocosSharp. Sono disponibili ulteriori informazioni su radianti [nella pagina di Wikipedia radiante](http://en.wikipedia.org/wiki/Radian).

### <a name="forward-angle"></a>Angolo di inoltro
Una volta il `FacePoint` metodo converte l'angolo in radianti, definisce un `forwardAngle` valore. Questo valore rappresenta l'angolo, in cui si sta verificando l'entità quando il valore di rotazione è uguale a 0. In questo esempio si presuppone che l'entità è rivolto verso l'alto, che è di 90 gradi, quando si utilizza una rotazione matematica (in contrapposizione a rotazione CocosSharp). Utilizziamo la rotazione matematica qui perché è ancora non invertito la rotazione per CocosSharp.

Di seguito viene illustrato quale un'entità con un `forwardAngle` di 90 gradi, potrebbe essere simile:

![](math-images/image6.png "Viene illustrato il possibile aspetto un'entità con un forwardAngle di 90 gradi")


## <a name="angled-velocity"></a>Velocità angolare

Finora abbiamo esaminato come convertire un offset in un angolo. In questa sezione va viceversa: utilizza un angolo e lo converte in X e i valori Y. Esempi comuni includono un'automobile lo spostamento, la direzione che sta verificando o nave spazio la ripresa di un punto elenco in modo da spostare nella direzione verso la spedizione. 

Concettualmente, velocità può essere calcolata, innanzitutto definire la velocità desiderata quando non ruotata, quindi tale velocità che un'entità è rivolta verso l'angolo di rotazione. Per illustrare questo concetto, velocità (e l'accelerazione) possono essere visualizzati come un 2 dimensioni *vettore* (che in genere disegnato sotto forma di freccia). Un vettore per il valore relativo alla velocità con X = 100 e Y = 0 possono essere visualizzati come segue:

![](math-images/image7.png "Un vettore per il valore relativo alla velocità con X = 100 e Y = 0 possono essere visualizzati come indicato di seguito")

Il vettore può essere ruotato per garantire una velocità di nuovo. Ad esempio, il vettore di rotazione di 45 gradi (utilizzando la rotazione in senso antiorario) comporta le operazioni seguenti:

![](math-images/image8.png "Il vettore di rotazione di 45 gradi utilizzando i risultati di rotazione in senso antiorario in questo")

Fortunatamente, velocità, accelerazione e posizione anche possono tutti essere ruotate con lo stesso codice indipendentemente dal modo in cui vengono applicati i valori. La funzione di uso generale seguente consente di ruotare un vettore da un valore di rotazione CocosSharp:


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

Una conoscenza approfondita del `RotateVector` metodo è necessario acquisire familiarità con le funzioni trigonometriche coseno e seno insieme alcuni algebra lineare, che non rientra nell'ambito di questo articolo. Tuttavia, una volta implementato il `RotateVector` metodo può essere utilizzato per qualsiasi vettore, tra cui un vettore di velocità di rotazione. Ad esempio, il codice seguente può essere generato in una direzione specificata da un punto di `rotation` valore:


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

Questo codice potrebbe produrre simile al seguente:

![](math-images/image9.png "Questo codice può produrre un risultato simile a questa schermata")


# <a name="summary"></a>Riepilogo

Questa guida illustra i concetti matematici comuni nello sviluppo di gioco 2D. Viene illustrato come assegnare e implementare velocità e l'accelerazione e viene descritto come eseguire la rotazione di oggetti e i vettori per lo spostamento in qualsiasi direzione.