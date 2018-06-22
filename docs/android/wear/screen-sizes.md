---
title: Utilizzo di dimensioni dello schermo in xamarin. Android e usura del sistema operativo
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 40e7850ffe239b0ede43e4d0cd3c6da08bce3a40
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436076"
---
# <a name="working-with-screen-sizes"></a>Utilizzo delle dimensioni dello schermo

I dispositivi Android usura possono avere un rettangolare o una visualizzazione, che può anche essere di dimensioni diverse.

![Consente di visualizzare le schermate di usura rettangolare e round](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Che identifica il tipo di schermata

La libreria di supporto usura fornisce alcuni controlli che consentono di rilevare e adattano alle forme dello schermo diverse, ad esempio `WatchViewStub` e `BoxInsetLayout`.

Tenere presente che alcuni degli altri supportano i controlli di libreria (ad esempio `GridViewPager`) *automaticamente* rilevare forma schermata se stessi e non devono essere aggiunti come elementi figlio dei controlli descritto di seguito.

### <a name="watchviewstub"></a>WatchViewStub

Vedere il [WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/) per informazioni su come rilevare il tipo di schermata e visualizzare un layout diverso per ogni tipo.

Il file di layout principale contiene un `android.support.wearable.view.WatchViewStub` che fa riferimento a diversi layout per le schermate rettangolare e arrotondamento utilizzando il `app:rectLayout` e `app:roundLayout` attributi:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

La soluzione contiene layout diversi per ogni stile che verrà selezionato in fase di esecuzione:

![File visualizzati nell'area risorse/layout](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

Invece di creare diversi layout per ogni tipo di schermata, è possibile creare anche una singola visualizzazione che si adatta alle schermate di arrotondamento o rettangolare.

Questo [esempio Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) viene illustrato come utilizzare il `BoxInsetLayout` per utilizzare lo stesso layout rettangolare e arrotonda per schermate.


## <a name="wear-ui-designer"></a>Usare la progettazione dell'interfaccia utente

La finestra di progettazione Android di Xamarin supporta rettangolare e arrotondare per schermate:

![Selezionando la schermata di Android usura quadrato nella finestra di progettazione Android Xamarin](screen-sizes-images/design-screen-type.png)

Finestra di progettazione di applicazioni in stile rettangolare è illustrato di seguito:

![Area di progettazione in stile rettangolare](screen-sizes-images/design-rect.png) 

Finestra di progettazione di applicazioni in stile round è illustrato di seguito:

![Area di progettazione nello stile della funzione round](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Accenti simulatore

Il **Gestione emulatori di Google** contiene le definizioni di dispositivo per entrambi i tipi di schermata. È possibile creare rettangolari e round emulatori per testare l'app.

![Usare le definizioni di dispositivo visualizzate in Gestione emulatori di Google](screen-sizes-images/emulator-devices.png)

L'emulatore eseguirà il rendering di una schermata rettangolare simile al seguente:

![Emulatore per il rendering di una schermata rettangolare](screen-sizes-images/recipe-2.png) 

Per una schermata di arrotondamento, esegue il rendering simile al seguente:

![Emulatore per il rendering di una schermata di arrotondamento](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Video

[Le app a schermo intero per Android usura](https://www.youtube.com/watch?v=naf_WbtFAlY) da [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

