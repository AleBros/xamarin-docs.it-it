---
title: MultiBinding Novell. Forms
description: Questo articolo illustra come associare una raccolta di oggetti di associazione a una singola proprietà di destinazione del binding usando la classe MultiBinding.
ms.prod: xamarin
ms.assetid: E73AE622-664C-4A90-B5B2-BD47D0E7A1A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2020
ms.openlocfilehash: dfe6da8a76b447bf0c2a6c0a3bea9823e498d5e4
ms.sourcegitcommit: 8a18471b3d96f3f726b66f9bc50a829f1c122f29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988192"
---
# <a name="xamarinforms-multi-bindings"></a>MultiBinding Novell. Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Le associazioni MultiBinding offrono la possibilità di associare una raccolta di [`Binding`](xref:Xamarin.Forms.Binding) oggetti a una singola proprietà di destinazione del binding. Vengono creati con la `MultiBinding` classe, che valuta tutti i relativi `Binding` oggetti e restituisce un singolo valore tramite un' `IMultiValueConverter` istanza fornita dall'applicazione. Inoltre, `MultiBinding` Rivaluta tutti i relativi `Binding` oggetti quando uno dei dati associati viene modificato.

La `MultiBinding` classe definisce le proprietà seguenti:

- `Bindings`, di tipo `IList<BindingBase>` , che rappresenta la raccolta di [`Binding`](xref:Xamarin.Forms.Binding) oggetti all'interno dell' `MultiBinding` istanza.
- `Converter`, di tipo `IMultiValueConverter` , che rappresenta il convertitore da usare per convertire i valori di origine da o verso il valore di destinazione.
- `ConverterParameter`, di tipo `object` , che rappresenta un parametro facoltativo da passare a `Converter` .

La `Bindings` proprietà è la proprietà Content della `MultiBinding` classe e pertanto non deve essere impostata in modo esplicito da XAML.

Inoltre, la `MultiBinding` classe eredita le proprietà seguenti dalla `BindingBase` classe:

- `FallbackValue`, di tipo `object` , che rappresenta il valore da utilizzare quando il MultiBinding non è in grado di restituire un valore.
- `Mode`, di tipo [`BindingMode`](xref:Xamarin.Forms.BindingMode) , che indica la direzione del flusso di dati del MultiBinding.
- `StringFormat`, di tipo `string` , che specifica come formattare il risultato del MultiBinding se viene visualizzato come stringa.
- `TargetNullValue`, di tipo `object` , che rappresenta il valore usato nell'oggetto di destinazione Wen, il valore dell'origine è `null` .

Un oggetto `MultiBinding` deve usare un oggetto `IMultiValueConverter` per produrre un valore per la destinazione del binding, in base al valore delle associazioni nella `Bindings` raccolta. Ad esempio, un può [`Color`](xref:Xamarin.Forms.Color) essere calcolato da valori rossi, blu e verdi, che possono essere valori dello stesso o di oggetti di origine di associazione diversi. Quando un valore passa dalla destinazione alle origini, il valore della proprietà di destinazione viene convertito in un set di valori che vengono inseriti nelle associazioni.

> [!IMPORTANT]
> I singoli binding della `Bindings` raccolta possono avere convertitori di valori propri.

Il valore della `Mode` proprietà determina la funzionalità di `MultiBinding` e viene usato come modalità di associazione per tutte le associazioni nella raccolta, a meno che una singola associazione non esegua l'override della proprietà. Se, ad esempio, la `Mode` proprietà di un `MultiBinding` oggetto è impostata su [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , vengono considerate tutte le associazioni della raccolta, a meno che non `TwoWay` si imposti in modo esplicito un valore diverso `Mode` in una delle associazioni.

## <a name="define-a-imultivalueconverter"></a>Definire un IMultiValueConverter

