---
title: Convalida
ms.topic: article
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: cb87593b63e28c01beacdea479cc9d6ec4aceb9b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="validation"></a>Convalida

Qualsiasi app che accetta l'input dell'utente è necessario assicurarsi che l'input è valido. Un'app potrebbe, ad esempio, controllare per l'input che contiene solo i caratteri in un intervallo specifico, è una certa lunghezza o corrisponde a un determinato formato. Senza convalida, l'utente può fornire dati che causano l'esito negativo dell'app. Convalida applica le regole di business e impedisce che un utente malintenzionato inserendo dati dannosi.

Nel contesto del modello-modello ViewModel (MVVM) di schema, un modello di visualizzazione o modello sarà spesso necessario per eseguire la convalida dei dati e segnalare gli errori di convalida per la visualizzazione in modo che l'utente può correggere questi ultimi. L'app mobile eShopOnContainers esegue la convalida lato client sincrona visualizzazione delle proprietà del modello e notifica all'utente eventuali errori di convalida per evidenziare il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente del motivo per cui i dati non sono validi. Figura 6-1 vengono illustrate le classi necessarie per l'esecuzione di convalida nell'app mobile eShopOnContainers.

[![](validation-images/validation.png "Classi di convalida nell'app mobile eShopOnContainers")](validation-images/validation-large.png#lightbox "classi di convalida nell'app mobile eShopOnContainers")

**Figura 6-1**: le classi di convalida nell'app mobile eShopOnContainers

Visualizzare le proprietà di modello che richiedono la convalida sono di tipo `ValidatableObject<T>`e ogni `ValidatableObject<T>` istanza dispone di regole di convalida aggiunte relativo `Validations` proprietà. Convalida viene richiamata dal modello di visualizzazione chiamando il `Validate` metodo il `ValidatableObject<T>` regole di istanza, che recupera la convalida e li esegue contro il `ValidatableObject<T>` `Value` proprietà. Eventuali errori di convalida vengono inseriti il `Errors` proprietà del `ValidatableObject<T>` istanza e `IsValid` proprietà del `ValidatableObject<T>` istanza viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.

La notifica di modifiche viene fornita dal `ExtendedBindableObject` (classe) e pertanto un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo è possibile associare il `IsValid` proprietà di `ValidatableObject<T>` istanza nella classe di modello di visualizzazione per ricevere una notifica o meno i dati immessi non validi.

## <a name="specifying-validation-rules"></a>Specifica le regole di convalida

Le regole di convalida vengono specificate mediante la creazione di una classe che deriva dal `IValidationRule<T>` interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Questa interfaccia consente di specificare che una classe di regola di convalida deve fornire un `boolean` `Check` metodo utilizzato per eseguire la convalida richiesta, e un `ValidationMessage` proprietà il cui valore è il messaggio di errore di convalida che verrà visualizzato se convalida non riesce.

Nell'esempio di codice riportato di seguito viene illustrato il `IsNotNullOrEmptyRule<T>` regola di convalida, viene utilizzato per eseguire la convalida del nome utente e della password immessi dall'utente `LoginView` quando si utilizza servizi fittizi nell'app mobile eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

Il `Check` metodo restituisce un `boolean` che indica se l'argomento di valore è `null`, vuota o costituita solo da spazi vuoti.

Anche se non è utilizzato dall'app mobile eShopOnContainers, il codice seguente illustra una regola di convalida per la convalida di indirizzi di posta elettronica:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

Il `Check` metodo restituisce un `boolean` che indica se l'argomento di valore è un indirizzo di posta elettronica valido. A questo scopo, l'argomento di valore per la prima occorrenza del criterio di espressione regolare specificata nella ricerca di `Regex` costruttore. Se il criterio di espressione regolare rilevato nella stringa di input può essere determinato dal controllando il valore del `Match` dell'oggetto `Success` proprietà.

> [!NOTE]
> La convalida delle proprietà che può comportare talvolta le proprietà dipendenti. Un esempio di proprietà dipendente è quando il set di valori validi per la proprietà dipende dal valore specifico che è stato impostato nella proprietà B. Per verificare che il valore della proprietà A è uno dei valori consentiti comporta il recupero del valore della proprietà B. Inoltre, quando viene modificato il valore della proprietà B, proprietà A dovrebbe essere riconvalidati.

## <a name="adding-validation-rules-to-a-property"></a>Aggiunta di regole di convalida a una proprietà

Nell'app mobile eShopOnContainers, visualizzare le proprietà di modello che richiedono la convalida vengono dichiarati come di tipo `ValidatableObject<T>`, dove `T` è il tipo di dati da convalidare. Esempio di codice seguente viene illustrato un esempio di due proprietà:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Per la convalida, è necessario aggiungere regole di convalida per il `Validations` insieme di ogni `ValidatableObject<T>` istanza, come illustrato nell'esempio di codice seguente:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Questo metodo aggiunge il `IsNotNullOrEmptyRule<T>` regola di convalida per il `Validations` insieme di ogni `ValidatableObject<T>` istanza, specificando i valori per la regola di convalida `ValidationMessage` proprietà che specifica il messaggio di errore di convalida che verrà visualizzato se convalida non riesce.

## <a name="triggering-validation"></a>Attivare la convalida

L'approccio di convalida utilizzato nell'app mobile eShopOnContainers può attivare manualmente la convalida di una proprietà e automaticamente la convalida di trigger quando viene modificata una proprietà.

### <a name="triggering-validation-manually"></a>Attivare manualmente la convalida

La convalida può essere attivata manualmente per una proprietà del modello di visualizzazione. Ad esempio, ciò si verifica nell'app mobile eShopOnContainers quando l'utente tocca il **accesso** pulsante il `LoginView`, quando si utilizzano servizi fittizi. Il comando delegato chiama il `MockSignInAsync` metodo nel `LoginViewModel`, che richiama la convalida eseguendo il `Validate` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

Il `Validate` metodo esegue la convalida del nome utente e della password immessi dall'utente `LoginView`, richiamando il metodo di convalida su ogni `ValidatableObject<T>` istanza. Esempio di codice seguente viene illustrato il metodo di convalida dalla `ValidatableObject<T>` classe:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Questo metodo cancella il `Errors` insieme, quindi recupera alcuna convalida delle regole che sono stati aggiunti all'oggetto `Validations` insieme. Il `Check` esecuzione del metodo per ogni regola di convalida recuperati e `ValidationMessage` viene aggiunto il valore di proprietà per qualsiasi regola di convalida non riesce a convalidare i dati di `Errors` insieme del `ValidatableObject<T>` istanza. Infine, il `IsValid` proprietà è impostata e il relativo valore viene restituito al metodo di chiamata, che indica se la convalida ha avuto esito positivo o negativo.

### <a name="triggering-validation-when-properties-change"></a>Attivare la convalida quando vengono modificate

La convalida viene attivata automaticamente anche ogni volta che viene modificata una proprietà associata. Ad esempio, quando un'associazione bidirezionale nel `LoginView` imposta il `UserName` o `Password` proprietà, la convalida viene attivata. Esempio di codice seguente viene illustrato come questo errore si verifica:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

Il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) associato il controllo di `UserName.Value` proprietà del `ValidatableObject<T>` istanza e il controllo `Behaviors` raccolta ha un `EventToCommandBehavior` aggiunto all'istanza. Questo comportamento viene eseguito il `ValidateUserNameCommand` in risposta al [`TextChanged`] generazione dell'evento sul `Entry`, che viene generato quando il testo nel `Entry` le modifiche. A sua volta, il `ValidateUserNameCommand` delegato viene eseguito il `ValidateUserName` metodo, che esegue il `Validate` metodo il `ValidatableObject<T>` istanza. Pertanto, ogni volta che l'utente immette un carattere di `Entry` viene eseguito il controllo per il nome utente, la convalida dei dati immessi.

