---
title: Xamarin.FormsRefreshView
description: Xamarin.FormsRefreshView è un controllo contenitore che fornisce il pull per aggiornare la funzionalità per il contenuto scorrevole.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d84e6bb6ed41f2fbc213cd15051d071521f588cd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127595"
---
# <a name="xamarinforms-refreshview"></a>Xamarin.FormsRefreshView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

`RefreshView`È un controllo contenitore che fornisce il pull per aggiornare la funzionalità per il contenuto scorrevole. Pertanto, l'elemento figlio di un `RefreshView` deve essere un controllo scorrevole, ad esempio [`ScrollView`](xref:Xamarin.Forms.ScrollView) , [`CollectionView`](xref:Xamarin.Forms.CollectionView) o [`ListView`](xref:Xamarin.Forms.ListView) .

`RefreshView` definisce le proprietà seguenti:

- `Command`, di tipo `ICommand` , che viene eseguito quando viene attivato un aggiornamento.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.
- `IsRefreshing`, di tipo `bool` , che indica lo stato corrente dell'oggetto `RefreshView` .
- `RefreshColor`, di tipo `Color` , il colore del cerchio dello stato di avanzamento visualizzato durante l'aggiornamento.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> Nella piattaforma UWP (Universal Windows Platform) è possibile impostare la direzione di pull di un oggetto `RefreshView` con un oggetto specifico della piattaforma. Per ulteriori informazioni, vedere [RefreshView pull direction](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-refreshview"></a>Creare un RefreshView

Nell'esempio seguente viene illustrato come creare un'istanza di `RefreshView` in XAML:

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

Un oggetto `RefreshView` può essere creato anche nel codice:

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

In questo esempio, fornisce l'oggetto `RefreshView` pull per aggiornare la funzionalità a un oggetto il [`ScrollView`](xref:Xamarin.Forms.ScrollView) cui figlio è un oggetto [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) . `FlexLayout`Usa un layout associabile per generare il contenuto mediante l'associazione a una raccolta di elementi e imposta l'aspetto di ogni elemento con un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Per ulteriori informazioni sui layout associabili, vedere [layout associabili in Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

Il valore della `RefreshView.IsRefreshing` proprietà indica lo stato corrente dell'oggetto `RefreshView` . Quando un aggiornamento viene attivato dall'utente, questa proprietà passerà automaticamente a `true` . Una volta completato l'aggiornamento, è necessario reimpostare la proprietà su `false` .

Quando l'utente avvia un aggiornamento, `ICommand` viene eseguito il definito dalla `Command` proprietà, che dovrebbe aggiornare gli elementi visualizzati. Quando si verifica l'aggiornamento, viene visualizzata una visualizzazione di aggiornamento, che è costituita da un cerchio di avanzamento animato:

[![Screenshot di un RefreshView per l'aggiornamento dei dati, in iOS e Android](refreshview-images/default-progress-circle.png "RefreshView aggiornamento dei dati")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView aggiornamento dei dati")

> [!NOTE]
> Se si imposta manualmente la `IsRefreshing` proprietà su `true` , verrà attivata la visualizzazione di aggiornamento e verrà eseguito il `ICommand` definito dalla `Command` Proprietà.

## <a name="refreshview-appearance"></a>Aspetto RefreshView

Oltre alle proprietà che `RefreshView` ereditano dalla [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, `RefreshView` definisce anche la `RefreshColor` Proprietà. Questa proprietà può essere impostata in modo da definire il colore del cerchio dello stato di avanzamento visualizzato durante l'aggiornamento:

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

La schermata seguente mostra un oggetto `RefreshView` con la `RefreshColor` proprietà impostata:

[![Screenshot di un RefreshView con un cerchio di avanzamento dell'alzavola, in iOS e Android](refreshview-images/teal-progress-circle.png "RefreshView con un cerchio di stato verde acqua")](refreshview-images/teal-progress-circle-large.png#lightbox "RefreshView con un cerchio di stato verde acqua")

Inoltre, la `BackgroundColor` proprietà può essere impostata su un oggetto [`Color`](xref:Xamarin.Forms.Color) che rappresenta il colore di sfondo del cerchio dello stato di avanzamento.

> [!NOTE]
> In iOS, la `BackgroundColor` proprietà imposta il colore di sfondo dell'oggetto `UIView` che contiene il cerchio dello stato di avanzamento.

## <a name="disable-a-refreshview"></a>Disabilitare un RefreshView

Un'applicazione può entrare in uno stato in cui il pull per l'aggiornamento non è un'operazione valida. In questi casi, `RefreshView` può essere disabilitato impostando la relativa `IsEnabled` proprietà su `false` . In questo modo si impedisce agli utenti di attivare il pull per l'aggiornamento.

In alternativa, quando si definisce la `Command` proprietà, `CanExecute` `ICommand` è possibile specificare il delegato di per abilitare o disabilitare il comando.

## <a name="related-links"></a>Collegamenti correlati

- [RefreshView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [Layout associabili inXamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView pull direction-specifico della piattaforma](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
