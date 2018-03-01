---
title: Percorso di associazione
description: "Utilizzare le associazioni di dati di sottoproprietà di accesso e i membri della raccolta"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 39d326714a6fee1abe242a7256888647784cdec3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="binding-path"></a>Percorso di associazione

In tutti gli esempi di associazione di dati precedenti, il [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) proprietà del `Binding` classe (o [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/) proprietà del `Binding` estensione di markup) è stato impostato per una singola proprietà. È effettivamente possibile impostare `Path` per un *delle sottoproprietà* (una proprietà di una proprietà), o a un membro di una raccolta.

Ad esempio, si supponga che la pagina contiene un `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

Il `Time` proprietà di `TimePicker` è di tipo `TimeSpan`, ma forse si desidera creare un'associazione di dati che fa riferimento il `TotalSeconds` proprietà di tale `TimeSpan` valore. Di seguito è l'associazione di dati:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```
         
Il `Time` proprietà è di tipo `TimeSpan`, che presenta un `TotalSeconds` proprietà. Il `Time` e `TotalSeconds` proprietà sono simply connessa con un punto. Gli elementi di `Path` stringa sempre fare riferimento alle proprietà e non i tipi di queste proprietà.

Esempio e molti altri verranno mostrati nel **percorso variazioni** pagina:

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

Nella seconda `Label`, l'origine di associazione è la pagina stessa. Il `Content` proprietà è di tipo `StackLayout`, che presenta un `Children` proprietà di tipo `IList<View>`, che presenta un `Count` proprietà che indica il numero di elementi figlio.

## <a name="paths-with-indexers"></a>Percorsi con gli indicizzatori

L'associazione nel terzo `Label` nel **variazioni percorso** pagine riferimenti il [ `CultureInfo` ](https://developer.xamarin.com/api/type/System.Globalization.CultureInfo/) classe il `System.Globalization` dello spazio dei nomi:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

L'origine è impostata su statica `CultureInfo.CurrentCulture` proprietà, che è un oggetto di tipo `CultureInfo`. Che classe definisce una proprietà denominata `DateTimeFormat` di tipo [ `DateTimeFormatInfo` ](https://developer.xamarin.com/api/type/System.Globalization.DateTimeFormatInfo/) che contiene un `DayNames` insieme. L'indice consente di selezionare il quarto elemento.

Il quarto `Label` è un'operazione simile, ma per le impostazioni cultura associate Francia. Il `Source` dell'associazione è impostata su `CultureInfo` oggetto con un costruttore:

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

Vedere [passando gli argomenti del costruttore](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) per ulteriori informazioni su come specificare gli argomenti del costruttore in XAML.

Infine, nell'ultimo esempio è simile al secondo, ad eccezione del fatto che fa riferimento a uno degli elementi figlio del `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Tale elemento figlio è un `Label`, che presenta un `Text` proprietà di tipo `String`, che presenta un `Length` proprietà. Il primo `Label` report il `TimeSpan` impostato `TimePicker`, pertanto quando viene modificato il testo, finale `Label` anche le modifiche.

Di seguito è riportato il programma in esecuzione in tutti e tre le piattaforme:

[![Percorso variazioni](binding-path-images/pathvariations-small.png "variazioni percorso")](binding-path-images/pathvariations-large.png "variazioni di percorso")

## <a name="debugging-complex-paths"></a>Debug percorsi complessi

Le definizioni di percorso complesse possono essere difficili da costruire: È necessario conoscere il tipo di ogni proprietà secondaria o il tipo di elementi nella raccolta per aggiungere correttamente la proprietà secondaria successiva, ma degli stessi tipi non vengono visualizzati nel percorso. Una buona tecnica consiste nel compilare costituisce il percorso in modo incrementale ed esaminare i risultati intermedi. Ad esempio ultimo che è possibile iniziare senza alcun `Path` definizione affatto:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Che visualizza il tipo di origine di associazione, o `DataBindingDemos.PathVariationsPage`. Si è certi `PathVariationsPage` deriva da `ContentPage`, ha un `Content` proprietà:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

Il tipo di `Content` proprietà viene ora visualizzata da `Xamarin.Forms.StackLayout`. Aggiungere il `Children` proprietà per il `Path` e il tipo è `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, ovvero una classe interna per xamarin. Forms, ma ovviamente un tipo di raccolta. Aggiungere un indice che e il tipo è `Xamarin.Forms.Label`. In questo modo, continuare.

Come xamarin. Forms elabora il percorso di associazione, viene installato un `PropertyChanged` gestore su qualsiasi oggetto nel percorso che implementa il `INotifyPropertyChanged` interfaccia. Ad esempio, l'associazione finale risponde a una modifica nel primo `Label` perché il `Text` le modifiche alle proprietà. 

Se una proprietà nel percorso di associazione non implementa `INotifyPropertyChanged`, qualsiasi modifica a tale proprietà verrà ignorata. Alcune modifiche potrebbero completamente invalidare il percorso di associazione, è necessario utilizzare questa tecnica solo quando la stringa di proprietà e sottoproprietà non diventano non validi.



## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dalla Rubrica di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
