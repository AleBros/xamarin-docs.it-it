---
title: Creazione di un progetto MonoGame UWP
description: Monogame può essere usato per creare giochi e app per piattaforma UWP (Universal Windows Platform), destinando a più dispositivi con una codebase e un set di contenuti.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 367f44104b0d6049b8d65ab6e3b3f38c703cdb3f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198720"
---
# <a name="creating-a-monogame-uwp-project"></a>Creazione di un progetto MonoGame UWP

_Monogame può essere usato per creare giochi e app per piattaforma UWP (Universal Windows Platform), destinando a più dispositivi con una codebase e un set di contenuti._

Questa procedura dettagliata illustra la creazione di un progetto MonoGame piattaforma UWP (Universal Windows Platform) (UWP) e il caricamento del contenuto. Le app UWP possono essere eseguite su tutti i dispositivi Windows 10, tra cui desktop, tablet, Windows Phone e Xbox One.

Questa procedura dettagliata crea un progetto vuoto che visualizza uno sfondo *blu fiordaliso* (il colore di sfondo tradizionale delle app XNA).

## <a name="requirements"></a>Requisiti

Per lo sviluppo di app monogame UWP è necessario:

- Sistema operativo Windows 10
- Qualsiasi versione di Visual Studio 2017
- Strumenti di sviluppo per Windows 10
- Impostazione del dispositivo sulla modalità sviluppatore
- [Monogame 3.7.1 per Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) o versione successiva

Per ulteriori informazioni, vedere la [pagina relativa alla configurazione per lo sviluppo di UWP per Windows 10](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

È possibile sviluppare giochi Xbox One in una versione finale dell'hardware Xbox One. È necessario software aggiuntivo per il PC di sviluppo e per Xbox One. Per informazioni sulla configurazione di una Xbox One per lo sviluppo di giochi, vedere questa pagina sulla [configurazione di una Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Creazione di un modello vuoto

Una volta che tutte le risorse necessarie sono state installate e la modalità sviluppatore è stata abilitata nel computer Windows 10, è possibile creare un nuovo progetto MonoGame usando Visual Studio attenendosi alla procedura seguente:

1. Selezionare **file** > nuovoprogetto >  **...**
1. Selezionare la >  > categoria**Visual C#**  monogameperimodelli > installati:

    ![](uwp-images/image1.png "Categoria monogame")

1. Selezionare l'opzione **monogame Windows 10 Universal Project** :

    ![](uwp-images/image2.png "Selezionare l'opzione monogame Windows 10 Universal Project")

1. Immettere un nome per il nuovo progetto e fare clic su **OK**.
Se Visual Studio Visualizza errori dopo aver fatto clic su OK, verificare che gli strumenti di Windows 10 siano installati e che il dispositivo sia in modalità sviluppatore.

Una volta che Visual Studio ha completato la creazione del modello, è possibile eseguirlo per vedere il progetto vuoto in esecuzione:

![](uwp-images/image3.png "Dopo che Visual Studio ha completato la creazione del modello, eseguirlo per visualizzare il progetto vuoto in esecuzione")

I numeri negli angoli forniscono informazioni di diagnostica. Queste informazioni possono essere rimosse eliminando il codice `App.xaml.cs` in `DEBUG` nel blocco nel `OnLaunched` metodo:


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

## <a name="running-on-xbox-one"></a>Esecuzione su Xbox One

I progetti UWP possono essere distribuiti in qualsiasi dispositivo Windows 10 dallo stesso progetto. Dopo aver configurato il computer di sviluppo Windows 10 e la Xbox One, le app UWP possono essere distribuite spostando la destinazione nel computer remoto e immettendo l'indirizzo IP di Xbox One:

![](uwp-images/remote.png "Le app UWP possono essere distribuite cambiando la destinazione nel computer remoto e immettendo l'indirizzo IP di Xbox")

In Xbox One il bordo bianco rappresenta l'area non sicura per TV. Per ulteriori informazioni, vedere la [sezione safe area](#safe-area-on-xbox-one).

![](uwp-images/safearea.png "In Xbox One il bordo bianco rappresenta l'area non sicura per TV")

### <a name="safe-area-on-xbox-one"></a>Area sicura su Xbox One

Per lo sviluppo di giochi per le console è necessario considerare l'area sicura, ovvero un'area al centro dello schermo che dovrebbe contenere tutti gli oggetti visivi critici, ad esempio interfaccia utente o HUD. Non è garantito che l'area esterna all'area sicura sia visibile in tutti i televisori, quindi gli oggetti visivi posizionati in quest'area possono essere parzialmente o completamente invisibili in alcune visualizzazioni.

Il modello monogame per Xbox One considera l'area sicura e ne esegue il rendering come bordo bianco. Anche quest'area viene riflessa in fase di `Window.ClientBounds` esecuzione nella proprietà del gioco, come illustrato in questa immagine della finestra espressioni di controllo in Visual Studio. Si noti che l'altezza dei limiti client è 1016, nonostante la risoluzione dello schermo 1920x1080:

![](uwp-images/clientbounds.png "Si noti che l'altezza dei limiti client è 1016, nonostante la risoluzione dello schermo 1920x1080")

## <a name="referencing-content-in-uwp-projects"></a>Riferimento al contenuto nei progetti UWP

È possibile fare riferimento al contenuto nei progetti monogame direttamente da file o tramite la [pipeline di contenuti monogame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md). I progetti di piccoli giochi possono trarre vantaggio dalla semplicità di caricamento da file. I progetti di dimensioni maggiori trarranno vantaggio dall'uso della pipeline di contenuti per ottimizzare il contenuto per ridurre le dimensioni e i tempi di caricamento. A differenza di XNA in Xbox 360, la `System.IO.File` classe è disponibile nelle app UWP per Xbox One.

Per ulteriori informazioni sul caricamento di contenuto tramite la pipeline di contenuti, vedere la [Guida alla pipeline di contenuti](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

### <a name="loading-content-from-file"></a>Caricamento del contenuto da un file

A differenza di iOS e Android, i progetti UWP possono fare riferimento a file relativi all'eseguibile. I giochi semplici possono usare questa tecnica per caricare il contenuto senza dover modificare e compilare il progetto della pipeline di contenuti.

Per caricare un `Texture2D` file da un file:

1. Aggiungere un file con estensione png alla cartella Content nel progetto UWP. L'aggiunta di contenuto alla cartella contenuto è una convenzione in XNA e monogame.
1. Fare clic con il pulsante destro del mouse sul PNG appena aggiunto e scegliere Proprietà.
1. Modificare la **copia nella directory di output** in **copia se più recente**.
1. Aggiungere il codice seguente al metodo Initialize del gioco per caricare un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Per ulteriori informazioni sull'utilizzo di `Texture2D`un, vedere la [Guida introduttiva a monogame](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Riepilogo

Questa guida illustra come creare un nuovo progetto UWP e considerazioni specifiche di UWP durante il caricamento dei file. Gli sviluppatori interessati a creare giochi UWP completi possono leggere altre informazioni su monogame nella [Guida introduttiva a monogame](~/graphics-games/monogame/introduction/index.md).
