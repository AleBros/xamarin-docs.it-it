---
title: Crea un progetto UWP MonoGame
description: MonoGame è utilizzabile per creare giochi e App per la Universal Windows Platform, destinate a che più dispositivi con una codebase e un set di contenuto.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 9f39580d282defed354f3b9e5cbe4eb1cdec4796
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161150"
---
# <a name="creating-a-monogame-uwp-project"></a>Crea un progetto UWP MonoGame

_MonoGame è utilizzabile per creare giochi e App per la Universal Windows Platform, destinate a che più dispositivi con una codebase e un set di contenuto._

Questa procedura dettagliata illustra la creazione del progetto MonoGame Universal Windows Platform (UWP) e il caricamento di contenuto. App UWP possono essere eseguite su tutti i dispositivi Windows 10, tra cui desktop, Tablet, Windows Phone e Xbox One.

Questa procedura dettagliata crea un progetto vuoto che consente di visualizzare una *Blu fiordaliso* sfondo (il colore di sfondo tradizionale delle App XNA).

## <a name="requirements"></a>Requisiti

Lo sviluppo di App UWP MonoGame richiede:

- Sistema operativo Windows 10
- Qualsiasi versione di Visual Studio 2017
- Strumenti di sviluppo di Windows 10
- Dispositivo di impostazione di modalità sviluppatore
- [MonoGame 3.7.1 per Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) o versione successiva

Per altre informazioni, vedere questo [pagina sulla configurazione per lo sviluppo UWP di Windows 10](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

È possibile sviluppare giochi Xbox One su hardware Xbox One delle vendite al dettaglio. È necessario software aggiuntivo sul PC di sviluppo e Xbox One. Per informazioni sulla configurazione di un ambito per lo sviluppo di giochi Xbox One, vedere questa pagina nel [configurazione di una Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Creazione di un modello vuoto

Dopo che tutte le risorse necessarie sono state installate e la modalità sviluppatore è stata abilitata nel computer Windows 10, è possibile creare un nuovo progetto MonoGame usando Visual Studio seguendo questa procedura:

1. Selezionare **File** > **nuova** > **progetto...**
1. Selezionare il **installata** > **modelli** > **Visual C#**   >  **MonoGame** categoria:

    ![](uwp-images/image1.png "Categoria MonoGame")

1. Selezionare il **MonoGame Windows 10 Universal progetto** opzione:

    ![](uwp-images/image2.png "Selezionare l'opzione di progetto universale MonoGame Windows 10")

1. Immettere un nome per il nuovo progetto e fare clic su **OK**.
Se Visual Studio vengono visualizzati eventuali errori dopo aver fatto clic su OK, verificare che siano installati gli strumenti di Windows 10 e che il dispositivo è in modalità sviluppatore.

Dopo che Visual Studio ha completato la creazione del modello, è possibile eseguire in modo da visualizzare il progetto vuoto in esecuzione:

![](uwp-images/image3.png "Dopo che Visual Studio ha completato la creazione del modello, eseguire il codice per visualizzare il progetto vuoto in esecuzione")

I numeri negli angoli forniscono informazioni di diagnostica. Queste informazioni possono essere rimosse, eliminando il codice nel `App.xaml.cs` nella `DEBUG` blocco nel `OnLaunched` metodo:


```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>In esecuzione su Xbox One

I progetti UWP è possono distribuire in qualsiasi dispositivo Windows 10 dallo stesso progetto. Dopo aver configurato il computer di sviluppo di Windows 10 e Xbox One, passando la destinazione al computer remoto e l'immissione indirizzo IP di Xbox One è possibile distribuire App della piattaforma UWP:

![](uwp-images/remote.png "Le app UWP possono essere distribuite, passando la destinazione al computer remoto e immettere l'indirizzo IP di quelle Xbox")

In Xbox One, il bordo bianco rappresenta l'area non safe per TV. Per altre informazioni, vedere la [sezione di area sicura](#safe-area-on-xbox-one).

![](uwp-images/safearea.png "In Xbox One, il bordo bianco rappresenta l'area non safe per TV")

### <a name="safe-area-on-xbox-one"></a>Area sicura su Xbox One

Sviluppo di giochi per console, è necessario prendere in considerazione l'area di sicurezza, che è un'area al centro dello schermo che deve contenere tutti gli oggetti visivi critici (ad esempio dell'interfaccia utente o HUD). L'area all'esterno dell'area sicura non è garantito siano visibili nel tutti televisori, pertanto potrebbero essere parzialmente o completamente invisibili in alcune consente di visualizzare gli oggetti visivi inseriti in questa area.

Il modello MonoGame per Xbox One prende in considerazione l'area sicura e ne esegue il rendering come un bordo bianco. Quest'area viene inoltre riflessa in fase di esecuzione del gioco `Window.ClientBounds` proprietà come illustrato in questa immagine della finestra Espressioni di controllo in Visual Studio. Si noti che l'altezza dei limiti di client è 1016, nonostante risoluzione dello schermo di 1920 x 1080:

![](uwp-images/clientbounds.png "Si noti che l'altezza dei limiti di client è 1016, nonostante risoluzione dello schermo di 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Riferimento ai contenuti nei progetti UWP

Il contenuto nei progetti MonoGame è possibile fare riferimento direttamente dal file o tramite il [Pipeline di contenuti MonoGame](~/graphics-games/cocossharp/content-pipeline/index.md). Progetti di gioco di piccole dimensioni possono trarre vantaggio dalla semplicità di caricamento file. Progetti di grandi dimensioni trarranno vantaggio dall'uso della pipeline di contenuti per ottimizzare il contenuto per ridurre le dimensioni e tempi di caricamento. A differenza di XNA su Xbox 360, di `System.IO.File` classe è disponibile nelle App UWP per Xbox One.

Per altre informazioni sul caricamento di contenuti mediante la pipeline di contenuti, vedere la [Guida alla Pipeline di contenuti](~/graphics-games/cocossharp/content-pipeline/index.md).

### <a name="loading-content-from-file"></a>Caricamento del contenuto da File

A differenza di iOS e Android, i progetti UWP possono fare riferimento a file relative al file eseguibile. Semplici giochi possono usare questo contenuto carico tecnica senza dover modificare e compilare il progetto di pipeline di contenuti.

Per caricare un `Texture2D` dal file:

1. Aggiungere un file con estensione png per la cartella del contenuto nel progetto UWP. Aggiunta di contenuto nella cartella Content è una convenzione in XNA e MonoGame.
1. Fare clic sul file PNG appena aggiunto e scegliere Proprietà.
1. Modifica il **copia in Directory di Output** al **copia se più recente**.
1. Aggiungere il codice seguente al metodo di inizializzazione del gioco per caricare un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Per altre informazioni sull'uso di un `Texture2D`, vedere la [Introduzione a MonoGame Guida](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Riepilogo

Questa guida illustra come creare un nuovo progetto UWP e le considerazioni specifiche UWP durante il caricamento di file. Gli sviluppatori che desideri creare giochi UWP complete altre informazioni su MonoGame nel [Introduzione a MonoGame Guida](~/graphics-games/monogame/introduction/index.md).
