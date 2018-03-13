---
title: ViewPager
description: "ViewPager è un gestore di layout che consente di implementare gestuali navigazione. Navigazione gestuali consente all'utente Scorri rapidamente verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare gestuali navigazione con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina utilizzando PagerTitleStrip e PagerTabStrip."
ms.topic: article
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 5e2f93eea970a15df03b00cc962ca7482624973d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="viewpager"></a>ViewPager

_ViewPager è un gestore di layout che consente di implementare gestuali navigazione. Navigazione gestuali consente all'utente Scorri rapidamente verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare gestuali navigazione con ViewPager, con e senza frammenti. Viene inoltre descritto come aggiungere indicatori di pagina utilizzando PagerTitleStrip e PagerTabStrip._

 
## <a name="overview"></a>Panoramica

Sviluppo di applicazioni in uno scenario comune è la necessità di fornire agli utenti gestuali spostamento tra le visualizzazioni di pari livello. In questo approccio, l'utente passa a sinistra o verso destra per accedere alle pagine di contenuto (ad esempio, in una procedura guidata o una presentazione). È possibile creare queste viste scorrere usando il `ViewPager` widget, disponibile in [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Il `ViewPager` è costituito da più visualizzazioni figlio un widget di layout in ogni visualizzazione figlio costituisce una pagina nel layout: 

[![Schermate di TreePager app con un esempio di scorrere orizzontale](images/01-intro-sml.png)](images/01-intro.png#lightbox)

In genere, `ViewPager` viene utilizzato in combinazione con [frammenti](https://developer.xamarin.com/guides/android/platform_features/fragments/); tuttavia, esistono alcune situazioni in cui si desidera utilizzare `ViewPager` senza la complessità aggiuntiva dei `Fragment`s.

`ViewPager` Usa un modello dell'adattatore per fornire visualizzazioni da visualizzare. L'adapter utilizzato qui è concettualmente simile a quella usata dalla [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; è fornire un'implementazione di `PagerAdapter` per generare le pagine che il `ViewPager` Visualizza all'utente. Le pagine visualizzate da `ViewPager` può essere `View`s o `Fragment`s. Quando `View`s vengono visualizzati, dell'adapter sottoclassi Android `PagerAdapter` classe di base. Se `Fragment`s vengono visualizzati, dell'adapter sottoclassi Android `FragmentPagerAdapter`. Include anche la libreria di supporto Android `FragmentPagerAdapter` (una sottoclasse di `PagerAdapter`) per i dettagli della connessione `Fragment`s ai dati. 

Questa guida illustra entrambi gli approcci: 

-   In [Viewpager con viste](~/android/user-interface/controls/view-pager/viewpager-and-views.md), [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) app è stata sviluppata per viene illustrato come utilizzare `ViewPager` per visualizzare le viste del catalogo struttura ad albero (una raccolta di immagini di alberi caducifoglio e sempreverde). 
    `PagerTabStrip`  e `PagerTitleStrip` vengono utilizzati per visualizzare i titoli che ti consentono di navigazione a pagina.

-   In [Viewpager con frammenti](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), leggermente più complesso [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) app è stata sviluppata per viene illustrato come utilizzare `ViewPager` con `Fragment`s per compilare un'app che presenta problemi di matematica come schede e risponde all'input dell'utente. 


## <a name="requirements"></a>Requisiti

Per utilizzare `ViewPager` nel progetto dell'app, è necessario installare il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacchetto. Per ulteriori informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: inclusi un NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

 
## <a name="architecture"></a>Architettura

Tre componenti vengono utilizzati per l'implementazione dello spostamento gestuali con `ViewPager`:

-   ViewPager
-   Adattatore
-   Indicatore di cercapersone

Ognuno di questi componenti riepilogata di seguito.



### <a name="viewpager"></a>ViewPager

`ViewPager` è un gestore di layout che visualizza una raccolta di `View`s uno alla volta. Il processo è per rilevare i movimenti di scorrere dell'utente e passare alla visualizzazione precedente o successiva, come appropriato. Ad esempio, la schermata seguente illustra un `ViewPager` la transizione da un'immagine a quella successiva in risposta a un movimento dell'utente: 

[![Primo piano di TreePager app la visualizzazione di una transizione tra le visualizzazioni](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adattatore

`ViewPager` Estrae i dati da un *adapter*. Processo della scheda di rete consiste nel creare il `View`s visualizzato per il `ViewPager`, fornendo loro in base alle esigenze. Nel diagramma seguente viene illustrato questo concetto &ndash; l'adapter crea e popola `View`s e consente loro del `ViewPager`. Come il `ViewPager` rileva i movimenti scorrere dell'utente, viene chiesto all'adapter di fornire appropriata `View` da visualizzare: 

[![Diagramma che illustra come l'Adapter si connette le immagini e i nomi per il ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

In questo particolare esempio, ogni `View` viene costruita da un'immagine di struttura ad albero e un nome di struttura prima di essere passato per il `ViewPager`. 



### <a name="pager-indicator"></a>Indicatore di cercapersone

`ViewPager` può essere utilizzato per visualizzare un set di dati di grandi dimensioni (ad esempio, una raccolta di immagini può contenere centinaia di immagini). Per consentire all'utente di grandi set di dati, passare `ViewPager` è spesso accompagnato da un *indicatore cercapersone* che viene visualizzata una stringa. La stringa può essere il titolo dell'immagine, una didascalia o semplicemente i posizione della visualizzazione corrente all'interno del set di dati. 

Sono disponibili due visualizzazioni che consentono di ottenere queste informazioni di navigazione per l'utente: `PagerTabStrip` e `PagerTitleStrip.` ognuno viene visualizzata una stringa all'inizio di un `ViewPager`, ed ogni estrae i dati di `ViewPager`dell'adapter in modo che non è sempre sincronizzata con il attualmente visualizzato `View`. La differenza tra di esse è che `PagerTabStrip` include un indicatore visivo per la stringa "corrente" mentre `PagerTitleStrip` non (come illustrato in queste schermate): 

[![Schermate dell'app TreePager con PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Questa guida illustra come immplement `ViewPager`, adapter e componenti dell'app indicatore e integrare in modo da supportare la navigazione gestuali. 



## <a name="related-links"></a>Collegamenti correlati

- [TreePager (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
