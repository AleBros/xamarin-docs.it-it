---
title: Origini dati ListView
description: Questo articolo illustra come compilare il ListView di xamarin. Forms con i dati e come usare il data binding con un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2018
ms.openlocfilehash: 9855255464b32b99d78d7a1cdb24acce22d01648
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654750"
---
# <a name="listview-data-sources"></a>Origini dati ListView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Oggetto [`ListView`](xref:Xamarin.Forms.ListView) viene utilizzato per visualizzare gli elenchi di dati. Si apprenderanno informazioni sul popolamento di un ListView con dati e come è possibile associare all'elemento selezionato.

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

L'approccio precedente popolerà il `ListView` con un elenco di stringhe. Per impostazione predefinita `ListView` chiamerà `ToString` e visualizzare il risultato in un `TextCell` per ogni riga. Per personalizzare la modalità di visualizzazione dei dati, vedere [aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Poiché `ItemsSource` è stato inviato a una matrice, il contenuto non verrà aggiornata quando viene modificato l'elenco o una matrice sottostante. Se si desidera che il ListView per aggiornare automaticamente come gli elementi vengono aggiunti, rimossi e modificati nell'elenco sottostante, è necessario usare un `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) è definito in `System.Collections.ObjectModel` ed è analoga a `List`, ad eccezione del fatto che è possibile avvisare `ListView` di tutte le modifiche:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>Data binding
Associazione dati è il "glue" che associa le proprietà di un oggetto di interfaccia utente per le proprietà di un oggetto CLR, ad esempio una classe nei ViewModel. Associazione dati è utile perché semplifica lo sviluppo di interfacce utente mediante la sostituzione di una grande quantità di noioso codice standard.

Data binding dati funziona, mantenendo gli oggetti sincronizzati man mano che cambiano i relativi valori associati. Anziché dover scrivere gestori eventi per ogni volta che cambia il valore di un controllo, stabilire l'associazione e abilitare l'associazione nei ViewModel.

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

Viene `ObservableCollection<Employee>` creato un oggetto e impostato `ListView`come `ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
  //defined in XAML to follow
  EmployeeView.ItemsSource = employees;
  ...
}
```

L'elenco viene popolato con i dati:

```csharp
public EmployeeListPage()
{
  ...
  employees.Add(new Employee{ DisplayName="Rob Finnerty"});
  employees.Add(new Employee{ DisplayName="Bill Wrestler"});
  employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
  employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
  employees.Add(new Employee{ DisplayName="Sheri Spruce"});
  employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

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

Presupponendo che `listView`del `ItemsSource` è riportato un elenco di stringhe `SomeLabel` avrà la relativa proprietà text associata al `SelectedItem`.

## <a name="related-links"></a>Collegamenti correlati

- [Associazione bidirezionale (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
