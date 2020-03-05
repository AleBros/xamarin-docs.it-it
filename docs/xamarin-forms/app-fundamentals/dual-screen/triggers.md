---
title: Trigger dual screen per Novell. Forms
description: Questo articolo illustra come usare i trigger Novell. Forms dual screen per rispondere alle modifiche dell'interfaccia utente con XAML.
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165532"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Trigger dual screen per Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Lo spazio dei nomi [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) include due trigger di stato:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) attiva una modifica [`VisualState`](xref:Xamarin.Forms.VisualState) quando viene modificata la modalità di visualizzazione del layout associato.
- `WindowSpanModeStateTrigger` attiva una modifica [`VisualState`](xref:Xamarin.Forms.VisualState) quando viene modificata la modalità di visualizzazione della finestra.

Per ulteriori informazioni sui trigger di stato, vedere [trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Trigger stato modalità span

Un [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) attiva una modifica [`VisualState`](xref:Xamarin.Forms.VisualState) quando viene modificata la modalità di estensione del layout associato. Questo trigger ha una singola proprietà associabile:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), di tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), che indica la modalità di intervallo a cui applicare il [`VisualState`](xref:Xamarin.Forms.VisualState) .

> [!NOTE]
> Il [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e pertanto può alleghire un gestore eventi all'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`Grid`](xref:Xamarin.Forms.Grid) che include `SpanModeStateTrigger` oggetti:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="GridSingle">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridWide">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridTall">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Tall" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Purple" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>
```

In questo esempio gli Stati di visualizzazione vengono impostati su un oggetto [`Grid`](xref:Xamarin.Forms.Grid) . Il colore di sfondo del `Grid` è verde quando viene visualizzato un solo riquadro, è rosso quando i riquadri vengono visualizzati affiancati e è viola quando i riquadri vengono visualizzati in alto a sinistra.

## <a name="window-span-mode-state-trigger"></a>Trigger stato modalità span finestra

Un `WindowSpanModeStateTrigger` attiva una modifica [`VisualState`](xref:Xamarin.Forms.VisualState) quando viene modificata la modalità span della finestra. Questo trigger ha una singola proprietà associabile:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), di tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), che indica la modalità di intervallo a cui applicare il [`VisualState`](xref:Xamarin.Forms.VisualState) .

> [!NOTE]
> Il `WindowSpanModeStateTrigger` deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e pertanto può alleghire un gestore eventi all'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`Grid`](xref:Xamarin.Forms.Grid) che include `WindowSpanModeStateTrigger` oggetti:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="NotSpanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Spanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
                <VisualState x:Name="Tall">
                    <VisualState.StateTriggers>
                        <dualScreen:WindowSpanModeStateTrigger SpanMode="Tall" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor" Value="Yellow" />
                    </VisualState.Setters>
                </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>    
```

In questo esempio gli Stati di visualizzazione vengono impostati su un oggetto [`Grid`](xref:Xamarin.Forms.Grid) . Il colore di sfondo del `Grid` è rosso quando viene visualizzato un solo riquadro, è verde quando i riquadri vengono visualizzati affiancati e è giallo quando i riquadri vengono visualizzati in alto a sinistra.

## <a name="related-links"></a>Collegamenti correlati

- [Trigger di Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestore dello stato di visualizzazione di Novell. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
