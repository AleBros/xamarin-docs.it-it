---
title: Convalida nelle App aziendali
description: In questo capitolo viene illustrato come l'app per dispositivi mobili di eShopOnContainers esegue la convalida dell'input utente. Ciò include specificando regole di convalida, attivare la convalida e visualizzazione di errori di convalida.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 22b5fe703486f0ded3a5b91241e3fe5ce41bbc98
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507097"
---
# <a name="validation-in-enterprise-apps"></a>Convalida nelle App aziendali

Qualsiasi app che accetta l'input dagli utenti devono assicurarsi che l'input è valido. Un'app è stato possibile, ad esempio, controllare per l'input che contenga solo caratteri in un intervallo specifico, è di un certo periodo o corrisponde a un formato particolare. Senza la convalida, è possibile fornire i dati che fa sì che l'app avrà esito negativo. Convalida verranno applicate le regole di business e impedisce a un utente malintenzionato di inserimento di dati dannosi.

Nel contesto di Model-View-ViewModel (MVVM) modello, un modello di visualizzazione o modello sarà spesso necessario per eseguire la convalida dei dati e segnalare eventuali errori di convalida per la visualizzazione in modo che l'utente ne consente la correzione. L'app per dispositivi mobili di eShopOnContainers esegue la convalida lato client sincrona visualizzazione delle proprietà del modello e informa l'utente di eventuali errori di convalida per evidenziare il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente del motivo per cui i dati non sono validi. Figura 6-1 vengono illustrate le classi coinvolte nell'esecuzione della convalida nell'app per dispositivi mobili di eShopOnContainers.

