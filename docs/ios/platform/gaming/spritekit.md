---
title: SpriteKit in xamarin. IOS
description: Questo documento descrive SpriteKit, il framework di grafica 2D di Apple che si integra con SceneKit, incorpora la fisica e l'animazione, include il supporto per l'illuminazione e sfondi e altro ancora. SpriteKit può essere utilizzato per creare giochi 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: ef1e9a98b76166f4ee5638d1ab9762896d1e3bc8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293899"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit in xamarin. IOS

SpriteKit, il framework di grafica 2D da Apple, dispone di alcune nuove interessanti funzionalità in iOS 8 e OS X Yosemite. Queste includono l'integrazione con SceneKit, supporto dello shader, illuminazione, ombreggiature, vincoli, generazione di mappe normali e miglioramenti di fisica. In particolare, le nuove funzionalità di effetti fisici rendono molto semplice aggiungere effetti realistici di un gioco.

## <a name="physics-bodies"></a>Corpi di fisica

SpriteKit include un 2D, physics corpo rigida API. Ogni sprite ha un corpo fisica associata (`SKPhysicsBody`) che definisce le proprietà di fisica, ad esempio massa e problemi, nonché la geometria del corpo del mondo fisico.

## <a name="creating-a-physics-body-from-a-texture"></a>Creazione di un corpo fisica da una trama
SpriteKit supporta ora la derivazione corpo fisica di sprite dalla trama. Questo rende facile da implementare conflitti con un aspetto più naturali.

Ad esempio, si noti che in collisione seguente come il banana e monkey entri in conflitto quasi sulla superficie di ogni immagine:
 
![](spritekit-images/image13.png "Il banana e monkey entri in conflitto quasi sulla superficie di ogni immagine")

SpriteKit semplifica la creazione di questo tipo un corpo di effetti fisici possibili con una singola riga di codice. È sufficiente chiamare `SKPhysicsBody.Create` con le dimensioni e la trama: sprite. PhysicsBody = SKPhysicsBody.Create (sprite. Trama, sprite. Dimensioni).

## <a name="alpha-threshold"></a>Soglia alfa

Oltre a impostare semplicemente il `PhysicsBody` proprietà direttamente alla geometria derivata dalla trama, le applicazioni possono impostare e soglia alfa per controllare come verrà derivata della geometria. 

La soglia alfa definisce il valore minimo alfa da includere nel corpo fisica risultante deve avere un pixel. Ad esempio, il codice seguente comporta un corpo fisica leggermente diverso:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

L'effetto della modifica della soglia alfa simile al seguente ottimizza la collisione precedente, in modo che il monkey rientra quando che entrino in conflitto con il banana:

![](spritekit-images/image14.png "Il monkey rientra quando che entrino in conflitto con il banana")
 
## <a name="physics-fields"></a>I campi di fisica

Un altro straordinario arricchimento SpriteKit è supportano il nuovo campo fisica. Questi consentono di aggiungere altri elementi, ad esempio campi vortex, gravità radiale campi e i campi di spring per citarne solo alcuni.

Campi fisica vengono creati utilizzando la classe SKFieldNode, che viene aggiunto a una scena esattamente come qualsiasi altro `SKNode`. Esistono svariati metodi factory su `SKFieldNode` creare campi fisica diversa. È possibile creare un campo di spring chiamando `SKFieldNode.CreateSpringField()`, un campo gravità radiale chiamando `SKFieldNode.CreateRadialGravityField()`e così via.

`SKFieldNode` ha anche proprietà per controllare gli attributi di campo, ad esempio il livello di campo, l'area di campo e l'attenuazione delle forze di campo.

## <a name="spring-field"></a>Campo di Spring

Ad esempio, il codice seguente crea un campo di spring e lo aggiunge alla scena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

È quindi possibile aggiungere Sprite e impostare i `PhysicsBody` proprietà in modo che il campo fisica avrà effetto su sprite, così come il codice seguente quando l'utente tocca lo schermo:

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

In questo modo il bananas a oscillazione, ad esempio una molla intorno al nodo di campo:

![](spritekit-images/image15.png "Il bananas oscillazione, ad esempio una molla intorno al nodo di campo")
 
## <a name="radial-gravity-field"></a>Campo gravità radiale

Aggiunta di un campo diverso è simile. Ad esempio, il codice seguente crea un campo radiale gravità:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Ciò comporta un campo force diverso, in cui il bananas vengono estratti in senso radiale sul campo:

![](spritekit-images/image16.png "Il bananas vengono estratti in senso radiale intorno al campo")
