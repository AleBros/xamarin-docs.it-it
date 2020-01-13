---
title: SpriteKit in Xamarin.iOS
description: Questo documento descrive SpriteKit, il Framework di grafica 2D di Apple che si integra con SceneKit, incorpora la fisica e l'animazione, include il supporto per l'illuminazione e l'ombreggiatura e altro ancora. SpriteKit può essere usato per creare giochi 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: e8829211ebf06eea224eade3f1b9d836207cdd64
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032477"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit in Xamarin.iOS

SpriteKit, il Framework di grafica 2D di Apple, presenta alcune nuove interessanti funzionalità in iOS 8 e OS X Yosemite. Sono inclusi l'integrazione con SceneKit, il supporto dello shader, l'illuminazione, le ombre, i vincoli, la generazione normale della mappa e i miglioramenti della fisica. In particolare, le nuove funzionalità di fisica facilitano l'aggiunta di effetti realistici a un gioco.

## <a name="physics-bodies"></a>Corpi fisici

SpriteKit include un'API di fisica del corpo rigida 2D. A ogni sprite è associato un corpo fisico (`SKPhysicsBody`) che definisce le proprietà di fisica, ad esempio la massa e l'attrito, nonché la geometria del corpo nel mondo fisico.

## <a name="creating-a-physics-body-from-a-texture"></a>Creazione di un corpo fisico da una trama
SpriteKit supporta ora la derivazione del corpo fisico di uno sprite dalla relativa trama. In questo modo è più semplice implementare collisioni che sembrano più naturali.

Si noti, ad esempio, il modo in cui la banana e la scimmia si scontrano quasi alla superficie di ogni immagine:

![](spritekit-images/image13.png "The banana and monkey collide nearly at the surface of each image")

SpriteKit rende possibile la creazione di questo corpo fisico con una sola riga di codice. È sufficiente chiamare `SKPhysicsBody.Create` con trama e dimensioni: Sprite. PhysicsBody = SKPhysicsBody. Create (sprite. Trama, sprite. Dimensioni);

## <a name="alpha-threshold"></a>Soglia alfa

Oltre a impostare semplicemente la proprietà `PhysicsBody` direttamente sulla geometria derivata dalla trama, le applicazioni possono impostare e la soglia alfa per controllare la modalità di derivazione della geometria. 

La soglia alfa definisce il valore alfa minimo che deve essere incluso in un pixel nel corpo fisico risultante. Il codice seguente, ad esempio, genera un corpo fisico leggermente diverso:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

L'effetto della modifica della soglia alfa, come questa, ottimizza la collisione precedente, in modo che la scimmia cada in collisione con la banana:

![](spritekit-images/image14.png "The monkey falls over when colliding with the banana")

## <a name="physics-fields"></a>Campi di fisica

Un altro aspetto importante di SpriteKit è il nuovo supporto per i campi di fisica. Che consentono di aggiungere elementi come i campi Vortex, i campi di gravità radiale e i campi Spring per citarne solo alcuni.

I campi di fisica vengono creati usando la classe SKFieldNode, che viene aggiunta a una scena Analogamente a qualsiasi altra `SKNode`. Sono disponibili diversi metodi factory `SKFieldNode` per creare campi fisici diversi. È possibile creare un campo Spring chiamando `SKFieldNode.CreateSpringField()`, un campo di gravità radiale chiamando `SKFieldNode.CreateRadialGravityField()`e così via.

`SKFieldNode` dispone anche di proprietà per controllare gli attributi di campo, ad esempio il livello di attendibilità del campo, l'area del campo e l'attenuazione delle forze dei campi.

## <a name="spring-field"></a>Campo Spring

Ad esempio, il codice seguente crea un campo Spring e lo aggiunge alla scena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

È quindi possibile aggiungere sprite e impostare le relative proprietà `PhysicsBody` in modo che il campo fisica influirà sugli sprite, come avviene nel codice seguente quando l'utente tocca lo schermo:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

In questo modo le banane oscillano come una molla intorno al nodo del campo:

![](spritekit-images/image15.png "The bananas oscillate like a spring around the field node")

## <a name="radial-gravity-field"></a>Campo gravità radiale

L'aggiunta di un campo diverso è simile. Ad esempio, il codice seguente crea un campo di gravità radiale:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

In questo modo si ottiene un campo Force diverso, in cui le banane vengono estratte radialmente sul campo:

![](spritekit-images/image16.png "The bananas are pulled radially around the field")
