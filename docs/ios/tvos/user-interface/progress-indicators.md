---
title: Utilizzo di indicatori di stato di avanzamento
description: Questo articolo descrive la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c021550e17cf8206d59102856a11c72000ad06aa
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-progress-indicators"></a>Utilizzo di indicatori di stato di avanzamento

_Questo articolo descrive la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app Xamarin.tvOS._


Talvolta potrebbe essere quando l'app Xamarin.tvOS deve caricare nuovo contenuto o eseguire un'operazione di lunga durata di elaborazione. Durante tali periodi, si deve presentare un indicatore di attività o indicatore di stato per informare l'utente che l'app è ancora in esecuzione e per consentire loro alcune indicazioni in merito alla durata dell'attività in esecuzione.

[![](progress-indicators-images/intro01.png "Indicatori di stato di esempio")](progress-indicators-images/intro01.png#lightbox)

<a name="About-Activity-Indicators" />

## <a name="about-activity-indicators"></a>Informazioni sugli indicatori di attività

Un indicatore di attività viene presentato come una ruota dentata rotante visivamente e viene utilizzato per rappresentare un'attività di una determinata lunghezza. L'indicatore viene visualizzato quando l'attività viene avviata e scompare quando l'attività è stata completata.

Apple ha i seguenti suggerimenti per l'utilizzo di indicatori di attività:

- **Se possibile, utilizzare invece le barre di stato di avanzamento** - perché un indicatore di attività non fornisce all'utente alcun feedback per il processo viene eseguito il tempo necessario, utilizzare sempre una barra di stato, se si conosce la lunghezza (ad esempio, il numero di byte da scaricare in un file).
- **Mantenere l'indicatore animata** -gli utenti correlati a un indicatore di attività fissa a un'app bloccata è sempre l'indicatore animato quando viene visualizzato.
- **Descrizione per l'attività in fase di elaborazione** -solo visualizzazione l'indicatore di attività da sola non è sufficiente, l'utente deve essere informato il processo in attesa su. Includere un'etichetta significativa (in genere una frase completa, single) che definisce in modo chiaro l'attività.

<a name="Summary" />

## <a name="about-progress-bars"></a>Informazioni sulle barre di stato di avanzamento

Un indicatore di stato viene visualizzata come una riga che viene compilato con colore per indicare lo stato e la lunghezza di un'attività richiede molto tempo. Indicatori di stato deve sempre essere utilizzati quando la lunghezza delle attività è noto o può essere calcolata.

Apple ha i seguenti suggerimenti per l'utilizzo di indicatori di stato:

- **Segnalare con precisione lo stato** -indicatori di stato deve essere sempre una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per rendere l'app disponibile.
- **Utilizzare per la durata Well-Defined** -indicatore di stato deve non solo mostra che è in esecuzione un'attività di lunga durata impone, ma offrono all'utente e l'indicazione della quantità di attività viene completata e una stima del tempo rimanente.

<a name="Progress-Indicators-and-Storyboards" />

## <a name="progress-indicators-and-storyboards"></a>Storyboard e gli indicatori di stato

Il modo più semplice per lavorare con indicatore di stato in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **indicatore di attività** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](progress-indicators-images/activity01.png "Un indicatore di attività")](progress-indicators-images/activity01.png#lightbox)
1. Nel **scheda Widget** del **proprietà riempimento**, è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio il **stile** e **comportamento**: 

    [![](progress-indicators-images/activity02.png "La scheda Widget ")](progress-indicators-images/activity02.png#lightbox)
1. Trascinare un **Visualizza lo stato di avanzamento** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](progress-indicators-images/activity03.png "Una vista stato")](progress-indicators-images/activity03.png#lightbox)
1. Nel **scheda Widget** del **Esplora proprietà**, è possibile modificare diverse proprietà della vista di stato di avanzamento, ad esempio il **stile** e **corso**(percentuale di completamento): 

    [![](progress-indicators-images/activity04.png "La scheda Widget")](progress-indicators-images/activity04.png#lightbox)
1. Infine, assegnare **nomi** ai controlli in modo che è possibile rispondere ad essi nel codice c#. Ad esempio: 

    [![](progress-indicators-images/activity05.png "Assegnare un nome")](progress-indicators-images/activity05.png#lightbox)
1. Salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **indicatore di attività** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](progress-indicators-images/activity01-vs.png "Un indicatore di attività")](progress-indicators-images/activity01-vs.png#lightbox)
1. Nel **scheda Widget** del **Esplora proprietà**, è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio il **stile** e **comportamento**: 

    [![](progress-indicators-images/activity02-vs.png "La scheda Widget")](progress-indicators-images/activity02-vs.png#lightbox)
1. Trascinare un **Visualizza lo stato di avanzamento** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](progress-indicators-images/activity03-vs.png "Una vista stato")](progress-indicators-images/activity03-vs.png#lightbox)
1. Nel **scheda Widget** del **Esplora proprietà**, è possibile modificare diverse proprietà della vista di stato di avanzamento, ad esempio il **stile** e **corso**(percentuale di completamento): 

    [![](progress-indicators-images/activity04-vs.png "La scheda Widget")](progress-indicators-images/activity04-vs.png#lightbox)
1. Infine, assegnare **nomi** ai controlli in modo che è possibile rispondere ad essi nel codice c#. Ad esempio: 

    [![](progress-indicators-images/activity05-vs.png "Assegnare un nome")](progress-indicators-images/activity05-vs.png#lightbox)
1. Salvare le modifiche.

-----

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Activity-Indicators" />

## <a name="working-with-activity-indicators"></a>Utilizzo di indicatori di attività

Come descritto in precedenza, gli indicatori di attività deve essere visualizzati quando l'app è in esecuzione un processo lungo, ma non si conosce la lunghezza esatta del tempo che richiederà l'attività.

In qualsiasi momento è possibile visualizzare se l'indicatore di attività è in esecuzione l'animazione di rotazione controllando il `IsAnimating` proprietà. Se il `HidesWhenStopped` proprietà `true`, verrà automaticamente nascosto l'indicatore di attività quando viene arrestato l'animazione.

Per avviare l'animazione, è possibile utilizzare il codice seguente: 

```csharp
ActivityIndicator.StartAnimating();
```

E l'animazione verrà arrestare le operazioni seguenti:

```csharp
ActivityIndicator.StopAnimating();
```

<a name="Working-with-Progress-Bars" />

## <a name="working-with-progress-bars"></a>Utilizzo di indicatori di stato

Nuovamente, un indicatore di stato deve essere utilizzato ogni volta che l'app è in esecuzione un'attività a esecuzione prolungata di una durata noti. 

Il `Progress` proprietà viene utilizzata per impostare la quantità di attività che è stata completata da 0% e 100% (da 0,0 a 1,0). Utilizzare il `ProgressTintColor` proprietà per impostare il colore della barra di quantità completata e `TrackTintColor` proprietà per impostare il colore di sfondo (quantità incompleto).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
