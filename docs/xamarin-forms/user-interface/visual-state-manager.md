---
title: La gestione dello stato di visualizzazione di xamarin. Forms
description: Utilizzare il gestore dello stato per apportare modifiche agli elementi XAML in base a stati di visualizzazione impostata dal codice.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 3c0330d8d6d07112350db007d0500d57c236dc24
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848024"
---
# <a name="the-xamarinforms-visual-state-manager"></a>La gestione dello stato di visualizzazione di xamarin. Forms

_Utilizzare il gestore dello stato per apportare modifiche agli elementi XAML in base a stati di visualizzazione impostata dal codice._

Il Manager di stato Visual (VSM) è stato introdotto in xamarin. Forms 3.0. VSM fornisce un modo strutturato per apportare delle modifiche visive per l'interfaccia utente dal codice. Nella maggior parte dei casi, l'interfaccia utente dell'applicazione è definito in XAML, e questo codice XAML include il markup che descrive gli elementi visivi dell'interfaccia utente di influenza il gestore dello stato.

VSM introduce il concetto di _gli stati visivi_. Una vista di xamarin. Forms, ad esempio un `Button` può avere diversi aspetti di visual diversi a seconda dello stato sottostante &mdash; se è disabilitato, oppure premuto o ha lo stato attivo di input. Questi sono gli stati del pulsante.

Gli stati visivi vengono raccolti nel _gruppi dello stato di visualizzazione_. Tutti gli stati visivi all'interno di un gruppo dello stato di visualizzazione si escludono a vicenda. Gli stati visivi sia gruppi dello stato di visualizzazione vengono identificati da stringhe di testo semplice.

Il gestore dello stato di xamarin. Forms definisce un gruppo di stato di visualizzazione denominato "CommonStates" con tre stati di visualizzazione:

- "Normal"
- "Disabilitato"
- "Attivo"

Questo gruppo dello stato di visualizzazione è supportato per tutte le classi che derivano da [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), che è la classe base per [ `View` ](xref:Xamarin.Forms.View) e [ `Page` ](xref:Xamarin.Forms.Page). 

È inoltre possibile definire i propri gruppi dello stato di visualizzazione e gli stati visivi, come in questo articolo illustrerà.

> [!NOTE]
> Familiarità con gli sviluppatori di xamarin. Forms [trigger](~/xamarin-forms/app-fundamentals/triggers.md) sono a conoscenza che i trigger possono anche apportare modifiche agli oggetti visivi nell'interfaccia utente in base alle modifiche nelle proprietà della vista o la generazione di eventi. Tuttavia, l'utilizzo di trigger per gestire le varie combinazioni di queste modifiche può risultare piuttosto confusione. In passato, il gestore dello stato è stato introdotto in ambienti basati su XAML di Windows per alleviare la confusione risultante da combinazioni di stati di visualizzazione. Con questo tipo di migrazione, gli stati visivi all'interno di un gruppo dello stato di visualizzazione sono sempre si escludono a vicenda. In qualsiasi momento, solo uno stato in ogni gruppo è lo stato corrente.

## <a name="the-common-states"></a>Gli Stati comuni

Il gestore dello stato consente di includere sezioni nel file XAML che può cambiare l'aspetto visivo di una vista se la visualizzazione normale o disabilitato, oppure ha lo stato attivo di input. Tali parametri sono noti come il _stati comuni_.

Si supponga, ad esempio, un' `Entry` visualizzazione della pagina, e si desidera che l'aspetto visivo del `Entry` modificare nei modi seguenti:

- Il `Entry` devono avere un rosa in background quando il `Entry` è disabilitato.
- Il `Entry` deve avere uno sfondo verde brillante normalmente.
- Il `Entry` deve espandere per due volte l'altezza normale quando lo stato attivo dispone di input.

È possibile collegare il markup VSM a una singola vista oppure è possibile definirla in uno stile se si applica a più visualizzazioni. Le prossime due sezioni vengono descritti questi approcci.

### <a name="vsm-markup-on-a-view"></a>Markup VSM su una vista

Per collegare markup VSM a un `Entry` visualizzare, prima di tutto separare il `Entry` nel tag di inizio e fine:

```xaml
<Entry FontSize="18">

</Entry>
```

Gli ha assegnato una dimensione del carattere esplicita poiché uno degli stati utilizzerà il `FontSize` raddoppiare le dimensioni del testo nella proprietà il `Entry`.

