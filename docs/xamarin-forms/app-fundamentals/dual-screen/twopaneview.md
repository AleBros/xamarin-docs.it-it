---
title: Controllo TwoPaneView di Xamarin.Forms
description: Questa guida illustra come usare il controllo TwoPaneView di Xamarin.Forms per ottimizzare l'esperienza delle app per i dispositivi con doppio schermo, ad esempio Surface Duo e Surface Neo.
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1992a714774dba79e42c82e71af0bfe6aed7feb7
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145505"
---
# <a name="xamarinforms-twopaneview"></a>Controllo TwoPaneView di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

Rappresenta un contenitore con due visualizzazioni che consente di ridimensionare e posizionare il contenuto nello spazio disponibile, affiancandolo orizzontalmente o verticalmente. Il controllo TwoPaneView eredita da Grid, quindi è più semplice pensare a queste proprietà come se fossero applicate a una griglia.

## <a name="set-up-twopaneview"></a>Configurare il controllo TwoPaneView

La proprietà `Source` di `TwoPaneView` può accettare un URI o un percorso di file locale. La riproduzione inizierà immediatamente all'apertura dell'elemento multimediale.

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>Informazioni sulle modalità di TwoPaneView

Solo una di queste modalità può essere attiva

- `SinglePane`: è attualmente visibile un solo riquadro
- `Wide`: i due riquadri sono disposti orizzontalmente. Un riquadro si trova a sinistra e l'altro a destra. Con due schermi, questa è la modalità usata quando il dispositivo viene usato con orientamento verticale
- `Tall`: i due riquadri sono disposti verticalmente. Un riquadro si trova nella parte superiore e l'altro in basso. Con due schermi, questa è la modalità usata quando il dispositivo viene usato con orientamento orizzontale

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>Controllo TwoPaneView con un solo schermo

Le proprietà seguenti sono rilevanti solo quando il controllo TwoPaneView occupa un solo schermo. Se il controllo TwoPaneView è esteso su due schermi, queste proprietà non hanno alcun effetto.

- `MinTallModeHeight` indica l'altezza minima del controllo per passare alla modalità Tall
- `MinWideModeWidth` indica la larghezza minima del controllo per passare alla modalità Wide
- `Pane1Length` imposta la larghezza di Pane1 in modalità Wide, l'altezza di Pane1 in modalità Tall e non ha effetto in modalità SinglePane
- `Pane2Length` imposta la larghezza di Pane2 in modalità Wide, l'altezza di Pane2 in modalità Tall e non ha effetto in modalità SinglePane

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>Proprietà che si applicano per l'uso con uno o due schermi

- `TallModeConfiguration`: in modalità Tall questa proprietà indica la disposizione sinistra/destra o se si vuole rendere visibile un solo riquadro come definito da TwoPaneViewPriority
- `WideModeConfiguration`: in modalità Tall questa proprietà indica la disposizione alto/basso o se si vuole rendere visibile un solo riquadro come definito da TwoPaneViewPriority
- `PanePriority` indica se visualizzare Pane1 o Pane2 se in modalità SinglePane

## <a name="related-links"></a>Collegamenti correlati

- [DualScreen (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
