---
title: Stile di SearchBar in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se un oggetto SearchBar presenta uno sfondo.
ms.prod: xamarin
ms.assetid: 3D512DD6-078E-4BC6-926E-62BA6F4DE640
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: 7d95a90e96f868b6d8368054659f978555bc28ac
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532702"
---
# <a name="searchbar-style-on-ios"></a>Stile di SearchBar in iOS

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

Il `SearchBar.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `SearchBar.SetSearchBarStyle` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se [`SearchBar`](xref:Xamarin.Forms.SearchBar) l'oggetto ha uno sfondo. L' `UISearchBarStyle` enumerazione fornisce tre valori possibili:

- `Default`indica che l' [`SearchBar`](xref:Xamarin.Forms.SearchBar) oggetto ha lo stile predefinito. Si tratta del valore predefinito della proprietà `SearchBar.SearchBarStyle` associabile.
- `Prominent`indica che l' [`SearchBar`](xref:Xamarin.Forms.SearchBar) oggetto ha uno sfondo trasparente e il campo di ricerca è opaco.
- `Minimal`indica che l' [`SearchBar`](xref:Xamarin.Forms.SearchBar) oggetto non ha sfondo e il campo di ricerca è traslucido.

Inoltre, il `SearchBar.GetSearchBarStyle` metodo può essere utilizzato per restituire l'oggetto `UISearchBarStyle` applicato a `SearchBar`.

Il risultato è che un membro `UISearchBarStyle` specificato viene applicato a un [`SearchBar`](xref:Xamarin.Forms.SearchBar)oggetto, che controlla se `SearchBar` l'oggetto ha uno sfondo:

![Screenshot degli stili di SearchBar in iOS](searchbar-style-images/searchbar-styles.png "Stili di SearchBar in iOS")

Le schermate seguenti mostrano i `UISearchBarStyle` membri applicati agli [`SearchBar`](xref:Xamarin.Forms.SearchBar) oggetti con la relativa `BackgroundColor` proprietà impostata:

![Screenshot degli stili di SearchBar con colore di sfondo, in iOS](searchbar-style-images/searchbar-background-styles.png "Stili di SearchBar con colore di sfondo in iOS")

## <a name="related-links"></a>Link correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
