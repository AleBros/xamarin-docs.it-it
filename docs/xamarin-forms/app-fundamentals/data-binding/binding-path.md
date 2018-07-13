---
title: Percorso di associazione di xamarin. Forms
description: Questo articolo illustra come usare le associazioni di dati di xamarin. Forms per accedere alle proprietà secondarie e i membri della raccolta con la proprietà percorso della classe di associazione.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 887a20f1791a190c182e6d179cfabb46c6e0eb48
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998947"
---
# <a name="xamarinforms-binding-path"></a>Percorso di associazione di xamarin. Forms

In tutti gli esempi di data binding precedenti, il [ `Path` ](xref:Xamarin.Forms.Binding.Path) proprietà del `Binding` classe (o la [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) proprietà del `Binding` estensione di markup) è stata impostata per una singola proprietà. È possibile effettivamente impostare `Path` a un *sottoproprietà* (una proprietà di una proprietà), o a un membro di una raccolta.

Ad esempio, si supponga che la pagina contiene un `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

Il `Time` proprietà di `TimePicker` JE typu `TimeSpan`, ma si vogliono creare un'associazione di dati che fa riferimento il `TotalSeconds` proprietà di tale `TimeSpan` valore. Ecco il data binding:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

Il `Time` proprietà è di tipo `TimeSpan`, che ha un `TotalSeconds` proprietà. Il `Time` e `TotalSeconds` proprietà sono connessa con un punto. Gli elementi di `Path` stringa fanno sempre riferimento alle proprietà e non ai tipi di queste proprietà.

Esempio e altri ancora verranno mostrati nel **variazioni percorso** pagina:

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
                              StringFormat='The first Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

Nella seconda `Label`, l'origine del binding è la pagina stessa. Il `Content` proprietà è di tipo `StackLayout`, che dispone di un `Children` vlastnosti typu `IList<View>`, che ha un `Count` proprietà che indica il numero di elementi figlio.

## <a name="paths-with-indexers"></a>Percorsi con gli indicizzatori

L'associazione nel terzo `Label` nella **percorso variazioni** pagine riferimenti il [ `CultureInfo` ](xref:System.Globalization.CultureInfo) classe il `System.Globalization` dello spazio dei nomi:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

L'origine è impostato su statico `CultureInfo.CurrentCulture` proprietà, ovvero un oggetto di tipo `CultureInfo`. Che classe implementa una proprietà denominata `DateTimeFormat` typu [ `DateTimeFormatInfo` ](xref:System.Globalization.DateTimeFormatInfo) che contiene un `DayNames` raccolta. L'indice consente di selezionare il quarto elemento.

Il quarto `Label` esegue un'operazione simile ma per le impostazioni cultura associato Francia. Il `Source` dell'associazione è impostata su `CultureInfo` oggetto con un costruttore:

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

Visualizzare [passando gli argomenti del costruttore](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) per altri dettagli su come specificare gli argomenti del costruttore in XAML.

Infine, nell'ultimo esempio è simile al secondo, ad eccezione del fatto che fa riferimento a uno degli elementi figlio del `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Tale elemento figlio è un `Label`, che dispone di un `Text` vlastnosti typu `String`, che ha un `Length` proprietà. Il primo `Label` report il `TimeSpan` impostato `TimePicker`, pertanto quando viene modificato, che il testo finale `Label` anche le modifiche.

Ecco il programma in esecuzione in tutti e tre le piattaforme:

[![Percorso variazioni](binding-path-images/pathvariations-small.png "percorso variazioni")](binding-path-images/pathvariations-large.png#lightbox "variazioni di percorso")

## <a name="debugging-complex-paths"></a>Debug di tracciati complessi

Le definizioni di percorso complesse possono essere difficili da costruire: È necessario conoscere il tipo di ogni proprietà secondarie o il tipo di elementi nella raccolta aggiungere correttamente la proprietà secondaria Avanti, ma non vengono visualizzati degli stessi tipi di nel percorso. Una buona tecnica consiste nel compilare il percorso in modo incrementale ed esaminare i risultati intermedi. Ultimo esempio, è possibile iniziare senza alcun `Path` definizione affatto:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Che visualizza il tipo di origine del binding, o `DataBindingDemos.PathVariationsPage`. Si conosce `PathVariationsPage` deriva da `ContentPage`, pertanto un `Content` proprietà:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

Il tipo dei `Content` proprietà viene ora visualizzata sia `Xamarin.Forms.StackLayout`. Aggiungere il `Children` proprietà per il `Path` e il tipo è `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, ovvero una classe interna per xamarin. Forms, ma ovviamente un tipo di raccolta. Aggiungere un indice che e il tipo è `Xamarin.Forms.Label`. Continua in questo modo.

Poiché xamarin. Forms ha elaborato il percorso di associazione, viene installato un `PropertyChanged` gestore su qualsiasi oggetto nel percorso che implementa il `INotifyPropertyChanged` interfaccia. Ad esempio, l'associazione finale reagisce a una modifica nel primo `Label` perché il `Text` le modifiche alle proprietà.

Se una proprietà nel percorso di associazione non implementa `INotifyPropertyChanged`, eventuali modifiche a tale proprietà verranno ignorate. Alcune modifiche è stato interamente invalidare il percorso di associazione, è necessario utilizzare questa tecnica solo quando la stringa di proprietà e sottoproprietà mai diventano non validi.



## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
