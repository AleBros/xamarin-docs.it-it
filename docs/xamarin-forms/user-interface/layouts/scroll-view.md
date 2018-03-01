---
title: ScrollView
description: "Utilizzare ScrollView layout che non è un solo schermo e includere contenuto liberare spazio per la tastiera."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2016
ms.openlocfilehash: c305075d37a25bfe828f16d4e69955437a591f9a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="scrollview"></a>ScrollView

[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) include layout e consente loro di scorrimento fuori schermo. `ScrollView` viene usata anche per consentire le visualizzazioni spostare automaticamente per la parte visibile dello schermo quando viene visualizzato sulla tastiera.

[ ![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png "Xamarin.Forms Layouts")

Questo articolo descrive:

- **[Scopo](#Purpose)**  &ndash; allo scopo di `ScrollView` e quando viene usato.
- **[Utilizzo](#Usage)**  &ndash; illustrato come utilizzare `ScrollView` in pratica.
- **[Proprietà](#Properties)**  &ndash; proprietà pubbliche che possono essere letti e modificate.
- **[Metodi](#Methods)**  &ndash; metodi pubblici che possono essere chiamati per scorrere la visualizzazione.
- **[Eventi](#Events)**  &ndash; gli eventi che possono essere usati per ascoltare le modifiche degli stati della vista.

## <a name="purpose"></a>Scopo

`ScrollView` può essere utilizzato per garantire che più grande nelle visualizzazioni anche su telefoni più piccoli. Ad esempio, un layout che funziona in un iPhone 6s potrebbe essere ritagliato in un iPhone 4s. Utilizzando un `ScrollView` limiterebbe le porzioni del layout da visualizzare sullo schermo più piccolo.

## <a name="usage"></a>Utilizzo

> [!NOTE]
> **Nota**: `ScrollView`s non devono essere nidificate. Inoltre, `ScrollView`s non devono essere nidificate con altri controlli che forniscono lo scorrimento, ad esempio `ListView` e `WebView`.

`ScrollView` espone un `Content` proprietà che può essere impostata su una singola visualizzazione o layout. Si consideri l'esempio di un layout con un boxView molto grandi, seguito da un `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

In C#:

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,   HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Prima che l'utente scorre verso il basso, solo il `BoxView` visibile:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Si noti che quando l'utente inizia a immettere testo nel `Entry`, consente di scorrere la visualizzazione per mantenere visibile sullo schermo:

![](scroll-view-images/scroll-end.png "Voce ScrollView")

## <a name="properties"></a>Proprietà

Elemento ScrollView dispone delle proprietà seguenti:

- **Contenuto** &ndash; Ottiene o imposta la visualizzazione per visualizzare il `ScrollView`.
- **[ContentSize](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)**  &ndash; sola lettura, ottiene le dimensioni del contenuto, che dispone di un componente di larghezza e altezza. Si tratta di una proprietà associabile
- **[Orientamento](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)**  &ndash; si tratta di un [ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/), che è un'enumerazione che può essere impostata su `Horizontal`, `Vertical`, o `Both`.
- **ScrollX** &ndash; sola lettura, ottiene la posizione di scorrimento corrente nella dimensione X.
- **ScrollY** &ndash; sola lettura, ottiene la posizione di scorrimento corrente nella dimensione Y.

## <a name="methods"></a>Metodi

`ScrollView` fornisce un `ScrollToAsync` metodo, che può essere utilizzato per scorrere la visualizzazione utilizzando coordinate oppure specificare una visualizzazione specifica che deve essere reso visibile.

Quando si utilizzano le coordinate, specificare il `x` e `y` coordinate, insieme a un valore booleano che indica se le barre di scorrimento deve essere aggiunta un'animazione:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Durante lo scorrimento a un particolare elemento, il `ScrollToPosition` enumerazione specifica in cui verrà visualizzato l'elemento nella visualizzazione:

- **Center** &ndash; scorre l'elemento al centro della parte visibile della vista.
- **Fine** &ndash; scorre l'elemento alla fine della parte visibile della vista.
- **MakeVisible** &ndash; scorre l'elemento in modo che sia visibile all'interno della visualizzazione.
- **Avviare** &ndash; scorre l'elemento all'inizio della parte visibile della vista.

Il `IsAnimated` proprietà consente di specificare come verrà scorso la visualizzazione. Quando impostato su true, un'animazione smooth verrà utilizzata, anziché immediatamente lo spostamento del contenuto nella visualizzazione.

## <a name="events"></a>Eventi

`ScrollView` espone un solo evento, `Scrolled`. `Scrolled` viene generato quando la vista ha completato lo scorrimento. Il gestore eventi per `Scrolled` accetta `ScrolledEventArgs`, che presenta il `ScrollX` e `ScrollY` proprietà. Nell'esempio viene illustrato come aggiornare un'etichetta con la posizione di scorrimento corrente un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Si noti che posizioni di scorrimento possono essere negative, a causa l'effetto di rimbalzo durante lo scorrimento alla fine di un elenco.


## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
