---
title: Uso delle dimensioni dello schermo in Novell. Android e uso del sistema operativo
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 86e05dc0e9cd5df325126cc5a339b36dd27c1e45
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030363"
---
# <a name="working-with-screen-sizes"></a>Utilizzo delle dimensioni dello schermo

I dispositivi Android Wear possono avere una visualizzazione rettangolare o rotonda, che può essere anche di dimensioni diverse.

![Schermate di visualizzazioni rettangolari e round Wear](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Tipo di schermata di identificazione

La libreria di supporto Wear fornisce alcuni controlli che consentono di rilevare e adattarsi a diverse forme dello schermo, ad esempio `WatchViewStub` e `BoxInsetLayout`.

Tenere presente che alcuni degli altri controlli della libreria di supporto (ad esempio `GridViewPager`) rilevano *automaticamente* la forma dello schermo e non devono essere aggiunti come elementi figlio dei controlli descritti di seguito.

### <a name="watchviewstub"></a>WatchViewStub

Vedere l'esempio [WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub) per vedere come rilevare il tipo di schermata e visualizzare un layout diverso per ogni tipo.

Il file di layout principale contiene un `android.support.wearable.view.WatchViewStub` che fa riferimento a layout diversi per schermi rettangolari e arrotondati usando gli attributi `app:rectLayout` e `app:roundLayout`:

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

![File visualizzati in risorse/layout](screen-sizes-images/solution.png)

### <a name="boxinsetlayout"></a>BoxInsetLayout

Anziché creare layout diversi per ogni tipo di schermata, è anche possibile creare una singola visualizzazione che si adatta a schermi rettangolari o rotondi.

Questo [esempio di Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) Mostra come usare il `BoxInsetLayout` per usare lo stesso layout sia negli schermi rettangolari che negli schermi rotondi.

## <a name="wear-ui-designer"></a>Indossare la finestra di progettazione interfaccia utente

Il Android Designer Novell supporta schermate sia rettangolari che rotonde:

![Selezione della schermata di Android Wear Square nel Novell Android Designer](screen-sizes-images/design-screen-type.png)

L'area di progettazione in stile rettangolare è illustrata di seguito:

![Area di progettazione con stile rettangolare](screen-sizes-images/design-rect.png) 

L'area di progettazione nello stile arrotondato è illustrata di seguito:

![Area di progettazione con stile arrotondato](screen-sizes-images/design-round.png)

## <a name="wear-simulator"></a>Simulatore di usura

**Gestione emulatori Google** contiene le definizioni dei dispositivi per entrambi i tipi di schermata. È possibile creare emulatori rettangolari e rotondi per testare l'app.

![Usare le definizioni del dispositivo visualizzate in Google Emulator Manager](screen-sizes-images/emulator-devices.png)

Il rendering dell'emulatore verrà eseguito come questo per una schermata rettangolare:

![Rendering emulatore di una schermata rettangolare](screen-sizes-images/recipe-2.png) 

Il rendering sarà simile al seguente per una schermata rotonda:

![Rendering emulatore di un round screen](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Video

[App fullscreen per Android Wear](https://www.youtube.com/watch?v=naf_WbtFAlY) da [Developers.Google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).
