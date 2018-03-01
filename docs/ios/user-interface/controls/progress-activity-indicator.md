---
title: "Lo stato di avanzamento e indicatori di attività"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b91c0d7504b391630beded7a52e240a0d043152f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="progress-and-activity-indicators"></a>Lo stato di avanzamento e indicatori di attività

È probabile che l'app dovrà eseguire prolungata che esegue attività quali il caricamento o l'elaborazione dati e che questo ritardo può provocare un ritardo durante l'aggiornamento dell'interfaccia utente. Durante questo periodo è consigliabile usare sempre un indicatore di stato per rassicurare l'utente che il sistema è occupato, eseguire il lavoro. In questo modo il controllo utente che l'app funziona su richiesta, che non è in attesa per i relativi input e forniscono un mezzo che riporta in dettaglio esattamente il tempo di attesa hanno.

iOS fornisce due modi per fornire questo indicatore di stato di avanzamento nell'app: gli indicatori di attività (incluso uno specifico _rete_ indicatore di attività) e indicatori di stato.

## <a name="activity-indicator"></a>Indicatore di attività

Gli indicatori di attività devono essere visualizzati quando l'app è in esecuzione un processo lungo, ma non si conosce la lunghezza esatta del tempo che richiederà l'attività.

Apple ha i seguenti suggerimenti per l'utilizzo di indicatori di attività:

- **Se possibile, utilizzare invece le barre di stato di avanzamento** - perché un indicatore di attività non fornisce all'utente alcun feedback per il processo viene eseguito il tempo necessario, utilizzare sempre una barra di stato, se si conosce la lunghezza (ad esempio, il numero di byte da scaricare in un file).
- **Mantenere l'indicatore animata** -gli utenti correlati a un indicatore di attività fissa a un'app bloccata è sempre l'indicatore animato quando viene visualizzato.
- **Descrizione per l'attività in fase di elaborazione** -solo visualizzazione l'indicatore di attività da sola non è sufficiente, l'utente deve essere informato il processo in attesa su. Includere un'etichetta significativa (in genere una frase completa, single) che definisce in modo chiaro l'attività.

### <a name="implementing-an-activity-indicator"></a>Implementazione di un indicatore di attività

Un indicatore di attività viene implementato tramite il [ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/) classe per indicare che un `UIActivity` in atto.

### <a name="activity-indicators-and-storyboards"></a>Storyboard e indicatori di attività

Se si utilizza la finestra di progettazione iOS per creare l'interfaccia utente, l'indicatore di attività possono essere aggiunti al layout dalla casella degli strumenti. Dal riquadro proprietà consente di effettuare le seguenti proprietà:

![Proprietà riempimento](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gestione del comportamento di indicatore di attività

Utilizzare il `StartAnimating()` e `StopAnimating()` metodi per avviare e arrestare l'animazione di indicatore di attività.

Impostare il `HidesWhenStopped` proprietà `true` per rendere l'indicatore di attività scomparire dopo aver `StopAnimating()` è stato chiamato. È impostato su `true` per impostazione predefinita. In qualsiasi momento è possibile visualizzare se l'indicatore di attività è in esecuzione l'animazione di rotazione controllando il `IsAnimating` proprietà. 


### <a name="managing-activity-indicator-appearances"></a>La gestione di aspetti di indicatore di attività

Il `UIActivityIndicatorViewStyle` enumerazione può essere passata come parametro quando si crea l'indicatore di attività. Ciò consente di impostare lo stile di visualizzazione `Gray`, `White`, o `WhiteLarge`, ad esempio:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

È possibile sostituire il colore fornito da `UIActivityIndicatorViewStyle` impostando il `Color` proprietà.

## <a name="progress-bar"></a>ProgressBar

Un indicatore di stato viene visualizzata come una riga che viene compilato con colore per indicare lo stato e la lunghezza di un'attività richiede molto tempo. Indicatori di stato deve sempre essere utilizzati quando la lunghezza delle attività è noto o può essere calcolata.

Apple ha i seguenti suggerimenti per l'utilizzo di indicatori di stato:

- **Segnalare con precisione lo stato** -indicatori di stato deve essere sempre una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per rendere l'app disponibile.
- **Utilizzare per la durata Well-Defined** -indicatore di stato deve non solo mostra che è in esecuzione un'attività di lunga durata impone, ma offrono all'utente e l'indicazione della quantità di attività viene completata e una stima del tempo rimanente.

### <a name="implementing-an-progress-bar"></a>Implementazione di un indicatore di stato

Viene creato un indicatore di stato creando un [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>Indicatori di stato e gli storyboard

È anche possibile aggiungere un indicatore di stato per l'interfaccia utente quando si utilizza la finestra di progettazione iOS. Cercare **Visualizza lo stato di avanzamento** nel **della casella degli strumenti** e trascinarlo nella propria visualizzazione.

Nel riquadro proprietà consente di effettuare le seguenti proprietà:

![Proprietà riempimento](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>Gestione del comportamento di indicatore di stato

Lo stato di avanzamento della barra può essere impostata inizialmente tramite il `Progress` proprietà:

```csharp
ProgressBar.Progress = 0f;
```

Lo stato di avanzamento può essere modificata tramite il `SetProgress` e passando un valore booleano dichiarazione se si desidera che la modifica animata o non.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Per ulteriori informazioni sull'utilizzo della barra di stato di avanzamento, consultare il [Reporting Progress](https://developer.xamarin.com/recipes/cross-platform/networking/download_progress/#Reporting_Progress_in_iOS) recipe e [UICatalog tvOS esempio](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/).

### <a name="managing-progress-bar-appearance"></a>La gestione di aspetto indicatore di stato

Simile a un indicatore di attività, il `UIProgressViewStyle` enumerazione può essere passata come parametro quando si crea l'indicatore di stato.

L'avanzamento e traccia immagine e colore a tinta può essere modificata tramite le proprietà seguenti:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



