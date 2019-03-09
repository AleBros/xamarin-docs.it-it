---
title: Riferimento su GamePad MonoGame
description: Questo documento descrive GamePad, una classe multipiattaforma per l'accesso ai dispositivi di input in MonoGame. Questo viene illustrato come leggere il gamepad come input e fornisce il codice di esempio.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c1c03e0ec17ade57536b4ed121469e3ae2274e75
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668972"
---
# <a name="monogame-gamepad-reference"></a>Riferimento su GamePad MonoGame

_GamePad è una classe standard, lo sviluppo multipiattaforma per l'accesso ai dispositivi di input in MonoGame._

`GamePad` può essere utilizzato per leggere l'input da input dispositivi su più piattaforme MonoGame. Questa guida illustra come usare la classe GamePad. Poiché ogni dispositivo di input è diverso nei layout e il numero di pulsanti che fornisce, questa guida include i diagrammi che mostrano i mapping di dispositivo diverse.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>GamePad come una sostituzione per Xbox360GamePad

L'API XNA originale fornita il `Xbox360GamePad` classe per la lettura di input da un controller di gioco nel PC o Xbox 360. MonoGame è sostituito con un `GamePad` classe poiché i controller Xbox 360 non possono essere usati nella maggior parte delle piattaforme MonoGame (ad esempio, iOS o in Xbox One). Nonostante la modifica del nome, l'utilizzo dei `GamePad` classe è simile al `Xbox360GamePad` classe.

## <a name="reading-input-from-gamepad"></a>La lettura di Input da su GamePad

Il `GamePad` classe fornisce una modalità standardizzata per la lettura di input su qualsiasi piattaforma MonoGame. Fornisce informazioni sono disponibili due metodi:

- `GetState` : restituisce lo stato corrente dei pulsanti, bastoncini analogici e d-riempimento del controller.
- `GetCapabilities` : restituisce informazioni sulle funzionalità dell'hardware, ad esempio se il controller ha alcuni pulsanti o supporta vibrazione.

### <a name="example-moving-a-character"></a>Esempio: Lo spostamento di un carattere

Il codice seguente illustra come la chiavetta thumb a sinistra consente di spostare un carattere impostando relativi `XVelocity` e `YVelocity` proprietà. Questo codice si presuppone che `characterInstance` è un'istanza di un oggetto che ha `XVelocity` e `YVelocity` proprietà:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Esempio: Rilevamento di notifiche push

`GamePadState` fornisce informazioni sullo stato corrente del controller, ad esempio se un determinato pulsante viene premuto. Determinate azioni, ad esempio per rendere un carattere jump, richiedono la verifica se il pulsante è stato eseguito il push (non è stato premuto ultimo fotogramma, ma non è attivo questo frame) o del rilascio (era premuto ultimo fotogramma, ma non verso il basso il frame).

Per eseguire questo tipo di logica, le variabili locali che archiviano i frame precedente `GamePadState` e il frame corrente `GamePadState` deve essere creato. Nell'esempio seguente viene illustrato come archiviare e utilizzare il frame precedente `GamePadState` implementare del passaggio:

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed =
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>Esempio: Verifica per i pulsanti

`GetCapabilities` può essere utilizzato per controllare se un controller dispone di hardware specifico, ad esempio un particolare pulsante o un joystick analogico. Il codice seguente viene illustrato come cercare i pulsanti B e Y in un controller in un gioco che richiede la presenza di entrambi i pulsanti:

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

le app iOS supportano l'input di gioco senza fili.

> [!IMPORTANT]
> I pacchetti NuGet per MonoGame 3.5 non includono il supporto per periferiche di gioco senza fili. Utilizzo della classe GamePad in iOS richiede compilazione 3.5 MonoGame dall'origine o usando i file binari NuGet 3.6 MonoGame.

### <a name="ios-game-controller"></a>Controller di gioco iOS

Il `GamePad` classe restituisce delle proprietà lette dal controller wireless. Le proprietà nel `GamePad` offrono una buona copertura al programma IOS standard hardware del controller, come illustrato nel diagramma seguente:

![](input-images/image1.png "Le proprietà nella finestra di GamePad offrono una buona copertura al programma IOS standard hardware del controller, come illustrato nella figura seguente")

## <a name="apple-tv"></a>Apple TV

Giochi Apple TV possono usare il remoti per Siri o periferiche di gioco wireless per l'input.

### <a name="siri-remote"></a>Remoti per Siri

*Remoti per Siri* è il dispositivo di input nativo per Apple TV. Anche se i valori da Siri remoto possono essere letti tramite eventi (come illustrato nella [Guida controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md)), il `GamePad` classe può restituire valori da remoto Siri.

Si noti che `GamePad` può solo leggere l'input dal pulsante di riproduzione e touch area:

![](input-images/image2.png "Si noti che GamePad può solo leggere l'input dal pulsante di riproduzione e touch area")

Poiché il tocco superficie dello spostamento è di lettura il `DPad` proprietà, lo spostamento dei valori sono segnalati tramite i `ButtonState` classe. In altre parole, i valori sono disponibili solo come `ButtonState.Pressed` o `ButtonState.Released`, anziché a valori numerici o movimenti.

### <a name="apple-tv-game-controller"></a>Gioco di Apple TV

Periferiche di gioco per Apple TV si comportano in modo identico a periferiche di gioco per le app iOS. Per altre informazioni, vedere la [un controller di gioco iOS](#iOS-game-controller). 

## <a name="xbox-one"></a>Xbox One

La console Xbox One supporta la lettura di input da un controller di giochi Xbox One.

### <a name="xbox-one-game-controller"></a>Gioco per Xbox One

Il controller di giochi Xbox One è il dispositivo di input più comune per Xbox One. Il `GamePad` classe fornisce i valori di input dall'hardware di gioco.

![](input-images/image3.png "La classe GamePad fornisce i valori di input dall'hardware di gioco")

## <a name="summary"></a>Riepilogo

Questa guida fornita una panoramica del MonoGame `GamePad` classe, come implementare la logica di input per la lettura e i diagrammi di common `GamePad` implementazioni.

## <a name="related-links"></a>Collegamenti correlati

- [MonoGame GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