[![](validation-images/validation.png "Classi di convalida nell'app eShopOnContainers")](validation-images/validation-large.png#lightbox "classi di convalida nell'app eShopOnContainers")

**Figura 6-1**: Classi di convalida nell'app eShopOnContainers

Proprietà modello di visualizzazione che richiede la convalida sono di tipo `ValidatableObject<T>`e ogni `ValidatableObject<T>` istanza dispone di regole di convalida aggiunto al relativo `Validations` proprietà. La convalida viene richiamata dal modello di visualizzazione chiamando il `Validate` metodo del `ValidatableObject<T>` regole di istanza, che recupera la convalida e li esegue sul `ValidatableObject<T>` `Value` proprietà. Eventuali errori di convalida vengono inseriti nel `Errors` proprietà del `ValidatableObject<T>` istanza e il `IsValid` proprietà del `ValidatableObject<T>` istanza viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.

La notifica di modifiche viene fornita dal `ExtendedBindableObject` (classe) e pertanto un' [ `Entry` ](xref:Xamarin.Forms.Entry) controllo può essere associato ai `IsValid` proprietà di `ValidatableObject<T>` istanza nella classe di modello di visualizzazione per ricevere una notifica o meno i dati immessi siano validi.

## <a name="specifying-validation-rules"></a>Specifica le regole di convalida

Le regole di convalida vengono specificate tramite la creazione di una classe che deriva dal `IValidationRule<T>` interfaccia, che viene visualizzato nell'esempio di codice seguente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Questa interfaccia specifica che una classe di regola di convalida deve fornire un `boolean` `Check` metodo che viene usato per eseguire la convalida richiesta, e un `ValidationMessage` proprietà il cui valore è il messaggio di errore di convalida che verrà visualizzato se convalida non riesce.

Nell'esempio di codice riportato di seguito viene illustrato il `IsNotNullOrEmptyRule<T>` regola di convalida, che consente di eseguire la convalida del nome utente e della password immessa dall'utente `LoginView` quando si usa servizi fittizi nell'app eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

Il `Check` metodo restituisce un `boolean` che indica se l'argomento di valore è `null`, vuota o costituita solo da spazi vuoti.

Anche se non è usato dall'app per dispositivi mobili di eShopOnContainers, il codice seguente illustra una regola di convalida per la convalida indirizzi di posta elettronica:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

Il `Check` metodo restituisce un `boolean` che indica se l'argomento di valore è un indirizzo di posta elettronica valido. A questo scopo, cercare l'argomento di valore per la prima occorrenza del criterio di espressione regolare specificata nel `Regex` costruttore. Se il criterio di espressione regolare è stato trovato nella stringa di input può essere determinato controllando il valore dei `Match` dell'oggetto `Success` proprietà.

> [!NOTE]
> Convalida delle proprietà in alcuni casi può implicare la proprietà dipendenti. Un esempio di proprietà dipendenti è quando il set di valori validi per una proprietà dipende dal valore specifico che è stato impostato nella proprietà B. Per verificare che il valore della proprietà oggetto è uno dei valori consentiti comporta il recupero del valore della proprietà B. Inoltre, quando viene modificato il valore della proprietà B, proprietà oggetto necessario possibile riconvalidati.

## <a name="adding-validation-rules-to-a-property"></a>Aggiunta di regole di convalida a una proprietà

Nell'app eShopOnContainers, vengono dichiarate proprietà modello di visualizzazione che richiede la convalida di tipo `ValidatableObject<T>`, dove `T` è il tipo di dati da convalidare. Esempio di codice seguente illustra un esempio di due proprietà:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Per ottenere la convalida, è necessario aggiungere regole di convalida per il `Validations` raccolta della ognuno `ValidatableObject<T>` istanze, come illustrato nell'esempio di codice seguente:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Questo metodo aggiunge il `IsNotNullOrEmptyRule<T>` regola di convalida il `Validations` raccolta della ognuno `ValidatableObject<T>` istanza, specificando i valori per la regola di convalida `ValidationMessage` proprietà che specifica il messaggio di errore di convalida che verrà visualizzato se convalida non riesce.

## <a name="triggering-validation"></a>Attivare la convalida

L'approccio di convalida utilizzata nell'app eShopOnContainers possibile attivare manualmente la convalida di una proprietà e automaticamente convalida trigger quando viene modificata una proprietà.

### <a name="triggering-validation-manually"></a>Attivare manualmente la convalida

La convalida può essere attivata manualmente per una proprietà del modello di visualizzazione. Ad esempio, ciò si verifica nell'app eShopOnContainers per dispositivi mobili quando l'utente tocca il **account di accesso** pulsante la `LoginView`, quando si usano servizi fittizi. Le chiamate al delegato comando il `MockSignInAsync` metodo nella `LoginViewModel`, che richiama la convalida tramite l'esecuzione di `Validate` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

Il `Validate` metodo esegue la convalida del nome utente e della password immessa dall'utente `LoginView`, richiamando il metodo di convalida su ogni `ValidatableObject<T>` istanza. Esempio di codice seguente viene illustrato il metodo Validate dal `ValidatableObject<T>` classe:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Questo metodo cancella il `Errors` raccolta e quindi recupera alcuna convalida delle regole che sono stati aggiunti all'oggetto `Validations` raccolta. Il `Check` esecuzione del metodo per ogni regola di convalida recuperato e il `ValidationMessage` viene aggiunto il valore di proprietà per qualsiasi regola di convalida non riesce a convalidare i dati per il `Errors` raccolta del `ValidatableObject<T>` istanza. Infine, il `IsValid` viene impostata e il relativo valore viene restituito al metodo di chiamata, che indica se la convalida ha avuto esito positivo o negativo.

### <a name="triggering-validation-when-properties-change"></a>Attivare la convalida quando vengono modificate

Convalida può anche essere attivata ogni volta che viene modificata una proprietà associata. Ad esempio, quando un'associazione bidirezionale nel `LoginView` imposta la `UserName` o `Password` proprietà, la convalida viene attivata. Esempio di codice seguente viene illustrato come in questo caso:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

Il [ `Entry` ](xref:Xamarin.Forms.Entry) controllo viene associato ai `UserName.Value` proprietà del `ValidatableObject<T>` istanza e del controllo `Behaviors` raccolta ha un `EventToCommandBehavior` aggiunto a tale istanza. Questo comportamento viene eseguito il `ValidateUserNameCommand` in risposta al [`TextChanged`] generazione dell'evento sul `Entry`, che viene generato quando il testo nel `Entry` le modifiche. A sua volta, il `ValidateUserNameCommand` delegato esegue il `ValidateUserName` metodo, che esegue il `Validate` metodo su di `ValidatableObject<T>` istanza. Pertanto, ogni volta che l'utente immette un carattere nel `Entry` viene eseguito il controllo per il nome utente, la convalida dei dati immessi.

Per altre informazioni sui comportamenti, vedere [implementazione di comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>La visualizzazione degli errori di convalida

L'app per dispositivi mobili di eShopOnContainers notifica all'utente eventuali errori di convalida da evidenziare il controllo che contiene i dati non validi con una linea rossa e visualizzando un messaggio di errore che informa l'utente perché non sono validi sotto il controllo che contiene i dati di dati non validi. Quando i dati non validi viene risolto, la riga viene modificato in nero e viene rimosso il messaggio di errore. Figura 6-2 mostra il LoginView nell'app eShopOnContainers per dispositivi mobili quando sono presenti errori di convalida.

![](validation-images/validation-login.png "La visualizzazione degli errori di convalida durante l'accesso")

**Figura 6-2:** La visualizzazione degli errori di convalida durante l'accesso

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Evidenziare un controllo che contiene dati non validi

Il `LineColorBehavior` comportamento collegato viene utilizzato per evidenziare [ `Entry` ](xref:Xamarin.Forms.Entry) controlli in cui si sono verificati errori di convalida. Nell'esempio di codice riportato di seguito viene illustrato come la `LineColorBehavior` comportamento associato è associato a un `Entry` controllo:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

Il [ `Entry` ](xref:Xamarin.Forms.Entry) controllo utilizza uno stile esplicito, che è illustrato nell'esempio di codice seguente:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Imposta questo stile il `ApplyLineColor` e `LineColor` le proprietà associate del `LineColorBehavior` collegato comportamento nel [ `Entry` ](xref:Xamarin.Forms.Entry) controllo. Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

Quando il valore della `ApplyLineColor` proprietà associata è impostata oppure le modifiche, il `LineColorBehavior` comportamento collegato viene eseguita la `OnApplyLineColorChanged` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

I parametri per questo metodo forniscono l'istanza del controllo a cui il comportamento è collegato a e i valori vecchi e nuovi del `ApplyLineColor` proprietà associata. Il `EntryLineColorEffect` viene aggiunta la classe del controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta se la `ApplyLineColor` proprietà associata è `true`, in caso contrario, viene rimosso dalla proprietà del controllo `Effects` raccolta. Per altre informazioni sui comportamenti, vedere [implementazione di comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

Il `EntryLineColorEffect` sottoclassi il [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe e viene illustrato nell'esempio di codice seguente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

Il [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che è specifica della piattaforma. Ciò semplifica il processo di rimozione dell'effetto, poiché non è previsto l'accesso alle informazioni sul tipo in fase di compilazione per un effetto specifico della piattaforma. Il `EntryLineColorEffect` chiama il costruttore di classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e l'ID univoco specificata su ogni classe effetto specifico della piattaforma.

Nell'esempio di codice riportato di seguito viene illustrato il `eShopOnContainers.EntryLineColorEffect` implementazione per iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

Il `OnAttached` metodo recupera il controllo nativo per xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) controllano e aggiorna il colore della linea chiamando il `UpdateLineColor` (metodo). Il `OnElementPropertyChanged` override risponde alle modifiche di proprietà associabili sul `Entry` controllo aggiornando il colore della linea, se l'oggetto associato `LineColor` le modifiche alle proprietà, o il [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) proprietà il `Entry`le modifiche. Per altre informazioni sugli effetti, vedere [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando in cui vengono immessi dati validi nel [ `Entry` ](xref:Xamarin.Forms.Entry) (controllo), verrà applicata una linea nera nella parte inferiore del controllo, per indicare che non sono presenti errori di convalida. Figura 6-3 mostra un esempio di questo oggetto.

![](validation-images/validation-blackline.png "Linea nera che indica nessun errore di convalida")

**Figura 6-3**: Linea nera che indica nessun errore di convalida

Il [ `Entry` ](xref:Xamarin.Forms.Entry) controllo dispone anche di un [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) aggiunto al relativo [ `Triggers` ](xref:Xamarin.Forms.VisualElement.Triggers) raccolta. Nell'esempio di codice riportato di seguito viene illustrato il `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Ciò [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) monitoraggi il `UserName.IsValid` proprietà e se si tratta valore diventa `false`, esegue il [ `Setter` ](xref:Xamarin.Forms.Setter), quali modifiche il `LineColor` collegati proprietà del `LineColorBehavior` collegato comportamento su rosso. Figura 6-4 viene illustrato un esempio di questo oggetto.

![](validation-images/validation-redline.png "Linea rossa che indica errore di convalida")

**Figura 6-4**: Linea rossa che indica errore di convalida

La riga nel [ `Entry` ](xref:Xamarin.Forms.Entry) controllo rimarrà rosso mentre i dati immessi non sono validi, in caso contrario, verrà modificato in nero per indicare che i dati immessi siano validi.

Per altre informazioni sui trigger, vedere [trigger](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Visualizzazione dei messaggi di errore

L'interfaccia utente vengono visualizzati i messaggi di errore di convalida in controlli etichetta sotto ogni controllo i cui dati non è riuscita la convalida. Nell'esempio di codice riportato di seguito viene illustrato il [ `Label` ](xref:Xamarin.Forms.Label) che visualizza un messaggio di errore di convalida se l'utente non ha immesso un nome utente valido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Ciascuna [ `Label` ](xref:Xamarin.Forms.Label) associa il `Errors` proprietà dell'oggetto modello di visualizzazione in fase di convalida. Il `Errors` proprietà avviene tramite il `ValidatableObject<T>` classe ed è di tipo `List<string>`. Poiché il `Errors` proprietà può contenere più errori di convalida, il `FirstValidationErrorConverter` istanza viene usata per recuperare il primo errore dalla raccolta per la visualizzazione.

## <a name="summary"></a>Riepilogo

L'app per dispositivi mobili di eShopOnContainers esegue la convalida lato client sincrona visualizzazione delle proprietà del modello e informa l'utente di eventuali errori di convalida per evidenziare il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente il motivo per cui i dati non sono validi.

Proprietà modello di visualizzazione che richiede la convalida sono di tipo `ValidatableObject<T>`e ogni `ValidatableObject<T>` istanza dispone di regole di convalida aggiunto al relativo `Validations` proprietà. La convalida viene richiamata dal modello di visualizzazione chiamando il `Validate` metodo del `ValidatableObject<T>` regole di istanza, che recupera la convalida e li esegue sul `ValidatableObject<T>` `Value` proprietà. Eventuali errori di convalida vengono inseriti nel `Errors` proprietà del `ValidatableObject<T>`istanza e il `IsValid` proprietà del `ValidatableObject<T>` istanza viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
