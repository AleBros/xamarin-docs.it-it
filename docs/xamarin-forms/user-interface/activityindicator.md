---
title: Indicatore attività in Novell. Forms
description: Il controllo ActivityIndicator indica agli utenti che l'applicazione è impegnata in un'attività di lunga durata, senza fornire indicazioni sullo stato di avanzamento. Questo articolo illustra come usare un ActivityIndicator in XAML e codice.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: e13a46e1022f4e33ace6f9f19bb5cea5d1ac784b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739157"
---
# <a name="xamarinforms-activityindicator"></a>Novell. Forms ActivityIndicator
[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Novell. Forms[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) è un controllo che visualizza un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata. Diversamente da [`ProgressBar`](xref:Xamarin.Forms.ProgressBar), l'oggetto `ActivityIndicator` non indica lo stato di avanzamento. `ActivityIndicator` Eredita [da`View`](xref:Xamarin.Forms.View).

Lo screenshot seguente mostra un `ActivityIndicator` controllo su iOS e Android:

![Screenshot di ActivityIndicator in iOS e Android](activityindicator-images/activityindicators-default.png "Screenshot di ActivityIndicator in iOS e Android")

Il `ActivityIndicator` controllo definisce le proprietà seguenti:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)valore che indica `ActivityIndicator` se deve essere visibile, animato o nascosto. `bool` Quando il valore è `false` `ActivityIndicator` , non sarà visibile.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)valore che definisce il colore di visualizzazione dell'oggetto `ActivityIndicator`. `Color`

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il `ActivityIndicator` che significa che è possibile applicare uno stile a ed essere la destinazione delle associazioni dati.

## <a name="create-an-activityindicator"></a>Creare un ActivityIndicator

È `ActivityIndicator` possibile creare un'istanza di un oggetto in XAML. La `IsRunning` proprietà può essere impostata in modo da determinare se il controllo è visibile e animato. Se la `IsRunning` proprietà non è impostata, il valore predefinito `false` è e `ActivityIndicator` il non sarà visibile. Nell'esempio seguente viene illustrato come creare un' `ActivityIndicator` istanza di in XAML con il set di proprietà facoltativo: `IsRunning`

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` oggetto può essere creato anche nel codice:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Proprietà aspetto ActivityIndicator

La `Color` proprietà può essere impostata in modo da `ActivityIndicator` definire il colore. Nell'esempio seguente viene illustrato come creare un' `ActivityIndicator` istanza di in XAML con il `Color` set di proprietà:

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` proprietà può essere impostata anche durante la creazione `ActivityIndicator` di un nel codice:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

La schermata seguente mostra l' `ActivityIndicator` oggetto con `Color` la proprietà impostata `Color.Orange` su in iOS e Android:

![Screenshot di ActivityIndicator con stile in iOS e Android](activityindicator-images/activityindicators-styled.png "Screenshot di ActivityIndicator con stile in iOS e Android")

## <a name="related-links"></a>Collegamenti correlati

* [Demo di ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
