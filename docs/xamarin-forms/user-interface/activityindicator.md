---
title: "Activity Indicator in Xamarin.Forms " Description: "il controllo ActivityIndicator indica agli utenti che l'applicazione è impegnata in un'attività di lunga durata, senza fornire alcuna indicazione dello stato di avanzamento. Questo articolo illustra come usare un ActivityIndicator in XAML e nel codice ".
ms. prod: Novell MS. AssetID: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261 ms. Technology: Novell-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 07/10/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-activityindicator"></a>Xamarin.FormsActivityIndicator
[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Il Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) controllo Visualizza un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata. Diversamente da [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , l'oggetto `ActivityIndicator` non indica lo stato di avanzamento. `ActivityIndicator`Eredita da [`View`](xref:Xamarin.Forms.View) .

Gli screenshot seguenti mostrano un `ActivityIndicator` controllo su iOS e Android:

![Screenshot di ActivityIndicator in iOS e Android](activityindicator-images/activityindicators-default.png "Screenshot di ActivityIndicator in iOS e Android")

Il `ActivityIndicator` controllo definisce le proprietà seguenti:

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)`Color`valore che definisce il colore di visualizzazione dell'oggetto `ActivityIndicator` .
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)`bool`valore che indica se `ActivityIndicator` deve essere visibile, animato o nascosto. Quando il valore è `false` `ActivityIndicator` , non è visibile.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che è `ActivityIndicator` possibile applicare uno stile a ed essere la destinazione delle associazioni dati.

## <a name="create-an-activityindicator"></a>Creare un ActivityIndicator

`ActivityIndicator`È possibile creare un'istanza della classe in XAML. La `IsRunning` proprietà determina se il controllo è visibile e animato. `IsRunning`Per impostazione predefinita, la proprietà viene impostata su `false` . Nell'esempio seguente viene illustrato come creare un'istanza di `ActivityIndicator` in XAML con il `IsRunning` set di proprietà facoltativo:

```xaml
<ActivityIndicator IsRunning="true" />
```

Un oggetto `ActivityIndicator` può essere creato anche nel codice:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Proprietà aspetto ActivityIndicator

La `Color` proprietà definisce il `ActivityIndicator` colore. Nell'esempio seguente viene illustrato come creare un'istanza di `ActivityIndicator` in XAML con il `Color` set di proprietà:

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` proprietà può essere impostata anche durante la creazione `ActivityIndicator` di un nel codice:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

Gli screenshot seguenti mostrano con la `ActivityIndicator` `Color` proprietà impostata `Color.Orange` su in iOS e Android:

![Screenshot di ActivityIndicator con stile in iOS e Android](activityindicator-images/activityindicators-styled.png "Screenshot di ActivityIndicator con stile in iOS e Android")

## <a name="related-links"></a>Collegamenti correlati

* [Demo di ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
