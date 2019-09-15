---
title: Origini dati ListView
description: Questo articolo illustra come compilare il ListView di xamarin. Forms con i dati e come usare il data binding con un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2018
ms.openlocfilehash: aa968562470a1e3405bf68be7eb0294273970386
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998022"
---
# <a name="listview-data-sources"></a>Origini dati ListView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Un Novell. Forms [`ListView`](xref:Xamarin.Forms.ListView) viene usato per visualizzare gli elenchi di dati. In questo articolo viene illustrato come popolare `ListView` un oggetto con i dati e come associare dati all'elemento selezionato.

## <a name="itemssource"></a>ItemsSource

Oggetto [`ListView`](xref:Xamarin.Forms.ListView) viene popolata con i dati utilizzando le [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà, che può accettare qualsiasi insieme che implementa `IEnumerable`. Il modo più semplice per popolare un `ListView` prevede l'uso di una matrice di stringhe:

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

Il codice C# equivalente è:

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

![](data-and-databinding-images/itemssource-simple.png "ListView visualizzazione elenco di stringhe")

Questo approccio compilerà `ListView` l'oggetto con un elenco di stringhe. Per impostazione predefinita `ListView` chiamerà `ToString` e visualizzare il risultato in un `TextCell` per ogni riga. Per personalizzare la modalità di visualizzazione dei dati, vedere [aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Poiché `ItemsSource` è stato inviato a una matrice, il contenuto non verrà aggiornata quando viene modificato l'elenco o una matrice sottostante. Se si desidera che il ListView per aggiornare automaticamente come gli elementi vengono aggiunti, rimossi e modificati nell'elenco sottostante, è necessario usare un `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) è definito in `System.Collections.ObjectModel` ed è analoga a `List`, ad eccezione del fatto che è possibile avvisare `ListView` di tutte le modifiche:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Data binding

Il data binding è la "collazione" che associa le proprietà di un oggetto dell'interfaccia utente alle proprietà di un oggetto CLR, ad esempio una classe nell'elemento ViewModel. Associazione dati è utile perché semplifica lo sviluppo di interfacce utente mediante la sostituzione di una grande quantità di noioso codice standard.

Data binding dati funziona, mantenendo gli oggetti sincronizzati man mano che cambiano i relativi valori associati. Anziché dover scrivere i gestori eventi per ogni modifica del valore di un controllo, è necessario definire l'associazione e abilitare l'associazione nell'elemento ViewModel.

Per altre informazioni sul data binding, vedere [nozioni di base di Data Binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) che fa parte di quattro il [serie di articoli nozioni di base di xamarin. Forms XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Binding di celle

Proprietà delle celle (e gli elementi figlio di celle) possono essere associate alle proprietà degli oggetti nel `ItemsSource`. È ad esempio possibile `ListView` utilizzare un oggetto per presentare un elenco di dipendenti.

La classe dipendente:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Viene `ObservableCollection<Employee>` creato un oggetto, impostato `ListView` `ItemsSource`come e l'elenco viene popolato con i dati:

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
> `ObservableCollection`non è thread-safe. La modifica `ObservableCollection` di un oggetto comporta l'esecuzione degli aggiornamenti dell'interfaccia utente sullo stesso thread che ha eseguito le modifiche. Se il thread non è il thread principale dell'interfaccia utente, verrà generata un'eccezione.

Il frammento seguente illustra un `ListView` associato a un elenco di dipendenti:

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

Questo esempio XAML definisce un `ContentPage` oggetto che contiene `ListView`un oggetto. L'origine dati del `ListView` viene impostato tramite la `ItemsSource` attributo. Il layout di ogni riga in `ItemsSource` viene definito all'interno dell' `ListView.ItemTemplate` elemento. Ciò comporta gli screenshot seguenti:

![](data-and-databinding-images/bound-data.png "ListView con Data Binding")

### <a name="binding-selecteditem"></a>Associazione SelectedItem

Spesso è opportuno per l'associazione all'elemento selezionato di un `ListView`, anziché usare un gestore eventi per rispondere alle modifiche. A questo scopo in XAML, associare il `SelectedItem` proprietà:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

`listView`Supponendoche sia un elenco di stringhe `SomeLabel` , la relativa `Text` proprietà sarà associata a `SelectedItem`. `ItemsSource`

## <a name="related-links"></a>Collegamenti correlati

- [Associazione bidirezionale (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
