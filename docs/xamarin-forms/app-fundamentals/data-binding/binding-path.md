---
title: Percorso di binding di Xamarin.Forms
description: Questo articolo illustra come usare i data binding di Xamarin.Forms per accedere alle proprietà secondarie e ai membri delle raccolte con la proprietà Path della classe Binding.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: ca27b0ba0f9e434809250a78047f3bd503f80b50
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771650"
---
# <a name="xamarinforms-binding-path"></a>Percorso di binding di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

In tutti gli esempi di data binding precedenti, la proprietà [`Path`](xref:Xamarin.Forms.Binding.Path) della classe `Binding` (o la proprietà [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) dell'estensione di markup `Binding`) è stata impostata su una singola proprietà. È in effetti possibile impostare `Path` su una *proprietà secondaria* (una proprietà di una proprietà) o su un membro di una raccolta.

Ad esempio, si supponga che la pagina contenga un controllo `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

La proprietà `Time` di `TimePicker` è di tipo `TimeSpan`, ma si potrebbe voler creare un data binding che fa riferimento alla proprietà `TotalSeconds` di tale valore `TimeSpan`. Ecco il data binding:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

La proprietà `Time` è di tipo `TimeSpan`, che ha una proprietà `TotalSeconds`. Le proprietà `Time` e `TotalSeconds` sono semplicemente collegate tramite un punto. Gli elementi nella stringa `Path` fanno sempre riferimento alle proprietà e non ai tipi di queste proprietà.

Questo esempio e vari altri sono disponibili nella pagina **Path Variations**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

Nella seconda `Label`, l'origine del binding è la pagina stessa. La proprietà `Content` è di tipo `StackLayout`, che ha una proprietà `Children` di tipo `IList<View>`, che ha una proprietà `Count` che indica il numero di elementi figlio.

## <a name="paths-with-indexers"></a>Percorsi con indicizzatori

Il binding nella terza `Label` nella pagina **Path Variations** fa riferimento alla classe [`CultureInfo`](xref:System.Globalization.CultureInfo) nello spazio dei nomi `System.Globalization`:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

L'origine è impostata sulla proprietà statica `CultureInfo.CurrentCulture`, ovvero un oggetto di tipo `CultureInfo`. Tale classe definisce una proprietà denominata `DateTimeFormat` di tipo [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) che contiene una raccolta `DayNames`. L'indice seleziona il quarto elemento.

La quarta `Label` esegue un'operazione simile ma per le impostazioni cultura associato alla Francia. La proprietà `Source` del binding viene impostata sull'oggetto `CultureInfo` con un costruttore:

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

Vedere [Passaggio degli argomenti del costruttore](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) per altri dettagli su come specificare gli argomenti del costruttore in XAML.

Infine, l'ultimo esempio è simile al secondo, ad eccezione del fatto che fa riferimento a uno degli elementi figlio di `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Tale elemento figlio è una `Label`, con una proprietà `Text` di tipo `String`, con una proprietà `Length`. La prima `Label` indica il valore `TimeSpan` impostato in `TimePicker`, pertanto quando cambia il testo, cambia anche la `Label` finale.

Ecco il programma in esecuzione:

[![Path Variations](binding-path-images/pathvariations-small.png "Path Variations")](binding-path-images/pathvariations-large.png#lightbox "Path Variations")

## <a name="debugging-complex-paths"></a>Debug di percorsi complessi

Le definizioni di percorso complesse possono essere difficili da costruire. È necessario conoscere il tipo di ogni proprietà secondaria o il tipo di elementi nella raccolta per aggiungere correttamente la proprietà secondaria successiva, ma i tipi stessi non compaiono nel percorso. Una buona tecnica consiste nel costruire il percorso in modo incrementale ed esaminare i risultati intermedi. Per l'ultimo esempio si potrebbe iniziare senza alcuna definizione di `Path`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Questo codice visualizza il tipo di origine del binding, o `DataBindingDemos.PathVariationsPage`. Si sa che `PathVariationsPage` deriva da `ContentPage`, quindi ha una proprietà `Content`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

Il tipo della proprietà `Content` è ora noto ed è `Xamarin.Forms.StackLayout`. Aggiungere la proprietà `Children` a `Path` e il tipo è `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, ovvero una classe interna di Xamarin.Forms, ma ovviamente un tipo raccolta. Aggiungere un indice e il tipo è `Xamarin.Forms.Label`. Continuare in questo modo.

Dato che Xamarin.Forms elabora il percorso di binding, installa un gestore `PropertyChanged` per qualsiasi oggetto nel percorso che implementa l'interfaccia `INotifyPropertyChanged`. Ad esempio, il binding finale reagisce a una modifica nella prima `Label` perché cambia la proprietà `Text`.

Se una proprietà nel percorso di binding non implementa `INotifyPropertyChanged`, eventuali modifiche a tale proprietà verranno ignorate. Alcune modifiche potrebbero invalidare del tutto il percorso di binding, quindi è consigliabile usare questa tecnica solo quando la stringa di proprietà e proprietà secondarie non diventa mai non valida.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Capitolo sul data binding della documentazione di Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
