---
title: funzionalità specifiche della piattaforma iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare il iOS-funzionalità specifiche della piattaforma incorporate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/06/2018
ms.openlocfilehash: 98d4ce241c01bd09c68d86c583f12fdc7a11db0f
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "39175190"
---
# <a name="ios-platform-specifics"></a>funzionalità specifiche della piattaforma iOS

_Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare il iOS-funzionalità specifiche della piattaforma incorporate in xamarin. Forms._

## <a name="visualelements"></a>VisualElements

In iOS, le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni, pagine e layout di xamarin. Forms:

- Offusca il supporto per qualunque [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [applicando Blur](#blur).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [disattivazione modalità colore Legacy](#legacy-color-mode).
- Abilitare un'ombreggiatura in una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [l'abilitazione di un'ombreggiatura](#drop-shadow).

<a name="blur" />

### <a name="applying-blur"></a>Applicazione di sfocatura

Questo specifico della piattaforma consente di sfocare il contenuto a più livelli di sotto e vengono utilizzato in XAML, impostando il [ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) proprietà associata a un valore del [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

Il `BoxView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usato per applicare l'effetto di sfocatura, con la [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumerazione che fornisce quattro valori: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), e [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

Il risultato è che un oggetto specificato [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) viene applicato al [ `BoxView` ](xref:Xamarin.Forms.BoxView) dell'istanza, quali sfocature il [ `Image` ](xref:Xamarin.Forms.Image) a più livelli sottostanti:

![](ios-images/blur-effect.png "Specifiche della piattaforma effetto di sfocatura")

> [!NOTE]
> Quando si aggiunge un effetto di sfocatura da un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), gli eventi di tocco verranno comunque ricevuti dal `VisualElement`.

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>La disabilitazione della modalità di colore precedente

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando la [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la visualizzazione sostituiranno i colori impostati dall'utente con i colori nativo predefinito per lo stato disabilitato. Per garantire la compatibilità, questa modalità legacy colore rimane con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questo specifico della piattaforma disabilita questa modalità colore legacy, in modo che i colori impostati su una vista dall'utente rimangano anche quando la visualizzazione è disabilitata. Vengono utilizzati in XAML, impostando il [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) metodo può essere utilizzato da restituire se la modalità legacy è disabilitata.

Il risultato è che può essere disabilitata la modalità legacy, in modo che rimangano anche i colori impostati su una vista dall'utente quando la visualizzazione è disabilitata:

![](ios-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Quando si imposta una [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità legacy di colori viene completamente ignorata. Per altre informazioni sugli stati visual, vedere [di xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="drop-shadow" />

### <a name="enabling-a-drop-shadow"></a>Abilitazione di un'ombreggiatura

Questo specifico della piattaforma viene usato per abilitare un'ombreggiatura in una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Vengono utilizzati in XAML, impostando il [ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) proprietà associata `true`, insieme a un numero di altri facoltativo proprietà che controllano l'ombreggiatura associate:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

Il `VisualElement.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `VisualElement.SetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se un'ombreggiatura è abilitata nel `VisualElement`. Inoltre, è possibile richiamare i metodi seguenti per controllare l'ombreggiatura:

- [`SetShadowColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Color)) : imposta il colore dell'ombreggiatura. Il colore predefinito è [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- [`SetShadowOffset`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Size)) : imposta l'offset dell'ombreggiatura. L'offset viene modificato la direzione dell'ombreggiatura viene eseguito il cast e viene specificato come un [ `Size` ](xref:Xamarin.Forms.Size) valore. Il `Size` struttura valori sono espressi in unità indipendenti dal dispositivo, con il primo valore in corso la distanza a sinistra (valore negativo) o a destra (valore positivo) e il secondo valore da precedente distanza (negativo) o di sotto (positivo) . Il valore predefinito di questa proprietà è (0,0, 0,0), che comporta l'ombreggiatura viene eseguito il cast intorno a ogni lato del `VisualElement`.
- [`SetShadowOpacity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) : imposta l'opacità dell'ombreggiatura, con il cui valore è compreso nell'intervallo 0.0 (trasparente) a 1.0 (opaco). Il valore di opacità predefinita è 0,5.
- [`SetShadowRadius`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) : imposta il raggio sfocatura utilizzato per il rendering dell'ombreggiatura. Il valore del raggio predefinito è 10,0.

> [!NOTE]
> Lo stato di un'ombreggiatura è possibile eseguire query chiamando il [ `GetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOffset` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOpacity` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), e [ `GetShadowRadius` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) metodi.

Il risultato è che può essere abilitata un'ombreggiatura in una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement):

![](ios-images/drop-shadow.png "Ombreggiatura abilitato")

## <a name="views"></a>Visualizzazioni

In iOS, le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni di xamarin. Forms:

- Garantire che il testo di input si inserisce in un' [ `Entry` ](xref:Xamarin.Forms.Entry) modificando le dimensioni del carattere. Per altre informazioni, vedere [modificando le dimensioni del carattere di una voce](#adjust_font_size).
- Impostare il colore del cursore un [ `Entry` ](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [impostare il colore del cursore voce](#entry-cursorcolor).
- Impostando lo stile dei separatori in un [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [impostando lo stile dei separatori in un ListView](#listview-separatorstyle).
- Il controllo quando si verifica la selezione di elementi in un [ `Picker` ](xref:Xamarin.Forms.Picker). Per altre informazioni, vedere [controllo di selezione Item Selection](#picker_update_mode).
- L'abilitazione di [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà da impostare toccando in base a una posizione per il [ `Slider` ](xref:Xamarin.Forms.Slider) barra, anziché dover trascinare il `Slider` thumb. Per altre informazioni, vedere [l'abilitazione di un controllo Thumb dispositivo di scorrimento da spostare al tocco](#slider-updateontap).

<a name="adjust_font_size" />

### <a name="adjusting-the-font-size-of-an-entry"></a>Regolare le dimensioni del carattere di una voce

Questo specifico della piattaforma viene utilizzata per ridimensionare le dimensioni del carattere una [ `Entry` ](xref:Xamarin.Forms.Entry) per garantire che il testo immessi sia adeguato nel controllo. Vengono utilizzati in XAML, impostando il [ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

Il `Entry.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usato per aumentare le dimensioni del carattere del testo valori immessi per garantire che si adatta al [ `Entry` ](xref:Xamarin.Forms.Entry). Inoltre, il [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) classe nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` dello spazio dei nomi ha anche un [ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo che disabilita questo specifico della piattaforma, e un [ `SetAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean)) metodo che può essere utilizzato per attivare o disattivare la dimensione del carattere ridimensionamento chiamando il [ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

Il risultato è che le dimensioni del carattere i [ `Entry` ](xref:Xamarin.Forms.Entry) viene ridimensionata per garantire che il testo immessi sia adeguato nel controllo:

![](ios-images/entry-font-size.png "Regolare specifici delle voci del tipo di carattere delle dimensioni della piattaforma")

<a name="entry-cursorcolor" />

### <a name="setting-the-entry-cursor-color"></a>Impostazione del colore cursore di movimento

Questo specifico della piattaforma imposta il colore del cursore un' [ `Entry` ](xref:Xamarin.Forms.Entry) un colore specificato. Vengono utilizzati in XAML, impostando il [ `Entry.CursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) la proprietà associabile per una [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

Il `Entry.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `Entry.SetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},Xamarin.Forms.Color)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, imposta il colore del cursore per un determinato [ `Color` ](xref:Xamarin.Forms.Color). Inoltre, il [ `Entry.GetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.GetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo può essere usato per recuperare il colore corrente del cursore.

Il risultato è che il colore del cursore in un [ `Entry` ](xref:Xamarin.Forms.Entry) può essere impostato su uno specifico [ `Color` ](xref:Xamarin.Forms.Color):

![](ios-images/entry-cursorcolor.png "Colore cursore di movimento")

<a name="listview-separatorstyle" />

### <a name="setting-the-separator-style-on-a-listview"></a>Impostando lo stile dei separatori in un ListView

Questo specifico della piattaforma controlla se il separatore tra le celle una [ `ListView` ](xref:Xamarin.Forms.ListView) Usa l'intera larghezza del `ListView`. Vengono utilizzati in XAML, impostando il [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) proprietà associata a un valore del [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se il separatore tra le celle nel [ `ListView` ](xref:Xamarin.Forms.ListView) Usa la versione completa larghezza del `ListView`, con il [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumerazione che fornisce due valori possibili:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) : indica il comportamento di separatore iOS predefinite. Questo è il comportamento predefinito in xamarin. Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) : indica che verranno disegnati un margine di separatori di `ListView` a altro.

Il risultato è che un oggetto specificato [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valore viene applicato per il [ `ListView` ](xref:Xamarin.Forms.ListView), che determina la larghezza del separatore tra le celle:

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle specifiche della piattaforma")

> [!NOTE]
> Dopo aver impostato lo stile dei separatori `FullWidth`, non può essere modificato a `Default` in fase di esecuzione.

<a name="picker_update_mode" />

### <a name="controlling-picker-item-selection"></a>Controllare la selezione di elementi di selezione

Questo specifico della piattaforma controlla quando si verifica la selezione di elementi in un [ `Picker` ](xref:Xamarin.Forms.Picker), che consente all'utente di specificare che la selezione di elementi si verifica quando si esplorano gli elementi del controllo o solo una volta il **eseguita** pulsante è premuto. Vengono utilizzati in XAML, impostando il `Picker.UpdateMode` proprietà associata a un valore del `UpdateMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il `Picker.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Picker.SetUpdateMode` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per controllare quando si verifica la selezione di elementi, con il `UpdateMode` enumerazione che fornisce due valori possibili:

- `Immediately` -la selezione di elementi si verifica quando l'utente torna a visualizzare gli elementi di [ `Picker` ](xref:Xamarin.Forms.Picker). Questo è il comportamento predefinito in xamarin. Forms.
- `WhenFinished` – la selezione di elementi ha effetto solo quando l'utente ha premuto il **eseguita** pulsante il [ `Picker` ](xref:Xamarin.Forms.Picker).

Inoltre, il `SetUpdateMode` metodo può essere utilizzato per attivare o disattivare i valori di enumerazione chiamando il `UpdateMode` metodo, che restituisce l'attuale `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Il risultato è che un oggetto specificato `UpdateMode` viene applicato per il [ `Picker` ](xref:Xamarin.Forms.Picker), che controlla quando si verifica la selezione di elementi:

[![](ios-images/picker-updatemode.png "Selezione UpdateMode specifiche della piattaforma")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="slider-updateontap" />

### <a name="enabling-a-slider-thumb-to-move-on-tap"></a>Abilitazione di un controllo Thumb dispositivo di scorrimento da spostare al tocco

Questo specifico della piattaforma consente il [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà da impostare toccando in base a una posizione per il [ `Slider` ](xref:Xamarin.Forms.Slider) barra, anziché dover trascinare il `Slider` thumb. Vengono utilizzati in XAML, impostando il [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) la proprietà associabile per `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

Il `Slider.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se una scelta nel `Slider` barra imposterà il [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà. Inoltre, il [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) metodo può essere utilizzato per restituire se una scelta nel `Slider` barra imposterà il `Slider.Value` proprietà.

Il risultato è che un tocco sul [ `Slider` ](xref:Xamarin.Forms.Slider) barra è possibile spostare il `Slider` thumb e impostare il [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà:

![](ios-images/slider-updateontap.png "Aggiornamento dispositivo di scorrimento al tocco abilitata")

## <a name="pages"></a>Pages

In iOS, le funzionalità specifiche della piattaforma seguenti viene fornita per le pagine di xamarin. Forms:

- Se si nasconde il separatore barra di navigazione in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Per altre informazioni, vedere [nascondendo il separatore barra di navigazione in un NavigationPage](#navigationpage-hideseparatorbar).
- Controllare se la barra di navigazione è semitrasparente. Per altre informazioni, vedere [rendere semitrasparente barra di navigazione](#translucent_navigation_bar).
- Controllare se il testo della barra di stato di colore in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) viene regolata in modo da corrispondere la luminosità della barra di spostamento. Per altre informazioni, vedere [modificando la modalità di colore testo barra di stato](#status_bar_color_mode).
- Controllare se il titolo della pagina viene visualizzato come un titolo di grandi dimensioni nella barra di spostamento della pagina. Per altre informazioni, vedere [visualizzazione di titoli di grandi dimensioni](#large_title).
- Impostazione di visibilità della barra di stato in una [ `Page` ](xref:Xamarin.Forms.Page). Per altre informazioni, vedere [impostando la visibilità della barra di stato in una pagina](#set_status_bar_visibility).
- Garantire tale contenuto della pagina è posizionato su un'area dello schermo sicuro per tutti i dispositivi iOS. Per altre informazioni, vedere [abilitare la Guida di Layout Area sicura](#safe_area_layout).

<a name="navigationpage-hideseparatorbar" />

### <a name="hiding-the-navigation-bar-separator-on-a-navigationpage"></a>Se si nasconde la barra di spostamento separatore in un NavigationPage

Questo specifico della piattaforma nasconde la linea di separazione e sullo shadow che si trova nella parte inferiore della barra di spostamento in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Vengono utilizzati in XAML, impostando il [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) la proprietà associabile per `false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

Il `NavigationPage.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se il separatore barra di navigazione è nascosto. Inoltre, il [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) metodo può essere utilizzato da restituire se il separatore barra di navigazione è nascosto.

Il risultato è che il separatore barra di navigazione in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) possono essere nascosti:

![](ios-images/navigationpage-hideseparatorbar.png "Barra di spostamento NavigationPage nascosta")

<a name="translucent_navigation_bar" />

### <a name="making-the-navigation-bar-translucent"></a>Rendere semitrasparente barra di spostamento

Questo specifico della piattaforma viene utilizzata per modificare la trasparenza della barra di spostamento e vengono utilizzato in XAML, impostando il [ `NavigationPage.IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) proprietà associata una `boolean` valore:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

Il `NavigationPage.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `NavigationPage.EnableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per rendere semitrasparente barra di spostamento. Inoltre, il [ `NavigationPage` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) classe nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` dello spazio dei nomi ha anche un [ `DisableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) metodo che consente di ripristinare la barra di spostamento sul relativo stato predefinito, e un [ `SetIsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) metodo che può essere usato per attivare o disattivare la trasparenza di barra di navigazione chiamando il [ `IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) metodo:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

Il risultato è che può essere modificata la trasparenza della barra di spostamento:

![](ios-images/translucent-navigation-bar.png "Barra di spostamento semitrasparente specifiche della piattaforma")

<a name="status_bar_color_mode" />

### <a name="adjusting-the-status-bar-text-color-mode"></a>Modifica della modalità colore testo barra di stato

Questo controlli specifici della piattaforma se il testo della barra di stato di colore in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) viene regolata in modo da corrispondere la luminosità della barra di spostamento. Vengono utilizzati in XAML, impostando il [ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) proprietà associata a un valore del [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumerazione:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

Il `NavigationPage.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, i controlli se il testo della barra di stato dei colori nella [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) viene regolata in modo che corrisponda il luminosità della barra di spostamento con la [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumerazione che fornisce due valori possibili:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) : indica che lo stato della barra di colore del testo non deve essere regolato.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) : indica che lo stato della barra di colore del testo deve corrispondere la luminosità della barra di spostamento.

Inoltre, il [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) metodo può essere utilizzato per recuperare il valore corrente del [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumerazione che viene applicato al [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

Il risultato è che lo stato della barra di colore del testo in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) può essere regolato in modo da corrispondere la luminosità della barra di spostamento. In questo esempio, lo stato della barra di modifiche ai colori di testo come l'utente passa tra i [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) pagine di un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](ios-images/status-bar-text-color-mode.png "Barra di stato testo colore modalità specifiche della piattaforma")

<a name="large_title" />

### <a name="displaying-large-titles"></a>Visualizzazione di titoli di grandi dimensioni

Questo specifico della piattaforma viene utilizzato per visualizzare il titolo della pagina come un titolo di grandi dimensioni sulla barra di spostamento per i dispositivi che usano iOS 11 o versione successiva. Un grande titolo è allineato a sinistra Usa un carattere di dimensioni maggiori e passa a un titolo standard come l'utente inizia a scorrimento di contenuto, in modo che l'area dello schermo viene usato in modo efficiente. Tuttavia, con orientamento orizzontale, il titolo verrà restituito al centro della barra di spostamento per ottimizzare il layout del contenuto. Vengono utilizzati in XAML, impostando il `NavigationPage.PrefersLargeTitles` proprietà associata una `boolean` valore:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

In alternativa può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

Il `NavigationPage.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `NavigationPage.SetPrefersLargeTitle` metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, controlla se sono abilitati i titoli di grandi dimensioni.

Condizione che i titoli di grandi dimensioni sono abilitati nel [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), tutte le pagine nello stack di navigazione verranno visualizzate titoli di grandi dimensioni. Questo comportamento può essere sottoposto a override nelle pagine impostando il `Page.LargeTitleDisplay` proprietà associata a un valore di `LargeTitleDisplayMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

In alternativa, il comportamento di pagina può essere sottoposto a override dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetLargeTitleDisplay` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, controlla il comportamento di titolo di grandi dimensioni nel [ `Page` ](xref:Xamarin.Forms.Page), con il `LargeTitleDisplayMode` enumerazione fornendo tre possibili valori:

- `Always` : forza la barra di spostamento e il tipo di carattere per utilizzare il formato di grande dimensioni.
- `Automatic` -usare lo stesso stile (grande o piccolo) come l'elemento precedente nello stack di navigazione.
- `Never` : forzare l'utilizzo della barra di spostamento formato regolare di piccole dimensioni.

Inoltre, il `SetLargeTitleDisplay` metodo può essere utilizzato per attivare o disattivare i valori di enumerazione chiamando il `LargeTitleDisplay` metodo, che restituisce l'attuale `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

Il risultato è che un oggetto specificato `LargeTitleDisplayMode` viene applicato per il [ `Page` ](xref:Xamarin.Forms.Page), che controlla il comportamento di titolo di grandi dimensioni:

![](ios-images/large-title.png "Specifiche della piattaforma effetto di sfocatura")

<a name="set_status_bar_visibility" />

### <a name="setting-the-status-bar-visibility-on-a-page"></a>Impostare la barra di stato visibilità in una pagina

Questo specifico della piattaforma usato per impostare la visibilità della barra di stato in una [ `Page` ](xref:Xamarin.Forms.Page), e include la possibilità di controllare come la barra di stato entra o esce dal `Page`. Vengono utilizzati in XAML mediante l'impostazione di `Page.PrefersStatusBarHidden` proprietà associata a un valore del `StatusBarHiddenMode` enumerazione e, facoltativamente, il `Page.PreferredStatusBarUpdateAnimation` proprietà associata a un valore del `UIStatusBarAnimation` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetPrefersStatusBarHidden` metodo, nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` dello spazio dei nomi, viene usato per impostare la visibilità della barra di stato in un [ `Page` ](xref:Xamarin.Forms.Page) specificando uno del `StatusBarHiddenMode` valori di enumerazione: `Default`, `True` , o `False`. Il `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` i valori impostati indipendentemente dall'orientamento del dispositivo, la visibilità della barra di stato e il `StatusBarHiddenMode.Default` valore nasconde la barra di stato in un ambiente verticalmente compact.

Il risultato è che la visibilità della barra di stato in una [ `Page` ](xref:Xamarin.Forms.Page) può essere impostato:

![](ios-images/hide-status-bar.png "Barra di stato visibilità specifico della piattaforma")

> [!NOTE]
> In un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), specificato `StatusBarHiddenMode` valore di enumerazione verrà aggiornato anche la barra di stato di tutte le pagine figlio. In tutti gli altri [ `Page` ](xref:Xamarin.Forms.Page)-tipi derivati, l'oggetto specificati `StatusBarHiddenMode` valore di enumerazione verrà aggiornati solo la barra di stato nella pagina corrente.

Il `Page.SetPreferredStatusBarUpdateAnimation` metodo viene utilizzato per impostare la modalità barra di stato entra o esce dal [ `Page` ](xref:Xamarin.Forms.Page) specificando uno dei `UIStatusBarAnimation` valori di enumerazione: `None`, `Fade`, o `Slide`. Se il `Fade` oppure `Slide` viene specificato il valore di enumerazione, una seconda 0,25 animazione viene eseguita come la barra di stato entra o esce dal `Page`.

<a name="safe_area_layout" />

### <a name="enabling-the-safe-area-layout-guide"></a>Abilitare la Guida di Layout Area sicura

Questo specifico della piattaforma viene usato per assicurarsi che il contenuto della pagina è posizionato su un'area dello schermo sicuro per tutti i dispositivi che usano iOS 11 e versioni successive. In particolare, può risultare utile per verificare che il contenuto non viene ritagliato angoli arrotondati dispositivo, l'indicatore principale o alloggiamento del sensore in un iPhone X. Vengono utilizzati in XAML, impostando il `Page.UseSafeArea` proprietà associata una `boolean` valore:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetUseSafeArea` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, controlla se è abilitata la Guida per il layout area sicura.

Il risultato è che il contenuto della pagina può essere posizionato su un'area dello schermo sicuro per tutti i dispositivi iPhone:

[![](ios-images/safe-area-layout.png "Guida Layout Area sicura")](ios-images/safe-area-layout-large.png#lightbox "Guida Layout Area sicura")

> [!NOTE]
> Area di sicurezza definita da Apple usato in xamarin. Forms per impostare il [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) proprietà che avrà la precedenza eventuali valori precedenti di questa proprietà che sono stati impostati.

Area sicura può essere personalizzato tramite il recupero dei relativi [ `Thickness` ](xref:Xamarin.Forms.Thickness) valore con il `Page.SafeAreaInsets` metodo dal [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi. Può quindi essere modificato come richiesto e riassegnato al `Padding` proprietà nel costruttore della pagina oppure [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) eseguire l'override:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="layouts"></a>Layout

In iOS, le funzionalità specifiche della piattaforma seguenti viene fornita per il layout di xamarin. Forms:

- Controllare se un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gestisce un gesto tocco o passarlo al relativo contenuto. Per altre informazioni, vedere [ritardare i tocchi contenuto in un ScrollView](#delay_content_touches).

<a name="delay_content_touches" />

### <a name="delaying-content-touches-in-a-scrollview"></a>Ritardante tocchi contenuti in un ScrollView

Un timer implicito viene attivato quando un gesto tocco viene avviata in un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) in iOS e `ScrollView` decide, in base l'azione dell'utente all'interno dell'intervallo del timer, se deve gestire il movimento o passarlo al relativo contenuto. Per impostazione predefinita, iOS `ScrollView` tocchi contenuto ritardi, ma questo può causare problemi in alcuni casi con il `ScrollView` contenuto non vincere il movimento in un momento opportuno. Di conseguenza, questo controlli specifici della piattaforma se un `ScrollView` gestisce un gesto tocco o passarlo al relativo contenuto. Vengono utilizzati in XAML, impostando il `ScrollView.ShouldDelayContentTouches` proprietà associata una `boolean` valore:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

Il `ScrollView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `ScrollView.SetShouldDelayContentTouches` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per controllare se un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gestisce un gesto tocco o passarlo al relativo contenuto. Inoltre, il `SetShouldDelayContentTouches` metodo può essere utilizzato per attivare/disattivare ritardando tocchi contenuti chiamando il `ShouldDelayContentTouches` metodo restituisca se sono posticipati tocchi contenuti:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

Il risultato è che un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) possibile disabilitare ritardare la ricezione di contenuto computerizzati, pertanto, che in questo scenario il [ `Slider` ](xref:Xamarin.Forms.Slider) riceve il movimento anziché il [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) della pagina la [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](ios-images/scrollview-delay-content-touches.png "Il contenuto di ritardo ScrollView tocca specifiche della piattaforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

## <a name="application"></a>Applicazione

In iOS, le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe:

- Abilitazione di un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) in una visualizzazione a scorrimento per acquisire e condividere il movimento di zoom con la visualizzazione a scorrimento. Per altre informazioni, vedere [abilitazione di riconoscimento del movimento di zoom simultanee](#simultaneous-pan-gesture).

<a name="simultaneous-pan-gesture" />

### <a name="enabling-simultaneous-pan-gesture-recognition"></a>Abilitazione di riconoscimento del movimento di zoom simultanee

Quando un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) collegata a una visualizzazione all'interno di una visualizzazione a scorrimento, tutti il pan movimenti acquisiti dal `PanGestureRecognizer` e non vengono passati alla visualizzazione scorrimento. Pertanto, non è più scorre la visualizzazione a scorrimento.

Questo specifico della piattaforma consente un `PanGestureRecognizer` in una visualizzazione a scorrimento per acquisire e condividere il movimento di zoom con la visualizzazione a scorrimento. Vengono utilizzati in XAML, impostando il [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) proprietà associata `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

Il `Application.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se un riconoscitore di movimento di zoom in una visualizzazione a scorrimento sarà acquisire il movimento di zoom, o acquisire e condividere il pan movimento con la visualizzazione a scorrimento. Inoltre, il [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) metodo può essere utilizzato da restituire se il movimento di zoom è condiviso con la visualizzazione a scorrimento che contiene il [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Pertanto, con questo specifico della piattaforma abilitata, quando un [ `ListView` ](xref:Xamarin.Forms.ListView) contiene una [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), entrambi i `ListView` e il `PanGestureRecognizer` riceverà il movimento di zoom e elaborarlo. Tuttavia, con questo specifico della piattaforma disabilitato, quando un `ListView` contiene un `PanGestureRecognizer`, il `PanGestureRecognizer` sarà acquisire il movimento di zoom ed elaborarlo e il `ListView` non riceverà il movimento di zoom.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare il iOS-funzionalità specifiche della piattaforma incorporate in xamarin. Forms. Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