Per ulteriori informazioni sui comportamenti, vedere [implementare comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>La visualizzazione degli errori di convalida

L'app mobile eShopOnContainers notifica all'utente eventuali errori di convalida evidenziando il controllo che contiene i dati non validi con una riga rossa e visualizzando un messaggio di errore che informa l'utente perché non sono validi sotto il controllo che contiene i dati di dati non validi. Quando i dati non validi viene risolto, la riga cambierà in nero e viene rimosso il messaggio di errore. Figura 6-2 viene mostrato il LoginView nell'app mobile eShopOnContainers quando sono presenti errori di convalida.

![](validation-images/validation-login.png "La visualizzazione degli errori di convalida durante l'accesso")

**Figura 6-2:** la visualizzazione di errori di convalida durante l'accesso

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Evidenziare un controllo che contiene dati non validi

Il `LineColorBehavior` comportamento collegato viene utilizzato per evidenziare [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlli in cui si sono verificati errori di convalida. Nell'esempio di codice riportato di seguito viene illustrato come la `LineColorBehavior` comportamento collegato è collegato a un `Entry` controllo:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP, WinRT, WinPhone" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

Il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo utilizza uno stile esplicito, come illustrato nell'esempio di codice seguente:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Imposta questo stile di `ApplyLineColor` e `LineColor` le proprietà associate del `LineColorBehavior` collegata comportamento il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo. Per ulteriori informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

Quando il valore della `ApplyLineColor` proprietà associata è impostata oppure le modifiche, il `LineColorBehavior` comportamento collegato viene eseguita la `OnApplyLineColorChanged` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

I parametri per questo metodo forniscono l'istanza del controllo a cui è associato il comportamento e i valori vecchi e nuovi del `ApplyLineColor` proprietà associata. Il `EntryLineColorEffect` classe viene aggiunto al controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) raccolta se il `ApplyLineColor` proprietà associata è `true`, in caso contrario viene rimosso dalla proprietà del controllo `Effects` insieme. Per ulteriori informazioni sui comportamenti, vedere [implementare comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

Il `EntryLineColorEffect` sottoclassi di [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe e viene illustrato nell'esempio di codice seguente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

Il [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che è specifica della piattaforma. Questa operazione semplifica il processo di rimozione effetto, poiché non è possibile accedere in fase di compilazione per le informazioni sul tipo per un effetto specifico della piattaforma. Il `EntryLineColorEffect` chiama il costruttore di classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e l'ID univoco specificato su ogni classe effetto specifico della piattaforma.

Nell'esempio di codice riportato di seguito viene illustrato il `eShopOnContainers.EntryLineColorEffect` implementazione per iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

Il `OnAttached` metodo recupera il controllo nativo per il xamarin. Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllare e aggiorna il colore della linea chiamando il `UpdateLineColor` metodo. Il `OnElementPropertyChanged` override risponde alle modifiche di proprietà associabile nel `Entry` controllo aggiornando il colore della linea se collegato `LineColor` modifiche delle proprietà, o [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) proprietà del `Entry`modifiche. Per ulteriori informazioni sugli effetti, vedere [effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

Immissione di dati validi nel [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) (controllo), verrà applicata una linea nera nella parte inferiore del controllo, per indicare che non si verificano errori di convalida. Figura 6-3 viene illustrato un esempio di questo oggetto.

![](validation-images/validation-blackline.png "Linea nera che indica nessun errore di convalida")

**Figura 6-3**: linea nera che indica nessun errore di convalida

Il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo dispone anche di un [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) aggiunto alla relativa [ `Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) insieme. Nell'esempio di codice riportato di seguito viene illustrato il `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Questo [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) monitoraggi il `UserName.IsValid` proprietà e in caso di valore diventa `false`, viene eseguito il [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/), quali modifiche di `LineColor` collegato proprietà del `LineColorBehavior` collegato comportamento a rosso. Figura 6-4 viene illustrato un esempio di questo oggetto.

![](validation-images/validation-redline.png "Linea rossa che indica errore di convalida")

**Figura 6-4**: la linea rossa che indica errore di convalida

La riga di [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo rimarrà rosso mentre i dati immessi non sono validi, in caso contrario verrà modificato in gradazioni di grigio per indicare che i dati immessi siano validi.

Per ulteriori informazioni sui trigger, vedere [trigger](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Visualizzazione di messaggi di errore

L'interfaccia utente visualizza messaggi di errore di convalida nei controlli etichetta sotto ogni controllo i cui dati di convalida non riuscita. Nell'esempio di codice riportato di seguito viene illustrato il [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) che viene visualizzato un messaggio di errore di convalida se l'utente non ha immesso un nome utente valido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Ogni [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) associa il `Errors` proprietà dell'oggetto modello di visualizzazione in fase di convalida. Il `Errors` proprietà viene fornita per il `ValidatableObject<T>` ed è di tipo `List<string>`. Poiché il `Errors` proprietà può contenere più errori di convalida, il `FirstValidationErrorConverter` istanza viene utilizzata per recuperare il primo errore dalla raccolta per la visualizzazione.

## <a name="summary"></a>Riepilogo

L'app mobile eShopOnContainers esegue la convalida lato client sincrona visualizzazione delle proprietà del modello e notifica all'utente eventuali errori di convalida per evidenziare il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente motivo per cui i dati non sono validi.

Visualizzare le proprietà di modello che richiedono la convalida sono di tipo `ValidatableObject<T>`e ogni `ValidatableObject<T>` istanza dispone di regole di convalida aggiunte relativo `Validations` proprietà. Convalida viene richiamata dal modello di visualizzazione chiamando il `Validate` metodo il `ValidatableObject<T>` regole di istanza, che recupera la convalida e li esegue contro il `ValidatableObject<T>` `Value` proprietà. Eventuali errori di convalida vengono inseriti il `Errors` proprietà del `ValidatableObject<T>`istanza e `IsValid` proprietà del `ValidatableObject<T>` istanza viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