Successivamente, Inserisci `VisualStateManager.VisualStateGroups` tag tra i tag:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) è una proprietà associabile associata definita per il [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) classe. (Per ulteriori informazioni sulle proprietà associabile associate, vedere l'articolo [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md).) Questo è il modo in cui `VisualStateGroups` proprietà è associata al `Entry` oggetto.

Il `VisualStateGroups` proprietà è di tipo [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), che è una raccolta di [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) oggetti. All'interno di `VisualStateManager.VisualStateGroups` tag, inserire una coppia di `VisualStateGroup` tag per ogni gruppo di stati di visualizzazione che si desidera includere:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Si noti che il `VisualStateGroup` tag ha un `x:Name` attributo che indica il nome del gruppo. Il `VisualStateGroup` classe definisce un `Name` proprietà che è possibile usare:

```xaml
<VisualStateGroup Name="CommonStates">
```

È possibile utilizzare `x:Name` o `Name` ma non entrambi nello stesso elemento.

Il `VisualStateGroup` classe definisce una proprietà denominata [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), che è una raccolta di [ `VisualState` ](xref:Xamarin.Forms.VisualState) oggetti. `States` è il _proprietà content_ di `VisualStateGroups` pertanto è possibile includere il `VisualState` direttamente tra i tag di `VisualStateGroup` tag. (Contenuto proprietà sono descritte nell'articolo [essenziali sintassi di XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

Il passaggio successivo è per includere una coppia di tag per ogni stato di visualizzazione in tale gruppo. Inoltre possono essere identificati utilizzando `x:Name` o `Name`:

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

`VisualState` definisce una proprietà denominata [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), che è una raccolta di [ `Setter` ](xref:Xamarin.Forms.Setter) oggetti. Questi sono gli stessi `Setter` oggetti utilizzati in un [ `Style` ](xref:Xamarin.Forms.Style) oggetto.

`Setters` viene _non_ la proprietà content del `VisualState`, pertanto è necessario includere il tag di elementi di proprietà per il `Setters` proprietà:

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

È ora possibile inserire uno o più `Setter` oggetti tra ogni coppia di `Setters` tag. Si tratta di `Setter` oggetti che definiscono gli stati visivi descritti in precedenza:

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

Ogni `Setter` tag indica il valore di una determinata proprietà quando tale stato è corrente. Qualsiasi proprietà fa riferimento un `Setter` oggetto deve essere supportato da una proprietà associabile.

Simile al seguente markup è la base del **VSM sulla vista** nella pagina di **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** programma di esempio. La pagina include tre `Entry` viste, ma solo il secondo è il markup VSM collegato:

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

Si noti che il secondo `Entry` dispone anche di un `DataTrigger` come parte del relativo `Trigger` insieme. In questo modo, il `Entry` deve essere disabilitato fino a quando non si è digitato nel terzo `Entry`. Di seguito è la pagina all'avvio eseguiti su iOS, Android e la piattaforma UWP (Universal Windows):

[![VSM sulla vista: disabilitata](vsm-images/VsmOnViewDisabled.png "VSM sulla vista - disabilitato")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

Lo stato di visualizzazione corrente è "Disabled" pertanto la sfondo del secondo `Entry` è rosa nelle iOS e Android schermate. L'implementazione di piattaforma UWP di `Entry` non consente l'impostazione background colore quando i `Entry` è disabilitato. 

Quando si immette un testo nel terzo `Entry`, il secondo `Entry` switch in stato di "Normal" e lo sfondo è ora verde:

[![VSM sulla vista: Normal](vsm-images/VsmOnViewNormal.png "VSM sulla vista - normale")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando si tocca la seconda `Entry`, ottiene lo stato attivo di input. Passa allo stato "Attivo" e si espande in due volte l'altezza:

[![VSM sulla vista: con stato attivo](vsm-images/VsmOnViewFocused.png "VSM sulla vista - con stato attivo")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Si noti che il `Entry` non conserva lo sfondo verde brillante quando raggiunge lo stato attivo di input. Come il gestore dello stato cambia tra gli stati visivi, non sono impostate le proprietà impostate dallo stato precedente. Tenere presente che gli stati visivi si escludono a vicenda. Lo stato "Normal" significa solo che la `Entry` è abilitata. Indica che il `Entry` è abilitata e non è stato attivo di input. 

Se si desidera che il `Entry` per avere uno sfondo verde brillante nello stato "Attivo", aggiungere un altro `Setter` a tale stato di visualizzazione:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Affinché queste `Setter` oggetti il corretto funzionamento di un `VisualStateGroup` deve contenere `VisualState` gli oggetti per tutti gli stati in tale gruppo. Se è presente uno stato di visualizzazione che non è presenti `Setter` oggetti, includerlo comunque come un tag vuoto:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Markup di gestione dello stato di visualizzazione in uno stile

È spesso necessario condividere lo stesso markup gestore dello stato tra due o più visualizzazioni. In questo caso, è opportuno inserire il markup in un `Style` definizione.

Ecco esistenti implicita `Style` per il `Entry` elementi il **VSM nella vista** pagina:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Aggiungere `Setter` tag per il `VisualStateManager.VisualStateGroups` proprietà associabile associata:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

La proprietà di contenuto per `Setter` viene `Value`, quindi il valore della `Value` proprietà può essere specificata direttamente all'interno di tali tag. Che proprietà è di tipo `VisualStateGroupList`:

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

All'interno di tali tag è possibile includere uno o più `VisualStateGroup` oggetti:

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

Ecco il **VSM in stile** pagina che mostra il markup completo di questo tipo di migrazione:

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

Ora tutti i `Entry` viste in questa pagina rispondono allo stesso modo per i relativi stati di visualizzazione. Si noti inoltre che lo stato "Attivo" include ora una seconda `Setter` che assegna a ognuno `Entry` un limone in background anche quando lo stato attivo dispone di input:

[![Questo tipo di migrazione in stile](vsm-images/VsmInStyle.png "VSM in stile")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definire i propri Stati di visualizzazione

Ogni classe che deriva da `VisualElement` supporta i tre stati comuni "Normal", "Con stato attivo" e "Disabilitato". Internamente, il [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe rileva se sta diventando abilitato o disabilitato, o con lo stato attivo o con stato non attivo e chiama il metodo statico [ `VisualStateManager.GoToState` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualStateManager.GoToState/p/Xamarin.Forms.VisualElement/System.String/) metodo:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Questo è l'unico codice gestore dello stato che si trova nel `VisualElement` classe. Poiché `GoToState` viene chiamato per ogni oggetto in base a ogni classe che deriva da `VisualElement`, è possibile utilizzare il gestore dello stato con qualsiasi `VisualElement` oggetto per rispondere a queste modifiche.

È interessante notare che il nome del gruppo dello stato di visualizzazione "CommonStates" è un riferimento esplicito a `VisualElement`. Il nome del gruppo non fa parte dell'API per il gestore dello stato. All'interno di uno dei due programma di esempio illustrato finora, è possibile modificare il nome del gruppo da "CommonStates" in modo diverso e il programma continuerà a funzionare. Il nome del gruppo è semplicemente una descrizione generale degli stati in tale gruppo. In modo implicito è inteso che gli stati visivi in qualsiasi gruppo si escludono a vicenda: uno stato e un solo stato sia aggiornato in qualsiasi momento.

Se si desidera implementare il propria gli stati visivi, è necessario chiamare `VisualStateManager.GoToState` dal codice. In genere si apporteranno questa chiamata dal file code-behind di classe della pagina.

Il **convalida VSM** nella pagina di **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** esempio viene illustrato come utilizzare il gestore dello stato per la convalida dell'input. Il file XAML è costituita da due `Label` elementi, un `Entry`, e `Button`:

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

Markup VSM è collegato alla seconda `Label` (denominata `helpLabel`) e il `Button` (denominato `submitButton`). Esistono due stati che si escludono a vicenda, denominati "Valid" e "Non valido". Si noti che ognuno dei due gruppi "ValidationState" contiene `VisualState` tag per "Valid" e "Non valido", anche se uno di essi è vuoto in ogni caso. 

Se il `Entry` non contiene un numero di telefono valido, quindi lo stato corrente è "Valido" e quindi il secondo `Label` è visibile e il `Button` è disabilitato:

[![Convalida VSM: Stato non valido](vsm-images/VsmValidationInvalid.png "convalida VSM - non valido")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando viene immesso un numero di telefono valido, lo stato corrente diventa "Valid". Il secondo `Entry` scompare e `Button` è ora abilitato:

[![Convalida VSM: Stato valido](vsm-images/VsmValidationValid.png "convalida VSM - valida")](vsm-images/VsmValidationValid-Large.png#lightbox)

Il file code-behind è prestazione per la gestione di `TextChanged` evento dal `Entry`. Il gestore utilizza un'espressione regolare per determinare se la stringa di input è valida o meno. Il metodo nel file code-behind denominato `GoToState` chiama il metodo statico `VisualStateManager.GoToState` metodo per entrambi `helpLabel` e `submitButton`:

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

Si noti inoltre che il `GoToState` metodo viene chiamato dal costruttore per inizializzare lo stato. Deve esistere sempre uno stato corrente. Ma non nel codice è presente qualsiasi riferimento al nome del gruppo dello stato di visualizzazione, anche se viene fatto riferimento nel codice XAML come "ValidationStates" ai fini di chiarezza. 

Si noti che il file code-behind deve tener conto di ogni oggetto nella pagina in cui è influenzata da questi stati di visualizzazione e di chiamare `VisualStateManager.GoToState` per ognuno di questi oggetti. In questo esempio, che si tratta solo di due oggetti (il `Label` e il `Button`), ma potrebbe risultare diverse altre.

Ci si potrebbe chiedere: se il file code-behind deve fare riferimento a tutti gli oggetti nella pagina in cui è influenzato da questi stati di visualizzazione, perché non è il file code-behind semplicemente accedere agli oggetti direttamente? Sicuramente Impossibile. Tuttavia, il vantaggio dell'utilizzo di questo tipo di migrazione è che è possibile controllare gli elementi visivi come reagire alle diverso stato interamente in XAML, che consente di mantenere tutta la progettazione dell'interfaccia utente in un'unica posizione. Questo evita l'aspetto visivo di impostazione accedendo elementi visivi direttamente il code-behind.

Potrebbe essere tentato di prendere in considerazione la derivazione di una classe da `Entry` e forse la definizione di una proprietà che è possibile impostare per una funzione esterna di convalida. La classe che deriva da `Entry` può quindi chiamare il `VisualStateManager.GoToState` metodo. Questo schema funzionerà correttamente, ma solo se il `Entry` sono l'unico oggetto interessato da diversi stati di visualizzazione. In questo esempio, un `Label` e un `Button` anche interessati. Non esiste un modo per markup VSM associate a un `Entry` per controllare gli altri oggetti nella pagina e può in alcun modo per markup VSM associate a questi altri oggetti per fare riferimento a una modifica nello stato di visualizzazione da un altro oggetto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Utilizzare il gestore dello stato per il layout adattivo

È possibile ridimensionare un xamarin. Forms applicazione in esecuzione su un telefono può essere visualizzati in genere in verticale o orizzontale proporzioni e un programma di xamarin. Forms in esecuzione sul desktop per assumere molte diverse dimensioni e proporzioni possono variare. Un'applicazione ben progettata potrebbe visualizzare il relativo contenuto in modo diverso per i vari fattori di forma pagina o nella finestra. 

Questa tecnica viene talvolta definita come _adattivo layout_. Poiché layout adattivo riguarda unicamente gli oggetti visivi di un programma, è un'applicazione ideale del gestore dello stato.

Un semplice esempio è un'applicazione che visualizza una piccola raccolta di pulsanti che influiscono sul contenuto dell'applicazione. In modalità verticale, questi pulsanti potrebbero essere visualizzati in una riga orizzontale nella parte superiore della pagina:

[![Layout adattivo VSM: Verticale](vsm-images/VsmAdaptiveLayoutPortrait.png "layout adattivo VSM - verticale")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

In modalità orizzontale, la matrice dei pulsanti potrebbe essere spostata su un lato e visualizzata in una colonna:

[![Layout adattivo VSM: Landscape](vsm-images/VsmAdaptiveLayoutLandscape.png "layout adattivo VSM - orizzontale")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Dall'alto verso il basso, il programma è in esecuzione la piattaforma Windows universale, Android e iOS.

Il **VSM adattivo Layout** nella pagina di [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/) esempio definisce un gruppo denominato "OrientationStates" con due stati di visualizzazione denominati "Verticale" e "Orizzontale". (Un approccio più complesso potrebbe essere basato su numerose larghezze diverse di pagina o nella finestra). 

Markup VSM si verifica in quattro posizioni nel file XAML. Il `StackLayout` denominato `mainStack` contiene sia il menu del contenuto, che è un `Image` elemento. Questo `StackLayout` deve avere un orientamento verticale in modalità verticale e l'orientamento orizzontale in modalità orizzontale:

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

Interna `ScrollView` denominato `menuScroll` e il `StackLayout` denominato `menuStack` implementare il menu dei pulsanti. L'orientamento di questi layout è opposto di `mainStack`. Il menu deve essere in modalità verticale orizzontali e verticali in modalità orizzontale.

Nella quarta sezione markup VSM è in uno stile implicito per i pulsanti di se stessi. In questo markup `VerticalOptions`, `HorizontalOptions`, e `Margin` proprietà specifiche per gli orientamenti supportati dal portait e orizzontale.

I set di file di codice il `BindingContext` proprietà di `menuStack` implementare `Button` comandi e collega anche un gestore per il `SizeChanged` evento della pagina:

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

Il `SizeChanged` chiamate del gestore `VisualStateManager.GoToState` per i due `StackLayout` e `ScrollView` gli elementi e quindi scorre in ciclo gli elementi figlio del `menuStack` chiamare `VisualStateManager.GoToState` per il `Button` elementi.

Può sembrare come se il file code-behind è possibile gestire le modifiche di orientamento più direttamente impostando le proprietà degli elementi nel file XAML, ma il gestore dello stato tratta decisamente di un approccio più strutturato. Tutti gli elementi visivi vengono mantenuti nel file XAML, in cui diventano più facile da esaminare, gestire e modificare.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestione dello stato di visualizzazione con Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Gestione dello stato di visualizzazione di xamarin. Forms 3.0, da [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Collegamenti correlati

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

