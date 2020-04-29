---
title: Gestore dello stato di visualizzazione di Novell. Forms
description: Utilizzare Visual State Manager per apportare modifiche agli elementi XAML in base agli Stati di visualizzazione impostati dal codice.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: c6930f3361394b04e90083594e2343b50dac64ab
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517505"
---
# <a name="xamarinforms-visual-state-manager"></a>Gestore dello stato di visualizzazione di Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilizzare Visual State Manager per apportare modifiche agli elementi XAML in base agli Stati di visualizzazione impostati dal codice._

Visual State Manager (VSM) offre un modo strutturato per apportare modifiche visive all'interfaccia utente dal codice. Nella maggior parte dei casi, l'interfaccia utente dell'applicazione è definita in XAML e questo codice XAML include il markup che descrive il modo in cui il gestore dello stato di visualizzazione influiscono sugli oggetti visivi dell'interfaccia utente.

In VSM è stato introdotto il concetto di _stati visivi_. Una vista Novell. Forms, ad `Button` esempio, può avere diversi aspetti visivi a seconda dello stato &mdash; sottostante, che sia disabilitata o premuta o abbia lo stato attivo per l'input. Si tratta dello stato del pulsante.

Gli Stati di visualizzazione vengono raccolti nei _gruppi di stati visivi_. Tutti gli stati visivi all'interno di un gruppo di Stati di visualizzazione si escludono a vicenda. Gli stati visivi e i gruppi di stati visivi vengono identificati da semplici stringhe di testo.

Il gestore dello stato di visualizzazione Novell. Forms definisce un gruppo di Stati di visualizzazione denominato "CommonStates" con gli Stati di visualizzazione seguenti:

- "Normal"
- Disabilitato
- Con stato attivo
- Selezionato

Questo gruppo di Stati di visualizzazione è supportato per tutte le classi che [`VisualElement`](xref:Xamarin.Forms.VisualElement)derivano da, che è [`View`](xref:Xamarin.Forms.View) la [`Page`](xref:Xamarin.Forms.Page)classe di base per e.

È anche possibile definire i propri gruppi di stati visivi e gli stati visivi, come illustrato in questo articolo.

> [!NOTE]
> Gli sviluppatori Novell. Forms che conoscono i [trigger](~/xamarin-forms/app-fundamentals/triggers.md) sono consapevoli del fatto che i trigger possono anche apportare modifiche agli oggetti visivi nell'interfaccia utente in base alle modifiche apportate alle proprietà di una vista o all'attivazione di eventi. Tuttavia, l'utilizzo di trigger per gestire diverse combinazioni di queste modifiche può diventare piuttosto confuso. In passato, il gestore degli Stati di visualizzazione era stato introdotto negli ambienti basati su XAML di Windows per attenuare la confusione risultante dalle combinazioni di Stati di visualizzazione. Con VSM, gli stati visivi all'interno di un gruppo di Stati di visualizzazione si escludono sempre a vicenda. In qualsiasi momento, solo uno stato in ogni gruppo è lo stato corrente.

## <a name="common-states"></a>Stati comuni

Il gestore dello stato di visualizzazione consente di includere il markup nel file XAML in grado di modificare l'aspetto visivo di una vista se la vista è normale o disabilitata o ha lo stato attivo per l'input. Questi sono noti come _stati comuni_.

Si supponga, ad esempio, di `Entry` disporre di una visualizzazione nella pagina e che l'aspetto visivo di `Entry` venga modificato nei modi seguenti:

- Quando `Entry` `Entry` è disabilitato, l'oggetto deve avere uno sfondo rosa.
- Il `Entry` controllo deve avere uno sfondo a lime in genere.
- Il `Entry` deve espandersi fino al doppio dell'altezza normale quando presenta lo stato attivo per l'input.

È possibile aggiungere il markup VSM a una singola visualizzazione oppure è possibile definirlo in uno stile se si applica a più visualizzazioni. Le due sezioni successive descrivono questi approcci.