L' `IMultiValueConverter` interfaccia consente di applicare la logica personalizzata a un `MultiBinding` . Per associare un convertitore a un `MultiBinding` , creare una classe che implementi l' `IValueConverter` interfaccia, quindi implementare i `Convert` `ConvertBack` metodi e:

```csharp
public class AllTrueMultiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
    {
        if (values == null || !targetType.IsAssignableFrom(typeof(bool)))
        {
            return false;
            // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
        }

        foreach (var value in values)
        {
            if (!(value is bool b))
            {
                return false;
                // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
            }
            else if (!b)
            {
                return false;
            }
        }
        return true;
    }

    public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
    {
        if (!(value is bool b) || targetTypes.Any(t => !t.IsAssignableFrom(typeof(bool))))
        {
            // Return null to indicate conversion back is not possible
            return null;
        }

        if (b)
        {
            return targetTypes.Select(t => (object)true).ToArray();
        }
        else
        {
            // Can't convert back from false because of ambiguity
            return null;
        }
    }
}
```

Il `Convert` metodo converte i valori di origine in un valore per la destinazione del binding. Novell. Forms chiama questo metodo quando propaga i valori dai binding di origine alla destinazione del binding. Questo metodo accetta quattro argomenti:

- `values`, di tipo `object[]` , è una matrice di valori generati dalle associazioni di origine in `MultiBinding` .
- `targetType`, di tipo `Type` , è il tipo della proprietà di destinazione del binding.
- `parameter`, di tipo `object` , è il parametro del convertitore da usare.
- `culture`, di tipo `CultureInfo` , rappresenta le impostazioni cultura da utilizzare nel convertitore.

Il `Convert` metodo restituisce un oggetto `object` che rappresenta un valore convertito. Questo metodo deve restituire:

- `BindableProperty.UnsetValue`per indicare che il convertitore non ha generato un valore e che l'associazione utilizzerà `FallbackValue` .
- `Binding.DoNothing`per indicare a Novell. Forms di non eseguire alcuna azione. Ad esempio, per indicare a Novell. Forms di non trasferire un valore nella destinazione del binding o di non usare `FallbackValue` .
- `null`per indicare che il convertitore non è in grado di eseguire la conversione e che l'associazione utilizzerà `TargetNullValue` .

> [!IMPORTANT]
> Un oggetto `MultiBinding` che riceve `BindableProperty.UnsetValue` da un `Convert` metodo deve definire la relativa [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) Proprietà. Analogamente, un oggetto `MultiBinding` che riceve `null` da un `Convert` metodo deve definire il relativo [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) propety.

Il `ConvertBack` metodo converte una destinazione di associazione nei valori dell'associazione di origine. Questo metodo accetta quattro argomenti:

- `value`, di tipo `object` , è il valore prodotto dalla destinazione del binding.
- `targetTypes`, di tipo `Type[]` , è la matrice di tipi in cui eseguire la conversione. La lunghezza della matrice indica il numero e i tipi di valori proposti per la restituzione da parte del metodo.
- `parameter`, di tipo `object` , è il parametro del convertitore da usare.
- `culture`, di tipo `CultureInfo` , rappresenta le impostazioni cultura da utilizzare nel convertitore.

Il `ConvertBack` metodo restituisce una matrice di valori, di tipo `object[]` , che sono stati convertiti dal valore di destinazione ai valori di origine. Questo metodo deve restituire:

- `BindableProperty.UnsetValue`alla posizione `i` per indicare che il convertitore non è in grado di fornire un valore per l'associazione di origine in corrispondenza dell'indice `i` e che non deve essere impostato alcun valore.
- `Binding.DoNothing`alla posizione `i` per indicare che non è necessario impostare alcun valore sull'associazione di origine in corrispondenza dell'indice `i` .
- `null`per indicare che il convertitore non è in grado di eseguire la conversione o che non supporta la conversione in questa direzione.

## <a name="consume-a-imultivalueconverter"></a>Utilizzare un IMultiValueConverter

