---
title: ViewPager
description: ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare la navigazione gestuale con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina utilizzando PagerTitleStrip e PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 0535dc5d2abcfa1587b8101d7a4e382782efb8ca
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510202"
---
# <a name="viewpager"></a>ViewPager

_ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare la navigazione gestuale con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina utilizzando PagerTitleStrip e PagerTabStrip._

 
## <a name="overview"></a>Panoramica

Uno scenario comune nello sviluppo di app è la necessità di fornire agli utenti la navigazione gestuale tra le visualizzazioni di pari livello. Con questo approccio, l'utente scorre verso sinistra o verso destra per accedere alle pagine di contenuto (ad esempio, in una procedura guidata di installazione o di presentazione). È possibile creare queste visualizzazioni swipe usando il `ViewPager` widget, disponibile nella libreria di [supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). `ViewPager` È un widget di layout composto da più visualizzazioni figlio in cui ogni visualizzazione figlio costituisce una pagina nel layout: 

[![Screenshot dell'app TreePager con esempio di scorrimento orizzontale](images/01-intro-sml.png)](images/01-intro.png#lightbox)

In genere `ViewPager` , viene utilizzato in combinazione con i [frammenti](~/android/platform/fragments/index.md). Tuttavia, esistono alcune situazioni in cui è possibile utilizzare `ViewPager` senza la complessità aggiuntiva di. `Fragment`

`ViewPager`Usa un modello di adapter per fornire le visualizzazioni da visualizzare. L'adapter usato in questo caso è concettualmente simile a quello usato da [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; . si fornisce `PagerAdapter` un'implementazione di per generare le `ViewPager` pagine visualizzate dall'utente. Le pagine visualizzate da `ViewPager` possono essere `View`s o `Fragment`s. Quando `View`vengono visualizzati gli oggetti, l'adapter esegue la sottoclasse `PagerAdapter` della classe di base di Android. Se `Fragment`vengono visualizzati, l'adapter sottoclassi di `FragmentPagerAdapter`Android. La libreria di supporto Android include `FragmentPagerAdapter` anche (una sottoclasse di `PagerAdapter`) per semplificare i dettagli della `Fragment`connessione dei ai dati. 

In questa guida vengono illustrati entrambi gli approcci: 

-   In [viewpager con visualizzazioni](~/android/user-interface/controls/view-pager/viewpager-and-views.md), viene sviluppata un'app [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) per illustrare come usare `ViewPager` per visualizzare le visualizzazioni di un catalogo ad albero (una raccolta di immagini di alberi decidui ed Evergreen). 
    `PagerTabStrip`e `PagerTitleStrip` vengono usati per visualizzare i titoli che facilitano l'esplorazione delle pagine.

-   In [viewpager con frammenti](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), è stata sviluppata un'app [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) leggermente più complessa per dimostrare come usare `ViewPager` con `Fragment`i per creare un'app che presenta problemi matematici come schede flash e risponde all'input dell'utente. 


## <a name="requirements"></a>Requisiti

Per usare `ViewPager` nel progetto dell'app, è necessario installare il pacchetto della [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Per ulteriori informazioni sull'installazione di pacchetti NuGet, [vedere Procedura dettagliata: Inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

 
## <a name="architecture"></a>Architettura

Vengono usati tre componenti per implementare la navigazione gestuale `ViewPager`con:

-   ViewPager
-   Adattatore
-   Indicatore cercapersone

Ognuno di questi componenti è riepilogato di seguito.



### <a name="viewpager"></a>ViewPager

`ViewPager`è un gestore di layout che visualizza una raccolta `View`di uno alla volta. Il processo consiste nel rilevare il gesto di scorrimento dell'utente e passare alla visualizzazione successiva o precedente, in base alle esigenze. Ad esempio, nella schermata seguente viene illustrato `ViewPager` un passaggio da un'immagine a quella successiva in risposta a un gesto utente: 

[![Primo piano dell'app TreePager che visualizza una transizione tra le visualizzazioni](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adattatore

`ViewPager`estrae i dati da un *Adapter*. Il processo dell'adapter consiste nel creare le `View`istanze `ViewPager`di visualizzate da, fornendole in base alle esigenze. Il diagramma seguente illustra questo concetto &ndash; `ViewPager`. l'adapter crea e `View`popola i e li fornisce a. Poiché rileva i movimenti di scorrimento dell'utente, chiede all'adapter di fornire l'oggetto appropriato `View` da visualizzare: `ViewPager` 

[![Diagramma che illustra il modo in cui l'adapter connette le immagini e i nomi a ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

In questo particolare esempio, ogni `View` oggetto viene costruito da un'immagine di struttura ad albero e da un nome `ViewPager`di albero prima che venga passato a. 



### <a name="pager-indicator"></a>Indicatore cercapersone

`ViewPager`può essere usato per visualizzare un set di dati di grandi dimensioni (ad esempio, una raccolta immagini può contenere centinaia di immagini). Per consentire all'utente di spostarsi tra set di `ViewPager` dati di grandi dimensioni, spesso è accompagnato da un *indicatore cercapersone* che visualizza una stringa. La stringa può essere il titolo dell'immagine, una didascalia o semplicemente la posizione della visualizzazione corrente all'interno del set di dati. 

Sono disponibili due visualizzazioni che possono produrre le informazioni di navigazione per l' `PagerTabStrip` utente `PagerTitleStrip.` : e ognuna Visualizza una stringa nella parte superiore `ViewPager`di un oggetto `ViewPager`ed esegue il pull dei dati dall'adapter in modo che rimanga sempre sincronizzato con il attualmente visualizzato `View`. La differenza tra di esse è `PagerTabStrip` che include un indicatore visivo per la stringa "corrente" `PagerTitleStrip` mentre non lo è, come illustrato nelle schermate seguenti: 

[![Screenshot dell'app TreePager con PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Questa guida illustra come immplement `ViewPager`, adapter e indicatore dei componenti dell'app e per integrarli per supportare la navigazione gestuale. 



## <a name="related-links"></a>Collegamenti correlati

- [TreePager (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
