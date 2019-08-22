---
title: Novell. Android RatingBar
description: Come aggiungere un widget RatingBar a un'attività Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: de63a0f3f6564671a50594c66b55ed095329c95c
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887629"
---
# <a name="xamarinandroid-ratingbar"></a>Novell. Android RatingBar

Un RatingBar è un widget dell'interfaccia utente che visualizza una valutazione da una a cinque stelle. L'utente può selezionare una classificazione mediante taping su una stella in questa sezione. verrà creato un widget che consente all'utente di fornire una classificazione con il [`RatingBar`](xref:Android.Widget.RatingBar) widget.

![Esempio di RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Creazione di un RatingBar

1. Aprire il file **Resource/layout/Main. aXML** e aggiungere il[`RatingBar`](xref:Android.Widget.RatingBar)
   elemento (all'interno [`LinearLayout`](xref:Android.Widget.LinearLayout)di):

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   L' `android:numStars` attributo definisce il numero di stelle da visualizzare per la barra di valutazione. L' `android:stepSize` attributo definisce la granularità per ogni stella (ad esempio, un valore di `0.5` può consentire le classificazioni di mezza stella).

2. Per eseguire un'operazione quando è stata impostata una nuova classificazione, aggiungere il codice seguente alla fine del[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   Metodo

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Acquisisce il [`RatingBar`](xref:Android.Widget.RatingBar) widget dal layout con [`FindViewById`](xref:Android.App.Activity.FindViewById*) e quindi imposta un metodo di evento, quindi definisce l'azione da eseguire quando l'utente imposta una classificazione. In questo caso, un messaggio [`Toast`](xref:Android.Widget.Toast) semplice Visualizza la nuova classificazione.

3. Eseguire l'applicazione.