Un oggetto `IMultiValueConverter` viene utilizzato creando un'istanza in un dizionario risorse e quindi facendovi riferimento utilizzando l' `StaticResource` estensione di markup per impostare la `MultiBinding.Converter` proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MultiBindingConverterPage"
             Title="MultiBinding Converter demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AllTrueConverter}">
                <Binding Path="Employee.IsOver16" />
                <Binding Path="Employee.HasPassedTest" />
                <Binding Path="Employee.IsSuspended"
                         Converter="{StaticResource InverterConverter}" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>    
```

In questo esempio, l' `MultiBinding` oggetto utilizza l' `AllTrueMultiConverter` istanza per impostare la [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) proprietà su `true` , purché i tre [`Binding`](xref:Xamarin.Forms.Binding) oggetti restituiscono `true` . In caso contrario, la `CheckBox.IsChecked` proprietà viene impostata su `false` .

Per impostazione predefinita, la [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) Proprietà usa un' [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) associazione. Pertanto, il `ConvertBack` metodo dell' `AllTrueMultiConverter` istanza viene eseguito quando l'oggetto [`CheckBox`](xref:Xamarin.Forms.CheckBox) è deselezionato dall'utente, che imposta i valori di associazione di origine sul valore della `CheckBox.IsChecked` Proprietà.

## <a name="format-strings"></a>Stringhe di formato

Un oggetto `MultiBinding` può formattare qualsiasi risultato MultiBinding visualizzato sotto forma di stringa, con la `StringFormat` Proprietà. Questa proprietà può essere impostata su una stringa di formattazione .NET standard, con segnaposto, che specifica come formattare il risultato del MultiBinding:

```xaml
<Label>
    <Label.Text>
        <MultiBinding StringFormat="{}{0} {1} {2}">
            <Binding Path="Employee1.Forename" />
            <Binding Path="Employee1.MiddleName" />
            <Binding Path="Employee1.Surname" />
        </MultiBinding>
    </Label.Text>
</Label>
```

In questo esempio la `StringFormat` proprietà combina i tre valori associati in un'unica stringa visualizzata da [`Label`](xref:Xamarin.Forms.Label) .

> [!IMPORTANT]
> Il numero di parametri in un formato stringa composito non può superare il numero di `Binding` oggetti figlio nell'oggetto `MultiBinding` .

Quando si impostano le `Converter` `StringFormat` proprietà e, il convertitore viene applicato per primo al valore di dati, quindi `StringFormat` viene applicato.

Per ulteriori informazioni sulla formattazione delle stringhe in Novell. Forms, vedere la pagina relativa alla [formattazione della stringa Novell. Forms](string-formatting.md).

## <a name="provide-fallback-values"></a>Specificare i valori di fallback

Le associazioni dati possono essere rese più solide definendo i valori di fallback da usare in caso di errore del processo di associazione. Questa operazione può essere eseguita definendo facoltativamente le [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà e in un `MultiBinding` oggetto.

Un oggetto utilizzerà la proprietà `MultiBinding` [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) quando il `Convert` metodo di un' `IMultiValueConverter` istanza restituisce `BindableProperty.UnsetValue` , che indica che il convertitore non ha prodotto un valore. Un oggetto utilizzerà la proprietà `MultiBinding` [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) quando il `Convert` metodo di un' `IMultiValueConverter` istanza restituisce `null` , che indica che il convertitore non è in grado di eseguire la conversione.

Per ulteriori informazioni sui fallback di binding, vedere [fallback dell'associazione Novell. Forms](binding-fallbacks.md).

## <a name="nest-multibinding-objects"></a>Annidare oggetti MultiBinding

`MultiBinding`gli oggetti possono essere annidati in modo che più `MultiBinding` oggetti vengano valutati per restituire un valore tramite un' `IMultiValueConverter` istanza:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.NestedMultiBindingPage"
             Title="Nested MultiBinding demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:AnyTrueMultiConverter x:Key="AnyTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AnyTrueConverter}">
                <MultiBinding Converter="{StaticResource AllTrueConverter}">
                    <Binding Path="Employee.IsOver16" />
                    <Binding Path="Employee.HasPassedTest" />
                    <Binding Path="Employee.IsSuspended" Converter="{StaticResource InverterConverter}" />                        
                </MultiBinding>
                <Binding Path="Employee.IsMonarch" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>
```

