---
title: Utilizzo di indicatori di stato di avanzamento
description: Questo articolo descrive la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/25/2018
ms.openlocfilehash: d512dfddb3a6c81767f937272a4ffb1ab1a35372
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="working-with-progress-indicators"></a>Utilizzo di indicatori di stato di avanzamento

_Questo articolo descrive la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app Xamarin.tvOS._

Talvolta potrebbe essere quando l'app Xamarin.tvOS deve caricare nuovo contenuto o eseguire un'operazione di lunga durata di elaborazione. Durante tali periodi, è necessario presentare un indicatore di attività o un indicatore di stato per informare l'utente che l'app è ancora in esecuzione e per consentire loro alcune indicazione per capire la lunghezza dell'attività in esecuzione.

![Gli indicatori di stato di esempio](progress-indicators-images/intro01.png "gli indicatori di stato di esempio")

## <a name="about-activity-indicators"></a>Informazioni sugli indicatori di attività

Un indicatore di attività viene presentata come una ruota dentata rotante e viene utilizzato per rappresentare un'attività di una determinata lunghezza. L'indicatore viene visualizzato quando l'attività viene avviata e scompare quando l'attività è stata completata.

Apple ha i seguenti suggerimenti per l'utilizzo di indicatori di attività:

- **Se possibile, utilizzare le barre di stato di avanzamento** , perché consente di indicatore un'attività richiederà all'utente alcun feedback sulla procedura non lunghi per il processo in esecuzione, utilizzare sempre una barra di stato se la lunghezza è nota (ad esempio, il numero di byte da scaricare in un file).
- **Mantenere l'indicatore animata** -gli utenti correlati un indicatore di attività fissa per un'app bloccata, in modo che si deve sempre aggiunge un'animazione l'indicatore mentre viene visualizzato.
- **Descrizione per l'attività in fase di elaborazione** -solo visualizzazione l'indicatore di attività da sola non è sufficiente, l'utente deve essere informato il processo in cui sono in attesa. Includere un'etichetta significativa (in genere una frase completa, single) che definisce in modo chiaro l'attività.

## <a name="about-progress-bars"></a>Informazioni sulle barre di stato di avanzamento

Un indicatore di stato viene presentata come una riga che viene compilato con colore per indicare lo stato e lunghezza di un'attività richiede molto tempo. Barre di stato di avanzamento devono sempre essere utilizzate quando la lunghezza delle attività è noto o può essere calcolata.

Apple ha i seguenti suggerimenti per l'utilizzo di indicatori di stato:

- **Segnalare con precisione lo stato di avanzamento** -indicatori di stato devono sempre presentare una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per rendere l'app disponibile.
- **Utilizzare per la durata ben definita** -lo stato di avanzamento barre devono non solo mostra che impiega un'attività di lunga durata impone, ma offrono all'utente e indicazione della quantità di attività viene completata e una stima del tempo rimanente.

## <a name="progress-indicators-and-storyboards"></a>Storyboard e gli indicatori di stato

Il modo più semplice per lavorare con un indicatore di stato in un'app Xamarin.tvOS è per aggiungerlo all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
1. Nel **soluzione riempimento**, fare doppio clic sui **Main** file e aprirlo e modificarlo.

2. Trascinare un **indicatore di attività** dal **casella degli strumenti** e rilasciarla sulla vista: 

    ![Un indicatore di attività](progress-indicators-images/activity01.png "un indicatore di attività")

3. Nel **Widget** scheda della finestra il **proprietà riempimento**, è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio relativi **stile**, **comportamento**, e **nome**: 

    ![La scheda di Widget per un indicatore di attività](progress-indicators-images/activity02.png "scheda il Widget per un indicatore di attività")
    
    Il **nome** determina il nome della proprietà che rappresenta l'indicatore di attività nel codice c#.

4. Trascinare un **Visualizza lo stato di avanzamento** dal **casella degli strumenti** e rilasciarla sulla vista: 

    ![Una vista di stato di avanzamento](progress-indicators-images/activity03.png "una vista di stato di avanzamento")

5. Nel **Widget** scheda della finestra il **Esplora proprietà**, è possibile modificare diverse proprietà della vista lo stato di avanzamento, ad esempio relativi **stile**, **lo stato di avanzamento**(percentuale di completamento), e **nome**: 

    ![La scheda di Widget per una vista di stato di avanzamento](progress-indicators-images/activity04.png "scheda il Widget per una vista di stato di avanzamento")
    
    Il **nome** determina il nome della proprietà che rappresenta la visualizzazione di stato di avanzamento nel codice c#.

6. Salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Nel **Esplora soluzioni**, fare doppio clic sui **Main** file e aprirlo e modificarlo.

2. Trascinare un **indicatore di attività** dal **casella degli strumenti** e rilasciarla sulla vista: 

    ![Un indicatore di attività](progress-indicators-images/activity01-vs.png
    "un indicatore di attività")

3. Nel **Widget** scheda della finestra di **Esplora proprietà**, è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio relativo **stile**, **comportamento**, e **nome**: 

    ![La scheda di Widget per un indicatore di attività](progress-indicators-images/activity02-vs.png "scheda il Widget per un indicatore di attività")

    Il **nome** determina il nome della proprietà che rappresenta l'indicatore di attività nel codice c#.

4. Trascinare un **Visualizza lo stato di avanzamento** dal **casella degli strumenti** e rilasciarla sulla vista: 

   ![Una vista di stato di avanzamento](progress-indicators-images/activity03-vs.png "una vista di stato di avanzamento")

5. Nel **Widget** scheda della finestra il **Esplora proprietà**, è possibile modificare diverse proprietà della vista lo stato di avanzamento, ad esempio relativi **stile**, **lo stato di avanzamento**(percentuale di completamento), e **nome**: 

    ![La scheda di Widget per una vista di stato di avanzamento](progress-indicators-images/activity04-vs.png "scheda il Widget per una vista di stato di avanzamento")
    
    Il **nome** determina il nome della proprietà che rappresenta la visualizzazione di stato di avanzamento nel codice c#.

6. Salvare le modifiche.

-----

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Utilizzo di indicatori di attività

Come descritto in precedenza, gli indicatori di attività devono essere visualizzati quando l'app è in esecuzione un processo lungo della lunghezza indeterminato.

Un punto qualsiasi, è possibile vedere se un indicatore di attività sta aggiungendo un'animazione controllando il relativo `IsAnimating` proprietà. Se il `HidesWhenStopped` proprietà `true`, l'indicatore di attività verrà nascoste automaticamente quando viene arrestata l'animazione.

Per avviare l'animazione, è possibile utilizzare il codice seguente: 

```csharp
ActivityIndicator.StartAnimating();
```

E l'animazione verrà arrestare le operazioni seguenti:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Questi frammenti di codice si presuppongono che l'indicatore di attività **nome** è stato impostato su **ActivityIndicator** nel **Widget** scheda della finestra di progettazione iOS.

## <a name="working-with-progress-bars"></a>Utilizzo di indicatori di stato

Anche in questo caso un indicatore di stato deve essere utilizzato ogni volta che l'app è in esecuzione un'attività a esecuzione prolungata di una durata nota. 

Il `Progress` proprietà viene utilizzata per impostare la quantità di attività che è stata completata da 0% e 100% (da 0,0 a 1,0). Utilizzare il `ProgressTintColor` proprietà per impostare il colore della barra di quantità completata e `TrackTintColor` proprietà per impostare il colore di sfondo (quantità incompleto).

## <a name="summary"></a>Riepilogo

In questo articolo è illustrata la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app Xamarin.tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
