---
title: Xamarin. Forms Visual State Manager
description: Usare Visual State Manager per apportare modifiche agli elementi XAML in base gli stati visivi impostata dal codice.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: d33dc7d789b9a21fd843cd1f7cab8d221145f492
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978038"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Xamarin. Forms Visual State Manager

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

_Usare Visual State Manager per apportare modifiche agli elementi XAML in base gli stati visivi impostata dal codice._

I Visual State Manager (VSM) è stata introdotta nella versione 3.0 di xamarin. Forms. VSM fornisce un modo strutturato per apportare modifiche visive per l'interfaccia utente dal codice. Nella maggior parte dei casi, l'interfaccia utente dell'applicazione è definita in XAML, e questo XAML include il markup che descrive gli oggetti visivi dell'interfaccia utente di influenza Visual State Manager.

VSM introduce il concetto di _gli stati visivi_. Una vista di xamarin. Forms, ad esempio un `Button` può avere diversi aspetti di visual diversi a seconda dello stato sottostante &mdash; se è disabilitato, premuto o ha lo stato attivo. Questi sono gli stati del pulsante.

Stati di visualizzazione vengono raccolti nel _gruppi di stato di visualizzazione_. Tutti gli stati visivi all'interno di un gruppo di stati di visualizzazione si escludono a vicenda. Sia gli stati visivi e i gruppi dello stato di visualizzazione vengono identificati da stringhe di testo semplice.

Xamarin. Forms Visual State Manager definisce un gruppo di stati di visualizzazione denominato "CommonStates" con tre stati di visualizzazione:

- "Normal"
- "Disabilitata"
- Con stato "attivo"

Questo gruppo dello stato di visualizzazione è supportato per tutte le classi che derivano da [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), ovvero la classe base per [ `View` ](xref:Xamarin.Forms.View) e [ `Page` ](xref:Xamarin.Forms.Page). 

È anche possibile definire i propri gruppi dello stato di visualizzazione e stati di visualizzazione, come in questo articolo verranno illustrato.

> [!NOTE]
> Familiarità con gli sviluppatori di xamarin. Forms [trigger](~/xamarin-forms/app-fundamentals/triggers.md) sono consapevoli che i trigger possono anche apportare modifiche agli oggetti visivi nell'interfaccia utente in base alle modifiche nelle proprietà di una vista o la generazione degli eventi. Tuttavia, l'utilizzo di trigger per affrontare varie combinazioni di queste modifiche può risultare poco chiaro. In passato, il gestore di stato di visualizzazione è stata introdotta in ambienti basati su XAML di Windows per ridurre la confusione derivante da combinazioni di stati di visualizzazione. Con VSM, gli stati visivi all'interno di un gruppo di stati di visualizzazione sono sempre si escludono a vicenda. In qualsiasi momento, solo uno stato di ogni gruppo rappresenta lo stato corrente.

## <a name="the-common-states"></a>Gli Stati comuni

Visual State Manager consente di includere le sezioni nel file XAML che è possibile modificare l'aspetto visivo di una vista se la vista è normale, o disabilitata, o è nello stato attivo. Questi sono conosciuti come le _comuni stati_.

Ad esempio, si supponga di disporre un `Entry` visualizzazione nella pagina e si desidera che l'aspetto visivo del `Entry` modificare nei modi seguenti:

- Il `Entry` deve avere una rosa in background quando il `Entry` è disabilitato.
- Il `Entry` avrà uno sfondo verde limone normalmente.
- Il `Entry` deve espandere per due volte l'altezza normale quando ha lo stato attivo.

È possibile collegare il markup di questo tipo di migrazione a una singola visualizzazione, oppure è possibile definirlo in uno stile se si applica a più visualizzazioni. Due sezioni successive descrivono questi approcci.

### <a name="vsm-markup-on-a-view"></a>Markup di questo tipo di migrazione in una vista

Collegare il markup di questo tipo di migrazione a un `Entry` visualizzare, prima di tutto separare il `Entry` nel tag di inizio e fine:

```xaml
<Entry FontSize="18">

</Entry>
```

Gli ha assegnato una dimensione del carattere esplicito perché uno degli stati useranno il `FontSize` raddoppiare le dimensioni del testo nella proprietà il `Entry`.

