---
title: Opzioni di layout in Xamarin.iOS
description: In questo documento vengono descritti i diversi modi per disporre le interfacce utente in Xamarin.iOS. Viene illustrato il ridimensionamento automatico e il layout automatico.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 2514287fe06216d62b994cf19ba8f0901dd36c49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003318"
---
# <a name="layout-options-in-xamarinios"></a>Opzioni di layout in Xamarin.iOS

Esistono due meccanismi diversi per controllare il layout quando una visualizzazione viene ridimensionata o ruotata:

- **Ridimensionamento** automatico: il controllo di ridimensionamento automatico nella finestra di progettazione consente di impostare le proprietà del `AutoresizingMask`. In questo modo un controllo sarà ancorato ai bordi del contenitore e/o ne correggerà le dimensioni. Il dimensionamento automatico funziona in tutte le versioni di iOS. Questo aspetto viene descritto in dettaglio di seguito
- **Layout automatico** : funzionalità introdotta in iOS 6 che consente un controllo con granularità fine sulle relazioni dei controlli dell'interfaccia utente. Consente di controllare le posizioni degli elementi rispetto ad altri elementi nell'area di progettazione. Questo argomento viene trattato in modo più dettagliato nel [layout automatico con la Guida di Xamarin iOS designer](~/ios/user-interface/designer/designer-auto-layout.md) .

## <a name="autosizing"></a>Ridimensionamento automatico

Quando un utente ridimensiona una finestra, ad esempio quando il dispositivo viene ruotato e l'orientamento cambia, le visualizzazioni all'interno di tale finestra vengono ridimensionate automaticamente in base alle regole di ridimensionamento automatico. Queste regole possono essere impostate C# usando la proprietà`AutoresizingMask`del`UIView`o nel **riquadro delle proprietà** di iOS designer, come illustrato di seguito:

 [![](layout-options-images/image41.png "Visual Studio for Mac Designer")](layout-options-images/image41.png#lightbox)

Quando si seleziona un controllo, questo consente di specificare manualmente la posizione e le dimensioni del controllo, nonché di scegliere il comportamento di **ridimensionamento** automatico. Come illustrato nello screenshot seguente, è possibile usare le molle e i puntone nel controllo di ridimensionamento automatico per definire la relazione della visualizzazione selezionata con l'elemento padre:

 [![](layout-options-images/image42.png "Visual Studio for Mac Designer")](layout-options-images/image42.png#lightbox)

La regolazione di una *molla* provocherà il ridimensionamento della visualizzazione in base alla larghezza o all'altezza della visualizzazione padre. Se si modifica un *puntone* , la visualizzazione manterrà una distanza costante tra se stessa e la relativa visualizzazione padre, sul bordo specifico.

Queste impostazioni possono essere impostate anche nel codice:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

Per testare le impostazioni di ridimensionamento automatico, abilitare diversi **orientamenti dei dispositivi supportati** nelle opzioni del progetto:

 [![](layout-options-images/image43a.png "Autosizing Settings")](layout-options-images/image43a.png#lightbox)

Nel code-behind è possibile usare il codice seguente, che fa sì che i due controlli di testo vengano ridimensionati orizzontalmente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

È anche possibile modificare i controlli usando la finestra di progettazione. Se si selezionano i puntone come mostrato di seguito, l'immagine resterà allineata a destra senza essere ritagliata nella parte inferiore della visualizzazione:

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

Queste schermate mostrano il ridimensionamento o il riposizionamento dei controlli quando viene ruotata la schermata:

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

Si noti che la visualizzazione di testo e il campo di testo si estendono entrambi per mantenere gli stessi margini sinistro e destro, a causa dell'impostazione `FlexibleWidth`. L'immagine ha il margine superiore e sinistro flessibile, ovvero mantiene i margini inferiore e destro, mantenendo l'immagine in visualizzazione quando viene ruotata la schermata. I layout complessi richiedono in genere una combinazione di queste impostazioni in ogni controllo visibile per assicurare la coerenza dell'interfaccia utente e impedire la sovrapposizione dei controlli quando i limiti della visualizzazione cambiano (a causa della rotazione o di un altro evento di ridimensionamento).

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
