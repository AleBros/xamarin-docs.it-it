---
title: Xamarin.Android RatingBar
description: Come aggiungere un widget RatingBar a un'attività Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 529fecb4e24e83ef7b783815843e132347d99262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029150"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin.Android RatingBar

Un RatingBar è un widget dell'interfaccia utente che visualizza una valutazione da una a cinque stelle. L'utente può selezionare una classificazione mediante taping su una stella in questa sezione. verrà creato un widget che consente all'utente di fornire una classificazione, con il widget [`RatingBar`](xref:Android.Widget.RatingBar) .

![Esempio di RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="creating-a-ratingbar"></a>Creazione di un RatingBar

1. Aprire il file **Resource/layout/Main. aXML** e aggiungere il [`RatingBar`](xref:Android.Widget.RatingBar)
   elemento (all'interno del [`LinearLayout`](xref:Android.Widget.LinearLayout)):

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   L'attributo `android:numStars` definisce il numero di stelle da visualizzare per la barra di valutazione. L'attributo `android:stepSize` definisce la granularità per ogni stella (ad esempio, un valore di `0.5` consentirebbe classificazioni di mezza stella).

2. Per eseguire un'operazione quando è stata impostata una nuova classificazione, aggiungere il codice seguente alla fine del [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   Metodo

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Questa operazione acquisisce il widget [`RatingBar`](xref:Android.Widget.RatingBar) dal layout con [`FindViewById`](xref:Android.App.Activity.FindViewById*) e quindi imposta un metodo di evento, quindi definisce l'azione da eseguire quando l'utente imposta una classificazione. In questo caso, un messaggio di [`Toast`](xref:Android.Widget.Toast) semplice Visualizza la nuova classificazione.

3. Eseguire l'applicazione.
