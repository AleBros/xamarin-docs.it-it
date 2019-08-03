---
title: ProgressBar di Novell. Forms
description: Il ProgressBar Novell. Forms è un controllo che rappresenta visivamente lo stato di avanzamento come barra orizzontale riempita in base a una proprietà float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 78c5f38428e20a2e0c6a15d0964f8fd505a8d082
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739407"
---
# <a name="xamarinforms-progressbar"></a>ProgressBar di Novell. Forms
[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Novell. Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) è un controllo che rappresenta visivamente lo stato di avanzamento come barra orizzontale riempita a una percentuale rappresentata `float` da un valore. La `ProgressBar` classe eredita da [`View`](xref:Xamarin.Forms.View).

Lo screenshot seguente mostra un `ProgressBar` in iOS e Android:

![Screenshot di ProgressBar in iOS e Android](progressbar-images/progressbars-default.png "ProgressBar in iOS e Android")

Il `ProgressBar` controllo definisce due proprietà:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)`float` valore che rappresenta lo stato di avanzamento corrente come valore compreso tra 0 e 1. `Progress`i valori minori di 0 verranno fissati a 0, i valori maggiori di 1 verranno fissati a 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)è un `Color` oggetto che influiscono sul colore della barra interna che rappresenta lo stato corrente.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il `ProgressBar` che significa che è possibile applicare uno stile a ed essere la destinazione delle associazioni dati.

Il `ProgressBar` controllo definisce anche un `ProgressTo` metodo che aggiunge un'animazione alla barra dal relativo valore corrente a un valore specificato. Per ulteriori informazioni, vedere [animare un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> Non `ProgressBar` accetta la manipolazione dell'utente, pertanto viene ignorato quando si usa il tasto TAB per selezionare i controlli.

## <a name="create-a-progressbar"></a>Creare una ProgressBar

È `ProgressBar` possibile creare un'istanza di un oggetto in XAML. La `Progress` proprietà può essere impostata in modo da determinare la percentuale di riempimento della barra colorata interna. Se la `Progress` proprietà non è impostata, il valore predefinito è 0. Nell'esempio seguente viene illustrato come creare un'istanza `ProgressBar` di in XAML con il `Progress` set di proprietà facoltativo:

```xaml
<ProgressBar Progress="0.5" />
```

Un `ProgressBar` oggetto può essere creato anche nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Non usare opzioni di layout `Center`orizzontale non vincolate, ad esempio, `Start`o `End` con `ProgressBar`. In UWP il `ProgressBar` valore viene compresso in una barra di larghezza zero. Mantieni il valore `HorizontalOptions` predefinito di `Fill` e non usare una larghezza di `Auto` quando si inserisce `ProgressBar` un oggetto `Grid` in un layout.

## <a name="progressbar-appearance-properties"></a>Proprietà dell'aspetto ProgressBar

La `ProgressColor` proprietà può essere impostata in modo da definire il colore della barra `Progress` interna quando la proprietà è maggiore di zero. Nell'esempio seguente viene illustrato come creare un'istanza `ProgressBar` di in XAML con `ProgressColor` il set di proprietà:

```xaml
<ProgressBar OnColor="Orange" />
```

La `ProgressColor` proprietà può essere impostata anche durante la creazione `ProgressBar` di un nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

La schermata seguente mostra l' `ProgressBar` oggetto con `ProgressColor` la proprietà impostata `Color.Orange` su in iOS e Android:

![Screenshot di ProgressBar con stile in iOS e Android](progressbar-images/progressbars-styled.png "ProgressBar con stile in iOS e Android")

## <a name="animate-a-progressbar"></a>Animare un oggetto ProgressBar

Il `ProgressTo` metodo aggiunge un'animazione `ProgressBar` dal valore corrente `Progress` a un valore fornito nel tempo. Il metodo accetta un `float` valore di avanzamento, `uint` una durata in millisecondi, `Easing` un valore enum e restituisce `Task<bool>`un. Il codice seguente illustra come aggiungere un'animazione a `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Per ulteriori informazioni sull' `Easing` enumerazione, vedere interpolazione [di funzioni in Novell. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
