---
title: Gestore dello stato di visualizzazione di Novell. Forms
description: Usare Visual State Manager per apportare modifiche agli elementi XAML in base gli stati visivi impostata dal codice.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: 0149806f3ab3772bc206cea9540a989d997c817b
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918502"
---
# <a name="xamarinforms-visual-state-manager"></a>Gestore dello stato di visualizzazione di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilizzare Visual State Manager per apportare modifiche agli elementi XAML in base agli Stati di visualizzazione impostati dal codice._

Visual State Manager (VSM) offre un modo strutturato per apportare modifiche visive all'interfaccia utente dal codice. Nella maggior parte dei casi, l'interfaccia utente dell'applicazione è definita in XAML, e questo XAML include il markup che descrive gli oggetti visivi dell'interfaccia utente di influenza Visual State Manager.

In VSM è stato introdotto il concetto di _stati visivi_. Una vista Novell. Forms, ad esempio un `Button` può avere diversi aspetti visivi a seconda dello stato sottostante &mdash; se è disabilitato o premuto oppure ha lo stato attivo per l'input. Questi sono gli stati del pulsante.

Gli Stati di visualizzazione vengono raccolti nei _gruppi di stati visivi_. Tutti gli stati visivi all'interno di un gruppo di stati di visualizzazione si escludono a vicenda. Sia gli stati visivi e i gruppi dello stato di visualizzazione vengono identificati da stringhe di testo semplice.

Il gestore dello stato di visualizzazione Novell. Forms definisce un gruppo di Stati di visualizzazione denominato "CommonStates" con gli Stati di visualizzazione seguenti:

- "Normal"
- "Disabilitata"
- Con stato "attivo"
- Selezionato

Questo gruppo di Stati di visualizzazione è supportato per tutte le classi che derivano da [`VisualElement`](xref:Xamarin.Forms.VisualElement), che è la classe di base per [`View`](xref:Xamarin.Forms.View) e [`Page`](xref:Xamarin.Forms.Page).

È anche possibile definire i propri gruppi dello stato di visualizzazione e stati di visualizzazione, come in questo articolo verranno illustrato.

> [!NOTE]
> Gli sviluppatori Novell. Forms che conoscono i [trigger](~/xamarin-forms/app-fundamentals/triggers.md) sono consapevoli del fatto che i trigger possono anche apportare modifiche agli oggetti visivi nell'interfaccia utente in base alle modifiche apportate alle proprietà di una vista o all'attivazione di eventi. Tuttavia, l'utilizzo di trigger per affrontare varie combinazioni di queste modifiche può risultare poco chiaro. In passato, il gestore di stato di visualizzazione è stata introdotta in ambienti basati su XAML di Windows per ridurre la confusione derivante da combinazioni di stati di visualizzazione. Con VSM, gli stati visivi all'interno di un gruppo di stati di visualizzazione sono sempre si escludono a vicenda. In qualsiasi momento, solo uno stato di ogni gruppo rappresenta lo stato corrente.

## <a name="common-states"></a>Stati comuni

Il gestore dello stato di visualizzazione consente di includere il markup nel file XAML in grado di modificare l'aspetto visivo di una vista se la vista è normale o disabilitata o ha lo stato attivo per l'input. Questi sono noti come _stati comuni_.

Si supponga, ad esempio, di disporre di una visualizzazione `Entry` nella pagina e che si desideri modificare l'aspetto visivo del `Entry` nei modi seguenti:

- Il `Entry` deve avere uno sfondo rosa quando il `Entry` è disabilitato.
- Il `Entry` deve avere uno sfondo di calce normalmente.
- Il `Entry` deve espandersi fino al doppio dell'altezza normale quando presenta lo stato attivo per l'input.

È possibile collegare il markup di questo tipo di migrazione a una singola visualizzazione, oppure è possibile definirlo in uno stile se si applica a più visualizzazioni. Due sezioni successive descrivono questi approcci.

### <a name="vsm-markup-on-a-view"></a>Markup di questo tipo di migrazione in una vista

Per alleghi il markup VSM a una vista `Entry`, separare prima i `Entry` in tag di inizio e di fine:

```xaml
<Entry FontSize="18">

</Entry>
```

Viene fornita una dimensione del carattere esplicita perché uno degli Stati utilizzerà la proprietà `FontSize` per raddoppiare le dimensioni del testo nel `Entry`.

Inserire quindi `VisualStateManager.VisualStateGroups` tag tra i tag seguenti:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) è una proprietà associabile associata definita dalla classe [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) . Per ulteriori informazioni sulle proprietà associabili associate, vedere l'articolo [proprietà associate](~/xamarin-forms/xaml/attached-properties.md). Questo è il modo in cui la proprietà `VisualStateGroups` viene associata all'oggetto `Entry`.

