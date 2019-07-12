---
title: Xamarin. Forms ProgressBar
description: La ProgressBar di xamarin. Forms è un controllo che rappresenta graficamente lo stato di avanzamento come una barra orizzontale che viene riempita basata su una proprietà di float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 2e2917077575ebc78dbdda8ae55aa230a39a7ba1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837011"
---
# <a name="xamarinforms-progressbar"></a>Xamarin. Forms ProgressBar
[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)

Xamarin. Forms [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) è un controllo che rappresenta graficamente lo stato di avanzamento come una barra orizzontale che viene riempita a percentuali rappresentate da un `float` valore. Il `ProgressBar` classe eredita da [ `View` ](xref:Xamarin.Forms.View).

La schermata seguente mostra un `ProgressBar` in iOS e Android:

![Screenshot della ProgressBar in iOS e Android](progressbar-images/progressbars-default.png "ProgressBar in iOS e Android")

Il `ProgressBar` controllo definisce due proprietà:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) è un `float` valore che rappresenta lo stato di avanzamento corrente come valore compreso tra 0 a 1. `Progress` i valori minori di 0 verrà impostato su 0, i valori maggiori di 1 verrà impostato su 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) è un `Color` che interessa l'interno della barra di colore che rappresenta lo stato di avanzamento corrente.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che il `ProgressBar` possono essere personalizzati con stili e può essere la destinazione di associazioni dati.

Il `ProgressBar` controllo definisce anche un `ProgressTo` metodo che anima la barra rispetto al valore corrente a un valore specificato. Per altre informazioni, vedere [animare una ProgressBar](#animate-a-progressbar).

> [!NOTE]
> Il `ProgressBar` non accetta la modifica dell'utente in modo che l'istruzione viene ignorata quando si usa il tasto Tab per selezionare i controlli.

## <a name="create-a-progressbar"></a>Creare un ProgressBar

Oggetto `ProgressBar` può essere inizializzata in XAML. Relativo `Progress` proprietà può essere impostata per determinare la percentuale di riempimento della barra colorata, interna. Se il `Progress` proprietà non è impostata, assume il valore su 0. Nell'esempio seguente viene illustrato come creare un'istanza di un `ProgressBar` in XAML con l'opzione facoltativa `Progress` set di proprietà:

```xaml
<ProgressBar Progress="0.5" />
```

Oggetto `ProgressBar` possono anche essere creati nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Non usare opzioni di layout orizzontale non vincolato, ad esempio `Center`, `Start`, o `End` con `ProgressBar`. Nella piattaforma UWP, il `ProgressBar` comprime in una barra di larghezza zero. Mantenere il valore predefinito `HorizontalOptions` valore del `Fill` e non usare una larghezza pari `Auto` quando si inserisce un `ProgressBar` in un `Grid` layout.

## <a name="progressbar-appearance-properties"></a>Proprietà dell'aspetto ProgressBar

Il `ProgressColor` proprietà può essere impostata per definire la barra interna colore quando i `Progress` proprietà è maggiore di zero. Nell'esempio seguente viene illustrato come creare un'istanza di un `ProgressBar` in XAML con il `ProgressColor` set di proprietà:

```xaml
<ProgressBar OnColor="Orange" />
```

Il `ProgressColor` proprietà può essere impostata anche durante la creazione di un `ProgressBar` nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

La schermata seguente mostra le `ProgressBar` con il `ProgressColor` impostata su `Color.Orange` in iOS e Android:

![Screenshot della ProgressBar con stile in iOS e Android](progressbar-images/progressbars-styled.png "nello stile ProgressBar in iOS e Android")

## <a name="animate-a-progressbar"></a>Animare un ProgressBar

Il `ProgressTo` metodo anima la `ProgressBar` dalla relativa posizione corrente `Progress` valore su un valore fornito nel corso del tempo. Il metodo accetta un `float` avanzamento valore, una `uint` durata in millisecondi, un `Easing` valore di enumerazione e restituisce un `Task<bool>`. Il codice seguente illustra come animare un `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Per altre informazioni sul `Easing` enumerazione, vedere [funzioni di interpolazione in xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo di ProgressBar](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)
