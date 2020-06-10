---
title: "la barra di tabulazione TabbedPage in iOS" Descrizione: "specifiche della piattaforma consente di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta la modalità di traslucidità della barra delle schede in un TabbedPage ".
ms. prod: Novell MS. AssetID: 9581AE81-9557-47AD-8B07-25A1AC5F055B ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/16/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>Barra schede TabbedPage traslucida in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare la modalità di traslucidità della barra delle schede in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Viene utilizzato in XAML impostando la `TabbedPage.TranslucencyMode` proprietà associabile su un `TranslucencyMode` valore di enumerazione:

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

Il `TabbedPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `TabbedPage.SetTranslucencyMode` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per impostare la modalità di traslucidità della barra della scheda in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) specificando uno dei `TranslucencyMode` valori di enumerazione seguenti:

- `Default`, che imposta la barra della scheda sulla relativa modalità di traslucidità predefinita. Questo è il valore predefinito per la proprietà `TabbedPage.TranslucencyMode`.
- `Translucent`, che imposta la barra della scheda come traslucida.
- `Opaque`, che imposta la barra della scheda come opaca.

Inoltre, il `GetTranslucencyMode` metodo può essere utilizzato per recuperare il valore corrente dell' `TranslucencyMode` enumerazione applicata a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

Il risultato è che è possibile impostare la modalità di traslucidità della barra scheda in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![Screenshot delle barre delle schede trasparenti e opache in iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barre delle schede trasparenti e opache")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
