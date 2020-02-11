---
title: Icone di TabbedPage in Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che consente di visualizzare le icone di pagina su una barra degli strumenti TabbedPage.
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a40203e4d01f45ef36ee6988198400a259600aac
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198090"
---
# <a name="tabbedpage-icons-on-windows"></a>Icone di TabbedPage in Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente di visualizzare le icone di pagina su [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) una barra degli strumenti e consente di specificare facoltativamente le dimensioni dell'icona. Vengono utilizzati in XAML, impostando il [`TabbedPage.HeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) proprietà associata `true`e, facoltativamente impostando il [`TabbedPage.HeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) proprietà associata una [`Size`](xref:Xamarin.Forms.Size) valore:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" IconImageSource="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" IconImageSource="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" IconImageSource="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
  {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", IconImageSource = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", IconImageSource = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", IconImageSource = "contacts.png" });
  }
}
```

Il `TabbedPage.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [`TabbedPage.SetHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean)) metodo, nelle [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene utilizzato per attivare o disattivare la icone di intestazione. Il [`TabbedPage.SetHeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size)) metodo facoltativamente specifica le dimensioni dell'icona intestazione con un [`Size`](xref:Xamarin.Forms.Size) valore.

Inoltre, il `TabbedPage` classe nel `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` dello spazio dei nomi ha anche un [`EnableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) metodo che abilita le icone di intestazione, un [`DisableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) metodo che consente di disattivare le icone di intestazione, e una [`IsHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) metodo che restituisce un `boolean` valore che indica se sono abilitate le icone di intestazione.

Il risultato è tale pagina è possibile visualizzare le icone in una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra degli strumenti con le dimensioni dell'icona facoltativamente impostata in base alle esigenze:

![Specifiche della piattaforma icone abilitate TabbedPage](tabbedpage-icons-images/tabbedpage-icons.png "TabbedPage icone abilitate specifico della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
