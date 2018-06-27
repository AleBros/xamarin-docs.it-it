---
title: Approfondimento su Xamarin.Forms Multiscreen
description: Questo articolo illustra la navigazione tra le pagine e il data binding in un'applicazione Xamarin.Forms e ne dimostra l'uso in un'applicazione multipiattaforma con più schermate.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: e4faa36c-6600-48c0-94c4-b4431103a4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 1c7edff3c71b9d7530b2acf21acaa06149156d43
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242235"
---
# <a name="xamarinforms-multiscreen-deep-dive"></a>Approfondimento su Xamarin.Forms Multiscreen

Nella [Guida rapida di Xamarin.Forms Multiscreen](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/quickstart.md) l'applicazione Phoneword è stata estesa in modo da includere una seconda schermata per tenere traccia della cronologia delle chiamate per l'applicazione. Questo articolo riesamina quello che è stato compilato, per comprendere meglio la navigazione tra le pagine e il data binding nell'applicazione Xamarin.Forms.

## <a name="navigation"></a>Navigazione

Xamarin.Forms fornisce un modello di navigazione predefinito che gestisce la navigazione e l'esperienza utente di uno stack di pagine. Questo modello implementa uno stack LIFO (Last-In, First-Out) di oggetti `Page`. Per passare da una pagina all'altra, un'applicazione eseguirà il push di una nuova pagina su questo stack. Per tornare alla pagina precedente, l'applicazione preleverà la pagina corrente dallo stack.

Xamarin.Forms ha una classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) che gestisce lo stack di oggetti [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). La classe `NavigationPage` aggiungerà inoltre una barra di navigazione nella parte superiore della pagina che consente di visualizzare un titolo e un pulsante <span class="uiitem">Indietro</span> appropriato per la piattaforma per tornare alla pagina precedente. L'esempio di codice seguente illustra come eseguire il wrapping di una `NavigationPage` attorno alla prima pagina in un'applicazione:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new MainPage ());
}
```

Tutte le istanze di [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) hanno una proprietà [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) che espone i metodi per modificare lo stack della pagina. Questi metodi dovrebbero essere richiamati solo se l'applicazione include una [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). Per passare alla `CallHistoryPage`, è necessario richiamare il metodo [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) come illustrato nell'esempio di codice seguente:

```csharp
async void OnCallHistory(object sender, EventArgs e)
{
    await Navigation.PushAsync (new CallHistoryPage ());
}
```

In questo modo viene eseguito il push del nuovo oggetto `CallHistoryPage` nello stack di navigazione. Per tornare a livello di codice alla pagina originale, l'oggetto `CallHistoryPage` deve chiamare il metodo [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PopAsync();
```

Tuttavia, nell'applicazione Phoneword questo codice non è richiesto dal momento che la classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) aggiunge una barra di navigazione nella parte superiore della pagina che include un pulsante <span class="uiitem">Indietro</span> appropriato per la piattaforma che consentirà di tornare alla pagina precedente.

## <a name="data-binding"></a>Data binding

Il data binding viene usato per semplificare il modo in cui un'applicazione Xamarin.Forms visualizza i propri dati e interagisce con essi. Stabilisce una connessione tra l'interfaccia utente e l'applicazione sottostante. La classe [`BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) contiene la maggior parte dell'infrastruttura per supportare il data binding.

Il data binding definisce la relazione tra due oggetti. L'oggetto di *origine* fornirà i dati. L'oggetto di *destinazione* utilizzerà (e spesso visualizzerà) i dati dall'oggetto di origine. Nell'applicazione Phoneword la destinazione del binding è il controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) che visualizza i numeri di telefono, mentre la raccolta `PhoneNumbers` rappresenta l'origine del binding.

La raccolta `PhoneNumbers` viene dichiarata e inizializzata nella classe `App`, come illustrato nell'esempio di codice seguente:

```csharp
public partial class App : Application
{
   public static List<string> PhoneNumbers { get; set; }

   public App ()
   {
     PhoneNumbers = new List<string>();
     ...
   }
   ...
}
```

L'istanza [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) viene dichiarata e inizializzata nella classe `CallHistoryPage`, come illustrato nell'esempio di codice seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    ...
    <ContentPage.Content>
       ...
       <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
       ...
    </ContentPage.Content>
</ContentPage>
```

In questo esempio il controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) visualizzerà la raccolta `IEnumerable` di dati a cui la proprietà [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView.ItemsSource/) è associata. La raccolta di dati può essere costituita da oggetti di qualsiasi tipo, ma per impostazione predefinita `ListView` userà il metodo `ToString` di ogni elemento per visualizzare tale elemento. L'estensione di markup [`x:Static`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) viene usata per indicare che la proprietà `ItemsSource` sarà associata alla proprietà statica `PhoneNumbers` della classe `App`, che può trovarsi nello spazio dei nomi `local`.

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding). Per altre informazioni sulle estensioni di markup XAML, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md) (Estensioni di markup XAML).

## <a name="additional-concepts-introduced-in-phoneword"></a>Altri concetti introdotti in Phoneword

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è responsabile della visualizzazione di una raccolta di elementi sullo schermo. Ogni elemento in `ListView` è contenuto in una singola cella. Per altre informazioni sull'uso del controllo `ListView`, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la navigazione tra le pagine e il data binding in un'applicazione Xamarin.Forms e ne ha dimostrato l'uso in un'applicazione multipiattaforma multi-schermata.
