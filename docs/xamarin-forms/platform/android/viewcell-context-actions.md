---
title: Azioni di contesto ViewCell in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma Android che Abilita le azioni del contesto ViewCell in modalità legacy.
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: b040c7c5b7f132b0832469efba91dd89d6b2ddbd
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697430"
---
# <a name="viewcell-context-actions-on-android"></a>Azioni di contesto ViewCell in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Per impostazione predefinita, da Xamarin.Forms 4,3, quando un [`ViewCell`](xref:Xamarin.Forms.ViewCell) in un'applicazione Android definisce le azioni di contesto per ogni elemento in una [`ListView`](xref:Xamarin.Forms.ListView), il menu azioni del contesto viene aggiornato quando l'elemento selezionato nel `ListView` viene modificato. Tuttavia, nelle versioni precedenti di Xamarin.Forms il menu azioni del contesto non è stato aggiornato e questo comportamento viene definito `ViewCell` modalità legacy. Questa modalità legacy può comportare un comportamento errato se un `ListView` utilizza un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) per impostare la relativa `ItemTemplate` da oggetti [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definiscono azioni di contesto diverse.

Questa specifica per la piattaforma Android consente la [`ViewCell`](xref:Xamarin.Forms.ViewCell) modalità legacy del menu azioni del contesto, per la compatibilità con le versioni precedenti, in modo che il menu azioni del contesto non venga aggiornato quando viene modificato l'elemento selezionato in una [`ListView`](xref:Xamarin.Forms.ListView) . Viene utilizzato in XAML impostando la proprietà associabile `ViewCell.IsContextActionsLegacyModeEnabled` su `true`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, è possibile C# usare l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

Il metodo `ViewCell.On<Android>` specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il metodo `ViewCell.SetIsContextActionsLegacyModeEnabled`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , viene utilizzato per abilitare il menu azioni del contesto [`ViewCell`](xref:Xamarin.Forms.ViewCell) modalità legacy, in modo che il menu azioni del contesto non venga aggiornato quando viene modificato l'elemento selezionato in un [`ListView`](xref:Xamarin.Forms.ListView) . Inoltre, è possibile usare il metodo `ViewCell.GetIsContextActionsLegacyModeEnabled` per restituire se è abilitata la modalità legacy delle azioni di contesto.

Gli screenshot seguenti illustrano [`ViewCell`](xref:Xamarin.Forms.ViewCell) modalità legacy delle azioni di contesto abilitata:

![Screenshot della modalità legacy di ViewCell abilitata, in Android](viewcell-context-actions-images/legacy-mode-enabled.png "Modalità legacy ViewCell abilitata")

In questa modalità, le voci di menu azione contesto visualizzate sono identiche per la cella 1 e la cella 2, nonostante le diverse voci di menu di scelta rapida definite per la cella 2.

Gli screenshot seguenti illustrano [`ViewCell`](xref:Xamarin.Forms.ViewCell) modalità legacy delle azioni di contesto disabilitata, che è il comportamento predefinito di Xamarin.Forms:

![Screenshot della modalità legacy ViewCell disabilitata, in Android](viewcell-context-actions-images/legacy-mode-disabled.png "Modalità legacy ViewCell disabilitata")

In questa modalità vengono visualizzate le voci di menu di azione del contesto corrette per la cella 1 e la cella 2.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
