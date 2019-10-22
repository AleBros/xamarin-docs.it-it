---
title: Gestore dello stato di visualizzazione Novell. Forms
description: Utilizzare Visual State Manager per apportare modifiche agli elementi XAML in base agli Stati di visualizzazione impostati dal codice.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656062"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Gestore dello stato di visualizzazione Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilizzare Visual State Manager per apportare modifiche agli elementi XAML in base agli Stati di visualizzazione impostati dal codice._

Visual State Manager (VSM) è una novità di Novell. Forms 3,0. VSM fornisce un modo strutturato per apportare modifiche visive all'interfaccia utente dal codice. Nella maggior parte dei casi, l'interfaccia utente dell'applicazione è definita in XAML e questo codice XAML include il markup che descrive il modo in cui il gestore dello stato di visualizzazione influiscono sugli oggetti visivi dell'interfaccia utente.

In VSM è stato introdotto il concetto di _stati visivi_. Una vista Novell. Forms, ad esempio un `Button` può avere diversi aspetti visivi a seconda dello stato sottostante &mdash; se è disabilitato o premuto oppure ha lo stato attivo per l'input. Si tratta dello stato del pulsante.

Gli Stati di visualizzazione vengono raccolti nei _gruppi di stati visivi_. Tutti gli stati visivi all'interno di un gruppo di Stati di visualizzazione si escludono a vicenda. Gli stati visivi e i gruppi di stati visivi vengono identificati da semplici stringhe di testo.

Il gestore dello stato di visualizzazione Novell. Forms definisce un gruppo di Stati di visualizzazione denominato "CommonStates" con tre stati di visualizzazione:

- "Normal"
- Disabilitato
- Con stato attivo

Questo gruppo di Stati di visualizzazione è supportato per tutte le classi che derivano da [`VisualElement`](xref:Xamarin.Forms.VisualElement), che è la classe di base per [`View`](xref:Xamarin.Forms.View) e [`Page`](xref:Xamarin.Forms.Page). 

È anche possibile definire i propri gruppi di stati visivi e gli stati visivi, come illustrato in questo articolo.

> [!NOTE]
> Gli sviluppatori Novell. Forms che conoscono i [trigger](~/xamarin-forms/app-fundamentals/triggers.md) sono consapevoli del fatto che i trigger possono anche apportare modifiche agli oggetti visivi nell'interfaccia utente in base alle modifiche apportate alle proprietà di una vista o all'attivazione di eventi. Tuttavia, l'utilizzo di trigger per gestire diverse combinazioni di queste modifiche può diventare piuttosto confuso. In passato, il gestore degli Stati di visualizzazione era stato introdotto negli ambienti basati su XAML di Windows per attenuare la confusione risultante dalle combinazioni di Stati di visualizzazione. Con VSM, gli stati visivi all'interno di un gruppo di Stati di visualizzazione si escludono sempre a vicenda. In qualsiasi momento, solo uno stato in ogni gruppo è lo stato corrente.

## <a name="the-common-states"></a>Stati comuni

Il gestore dello stato di visualizzazione consente di includere sezioni del file XAML in grado di modificare l'aspetto visivo di una vista se la vista è normale o disabilitata o ha lo stato attivo per l'input. Questi sono noti come _stati comuni_.

Si supponga, ad esempio, di disporre di una visualizzazione `Entry` nella pagina e che si desideri modificare l'aspetto visivo del `Entry` nei modi seguenti:

- Il `Entry` deve avere uno sfondo rosa quando il `Entry` è disabilitato.
- Il `Entry` deve avere uno sfondo di calce normalmente.
- Il `Entry` deve espandersi fino al doppio dell'altezza normale quando presenta lo stato attivo per l'input.

È possibile aggiungere il markup VSM a una singola visualizzazione oppure è possibile definirlo in uno stile se si applica a più visualizzazioni. Le due sezioni successive descrivono questi approcci.

### <a name="vsm-markup-on-a-view"></a>Markup VSM in una vista

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

Si noti che il secondo `Entry` dispone anche di un `DataTrigger` come parte della raccolta di `Trigger`. In questo modo il `Entry` viene disabilitato fino a quando un elemento non viene digitato nella terza `Entry`. Di seguito è illustrata la pagina all'avvio eseguito su iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP):

