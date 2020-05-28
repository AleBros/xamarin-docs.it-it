---
title: Xamarin.Formstrigger a doppio schermo
description: Questo articolo illustra come usare i Xamarin.Forms trigger a doppio schermo per rispondere alle modifiche dell'interfaccia utente con XAML.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: df10327b2ac12d2b119f1ab558d7f27e8c319507
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131196"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Formstrigger a doppio schermo

![](~/media/shared/preview.png "This API is currently pre-release")

Lo [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) spazio dei nomi include due trigger di stato:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)attiva una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando viene modificata la modalità di visualizzazione del layout associato.
- `WindowSpanModeStateTrigger`attiva una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando viene modificata la modalità di visualizzazione della finestra.

Per ulteriori informazioni sui trigger di stato, vedere [trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Trigger stato modalità span

Un oggetto [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) attiva una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando viene modificata la modalità di estensione del layout associato. Questo trigger ha una singola proprietà associabile:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), di tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) , che indica la modalità di estensione alla quale [`VisualState`](xref:Xamarin.Forms.VisualState) deve essere applicato l'oggetto.

> [!NOTE]
> [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)Deriva dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e quindi può alleghire un gestore eventi all' [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) evento.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`Grid`](xref:Xamarin.Forms.Grid) che include `SpanModeStateTrigger` oggetti:

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

In questo esempio gli Stati di visualizzazione vengono impostati in un [`Grid`](xref:Xamarin.Forms.Grid) oggetto. Il colore di sfondo di `Grid` è verde quando viene visualizzato un solo riquadro, è rosso quando i riquadri vengono visualizzati affiancati e è viola quando i riquadri vengono visualizzati in alto a sinistra.

## <a name="window-span-mode-state-trigger"></a>Trigger stato modalità span finestra

Un oggetto `WindowSpanModeStateTrigger` attiva una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando viene modificata la modalità span della finestra. Questo trigger ha una singola proprietà associabile:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), di tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) , che indica la modalità di estensione alla quale [`VisualState`](xref:Xamarin.Forms.VisualState) deve essere applicato l'oggetto.

> [!NOTE]
> `WindowSpanModeStateTrigger`Deriva dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e quindi può alleghire un gestore eventi all' [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) evento.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`Grid`](xref:Xamarin.Forms.Grid) che include `WindowSpanModeStateTrigger` oggetti:

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

In questo esempio gli Stati di visualizzazione vengono impostati in un [`Grid`](xref:Xamarin.Forms.Grid) oggetto. Il colore di sfondo di `Grid` è rosso quando viene visualizzato un solo riquadro, è verde quando i riquadri vengono visualizzati affiancati e è giallo quando i riquadri vengono visualizzati in alto a sinistra.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsTrigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsGestione stato di visualizzazione](~/xamarin-forms/user-interface/visual-state-manager.md)
