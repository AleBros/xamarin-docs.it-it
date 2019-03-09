---
title: Matematica 2D con CocosSharp
description: Questa guida illustra la matematica 2D per lo sviluppo di giochi. Usa CocosSharp in cui viene illustrato come eseguire attività comuni di sviluppo di giochi e illustra le operazioni matematiche alla base di queste attività.
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: ac84d5b28b0f211dccb1697a4b3dbbc9cedf81e9
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670161"
---
# <a name="2d-math-with-cocossharp"></a>Matematica 2D con CocosSharp

_Questa guida illustra la matematica 2D per lo sviluppo di giochi. Usa CocosSharp in cui viene illustrato come eseguire attività comuni di sviluppo di giochi e illustra le operazioni matematiche alla base di queste attività._

Per posizionare e spostare gli oggetti con il codice è una parte fondamentale di sviluppare giochi di qualsiasi dimensione. Il posizionamento e lo spostamento di richiedono l'uso di matematica, che indica se un gioco richiede lo spostamento di un oggetto lungo una linea retta, o l'uso di trigonometria per la rotazione. Questo documento illustra gli argomenti seguenti:

 - Velocità
 - Accelerazione
 - Rotazione di oggetti CocosSharp
 - Uso di rotazione con velocità

Gli sviluppatori non hanno uno sfondo matematiche complesse o che sono prolungata-dimenticate questi argomenti dall'istituto di istruzione, non devi preoccuparti, in questo documento verrà suddividere i concetti in parti dimensioni cenni accompagnamento teoriche spiegazioni con esempi pratici. In breve, questo articolo verrà rispondere alla domanda di student annoso matematica: "Quando è effettivamente necessario utilizzare queste cose?"


## <a name="requirements"></a>Requisiti

Sebbene questo documento è incentrato principalmente sul lato di CocosSharp matematico, esempi di codice presuppongono l'uso degli oggetti che ereditano form `CCNode`. Inoltre, poiché `CCNode` non includerà i valori per la velocità e l'accelerazione, il codice presuppone l'utilizzo delle entità che forniscono i valori, ad esempio VelocityX, VelocityY, AccelerationX e AccelerationY. Per altre informazioni sulle entità, vedere la procedura dettagliata sul [entità in CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="velocity"></a>Velocità

Gli sviluppatori di giochi viene utilizzato il termine *velocità* per descrivere come un oggetto è in movimento – specificamente velocità con cui un elemento è lo spostamento e la direzione che non è in movimento. 

Velocità viene definita utilizzando due tipi di unità: un'unità di posizione e un'unità di tempo. Ad esempio, velocità di un'auto è definito come chilometri all'ora o chilometri all'ora. Sviluppatori di giochi spesso Usa pixel al secondo per definire la velocità è un oggetto viene spostato. Ad esempio, un elenco puntato può spostare una velocità di 300 pixel al secondo. Vale a dire, se un punto elenco è in movimento a 300 pixel al secondo, quindi è stati spostati 600 unità in due secondi e le 900 unità in tre secondi e così via. Più in generale, il valore di velocità *aggiunge* nella posizione di un oggetto (come si vedrà di seguito).

Anche se usassimo velocità per spiegare le unità di velocità, la velocità di termini in genere fa riferimento a un valore indipendente dalla direzione, mentre la velocità di termine si riferisce a velocità e direzione. L'assegnazione della velocità del punto elenco (presupponendo che punto elenco è una classe che include le proprietà necessarie) può quindi avvenire come segue:


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>Implementazione di velocità

CocosSharp neimplementuje metodu velocità, in modo che gli oggetti che richiedono lo spostamento saranno necessario implementare la propria logica di spostamento. Nuovi sviluppatori di giochi implementazione velocità spesso commettono l'errore di rendere loro velocità dipende dalla frequenza dei fotogrammi. Vale a dire, quanto segue *implementazione non corretta* risulterà per fornire risultati corretti, ma si baseranno sulla frequenza di fotogrammi del gioco:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

Se il gioco viene eseguito con una frequenza frame superiore (ad esempio, 60 fotogrammi al secondo invece di 30 fotogrammi al secondo), quindi l'oggetto risulterà muoverti più rapidamente rispetto a se in esecuzione con una frequenza frame più lenta. Analogamente, se il gioco è in grado di elaborare i frame in alto di una frequenza dei fotogrammi (che potrebbe essere causata da processi in background usando le risorse del dispositivo), verrà visualizzato il gioco rallentamento.

Per questo motivo, velocità viene spesso implementata usando un valore di ora. Ad esempio, se il `seconds` variabile rappresenta il numero (o frazione) di secondi dal momento che è stata applicata alla velocità ora ultimo, quindi il codice seguente restituirà nell'oggetto uno spostamento dei coerente indipendentemente dalla frequenza dei fotogrammi:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

