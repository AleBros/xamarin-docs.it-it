---
title: "lo scorrimento rapido di ListView su Android" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. In questo articolo viene illustrato come utilizzare la piattaforma Android specifica che consente lo scorrimento rapido dei dati in un controllo ListView ".
ms. prod: Novell MS. AssetID: 37D95A2D-74AC-488A-B903-2BDD799EAA5C ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-fast-scrolling-on-android"></a>Scorrimento rapido di ListView in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per consentire lo scorrimento rapido dei dati in un [`ListView`](xref:Xamarin.Forms.ListView) . Viene utilizzato in XAML impostando la `ListView.IsFastScrollEnabled` proprietà associata su un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

Il `ListView.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `ListView.SetIsFastScrollEnabled` metodo, nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per consentire lo scorrimento rapido dei dati in un oggetto [`ListView`](xref:Xamarin.Forms.ListView) . Inoltre, il `SetIsFastScrollEnabled` metodo può essere usato per attivare o disabilitare lo scorrimento rapido chiamando il `IsFastScrollEnabled` metodo per restituire se lo scorrimento rapido è abilitato:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

Il risultato è che è possibile abilitare lo scorrimento rapido dei dati in un oggetto [`ListView`](xref:Xamarin.Forms.ListView) , che modifica le dimensioni del cursore di scorrimento:

[![](listview-fast-scrolling-images/fastscroll.png "ListView FastScroll Platform-Specific")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
