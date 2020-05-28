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
ms.openlocfilehash: 053697921f1adacc102e9e9bee9dd17f8d44526b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128557"
---
# <a name="viewcell-context-actions-on-android"></a>Azioni di contesto ViewCell in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Per impostazione predefinita Xamarin.Forms , da 4,3, quando un [`ViewCell`](xref:Xamarin.Forms.ViewCell) in un'applicazione Android definisce azioni di contesto per ogni elemento in un [`ListView`](xref:Xamarin.Forms.ListView) , il menu azioni del contesto viene aggiornato quando l'elemento selezionato in viene `ListView` modificato. Tuttavia, nelle versioni precedenti del Xamarin.Forms menu azioni del contesto non è stato aggiornato e questo comportamento viene definito `ViewCell` modalità legacy. Questa modalità legacy può causare un comportamento errato se un oggetto `ListView` utilizza un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) per impostarne `ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) gli oggetti da che definiscono azioni di contesto diverse.

Questa specifica piattaforma Android consente la [`ViewCell`](xref:Xamarin.Forms.ViewCell) modalità legacy del menu azioni del contesto, per la compatibilità con le versioni precedenti, in modo che il menu azioni del contesto non venga aggiornato quando l'elemento selezionato in viene [`ListView`](xref:Xamarin.Forms.ListView) modificato. Viene utilizzato in XAML impostando la `ViewCell.IsContextActionsLegacyModeEnabled` proprietà associabile su `true` :

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

Il `ViewCell.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `ViewCell.SetIsContextActionsLegacyModeEnabled` metodo, nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene utilizzato per abilitare la [`ViewCell`](xref:Xamarin.Forms.ViewCell) modalità legacy del menu azioni del contesto, in modo che il menu azioni del contesto non venga aggiornato quando l'elemento selezionato in viene [`ListView`](xref:Xamarin.Forms.ListView) modificato. Inoltre, il `ViewCell.GetIsContextActionsLegacyModeEnabled` metodo può essere utilizzato per restituire se è abilitata la modalità legacy delle azioni di contesto.

Gli screenshot seguenti mostrano le [`ViewCell`](xref:Xamarin.Forms.ViewCell) azioni di contesto modalità legacy abilitata:

![Screenshot della modalità legacy di ViewCell abilitata, in Android](viewcell-context-actions-images/legacy-mode-enabled.png "Modalità legacy ViewCell abilitata")

In questa modalità, le voci di menu azione contesto visualizzate sono identiche per la cella 1 e la cella 2, nonostante le diverse voci di menu di scelta rapida definite per la cella 2.

Gli screenshot seguenti mostrano le [`ViewCell`](xref:Xamarin.Forms.ViewCell) azioni di contesto modalità legacy disabilitata, che è il Xamarin.Forms comportamento predefinito:

![Screenshot della modalità legacy ViewCell disabilitata, in Android](viewcell-context-actions-images/legacy-mode-disabled.png "Modalità legacy ViewCell disabilitata")

In questa modalità vengono visualizzate le voci di menu di azione del contesto corrette per la cella 1 e la cella 2.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
