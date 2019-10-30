---
title: Indicatori di stato e attività in Novell. iOS
description: Questo documento illustra come usare gli indicatori di stato e attività in Novell. iOS. Viene descritto come utilizzarli sia a livello di codice che con uno storyboard.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 76e1ee54a5e1b729fdcb0b0a2c1f278703b2b4d6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021966"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Indicatori di stato e attività in Novell. iOS

È probabile che l'app debba eseguire attività a esecuzione prolungata, ad esempio il caricamento o l'elaborazione dei dati e che questo ritardo può causare un ritardo nell'aggiornamento dell'interfaccia utente. Durante questo intervallo di tempo è necessario usare sempre un indicatore di stato per rassicurare l'utente che il sistema è occupato a lavorare. In questo modo si fornisce al controllo utente che l'app sta lavorando alla richiesta, che non è in attesa dell'input e che può fornire un modo per definirne i dettagli esattamente quanto tempo è necessario attendere.

iOS offre due modi principali per fornire questa indicazione di stato nell'app: gli indicatori di attività (incluso un indicatore di attività di _rete_ specifico) e le barre di stato.

## <a name="activity-indicator"></a>Indicatore attività

Gli indicatori di attività devono essere visualizzati quando l'app esegue un processo lungo, ma non si conosce l'esatto periodo di tempo necessario per l'attività.

Apple presenta i suggerimenti seguenti per l'utilizzo degli indicatori di attività:

- **Quando possibile, usare invece le barre di stato** , perché un indicatore di attività fornisce all'utente nessun feedback per quanto tempo verrà eseguito dal processo, usare sempre un indicatore di stato se la lunghezza è nota (ad esempio, il numero di byte da scaricare in un file).
- **Tenere l'indicatore animato** -gli utenti correlano un indicatore di attività stazionari a un'app bloccata, quindi è necessario che l'indicatore venga sempre animato mentre viene visualizzato.
- **Descrivere l'attività in fase di elaborazione** . la semplice visualizzazione dell'indicatore di attività non è sufficiente, l'utente deve essere informato del processo in attesa. Includere un'etichetta significativa (in genere una singola frase completa) che definisce chiaramente l'attività.

### <a name="implementing-an-activity-indicator"></a>Implementazione di un indicatore di attività

Un indicatore di attività viene implementato tramite la classe [`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView) per indicare che viene eseguita una `UIActivity`.

### <a name="activity-indicators-and-storyboards"></a>Indicatori di attività e storyboard

Se si usa la finestra di progettazione iOS per creare l'interfaccia utente, è possibile aggiungere l'indicatore di attività al layout dalla casella degli strumenti. Le proprietà seguenti possono essere modificate dal riquadro delle proprietà:

![riquadro delle proprietà](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gestione del comportamento dell'indicatore di attività

Usare i metodi `StartAnimating()` e `StopAnimating()` per avviare e arrestare l'animazione dell'indicatore di attività.

Impostare la proprietà `HidesWhenStopped` su `true` per far scomparire l'indicatore dell'attività dopo la chiamata di `StopAnimating()`. Questa impostazione è impostata su `true` per impostazione predefinita. In qualsiasi momento è possibile verificare se l'indicatore di attività sta eseguendo l'animazione rotante controllando la proprietà `IsAnimating`. 

### <a name="managing-activity-indicator-appearances"></a>Gestione dell'aspetto degli indicatori di attività

L'enumerazione `UIActivityIndicatorViewStyle` può essere passata come parametro quando si crea un'istanza dell'indicatore di attività. È possibile usare questa impostazione per impostare lo stile di visualizzazione su `Gray`, `White`o `WhiteLarge`, ad esempio:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

È possibile eseguire l'override del colore fornito da `UIActivityIndicatorViewStyle` impostando la proprietà `Color`.

## <a name="progress-bar"></a>ProgressBar

Un indicatore di stato presenta una linea che riempie il colore per indicare lo stato e la lunghezza di un'attività che richiede molto tempo. Gli indicatori di stato devono essere sempre utilizzati quando la lunghezza delle attività è noto o può essere calcolata.

Apple presenta i suggerimenti seguenti per l'utilizzo degli indicatori di stato:

- **Segnala accuratamente lo stato** di avanzamento: le barre di avanzamento devono essere sempre una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per far sembrare l'app occupata.
- **Usare per le durate ben definite** : l'indicatore di stato non solo indica che è in corso un'attività lunga, ma è possibile fornire all'utente e indicare la quantità di attività completata e una stima del tempo rimanente.

### <a name="implementing-an-progress-bar"></a>Implementazione di un indicatore di stato

Un indicatore di stato viene creato creando un'istanza di un [`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>Barre di stato e storyboard

È anche possibile aggiungere un indicatore di stato all'interfaccia utente quando si usa iOS designer. Cercare **visualizzazione stato** nella **casella degli strumenti** e trascinarlo nella visualizzazione.

Nel riquadro delle proprietà è possibile modificare le proprietà seguenti:

![riquadro delle proprietà](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>Gestione del comportamento dell'indicatore di stato

Lo stato di avanzamento della barra può essere impostato inizialmente usando la proprietà `Progress`:

```csharp
ProgressBar.Progress = 0f;
```

Lo stato di avanzamento può essere regolato utilizzando il metodo `SetProgress` e passando un valore booleano che dichiara se si desidera che la modifica venga animata o meno.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Per ulteriori informazioni sull'utilizzo dell'indicatore di stato, fare riferimento alla ricetta dello [stato di avanzamento del report](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) e all' [esempio UICatalog tvOS](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-uicatalog).

### <a name="managing-progress-bar-appearance"></a>Gestione dell'aspetto della barra di stato

Analogamente a un indicatore di attività, l'enumerazione `UIProgressViewStyle` può essere passata come parametro quando si crea un'istanza della barra di stato.

È possibile modificare l'immagine di avanzamento e di traccia e il colore della tinta usando le proprietà seguenti:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```
