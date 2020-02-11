---
title: ViewPager
description: ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare la navigazione gestuale con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina utilizzando PagerTitleStrip e PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 600a94a0ee9eb5bcf06dc19d95cf9e77132a2e81
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029058"
---
# <a name="viewpager"></a>ViewPager

_ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare la navigazione gestuale con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina utilizzando PagerTitleStrip e PagerTabStrip._

## <a name="overview"></a>Panoramica

Uno scenario comune nello sviluppo di app è la necessità di fornire agli utenti la navigazione gestuale tra le visualizzazioni di pari livello. Con questo approccio, l'utente scorre verso sinistra o verso destra per accedere alle pagine di contenuto (ad esempio, in una procedura guidata di installazione o di presentazione). È possibile creare queste visualizzazioni swipe usando il widget `ViewPager`, disponibile nella [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Il `ViewPager` è un widget di layout composto da più visualizzazioni figlio, in cui ogni visualizzazione figlio costituisce una pagina nel layout: 

[![screenshot dell'app TreePager con esempio di scorrimento orizzontale](images/01-intro-sml.png)](images/01-intro.png#lightbox)

In genere, `ViewPager` viene utilizzata insieme ai [frammenti](~/android/platform/fragments/index.md); Tuttavia, esistono alcune situazioni in cui potrebbe essere necessario usare `ViewPager` senza la complessità aggiuntiva di `Fragment`.

`ViewPager` utilizza un modello di adapter per fornire le visualizzazioni da visualizzare. L'adapter usato in questo caso è concettualmente simile a quello usato da [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; si fornisce un'implementazione di `PagerAdapter` per generare le pagine visualizzate dall'`ViewPager` all'utente. Le pagine visualizzate da `ViewPager` possono essere `View`s o `Fragment`s. Quando vengono visualizzati `View`s, l'adapter sottoclasses `PagerAdapter` classe di base di Android. Se vengono visualizzate `Fragment`s, l'adapter sottoclasses `FragmentPagerAdapter`di Android. La libreria di supporto Android include anche `FragmentPagerAdapter` (una sottoclasse di `PagerAdapter`) per semplificare i dettagli relativi alla connessione di `Fragment`ai dati. 

In questa guida vengono illustrati entrambi gli approcci: 

- In [viewpager con visualizzazioni](~/android/user-interface/controls/view-pager/viewpager-and-views.md), viene sviluppata un'app [TreePager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager) per illustrare come usare `ViewPager` per visualizzare le visualizzazioni di un catalogo ad albero (una raccolta di immagini di alberi decidui ed Evergreen). 
    `PagerTabStrip` e `PagerTitleStrip` vengono utilizzati per visualizzare i titoli che facilitano l'esplorazione delle pagine.

- In [viewpager con i frammenti](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), è stata sviluppata un'app [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) leggermente più complessa per illustrare come usare `ViewPager` con `Fragment`per creare un'app che presenta problemi matematici come schede flash e risponde all'input dell'utente. 

## <a name="requirements"></a>Requisiti

Per usare `ViewPager` nel progetto di app, è necessario installare il pacchetto della [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

## <a name="architecture"></a>Architettura

Vengono usati tre componenti per implementare la navigazione gestuale con `ViewPager`:

- ViewPager
- Adapter
- Indicatore cercapersone

Ognuno di questi componenti è riepilogato di seguito.

### <a name="viewpager"></a>ViewPager

`ViewPager` è un gestore di layout che visualizza una raccolta di `View`uno alla volta. Il processo consiste nel rilevare il gesto di scorrimento dell'utente e passare alla visualizzazione successiva o precedente, in base alle esigenze. Ad esempio, nella schermata seguente viene illustrato un `ViewPager` la transizione da un'immagine a quella successiva in risposta a un gesto utente: 

[![primo piano dell'app TreePager che visualizza una transizione tra le visualizzazioni](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>Adapter

`ViewPager` estrae i dati da un *Adapter*. Il processo dell'adapter consiste nel creare le `View`visualizzate dall'`ViewPager`, fornendole in base alle esigenze. Il diagramma seguente illustra questo concetto &ndash; l'adapter crea e popola `View`s e le fornisce al `ViewPager`. Quando il `ViewPager` rileva i movimenti di scorrimento dell'utente, chiede all'adapter di fornire i `View` appropriati da visualizzare: 

[![diagramma che illustra il modo in cui l'adapter connette le immagini e i nomi a ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

In questo particolare esempio, ogni `View` viene costruita da un'immagine dell'albero e da un nome di albero prima che venga passato al `ViewPager`. 

### <a name="pager-indicator"></a>Indicatore cercapersone

`ViewPager` possibile utilizzare per visualizzare un set di dati di grandi dimensioni (ad esempio, una raccolta immagini può contenere centinaia di immagini). Per consentire all'utente di spostarsi tra set di dati di grandi dimensioni, `ViewPager` spesso è accompagnato da un *indicatore cercapersone* che visualizza una stringa. La stringa può essere il titolo dell'immagine, una didascalia o semplicemente la posizione della visualizzazione corrente all'interno del set di dati. 

Sono disponibili due visualizzazioni che possono produrre le informazioni di navigazione seguenti: `PagerTabStrip` e `PagerTitleStrip.` ciascuna di esse Visualizza una stringa nella parte superiore di un `ViewPager`ed esegue il pull dei dati dalla scheda `ViewPager`in modo che rimanga sempre sincronizzata con il `View`attualmente visualizzato. La differenza tra di essi è che `PagerTabStrip` include un indicatore visivo per la stringa "corrente" mentre `PagerTitleStrip` non lo è, come illustrato nelle schermate seguenti: 

[![screenshot dell'app TreePager con PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Questa guida illustra come immplement i componenti dell'app `ViewPager`, adapter e Indicator e come integrarli per supportare la navigazione gestuale. 

## <a name="related-links"></a>Collegamenti correlati

- [TreePager (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