È consigliabile che un gioco che viene eseguita una minore frequenza dei fotogrammi aggiornerà la posizione dei relativi oggetti meno frequentemente. Di conseguenza, ogni aggiornamento comporta gli oggetti passaggio ulteriore di quanto farebbero se il gioco aggiornavano più frequentemente. Il `seconds` valore account a tale scopo, segnalando il tempo trascorso dopo l'ultimo aggiornamento.

Per un esempio di come aggiungere basati sul tempo di spostamento, vedere [ricetta che coprono tempo basato su spostamento](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/game_development/time_based_movement).


### <a name="calculating-positions-using-velocity"></a>Il calcolo delle posizioni con velocità

Velocità utilizzabile per eseguire stime su un oggetto in cui sarà dopo una certa quantità di tempo o per contribuire a ottimizzare il comportamento degli oggetti senza la necessità di eseguire il gioco. Ad esempio, uno sviluppatore che sta implementando lo spostamento di un elenco puntato attivato deve impostare la velocità del punto elenco dopo che ne viene creata un'istanza. Le dimensioni dello schermo sono utilizzabile per fornire una base per l'impostazione della velocità. Vale a dire, se lo sviluppatore sa che il punto elenco è consigliabile spostare l'altezza dello schermo in 2 secondi, quindi la velocità deve essere impostata all'altezza dello schermo diviso per 2. Se la schermata di 800 pixel in altezza, velocità del punto elenco verrebbe impostato a 400 (ovvero 800/2).

Analogamente, per la logica di gioco potrebbe essere necessario calcolare quanto tempo un oggetto sarà necessario per raggiungere una destinazione data la velocità. Questo può essere calcolato dividendo la distanza per viaggiare dalla velocità dell'oggetto viaggiano di frequente. Ad esempio, il codice seguente viene illustrato come assegnare testo a un'etichetta che visualizza quanti finché un missilistico raggiunge il valore di destinazione:


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>Accelerazione

*Accelerazione* è un concetto comune ampiamente lo sviluppo di giochi e condivide molte analogie con velocità. Accelerazione quantifica se un oggetto viene velocizzare o rallentare (come il valore della velocità cambia nel corso del tempo). Accelerazione *aggiunge* a velocità, proprio come velocità viene aggiunto alla posizione. Applicazioni comuni di accelerazione includono la gravità, un'automobile velocizzazione e una nave spazio relativo thrusters di attivazione. 

Simile a velocità, l'accelerazione è definito in una posizione e l'unità di tempo; Tuttavia, l'unità di tempo dell'accelerazione è detta un *quadratico* unit, che riflette le definite matematicamente accelerazione. Vale a dire l'accelerazione del gioco viene spesso misurato in *pixel al secondo elevato al quadrato*.

Se un oggetto dispone di un'accelerazione X 10 unità al secondo elevato al quadrato, ciò significa che la velocità comporta un aumento di 10 ogni secondo. Se a partire dalla produttività, dopo un secondo sarà passando a 10 unità al secondo, dopo due secondi 20 unità al secondo, e così via.

L'accelerazione in due dimensioni richiede un componente X e Y, in modo che può essere assegnato come segue:


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>Accelerazione e la decelerazione

Sebbene decelerazione e accelerazione in alcuni casi si differenziano in vocale ogni giorno, non vi è alcuna differenza tra le due tecnica. La gravità è una forza che comporta l'accelerazione. Se un oggetto viene generata un'eccezione verso l'alto quindi la gravità rallenterà lo (decelerando), ma dopo che l'oggetto è stato arrestato aumenta progressivamente e accumula nella stessa direzione gravità quindi la gravità sta accelerando lo (accelerazione). Come illustrato di seguito, l'applicazione di un'accelerazione non cambiano se viene applicato nella stessa direzione o opposto direzione dello spostamento. 


### <a name="implementing-acceleration"></a>Implementazione di accelerazione

