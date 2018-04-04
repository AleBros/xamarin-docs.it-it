---
title: Creazione di un progetto UWP MonoGame
description: MonoGame utilizzabile per creare giochi e App per la piattaforma Windows universale, destinato a che più dispositivi con una codebase e un set di contenuto.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: efee4847397db0e89a8d10211e13d61ce13824fb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-monogame-uwp-project"></a>Creazione di un progetto UWP MonoGame

_MonoGame utilizzabile per creare giochi e App per la piattaforma Windows universale, destinato a che più dispositivi con una codebase e un set di contenuto._

Questa procedura dettagliata vengono illustrate la creazione del progetto MonoGame Universal Windows Platform (UWP) e il caricamento di contenuto. App UWP possono essere eseguite su tutti i dispositivi Windows 10, tra cui desktop, Tablet, Windows Phone e Xbox One.

Questa procedura dettagliata crea un progetto vuoto che consente di visualizzare un *Blu fiordaliso* sfondo (il colore di sfondo tradizionali di XNA App).


# <a name="requirements"></a>Requisiti

Lo sviluppo di App UWP MonoGame richiede:

 - Sistema operativo Windows 10
 - Qualsiasi versione di Visual Studio 2015
 - Strumenti di sviluppo di Windows 10
 - Dispositivo di impostazione per la modalità sviluppatore
- [3.5 MonoGame per Visual Studio](http://www.monogame.net/2016/03/17/monogame-3-5/) o versione successiva

Per ulteriori informazioni, vedere questo [pagina su come configurare per lo sviluppo UWP Windows 10](https://msdn.microsoft.com/en-us/windows/uwp/get-started/get-set-up).

È possibile sviluppare giochi Xbox One hardware Xbox One delle vendite al dettaglio. Software aggiuntivo è necessaria per il PC di sviluppo e Xbox One. Per informazioni sulla configurazione di un ambito per lo sviluppo di gioco Xbox One, vedere questa pagina [impostazione un Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/index).


# <a name="creating-an-empty-template"></a>Creazione di un modello vuoto

Una volta tutte le risorse necessarie sono state installate e la modalità sviluppatore è stata abilitata nel computer Windows 10, è possibile creare un nuovo progetto MonoGame utilizzando Visual Studio eseguendo la procedura seguente:

1. Selezionare **File** > **nuova** > **progetto...**
1. Selezionare il **installato** > **modelli** > **Visual c#** > **MonoGame** categoria: 

    ![](uwp-images/image1.png "Categoria MonoGame")

1. Selezionare il **MonoGame Windows 10 Universal progetto** opzione: 

    ![](uwp-images/image2.png "Selezionare l'opzione MonoGame Windows 10 Universal progetto")

1. Immettere un nome per il nuovo progetto e fare clic su **OK**.
Se Visual Studio vengono visualizzati eventuali errori fare clic su OK, verificare che siano installati gli strumenti di Windows 10 e che il dispositivo è in modalità sviluppatore. 

Una volta terminato Visual Studio la creazione del modello, è possibile eseguirla per visualizzare il progetto vuoto in esecuzione:

![](uwp-images/image3.png "Una volta terminato Visual Studio la creazione del modello, eseguire il codice per il progetto vuoto in esecuzione")

I numeri negli angoli forniscono informazioni diagnostiche. Queste informazioni possono essere rimossi eliminando il codice in `App.xaml.cs` nel `DEBUG` blocco il `OnLaunched` metodo:


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

# <a name="running-on-xbox-one"></a>In esecuzione su Xbox uno

Progetti UWP è possono distribuire in qualsiasi dispositivo Windows 10 da nello stesso progetto. Dopo aver configurato il computer di sviluppo per Windows 10 e Xbox One, è possibile distribuire le app UWP passando la destinazione al computer remoto e immettere l'indirizzo IP di Xbox One:

![](uwp-images/remote.png "App UWP possono essere distribuite passando la destinazione al computer remoto e immettere l'indirizzo IP di quelli Xbox")

In Xbox One bordo bianco rappresenta l'area non-safe per TV. Per ulteriori informazioni, vedere il [sezione area di sicurezza](#Safe_Area_on_Xbox_One).

![](uwp-images/safearea.png "In Xbox One bordo bianco rappresenta l'area di non-safe per TV")

## <a name="safe-area-on-xbox-one"></a>Area di sicurezza su Xbox uno

Lo sviluppo di giochi per console, è necessario prendere in considerazione l'area di sicurezza, è un'area al centro dello schermo che deve contenere tutti gli elementi visivi critici (ad esempio, l'interfaccia utente o HUD). L'area di fuori di area di sicurezza non deve necessariamente essere visibile nelle tutti televisori, pertanto gli oggetti visivi inseriti in questa area potrebbero essere parzialmente o completamente invisibili in alcune visualizzazioni.

Il modello MonoGame per Xbox One considera l'area di sicurezza e ne esegue il rendering come un bordo nero. Quest'area viene riflessa anche in fase di esecuzione del gioco `Window.ClientBounds` proprietà come illustrato nell'immagine della finestra Espressioni di controllo in Visual Studio. Si noti che l'altezza dei limiti client 1016, nonostante risoluzione dello schermo diverse da 1920x1080:

![](uwp-images/clientbounds.png "Si noti che l'altezza dei limiti client 1016, nonostante risoluzione dello schermo diverse da 1920x1080")


# <a name="referencing-content-in-uwp-projects"></a>Riferimento ai contenuti nei progetti UWP

È possibile fare riferimento contenuto nei progetti MonoGame direttamente dal file o tramite il [MonoGame contenuto Pipeline](~/graphics-games/cocossharp/content-pipeline/index.md). Progetti di giochi di piccole dimensioni può risultare vantaggioso la semplicità di caricamento dal file. Progetti di grandi dimensioni sfrutterà tramite la pipeline di contenuto per ottimizzare il contenuto per ridurre le dimensioni e tempi di caricamento. A differenza di XNA su Xbox 360, la `System.IO.File` classe è disponibile per le app UWP una Xbox.

Per ulteriori informazioni sul caricamento di contenuto tramite la pipeline di contenuto, vedere il [contenuto Pipeline Guida](~/graphics-games/cocossharp/content-pipeline/index.md). 


## <a name="loading-content-from-file"></a>Il caricamento del contenuto dal File

A differenza di iOS e Android, progetti UWP possono fare riferimento a file dell'eseguibile. Semplici giochi possono usare questo contenuto carico tecnica senza dover modificare e compilare il progetto di pipeline contenuto.

Per caricare un `Texture2D` dal file:

1. Aggiungere un file con estensione png in questa cartella nel progetto UWP. Aggiunta del contenuto in questa cartella è una convenzione XNA e MonoGame.
1. Fare clic sul file PNG appena aggiunto e selezionare proprietà.
1. Modifica il **copia nella Directory di Output di** a **copia se più recente**.
1. Aggiungere il codice seguente al metodo di inizializzazione del gioco per caricare un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Per ulteriori informazioni sull'utilizzo di un `Texture2D`, vedere il [Introduzione alla Guida MonoGame](~/graphics-games/monogame/introduction/index.md).


# <a name="summary"></a>Riepilogo

Questa guida illustra come creare un nuovo progetto UWP e considerazioni specifiche UWP durante il caricamento di file. Gli sviluppatori che desiderano creare giochi UWP completi altre informazioni su MonoGame nel [Introduzione alla Guida MonoGame](~/graphics-games/monogame/introduction/index.md).