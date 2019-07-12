---
title: Indicatore dell'attività in xamarin. Forms
description: Il controllo ActivityIndicator indica agli utenti che l'applicazione è impegnata in un'attività di lunga durata, senza fornire alcuna indicazione dello stato di avanzamento. Questo articolo illustra come usare un ActivityIndicator in XAML e codice.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: abd8150e3aa4ec347c8d956004993340630208bf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837061"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms ActivityIndicator
[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)

Xamarin. Forms[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) è un controllo che visualizza un'animazione per mostrare che l'applicazione è impegnata in un'attività di lunga durata. A differenza di [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), il `ActivityIndicator` fornisce alcuna indicazione dello stato di avanzamento. Il `ActivityIndicator` eredita da [ `View` ](xref:Xamarin.Forms.View).

La schermata seguente mostra un `ActivityIndicator` controllo in iOS e Android:

![Schermata di ActivityIndicator in iOS e Android](activityindicator-images/activityindicators-default.png "Screenshot di ActivityIndicator in iOS e Android")

Il `ActivityIndicator` controllo definisce le proprietà seguenti:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) è un `bool` valore che indica se il `ActivityIndicator` deve essere visibile e animazione o nascosto. Quando il valore è `false` il `ActivityIndicator` non saranno visibili.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) è un `Color` che definisce il colore di visualizzazione del valore di `ActivityIndicator`.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che il `ActivityIndicator` possono essere personalizzati con stili e può essere la destinazione di associazioni dati.

## <a name="create-an-activityindicator"></a>Creare un ActivityIndicator

Un `ActivityIndicator` può essere inizializzata in XAML. Relativo `IsRunning` proprietà può essere impostata per determinare se il controllo è visibile e animazione. Se il `IsRunning` non è impostata, per impostazione predefinita `false` e il `ActivityIndicator` non saranno visibili. Nell'esempio seguente viene illustrato come creare un'istanza di un `ActivityIndicator` in XAML con l'opzione facoltativa `IsRunning` set di proprietà:

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` possono anche essere creati nel codice:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Proprietà dell'aspetto ActivityIndicator

Il `Color` proprietà può essere impostata per definire il `ActivityIndicator` colore. Nell'esempio seguente viene illustrato come creare un'istanza di un `ActivityIndicator` in XAML con il `Color` set di proprietà:

```xaml
<ActivityIndicator Color="Orange" />
```

Il `Color` proprietà può essere impostata anche durante la creazione di un `ActivityIndicator` nel codice:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

La schermata seguente mostra le `ActivityIndicator` con il `Color` impostata su `Color.Orange` in iOS e Android:

![Screenshot della con stile ActivityIndicator in iOS e Android](activityindicator-images/activityindicators-styled.png "Screenshot di ActivityIndicator con stile in iOS e Android")

## <a name="related-links"></a>Collegamenti correlati

* [Demo ActivityIndicator](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
