---
title: RatingBar
description: Come aggiungere un widget RatingBar a un'attività Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 97d2a126be70e210d2e8f4ebf4d7a25ff8777a02
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131430"
---
# <a name="ratingbar"></a>RatingBar

Un RatingBar è un widget dell'interfaccia utente che consente di visualizzare una classificazione da uno a cinque stelle. L'utente può selezionare una classificazione uguale a toccando su una stella In questa sezione, si creerà un widget che consente all'utente di specificare una classificazione uguale a, con la [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) widget.

![Esempio di un RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Creazione di un RatingBar

1. Aprire il **Resource/layout/Main.axml** file e aggiungere il [`RatingBar`](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)
   elemento (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   Il `android:numStars` attributo definisce il numero di stelle per visualizzare per la barra di classificazione. Il `android:stepSize` attributo definisce la granularità per ogni star (ad esempio, il valore `0.5` consentirebbe le classificazioni di mezza stella).

2. Per eseguire un'operazione quando è stato impostato un nuovo controllo rating, aggiungere il codice seguente alla fine del [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
   metodo:

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Ciò consente di acquisire il [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) widget dal layout con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/) e imposta un metodo dell'evento, quindi definisce l'azione da eseguire quando l'utente imposta una classificazione. In questo caso, un semplice [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio consente di visualizzare il nuovo controllo rating.

3.  Eseguire l'applicazione.