In questo esempio, l' `MultiBinding` oggetto utilizza l' `AnyTrueMultiConverter` istanza per impostare la [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) proprietà su `true` , a condizione che tutti gli [`Binding`](xref:Xamarin.Forms.Binding) oggetti nell'oggetto interno `MultiBinding` restituisca `true` o purché l' `Binding` oggetto nell' `MultiBinding` oggetto esterno restituisca `true` . In caso contrario, la `CheckBox.IsChecked` proprietà viene impostata su `false` .

## <a name="use-a-relativesource-binding-in-a-multibinding"></a>Usare un'associazione RelativeSource in un'associazione MultiBinding

`MultiBinding`gli oggetti supportano le associazioni relative che consentono di impostare l'origine di associazione in relazione alla posizione della destinazione del binding:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:DataBindingDemos">
    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />

        <ControlTemplate x:Key="CardViewExpanderControlTemplate">
            <Expander BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                      IsExpanded="{Binding IsExpanded, Source={RelativeSource TemplatedParent}}"
                      BackgroundColor="{Binding CardColor}">
                <Expander.IsVisible>
                    <MultiBinding Converter="{StaticResource AllTrueConverter}">
                        <Binding Path="IsExpanded" />
                        <Binding Path="IsEnabled" />
                    </MultiBinding>
                </Expander.IsVisible>
                <Expander.Header>
                    <Grid>
                        <!-- XAML that defines Expander header goes here -->
                    </Grid>
                </Expander.Header>
                <Grid>
                    <!-- XAML that defines Expander content goes here -->
                </Grid>
            </Expander>
        </ControlTemplate>
    </ContentPage.Resources>

    <StackLayout>
        <controls:CardViewExpander BorderColor="DarkGray"
                                   CardTitle="John Doe"
                                   CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                                   IconBackgroundColor="SlateGray"
                                   IconImageSource="user.png"
                                   ControlTemplate="{StaticResource CardViewExpanderControlTemplate}"
                                   IsEnabled="True"
                                   IsExpanded="True" />
    </StackLayout>
</ContentPage>
```

In questo esempio, la `TemplatedParent` modalità di associazione relativa viene utilizzata per eseguire l'associazione dall'interno di un modello di controllo all'istanza dell'oggetto di runtime a cui viene applicato il modello. L' `Expander` oggetto, che è l'elemento radice di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , è `BindingContext` impostato sull'istanza dell'oggetto di runtime a cui viene applicato il modello. Pertanto, `Expander` e i relativi elementi figlio risolvono le espressioni di associazione e [`Binding`](xref:Xamarin.Forms.Binding) gli oggetti in base alle proprietà dell' `CardViewExpander` oggetto. `MultiBinding`Usa l' `AllTrueMultiConverter` istanza di per impostare la `Expander.IsVisible` proprietà su `true` purché i due [`Binding`](xref:Xamarin.Forms.Binding) oggetti restituiscono `true` . In caso contrario, la `Expander.IsVisible` proprietà viene impostata su `false` .

Per altre informazioni sulle associazioni relative, vedere [Associazioni relative di Xamarin.Forms](relative-bindings.md). Per ulteriori informazioni sui modelli di controllo, vedere [Novell. Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Formattazione delle stringhe di Xamarin.Forms](string-formatting.md)
- [Fallback per i binding di Xamarin.Forms](binding-fallbacks.md)
- [Associazioni relative di Xamarin.Forms](relative-bindings.md)
- [Modelli di controllo Novell. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
