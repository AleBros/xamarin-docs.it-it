---
title: Origini dati ListView
description: Questo articolo illustra come compilare il ListView di xamarin. Forms con i dati e come usare il data binding con un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
ms.openlocfilehash: e51f0bd011750b030c0a11b9b89a2c2473f2a9ed
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247587"
---
# <a name="listview-data-sources"></a>Origini dati ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Un [`ListView`](xref:Xamarin.Forms.ListView) Novell. Forms viene usato per visualizzare gli elenchi di dati. In questo articolo viene illustrato come popolare un `ListView` con i dati e come associare dati all'elemento selezionato.

## <a name="itemssource"></a>ItemsSource

Una [`ListView`](xref:Xamarin.Forms.ListView) viene popolata con i dati usando la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) , che può accettare qualsiasi raccolta che implementa `IEnumerable`. Il modo più semplice per popolare un `ListView` prevede l'uso di una matrice di stringhe:

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

Con questo approccio il `ListView` verrà popolato con un elenco di stringhe. Per impostazione predefinita, `ListView` chiamerà `ToString` e visualizzerà il risultato in un `TextCell` per ogni riga. Per personalizzare la modalità di visualizzazione dei dati, vedere [aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Poiché `ItemsSource` è stato inviato a una matrice, il contenuto non viene aggiornato come l'elenco sottostante o le modifiche apportate alla matrice. Se si desidera che ListView venga aggiornato automaticamente durante l'aggiunta, la rimozione e la modifica degli elementi nell'elenco sottostante, sarà necessario utilizzare un `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) viene definito in `System.Collections.ObjectModel` ed è analogo a `List`, ad eccezione del fatto che è in grado di notificare `ListView` di eventuali modifiche:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Data binding

Il data binding è la "collazione" che associa le proprietà di un oggetto dell'interfaccia utente alle proprietà di un oggetto CLR, ad esempio una classe nell'elemento ViewModel. Associazione dati è utile perché semplifica lo sviluppo di interfacce utente mediante la sostituzione di una grande quantità di noioso codice standard.

Data binding dati funziona, mantenendo gli oggetti sincronizzati man mano che cambiano i relativi valori associati. Anziché dover scrivere i gestori eventi per ogni modifica del valore di un controllo, è necessario definire l'associazione e abilitare l'associazione nell'elemento ViewModel.

Per altre informazioni su data binding, vedere [nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) , che è la quarta parte della [serie di articoli di base XAML di Novell. Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Binding di celle

È possibile associare le proprietà delle celle (e degli elementi figlio delle celle) alle proprietà degli oggetti nel `ItemsSource`. Ad esempio, è possibile usare un `ListView` per presentare un elenco di dipendenti.

La classe dipendente:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Viene creata una `ObservableCollection<Employee>`, impostata come `ListView` `ItemsSource`e l'elenco viene popolato con i dati:

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
> Mentre una `ListView` verrà aggiornata in risposta alle modifiche apportate nella `ObservableCollection`sottostante, una `ListView` non verrà aggiornata se un'istanza di `ObservableCollection` diversa viene assegnata al riferimento `ObservableCollection` originale (ad esempio, `employees = otherObservableCollection;`).

Il frammento di codice seguente illustra un `ListView` associato a un elenco di dipendenti:

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

In questo esempio XAML viene definito un `ContentPage` contenente una `ListView`. L'origine dati del `ListView` viene impostata tramite l'attributo `ItemsSource`. Il layout di ogni riga nel `ItemsSource` viene definito all'interno dell'elemento `ListView.ItemTemplate`. Ciò comporta gli screenshot seguenti:

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` non è thread-safe. La modifica di un `ObservableCollection` comporta l'esecuzione degli aggiornamenti dell'interfaccia utente sullo stesso thread che ha eseguito le modifiche. Se il thread non è il thread principale dell'interfaccia utente, verrà generata un'eccezione.

### <a name="binding-selecteditem"></a>Associazione SelectedItem

Spesso è necessario eseguire il binding all'elemento selezionato di una `ListView`, anziché usare un gestore eventi per rispondere alle modifiche. Per eseguire questa operazione in XAML, associare la proprietà `SelectedItem`:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

Supponendo che `ItemsSource` di `listView`sia un elenco di stringhe, `SomeLabel` avrà la relativa proprietà `Text` associata al `SelectedItem`.

## <a name="related-links"></a>Collegamenti correlati

- [Binding bidirezionale (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
