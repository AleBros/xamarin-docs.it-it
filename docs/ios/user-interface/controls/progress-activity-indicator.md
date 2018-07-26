---
title: Lo stato di avanzamento e gli indicatori di attività in xamarin. IOS
description: Questo documento illustra come usare gli indicatori di stato e attività in xamarin. IOS. Viene descritto come usarli sia a livello di codice e con uno storyboard.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 24ad47bd37693eccf38033159acef72a9d4942be
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242179"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Lo stato di avanzamento e gli indicatori di attività in xamarin. IOS

È probabile che l'app dovrà svolgere prolungata che eseguono attività quali il caricamento o l'elaborazione dati e che questo ritardo può comportare un ritardo nell'aggiornamento dell'interfaccia utente. Durante questo periodo è consigliabile usare sempre un indicatore di stato per rassicurare l'utente che il sistema è occupato in questo lavoro. In questo modo il controllo utente che l'app sta lavorando per la richiesta, che non è in attesa per l'input, e può fornire un mezzo che riporta in dettaglio esattamente quanto tempo dovranno attendere.

iOS offre due modi principali per fornire questa indicazione dell'avanzamento nell'app: gli indicatori di attività (tra cui una determinata _rete_ indicatore dell'attività) e le barre di avanzamento.

## <a name="activity-indicator"></a>Indicatore dell'attività

Gli indicatori di attività devono essere visualizzati quando l'app è in esecuzione un processo lungo, ma non si conosce l'esatto periodo di tempo che richiederà l'attività.

Apple ha i seguenti suggerimenti per lavorare con gli indicatori di attività:

- **Se possibile, usare invece le barre di stato di avanzamento** - perché un indicatore dell'attività che consente all'utente alcun feedback sulla durata del processo eseguito richiederà, usare sempre un indicatore di stato se la lunghezza è noto (ad esempio, il numero di byte per il download in un file).
- **Mantenere l'indicatore animata** -utenti correlati un indicatore dell'attività di fermo a un'app bloccata in modo da avere sempre l'indicatore animato quando viene visualizzato.
- **Descrivere l'attività in fase di elaborazione** -solo visualizzazione l'indicatore dell'attività di per sé non è sufficiente, l'utente deve essere informato il processo di cui sono in attesa. Includere un'etichetta significativa (in genere una frase unica e completa) che definisca chiaramente l'attività.

### <a name="implementing-an-activity-indicator"></a>Implementazione di un indicatore dell'attività

Un indicatore dell'attività viene implementato tramite il [ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/) classe per indicare che un `UIActivity` sia in esecuzione.

### <a name="activity-indicators-and-storyboards"></a>Storyboard e gli indicatori di attività

Se si usa iOS Designer per creare l'interfaccia utente, l'indicatore di attività possono essere aggiunti al layout dalla casella degli strumenti. Le proprietà seguenti possono essere modificate dal riquadro delle proprietà:

![Riquadro delle proprietà](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gestione del comportamento di indicatore di attività

Usare la `StartAnimating()` e `StopAnimating()` metodi per avviare e arrestare l'animazione di indicatore di attività.

Impostare il `HidesWhenStopped` proprietà `true` per rendere l'indicatore di attività scomparire dopo aver `StopAnimating()` è stato chiamato. È impostato su `true` per impostazione predefinita. In qualsiasi momento è possibile visualizzare se l'indicatore di attività è in esecuzione l'animazione di rotazione controllando il `IsAnimating` proprietà. 


### <a name="managing-activity-indicator-appearances"></a>Gestire gli aspetti di indicatore di attività

Il `UIActivityIndicatorViewStyle` enumerazione può essere passato come parametro quando si crea l'indicatore di attività. È possibile usare ciò su cui per impostare lo stile di visualizzazione `Gray`, `White`, o `WhiteLarge`, ad esempio:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

È possibile ignorare il colore disql `UIActivityIndicatorViewStyle` impostando la `Color` proprietà.

## <a name="progress-bar"></a>ProgressBar

Un indicatore di stato viene presentato come una riga che viene riempito con il colore per indicare lo stato e lunghezza di un'attività che richiedono molto tempo. Le barre di avanzamento deve sempre essere utilizzate quando la lunghezza delle attività è sa o può essere calcolata.

Apple ha i seguenti suggerimenti per l'uso di indicatori di stato:

- **Segnalare con precisione lo stato** -indicatori di stato deve essere sempre una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per rendere l'app disponibile.
- **Uso di durate Well-Defined** -indicatore di stato deve non solo mostra che è in corso un'attività di lunga durata posizionare, ma offrono all'utente e indicazione della quantità di attività viene completata e una stima del tempo rimanente.

### <a name="implementing-an-progress-bar"></a>Implementazione di un indicatore di stato

Un indicatore di stato viene creato creando un [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>Indicatori di stato e gli storyboard

È anche possibile aggiungere un indicatore di stato per l'interfaccia utente quando si usa iOS Designer. Cercare **Visualizza lo stato di avanzamento** nel **della casella degli strumenti** e trascinarla per la visualizzazione.

Il riquadro delle proprietà è possibile regolare le proprietà seguenti:

![Riquadro delle proprietà](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>Gestione del comportamento di indicatore di stato

Lo stato di avanzamento della barra può essere impostata inizialmente tramite il `Progress` proprietà:

```csharp
ProgressBar.Progress = 0f;
```

Lo stato di avanzamento può essere modificata tramite il `SetProgress` metodo e passando un valore booleano dichiarare se si desidera che la modifica animata o No.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Per altre informazioni sull'utilizzo della barra di stato di avanzamento, vedere la [lo stato di avanzamento Reporting](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) ricetta e il [UICatalog tvOS esempio](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/).

### <a name="managing-progress-bar-appearance"></a>La gestione di aspetto della barra di stato di avanzamento

Simile a un indicatore dell'attività, il `UIProgressViewStyle` enumerazione può essere passato come parametro quando si crea l'indicatore di stato.

L'avanzamento e Track immagine e colore tinta può essere modificata tramite le proprietà seguenti:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



