---
title: " SearchBar Controllo ortografico in Windows"
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. In questo articolo viene illustrato come utilizzare la piattaforma Windows specifica che consente a un oggetto SearchBar di interagire con il motore di controllo ortografico.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: da75d42e0e0bcf38dd2ff50999b705125dc4e2b5
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291654"
---
# <a name="searchbar-spell-check-on-windows"></a>SearchBar Controllo ortografico in Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente a un [`SearchBar`](xref:Xamarin.Forms.SearchBar) di interagire con il motore di controllo ortografico. Viene utilizzato in XAML impostando la proprietà associata [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) su un valore `boolean`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

Il metodo `SearchBar.On<Windows>` specifica che questa specifica della piattaforma verrà eseguita solo nel piattaforma UWP (Universal Windows Platform). Il metodo [`SearchBar.SetIsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) , nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , attiva e disattiva il controllo ortografico. Inoltre, è possibile usare il metodo `SearchBar.SetIsSpellCheckEnabled` per attivare o disabilitare il controllo ortografico chiamando il metodo [`SearchBar.GetIsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) per restituire se il correttore ortografico è abilitato:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Il risultato è che il testo immesso nel [`SearchBar`](xref:Xamarin.Forms.SearchBar) può essere sottoposto a ortografia, con le ortografie non corrette indicate all'utente:

![Controllo ortografico di SearchBar specifico della piattaforma](searchbar-spell-check-images/searchbar-spellcheck.png "Controllo ortografico di SearchBar specifico della piattaforma")

> [!NOTE]
> La classe `SearchBar` nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dispone anche di [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) e [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) metodi che possono essere usati per abilitare e disabilitare il controllo ortografico rispettivamente sul `SearchBar`.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
