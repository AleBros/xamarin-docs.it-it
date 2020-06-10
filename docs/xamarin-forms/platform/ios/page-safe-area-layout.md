---
title: "Guida per il layout dell'area sicura in iOS" Descrizione: "le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma iOS specifica che garantisce che il contenuto della pagina sia posizionato in un'area della schermata sicura per tutti i dispositivi che usano iOS 11 e versioni successive.
ms. prod: Novell MS. AssetID: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="safe-area-layout-guide-on-ios"></a>Guida per il layout dell'area sicura in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma iOS viene usato per garantire che il contenuto della pagina sia posizionato in un'area dello schermo sicura per tutti i dispositivi che usano iOS 11 e versioni successive. In particolare, consente di verificare che il contenuto non venga troncato da angoli di dispositivo arrotondati, dall'indicatore Home o dall'alloggiamento del sensore su un iPhone X. Viene utilizzato in XAML impostando la `Page.UseSafeArea` proprietà associata su un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

Il `Page.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Page.SetUseSafeArea` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, controlla se è abilitata la guida per il layout dell'area sicura.

Il risultato è che il contenuto della pagina può essere posizionato in un'area della schermata sicura per tutti gli iPhone:

[![](page-safe-area-images/safe-area-layout.png "Safe Area Layout Guide")](page-safe-area-images/safe-area-layout-large.png#lightbox "Safe Area Layout Guide")

> [!NOTE]
> L'area sicura definita da Apple viene utilizzata in Xamarin.Forms per impostare la [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) proprietà e sostituirà tutti i valori precedenti di questa proprietà impostati.

L'area Safe può essere personalizzata recuperando il relativo [`Thickness`](xref:Xamarin.Forms.Thickness) valore con il `Page.SafeAreaInsets` Metodo dallo [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi. Può quindi essere modificato come richiesto e riassegnato alla `Padding` proprietà nel costruttore della pagina o [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) eseguire l'override di:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
