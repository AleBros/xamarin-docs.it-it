---
title: Convalida nelle app aziendali
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers esegue la convalida dell'input dell'utente. Ciò include la specifica delle regole di convalida, l'attivazione della convalida e la visualizzazione degli errori di convalida.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: de5728710a408b8e0c7c68dc89c7e6484cbcc3ce
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70760159"
---
# <a name="validation-in-enterprise-apps"></a>Convalida nelle app aziendali

Tutte le app che accettano input dagli utenti devono assicurarsi che l'input sia valido. Un'app può, ad esempio, verificare la presenza di input che contiene solo caratteri in un intervallo specifico, ha una certa lunghezza o corrisponde a un particolare formato. Senza convalida, un utente può fornire dati che causano l'esito negativo dell'app. La convalida impone regole business e impedisce a un utente malintenzionato di inserire dati dannosi.

Nel contesto del modello MVC (Model-View-ViewModel), sarà spesso necessario un modello di visualizzazione o un modello per eseguire la convalida dei dati e segnalare eventuali errori di convalida alla visualizzazione in modo che l'utente possa correggerli. L'app per dispositivi mobili eShopOnContainers esegue la convalida sincrona sul lato client delle proprietà del modello di visualizzazione e notifica all'utente eventuali errori di convalida evidenziando il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente dei motivi per cui i dati non sono validi. La figura 6-1 illustra le classi necessarie per l'esecuzione della convalida nell'app per dispositivi mobili eShopOnContainers.

