---
title: ViewPager
description: ViewPager è un gestore di layout che consente di implementare l'esplorazione gestuali. Navigazione gestuali consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare l'esplorazione gestuali con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina usando PagerTitleStrip e PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: bb9795eb1e77a48b01556c553ae19613d6ab6de6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115939"
---
# <a name="viewpager"></a>ViewPager

_ViewPager è un gestore di layout che consente di implementare l'esplorazione gestuali. Navigazione gestuali consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare l'esplorazione gestuali con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina usando PagerTitleStrip e PagerTabStrip._

 
## <a name="overview"></a>Panoramica

Uno scenario comune nello sviluppo di app è la necessità di fornire agli utenti gestuali spostamento tra le visualizzazioni di pari livello. Questo approccio, l'utente passa a sinistra o destra per accedere alle pagine di contenuto (ad esempio, in una procedura guidata o una presentazione di diapositive). È possibile creare queste viste scorrere rapidamente usando il `ViewPager` widget, disponibile in [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Il `ViewPager` è costituito da più visualizzazioni figlio di un widget di layout in cui si intende per una pagina nel layout di ogni visualizzazione figlio: 

[![Screenshot di TreePager app con esempio di scorrimento orizzontale rapido](images/01-intro-sml.png)](images/01-intro.png#lightbox)

In genere `ViewPager` viene usato in combinazione con [frammenti](https://developer.xamarin.com/guides/android/platform_features/fragments/); tuttavia, esistono alcune situazioni in cui si potrebbe voler usare `ViewPager` senza la complessità aggiuntiva dei `Fragment`s.

`ViewPager` Usa un modello dell'adattatore per fornire le visualizzazioni da visualizzare. L'adapter utilizzato qui è concettualmente simile a quella usata dalla [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; è fornire un'implementazione di `PagerAdapter` per generare le pagine che il `ViewPager` Visualizza all'utente. Le pagine visualizzate dal `ViewPager` può essere `View`s o `Fragment`s. Quando `View`s vengono visualizzati, dell'adapter sottoclassi Android `PagerAdapter` classe di base. Se `Fragment`s vengono visualizzati, dell'adapter sottoclassi Android `FragmentPagerAdapter`. La libreria di supporto per Android include inoltre `FragmentPagerAdapter` (una sottoclasse `PagerAdapter`) per facilitare i dettagli della connessione `Fragment`s per i dati. 

Questa guida illustra entrambi gli approcci: 

-   Nelle [Viewpager con visualizzazioni](~/android/user-interface/controls/view-pager/viewpager-and-views.md), un [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) app è stato sviluppato per viene illustrato come usare `ViewPager` per visualizzare le visualizzazioni di un catalogo di struttura ad albero (una raccolta di immagini di alberi caducifoglio e il costante aggiornamento). 
    `PagerTabStrip`  e `PagerTitleStrip` vengono utilizzati per visualizzare i titoli studiati per facilitare la navigazione.

-   Nelle [Viewpager con frammenti](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), leggermente più complesso [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) app è stato sviluppato per viene illustrato come usare `ViewPager` con `Fragment`s per compilare un'app che presenta problemi matematici come le schede flash e risponde all'input dell'utente. 


## <a name="requirements"></a>Requisiti

Per utilizzare `ViewPager` nel progetto dell'app, è necessario installare il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacchetto. Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: inclusi a NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

 
## <a name="architecture"></a>Architettura

Tre componenti vengono utilizzati per l'implementazione di navigazione gestuali con `ViewPager`:

-   ViewPager
-   Adattatore
-   Indicatore di cercapersone

Ognuno di questi componenti viene riepilogata di seguito.



### <a name="viewpager"></a>ViewPager

`ViewPager` è un gestore di layout che consente di visualizzare una raccolta di `View`s uno alla volta. Il processo consiste nel rilevare dito dell'utente e passare alla visualizzazione precedente o successiva come appropriato. Ad esempio, la schermata seguente illustra un `ViewPager` in corso la transizione da un'immagine a quella successiva in risposta a un movimento dell'utente: 

[![Primo piano di TreePager app la visualizzazione di una transizione tra le visualizzazioni](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adattatore

`ViewPager` Estrae i dati da un *adapter*. È compito dell'adattatore per creare il `View`s visualizzato dal `ViewPager`, fornendo loro in base alle esigenze. Il diagramma seguente illustra questo concetto &ndash; l'adapter crea e popola `View`s e li fornisce per i `ViewPager`. Come le `ViewPager` rileva i movimenti tramite passaggio del dito dell'utente, verrà chiesto all'adapter di fornire appropriato `View` da visualizzare: 

[![Diagramma che illustra come l'Adapter si connette le immagini e i nomi per il ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

In questo particolare esempio, ogni `View` viene costruita da un'immagine di struttura ad albero e un nome di struttura prima che venga passata al `ViewPager`. 



### <a name="pager-indicator"></a>Indicatore di cercapersone

`ViewPager` può essere utilizzato per visualizzare grandi set di dati (ad esempio, una raccolta di immagini possa contenere centinaia di immagini). Per consentire all'utente di grandi set di dati, passare `ViewPager` è spesso accompagnato da un *indicatore cercapersone* che visualizza una stringa. La stringa potrebbe essere il titolo dell'immagine, una didascalia o semplicemente i posizione della visualizzazione corrente all'interno del set di dati. 

Sono disponibili due viste che consentono di ottenere queste informazioni di navigazione per l'utente: `PagerTabStrip` e `PagerTitleStrip.` ognuno consente di visualizzare una stringa all'inizio di un `ViewPager`, e ognuno estrae i dati dal `ViewPager`dell'adapter in modo che venga sempre resta sincronizzato con il attualmente visualizzato `View`. La differenza tra di esse è che `PagerTabStrip` include un indicatore visivo per la stringa "corrente" mentre `PagerTitleStrip` non (come illustrato nelle schermate illustrate): 

[![Screenshot dell'app TreePager con PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Questa Guida dimostra come immplement `ViewPager`, adapter e componenti dell'app indicatore e integrare in modo da supportare la navigazione gestuali. 



## <a name="related-links"></a>Collegamenti correlati

- [TreePager (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
