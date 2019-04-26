---
title: Utilizzo di tvOS indicatori di stato di avanzamento in Xamarin
description: Questo documento descrive come lavorare con gli indicatori di stato di avanzamento in un'app tvOS compilate con Xamarin. Illustra sia gli indicatori di stato e gli indicatori di attività.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/25/2018
ms.openlocfilehash: cbd2b2de237a5bb22d1dc0242569b96b12bca070
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61180741"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Utilizzo di tvOS indicatori di stato di avanzamento in Xamarin

_Questo articolo illustra la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app xamarin. tvos._

Potrebbero esserci volte quando l'app xamarin. tvos deve caricare nuovo contenuto o eseguire un'operazione lunga elaborazione. Durante tali periodi, è necessario presentare un indicatore dell'attività o un indicatore di stato per informare l'utente che l'app è ancora in esecuzione e agli utenti alcune indicazione in merito alla durata dell'attività in esecuzione.

![Gli indicatori di stato di esempio](progress-indicators-images/intro01.png "gli indicatori di stato di esempio")

## <a name="about-activity-indicators"></a>Informazioni sugli indicatori di attività

Un indicatore dell'attività viene presentata come un'icona di rotazione e viene usato per rappresentare un'attività di lunghezza non determinato. L'indicatore viene visualizzata quando l'attività viene avviata e scomparirà quando l'attività è stata completata.

Apple ha i seguenti suggerimenti per lavorare con gli indicatori di attività:

- **Se possibile, usare le barre di avanzamento** - perché offre l'indicatore un'attività richiederà all'utente alcun feedback sulla come non long il processo in esecuzione, usare sempre un indicatore di stato se la lunghezza è nota (ad esempio, il numero di byte per il download in un file).
- **Mantenere l'indicatore animato** -utenti correlati un indicatore dell'attività fermo a un'app bloccata, in modo che si deve sempre animare l'indicatore mentre vengano visualizzati.
- **Descrivere l'attività in fase di elaborazione** -solo visualizzazione l'indicatore dell'attività di per sé non è sufficiente; l'utente deve essere informato il processo in cui sono in attesa. Includere un'etichetta significativa (in genere una frase unica e completa) che definisca chiaramente l'attività.

## <a name="about-progress-bars"></a>Informazioni sulle barre di stato di avanzamento

Un indicatore di stato viene presentato come una riga che viene riempito con il colore per indicare lo stato e lunghezza di un'attività che richiedono molto tempo. Le barre di avanzamento devono sempre essere utilizzate quando la lunghezza delle attività è noto o può essere calcolata.

Apple ha i seguenti suggerimenti per l'uso di indicatori di stato:

- **Segnalare con precisione lo stato di avanzamento** -indicatori di stato devono sempre presentare una rappresentazione accurata del tempo necessario per completare un'attività. Non falsificare mai il tempo necessario per rendere l'app disponibile.
- **Uso di durate ben definite** -collocare lo stato di avanzamento barre devono non solo mostrano che è in corso un'attività di lunga durata, ma offrono all'utente e indicazione della quantità di attività viene completata e una stima del tempo rimanente.

## <a name="progress-indicators-and-storyboards"></a>Storyboard e gli indicatori di stato

Il modo più semplice per lavorare con un indicatore di stato in un'app xamarin. tvos consiste nell'aggiungerlo all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
    
1. Nel **riquadro della soluzione**, fare doppio clic il **Main. Storyboard** file e aprirlo e modificarlo.