[![VSM sulla vista: disabilitato](vsm-images/VsmOnViewDisabled.png "VSM sulla visualizzazione-disabilitato")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

Lo stato di visualizzazione corrente è "disabilitato", quindi lo sfondo della seconda `Entry` è rosa sulle schermate iOS e Android. L'implementazione UWP di `Entry` non consente l'impostazione del colore di sfondo quando il `Entry` è disabilitato. 

Quando si immette testo nel terzo `Entry`, il secondo `Entry` passa allo stato "normale" e lo sfondo è ora:

[![VSM di visualizzazione: normale](vsm-images/VsmOnViewNormal.png "VSM di visualizzazione-normale")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando si tocca la seconda `Entry`, ottiene lo stato attivo per l'input. Passa allo stato "focused" e si espande fino al doppio dell'altezza:

[![VSM sulla vista: con stato attivo](vsm-images/VsmOnViewFocused.png "VSM su Vista-con stato attivo")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Si noti che il `Entry` non mantiene lo sfondo della calce quando ottiene lo stato attivo per l'input. Poiché il gestore dello stato di visualizzazione passa tra gli Stati di visualizzazione, le proprietà impostate dallo stato precedente vengono annullate. Tenere presente che gli Stati di visualizzazione si escludono a vicenda. Lo stato "normale" non significa solo che la `Entry` è abilitata. Significa che il `Entry` è abilitato e non ha lo stato attivo per l'input. 

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

### <a name="visual-state-manager-markup-in-a-style"></a>Markup di Visual State Manager in uno stile

Spesso è necessario condividere lo stesso markup di Visual State Manager tra due o più viste. In questo caso, è opportuno inserire il markup in una definizione di `Style`.

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

Ora tutte le visualizzazioni `Entry` in questa pagina rispondono allo stesso modo agli stati visivi. Si noti anche che lo stato "con stato attivo" include ora una seconda `Setter` che assegna a ogni `Entry` uno sfondo a lime anche quando ha lo stato attivo per l'input:

[![VSM nello stile](vsm-images/VsmInStyle.png "VSM nello stile")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definizione di Stati di visualizzazione personalizzati

Ogni classe che deriva da `VisualElement` supporta i tre stati comuni "normale", "con stato attivo" e "disabilitato". Internamente, la classe [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) rileva quando viene abilitata o disabilitata oppure con stato attivo o non attivo e chiama il metodo statico [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Si tratta dell'unico codice di Visual State Manager trovato nella classe `VisualElement`. Poiché `GoToState` viene chiamato per ogni oggetto in base a ogni classe che deriva da `VisualElement`, è possibile utilizzare il gestore dello stato di visualizzazione con qualsiasi `VisualElement` oggetto per rispondere a tali modifiche.

È interessante notare che il nome del gruppo di Stati di visualizzazione "CommonStates" non viene fatto riferimento in modo esplicito in `VisualElement`. Il nome del gruppo non fa parte dell'API per la gestione dello stato di visualizzazione. All'interno di uno dei due programmi di esempio illustrati finora, è possibile modificare il nome del gruppo da "CommonStates" a qualsiasi altro e il programma funzionerà comunque. Il nome del gruppo è semplicemente una descrizione generale degli Stati del gruppo. Viene implicitamente compreso che gli Stati di visualizzazione in qualsiasi gruppo si escludono a vicenda: uno stato e un solo stato sono correnti in qualsiasi momento.

Se si desidera implementare gli Stati di visualizzazione, è necessario chiamare `VisualStateManager.GoToState` dal codice. Spesso si effettuerà questa chiamata dal file code-behind della classe Page.

La pagina **convalida VSM** nell'esempio **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** illustra come usare la gestione dello stato di visualizzazione in connessione con la convalida dell'input. Il file XAML è costituito da due `Label` elementi, una `Entry` e `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

Il markup VSM è associato alla seconda `Label` (denominata `helpLabel`) e al `Button` (denominato `submitButton`). Ci sono due Stati che si escludono a vicenda, denominati "valid" e "valid". Si noti che ognuno dei due gruppi "ValidationState" contiene `VisualState` tag sia per "valid" che "valid", sebbene uno di essi sia vuoto in ogni caso. 

Se il `Entry` non contiene un numero di telefono valido, lo stato corrente è "non valido", quindi il secondo `Label` è visibile e il `Button` è disabilitato:

[![Convalida di VSM: stato non valido](vsm-images/VsmValidationInvalid.png "Convalida VSM-non valida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando viene immesso un numero di telefono valido, lo stato corrente diventa "valid". Il secondo `Entry` scompare e la `Button` è ora abilitata:

[![Convalida di VSM: stato valido](vsm-images/VsmValidationValid.png "Convalida VSM-valida")](vsm-images/VsmValidationValid-Large.png#lightbox)

Il file code-behind è responsabile per la gestione dell'evento `TextChanged` dalla `Entry`. Il gestore utilizza un'espressione regolare per determinare se la stringa di input è valida o meno. Il metodo nel file code-behind denominato `GoToState` chiama il metodo `VisualStateManager.GoToState` statico sia per `helpLabel` che per `submitButton`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

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
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

Si noti inoltre che il metodo `GoToState` viene chiamato dal costruttore per inizializzare lo stato. Deve essere sempre presente uno stato corrente. Nel codice, tuttavia, è presente un riferimento al nome del gruppo di Stati di visualizzazione, sebbene venga fatto riferimento in XAML come "ValidationStates" a scopo di chiarezza. 

Si noti che il file code-behind deve tenere conto di ogni oggetto della pagina interessato da questi stati di visualizzazione e chiamare `VisualStateManager.GoToState` per ognuno di questi oggetti. In questo esempio sono solo due oggetti (il `Label` e il `Button`), ma potrebbero essere diversi.

Ci si potrebbe chiedere: se il file code-behind deve fare riferimento a tutti gli oggetti della pagina interessati da questi stati di visualizzazione, perché il file code-behind non può accedere semplicemente agli oggetti direttamente? Certamente. Tuttavia, il vantaggio dell'uso di VSM è la possibilità di controllare il modo in cui gli elementi visivi reagiscono completamente a uno stato diverso in XAML, che consente di mantenere tutte le funzionalità di progettazione dell'interfaccia utente in un'unica posizione. In questo modo si evita di impostare l'aspetto visivo accedendo agli elementi visivi direttamente dal code-behind.

Potrebbe essere utile considerare la derivazione di una classe da `Entry` e forse definire una proprietà che è possibile impostare su una funzione di convalida esterna. La classe che deriva da `Entry` può quindi chiamare il metodo `VisualStateManager.GoToState`. Questo schema funziona correttamente, ma solo se l'`Entry` è l'unico oggetto interessato dai diversi Stati di visualizzazione. In questo esempio, sono interessati anche un `Label` e una `Button`. Non è possibile collegare il markup VSM a un `Entry` per controllare altri oggetti nella pagina e nessun modo per il markup VSM associato a questi altri oggetti per fare riferimento a una modifica nello stato di visualizzazione di un altro oggetto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Uso di Visual State Manager per il layout adattivo

Un'applicazione Novell. Forms in esecuzione su un telefono può in genere essere visualizzata in un formato verticale o orizzontale e un programma Novell. Forms in esecuzione sul desktop può essere ridimensionato in modo da assumere diverse dimensioni e proporzioni. Un'applicazione ben progettata potrebbe visualizzare il contenuto in modo diverso per questi diversi fattori di forma della pagina o della finestra. 

Questa tecnica è talvolta nota come _layout adattivo_. Poiché il layout adattivo implica esclusivamente gli oggetti visivi di un programma, è un'applicazione ideale del gestore dello stato di visualizzazione.

Un semplice esempio è costituito da un'applicazione che visualizza una piccola raccolta di pulsanti che influiscono sul contenuto dell'applicazione. In modalità verticale, questi pulsanti possono essere visualizzati in una riga orizzontale nella parte superiore della pagina:

[![Layout adattivo VSM: verticale](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adattivo VSM-verticale")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

In modalità orizzontale la matrice di pulsanti può essere spostata su un lato e visualizzata in una colonna:

[![Layout adattivo VSM: orizzontale](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adattivo VSM-orizzontale")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Dall'alto verso il basso, il programma è in esecuzione in piattaforma UWP (Universal Windows Platform), Android e iOS.

La pagina **layout adattivo VSM** nell'esempio [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) definisce un gruppo denominato "OrientationStates" con due stati visivi denominati "Portrait" e "Landscape". Un approccio più complesso può essere basato su diverse larghezze di pagina o finestre. 

Il markup VSM viene eseguito in quattro posizioni nel file XAML. Il `StackLayout` denominato `mainStack` contiene sia il menu che il contenuto, che è un elemento di `Image`. Questo `StackLayout` deve avere un orientamento verticale in modalità verticale e un orientamento orizzontale in modalità orizzontale:

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

Il `ScrollView` interno denominato `menuScroll` e il `StackLayout` denominato `menuStack` implementano il menu dei pulsanti. L'orientamento di questi layout è opposto a `mainStack`. Il menu deve essere orizzontale in modalità verticale e verticale in modalità orizzontale.

La quarta sezione del markup VSM è in uno stile implicito per i pulsanti. Questo markup imposta le proprietà `VerticalOptions`, `HorizontalOptions` e `Margin` specifiche per gli orientamenti ritratto e landscape.

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

Può sembrare che il file code-behind sia in grado di gestire le modifiche dell'orientamento più direttamente impostando le proprietà degli elementi nel file XAML, ma il gestore dello stato di visualizzazione è sicuramente un approccio più strutturato. Tutti gli oggetti visivi vengono conservati nel file XAML, dove diventano più semplici da esaminare, gestire e modificare.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Visual State Manager con Novell. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Novell. Forms 3,0 video di Visual State Manager**

## <a name="related-links"></a>Collegamenti correlati

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
