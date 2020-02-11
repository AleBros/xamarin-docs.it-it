---
title: Uso degli indicatori di stato tvOS in Xamarin
description: Questo documento descrive come usare gli indicatori di stato in un'app tvOS compilata con Xamarin. Vengono illustrati gli indicatori di avanzamento e di attività.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 9d346dc52437b5c079d499e142036e94cfaef65d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030432"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Uso degli indicatori di stato tvOS in Xamarin

_Questo articolo illustra la progettazione e l'uso degli indicatori di stato all'interno di un'app Xamarin.tvOS._

In alcuni casi l'app Xamarin.tvOS deve caricare nuovo contenuto o eseguire un'operazione di elaborazione lunga. In questi casi, è necessario presentare un indicatore di attività o un indicatore di stato per informare l'utente che l'app è ancora in esecuzione e per dare loro un'indicazione relativa alla lunghezza dell'attività in esecuzione.

![Indicatori di stato di esempio](progress-indicators-images/intro01.png "Indicatori di stato di esempio")

## <a name="about-activity-indicators"></a>Informazioni sugli indicatori di attività

Un indicatore di attività presenta un ingranaggio rotante e viene utilizzato per rappresentare un'attività di lunghezza indeterminata. L'indicatore viene presentato quando l'attività viene avviata e scompare al termine dell'attività.

Apple presenta i suggerimenti seguenti per l'utilizzo degli indicatori di attività:

- **Quando possibile, usare invece le barre di stato** , perché un indicatore di attività fornisce all'utente nessun feedback per quanto tempo verrà eseguito dal processo, usare sempre un indicatore di stato se la lunghezza è nota (ad esempio, il numero di byte da scaricare in un file).
- **Tenere l'indicatore animato** : gli utenti mettono in relazione un indicatore di attività stazionaria a un'app bloccata, quindi è sempre necessario animare l'indicatore mentre viene visualizzato.
- **Descrivere l'attività in fase di elaborazione** . la semplice visualizzazione dell'indicatore di attività non è sufficiente. l'utente deve essere informato del processo su cui è in attesa. Includere un'etichetta significativa (in genere una singola frase completa) che definisce chiaramente l'attività.

## <a name="about-progress-bars"></a>Informazioni sulle barre di stato

Un indicatore di stato presenta una linea che riempie il colore per indicare lo stato e la lunghezza di un'attività che richiede molto tempo. Gli indicatori di stato devono essere sempre utilizzati quando la lunghezza delle attività è nota o può essere calcolata.

Apple presenta i suggerimenti seguenti per l'utilizzo degli indicatori di stato:

- **Segnala in modo accurato lo stato** di avanzamento: le barre di avanzamento devono sempre presentare una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per far sembrare l'app occupata.
- **Usare per le durate ben definite** : gli indicatori di stato non solo indicano che è in corso un'attività lunga, ma forniscono all'utente e l'indicazione della quantità di attività completata e una stima del tempo rimanente.

## <a name="progress-indicators-and-storyboards"></a>Indicatori di stato e storyboard

Il modo più semplice per usare un indicatore di stato in un'app Xamarin.tvOS consiste nell'aggiungerlo all'interfaccia utente dell'app tramite iOS designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul file **Main. Storyboard** e aprirlo per la modifica.

2. Trascinare un **indicatore di attività** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione: 

    ![Indicatore di attività](progress-indicators-images/activity01.png "Indicatore di attività")

3. Nella scheda **widget** del **riquadro delle proprietà**è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio **lo stile**, il **comportamento**e il **nome**: 

    ![Scheda widget per un indicatore di attività](progress-indicators-images/activity02.png "Scheda widget per un indicatore di attività")
    
    Il **nome** determina il nome della proprietà che rappresenta l'indicatore di attività nel C# codice.

4. Trascinare una **visualizzazione stato** dalla **casella degli strumenti** e rilasciarla nella visualizzazione: 

    ![Visualizzazione stato](progress-indicators-images/activity03.png "Visualizzazione stato")

5. Nella scheda **widget** di **Esplora proprietà**è possibile modificare diverse proprietà della visualizzazione stato, ad esempio **lo stile, lo**stato di **avanzamento** (percentuale di completamento) e il **nome**: 

    ![Scheda widget per una visualizzazione dello stato di avanzamento](progress-indicators-images/activity04.png "Scheda widget per una visualizzazione dello stato di avanzamento")
    
    Il **nome** determina il nome della proprietà che rappresenta la visualizzazione dello stato di C# avanzamento nel codice.

6. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare doppio clic sul file **Main. Storyboard** e aprirlo per la modifica.

2. Trascinare un **indicatore di attività** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione: 

    ![Indicatore di attività](progress-indicators-images/activity01-vs.png
    "Indicatore di attività")

3. Nella scheda **widget** di **Esplora proprietà**è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio **lo stile**, il **comportamento**e il **nome**: 

    ![Scheda widget per un indicatore di attività](progress-indicators-images/activity02-vs.png "Scheda widget per un indicatore di attività")

    Il **nome** determina il nome della proprietà che rappresenta l'indicatore di attività nel C# codice.

4. Trascinare una **visualizzazione stato** dalla **casella degli strumenti** e rilasciarla nella visualizzazione: 

   ![Visualizzazione stato](progress-indicators-images/activity03-vs.png "Visualizzazione stato")

5. Nella scheda **widget** di **Esplora proprietà**è possibile modificare diverse proprietà della visualizzazione stato, ad esempio **lo stile, lo**stato di **avanzamento** (percentuale di completamento) e il **nome**: 

    ![Scheda widget per una visualizzazione dello stato di avanzamento](progress-indicators-images/activity04-vs.png "Scheda widget per una visualizzazione dello stato di avanzamento")
    
    Il **nome** determina il nome della proprietà che rappresenta la visualizzazione dello stato di C# avanzamento nel codice.

6. Salvare le modifiche.

-----

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Utilizzo degli indicatori di attività

Come indicato in precedenza, gli indicatori di attività devono essere visualizzati quando l'app esegue un lungo processo di lunghezza indeterminata.

In qualsiasi momento, è possibile verificare se un indicatore di attività sta aggiungendo un'animazione controllando la relativa proprietà `IsAnimating`. Se la proprietà `HidesWhenStopped` è `true`, l'indicatore di attività verrà nascosto automaticamente quando viene arrestata l'animazione.

Per avviare l'animazione, è possibile usare il codice seguente: 

```csharp
ActivityIndicator.StartAnimating();
```

L'animazione viene arrestata da quanto segue:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Questi frammenti di codice presuppongono che il **nome** dell'indicatore di attività sia stato impostato su **ActivityIndicator** nella scheda **widget** di iOS designer.

## <a name="working-with-progress-bars"></a>Utilizzo degli indicatori di stato

Anche in questo caso, è consigliabile usare un indicatore di stato ogni volta che l'applicazione esegue un'attività a esecuzione prolungata di una durata nota. 

La proprietà `Progress` viene utilizzata per impostare la quantità di attività completata da 0% a 100% (da 0,0 a 1,0). Utilizzare la proprietà `ProgressTintColor` per impostare il colore della barra di completamento della quantità e la proprietà `TrackTintColor` per impostare il colore di sfondo (importo non completato).

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata la progettazione e l'utilizzo degli indicatori di stato all'interno di un'app Xamarin.tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
