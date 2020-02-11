---
title: Xamarin.Forms RefreshView
description: Xamarin.Forms RefreshView è un controllo contenitore che fornisce il pull per aggiornare la funzionalità per il contenuto scorrevole.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
ms.openlocfilehash: e38987006025dad1c2ff49c3ea8916e2075d61d7
ms.sourcegitcommit: d1d4700b3b1b417a9d7b7da85ab5d28f8e8e599d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73649300"
---
# <a name="xamarinforms-refreshview"></a>Xamarin.Forms RefreshView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

Il `RefreshView` è un controllo contenitore che fornisce il pull per aggiornare la funzionalità per il contenuto scorrevole. Pertanto, l'elemento figlio di un `RefreshView` deve essere un controllo scorrevole, ad esempio [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`CollectionView`](xref:Xamarin.Forms.CollectionView)o [`ListView`](xref:Xamarin.Forms.ListView).

`RefreshView` definisce le proprietà seguenti:

- `Command`, di tipo `ICommand`, che viene eseguito quando viene attivato un aggiornamento.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.
- `IsRefreshing`, di tipo `bool`, che indica lo stato corrente della `RefreshView`.
- `RefreshColor`, di tipo `Color`, il colore del cerchio dello stato di avanzamento visualizzato durante l'aggiornamento.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> Nella piattaforma UWP (Universal Windows Platform) è possibile impostare la direzione di pull di un `RefreshView` con una specifica della piattaforma. Per ulteriori informazioni, vedere [RefreshView pull direction](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-refreshview"></a>Creare un RefreshView

Nell'esempio seguente viene illustrato come creare un'istanza di un `RefreshView` in XAML:

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

Nel codice è anche possibile creare un `RefreshView`:

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

In questo esempio, il `RefreshView` fornisce il pull per aggiornare la funzionalità a una [`ScrollView`](xref:Xamarin.Forms.ScrollView) il cui figlio è un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout). Il `FlexLayout` utilizza un layout associabile per generare il contenuto mediante l'associazione a una raccolta di elementi e imposta l'aspetto di ogni elemento con una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Per altre informazioni sui layout associabili, vedere [layout associabili in Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

Il valore della proprietà `RefreshView.IsRefreshing` indica lo stato corrente del `RefreshView`. Quando un aggiornamento viene attivato dall'utente, questa proprietà passerà automaticamente a `true`. Una volta completato l'aggiornamento, è necessario reimpostare la proprietà su `false`.

Quando l'utente avvia un aggiornamento, viene eseguito il `ICommand` definito dalla proprietà `Command`, che dovrebbe aggiornare gli elementi visualizzati. Quando si verifica l'aggiornamento, viene visualizzata una visualizzazione di aggiornamento, che è costituita da un cerchio di avanzamento animato:

[![Screenshot di un RefreshView per l'aggiornamento dei dati, in iOS e Android](refreshview-images/default-progress-circle.png "RefreshView aggiornamento dei dati")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView aggiornamento dei dati")

> [!NOTE]
> Impostando manualmente la proprietà `IsRefreshing` su `true` verrà attivata la visualizzazione di aggiornamento ed eseguirà il `ICommand` definito dalla proprietà `Command`.

## <a name="refreshview-appearance"></a>Aspetto RefreshView

Oltre alle proprietà che `RefreshView` eredita dalla classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , `RefreshView` definisce anche la proprietà `RefreshColor`. Questa proprietà può essere impostata in modo da definire il colore del cerchio dello stato di avanzamento visualizzato durante l'aggiornamento:

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

Lo screenshot seguente mostra un `RefreshView` con il set di proprietà `RefreshColor`:

[![Screenshot di un RefreshView con un cerchio di avanzamento dell'alzavola, in iOS e Android](refreshview-images/teal-progress-circle.png "RefreshView con un cerchio di stato verde acqua")](refreshview-images/teal-progress-circle-large.png#lightbox "RefreshView con un cerchio di stato verde acqua")

Inoltre, la proprietà `BackgroundColor` può essere impostata su un [`Color`](xref:Xamarin.Forms.Color) che rappresenta il colore di sfondo del cerchio dello stato di avanzamento.

> [!NOTE]
> In iOS, la proprietà `BackgroundColor` imposta il colore di sfondo del `UIView` che contiene il cerchio dello stato di avanzamento.

## <a name="disable-a-refreshview"></a>Disabilitare un RefreshView

Un'applicazione può entrare in uno stato in cui il pull per l'aggiornamento non è un'operazione valida. In questi casi, è possibile disabilitare il `RefreshView` impostando la relativa proprietà `IsEnabled` su `false`. In questo modo si impedisce agli utenti di attivare il pull per l'aggiornamento.

In alternativa, quando si definisce la proprietà `Command`, è possibile specificare il delegato `CanExecute` del `ICommand` per abilitare o disabilitare il comando.

## <a name="related-links"></a>Collegamenti correlati

- [RefreshView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [Layout associabili in Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView pull direction-specifico della piattaforma](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