Successivamente, Inserisci `VisualStateManager.VisualStateGroups` tag tra i tag:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) è una proprietà associabile associata definita per il [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) classe. (Per altre informazioni sulle proprietà associabili associate, vedere l'articolo [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md).) Si tratta di come la `VisualStateGroups` proprietà è associata la `Entry` oggetto.

Il `VisualStateGroups` proprietà è di tipo [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), che è una raccolta di [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) oggetti. All'interno di `VisualStateManager.VisualStateGroups` tag, inserire una coppia di `VisualStateGroup` tag per ogni gruppo di stati di visualizzazione che si desidera includere:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Si noti che il `VisualStateGroup` tag è costituito da un `x:Name` attributo che indica il nome del gruppo. Il `VisualStateGroup` classe definisce un `Name` proprietà che è possibile usare invece:

```xaml
<VisualStateGroup Name="CommonStates">
```

È possibile usare `x:Name` o `Name` ma non entrambi nello stesso elemento.

Il `VisualStateGroup` classe definisce una proprietà denominata [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), che è una raccolta di [ `VisualState` ](xref:Xamarin.Forms.VisualState) oggetti. `States` è il _proprietà di contenuto_ di `VisualStateGroups` pertanto è possibile includere il `VisualState` direttamente tra i tag di `VisualStateGroup` tag. (Contenuto proprietà sono descritte nell'articolo [sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

Il passaggio successivo è includere una coppia di tag per ogni stato di visualizzazione in tale gruppo. Inoltre possono essere identificati usando `x:Name` o `Name`:

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

`VisualState` definisce una proprietà denominata [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), che è una raccolta di [ `Setter` ](xref:Xamarin.Forms.Setter) oggetti. Si tratta delle stesse `Setter` oggetti utilizzabili in una [ `Style` ](xref:Xamarin.Forms.Style) oggetto.

`Setters` viene _non_ alla proprietà content del `VisualState`, pertanto è necessario includere i tag di elemento di proprietà per il `Setters` proprietà:

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

È ora possibile inserire uno o più `Setter` oggetti tra ogni coppia di `Setters` tag. Questi sono i `Setter` oggetti che definiscono gli stati visivi descritti in precedenza:

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

Markup simile al seguente costituisce la base del **VSM sulla vista** pagina il **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** programma di esempio. La pagina include tre `Entry` viste, ma solo il secondo è il markup VSM collegato:

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

Si noti che la seconda `Entry` ha anche una `DataTrigger` come parte del relativo `Trigger` raccolta. In questo modo, il `Entry` deve essere disabilitata fino a quando un elemento è stato digitato nel terzo `Entry`. Ecco la pagina all'avvio in esecuzione in iOS, Android e Universal Windows Platform (UWP):

[![VSM sulla vista: Disabilitata](vsm-images/VsmOnViewDisabled.png "VSM sulla vista - disabilitato")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

Lo stato di visualizzazione corrente è "Disabled" in modo che dello sfondo del secondo `Entry` è di colore rosa in iOS e Android schermate. L'implementazione di UWP del `Entry` non consente l'impostazione di background colore quando i `Entry` è disabilitato. 

Quando si immette del testo nel terzo `Entry`, il secondo `Entry` commutatori in stato di "Normal" e lo sfondo è ora lime:

[![VSM sulla vista: Normal](vsm-images/VsmOnViewNormal.png "VSM sulla vista - normale")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando si tocca la seconda `Entry`, ottiene lo stato attivo. Passa allo stato "Attivo" e si espande in due volte l'altezza:

[![VSM sulla vista: Incentrato](vsm-images/VsmOnViewFocused.png "VSM in visualizzazione - con stato attivo")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Si noti che il `Entry` non mantengono lo sfondo verde limone quando ottiene lo stato attivo. Poiché Visual State Manager attiva tra gli stati visivi, non sono impostate le proprietà impostate dallo stato precedente. Tenere presente che gli stati visivi si escludono a vicenda. Lo stato "Normal" non significa solo che il `Entry` è abilitata. Indica che il `Entry` è abilitato e non è stato attivo di input. 

Se si desidera che il `Entry` con uno sfondo verde limone nello stato "Attivo", aggiungere un altro `Setter` a tale stato di visualizzazione:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Affinché queste `Setter` gli oggetti per il corretto funzionamento, una `VisualStateGroup` deve contenere `VisualState` gli oggetti per tutti gli stati in tale gruppo. Se è presente uno stato di visualizzazione che non ha alcun `Setter` oggetti, includerla in ogni caso come un tag vuoto:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Markup di gestione dello stato di visualizzazione in uno stile

È spesso necessario condividere lo stesso markup Visual State Manager tra due o più visualizzazioni. In questo caso, è opportuno inserire il markup in un `Style` definizione.

Ecco esistente implicita `Style` per il `Entry` elementi nel **VSM in visualizzazione** pagina:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Aggiungere `Setter` i tag per il `VisualStateManager.VisualStateGroups` proprietà associabili associata:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

La proprietà di contenuto per `Setter` viene `Value`, quindi il valore della `Value` proprietà può essere specificata direttamente all'interno di tali tag. Che tipo è proprietà `VisualStateGroupList`:

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

Di seguito è riportato il **VSM in stile** pagina che mostra il markup di questo tipo di migrazione completato:

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

A questo punto tutti i `Entry` viste in questa pagina rispondono allo stesso modo gli stati visual. Si noti anche che lo stato "Attivo" include ora una seconda `Setter` che assegna a ognuno `Entry` un lime in background anche quando lo stato attivo dispone di input:

[![Questo tipo di migrazione in stile](vsm-images/VsmInStyle.png "VSM in stile")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definire i propri Stati di visualizzazione

Ogni classe che deriva da `VisualElement` supporta i tre stati comuni "Normal", "Con stato attivo" e "Disabilitato". Internamente, il [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe rileva quando sta per diventare abilitato o disabilitato, o con lo stato attivo o con stato non attivo e chiama il metodo statico [ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) metodo:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Questo è l'unico codice Visual State Manager che è possibile trovare nel `VisualElement` classe. In quanto `GoToState` viene chiamato per ogni oggetto in base a ogni classe che deriva da `VisualElement`, è possibile usare il gestore di stato di visualizzazione con una qualsiasi `VisualElement` oggetto per rispondere a queste modifiche.

È interessante notare che il nome del gruppo di stati di visualizzazione "CommonStates" non in modo esplicito fa `VisualElement`. Il nome del gruppo non è parte dell'API per il gestore di stato di visualizzazione. All'interno di uno dei valori il programma di due esempio illustrato finora, è possibile modificare il nome del gruppo da "CommonStates" in un account, e il programma continuerà a funzionare. Il nome del gruppo è semplicemente una descrizione generale degli stati in tale gruppo. In modo implicito è inteso che gli stati visivi in qualsiasi gruppo si escludono a vicenda: Uno stato e un solo stato è aggiornato in qualsiasi momento.

Se si desidera implementare i proprio gli stati di visualizzazione, è necessario chiamare `VisualStateManager.GoToState` dal codice. In genere si apporteranno questa chiamata dal file code-behind della classe di pagina.

Il **convalida VSM** pagina il **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** esempio viene illustrato come utilizzare Visual State Manager in relazione alla convalida dell'input. Il file XAML è costituito da due `Label` elementi, un `Entry`, e `Button`:

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

Markup di questo tipo di migrazione è collegato al secondo `Label` (denominato `helpLabel`) e il `Button` (denominato `submitButton`). Esistono due stati si escludono a vicenda, denominati "Valid" e "Non è valido". Si noti che ognuno dei due gruppi "ValidationState" contiene `VisualState` i tag per "Valid" e "Invalid", anche se uno di essi è vuoto in ogni caso. 

Se il `Entry` non contiene un numero di telefono valido, quindi lo stato corrente è "Invalid" e in modo che il secondo `Label` è visibile e `Button` è disabilitato:

[![Convalida di questo tipo di migrazione: Stato non valida](vsm-images/VsmValidationInvalid.png "convalida VSM - non è valida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando viene immesso un numero di telefono valido, quindi lo stato corrente diventa "Valid". La seconda `Entry` scompare e `Button` è ora abilitato:

[![Convalida di questo tipo di migrazione: Stato valido](vsm-images/VsmValidationValid.png "convalida VSM - valida")](vsm-images/VsmValidationValid-Large.png#lightbox)

Il file code-behind è responsabile per la gestione di `TextChanged` evento dal `Entry`. Il gestore Usa un'espressione regolare per determinare se la stringa di input è valida o meno. Il metodo nel file code-behind denominato `GoToState` chiama il metodo statico `VisualStateManager.GoToState` metodo per entrambi `helpLabel` e `submitButton`:

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

Si noti inoltre che il `GoToState` viene chiamato dal costruttore per inizializzare lo stato. Deve esistere sempre uno stato corrente. Ma è lungi dall'essere nel codice è presente alcun riferimento al nome del gruppo dello stato di visualizzazione, anche se vi fanno riferimento il XAML come "ValidationStates" ai fini di chiarezza. 

Si noti che il file code-behind deve tener conto di tutti gli oggetti nella pagina in cui è interessata da questi stati visivi e chiamare `VisualStateManager.GoToState` per ognuno di questi oggetti. In questo esempio, si tratta solo di due oggetti (il `Label` e il `Button`), ma potrebbe essere diverse altre.

Ci si potrebbe chiedere: Se il file code-behind deve fare riferimento a tutti gli oggetti nella pagina in cui è interessato da questi stati di visualizzazione, il motivo per cui non è il file code-behind è sufficiente accedere agli oggetti direttamente? Senza dubbio possibile. Tuttavia, il vantaggio dell'uso di questo tipo di migrazione è che è possibile controllare gli elementi visivi come reagire al diverso stato interamente in XAML, che consente di mantenere tutte la progettazione dell'interfaccia utente in un'unica posizione. Questo evita aspetto visivo di impostazione tramite l'accesso a elementi visivi direttamente dal code-behind.

Potrebbe essere tentato di prendere in considerazione la derivazione di una classe dalla classe `Entry` e forse la definizione di una proprietà che è possibile impostare una funzione di convalida esterno. La classe che deriva da `Entry` può quindi chiamare il `VisualStateManager.GoToState` (metodo). Questo schema funzionerà correttamente, ma solo se il `Entry` erano l'unico oggetto interessato dai diversi stati di visualizzazione. In questo esempio, un `Label` e un `Button` sono interessate anche. Non esiste un modo per markup VSM collegati a un `Entry` per controllare gli altri oggetti nella pagina e in alcun modo per markup VSM associate a questi altri oggetti per fare riferimento a una modifica dello stato di visualizzazione da un altro oggetto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Utilizzare il gestore di stato di visualizzazione per il layout adattivo

Un xamarin. Forms dell'applicazione in esecuzione su un telefono in genere può essere visualizzati in verticale o orizzontale proporzioni e un programma di xamarin. Forms in esecuzione sul desktop può essere ridimensionata per assumere molte diverse dimensioni e proporzioni. Un'applicazione ben progettata può visualizzare il contenuto in modo diverso per i vari fattori di forma pagina o nella finestra. 

Questa tecnica è talvolta detta _adattivo layout_. Poiché layout adattivo riguarda esclusivamente gli oggetti visivi di un programma, è un'applicazione ideale di Visual State Manager.

Un semplice esempio è un'applicazione che visualizza un piccolo insieme di pulsanti che interessano il contenuto dell'applicazione. In modalità verticale, questi pulsanti potrebbero essere visualizzati in una riga orizzontale nella parte superiore della pagina:

[![Layout adattivo VSM: Verticale](vsm-images/VsmAdaptiveLayoutPortrait.png "layout adattivo VSM - verticale")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

In modalità orizzontale, la matrice di pulsanti potrebbe essere spostata a un lato e visualizzata in una colonna:

[![Layout adattivo VSM: Panorama applicativo](vsm-images/VsmAdaptiveLayoutLandscape.png "layout adattivo VSM - orizzontale")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Dall'alto verso il basso, il programma è in esecuzione sulla piattaforma universale di Windows, Android e iOS.

Il **Layout adattivo VSM** nella pagina il [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/) esempio definisce un gruppo denominato "OrientationStates" con due stati di visualizzazione denominati "Orizzontale" e "Portrait". (Un approccio più complesso potrebbe essere basato su numerose le larghezze di pagina o nella finestra diverse). 

Markup VSM si verifica in quattro posizioni nel file XAML. Il `StackLayout` denominate `mainStack` contiene sia il menu di scelta e il contenuto, ovvero un `Image` elemento. Ciò `StackLayout` deve avere un orientamento verticale in modalità verticale e un orientamento orizzontale in modalità orizzontale:

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

Interna `ScrollView` denominate `menuScroll` e il `StackLayout` denominato `menuStack` implementare il menu di pulsanti. L'orientamento del layout di questi è opposto di `mainStack`. Il menu deve essere in modalità orizzontale verticale e orizzontale in modalità verticale.

La quarta sezione di markup di questo tipo di migrazione è in uno stile implicito per i pulsanti di se stessi. Questo markup imposta `VerticalOptions`, `HorizontalOptions`, e `Margin` proprietà specifiche per gli orientamenti orizzontale e portait.

I set di file code-behind il `BindingContext` proprietà di `menuStack` implementare `Button` esecuzione dei comandi e associa anche un gestore per il `SizeChanged` evento della pagina:

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

Il `SizeChanged` chiamate del gestore `VisualStateManager.GoToState` per le due `StackLayout` e `ScrollView` elementi e quindi scorre in ciclo gli elementi figlio del `menuStack` chiamare `VisualStateManager.GoToState` per il `Button` elementi.

Può sembrare come se il file code-behind è possibile gestire le modifiche apportate orientamento più direttamente impostando le proprietà degli elementi nel file XAML, ma Visual State Manager è sicuramente un approccio più strutturato. Tutti gli oggetti visivi vengono mantenuti nel file XAML, in cui essi diventano più facili da esaminare, gestire e modificare.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestione dello stato di visualizzazione con Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Video Visual State Manager xamarin. Forms 3.0**

## <a name="related-links"></a>Collegamenti correlati

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)
