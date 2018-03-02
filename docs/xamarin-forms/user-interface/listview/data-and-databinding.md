---
title: Origini dati di ListView
description: Informazioni su come inserire dati in ListView.
ms.topic: article
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 78659aff0b6b4e6401bec96a55935c141d1199f1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="listview-data-sources"></a>Origini dati di ListView

ListView viene utilizzato per visualizzare gli elenchi di dati. Si apprenderà sulla compilazione di un controllo ListView con i dati e come è possibile associare l'elemento selezionato.

- **[Impostazione ItemsSource](#ItemsSource)**  &ndash; utilizza un elenco semplice o una matrice.
- **[Associazione dati](#Data_Binding)**  &ndash; stabilisce una relazione tra un modello e il controllo ListView. L'associazione è ideale per il modello MVVM.

## <a name="itemssource"></a>ItemsSource
ListView viene popolato con i dati utilizzando il `ItemsSource` proprietà, che può accettare qualsiasi raccolta che implementa `IEnumerable`. Il modo più semplice per compilare un `ListView` comporta l'utilizzo di una matrice di stringhe:

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]{
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

//monochrome will not appear in the list because it was added
//after the list was populated.
listView.ItemsSource.Add("monochrome");
```

![](data-and-databinding-images/itemssource-simple.png "ListView visualizzazione elenco di stringhe")

L'approccio precedente popolerà il `ListView` con un elenco di stringhe. Per impostazione predefinita, `ListView` chiamerà `ToString` e visualizzare il risultato in un `TextCell` per ogni riga. Per personalizzare la modalità di visualizzazione dati, vedere [aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Poiché `ItemsSource` è stato inviato a una matrice, il contenuto non verrà aggiornate in base alle modifiche di elenco o matrice sottostante. Se si desidera il controllo ListView per aggiornare automaticamente come gli elementi vengono aggiunti, rimossi e modificati nell'elenco sottostante, è necessario utilizzare un `ObservableCollection`. [`ObservableCollection`](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) è definito in `System.Collections.ObjectModel` ed è analoga `List`, ad eccezione del fatto che è possibile segnalare `ListView` delle modifiche:

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>Data binding
Associazione dati è "glue" che associa le proprietà di un oggetto di interfaccia utente per le proprietà di un oggetto CLR, ad esempio una classe nel ViewModel. Associazione dati è utile perché semplifica lo sviluppo di interfacce utente sostituendo la quantità di codice boilerplate un'operazione noiosa.

Funzionamento dell'associazione dati per mantenere sincronizzati gli oggetti come modificare i relativi valori associati. Invece di dover scrivere gestori eventi per ogni volta che cambia il valore di un controllo, stabilire l'associazione e consentono l'associazione del ViewModel.

Per ulteriori informazioni sull'associazione dati, vedere [nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) che fa parte di quattro il [nozioni di base di xamarin. Forms XAML articolo serie](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Associazione di celle
Proprietà delle celle (e gli elementi figlio di celle) possono essere associate alle proprietà degli oggetti nel `ItemsSource`. Ad esempio, un controllo ListView potrebbe essere utilizzato per presentare un elenco di dipendenti con immagini.

La classe dipendente:

```csharp
public class Employee{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>` viene creato e impostato come il `ListView`del `ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
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

Nel frammento seguente viene illustrato un `ListView` associato a un elenco di dipendenti:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
Title="Employee List">
  <ListView x:Name="EmployeeView">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Si noti che l'associazione è stata impostata nel codice per semplicità, anche se è stato possibile associare in XAML.

Il bit precedente del codice XAML definisce un `ContentPage` che contiene un `ListView`. L'origine dati di `ListView` è impostata tramite il `ItemsSource` attributo. Il layout di ogni riga nel `ItemsSource`è definito all'interno di `ListView.ItemTemplate` elemento.

Questo è il risultato:

![](data-and-databinding-images/bound-data.png "ListView con associazione dati")

### <a name="binding-selecteditem"></a>Associazione SelectedItem

Sarà spesso si desidera associare all'elemento selezionato di un `ListView`, invece di utilizzare un gestore eventi per rispondere alle modifiche. A tale scopo in XAML, associare il `SelectedItem` proprietà:

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

Supponendo che `listView`del `ItemsSource` è riportato un elenco di stringhe, `SomeLabel` avrà la proprietà text associata al `SelectedItem`.



## <a name="related-links"></a>Collegamenti correlati

- [Associazione bidirezionale (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [note sulla versione 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [note sulla versione 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
