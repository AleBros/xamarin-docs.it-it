---
title: Controllo di selezione in xamarin. IOS
description: Questo documento descrive come progettare e usare i controlli di selezione in un'app xamarin. IOS. Illustra come implementare un controllo di selezione nel codice e in iOS Designer.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 0ef33c2036b1ff2d5a7e2035ca5fa8af58672867
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "34789912"
---
# <a name="picker-control-in-xamarinios"></a>Controllo di selezione in xamarin. IOS

Oggetto [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) consente di selezionare un valore da un elenco da singoli componenti di un'interfaccia simile a rotellina di scorrimento.

Controlli di selezione vengono spesso utilizzati per selezionare una data e ora, Apple offre la [`UIDatePicker`](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/)
classe per questo scopo.

L'articolo descrive come implementare e utilizzare il `UIPickerView` e `UIDatePicker` controlli.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implementazione di un controllo di selezione

Implementare un controllo di selezione creando un nuovo `UIPickerView`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>Controlli di selezione e storyboard

Per creare una selezione nel **iOS Designer**, trascinare un **selezione visualizzazione** dal **della casella degli strumenti** all'area di progettazione.

![Trascinare una visualizzazione della selezione nell'area di progettazione](picker-images/image1.png "trascinare una visualizzazione della selezione nell'area di progettazione")

### <a name="working-with-a-picker-control"></a>Utilizzo di un controllo di selezione

Un controllo di selezione Usa una _modello_ per interagire con i dati:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

Il [ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) classe base implementa due interfacce, [`IUIPickerDataSource`](https://developer.xamarin.com/api/type/UIKit.IUIPickerViewDataSource/)
e [ `IUIPickerViewDelegate` ](https://developer.xamarin.com/api/type/UIKit.IUIPickerViewDelegate/), che dichiarare i vari metodi che specificano i dati del controllo di selezione e come gestisce l'interazione:

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

Un controllo di selezione può includere più colonne, oppure _componenti_. Componenti di partizionare una selezione in più sezioni, che consente la selezione di dati più semplice e più specifico:

![Selezione con due componenti](picker-images/image3.png "selezione con due componenti")

Per specificare il numero di componenti in un controllo di selezione, usare il [`GetComponentCount`](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetComponentCount/p/UIKit.UIPickerView/) 
ProcessOnStatus.

### <a name="customizing-a-pickers-appearance"></a>Personalizzazione dell'aspetto del controllo di selezione

Per personalizzare l'aspetto di un controllo di selezione, usare il [`UIPickerView.UIPickerViewAppearance`](https://developer.xamarin.com/api/type/UIKit.UIPickerView+UIPickerViewAppearance/)
classe o eseguire l'override di [ `GetView` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetView/p/UIKit.UIPickerView/System.nint/System.nint/UIKit.UIView/) e [ `GetRowHeight` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetRowHeight/p/UIKit.UIPickerView/System.nint/) metodi nel `UIPickerViewModel`.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implementazione di un controllo selezione data

Implementare un controllo selezione data creando un `UIDatePicker`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>I controlli selezione data e storyboard

Per creare un controllo selezione data nel **iOS Designer**, trascinare un **Date Picker** dal **della casella degli strumenti** all'area di progettazione.

![Trascinare un controllo selezione data all'area di progettazione](picker-images/image2.png "trascinare un controllo selezione data all'area di progettazione")

### <a name="date-picker-properties"></a>Proprietà di selezione data

#### <a name="minimum-and-maximum-date"></a>Valore minimo e massimo della data

[`MinimumDate`](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MinimumDate/) e [ `MaximumDate` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MaximumDate/) limitare l'intervallo di date disponibili nella selezione data. Ad esempio, il codice seguente vincola un controllo selezione data per gli anni sessanta conducono all'istante presentano:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> È possibile eseguire il cast in modo esplicito un `DateTime` a un `NSDate`:
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervallo di minuti

Il [ `MinuteInterval` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MinuteInterval/) proprietà imposta l'intervallo in corrispondenza del quale lo strumento di selezione verrà visualizzato minuti:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Modalità

I controlli selezione data supportano quattro [modalità](https://developer.xamarin.com/api/type/UIKit.UIDatePickerMode/), come descritto di seguito:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` Visualizza l'ora con una designazione AM o PM facoltativa e un selettore dell'ora e minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` Visualizza la data con un mese, giorno e selettore anno:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

L'ordine dei selettori dipende dalle impostazioni locali del controllo di selezione data, che, per impostazione predefinita, Usa le impostazioni locali del sistema. L'immagine precedente viene mostrato il layout di selettori nel `en_US` delle impostazioni locali, ma la seguente modifica l'ordine per giorno | Mese | Anno:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Giorno | Mese | Anno](picker-images/image9.png "giorno | Mese | Anno")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` Consente di visualizzare un'abbreviata della data e l'ora in ore e minuti e una designazione AM o PM facoltativa (a seconda che venga usato il formato 12 o 24 ore):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Come per gli [ `UIDatePickerMode.Date` ](#uidatepickermodedate), l'ordine dei selettori e l'utilizzo del formato a 12 o 24 ore dipende dalle impostazioni locali di selezione data.

> [!TIP]
> Usare la `Date` proprietà per acquisire il valore di un controllo selezione data in modalità `UIDatePickerMode.Time`, `UIDatePickerMode.Date`, o `UIDatePickerMode.DateAndTime`. Questo valore viene archiviato come un `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` contiene i valori di ora e minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

Il `CountDownDuration` proprietà acquisisce il valore di un controllo selezione data in `UIDatePickerMode.CountDownTimer` modalità. Ad esempio, per aggiungere il valore del conto alla rovescia alla data corrente:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Per formattare un `NSDate`, usare un' [ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/).

Usare un `NSDateFormatter`, chiamare relativi [ `ToString` ](https://developer.xamarin.com/api/member/Foundation.NSDateFormatter.ToString/p/Foundation.NSDate/) (metodo). Ad esempio:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

Il [ `DateFormat` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.DateFormat/) proprietà (una stringa) di un `NSDateFormatter` consente la specifica di formato data personalizzabile:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

Il [ `TimeStyle` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.TimeStyle/) proprietà (un [ `NSDateFormatterStyle` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatterStyle/)) di un `NSDateFormatter` specifica formattazione dell'ora basato su stili predeterminati:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Vari `NSDateFormatterStyle` valori volte in cui verranno visualizzati come indicato di seguito:

- `NSDateFormatterStyle.Full`: Ora legale fuso orientale 19:46:00: 00
- `NSDateFormatterStyle.Long`: 7:47: PM DI NEW YORK
- `NSDateFormatterStyle.Medium`: 19:47:00: 00
- `NSDateFormatterSytle.Short`: 7 47 PM

##### <a name="datestyle"></a>DateStyle

Il [ `DateStyle` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.DateStyle/) proprietà (un `NSDateFormatterStyle`) di un `NSDateFormatter` specifica la formattazione della data basato su stili predeterminati:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Vari `NSDateFormatterStyle` valori visualizzare le date come indicato di seguito:

- `NSDateFormatterStyle.Full`: Mercoledì, 2 ° agosto 2017 alle ore 7:48
- `NSDateFormatterStyle.Long`: 2 agosto 2017 alle ore 7:49
- `NSDateFormatterStyle.Medium`: 2 agosto 2017, 7 49 PM
- `NSDateFormatterStyle.Short`: 8/2/17, PM 7:50

> [!NOTE]
> `DateFormat` e `DateStyle` / `TimeStyle` forniscono diversi modi per specificare formattazione di data e ora. Impostare le proprietà più di recente determinano di output al formattatore di Data.

## <a name="related-links"></a>Collegamenti correlati

- [PickerControl (esempio)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
