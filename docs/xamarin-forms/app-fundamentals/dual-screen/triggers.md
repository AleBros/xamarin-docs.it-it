---
title: Xamarin.Forms trigger a doppio schermo
description: In questo articolo viene illustrato come utilizzare i trigger a schermo doppio Xamarin.Forms per rispondere alle modifiche dell'interfaccia utente con XAML.
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78165532"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Forms trigger a doppio schermo

![](~/media/shared/preview.png "This API is currently pre-release")

Lo [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) spazio dei nomi include due trigger di stato:The namespace includes two state triggers:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)attiva una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando cambia la modalità di visualizzazione del layout associato.
- `WindowSpanModeStateTrigger`attiva una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando cambia la modalità di visualizzazione della finestra.

Per ulteriori informazioni sui trigger di stato, vedere [Trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Trigger di stato della modalità Span

Un [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) attiva [`VisualState`](xref:Xamarin.Forms.VisualState) una modifica quando cambia la modalità di estensione del layout associato. Questo trigger ha una singola proprietà associabile:This trigger has a single bindable property:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), di [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)tipo , che indica la [`VisualState`](xref:Xamarin.Forms.VisualState) modalità di intervallo a cui deve essere applicato l'oggetto .

> [!NOTE]
> Deriva [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e può pertanto associare [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) un gestore eventi all'evento.

L'esempio XAML [`Grid`](xref:Xamarin.Forms.Grid) seguente `SpanModeStateTrigger` mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, gli stati [`Grid`](xref:Xamarin.Forms.Grid) di visualizzazione vengono impostati su un oggetto. Il colore di `Grid` sfondo dell'oggetto è verde quando viene visualizzato un solo riquadro, è rosso quando i riquadri vengono visualizzati affiancati ed è viola quando i riquadri vengono visualizzati dall'alto in basso.

## <a name="window-span-mode-state-trigger"></a>Trigger di stato della modalità di estensione della finestra

Un `WindowSpanModeStateTrigger` trigger [`VisualState`](xref:Xamarin.Forms.VisualState) a una modifica quando cambia la modalità di estensione della finestra. Questo trigger ha una singola proprietà associabile:This trigger has a single bindable property:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), di [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)tipo , che indica la [`VisualState`](xref:Xamarin.Forms.VisualState) modalità di intervallo a cui deve essere applicato l'oggetto .

> [!NOTE]
> Deriva `WindowSpanModeStateTrigger` dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e può pertanto associare [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) un gestore eventi all'evento.

L'esempio XAML [`Grid`](xref:Xamarin.Forms.Grid) seguente `WindowSpanModeStateTrigger` mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, gli stati [`Grid`](xref:Xamarin.Forms.Grid) di visualizzazione vengono impostati su un oggetto. Il colore di `Grid` sfondo dell'oggetto è rosso quando viene visualizzato un solo riquadro, è verde quando i riquadri vengono visualizzati affiancati ed è giallo quando i riquadri vengono visualizzati dall'alto in basso.

## <a name="related-links"></a>Collegamenti correlati

- [Trigger Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
