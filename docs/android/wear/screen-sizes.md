---
title: Lavorare con le dimensioni dello schermo in xamarin. Android e l'usura del sistema operativo
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a9b71fb069a428d9bec03481c986f4deb4c904ea
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827729"
---
# <a name="working-with-screen-sizes"></a>Lavorare con le dimensioni dello schermo

I dispositivi Android Wear possono avere un rettangolare o una visualizzazione, che può anche essere di dimensioni diverse.

![Vengono visualizzate alcune schermate dell'usura rettangolare e della funzione round](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Che identifica il tipo di schermata

La libreria di supporto di usura fornisce alcuni controlli che consentono di rilevare e adattano alle forme dello schermo diverse, ad esempio `WatchViewStub` e `BoxInsetLayout`.

Tenere presente che alcuni degli altri supportano i controlli di libreria (ad esempio `GridViewPager`) *automaticamente* rilevare forma dello schermo se stessi e non devono essere aggiunti come elementi figlio dei controlli descritto di seguito.

### <a name="watchviewstub"></a>WatchViewStub

Vedere le [WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/) esempio per informazioni su come rilevare il tipo di schermata e visualizza un layout diverso per ogni tipo.

Il file di layout principale contiene un `android.support.wearable.view.WatchViewStub` che fa riferimento a un layout diversi per gli schermi rettangolari e round usando il `app:rectLayout` e `app:roundLayout` attributi:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

La soluzione contiene layout diversi per ogni stile di visualizzazione che verrà selezionato in fase di esecuzione:

![File illustrati nella sezione risorse/layout](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

Anziché compilare layout diversi per ogni tipo di schermata, è possibile creare anche una singola visualizzazione che si adatta alle schermate arrotondate o rettangolare.

Ciò [esempio di Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) illustra come usare il `BoxInsetLayout` usare lo stesso layout su schermi rettangolari e arrotondati per.


## <a name="wear-ui-designer"></a>Finestra di progettazione dell'interfaccia utente, usare

Xamarin Android Designer supporta gli schermi rettangolari e arrotondati per:

![Selezionando lo schermo Android Wear quadrato in Xamarin Android Designer](screen-sizes-images/design-screen-type.png)

Area di progettazione in stile rettangolare è illustrato di seguito:

![Area di progettazione in stile rettangolare](screen-sizes-images/design-rect.png) 

Area di progettazione in stile di arrotondamento è illustrato di seguito:

![Area di progettazione in stile di arrotondamento](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Wear simulatore

Il **Gestione emulatori Google** contiene definizioni di dispositivo per entrambi i tipi di schermate. È possibile creare gli emulatori rettangolari e della funzione round per testare l'app.

![Wear definizioni di dispositivo visualizzate in Gestione emulatori Google](screen-sizes-images/emulator-devices.png)

L'emulatore verrà eseguito il rendering come segue per uno schermo rettangolo:

![Emulatore per il rendering di una schermata rettangolare](screen-sizes-images/recipe-2.png) 

Per una schermata di arrotondamento, esegue il rendering simile al seguente:

![Emulatore per il rendering di una schermata della funzione round](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Video

[Le app a schermo intero per Android Wear](https://www.youtube.com/watch?v=naf_WbtFAlY) dal [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

