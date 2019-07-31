---
title: Xamarin. Forms ScrollView
description: Questo articolo illustra come usare la classe ScrollView xamarin. Forms per presentare i layout che non può essere contenuta in una sola schermata e ai quali è contenuto liberare spazio per la tastiera.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 79f3f88dba37899bcb9467fcd6d1bd7e9dc5eb10
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657156"
---
# <a name="xamarinforms-scrollview"></a>Xamarin. Forms ScrollView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) include layout e consente loro di scorrimento fuori schermo. `ScrollView` viene inoltre utilizzato per consentire le visualizzazioni spostare automaticamente per la parte visibile dello schermo quando viene visualizzata la tastiera.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

Questo articolo illustra le attività seguenti:

- **[Utilizzo generico](#purpose)**  &ndash; allo scopo di `ScrollView` e quando viene usato.
- **[Utilizzo](#usage)**  &ndash; illustrato come utilizzare `ScrollView` in pratica.
- **[Le proprietà](#properties)**  &ndash; proprietà pubbliche che può essere letto e modificata.
- **[I metodi](#methods)**  &ndash; metodi pubblici che possono essere chiamati per scorrere la visualizzazione.
- **[Gli eventi](#events)**  &ndash; eventi che possono essere utilizzati per ascoltare le modifiche dello stato della visualizzazione.

## <a name="purpose"></a>Scopo

`ScrollView` può essere utilizzato per garantire che più grande consente di visualizzare anche su telefoni più piccoli. Ad esempio, un layout che funziona in un iPhone 6s potrebbe essere ritagliato in un iPhone 4s. Usando un `ScrollView` consentirebbe le porzioni del layout per essere visualizzata su schermi più piccoli.

## <a name="usage"></a>Utilizzo

> [!NOTE]
> `ScrollView`s non devono essere nidificate. È inoltre `ScrollView`s non deve essere annidato con altri controlli che consentono lo scorrimento, ad esempio `ListView` e `WebView`.

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
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Prima che l'utente scorre verso il basso, solo il `BoxView` è visibile:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Si noti che quando l'utente inizia a immettere testo nel `Entry`, scorre la visualizzazione per mantenere visibile sullo schermo:

![](scroll-view-images/scroll-end.png "Voce in ScrollView")

## <a name="properties"></a>Proprietà

`ScrollView` definisce le proprietà seguenti:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Ottiene un [ `Size` ](xref:Xamarin.Forms.Size) valore che rappresenta le dimensioni del contenuto.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Ottiene o imposta una [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) valore di enumerazione che rappresenta la direzione di scorrimento di `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Ottiene un `double` che rappresenta l'oggetto corrente X posizione di scorrimento.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Ottiene un `double` che rappresenta la posizione di scorrimento Y corrente.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Ottiene o imposta una [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valore che rappresenta quando la barra di scorrimento orizzontale è visibile.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Ottiene o imposta una [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valore che rappresenta quando la barra di scorrimento verticale è visibile.

## <a name="methods"></a>Metodi

`ScrollView` fornisce un `ScrollToAsync` metodo, che può essere utilizzato per scorrere la visualizzazione utilizzando coordinate o specificando una visualizzazione specifica che deve essere reso visibile.

Quando si utilizzano le coordinate, specificare il `x` e `y` coordinate, insieme a un valore booleano che indica se lo scorrimento dovrebbe essere animato:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Durante lo scorrimento a un particolare elemento, il `ScrollToPosition` enumerazione specifica dove verrà visualizzato l'elemento nella visualizzazione:

- **Centro** &ndash; scorre l'elemento al centro della parte visibile della visualizzazione.
- **Estremità** &ndash; scorre l'elemento alla fine della parte visibile della visualizzazione.
- **MakeVisible** &ndash; scorre l'elemento in modo che sia visibile all'interno della visualizzazione.
- **Avviare** &ndash; scorre l'elemento all'inizio della parte visibile della visualizzazione.

Il `IsAnimated` proprietà specifica la modalità di visualizzazione avverrà. Quando impostato su true, un'animazione fluida viene utilizzata, invece di immediatamente lo spostamento del contenuto nella visualizzazione.

## <a name="events"></a>Eventi

`ScrollView` definisce un solo evento, `Scrolled`. `Scrolled` viene generato quando la vista ha completato lo scorrimento. Il gestore eventi per `Scrolled` accetta `ScrolledEventArgs`, che ha il `ScrollX` e `ScrollY` proprietà. Nell'esempio viene illustrato come aggiornare un'etichetta con la posizione di scorrimento corrente di un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Si noti che potrebbero essere un valore negativo, a causa l'effetto di rimbalzo durante lo scorrimento alla fine di un elenco di posizioni di scorrimento.


## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Esempio BusinessTumble (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