### <a name="vsm-markup-on-a-view"></a>Markup VSM in una vista

Per alleghi il markup VSM `Entry` a una vista, separare `Entry` prima i tag di inizio e di fine:

```xaml
<Entry FontSize="18">

</Entry>
```

Viene fornita una dimensione del carattere esplicita perché uno degli Stati utilizzerà la `FontSize` proprietà per raddoppiare le dimensioni del testo in `Entry`.

Inserire `VisualStateManager.VisualStateGroups` quindi i tag tra i tag seguenti:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)è una proprietà associabile associata definita dalla [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) classe. Per ulteriori informazioni sulle proprietà associabili associate, vedere l'articolo [proprietà associate](~/xamarin-forms/xaml/attached-properties.md). Questo è il modo `VisualStateGroups` in cui la proprietà è `Entry` associata all'oggetto.

La `VisualStateGroups` proprietà è di tipo [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList), che è una raccolta di [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) oggetti. All'interno `VisualStateManager.VisualStateGroups` dei tag inserire una coppia di `VisualStateGroup` tag per ogni gruppo di Stati di visualizzazione che si desidera includere:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Si noti che `VisualStateGroup` il tag ha `x:Name` un attributo che indica il nome del gruppo. La `VisualStateGroup` classe definisce una `Name` proprietà che è possibile usare in alternativa:

```xaml
<VisualStateGroup Name="CommonStates">
```

È possibile usare `x:Name` o `Name` , ma non entrambi, nello stesso elemento.

La `VisualStateGroup` classe definisce una proprietà denominata [`States`](xref:Xamarin.Forms.VisualStateGroup.States), che è una raccolta di [`VisualState`](xref:Xamarin.Forms.VisualState) oggetti. `States`è la _Proprietà_ di contenuto `VisualStateGroups` di, in modo che `VisualState` sia possibile includere i `VisualStateGroup` tag direttamente tra i tag. (Le proprietà di contenuto sono descritte nell'articolo [sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)).

Il passaggio successivo consiste nell'includere una coppia di tag per ogni stato di visualizzazione in tale gruppo. È anche possibile identificarli usando `x:Name` o `Name`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState`definisce una proprietà denominata [`Setters`](xref:Xamarin.Forms.VisualState.Setters), che è una raccolta di [`Setter`](xref:Xamarin.Forms.Setter) oggetti. Si tratta degli stessi `Setter` oggetti utilizzati in un [`Style`](xref:Xamarin.Forms.Style) oggetto.

`Setters`_non_ è la proprietà di contenuto `VisualState`di, pertanto è necessario includere i tag dell'elemento Property per `Setters` la proprietà:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

È ora possibile inserire uno o più `Setter` oggetti tra ogni coppia di `Setters` tag. Si tratta degli `Setter` oggetti che definiscono gli Stati di visualizzazione descritti in precedenza:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Ogni `Setter` tag indica il valore di una particolare proprietà quando lo stato è corrente. Qualsiasi proprietà a cui fa riferimento `Setter` un oggetto deve essere supportata da una proprietà associabile.

Un markup simile a questo è la base della pagina **VSM nella visualizzazione** del programma di esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . La pagina include tre `Entry` Visualizzazioni, ma solo la seconda è associata al markup VSM:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Si noti che la `Entry` seconda dispone anche `DataTrigger` di come parte della `Trigger` relativa raccolta. In questo modo `Entry` l'oggetto viene disabilitato fino a quando un elemento `Entry`non viene digitato nel terzo. Di seguito è illustrata la pagina all'avvio eseguito su iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP):