L'accelerazione è simile a velocità durante l'implementazione: non viene implementato automaticamente da CocosSharp e l'accelerazione basata su tempo è l'implementazione desiderata (in contrapposizione l'accelerazione basata su frame). Un'implementazione semplice accelerazione (insieme alla velocità) può quindi avvenire, ad esempio:

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

Il codice riportato sopra è ciò che si intende un *approssimazione lineare* per l'implementazione di accelerazione. In realtà, implementa l'accelerazione con un livello piuttosto chiudere di accuratezza, ma non è un modello perfettamente accurato di accelerazione. È incluso in precedenza aiuta a spiegare il concetto di modalità di implementazione di accelerazione.

L'implementazione seguente è un'applicazione matematicamente precisa di accelerazione e la velocità:


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

La differenza più ovvia per il codice riportato sopra è il `halfSecondsSquared` variabile e il relativo utilizzo per applicare l'accelerazione per posizionare. Il motivo matematico esula dall'ambito di questa esercitazione, ma gli sviluppatori interessati alle operazioni matematiche alla base di questa possono trovare altre informazioni, [questa discussione sull'integrazione di accelerazione.](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

L'impatto pratico di `halfSecondSquare` è che l'accelerazione comporterà matematicamente con precisione e in modo prevedibile indipendentemente dalla frequenza dei fotogrammi. L'approssimazione lineare di accelerazione è soggetto a frequenza dei fotogrammi: più basso la frequenza dei fotogrammi scende diventa l'approssimazione meno precisa. Usando `halfSecondsSquared` garanzie che codice comporterà lo stesso indipendentemente dalla frequenza dei fotogrammi.


## <a name="angles-and-rotation"></a>Gli angoli e rotazione

Ad esempio oggetti visivi `CCSprite` supportano la rotazione attraverso un `Rotation` variabile. Ciò può essere impostato su un valore per impostare la rotazione in gradi. Ad esempio, il codice seguente illustra come ruotare un `CCSprite` istanza:


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

L'opzione produce quanto segue:

![](math-images/image1.png "Ciò comporta in questo screenshot")

Si noti che la rotazione è 45 gradi in senso orario (che per motivi di cronologia è l'opposto del modo in cui viene applicata matematicamente rotazione):

![](math-images/image2.png "Si noti che la rotazione di 45 gradi in senso orario che, per motivi di cronologia è l'opposto del modo in cui viene applicata matematicamente rotazione")

In genere la rotazione per CocosSharp e matematiche approfondite regolari può essere visualizzata come indicato di seguito:

![](math-images/image3.png "In genere la rotazione per CocosSharp e matematiche approfondite regolari può essere visualizzata simile al seguente")

![](math-images/image4.png "In genere la rotazione per CocosSharp e matematiche approfondite regolari può essere visualizzata simile al seguente")

Questa distinzione è importante perché il `System.Math` classe utilizza la rotazione in senso antiorario, pertanto è necessario invertire gli angoli quando si lavora con gli sviluppatori esperti in questa classe `CCNode` istanze.

Si noti che nelle figure precedenti visualizzano rotazione in gradi; Tuttavia, alcune funzioni matematiche (ad esempio le funzioni nel `System.Math` dello spazio dei nomi) previsto e restituire valori in *radianti* anziché gradi. Esamineremo come eseguire la conversione tra tipi di unità di due più avanti in questa Guida.


### <a name="rotating-to-face-a-direction"></a>Ruotare un oggetto per affrontare una direzione

Come illustrato in precedenza, `CCSprite` può essere ruotato utilizzando i `Rotation` proprietà. Il `Rotation` proprietà viene fornita dai `CCNode` (la classe base per `CCSprite`), ovvero la rotazione può essere applicata a entità che ereditano da `CCNode` anche. 

Alcuni giochi richiedono oggetti per essere ruotate in modo da affrontare una destinazione. Ad esempio un libro enemy controllati dal computer a una destinazione di Windows Media player o una nave spazio in volo verso il punto in cui l'utente tocca la schermata di ripresa. Tuttavia, un valore di rotazione deve prima essere calcolato in base la posizione dell'entità da ruotare e la posizione di destinazione da affrontare.

Questo processo richiede diversi passaggi:

 - Che identifica la *offset* della destinazione. Offset fa riferimento la distanza tra l'entità la rotazione e l'entità di destinazione X e Y.
 - Calcola l'angolo dall'offset usando la funzione di trigonometria arcotangente (illustrata in dettaglio di seguito).
 - La modifica di una differenza tra 0 gradi rivolta verso destra e la direzione che fa riferimento l'entità la rotazione quando non ruotata.
 - Modifica della differenza tra la rotazione matematica (in senso antiorario) e la rotazione CocosSharp (in senso orario).

La funzione seguente (si presuppone che deve essere scritto in un'entità) consente di ruotare l'entità in modo che sia una destinazione:


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

Il codice riportato sopra può essere utilizzato per ruotare un'entità in modo che sia rivolta verso il punto in cui l'utente tocca la schermata, come indicato di seguito:


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

Questo codice produce il comportamento seguente:

![](math-images/image5.gif "Questo codice genera questo comportamento")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>Uso della funzione Atan2 per convertire agli offset angoli

`System.Math.Atan2` può essere utilizzato per convertire un offset a un angolo. Il nome della funzione `Atan2` proviene l'arcotangente di funzioni trigonometriche. Il suffisso "2" consente di distinguere questa funzione dallo standard `Atan` funzione, che corrisponde strettamente il comportamento matematico dell'arcotangente. Arcotangente è una funzione che restituisce un valore compreso tra -90 e + 90 gradi (o l'equivalente in radianti). Molte applicazioni, inclusi i giochi di computer, spesso richiedono di 360 gradi dei valori, in modo che il `Math` classe include `Atan2` per soddisfare questa esigenza.

Si noti che il codice sopra riportato passa il parametro Y in primo luogo, quindi il parametro di X, quando si chiama il `Atan2` (metodo). Si tratta con le versioni precedenti dalle consuete X, Y ordinamento delle coordinate di posizione. Per altre informazioni [, vedere la documentazione Atan2](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx).

È anche importante notare che il valore restituito da `Atan2` è espresso in radianti, che corrisponde a un'altra unità utilizzata per misurare gli angoli. Questa Guida non illustra i dettagli di radianti, ma tenere presente che tutte le funzioni trigonometriche il `System.Math` dello spazio dei nomi utilizzano radianti, in modo che tutti i valori devono essere convertiti in gradi prima di essere usata negli oggetti CocosSharp. Sono disponibili altre informazioni su radianti [nella pagina di Wikipedia relativa in radianti](https://en.wikipedia.org/wiki/Radian).

#### <a name="forward-angle"></a>Angolo di inoltro

Una volta il `FacePoint` metodo converte l'angolo in radianti, è stato definito un `forwardAngle` valore. Questo valore rappresenta l'angolo, in cui è rivolta verso l'entità quando il valore di rotazione è uguale a 0. In questo esempio si presuppone che l'entità è rivolta verso l'alto, che è di 90 gradi, quando si usa una rotazione matematica (in contrapposizione CocosSharp rotazione). Usiamo la rotazione matematica qui perché è ancora stato ancora invertito la rotazione per CocosSharp.

Di seguito viene illustrato che un'entità con un `forwardAngle` di 90 gradi è simile:

![](math-images/image6.png "Viene illustrato ciò che potrebbe essere simile un'entità con un forwardAngle di 90 gradi")


### <a name="angled-velocity"></a>Velocità angolare

Finora abbiamo esaminato come convertire un offset in un angolo. In questa sezione va in altra direzione: accetta un angolo e lo converte in X e Y. Esempi comuni includono un'automobile lo spostamento nella direzione sia rivolto verso o una nave spazio la ripresa di un punto elenco che li sposta nella direzione è rivolta verso la spedizione. 

Concettualmente, la velocità può essere calcolata dalla innanzitutto definire la velocità desiderata quando non ruotata, quindi la rotazione di tale velocità per l'angolo di un'entità nel sistema. Per spiegare questo concetto, velocità (e all'accelerazione) può essere visualizzati come un 2-dimensionale *vettore* (che viene in genere tracciato sotto forma di freccia). Un vettore per un valore di velocità x = Y e 100 = 0 possono essere visualizzati come indicato di seguito:

![](math-images/image7.png "Un vettore per un valore di velocità x = Y e 100 = 0 possono essere visualizzati come indicato di seguito")

Questo vettore può essere ruotato per generare una nuova velocità. Ad esempio, il vettore di rotazione di 45 gradi (usando la rotazione in senso antiorario) produce quanto segue:

![](math-images/image8.png "Il vettore di rotazione di 45 gradi usando la rotazione in senso antiorario risultati in questo")

Fortunatamente, velocità, l'accelerazione e posizione anche possono tutti essere ruotate con lo stesso codice indipendentemente dal modo in cui vengono applicati i valori. La funzione seguente per utilizzo generico è utilizzabile per ruotare un vettore da un valore di rotazione CocosSharp:


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

Comprende appieno il `RotateVector` metodo è necessario acquisire familiarità con le funzioni trigonometriche coseno e seno con alcuni algebra lineare, che esula dall'ambito di questo articolo. Tuttavia, una volta implementato il `RotateVector` metodo può essere utilizzato per ruotare vettori, inclusi un vettore di velocità. Ad esempio, il codice seguente può essere generato un elenco puntato in una direzione specificata dal `rotation` valore:


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

Questo codice potrebbe produrre simile:

![](math-images/image9.png "Questo codice potrebbe produrre qualcosa di simile allo screenshot")


## <a name="summary"></a>Riepilogo

Questa guida illustra i concetti matematici comuni nello sviluppo di giochi 2D. Viene illustrato come assegnare e implementare la velocità e l'accelerazione e illustra come eseguire la rotazione di oggetti e i vettori per lo spostamento in qualsiasi direzione.
