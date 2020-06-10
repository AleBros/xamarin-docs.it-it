---
title: "MasterDetailPage Shadow in iOS" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se alla pagina dei dettagli di un MasterDetailPage è applicata un'ombreggiatura, quando viene rivelata la pagina master ".
ms. prod: Novell MS. AssetID: FB907EA2-C00A-43A5-84B1-A43584C867A5 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/05/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="masterdetailpage-shadow-on-ios"></a>MasterDetailPage Shadow in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma controlla se alla pagina dei dettagli di un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) è applicata un'ombreggiatura, quando viene rivelata la pagina master. Viene utilizzato in XAML impostando la `MasterDetailPage.ApplyShadow` proprietà associabile su `true` :

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSMasterDetailPageCS : MasterDetailPage
{
    public iOSMasterDetailPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

Il `MasterDetailPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `MasterDetailPage.SetApplyShadow` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare se alla pagina dei dettagli di un oggetto è [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) applicata un'ombreggiatura, quando viene rivelata la pagina master. Inoltre, il `GetApplyShadow` metodo può essere utilizzato per determinare se l'ombreggiatura viene applicata alla pagina di dettaglio di un oggetto `MasterDetailPage` .

Il risultato è che alla pagina dei dettagli di un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) può essere applicata un'ombreggiatura, quando viene rivelata la pagina master:

[![Screenshot di un MasterDetailPage con e senza ombreggiatura](masterdetailpage-shadow-images/shadow.png "MasterDetailPage con e senza ombreggiatura")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage con e senza ombreggiatura")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
