---
title: "stile di SearchBar su iOS" Descrizione: "le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se un oggetto SearchBar presenta uno sfondo ".
ms. prod: Novell MS. AssetID: 3D512DD6-078E-4BC6-926E-62BA6F4DE640 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/05/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="searchbar-style-on-ios"></a>Stile di SearchBar in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se a [`SearchBar`](xref:Xamarin.Forms.SearchBar) è associato uno sfondo. Viene utilizzato in XAML impostando la `SearchBar.SearchBarStyle` proprietà associabile su un valore dell' `UISearchBarStyle` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

Il `SearchBar.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `SearchBar.SetSearchBarStyle` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se l'oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) ha uno sfondo. L' `UISearchBarStyle` enumerazione fornisce tre valori possibili:

- `Default`indica che l'oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) ha lo stile predefinito. Si tratta del valore predefinito della `SearchBar.SearchBarStyle` proprietà associabile.
- `Prominent`indica che l'oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) ha uno sfondo trasparente e il campo di ricerca è opaco.
- `Minimal`indica che l'oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) non ha sfondo e il campo di ricerca è traslucido.

Inoltre, il `SearchBar.GetSearchBarStyle` metodo può essere utilizzato per restituire l'oggetto `UISearchBarStyle` applicato a `SearchBar` .

Il risultato è che un `UISearchBarStyle` membro specificato viene applicato a un oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) , che controlla se l'oggetto `SearchBar` ha uno sfondo:

![Screenshot degli stili di SearchBar in iOS](searchbar-style-images/searchbar-styles.png "Stili di SearchBar in iOS")

Le schermate seguenti mostrano i `UISearchBarStyle` membri applicati agli [`SearchBar`](xref:Xamarin.Forms.SearchBar) oggetti con la relativa `BackgroundColor` proprietà impostata:

![Screenshot degli stili di SearchBar con colore di sfondo, in iOS](searchbar-style-images/searchbar-background-styles.png "Stili di SearchBar con colore di sfondo in iOS")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
