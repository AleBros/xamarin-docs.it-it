---
title: Selezione
description: Questa guida illustra la progettazione e l'utilizzo di controlli di selezione in un'app xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/02/2017
ms.openlocfilehash: 402b17ddbb28fb8896ad0f158fe8dbcd17689f40
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="picker"></a>Selezione

Il controllo di selezione Visualizza ' rotellina ' controllo che contiene un elenco di valori con il valore selezionato viene evidenziato scorrevole. Gli utenti ruota la rotellina per selezionare l'opzione desiderata.

Un utente specifico del case per controlli di selezione per impostare la data e / o l'ora. Per specificare per questo Apple ha creato una sottoclasse della classe UIPickerView chiamata UIDatePicker personalizzata.

L'articolo illustra l'implementazione e utilizzo di [selezione](#picker) e [selezione data](#datepicker) controlli.

<a name="picker">

## <a name="picker"></a>Selezione

### <a name="implementing-a-picker"></a>Implementazione di una selezione

Viene implementato un selettore creando un nuovo [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="pickers-and-storyboards"></a>Le selezioni e gli storyboard

Se si utilizza la finestra di progettazione iOS per creare l'interfaccia utente, la selezione è possibile aggiungere al layout dalla casella degli strumenti:

![](picker-images/image1.png)


### <a name="working-with-picker"></a>Utilizzo di selezione

Dopo aver creato un controllo di selezione, se nel codice o tramite gli storyboard, è necessario assegnare un _modello_ a esso in modo che è possibile passare e interagire con i dati;

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    var pickerModel = new PeopleModel(personLabel);

    personPicker.Model = pickerModel;
}
```

Il codice riportato di seguito viene illustrato un esempio di un modello:

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {   
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

È innanzitutto necessario trasferire alcuni dati per fornire opzioni diverse per un utente di selezionare. Quando possibile tenta di mantenere l'elenco più breve o se è necessario prova a utilizzare più di un 'Componi' (denominata *componenti*):

![Selezione con due componenti](picker-images/image3.png)

Per impostare il numero di componenti, eseguire l'override di `GetComponentCount` metodo: 

```csharp
public override nint GetComponentCount(UIPickerView pickerView)
{
    return 2;
}
```

Il valore restituito indica il numero di quadranti che avrà la selezione.

### <a name="customizing-appearance"></a>Personalizzazione dell'aspetto
 
L'aspetto del `UIPickerView` può essere personalizzato tramite la `UIPickerView.UIPickerViewAppearance` classe o da sottoporre a override la `UIPickerViewModel.GetView` e `UIPickerViewModel.GetRowHeight` metodi il `UIPickerViewModel`.


<a name="datepicker">

## <a name="date-picker"></a>Selezione data

### <a name="implementing-a-date-picker"></a>Implementazione di un controllo selezione data

Viene implementata un controllo selezione data creando un nuovo [ `UIDatePickerView` ](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="date-pickers-and-storyboards"></a>Controlli selezione data e storyboard

Se si utilizza la finestra di progettazione iOS per creare l'interfaccia utente, il **selezione data** possono essere aggiunti al layout dalla casella degli strumenti. Dal riquadro proprietà consente di effettuare le seguenti proprietà:

![](picker-images/image2.png)

* **Modalità** -modalità della data e ora. Può trattarsi di data, ora, data e ora o un conteggio alla rovescia. 
* **Impostazioni locali** : le impostazioni locali di selezione data. Scegliere **predefinito** per impostare il valore predefinito del sistema o su qualsiasi specifico delle impostazioni locali.
* **Intervallo** : Mostra l'incremento in cui verranno visualizzate le opzioni di clock.
* **Data, la data minima, massima data** : imposta la data iniziale verrà visualizzato il selettore e i vincoli per le date selezionabile.

### <a name="configuring-the-datepicker"></a>Configurazione di DatePicker

È possibile limitare l'intervallo di date che un utente può selezionare utilizzando il `MinimumDate` e `MaximumDate` proprietà. Frammento di codice seguente viene illustrato un esempio di come impostare l'intervallo compreso tra 60 anni fa e oggi:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();

components.Year = -60;

NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);

datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

In alternativa, è inoltre possibile utilizzare i controlli di .NET per impostare l'intervallo di date minimo e massimo. Ad esempio:

```csharp
DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
```

È inoltre possibile impostare il `MinuteInterval` proprietà per impostare l'intervallo in corrispondenza del quale il selettore visualizzerà i minuti. Frammento di codice seguente può essere utilizzato per impostare il selettore di minuti da impostare intervalli di 10.

```csharp
datePickerView.MinuteInterval = 10;
```

Sono disponibili quattro modalità di selezione data può essere impostata utilizzando il [ `UIDatePicker.Mode` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.Mode/) proprietà. Nell'elenco seguente viene illustrato un esempio di ciascuno di essi e come implementarlo:

#### <a name="time"></a>Ora

Attiva la modalità di Visualizza l'ora con un selettore dell'ora e minuto e un'indicazione AM o PM facoltativo. È impostato con la `UIDatePickerMode.Time` proprietà. Ad esempio:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

Nella figura seguente viene illustrato un esempio di questa modalità DatePicker:

![](picker-images/image8.png)



#### <a name="date"></a>Data

La modalità di Data consente di visualizzare la data di un mese, giorno e selettore anno. È impostato con la `UIDatePickerMode.Date` proprietà. Ad esempio:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

Nella figura seguente viene illustrato un esempio di questo DatePicker:

![](picker-images/image7.png)

L'ordine dei selettori dipende dalle impostazioni locali dell'il `UIDatePicker`. Per impostazione predefinita questo imposterà il valore predefinito del sistema. L'immagine precedente viene mostrato il layout dei selettori nel `en_US` delle impostazioni locali, ma per modificare un giorno | Mese | Layout di anno, è possibile utilizzare il codice seguente per configurare le impostazioni locali:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![](picker-images/image9.png)


#### <a name="date-and-time"></a>Data e ora

La modalità di data e ora visualizza una shortend della data e l'ora in ore e minuti e un dependings di Designazione AM o PM facoltativo nel se viene utilizzato il formato 12 o 24 ore. È impostato con la `UIDatePickerMode.DateAndTime` proprietà. Ad esempio:

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

Nella figura seguente viene illustrato un esempio di questo DatePicker:

![](picker-images/image6.png)

Come con [data](#Date), l'ordine dei selettori e l'utilizzo di 12 o 24 ore dipende dalle impostazioni locali dell'il `UIDatePicker`.

#### <a name="countdown-timer"></a>Conteggio alla rovescia

Consente di visualizzare la modalità di timer del conto alla rovescia ora e i valori dei minuti. È impostato con la `UIDatePickerMode.CountDownTimer` proprietà. Ad esempio:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

Nella figura seguente viene illustrato un esempio di questo DatePicker:

![](picker-images/image5.png)

È possibile utilizzare il `CountDownDuration` proprietà per acquisire il valore dispayed da selezione data conto alla rovescia. Per aggiungere il valore del conto alla rovescia alla data corrente, ad esempio, è possibile utilizzare il codice seguente:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="formatting"></a>Formattazione 

I valori delle modalità di data, ora e DateAndTime possono essere acquisiti usando il `Date` proprietà il UIDatePicker (ad esempio: `datePickerView.Date`), che è di tipo NSDate. Per formattare la data in un valore più leggibili, utilizzare [ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/). Gli esempi seguenti viene illustrato come utilizzare alcune delle proprietà disponibili in questa classe.

Il `DateFormat` è impostato come una stringa per rappresentare come la data deve essere visualizzata:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

Il `TimeStyle` set di proprietà un `NSDateFormatterStyle`:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

I campi per `NSDateFormatterStyle` visualizzare come segue:

![](picker-images/timestyle.png)

Il `DateStyle` set di proprietà un `NSDateFormatterStyle`:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

I campi per `NSDateFormatterStyle` visualizzare come segue:

![](picker-images/datestyle.png)

È quindi possibile output il NSDate in una stringa formattata usando il codice seguente:

```csharp
dateLabel.Text = dateTimeformat.ToString(datePickerView.Date);
```

## <a name="related-links"></a>Collegamenti correlati

- [PickerControl (esempio)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
