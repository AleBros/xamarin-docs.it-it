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
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760159"
---
# <a name="validation-in-enterprise-apps"></a>Convalida nelle app aziendali

Tutte le app che accettano input dagli utenti devono assicurarsi che l'input sia valido. Un'app può, ad esempio, verificare la presenza di input che contiene solo caratteri in un intervallo specifico, ha una certa lunghezza o corrisponde a un particolare formato. Senza convalida, un utente può fornire dati che causano l'esito negativo dell'app. La convalida impone regole business e impedisce a un utente malintenzionato di inserire dati dannosi.

Nel contesto del modello MVC (Model-View-ViewModel), sarà spesso necessario un modello di visualizzazione o un modello per eseguire la convalida dei dati e segnalare eventuali errori di convalida alla visualizzazione in modo che l'utente possa correggerli. L'app per dispositivi mobili eShopOnContainers esegue la convalida sincrona sul lato client delle proprietà del modello di visualizzazione e notifica all'utente eventuali errori di convalida evidenziando il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente dei motivi per cui i dati non sono validi. La figura 6-1 illustra le classi necessarie per l'esecuzione della convalida nell'app per dispositivi mobili eShopOnContainers.

[Classi di convalida nell'app per dispositivi mobili eShopOnContainers ![(validation-images/validation.png " ")]] (validation-images/validation-large.png#lightbox "Classi di convalida nell'app per dispositivi mobili eShopOnContainers")

**Figura 6-1**: Classi di convalida nell'app per dispositivi mobili eShopOnContainers

Le proprietà del modello di visualizzazione che richiedono la `ValidatableObject<T>`convalida sono di `ValidatableObject<T>` tipo e per ogni istanza sono state `Validations` aggiunte regole di convalida alla relativa proprietà. La convalida viene richiamata dal modello di visualizzazione chiamando `Validate` il metodo `ValidatableObject<T>` dell'istanza, che consente di recuperare le regole di convalida e di `ValidatableObject<T>` `Value` eseguirle sulla proprietà. Eventuali errori di convalida vengono inseriti nella `Errors` proprietà `ValidatableObject<T>` dell'istanza di `ValidatableObject<T>` e la proprietà `IsValid` dell'istanza viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.

La `ExtendedBindableObject` notifica delle modifiche alle proprietà viene fornita dalla classe, pertanto [`Entry`](xref:Xamarin.Forms.Entry) un `IsValid` controllo può essere associato alla proprietà `ValidatableObject<T>` dell'istanza nella classe del modello di visualizzazione per ricevere una notifica che indica se i dati immessi sono validi o meno.

## <a name="specifying-validation-rules"></a>Specifica delle regole di convalida

Le regole di convalida vengono specificate creando una classe che deriva dall' `IValidationRule<T>` interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Questa interfaccia specifica che una classe della regola di convalida deve `boolean` fornire un `Check` metodo utilizzato per eseguire la convalida richiesta e una `ValidationMessage` proprietà il cui valore è il messaggio di errore di convalida che verrà visualizzato se convalida non riuscita.

L'esempio di codice seguente illustra `IsNotNullOrEmptyRule<T>` la regola di convalida, che viene usata per eseguire la convalida del nome utente e della password immessi `LoginView` dall'utente nel quando si usano i servizi fittizi nell'app per dispositivi mobili eShopOnContainers:

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

Il `Check` metodo restituisce un `boolean` valore che indica se l'argomento `null`value è, Empty o è costituito solo da spazi vuoti.

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

Il `Check` metodo restituisce un `boolean` valore che indica se l'argomento value è un indirizzo di posta elettronica valido. Questa operazione viene eseguita eseguendo una ricerca nell'argomento value per la prima occorrenza del criterio di espressione regolare `Regex` specificato nel costruttore. Se il modello di espressione regolare è stato trovato nella stringa di input può essere determinato controllando il valore della `Match` `Success` proprietà dell'oggetto.

> [!NOTE]
> La convalida delle proprietà può talvolta includere proprietà dipendenti. Un esempio di proprietà dipendenti è quando il set di valori validi per la proprietà A dipende dal valore specifico impostato nella proprietà B. Per verificare che il valore della proprietà A sia uno dei valori consentiti comporterebbe il recupero del valore della proprietà B. Inoltre, quando viene modificato il valore della proprietà B, è necessario riconvalidare la proprietà A.

## <a name="adding-validation-rules-to-a-property"></a>Aggiunta di regole di convalida a una proprietà

Nell'app eShopOnContainers per dispositivi mobili, visualizzare le proprietà del modello che richiedono la convalida sono dichiarate `T` di tipo `ValidatableObject<T>`, dove è il tipo di dati da convalidare. Nell'esempio di codice seguente viene illustrato un esempio di due proprietà di questo tipo:

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

Per eseguire la convalida, è necessario aggiungere le regole di convalida `Validations` alla raccolta di `ValidatableObject<T>` ogni istanza, come illustrato nell'esempio di codice seguente:

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

Questo metodo aggiunge la `IsNotNullOrEmptyRule<T>` regola di convalida `Validations` alla raccolta di ogni `ValidatableObject<T>` istanza `ValidationMessage` , specificando i valori per la proprietà della regola di convalida, che specifica il messaggio di errore di convalida che verrà visualizzato se convalida non riuscita.

## <a name="triggering-validation"></a>Attivazione della convalida

L'approccio di convalida usato nell'app per dispositivi mobili eShopOnContainers può attivare manualmente la convalida di una proprietà e attivare automaticamente la convalida quando una proprietà viene modificata.

### <a name="triggering-validation-manually"></a>Attivazione manuale della convalida

La convalida può essere attivata manualmente per una proprietà del modello di visualizzazione. Questa situazione si verifica ad esempio nell'app per dispositivi mobili eShopOnContainers quando l'utente tocca il pulsante di `LoginView`accesso nella, quando si usano i servizi fittizi. Il delegato Command chiama il `MockSignInAsync` metodo nell'oggetto `LoginViewModel`, che richiama la convalida eseguendo il `Validate` metodo, illustrato nell'esempio di codice seguente:

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

Il `Validate` metodo esegue la convalida del nome utente e della password immessi dall'utente `LoginView`in, richiamando il metodo Validate in `ValidatableObject<T>` ogni istanza. Nell'esempio di `ValidatableObject<T>` codice seguente viene illustrato il metodo Validate della classe:

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

Questo metodo cancella la `Errors` raccolta, quindi recupera tutte le regole di convalida aggiunte alla `Validations` raccolta dell'oggetto. Viene `Check` eseguito il metodo per ogni regola di convalida recuperata e `ValidationMessage` il valore della proprietà per qualsiasi regola di convalida che non riesce a convalidare `Errors` i dati viene `ValidatableObject<T>` aggiunto alla raccolta dell'istanza. Infine, la `IsValid` proprietà viene impostata e il relativo valore viene restituito al metodo chiamante, che indica se la convalida ha avuto esito positivo o negativo.

### <a name="triggering-validation-when-properties-change"></a>Attivazione della convalida quando le proprietà cambiano

La convalida può anche essere attivata ogni volta che una proprietà associata viene modificata. Ad esempio, quando un'associazione bidirezionale in `LoginView` imposta la proprietà o `UserName` `Password` , viene attivata la convalida. Nell'esempio di codice seguente viene illustrata questa situazione:

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

Il [`Entry`](xref:Xamarin.Forms.Entry) controllo viene associato `UserName.Value` alla proprietà dell' `ValidatableObject<T>` istanza e alla `Behaviors` raccolta del controllo è stata aggiunta un' `EventToCommandBehavior` istanza. Questo comportamento esegue l'oggetto `ValidateUserNameCommand` in risposta alla generazione dell'`TextChanged`evento [] sull'oggetto `Entry`, `Entry` che viene generato quando il testo in viene modificato. Il `ValidateUserNameCommand` delegato esegue a sua volta il `ValidateUserName` metodo, che esegue il `Validate` metodo sull' `ValidatableObject<T>` istanza. Pertanto, ogni volta che l'utente immette un carattere nel `Entry` controllo per il nome utente, viene eseguita la convalida dei dati immessi.

Per ulteriori informazioni sui comportamenti, vedere [implementazione dei comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Visualizzazione degli errori di convalida

L'app per dispositivi mobili eShopOnContainers invia una notifica all'utente di eventuali errori di convalida evidenziando il controllo che contiene i dati non validi con una linea rossa e visualizzando un messaggio di errore che informa l'utente del motivo per cui i dati non sono validi sotto il controllo contenente il dati non validi. Quando i dati non validi vengono corretti, la riga diventa nera e il messaggio di errore viene rimosso. La figura 6-2 illustra l'oggetto LoginView nell'app per dispositivi mobili eShopOnContainers quando sono presenti errori di convalida.

![](validation-images/validation-login.png "Visualizzazione degli errori di convalida durante l'accesso")

**Figura 6-2:** Visualizzazione degli errori di convalida durante l'accesso

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Evidenziazione di un controllo che contiene dati non validi

Il `LineColorBehavior` comportamento associato viene usato per evidenziare [`Entry`](xref:Xamarin.Forms.Entry) i controlli in cui si sono verificati errori di convalida. Nell'esempio di codice seguente viene illustrato `LineColorBehavior` il modo in cui il comportamento `Entry` associato è associato a un controllo:

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

Il [`Entry`](xref:Xamarin.Forms.Entry) controllo utilizza uno stile esplicito, illustrato nell'esempio di codice seguente:

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

Questo stile imposta le `ApplyLineColor` proprietà `LineColor` associate e del `LineColorBehavior` comportamento associato sul [`Entry`](xref:Xamarin.Forms.Entry) controllo. Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

Quando viene impostato il valore `ApplyLineColor` della proprietà associata, o viene modificato, il `LineColorBehavior` comportamento associato esegue il `OnApplyLineColorChanged` metodo, illustrato nell'esempio di codice seguente:

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

I parametri per questo metodo forniscono l'istanza del controllo a cui è collegato il comportamento e i valori precedenti e nuovi della `ApplyLineColor` proprietà associata. La `EntryLineColorEffect` classe viene aggiunta alla [`Effects`](xref:Xamarin.Forms.Element.Effects) raccolta del controllo se la `ApplyLineColor` proprietà associata è `true`, in caso contrario viene rimossa dalla `Effects` raccolta del controllo. Per ulteriori informazioni sui comportamenti, vedere [implementazione dei comportamenti](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

Le `EntryLineColorEffect` sottoclassi della [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe e vengono mostrate nell'esempio di codice seguente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

La [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno specifico della piattaforma. Ciò semplifica il processo di rimozione dell'effetto, poiché non è previsto l'accesso alle informazioni sul tipo in fase di compilazione per un effetto specifico della piattaforma. `EntryLineColorEffect` Chiama il costruttore della classe base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e dall'ID univoco specificato in ogni classe di effetti specifici della piattaforma.

Nell'esempio di codice seguente viene `eShopOnContainers.EntryLineColorEffect` illustrata l'implementazione di per iOS:

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

Il `OnAttached` metodo recupera il controllo nativo per il controllo Novell. [`Entry`](xref:Xamarin.Forms.Entry) Forms e aggiorna il colore della linea chiamando il `UpdateLineColor` metodo. L' `OnElementPropertyChanged` override risponde alle modifiche delle proprietà associabili `Entry` sul controllo aggiornando il colore della linea se la `LineColor` `Entry` proprietà associata viene modificata o [`Height`](xref:Xamarin.Forms.VisualElement.Height) la proprietà delle modifiche. Per altre informazioni sugli effetti, vedere [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando vengono immessi dati validi nel [`Entry`](xref:Xamarin.Forms.Entry) controllo, viene applicata una linea nera nella parte inferiore del controllo per indicare che non è presente alcun errore di convalida. La figura 6-3 illustra un esempio di questo.

![](validation-images/validation-blackline.png "Linea nera che indica l'assenza di errori di convalida")

**Figura 6-3**: Linea nera che indica l'assenza di errori di convalida

Il [`Entry`](xref:Xamarin.Forms.Entry) controllo dispone anche di [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) un oggetto aggiunto [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) alla relativa raccolta. Nell'esempio di codice seguente viene `DataTrigger`illustrato quanto segue:

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

[`Setter`](xref:Xamarin.Forms.Setter) `false` `LineColor` `LineColorBehavior` Viene monitorata la `UserName.IsValid` proprietà e, se il valore diventa, viene eseguita la proprietà, che modifica la proprietà associata del comportamento associato in rosso. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) La figura 6-4 illustra un esempio di questo.

![](validation-images/validation-redline.png "Linea rossa che indica un errore di convalida")

**Figura 6-4**: Linea rossa che indica un errore di convalida

La riga nel [`Entry`](xref:Xamarin.Forms.Entry) controllo rimarrà rossa mentre i dati immessi non sono validi; in caso contrario, cambierà in nero per indicare che i dati immessi sono validi.

Per ulteriori informazioni sui trigger, vedere [trigger](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Visualizzazione di messaggi di errore

L'interfaccia utente Visualizza i messaggi di errore di convalida nei controlli etichetta sotto ogni controllo i cui dati non sono stati convalidati. Nell'esempio di codice seguente viene [`Label`](xref:Xamarin.Forms.Label) illustrato il che visualizza un messaggio di errore di convalida se l'utente non ha immesso un nome utente valido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Ogni [`Label`](xref:Xamarin.Forms.Label) viene associato `Errors` alla proprietà dell'oggetto modello di visualizzazione da convalidare. La `Errors` proprietà viene fornita `ValidatableObject<T>` dalla classe ed è di tipo `List<string>`. Poiché la `Errors` proprietà può contenere più errori di convalida, `FirstValidationErrorConverter` l'istanza viene utilizzata per recuperare il primo errore dalla raccolta per la visualizzazione.

## <a name="summary"></a>Riepilogo

L'app per dispositivi mobili eShopOnContainers esegue la convalida sincrona sul lato client delle proprietà del modello di visualizzazione e notifica all'utente eventuali errori di convalida evidenziando il controllo che contiene i dati non validi e visualizzando i messaggi di errore che informano l'utente perché i dati non sono validi.

Le proprietà del modello di visualizzazione che richiedono la `ValidatableObject<T>`convalida sono di `ValidatableObject<T>` tipo e per ogni istanza sono state `Validations` aggiunte regole di convalida alla relativa proprietà. La convalida viene richiamata dal modello di visualizzazione chiamando `Validate` il metodo `ValidatableObject<T>` dell'istanza, che consente di recuperare le regole di convalida e di `ValidatableObject<T>` `Value` eseguirle sulla proprietà. Eventuali errori di convalida vengono inseriti nella `Errors` proprietà `ValidatableObject<T>`dell'istanza di `ValidatableObject<T>` e la proprietà `IsValid` dell'istanza viene aggiornata per indicare se la convalida ha avuto esito positivo o negativo.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
