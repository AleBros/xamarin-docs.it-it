---
title: Controllo selezione in Novell. iOS
description: Questo documento descrive come progettare e usare i controlli di selezione in un'app Novell. iOS. Viene illustrato come implementare una selezione nel codice e in iOS designer.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/14/2018
ms.openlocfilehash: 9eec99ffe244ffdc290050bd54f083ad6582151d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286392"
---
# <a name="picker-control-in-xamarinios"></a>Controllo selezione in Novell. iOS

Un [`UIPickerView`](xref:UIKit.UIPickerView) oggetto consente di selezionare un valore da un elenco scorrendo i singoli componenti di un'interfaccia simile a una rotellina.

I selezionatori vengono spesso utilizzati per selezionare una data e un'ora. Apple fornisce il[`UIDatePicker`](xref:UIKit.UIDatePicker)
classe a questo scopo.

Questo articolo descrive come implementare e usare i `UIPickerView` controlli e. `UIDatePicker`

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implementazione di una selezione

Implementare una selezione creando un'istanza di un `UIPickerView`nuovo:

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

### <a name="pickers-and-storyboards"></a>Selezionatori e storyboard

Per creare una selezione in **iOS designer**, trascinare una **Visualizzazione selezione** dalla **casella degli strumenti** all'area di progettazione.

![Trascinare una visualizzazione selezione nell'area di progettazione](picker-images/image1.png "Trascinare una visualizzazione selezione nell'area di progettazione")

### <a name="working-with-a-picker-control"></a>Utilizzo di un controllo selezione

Un selettore utilizza un _modello_ per interagire con i dati:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

La [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) classe base implementa due interfacce,[`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
e [`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate), che dichiarano diversi metodi che specificano i dati di un selettore e il modo in cui gestisce l'interazione:

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

Una selezione può contenere più colonne o _componenti_. I componenti partizionano una selezione in più sezioni, consentendo una selezione dei dati più semplice e specifica:

![Selezione con due componenti](picker-images/image3.png "Selezione con due componenti")

Per specificare il numero di componenti in una selezione, utilizzare il[`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
ProcessOnStatus.

### <a name="customizing-a-pickers-appearance"></a>Personalizzazione dell'aspetto di un selettore

Per personalizzare l'aspetto di una selezione, usare il[`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
classe oppure eseguire l' [`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView)) override [`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) dei metodi e `UIPickerViewModel`in.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implementazione di una selezione data

Implementare una selezione data creando un'istanza `UIDatePicker`di:

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

### <a name="date-pickers-and-storyboards"></a>Selezionatori data e storyboard

Per creare una selezione data in **iOS designer**, trascinare una **selezione data** dalla **casella degli strumenti** nell'area di progettazione.

![Trascinare una selezione data nell'area di progettazione](picker-images/image2.png "Trascinare una selezione data nell'area di progettazione")

### <a name="date-picker-properties"></a>Proprietà selezione data

#### <a name="minimum-and-maximum-date"></a>Data minima e massima

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate)e [`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate) limitano l'intervallo di date disponibili nella selezione data. Il codice seguente, ad esempio, vincola una selezione data agli anni 60 che portano all'ora attuale:

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
> È possibile eseguire il cast esplicito di `DateTime` un oggetto `NSDate`a un oggetto:
>
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervallo minuti

La [`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval) proprietà consente di impostare l'intervallo di visualizzazione dei minuti da parte della selezione:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Modalità

I selezionatori Data supportano quattro [modalità](xref:UIKit.UIDatePickerMode), descritte di seguito:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time`Visualizza l'ora con un selettore di ora e minuti e una designazione AM o PM facoltativa:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date`Visualizza la data con selettore mese, giorno e anno:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode. date](picker-images/image7.png "UIDatePickerMode. date")

L'ordine dei selettori dipende dalle impostazioni locali della selezione data, che per impostazione predefinita usa le impostazioni locali del sistema. L'immagine precedente Mostra il layout dei selettori nelle `en_US` impostazioni locali, ma il seguente modifica l'ordine in Day | Mese | Anno

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Giorno | Mese | ](picker-images/image9.png "Giorno dell'anno | Mese | Anno")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime`Visualizza una vista abbreviata della data, l'ora in ore e minuti e una designazione AM o PM facoltativa (a seconda che venga usato un clock di 12 o 24 ore):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Come per [`UIDatePickerMode.Date`](#uidatepickermodedate), l'ordine dei selettori e l'utilizzo di un clock di 12 o 24 ore dipendono dalle impostazioni locali della selezione data.

> [!TIP]
> Utilizzare la `Date` proprietà per acquisire il valore di un selettore di `UIDatePickerMode.Time`data `UIDatePickerMode.Date`in modalità `UIDatePickerMode.DateAndTime`, o. Questo valore viene archiviato come `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer`Visualizza i valori di ora e minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

La `CountDownDuration` proprietà acquisisce il valore di un selettore `UIDatePickerMode.CountDownTimer` di data in modalità. Ad esempio, per aggiungere il valore del conto alla rovescia alla data corrente:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Per formattare un `NSDate`oggetto, usare [`NSDateFormatter`](xref:Foundation.NSDateFormatter)un oggetto.

Per usare un `NSDateFormatter`oggetto, chiamare [`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate)) il relativo metodo. Ad esempio:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

La [`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat) proprietà (una stringa) di un `NSDateFormatter` oggetto consente una specifica di formato di data personalizzabile:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

La [`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle) proprietà (un [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) di un `NSDateFormatter` oggetto specifica la formattazione dell'ora basata sugli stili predeterminati:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

I `NSDateFormatterStyle` vari valori visualizzano gli orari seguenti:

- `NSDateFormatterStyle.Full`: 7:46:00 PM ora legale orientale
- `NSDateFormatterStyle.Long`: 7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`: 7:47:00 PM
- `NSDateFormatterSytle.Short`: 7:47 PM

##### <a name="datestyle"></a>DateStyle

La [`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle) proprietà `NSDateFormatterStyle`(a) di un `NSDateFormatter` oggetto specifica la formattazione della data in base agli stili predeterminati:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

I `NSDateFormatterStyle` vari valori visualizzano le date come segue:

- `NSDateFormatterStyle.Full`: Mercoledì, 2 agosto 2017 alle 7:48 PM
- `NSDateFormatterStyle.Long`: 2 agosto 2017 alle 7:49 PM
- `NSDateFormatterStyle.Medium`: 2 agosto 2017, 7:49 PM
- `NSDateFormatterStyle.Short`: 8/2/17, 7:50 PM

> [!NOTE]
> `DateFormat`e `DateStyle` forniscono modi diversi per / `TimeStyle` specificare la formattazione di data e ora. Le proprietà impostate più di recente determinano l'output del formattatore di data.

## <a name="related-links"></a>Collegamenti correlati

- [PickerControl (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/pickercontrol)
