---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f6c8810ea37c767ef67c7f53d312b63a9de09f26
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136565"
---
# <a name="searchbar-spell-check-on-windows"></a>SearchBar Controllo ortografico in Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente a un oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) di interagire con il motore di controllo ortografico. Viene utilizzato in XAML impostando la [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) proprietà associata su un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

Il `SearchBar.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). [ `SearchBar.SetIsSpellCheckEnabled` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. SearchBar. SetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . SearchBar}, System. Boolean), il metodo, nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, attiva e disattiva il controllo ortografico. Inoltre, il `SearchBar.SetIsSpellCheckEnabled` metodo può essere utilizzato per abilitare o disabilitare il controllo ortografico chiamando il metodo [ `SearchBar.GetIsSpellCheckEnabled` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. SearchBar. GetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . SearchBar})) metodo per restituire se il correttore ortografico è abilitato:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Il risultato è che il testo immesso nel [`SearchBar`](xref:Xamarin.Forms.SearchBar) controllo può essere ortografico, con le ortografie non corrette indicate all'utente:

![Controllo ortografico di SearchBar specifico della piattaforma](searchbar-spell-check-images/searchbar-spellcheck.png "Controllo ortografico di SearchBar specifico della piattaforma")

> [!NOTE]
> La `SearchBar` classe nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi dispone [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) anche [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) di metodi e che possono essere usati per abilitare e disabilitare il controllo ortografico `SearchBar` rispettivamente in.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
