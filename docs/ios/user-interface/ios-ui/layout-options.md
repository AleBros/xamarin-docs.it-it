---
title: Opzioni di layout
ms.topic: article
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 1e3139eb4c94264c91307f6f8a69b183f3bf7fa6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="layout-options"></a>Opzioni di layout

Sono disponibili due meccanismi diversi per controllare il layout quando una vista è stata ridimensionata o ruotata:

-  **Ridimensionamento automatico** : ridimensionamento automatico del controllo nella finestra di progettazione fornisce un modo per impostare il `AutoresizingMask` proprietà. Ciò consente un controllo di essere ancorato al bordo del relativo contenitore e/o correggere le dimensioni. Ridimensionamento automatico funziona in tutte le versioni di iOS. Questo aspetto viene descritto in dettaglio più avanti
-  **Layout automatico** – una funzionalità introdotta in iOS6 che consente un controllo accurato le relazioni di controlli dell'interfaccia utente. Consentirà di controllo delle posizioni all'interno di elementi relazione ad altri elementi nell'area di progettazione. Questo argomento viene descritto più dettagliatamente il [Layout automatico con Xamarin iOS progettazione](~/ios/user-interface/designer/designer-auto-layout.md) Guida.


## <a name="autosizing"></a>Ridimensionamento automatico

Quando un utente ridimensiona una finestra, ad esempio quando il dispositivo viene ruotato e le modifiche di orientamento, il sistema verrà ridimensionato automaticamente le visualizzazioni all'interno di tale finestra secondo le regole di ridimensionamento automatico. Queste regole possono essere impostate in c# tramite la `AutoresizingMask` proprietà del `UIView` o nel **proprietà riempimento** di iOS progettazione, come illustrato di seguito:

 [ ![](layout-options-images/image41.png "Visual Studio per Mac finestra di progettazione")](layout-options-images/image41.png)

Se un controllo è selezionato, consente di specificare manualmente il percorso e le dimensioni del controllo, nonché scegliere **ridimensionamento automatico** comportamento. Come illustrato nella schermata seguente, è possibile utilizzare il springs e un strutture nel controllo di ridimensionamento automatico per definire la relazione della vista selezionata padre:

 [ ![](layout-options-images/image42.png "Visual Studio per Mac finestra di progettazione")](layout-options-images/image42.png)

Modifica un *spring* causerà la visualizzazione ridimensionare in base alla larghezza o altezza della visualizzazione padre. Modifica un *strut* renderà la visualizzazione di mantenere una distanza costante tra l'elemento e della visualizzazione padre, per il bordo particolare.

Queste impostazioni possono anche essere impostata nel codice:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Per verificare le impostazioni di ridimensionamento automatico, abilitare diversi **orientamenti di dispositivi supportati** nelle opzioni del progetto:

 [ ![](layout-options-images/image43a.png "Impostazioni di ridimensionamento automatico")](layout-options-images/image43a.png)

Nel code-behind è possibile utilizzare il codice seguente, che fa sì che i controlli di testo da ridimensionare in orizzontale:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


È inoltre possibile regolare i controlli usando la finestra di progettazione. La selezione di strutture come illustrato di seguito provocherà l'immagine di rimanere allineato a destra senza essere troncata dalla parte inferiore della visualizzazione:

 [ ![](layout-options-images/autoresize.png "Rotazione automatica")](layout-options-images/autoresize.png)

Questi screenshot mostrano come i controlli ridimensionare o si riposizionano quando viene ruotata la schermata:

 [ ![](layout-options-images/image44a.png "Rotazione automatica")](layout-options-images/image44a.png)

Si noti che la visualizzazione di testo e il campo di testo sia adattata per mantenere lo stesso a sinistra e destra i margini, a causa di `FlexibleWidth` impostazione. L'immagine include il superiore e sinistro margine flessibile, il che significa che consente di preservare i margini inferiore e destro: mantenendo l'immagine visualizzata quando viene ruotata la schermata. Layout complessi richiedono in genere una combinazione di queste impostazioni in tutti i controlli visibili per mantenere la coerenza l'interfaccia utente e per impedire che i controlli sovrapposti quando i limiti della visualizzazione della modifica (a causa di rotazione o un altro evento ridimensionamento).





## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
