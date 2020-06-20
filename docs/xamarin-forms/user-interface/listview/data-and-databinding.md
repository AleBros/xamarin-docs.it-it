---
title: Origini dati ListView
description: Questo articolo illustra come popolare Xamarin.Forms ListView con i dati e come usare Data Binding con un controllo ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38a895c9064fc012aec35b37eac78bb16ff009a9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131508"
---
# <a name="listview-data-sources"></a>Origini dati ListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) Viene utilizzato per visualizzare elenchi di dati. In questo articolo viene illustrato come popolare un oggetto `ListView` con i dati e come associare dati all'elemento selezionato.

## <a name="itemssource"></a>ItemsSource

Un oggetto [`ListView`](xref:Xamarin.Forms.ListView) viene popolato con i dati utilizzando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà, che può accettare qualsiasi raccolta che implementa `IEnumerable` . Il modo più semplice per popolare un oggetto `ListView` prevede l'uso di una matrice di stringhe:

```xaml
<ListView>
      <ListView.ItemsSource>
          <x:Array Type="{x:Type x:String}">
            <x:String>mono</x:String>
            <x:String>monodroid</x:String>
            <x:String>monotouch</x:String>
            <x:String>monorail</x:String>
            <x:String>monodevelop</x:String>
            <x:String>monotone</x:String>
            <x:String>monopoly</x:String>
            <x:String>monomodal</x:String>
            <x:String>mononucleosis</x:String>
          </x:Array>
      </ListView.ItemsSource>
</ListView>
```

Il codice C# equivalente è il seguente:

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]
{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};
```

![](data-and-databinding-images/itemssource-simple.png "ListView Displaying List of Strings")

Questo approccio compilerà l'oggetto `ListView` con un elenco di stringhe. Per impostazione predefinita, chiamerà `ListView` `ToString` e visualizzerà il risultato in un `TextCell` per ogni riga. Per personalizzare la modalità di visualizzazione dei dati, vedere [aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Poiché `ItemsSource` è stato inviato a una matrice, il contenuto non viene aggiornato come l'elenco sottostante o la matrice viene modificata. Se si desidera che ListView venga aggiornato automaticamente durante l'aggiunta, la rimozione e la modifica di elementi nell'elenco sottostante, sarà necessario utilizzare `ObservableCollection` . [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1)è definito in `System.Collections.ObjectModel` e è analogo a `List` , ad eccezione del fatto che può notificare `ListView` eventuali modifiche:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Data binding

Il data binding è la "collazione" che associa le proprietà di un oggetto dell'interfaccia utente alle proprietà di un oggetto CLR, ad esempio una classe nell'elemento ViewModel. Il data binding è utile perché semplifica lo sviluppo di interfacce utente sostituendo una grande quantità di codice standard noioso.

Il data binding funziona mantenendo gli oggetti sincronizzati quando cambiano i valori associati. Anziché dover scrivere i gestori eventi per ogni modifica del valore di un controllo, è necessario definire l'associazione e abilitare l'associazione nell'elemento ViewModel.

Per altre informazioni su data binding, vedere [nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) , che è la quarta delle [ Xamarin.Forms serie di articoli di base di XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Binding di celle

È possibile associare le proprietà delle celle (e degli elementi figlio delle celle) a proprietà di oggetti in `ItemsSource` . È ad esempio `ListView` possibile utilizzare un oggetto per presentare un elenco di dipendenti.

Classe Employee:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>`Viene creato un oggetto, impostato come `ListView` `ItemsSource` e l'elenco viene popolato con i dati:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
    EmployeeView.ItemsSource = employees;

    // ObservableCollection allows items to be added after ItemsSource
    // is set and the UI will react to changes
    employees.Add(new Employee{ DisplayName="Rob Finnerty"});
    employees.Add(new Employee{ DisplayName="Bill Wrestler"});
    employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
    employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
    employees.Add(new Employee{ DisplayName="Sheri Spruce"});
    employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

> [!WARNING]
> Mentre un oggetto `ListView` verrà aggiornato in risposta alle modifiche nell' `ObservableCollection` oggetto sottostante, un oggetto `ListView` non verrà aggiornato se un'istanza diversa `ObservableCollection` viene assegnata al `ObservableCollection` riferimento originale (ad esempio `employees = otherObservableCollection;` ).

Il frammento di codice seguente illustra un oggetto `ListView` associato a un elenco di dipendenti:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="EmployeeView"
            ItemsSource="{Binding Employees}">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Questo esempio XAML definisce un oggetto `ContentPage` che contiene un oggetto `ListView` . L'origine dati di `ListView` è impostata tramite l'attributo `ItemsSource`. Il layout di ogni riga in `ItemsSource` è definito all'interno dell'elemento `ListView.ItemTemplate`. Ciò comporta gli screenshot seguenti:

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` non è affidabile. La modifica di un oggetto `ObservableCollection` comporta l'esecuzione degli aggiornamenti dell'interfaccia utente sullo stesso thread che ha eseguito le modifiche. Se il thread non è il thread principale dell'interfaccia utente, verrà generata un'eccezione.

### <a name="binding-selecteditem"></a>Binding SelectedItem

Spesso è necessario eseguire l'associazione all'elemento selezionato di un `ListView` , anziché usare un gestore eventi per rispondere alle modifiche. Per eseguire questa operazione in XAML, associare la `SelectedItem` proprietà:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

Supponendo `listView` `ItemsSource` che sia un elenco di stringhe, la `SomeLabel` relativa `Text` proprietà sarà associata a `SelectedItem` .

## <a name="related-links"></a>Collegamenti correlati

- [Binding bidirezionale (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