[![](validation-images/validation.png "Validation classes in the eShopOnContainers mobile app")](validation-images/validation-large.png#lightbox "Validation classes in the eShopOnContainers mobile app")

**Figura 6-1**: classi di convalida nell'app per dispositivi mobili eShopOnContainers

Le proprietà del modello di visualizzazione che richiedono la convalida sono di tipo `ValidatableObject<T>` e ogni istanza di `ValidatableObject<T>` dispone di regole di convalida aggiunte alla relativa proprietà `Validations`. La convalida viene richiamata dal modello di vista chiamando il metodo di `Validate` dell'istanza di `ValidatableObject<T>`, che recupera le regole di convalida e le esegue con la proprietà di `Value` `ValidatableObject<T>`. Gli eventuali errori di convalida vengono inseriti nella proprietà `Errors` dell'istanza di `ValidatableObject<T>` e la proprietà `IsValid` dell'istanza `ValidatableObject<T>` viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.

La notifica delle modifiche alle proprietà viene fornita dalla classe `ExtendedBindableObject`, quindi un controllo [`Entry`](xref:Xamarin.Forms.Entry) può essere associato alla proprietà `IsValid` dell'istanza di `ValidatableObject<T>` nella classe del modello di visualizzazione per ricevere una notifica che indica se i dati immessi sono validi o meno.

## <a name="specifying-validation-rules"></a>Specifica delle regole di convalida

Le regole di convalida vengono specificate creando una classe che deriva dall'interfaccia `IValidationRule<T>`, illustrata nell'esempio di codice seguente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Questa interfaccia specifica che una classe della regola di convalida deve fornire un `boolean` `Check` metodo utilizzato per eseguire la convalida richiesta e una proprietà `ValidationMessage` il cui valore è il messaggio di errore di convalida che verrà visualizzato in caso di esito negativo della convalida.

L'esempio di codice seguente illustra la regola di convalida `IsNotNullOrEmptyRule<T>`, che viene usata per eseguire la convalida del nome utente e della password immessi dall'utente nell'`LoginView` quando si usano i servizi fittizi nell'app per dispositivi mobili eShopOnContainers:

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

Il metodo `Check` restituisce un `boolean` che indica se l'argomento value è `null`, Empty o è costituito solo da spazi vuoti.

Sebbene non venga usato dall'app per dispositivi mobili eShopOnContainers, l'esempio di codice seguente mostra una regola di convalida per la convalida degli indirizzi di posta elettronica:

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

Il metodo `Check` restituisce un `boolean` che indica se l'argomento value è un indirizzo di posta elettronica valido. Questa operazione viene eseguita eseguendo una ricerca nell'argomento value per la prima occorrenza del criterio di espressione regolare specificato nel costruttore `Regex`. Se il modello di espressione regolare è stato trovato nella stringa di input può essere determinato controllando il valore della proprietà `Success` dell'oggetto `Match`.

> [!NOTE]
> La convalida delle proprietà può talvolta includere proprietà dipendenti. Un esempio di proprietà dipendenti è quando il set di valori validi per la proprietà A dipende dal valore specifico impostato nella proprietà B. Per verificare che il valore della proprietà A sia uno dei valori consentiti comporterebbe il recupero del valore della proprietà B. Inoltre, quando viene modificato il valore della proprietà B, è necessario riconvalidare la proprietà A.

## <a name="adding-validation-rules-to-a-property"></a>Aggiunta di regole di convalida a una proprietà

Nell'app eShopOnContainers per dispositivi mobili, visualizzare le proprietà del modello che richiedono la convalida sono dichiarate di tipo `ValidatableObject<T>`, dove `T` è il tipo di dati da convalidare. Nell'esempio di codice seguente viene illustrato un esempio di due proprietà di questo tipo:

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

Per eseguire la convalida, è necessario aggiungere le regole di convalida alla raccolta `Validations` di ogni istanza di `ValidatableObject<T>`, come illustrato nell'esempio di codice seguente:

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

Questo metodo aggiunge la regola di convalida `IsNotNullOrEmptyRule<T>` alla raccolta `Validations` di ogni istanza di `ValidatableObject<T>`, specificando i valori per la proprietà `ValidationMessage` della regola di convalida, che specifica il messaggio di errore di convalida che verrà visualizzato in caso di esito negativo della convalida.

## <a name="triggering-validation"></a>Attivazione della convalida

L'approccio di convalida usato nell'app per dispositivi mobili eShopOnContainers può attivare manualmente la convalida di una proprietà e attivare automaticamente la convalida quando una proprietà viene modificata.

### <a name="triggering-validation-manually"></a>Attivazione manuale della convalida

La convalida può essere attivata manualmente per una proprietà del modello di visualizzazione. Questa situazione si verifica ad esempio nell'app per dispositivi mobili eShopOnContainers quando l'utente tocca il pulsante di **accesso** nella `LoginView` quando si usano i servizi fittizi. Il delegato Command chiama il metodo `MockSignInAsync` nel `LoginViewModel`, che richiama la convalida eseguendo il metodo `Validate`, illustrato nell'esempio di codice seguente:

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

Il metodo `Validate` esegue la convalida del nome utente e della password immessi dall'utente sul `LoginView`, richiamando il metodo Validate in ogni istanza di `ValidatableObject<T>`. Nell'esempio di codice seguente viene illustrato il metodo Validate della classe `ValidatableObject<T>`:

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

Questo metodo cancella l'insieme di `Errors` e recupera tutte le regole di convalida aggiunte alla raccolta di `Validations` dell'oggetto. Viene eseguito il metodo `Check` per ogni regola di convalida recuperata e il valore della proprietà `ValidationMessage` per qualsiasi regola di convalida che non riesce a convalidare i dati viene aggiunto alla raccolta `Errors` dell'istanza di `ValidatableObject<T>`. Infine, viene impostata la proprietà `IsValid` e il relativo valore viene restituito al metodo chiamante, che indica se la convalida ha avuto esito positivo o negativo.

### <a name="triggering-validation-when-properties-change"></a>Attivazione della convalida quando le proprietà cambiano

La convalida può anche essere attivata ogni volta che una proprietà associata viene modificata. Ad esempio, quando un'associazione bidirezionale nel `LoginView` imposta la proprietà `UserName` o `Password`, viene attivata la convalida. Nell'esempio di codice seguente viene illustrata questa situazione:

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

Il controllo [`Entry`](xref:Xamarin.Forms.Entry) viene associato alla proprietà `UserName.Value` dell'istanza di `ValidatableObject<T>` e alla raccolta `Behaviors` del controllo è stata aggiunta un'istanza di `EventToCommandBehavior`. Questo comportamento esegue l'`ValidateUserNameCommand` in risposta all'attivazione dell'evento [`TextChanged`] sul `Entry`, che viene generato quando il testo nel `Entry` viene modificato. Il delegato `ValidateUserNameCommand` esegue a sua volta il metodo `ValidateUserName`, che esegue il metodo `Validate` sull'istanza di `ValidatableObject<T>`. Pertanto, ogni volta che l'utente immette un carattere nel controllo `Entry` per il nome utente, viene eseguita la convalida dei dati immessi.

Per ulteriori informazioni sui comportamenti, vedere [implementazione dei comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Visualizzazione degli errori di convalida

L'app per dispositivi mobili eShopOnContainers invia una notifica all'utente di eventuali errori di convalida evidenziando il controllo che contiene i dati non validi con una linea rossa e visualizzando un messaggio di errore che informa l'utente del motivo per cui i dati non sono validi sotto il controllo contenente il dati non validi. Quando i dati non validi vengono corretti, la riga diventa nera e il messaggio di errore viene rimosso. La figura 6-2 illustra l'oggetto LoginView nell'app per dispositivi mobili eShopOnContainers quando sono presenti errori di convalida.

![](validation-images/validation-login.png "Displaying validation errors during login")

**Figura 6-2:** Visualizzazione degli errori di convalida durante l'accesso

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Evidenziazione di un controllo che contiene dati non validi

Il `LineColorBehavior` comportamento associato viene usato per evidenziare [`Entry`](xref:Xamarin.Forms.Entry) controlli in cui si sono verificati errori di convalida. Nell'esempio di codice seguente viene illustrato il modo in cui il `LineColorBehavior` comportamento associato è associato a un controllo `Entry`:

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

Il controllo [`Entry`](xref:Xamarin.Forms.Entry) utilizza uno stile esplicito, illustrato nell'esempio di codice seguente:

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

Questo stile imposta la `ApplyLineColor` e `LineColor` le proprietà associate del `LineColorBehavior` comportamento associato sul controllo [`Entry`](xref:Xamarin.Forms.Entry) . Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

Quando il valore della proprietà associata `ApplyLineColor` viene impostato, o viene modificato, il `LineColorBehavior` comportamento collegato esegue il metodo `OnApplyLineColorChanged`, illustrato nell'esempio di codice seguente:

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

I parametri per questo metodo forniscono l'istanza del controllo a cui è collegato il comportamento e i valori precedenti e nuovi della proprietà associata `ApplyLineColor`. La classe `EntryLineColorEffect` viene aggiunta alla raccolta di [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo se la proprietà associata `ApplyLineColor` è `true`; in caso contrario, viene rimossa dalla raccolta di `Effects` del controllo. Per ulteriori informazioni sui comportamenti, vedere [implementazione dei comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

Il `EntryLineColorEffect` sottoclassi della classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) e viene illustrato nell'esempio di codice seguente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

La classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno specifico della piattaforma. Ciò semplifica il processo di rimozione dell'effetto, poiché non è previsto l'accesso alle informazioni sul tipo in fase di compilazione per un effetto specifico della piattaforma. Il `EntryLineColorEffect` chiama il costruttore della classe base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e dall'ID univoco specificato in ogni classe di effetti specifici della piattaforma.

Nell'esempio di codice seguente viene illustrata l'implementazione di `eShopOnContainers.EntryLineColorEffect` per iOS:

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

Il metodo `OnAttached` Recupera il controllo nativo per il controllo [`Entry`](xref:Xamarin.Forms.Entry) Novell. Forms e aggiorna il colore della linea chiamando il metodo `UpdateLineColor`. L'override del `OnElementPropertyChanged` risponde alle modifiche delle proprietà associabili sul controllo `Entry` aggiornando il colore della linea se la proprietà `LineColor` associata viene modificata o se la proprietà [`Height`](xref:Xamarin.Forms.VisualElement.Height) del `Entry` viene modificata. Per altre informazioni sugli effetti, vedere [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando vengono immessi dati validi nel controllo [`Entry`](xref:Xamarin.Forms.Entry) , viene applicata una linea nera nella parte inferiore del controllo per indicare che non è presente alcun errore di convalida. La figura 6-3 illustra un esempio di questo.

![](validation-images/validation-blackline.png "Black line indicating no validation error")

**Figura 6-3**: riga nera che indica l'assenza di errori di convalida

Il controllo [`Entry`](xref:Xamarin.Forms.Entry) dispone anche di un [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) aggiunto alla raccolta di [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) . Nell'esempio di codice seguente viene illustrata la `DataTrigger`:

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

Questo [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) monitora la proprietà `UserName.IsValid` e, se il valore diventa `false`, esegue il [`Setter`](xref:Xamarin.Forms.Setter), che modifica la proprietà associata `LineColor` del comportamento `LineColorBehavior` associato in rosso. La figura 6-4 illustra un esempio di questo.

![](validation-images/validation-redline.png "Red line indicating validation error")

**Figura 6-4**: riga rossa che indica un errore di convalida

La riga nel controllo [`Entry`](xref:Xamarin.Forms.Entry) rimarrà rossa mentre i dati immessi non sono validi; in caso contrario, cambierà in nero per indicare che i dati immessi sono validi.

Per ulteriori informazioni sui trigger, vedere [trigger](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Visualizzazione di messaggi di errore

L'interfaccia utente Visualizza i messaggi di errore di convalida nei controlli etichetta sotto ogni controllo i cui dati non sono stati convalidati. Nell'esempio di codice seguente viene illustrata la [`Label`](xref:Xamarin.Forms.Label) che visualizza un messaggio di errore di convalida se l'utente non ha immesso un nome utente valido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Ogni [`Label`](xref:Xamarin.Forms.Label) viene associato alla proprietà `Errors` dell'oggetto modello di visualizzazione da convalidare. La proprietà `Errors` viene fornita dalla classe `ValidatableObject<T>` ed è di tipo `List<string>`. Poiché la proprietà `Errors` può contenere più errori di convalida, viene utilizzata l'istanza `FirstValidationErrorConverter` per recuperare il primo errore dalla raccolta per la visualizzazione.

## <a name="summary"></a>Riepilogo

L'app per dispositivi mobili eShopOnContainers esegue la convalida sincrona sul lato client delle proprietà del modello di visualizzazione e notifica all'utente eventuali errori di convalida evidenziando il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente perché i dati non sono validi.

Le proprietà del modello di visualizzazione che richiedono la convalida sono di tipo `ValidatableObject<T>` e ogni istanza di `ValidatableObject<T>` dispone di regole di convalida aggiunte alla relativa proprietà `Validations`. La convalida viene richiamata dal modello di vista chiamando il metodo di `Validate` dell'istanza di `ValidatableObject<T>`, che recupera le regole di convalida e le esegue con la proprietà di `Value` `ValidatableObject<T>`. Gli eventuali errori di convalida vengono inseriti nella proprietà `Errors` della `ValidatableObject<T>`instance e la proprietà `IsValid` dell'istanza di `ValidatableObject<T>` viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