2. Trascinare un' **indicatore dell'attività** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    ![Un indicatore dell'attività](progress-indicators-images/activity01.png "un indicatore dell'attività")

3. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio relativi **stile**, **comportamento**, e **nome**: 

    ![La scheda di Widget per un indicatore dell'attività](progress-indicators-images/activity02.png "scheda il Widget per un indicatore dell'attività")
    
    Il **Name** determina il nome della proprietà che rappresenta l'indicatore di attività in C# codice.

4. Trascinare un **Visualizza lo stato di avanzamento** dalle **della casella degli strumenti** e rilasciarla sulla vista: 

    ![Una vista stato di avanzamento](progress-indicators-images/activity03.png "una vista stato")

5. Nel **Widget** scheda della finestra di **Esplora proprietà**, è possibile modificare diverse proprietà della vista lo stato di avanzamento, ad esempio relativi **stile**, **lo stato di avanzamento**(percentuale di completamento), e **nome**: 

    ![La scheda di Widget per una visualizzazione stato di avanzamento](progress-indicators-images/activity04.png "scheda il Widget per una visualizzazione stato di avanzamento")
    
    Il **Name** determina il nome della proprietà che rappresenta la visualizzazione di stato di avanzamento in C# codice.

6. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Nel **Esplora soluzioni**, fare doppio clic il **Main. Storyboard** file e aprirlo e modificarlo.

2. Trascinare un' **indicatore dell'attività** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    ![Un indicatore dell'attività](progress-indicators-images/activity01-vs.png
    "un indicatore dell'attività")

3. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, è possibile modificare diverse proprietà dell'indicatore di attività, ad esempio relativi **stile**, **comportamento**, e **nome**: 

    ![La scheda di Widget per un indicatore dell'attività](progress-indicators-images/activity02-vs.png "scheda il Widget per un indicatore dell'attività")

    Il **Name** determina il nome della proprietà che rappresenta l'indicatore di attività in C# codice.

4. Trascinare un **Visualizza lo stato di avanzamento** dalle **della casella degli strumenti** e rilasciarla sulla vista: 

   ![Una vista stato di avanzamento](progress-indicators-images/activity03-vs.png "una vista stato")

5. Nel **Widget** scheda della finestra di **Esplora proprietà**, è possibile modificare diverse proprietà della vista lo stato di avanzamento, ad esempio relativi **stile**, **lo stato di avanzamento**(percentuale di completamento), e **nome**: 

    ![La scheda di Widget per una visualizzazione stato di avanzamento](progress-indicators-images/activity04-vs.png "scheda il Widget per una visualizzazione stato di avanzamento")
    
    Il **Name** determina il nome della proprietà che rappresenta la visualizzazione di stato di avanzamento in C# codice.

6. Salvare le modifiche.

-----

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Utilizzo di indicatori di attività

Come indicato in precedenza, gli indicatori di attività devono essere visualizzati quando l'app è in esecuzione un processo lungo della lunghezza indeterminato.

In qualsiasi momento, si può vedere se un indicatore dell'attività viene aggiunta un'animazione controllando il `IsAnimating` proprietà. Se il `HidesWhenStopped` è di proprietà `true`, l'indicatore di attività verrà automaticamente nascosto quando viene arrestata la relativa animazione.

Per avviare l'animazione, è possibile utilizzare il codice seguente: 

```csharp
ActivityIndicator.StartAnimating();
```

E l'animazione verrà interrotta la seguente:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Questi frammenti di codice presuppongono che l'indicatore di attività **Name** è stata impostata su **ActivityIndicator** nel **Widget** scheda della finestra di iOS Designer.

## <a name="working-with-progress-bars"></a>Utilizzo di indicatori di stato

Anche in questo caso, un indicatore di stato deve essere usato ogni volta che l'app è in esecuzione un'attività a esecuzione prolungata di una durata nota. 

Il `Progress` proprietà viene utilizzata per impostare la quantità di attività che è stata completata da 0% al 100% (da 0,0 a 1,0). Usare il `ProgressTintColor` proprietà per impostare il colore della barra di quantità completato e `TrackTintColor` proprietà da impostare il colore di sfondo (incompiute quantità).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e utilizzo di indicatori di stato di avanzamento all'interno di un'app xamarin. tvos.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
