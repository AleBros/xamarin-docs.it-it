---
title: Xamarin.FormsProgressBar
description: Xamarin.FormsProgressBar è un controllo che rappresenta visivamente lo stato di avanzamento come barra orizzontale riempita in base a una proprietà float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4ac6231c0483c0c44755c2ac9539f237dd64251
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136279"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.FormsProgressBar
[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Il Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) controllo rappresenta visivamente lo stato di avanzamento come barra orizzontale riempita con una percentuale rappresentata da un `float` valore. La `ProgressBar` classe eredita da [`View`](xref:Xamarin.Forms.View) .

Gli screenshot seguenti mostrano un `ProgressBar` in iOS e Android:

![Screenshot di ProgressBar in iOS e Android](progressbar-images/progressbars-default.png "ProgressBar in iOS e Android")

Il `ProgressBar` controllo definisce due proprietà:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)`float`valore che rappresenta lo stato di avanzamento corrente come valore compreso tra 0 e 1. `Progress`i valori minori di 0 verranno fissati a 0, i valori maggiori di 1 verranno fissati a 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)è un oggetto `Color` che influiscono sul colore della barra interna che rappresenta lo stato corrente.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che è `ProgressBar` possibile applicare uno stile a ed essere la destinazione delle associazioni dati.

Il `ProgressBar` controllo definisce anche un `ProgressTo` metodo che aggiunge un'animazione alla barra dal relativo valore corrente a un valore specificato. Per ulteriori informazioni, vedere [animare un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> Non `ProgressBar` accetta la manipolazione dell'utente, pertanto viene ignorato quando si usa il tasto TAB per selezionare i controlli.

## <a name="create-a-progressbar"></a>Creare una ProgressBar

`ProgressBar`È possibile creare un'istanza di un oggetto in XAML. La `Progress` proprietà determina la percentuale di riempimento della barra colorata interna. Il valore predefinito della `Progress` proprietà è 0. Nell'esempio seguente viene illustrato come creare un'istanza di `ProgressBar` in XAML con il `Progress` set di proprietà facoltativo:

```xaml
<ProgressBar Progress="0.5" />
```

Un oggetto `ProgressBar` può essere creato anche nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Non usare opzioni di layout orizzontale non vincolate, ad esempio `Center` , `Start` o `End` con `ProgressBar` . In UWP il `ProgressBar` valore viene compresso in una barra di larghezza zero. Mantieni il `HorizontalOptions` valore predefinito di `Fill` e non usare una larghezza di `Auto` quando si inserisce un oggetto `ProgressBar` in un `Grid` layout.

## <a name="progressbar-appearance-properties"></a>Proprietà dell'aspetto ProgressBar

La `ProgressColor` proprietà definisce il colore della barra interna quando la `Progress` proprietà è maggiore di zero. Nell'esempio seguente viene illustrato come creare un'istanza di `ProgressBar` in XAML con il `ProgressColor` set di proprietà:

```xaml
<ProgressBar ProgressColor="Orange" />
```

La `ProgressColor` proprietà può essere impostata anche durante la creazione `ProgressBar` di un nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Gli screenshot seguenti mostrano con la `ProgressBar` `ProgressColor` proprietà impostata `Color.Orange` su in iOS e Android:

![Screenshot di ProgressBar con stile in iOS e Android](progressbar-images/progressbars-styled.png "ProgressBar con stile in iOS e Android")

## <a name="animate-a-progressbar"></a>Animare un oggetto ProgressBar

Il `ProgressTo` metodo aggiunge un'animazione `ProgressBar` dal valore corrente `Progress` a un valore fornito nel tempo. Il metodo accetta un `float` valore di avanzamento, una `uint` durata in millisecondi, un `Easing` valore enum e restituisce un `Task<bool>` . Il codice seguente illustra come aggiungere un'animazione a `ProgressBar` :

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Per ulteriori informazioni sull' `Easing` enumerazione, vedere [funzioni di interpolazione in Xamarin.Forms ](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
