---
title: ProgressBar di Xamarin.Forms
description: Il ProgressBar Xamarin.Forms è un controllo che rappresenta visivamente lo stato di avanzamento come barra orizzontale riempita in base a una proprietà float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 40f3c9a5af29d1782249775b9f3166698eb6221a
ms.sourcegitcommit: 7acff5c5a03a0351962c05beebfc347503d83fe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73201938"
---
# <a name="xamarinforms-progressbar"></a>ProgressBar di Xamarin.Forms
[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Il controllo [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) Xamarin.Forms rappresenta visivamente lo stato di avanzamento come barra orizzontale riempita a una percentuale rappresentata da un valore `float`. La classe `ProgressBar` eredita da [`View`](xref:Xamarin.Forms.View).

Gli screenshot seguenti mostrano un `ProgressBar` in iOS e Android:

![Screenshot di ProgressBar in iOS e Android](progressbar-images/progressbars-default.png "ProgressBar in iOS e Android")

Il controllo `ProgressBar` definisce due proprietà:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) è un valore `float` che rappresenta lo stato di avanzamento corrente come valore compreso tra 0 e 1. `Progress` valori minori di 0 verranno fissati a 0, i valori maggiori di 1 verranno fissati a 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) è un `Color` che influisca sul colore della barra interna che rappresenta lo stato corrente.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che è possibile applicare uno stile ai `ProgressBar` ed essere la destinazione delle associazioni dati.

Il controllo `ProgressBar` definisce anche un metodo `ProgressTo` che aggiunge un'animazione alla barra dal valore corrente a un valore specificato. Per ulteriori informazioni, vedere [animare un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> Il `ProgressBar` non accetta la manipolazione dell'utente in modo che venga ignorato quando si usa il tasto TAB per selezionare i controlli.

## <a name="create-a-progressbar"></a>Creare una ProgressBar

È possibile creare un'istanza di un `ProgressBar` in XAML. La relativa proprietà `Progress` determina la percentuale di riempimento della barra colorata interna. Il valore predefinito della proprietà `Progress` è 0. Nell'esempio seguente viene illustrato come creare un'istanza di un `ProgressBar` in XAML con il set di proprietà `Progress` facoltativo:

```xaml
<ProgressBar Progress="0.5" />
```

Nel codice è anche possibile creare un `ProgressBar`:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Non usare opzioni di layout orizzontale non vincolate, ad esempio `Center`, `Start`o `End` con `ProgressBar`. In UWP il `ProgressBar` viene compresso in una barra di larghezza zero. Mantieni il valore predefinito di `HorizontalOptions` `Fill` e non usare una larghezza di `Auto` quando si inserisce un `ProgressBar` in un layout di `Grid`.

## <a name="progressbar-appearance-properties"></a>Proprietà dell'aspetto ProgressBar

La proprietà `ProgressColor` definisce il colore della barra interna quando la proprietà `Progress` è maggiore di zero. Nell'esempio seguente viene illustrato come creare un'istanza di un `ProgressBar` in XAML con il set di proprietà `ProgressColor`:

```xaml
<ProgressBar ProgressColor="Orange" />
```

È inoltre possibile impostare la proprietà `ProgressColor` quando si crea una `ProgressBar` nel codice:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Gli screenshot seguenti mostrano la `ProgressBar` con la proprietà `ProgressColor` impostata su `Color.Orange` in iOS e Android:

![Screenshot di ProgressBar con stile in iOS e Android](progressbar-images/progressbars-styled.png "ProgressBar con stile in iOS e Android")

## <a name="animate-a-progressbar"></a>Animare un oggetto ProgressBar

Il metodo `ProgressTo` aggiunge un'animazione al `ProgressBar` dal valore di `Progress` corrente a un valore fornito nel tempo. Il metodo accetta un valore `float` Progress, una `uint` durata in millisecondi, un `Easing` valore enum e restituisce un `Task<bool>`. Il codice seguente illustra come aggiungere un'animazione a un `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Per ulteriori informazioni sull'enumerazione `Easing`, vedere [interpolazione di funzioni in Xamarin.Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