La proprietà `VisualStateGroups` è di tipo [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList), ovvero una raccolta di oggetti di [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) . All'interno dei tag `VisualStateManager.VisualStateGroups` inserire una coppia di tag `VisualStateGroup` per ogni gruppo di Stati di visualizzazione che si desidera includere:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Si noti che il tag `VisualStateGroup` dispone di un attributo `x:Name` che indica il nome del gruppo. La classe `VisualStateGroup` definisce una proprietà `Name` che è possibile usare in alternativa:

```xaml
<VisualStateGroup Name="CommonStates">
```

È possibile utilizzare `x:Name` o `Name` ma non entrambi nello stesso elemento.

La classe `VisualStateGroup` definisce una proprietà denominata [`States`](xref:Xamarin.Forms.VisualStateGroup.States), che è una raccolta di oggetti di [`VisualState`](xref:Xamarin.Forms.VisualState) . `States` è la _proprietà Content_ di `VisualStateGroups` in modo che sia possibile includere i tag di `VisualState` direttamente tra i tag `VisualStateGroup`. (Le proprietà di contenuto sono descritte nell'articolo [sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)).

Il passaggio successivo è includere una coppia di tag per ogni stato di visualizzazione in tale gruppo. È anche possibile identificarli usando `x:Name` o `Name`:

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

`VisualState` definisce una proprietà denominata [`Setters`](xref:Xamarin.Forms.VisualState.Setters), che è una raccolta di oggetti di [`Setter`](xref:Xamarin.Forms.Setter) . Si tratta dello stesso `Setter` oggetti utilizzati in un oggetto [`Style`](xref:Xamarin.Forms.Style) .

`Setters` _non_ è la proprietà content di `VisualState`, quindi è necessario includere i tag dell'elemento Property per la proprietà `Setters`:

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

È ora possibile inserire uno o più oggetti `Setter` tra ogni coppia di tag di `Setters`. Si tratta degli oggetti `Setter` che definiscono gli Stati di visualizzazione descritti in precedenza:

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

Ogni tag di `Setter` indica il valore di una particolare proprietà quando lo stato è corrente. Qualsiasi proprietà a cui fa riferimento un oggetto `Setter` deve essere supportata da una proprietà associabile.

Un markup simile a questo è la base della pagina **VSM nella visualizzazione** del programma di esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . La pagina include tre visualizzazioni `Entry`, ma solo la seconda è associata al markup VSM:

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

Si noti che il secondo `Entry` dispone anche di un `DataTrigger` come parte della raccolta di `Trigger`. In questo modo il `Entry` viene disabilitato fino a quando un elemento non viene digitato nella terza `Entry`. Ecco la pagina all'avvio in esecuzione in iOS, Android e Universal Windows Platform (UWP):

[![VSM sulla vista: disabilitato](vsm-images/VsmOnViewDisabled.png "VSM sulla visualizzazione-disabilitato")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

Lo stato di visualizzazione corrente è "disabilitato", quindi lo sfondo della seconda `Entry` è rosa sulle schermate iOS e Android. L'implementazione UWP di `Entry` non consente l'impostazione del colore di sfondo quando il `Entry` è disabilitato.

Quando si immette testo nel terzo `Entry`, il secondo `Entry` passa allo stato "normale" e lo sfondo è ora:

[![VSM di visualizzazione: normale](vsm-images/VsmOnViewNormal.png "VSM di visualizzazione-normale")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando si tocca la seconda `Entry`, ottiene lo stato attivo per l'input. Passa allo stato "Attivo" e si espande in due volte l'altezza:

[![VSM sulla vista: con stato attivo](vsm-images/VsmOnViewFocused.png "VSM su Vista-con stato attivo")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Si noti che il `Entry` non mantiene lo sfondo della calce quando ottiene lo stato attivo per l'input. Poiché Visual State Manager attiva tra gli stati visivi, non sono impostate le proprietà impostate dallo stato precedente. Tenere presente che gli stati visivi si escludono a vicenda. Lo stato "normale" non significa solo che la `Entry` è abilitata. Significa che il `Entry` è abilitato e non ha lo stato attivo per l'input.

Se si vuole che il `Entry` disponga di uno sfondo a lime nello stato "focused", aggiungere un altro `Setter` a tale stato di visualizzazione:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Per il corretto funzionamento di questi oggetti `Setter`, un `VisualStateGroup` deve contenere `VisualState` oggetti per tutti gli Stati del gruppo. Se è presente uno stato di visualizzazione che non contiene oggetti `Setter`, includerlo comunque come tag vuoto:

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Markup di gestione dello stato di visualizzazione in uno stile

È spesso necessario condividere lo stesso markup Visual State Manager tra due o più visualizzazioni. In questo caso, è opportuno inserire il markup in una definizione di `Style`.

Ecco il `Style` implicito esistente per gli elementi `Entry` nella pagina **VSM nella visualizzazione** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Aggiungere `Setter` tag per la proprietà associabile associata `VisualStateManager.VisualStateGroups`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

La proprietà Content per `Setter` è `Value`, quindi il valore della proprietà `Value` può essere specificato direttamente all'interno di tali tag. Questa proprietà è di tipo `VisualStateGroupList`:

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

All'interno di questi tag è possibile includere uno o più oggetti `VisualStateGroup`:

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

Il resto del markup VSM è identico al precedente.

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

Ora tutte le visualizzazioni `Entry` in questa pagina rispondono allo stesso modo agli stati visivi. Si noti anche che lo stato "con stato attivo" include ora una seconda `Setter` che assegna a ogni `Entry` uno sfondo a lime anche quando ha lo stato attivo per l'input:

[![VSM nello stile](vsm-images/VsmInStyle.png "VSM nello stile")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>Stati di visualizzazione in Novell. Forms

Nella tabella seguente sono elencati gli Stati di visualizzazione definiti in Novell. Forms:

| Classe | Stati | Altre informazioni |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [Stati di visualizzazione pulsante](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [Stati di visualizzazione CarouselView](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [Stati di visualizzazione ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [Stati comuni](#common-states) |

È possibile accedere a ognuno di questi stati tramite il gruppo di Stati di visualizzazione denominato `CommonStates`.

Inoltre, il `CollectionView` implementa lo stato di `Selected`. Per altre informazioni, vedere [modificare il colore dell'elemento selezionato](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Impostare lo stato su più elementi

Negli esempi precedenti, gli Stati di visualizzazione erano collegati e utilizzati su singoli elementi. Tuttavia, è anche possibile creare stati di visualizzazione collegati a un singolo elemento, ma che impostano le proprietà su altri elementi all'interno dello stesso ambito. In questo modo si evita di dover ripetere gli stati visivi in ogni elemento su cui operano gli Stati.

Il tipo di [`Setter`](xref:Xamarin.Forms.Setter) dispone di una proprietà `TargetName`, di tipo `string`, che rappresenta l'elemento di destinazione che il `Setter` per uno stato di visualizzazione deve modificare. Quando viene definita la proprietà `TargetName`, il `Setter` imposta il `Property` dell'elemento definito in `TargetName` per `Value`:

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

In questo esempio un `Label` denominato `label` avrà la proprietà `TextColor` impostata su `Red`. Quando si imposta la proprietà `TargetName` è necessario specificare il percorso completo della proprietà in `Property`. Pertanto, per impostare la proprietà `TextColor` in un `Label`, `Property` viene specificato come `Label.TextColor`.

> [!NOTE]
> Qualsiasi proprietà a cui fa riferimento un oggetto `Setter` deve essere supportata da una proprietà associabile.

La pagina **VSM with Setter TargetName** nell'esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** Mostra come impostare lo stato su più elementi, da un singolo gruppo di Stati di visualizzazione. Il file XAML è costituito da un `StackLayout` contenente un elemento `Label`, un `Entry`e un `Button`:

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

Il markup VSM è associato al `StackLayout`. Ci sono due Stati che si escludono a vicenda, denominati "Normal" e "Pressed", con ogni stato contenente `VisualState` tag.

Lo stato "normale" è attivo quando il `Button` non è premuto ed è possibile immettere una risposta alla domanda:

[![VSM Setter TargetName: stato normale](vsm-images/VsmSetterTargetNameNormal.png "VSM Setter TargetName-normale")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

Lo stato "premuto" diventa attivo quando viene premuto il `Button`:

[![VSM Setter TargetName: stato premuto](vsm-images/VsmSetterTargetNamePressed.png "VSM Setter TargetName-premuto")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

Il `VisualState` "premuto" indica che, quando viene premuto il `Button`, la relativa proprietà `Scale` verrà modificata dal valore predefinito da 1 a 0,8. Inoltre, il `Entry` denominato `entry` avrà la proprietà `Text` impostata su Parigi. Pertanto, il risultato è che, quando viene premuto il `Button`, viene ridimensionato in modo da essere leggermente più piccolo e il `Entry` Visualizza Parigi. Quindi, quando viene rilasciata la `Button` viene riscalato il valore predefinito 1 e il `Entry` Visualizza il testo immesso in precedenza.

> [!IMPORTANT]
> I percorsi delle proprietà non sono attualmente supportati negli elementi `Setter` che specificano la proprietà `TargetName`.

## <a name="define-your-own-visual-states"></a>Definire stati di visualizzazione personalizzati

Ogni classe che deriva da `VisualElement` supporta gli stati comuni "normale", "con stato attivo" e "disabilitato". Inoltre, la classe `CollectionView` supporta lo stato "selezionato". Internamente, la classe [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) rileva quando viene abilitata o disabilitata oppure con stato attivo o non attivo e chiama il metodo statico [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Si tratta dell'unico codice di Visual State Manager trovato nella classe `VisualElement`. Poiché `GoToState` viene chiamato per ogni oggetto in base a ogni classe che deriva da `VisualElement`, è possibile utilizzare il gestore dello stato di visualizzazione con qualsiasi `VisualElement` oggetto per rispondere a tali modifiche.

È interessante notare che il nome del gruppo di Stati di visualizzazione "CommonStates" non viene fatto riferimento in modo esplicito in `VisualElement`. Il nome del gruppo non è parte dell'API per il gestore di stato di visualizzazione. All'interno di uno dei valori il programma di due esempio illustrato finora, è possibile modificare il nome del gruppo da "CommonStates" in un account, e il programma continuerà a funzionare. Il nome del gruppo è semplicemente una descrizione generale degli stati in tale gruppo. In modo implicito è inteso che gli stati visivi in qualsiasi gruppo si escludono a vicenda: uno stato e un solo stato sia aggiornato in qualsiasi momento.

Se si desidera implementare gli Stati di visualizzazione, è necessario chiamare `VisualStateManager.GoToState` dal codice. In genere si apporteranno questa chiamata dal file code-behind della classe di pagina.

La pagina **convalida VSM** nell'esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** illustra come usare la gestione dello stato di visualizzazione in connessione con la convalida dell'input. Il file XAML è costituito da un `StackLayout` contenente due elementi `Label`, un `Entry`e un `Button`:

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

Il markup VSM è associato al `StackLayout` (denominato `stackLayout`). Ci sono due Stati che si escludono a vicenda, denominati "valid" e "valid", con ogni stato contenente `VisualState` tag.

Se il `Entry` non contiene un numero di telefono valido, lo stato corrente è "non valido", quindi il `Entry` ha uno sfondo rosa, il secondo `Label` è visibile e il `Button` è disabilitato:

[![Convalida di VSM: stato non valido](vsm-images/VsmValidationInvalid.png "Convalida VSM-non valida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando viene immesso un numero di telefono valido, quindi lo stato corrente diventa "Valid". Il `Entry` ottiene uno sfondo a lime, il secondo `Label` scompare e la `Button` è ora abilitata:

[![Convalida di VSM: stato valido](vsm-images/VsmValidationValid.png "Convalida VSM-valida")](vsm-images/VsmValidationValid-Large.png#lightbox)

Il file code-behind è responsabile della gestione dell'evento `TextChanged` dalla `Entry`. Il gestore Usa un'espressione regolare per determinare se la stringa di input è valida o meno. Il metodo nel file code-behind denominato `GoToState` chiama il metodo `VisualStateManager.GoToState` statico per `stackLayout`:

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

Si noti inoltre che il metodo `GoToState` viene chiamato dal costruttore per inizializzare lo stato. Deve esistere sempre uno stato corrente. Ma è lungi dall'essere nel codice è presente alcun riferimento al nome del gruppo dello stato di visualizzazione, anche se vi fanno riferimento il XAML come "ValidationStates" ai fini di chiarezza.

Si noti che il file code-behind deve solo prendere in considerazione l'oggetto nella pagina che definisce gli Stati di visualizzazione e chiamare `VisualStateManager.GoToState` per questo oggetto. Questo è dovuto al fatto che entrambi gli stati visivi sono destinati a più oggetti nella pagina.

Ci si potrebbe chiedere: se il file code-behind deve fare riferimento all'oggetto nella pagina che definisce gli Stati di visualizzazione, perché non è possibile accedere direttamente al file code-behind e ad altri oggetti? Senza dubbio possibile. Tuttavia, il vantaggio dell'uso di questo tipo di migrazione è che è possibile controllare gli elementi visivi come reagire al diverso stato interamente in XAML, che consente di mantenere tutte la progettazione dell'interfaccia utente in un'unica posizione. Questo evita aspetto visivo di impostazione tramite l'accesso a elementi visivi direttamente dal code-behind.

## <a name="visual-state-triggers"></a>Trigger di stato di visualizzazione

Gli Stati di visualizzazione supportano i trigger di stato, che sono un gruppo specializzato di trigger che definiscono le condizioni in base alle quali deve essere applicata una [`VisualState`](xref:Xamarin.Forms.VisualState) .

I trigger di stato vengono aggiunti alla raccolta [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) di un [`VisualState`](xref:Xamarin.Forms.VisualState). Questa raccolta può contenere un solo trigger di stato o più trigger di stato. Un [`VisualState`](xref:Xamarin.Forms.VisualState) verrà applicato quando sono attivi tutti i trigger di stato nella raccolta.

Quando si usano i trigger di stato per controllare gli Stati di visualizzazione, Novell. Forms usa le seguenti regole di precedenza per determinare quale trigger (e [`VisualState`](xref:Xamarin.Forms.VisualState)corrispondenti) sarà attivo:

1. Qualsiasi trigger che deriva da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivata a causa della condizione di [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) soddisfatta.
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivata a causa della condizione di [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) soddisfatta.

Se più trigger sono contemporaneamente attivi, ad esempio due trigger personalizzati, il primo trigger dichiarato nel markup avrà la precedenza.

Per ulteriori informazioni sui trigger di stato, vedere [trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Utilizzare Visual State Manager per il layout adattivo

Un xamarin. Forms dell'applicazione in esecuzione su un telefono in genere può essere visualizzati in verticale o orizzontale proporzioni e un programma di xamarin. Forms in esecuzione sul desktop può essere ridimensionata per assumere molte diverse dimensioni e proporzioni. Un'applicazione ben progettata può visualizzare il contenuto in modo diverso per i vari fattori di forma pagina o nella finestra.

Questa tecnica è talvolta nota come _layout adattivo_. Poiché layout adattivo riguarda esclusivamente gli oggetti visivi di un programma, è un'applicazione ideale di Visual State Manager.

Un semplice esempio è un'applicazione che visualizza un piccolo insieme di pulsanti che interessano il contenuto dell'applicazione. In modalità verticale, questi pulsanti potrebbero essere visualizzati in una riga orizzontale nella parte superiore della pagina:

[![Layout adattivo VSM: verticale](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adattivo VSM-verticale")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

In modalità orizzontale, la matrice di pulsanti potrebbe essere spostata a un lato e visualizzata in una colonna:

[![Layout adattivo VSM: orizzontale](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adattivo VSM-orizzontale")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Dall'alto verso il basso, il programma è in esecuzione sulla piattaforma universale di Windows, Android e iOS.

La pagina **layout adattivo VSM** nell'esempio [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) definisce un gruppo denominato "OrientationStates" con due stati visivi denominati "Portrait" e "Landscape". (Un approccio più complesso potrebbe essere basato su numerose le larghezze di pagina o nella finestra diverse).

Markup VSM si verifica in quattro posizioni nel file XAML. Il `StackLayout` denominato `mainStack` contiene sia il menu che il contenuto, che è un elemento di `Image`. Questo `StackLayout` deve avere un orientamento verticale in modalità verticale e un orientamento orizzontale in modalità orizzontale:

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

Il `ScrollView` interno denominato `menuScroll` e il `StackLayout` denominato `menuStack` implementano il menu dei pulsanti. L'orientamento di questi layout è opposto a `mainStack`. Il menu deve essere in modalità orizzontale verticale e orizzontale in modalità verticale.

La quarta sezione di markup di questo tipo di migrazione è in uno stile implicito per i pulsanti di se stessi. Questo markup imposta le proprietà `VerticalOptions`, `HorizontalOptions`e `Margin` specifiche per gli orientamenti verticale e orizzontale.

Il file code-behind imposta la proprietà `BindingContext` di `menuStack` per implementare `Button` comando e connette anche un gestore all'evento `SizeChanged` della pagina:

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

Il gestore di `SizeChanged` chiama `VisualStateManager.GoToState` per i due elementi `StackLayout` e `ScrollView`, quindi esegue il ciclo degli elementi figlio di `menuStack` per chiamare `VisualStateManager.GoToState` per gli elementi `Button`.

Può sembrare come se il file code-behind è possibile gestire le modifiche apportate orientamento più direttamente impostando le proprietà degli elementi nel file XAML, ma Visual State Manager è sicuramente un approccio più strutturato. Tutti gli oggetti visivi vengono mantenuti nel file XAML, in cui essi diventano più facili da esaminare, gestire e modificare.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestione dello stato di visualizzazione con Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Novell. Forms 3,0 video di Visual State Manager**

## <a name="related-links"></a>Collegamenti correlati

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Trigger di stato](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