[![VSM sulla vista: disabilitato](vsm-images/VsmOnViewDisabled.png "VSM sulla visualizzazione-disabilitato")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

Lo stato di visualizzazione corrente è "disabilitato", quindi lo sfondo `Entry` della seconda è rosa sulle schermate iOS e Android. L'implementazione di UWP `Entry` di non consente l'impostazione del colore di sfondo `Entry` quando è disabilitata.

Quando si immette un testo nel terzo `Entry`, il secondo `Entry` passa allo stato "normale" e lo sfondo è ora verde:

[![VSM di visualizzazione: normale](vsm-images/VsmOnViewNormal.png "VSM di visualizzazione-normale")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando si tocca il secondo `Entry`, si ottiene lo stato attivo per l'input. Passa allo stato "focused" e si espande fino al doppio dell'altezza:

[![VSM sulla vista: con stato attivo](vsm-images/VsmOnViewFocused.png "VSM su Vista-con stato attivo")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Si noti che `Entry` non mantiene lo sfondo di limetta quando ottiene lo stato attivo per l'input. Poiché il gestore dello stato di visualizzazione passa tra gli Stati di visualizzazione, le proprietà impostate dallo stato precedente vengono annullate. Tenere presente che gli Stati di visualizzazione si escludono a vicenda. Lo stato "normale" non significa solo che `Entry` è abilitato. Significa che è abilitato `Entry` e non ha lo stato attivo per l'input.

Se si vuole che `Entry` il disponga di uno sfondo di limetta nello stato "focused", aggiungerne un altro `Setter` allo stato di visualizzazione:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Per il corretto funzionamento `Setter` di questi oggetti, un `VisualStateGroup` deve contenere `VisualState` oggetti per tutti gli Stati del gruppo. Se è presente uno stato di visualizzazione che non contiene `Setter` oggetti, includerlo comunque come tag vuoto:

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Markup di Visual State Manager in uno stile

Spesso è necessario condividere lo stesso markup di Visual State Manager tra due o più viste. In questo caso, è opportuno inserire il markup in una `Style` definizione.

Di seguito è riportato il `Style` implicito `Entry` esistente per gli elementi nella pagina **VSM nella visualizzazione** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Aggiungere `Setter` i tag per `VisualStateManager.VisualStateGroups` la proprietà associabile associata:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

La proprietà Content per `Setter` è `Value`, pertanto il valore della `Value` proprietà può essere specificato direttamente all'interno di tali tag. Questa proprietà è di tipo `VisualStateGroupList`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style>
```

All'interno di questi tag è possibile includere uno `VisualStateGroup` o più oggetti:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Il resto del markup VSM è uguale a quello precedente.

Ecco la pagina di **VSM in stile** che mostra il markup completo di VSM:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Ora tutte le `Entry` visualizzazioni in questa pagina rispondono allo stesso modo agli stati visivi. Si noti anche che lo stato "con stato attivo" include `Setter` ora un secondo `Entry` che fornisce a ogni sfondo di limetta anche quando ha lo stato attivo per l'input:

[![VSM nello stile](vsm-images/VsmInStyle.png "VSM nello stile")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>Stati di visualizzazione in Novell. Forms

Nella tabella seguente sono elencati gli Stati di visualizzazione definiti in Novell. Forms:

| Class | Stati | Altre informazioni |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [Stati di visualizzazione pulsante](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CheckBox` | `IsChecked` | [Stati di visualizzazione casella di controllo](~/xamarin-forms/user-interface/checkbox.md#checkbox-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [Stati di visualizzazione CarouselView](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [Stati di visualizzazione ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `RadioButton` | `IsChecked` | [Stati di visualizzazione RadioButton](~/xamarin-forms/user-interface/radiobutton.md#radiobutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [Stati comuni](#common-states) |

È possibile accedere a ognuno di questi stati tramite il gruppo di stati `CommonStates`di visualizzazione denominato.

Inoltre, `CollectionView` implementa lo `Selected` stato. Per altre informazioni, vedere [modificare il colore dell'elemento selezionato](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Impostare lo stato su più elementi

Negli esempi precedenti, gli Stati di visualizzazione erano collegati e utilizzati su singoli elementi. Tuttavia, è anche possibile creare stati di visualizzazione collegati a un singolo elemento, ma che impostano le proprietà su altri elementi all'interno dello stesso ambito. In questo modo si evita di dover ripetere gli stati visivi in ogni elemento su cui operano gli Stati.

Il [`Setter`](xref:Xamarin.Forms.Setter) tipo dispone di `TargetName` una proprietà, di `string`tipo, che rappresenta l'elemento `Setter` di destinazione che deve essere modificato da per uno stato di visualizzazione. Quando viene `TargetName` definita la proprietà, `Setter` imposta l'oggetto `Property` dell'elemento definito in `TargetName` su: `Value`

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

In questo esempio, la `Label` `label` `TextColor` proprietà di un oggetto denominato verrà impostata `Red`su. Quando si imposta `TargetName` la proprietà è necessario specificare il percorso completo della proprietà in `Property`. Pertanto, per impostare la `TextColor` proprietà su un `Label`oggetto `Property` , viene specificato `Label.TextColor`come.

> [!NOTE]
> Qualsiasi proprietà a cui fa riferimento `Setter` un oggetto deve essere supportata da una proprietà associabile.

La pagina **VSM with Setter TargetName** nell'esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** Mostra come impostare lo stato su più elementi, da un singolo gruppo di Stati di visualizzazione. Il file XAML è costituito `StackLayout` da un `Label` oggetto contenente un `Entry`elemento, un `Button`oggetto e un oggetto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

Il `StackLayout`markup VSM è associato a. Ci sono due Stati che si escludono a vicenda, denominati "Normal" e "Pressed", `VisualState` con ogni stato contenente i tag.

Lo stato "normale" è attivo quando l' `Button` oggetto non è premuto ed è possibile immettere una risposta alla domanda:

[![VSM Setter TargetName: stato normale](vsm-images/VsmSetterTargetNameNormal.png "VSM Setter TargetName-normale")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

Lo stato "premuto" diventa attivo quando viene `Button` premuto il tasto:

[![VSM Setter TargetName: stato premuto](vsm-images/VsmSetterTargetNamePressed.png "VSM Setter TargetName-premuto")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

Il valore "Pressed `VisualState` " indica che, `Button` quando viene premuto, `Scale` la relativa proprietà verrà modificata dal valore predefinito da 1 a 0,8. Inoltre, la `Entry` `Text` proprietà della `entry` proprietà denominata sarà impostata su Parigi. Pertanto, il risultato è che, quando `Button` viene premuto, viene ridimensionato in modo da essere leggermente più piccolo e `Entry` Visualizza Parigi. Quindi, quando `Button` viene rilasciata, viene riscalato il valore predefinito di 1 e `Entry` Visualizza qualsiasi testo immesso in precedenza.

> [!IMPORTANT]
> I percorsi delle proprietà non sono attualmente supportati `Setter` negli elementi che specificano la `TargetName` proprietà.

## <a name="define-your-own-visual-states"></a>Definire stati di visualizzazione personalizzati

Ogni classe che deriva da `VisualElement` supporta gli stati comuni "normale", "con stato attivo" e "disabilitato". Inoltre, la `CollectionView` classe supporta lo stato "selezionato". Internamente, [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) la classe rileva quando viene abilitata o disabilitata oppure con stato attivo o non attivo e chiama il metodo [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) statico:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Si tratta dell'unico codice di Visual State Manager che si troverà nella `VisualElement` classe. Poiché `GoToState` viene chiamato per ogni oggetto in base a ogni classe che deriva da `VisualElement`, è possibile utilizzare il gestore dello stato di visualizzazione `VisualElement` con qualsiasi oggetto per rispondere a queste modifiche.

È interessante notare che non viene fatto riferimento in `VisualElement`modo esplicito al nome del gruppo di Stati di visualizzazione "CommonStates". Il nome del gruppo non fa parte dell'API per la gestione dello stato di visualizzazione. All'interno di uno dei due programmi di esempio illustrati finora, è possibile modificare il nome del gruppo da "CommonStates" a qualsiasi altro e il programma funzionerà comunque. Il nome del gruppo è semplicemente una descrizione generale degli Stati del gruppo. Viene implicitamente compreso che gli Stati di visualizzazione in qualsiasi gruppo si escludono a vicenda: uno stato e un solo stato sono correnti in qualsiasi momento.

Se si desidera implementare gli Stati di visualizzazione, è necessario chiamare `VisualStateManager.GoToState` dal codice. Spesso si effettuerà questa chiamata dal file code-behind della classe Page.

La pagina **convalida VSM** nell'esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** illustra come usare la gestione dello stato di visualizzazione in connessione con la convalida dell'input. Il file XAML è costituito `StackLayout` da un `Label` oggetto contenente due `Entry`elementi, un `Button`oggetto e un oggetto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

Il `StackLayout` markup VSM è associato a (denominato `stackLayout`). Ci sono due Stati che si escludono a vicenda, denominati "valid" e "valid", `VisualState` con ogni stato contenente tag.

Se l' `Entry` oggetto non contiene un numero di telefono valido, lo stato corrente è "non valido", quindi l' `Entry` oggetto ha uno sfondo rosa, il `Label` secondo è visibile e `Button` è disabilitato:

[![Convalida di VSM: stato non valido](vsm-images/VsmValidationInvalid.png "Convalida VSM-non valida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando viene immesso un numero di telefono valido, lo stato corrente diventa "valid". Il `Entry` ottiene uno sfondo a calce, `Label` il secondo scompare ed `Button` è ora abilitato.

[![Convalida di VSM: stato valido](vsm-images/VsmValidationValid.png "Convalida VSM-valida")](vsm-images/VsmValidationValid-Large.png#lightbox)

Il file code-behind è responsabile della gestione dell' `TextChanged` evento da `Entry`. Il gestore utilizza un'espressione regolare per determinare se la stringa di input è valida o meno. Il metodo nel file code-behind denominato `GoToState` chiama il metodo statico `VisualStateManager.GoToState` per: `stackLayout`

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

Si noti inoltre che `GoToState` il metodo viene chiamato dal costruttore per inizializzare lo stato. Deve essere sempre presente uno stato corrente. Nel codice, tuttavia, è presente un riferimento al nome del gruppo di Stati di visualizzazione, sebbene venga fatto riferimento in XAML come "ValidationStates" a scopo di chiarezza.

Si noti che il file code-behind deve solo prendere in considerazione l'oggetto nella pagina che definisce gli Stati di visualizzazione e chiamare `VisualStateManager.GoToState` per questo oggetto. Questo è dovuto al fatto che entrambi gli stati visivi sono destinati a più oggetti nella pagina.

Ci si potrebbe chiedere: se il file code-behind deve fare riferimento all'oggetto nella pagina che definisce gli Stati di visualizzazione, perché non è possibile accedere direttamente al file code-behind e ad altri oggetti? Certamente. Tuttavia, il vantaggio dell'uso di VSM è la possibilità di controllare il modo in cui gli elementi visivi reagiscono completamente a uno stato diverso in XAML, che consente di mantenere tutte le funzionalità di progettazione dell'interfaccia utente in un'unica posizione. In questo modo si evita di impostare l'aspetto visivo accedendo agli elementi visivi direttamente dal code-behind.

## <a name="visual-state-triggers"></a>Trigger di stato di visualizzazione

Gli Stati di visualizzazione supportano i trigger di stato, che sono un gruppo specializzato di trigger che definiscono le [`VisualState`](xref:Xamarin.Forms.VisualState) condizioni in base alle quali deve essere applicato.

I [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) trigger di stato vengono aggiunti alla raccolta di [`VisualState`](xref:Xamarin.Forms.VisualState)un oggetto. Questa raccolta può contenere un solo trigger di stato o più trigger di stato. Un [`VisualState`](xref:Xamarin.Forms.VisualState) verrà applicato quando sono attivi tutti i trigger di stato nella raccolta.

Quando si usano i trigger di stato per controllare gli Stati di visualizzazione, Novell. Forms usa le seguenti regole di precedenza [`VisualState`](xref:Xamarin.Forms.VisualState)per determinare quale trigger (e corrispondente) sarà attivo:

1. Qualsiasi trigger che deriva da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Oggetto [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivato a causa della [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) condizione soddisfatta.
1. Oggetto [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivato a causa della [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) condizione soddisfatta.

Se più trigger sono contemporaneamente attivi, ad esempio due trigger personalizzati, il primo trigger dichiarato nel markup avrà la precedenza.

Per ulteriori informazioni sui trigger di stato, vedere [trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Utilizzare Visual State Manager per il layout adattivo

Un'applicazione Novell. Forms in esecuzione su un telefono può in genere essere visualizzata in un formato verticale o orizzontale e un programma Novell. Forms in esecuzione sul desktop può essere ridimensionato in modo da assumere diverse dimensioni e proporzioni. Un'applicazione ben progettata potrebbe visualizzare il contenuto in modo diverso per questi diversi fattori di forma della pagina o della finestra.

Questa tecnica è talvolta nota come _layout adattivo_. Poiché il layout adattivo implica esclusivamente gli oggetti visivi di un programma, è un'applicazione ideale del gestore dello stato di visualizzazione.

Un semplice esempio è costituito da un'applicazione che visualizza una piccola raccolta di pulsanti che influiscono sul contenuto dell'applicazione. In modalità verticale, questi pulsanti possono essere visualizzati in una riga orizzontale nella parte superiore della pagina:

[![Layout adattivo VSM: verticale](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adattivo VSM-verticale")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

In modalità orizzontale la matrice di pulsanti può essere spostata su un lato e visualizzata in una colonna:

[![Layout adattivo VSM: orizzontale](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adattivo VSM-orizzontale")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Dall'alto verso il basso, il programma è in esecuzione in piattaforma UWP (Universal Windows Platform), Android e iOS.

La pagina **layout adattivo VSM** nell'esempio [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) definisce un gruppo denominato "OrientationStates" con due stati visivi denominati "Portrait" e "Landscape". Un approccio più complesso può essere basato su diverse larghezze di pagina o finestre.

Il markup VSM viene eseguito in quattro posizioni nel file XAML. Il `StackLayout` nome `mainStack` contiene sia il menu che il contenuto, che è un `Image` elemento. Questo `StackLayout` deve avere un orientamento verticale in modalità verticale e un orientamento orizzontale in modalità orizzontale:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">
                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">
                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">
                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>
                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

Il nome `ScrollView` `menuScroll` interno e il `StackLayout` denominato `menuStack` implementano il menu dei pulsanti. L'orientamento di questi layout è opposto a `mainStack`. Il menu deve essere orizzontale in modalità verticale e verticale in modalità orizzontale.

La quarta sezione del markup VSM è in uno stile implicito per i pulsanti. Questo markup imposta `VerticalOptions` `HorizontalOptions`le proprietà, `Margin` e specifiche per gli orientamenti verticale e orizzontale.

Il file code-behind imposta la `BindingContext` proprietà di `menuStack` su per `Button` implementare i comandi e alleghi anche un gestore all' `SizeChanged` evento della pagina:

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

Il `SizeChanged` gestore chiama `VisualStateManager.GoToState` per i due `StackLayout` elementi `ScrollView` e, quindi esegue il ciclo degli elementi figlio `menuStack` di per `VisualStateManager.GoToState` chiamare gli `Button` elementi.

Può sembrare che il file code-behind sia in grado di gestire le modifiche dell'orientamento più direttamente impostando le proprietà degli elementi nel file XAML, ma il gestore dello stato di visualizzazione è sicuramente un approccio più strutturato. Tutti gli oggetti visivi vengono conservati nel file XAML, dove diventano più semplici da esaminare, gestire e modificare.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Visual State Manager con Novell. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Novell. Forms 3,0 video di Visual State Manager**

## <a name="related-links"></a>Link correlati

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
