---
title: Opzioni di layout in xamarin. IOS
description: Questo documento descrive diversi modi per definire il layout interfacce utente in xamarin. IOS. Viene descritto il Layout automatico e ridimensionamento automatico.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b35149028763691c17fe526673d023cc9b707c28
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61246062"
---
# <a name="layout-options-in-xamarinios"></a>Opzioni di layout in xamarin. IOS

Esistono due diversi meccanismi per controllare il layout quando una visualizzazione viene ridimensionata o ruotata:

-  **Ridimensionamento automatico** – il ridimensionamento automatico controllo nella finestra di progettazione fornisce un modo per impostare il `AutoresizingMask` proprietà. In questo modo un controllo essere agganciato ai bordi del relativo contenitore e/o eliminare le relative dimensioni. Ridimensionamento automatico funziona in tutte le versioni di iOS. Come descritto in dettaglio più avanti
-  **Layout automatico** – una funzionalità introdotta in iOS 6 che consente un controllo con granularità fine sulle relazioni di controlli dell'interfaccia utente. Consentirne il controllo delle posizioni degli elementi relazione ad altri elementi nell'area di progettazione. In questo argomento viene descritta più dettagliatamente la [Layout automatico con Xamarin iOS Designer](~/ios/user-interface/designer/designer-auto-layout.md) Guida.

## <a name="autosizing"></a>Ridimensionamento automatico

Quando un utente ridimensiona una finestra, ad esempio quando il dispositivo viene ruotato e le modifiche di orientamento, il sistema viene ridimensionato automaticamente le visualizzazioni all'interno di tale finestra secondo le regole di ridimensionamento automatico. Queste regole possono essere impostate nella C# utilizzando il `AutoresizingMask` proprietà del `UIView` o nel **riquadro delle proprietà** di iOS Designer, come illustrato di seguito:

 [![](layout-options-images/image41.png "Visual Studio per Mac Designer")](layout-options-images/image41.png#lightbox)

Quando un controllo è selezionato, in questo modo è possibile specificare manualmente il percorso e le dimensioni del controllo, oltre a scelta **ridimensionamento automatico** comportamento. Come illustrato nello screenshot seguente, è possibile usare il springs e strutture del controllo di ridimensionamento automatico per definire relazioni della vista selezionata per il relativo elemento padre:

 [![](layout-options-images/image42.png "Visual Studio per Mac Designer")](layout-options-images/image42.png#lightbox)

Regolazione un' *primavera* causerà la visualizzazione di ridimensionare in base alla larghezza o all'altezza della visualizzazione padre. Regolazione un' *strut* renderà la visualizzazione di mantenere una distanza costante tra se stesso e la relativa visualizzazione padre, tale bordo particolare.

Queste impostazioni possono essere impostate anche nella finestra di codice:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Per verificare le impostazioni di ridimensionamento automatico, attivare diversi **orientamenti dispositivo supportati** nelle opzioni del progetto:

 [![](layout-options-images/image43a.png "Impostazioni di ridimensionamento automatico")](layout-options-images/image43a.png#lightbox)

Code-behind è possibile usare il codice seguente, che fa sì che i controlli due text ridimensionare in senso orizzontale:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


È anche possibile modificare i controlli usando la finestra di progettazione. Se si seleziona il struts come anomalo sotto provocherà l'immagine rimanere allineato a destra senza essere troncata dal margine inferiore della visualizzazione:

 [![](layout-options-images/autoresize.png "Rotazione automatica")](layout-options-images/autoresize.png#lightbox)

Questi screenshot mostrano come i controlli ridimensionano o riposizionano stessi quando viene ruotata la schermata:

 [![](layout-options-images/image44a.png "Rotazione automatica")](layout-options-images/image44a.png#lightbox)

Si noti che la visualizzazione di testo e un campo di testo sia adattata per mantenere lo stesso a sinistra e destra i margini, a causa dell'errore di `FlexibleWidth` impostazione. L'immagine include il superiore e sinistro margine flessibile, il che significa che mantiene i margini inferiore e destro – mantenendo l'immagine visualizzata quando viene ruotata la schermata. Layout complessi richiedono in genere una combinazione di queste impostazioni in tutti i controlli visibili per mantenere l'interfaccia utente coerente e per impedire la sovrapposizione quando cambiano i limiti della visualizzazione (a causa della rotazione o altri eventi di ridimensionamento) i controlli.





## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
