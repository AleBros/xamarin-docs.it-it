---
title: Riferimento Game pad MonoGame
description: Game pad è una classe standard multipiattaforma per l'accesso ai dispositivi di input in MonoGame.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 0746cc0153e9e00309b7227ff3e8a683cdc46835
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="monogame-gamepad-reference"></a>Riferimento Game pad MonoGame

_Game pad è una classe standard multipiattaforma per l'accesso ai dispositivi di input in MonoGame._

`GamePad` può essere utilizzato per leggere l'input proveniente da dispositivi di input in più piattaforme MonoGame. Questa guida viene illustrato come utilizzare la classe Game pad. Poiché ogni dispositivo di input è diverso nel layout e il numero di pulsanti che fornisce, in questa guida include diagrammi che mostrano i mapping di dispositivo diverse.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>Game pad come sostituzione per Xbox360GamePad

L'API XNA originale fornita la `Xbox360GamePad` classe per la lettura dell'input da un gioco nel PC o Xbox 360. MonoGame è sostituito con un `GamePad` classe poiché il controller Xbox 360 non può essere utilizzato nella maggior parte delle piattaforme MonoGame (ad esempio iOS o Xbox One). Nonostante la modifica del nome, l'utilizzo del `GamePad` è simile alla classe il `Xbox360GamePad` classe.

## <a name="reading-input-from-gamepad"></a>Lettura di Input dalla Game pad

La `GamePad` classe fornisce un modo standardizzato di lettura di input su qualsiasi piattaforma MonoGame. Fornisce informazioni sono disponibili due metodi:

- `GetState` : restituisce lo stato corrente di pulsanti, bastoncini analogici e d-pad del controller.
- `GetCapabilities` : restituisce informazioni sulle funzionalità dell'hardware, ad esempio se il controller ha alcuni pulsanti o resistenza alle vibrazioni supporta.

### <a name="example-moving-a-character"></a>Esempio: Spostamento di un carattere

Il codice seguente viene illustrato come utilizzare la stick thumb a sinistra per spostarsi di un carattere impostando il relativo `XVelocity` e `YVelocity` proprietà. Questo codice presuppone che `characterInstance` è un'istanza di un oggetto che ha `XVelocity` e `YVelocity` proprietà:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Esempio: Rilevamento push

`GamePadState` fornisce informazioni sullo stato corrente del controller, ad esempio se un determinato tipo di pulsante è premuto. Alcune azioni, ad esempio, per un carattere di passare, richiedono la verifica se è stato premuto il pulsante di (non è stato premuto ultimo fotogramma, ma non è disponibile questo frame) o rilasciato (era all'ultimo fotogramma, ma non verso il basso di questo frame). 

Per eseguire questo tipo di logica, le variabili locali che archiviano il frame precedente `GamePadState` e il frame corrente `GamePadState` deve essere creato. Nell'esempio seguente viene illustrato come archiviare e utilizzare il frame precedente `GamePadState` per implementare il passaggio:

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

### <a name="example-checking-for-buttons"></a>Esempio: Controllo per i pulsanti

`GetCapabilities` può essere utilizzato per verificare se un controller è un hardware specifico, ad esempio un pulsante specifico o stick analogico. Il codice seguente viene illustrato come cercare i pulsanti di B e Y in un controller di un gioco che richiede la presenza di entrambi i pulsanti:

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
> I pacchetti NuGet per MonoGame 3.5 non includono il supporto per wireless di gioco. Utilizzo della classe Game pad in iOS richiede compilazione 3.5 MonoGame dall'origine o utilizzando i file binari MonoGame 3.6 NuGet. 

### <a name="ios-game-controller"></a>iOS di gioco

La `GamePad` classe restituisce proprietà lette dal controller wireless. Le proprietà di `GamePad` offrono una buona copertura per iOS standard hardware del controller, come illustrato nel diagramma seguente:

![](input-images/image1.png "Le proprietà del game pad forniscono un'adeguata copertura per iOS standard hardware del controller, come illustrato in questo diagramma")

## <a name="apple-tv"></a>Apple TV

Giochi Apple TV è possono utilizzare il Siri Remote o wireless di gioco per l'input.

### <a name="siri-remote"></a>Siri remoto

*Siri remoto* è il dispositivo di input nativo per Apple TV. Anche se i valori di Siri remoto possono essere letta tramite gli eventi (come illustrato nel [Siri remoto e i controller Bluetooth Guida](~/ios/tvos/platform/remote-bluetooth.md)), la `GamePad` classe può restituire valori da remoto Siri.

Si noti che `GamePad` può solo leggere l'input dal pulsante play e toccare superficie: 

![](input-images/image2.png "Si noti che Game pad può solo leggere l'input dal pulsante play e toccare superficie")

Dopo il tocco lo spostamento di superficie di attacco è di lettura il `DPad` proprietà, lo spostamento dei valori sono segnalati tramite la `ButtonState` classe. In altre parole, i valori sono disponibili solo come `ButtonState.Pressed` o `ButtonState.Released`, anziché i valori numerici o movimenti.

### <a name="apple-tv-game-controller"></a>Apple TV gioco

Gioco per Apple TV si comportano in modo identico alle periferiche di gioco per le app iOS. Per ulteriori informazioni, vedere il [iOS sezione gioco](#iOS_Game_Controller). 

## <a name="xbox-one"></a>Xbox One

La console Xbox One supporta la lettura dell'input da un gioco Xbox One.

### <a name="xbox-one-game-controller"></a>Gioco Xbox uno

Il gioco Xbox One è il dispositivo di input più comune per Xbox One. La `GamePad` classe fornisce i valori di input dall'hardware di gioco.

![](input-images/image3.png "La classe Game pad fornisce valori di input dall'hardware di gioco")

## <a name="summary"></a>Riepilogo

Questa guida fornita una panoramica del MonoGame `GamePad` classe, come implementare la logica di input per la lettura e i diagrammi di comune `GamePad` implementazioni.

## <a name="related-links"></a>Collegamenti correlati

- [Game pad MonoGame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
