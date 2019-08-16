---
title: Completamento automatico per Novell. Android
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/31/2018
ms.openlocfilehash: 186dab1d48d928426d223c8961ce21846c351107
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523083"
---
# <a name="auto-complete-for-xamarinandroid"></a>Completamento automatico per Novell. Android

`AutoCompleteTextView`è un elemento di visualizzazione di testo modificabile che mostra automaticamente i suggerimenti di completamento mentre l'utente sta digitando. L'elenco di suggerimenti viene visualizzato in un menu a discesa da cui l'utente può scegliere un elemento in cui sostituire il contenuto della casella di modifica.

![Esempio di completamento automatico](images/auto-complete.png)

## <a name="overview"></a>Panoramica

Per creare un widget immissione testo che fornisca suggerimenti per il completamento automatico, usare il[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget. I suggerimenti vengono ricevuti da una raccolta di stringhe associate al widget tramite un [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)oggetto.

In questa esercitazione verrà creato un[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget che fornisce suggerimenti per un nome di paese.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

[`TextView`](xref:Android.Widget.TextView) È un'etichetta che introduce[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget.


## <a name="tutorial"></a>Esercitazione

Avviare un nuovo progetto denominato *HelloAutoComplete*.

Creare un file XML denominato `list_item.xml` e salvarlo nella cartella Resources **/layout** . Impostare l'azione di compilazione del file su `AndroidResource`. Modificare il file in modo che abbia un aspetto simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp"
    android:textColor="#000">
</TextView> 
```

Questo file definisce un semplice [`TextView`](xref:Android.Widget.TextView) che verrà usato per ogni elemento visualizzato nell'elenco di suggerimenti.

Aprire **risorse/layout/Main. aXML** e inserire quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

Aprire **MainActivity.cs** e inserire il codice seguente per[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    AutoCompleteTextView textView = FindViewById<AutoCompleteTextView> (Resource.Id.autocomplete_country);
    var adapter = new ArrayAdapter<String> (this, Resource.Layout.list_item, COUNTRIES);

    textView.Adapter = adapter;
}
```

Dopo aver impostato `main.xml` la visualizzazione contenuto sul layout, il[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
il widget viene acquisito dal layout con [`FindViewById`](xref:Android.App.Activity.FindViewById*). Viene quindi [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) inizializzato un nuovo oggetto per `list_item.xml` associare il layout a ogni elemento dell' `COUNTRIES` elenco nella matrice di stringhe (definito nel passaggio successivo). Infine, `SetAdapter()` viene chiamato per associare l' [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) oggetto a[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget in modo che la matrice di stringhe compilerà l'elenco di suggerimenti.

All'interno `MainActivity` della classe aggiungere la matrice di stringhe:

```csharp
static string[] COUNTRIES = new string[] {
  "Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
  "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda", "Argentina",
  "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan",
  "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium",
  "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia",
  "Bosnia and Herzegovina", "Botswana", "Bouvet Island", "Brazil", "British Indian Ocean Territory",
  "British Virgin Islands", "Brunei", "Bulgaria", "Burkina Faso", "Burundi",
  "Cote d'Ivoire", "Cambodia", "Cameroon", "Canada", "Cape Verde",
  "Cayman Islands", "Central African Republic", "Chad", "Chile", "China",
  "Christmas Island", "Cocos (Keeling) Islands", "Colombia", "Comoros", "Congo",
  "Cook Islands", "Costa Rica", "Croatia", "Cuba", "Cyprus", "Czech Republic",
  "Democratic Republic of the Congo", "Denmark", "Djibouti", "Dominica", "Dominican Republic",
  "East Timor", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea", "Eritrea",
  "Estonia", "Ethiopia", "Faeroe Islands", "Falkland Islands", "Fiji", "Finland",
  "Former Yugoslav Republic of Macedonia", "France", "French Guiana", "French Polynesia",
  "French Southern Territories", "Gabon", "Georgia", "Germany", "Ghana", "Gibraltar",
  "Greece", "Greenland", "Grenada", "Guadeloupe", "Guam", "Guatemala", "Guinea", "Guinea-Bissau",
  "Guyana", "Haiti", "Heard Island and McDonald Islands", "Honduras", "Hong Kong", "Hungary",
  "Iceland", "India", "Indonesia", "Iran", "Iraq", "Ireland", "Israel", "Italy", "Jamaica",
  "Japan", "Jordan", "Kazakhstan", "Kenya", "Kiribati", "Kuwait", "Kyrgyzstan", "Laos",
  "Latvia", "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg",
  "Macau", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Marshall Islands",
  "Martinique", "Mauritania", "Mauritius", "Mayotte", "Mexico", "Micronesia", "Moldova",
  "Monaco", "Mongolia", "Montserrat", "Morocco", "Mozambique", "Myanmar", "Namibia",
  "Nauru", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia", "New Zealand",
  "Nicaragua", "Niger", "Nigeria", "Niue", "Norfolk Island", "North Korea", "Northern Marianas",
  "Norway", "Oman", "Pakistan", "Palau", "Panama", "Papua New Guinea", "Paraguay", "Peru",
  "Philippines", "Pitcairn Islands", "Poland", "Portugal", "Puerto Rico", "Qatar",
  "Reunion", "Romania", "Russia", "Rwanda", "Sqo Tome and Principe", "Saint Helena",
  "Saint Kitts and Nevis", "Saint Lucia", "Saint Pierre and Miquelon",
  "Saint Vincent and the Grenadines", "Samoa", "San Marino", "Saudi Arabia", "Senegal",
  "Seychelles", "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "Solomon Islands",
  "Somalia", "South Africa", "South Georgia and the South Sandwich Islands", "South Korea",
  "Spain", "Sri Lanka", "Sudan", "Suriname", "Svalbard and Jan Mayen", "Swaziland", "Sweden",
  "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "The Bahamas",
  "The Gambia", "Togo", "Tokelau", "Tonga", "Trinidad and Tobago", "Tunisia", "Turkey",
  "Turkmenistan", "Turks and Caicos Islands", "Tuvalu", "Virgin Islands", "Uganda",
  "Ukraine", "United Arab Emirates", "United Kingdom",
  "United States", "United States Minor Outlying Islands", "Uruguay", "Uzbekistan",
  "Vanuatu", "Vatican City", "Venezuela", "Vietnam", "Wallis and Futuna", "Western Sahara",
  "Yemen", "Yugoslavia", "Zambia", "Zimbabwe"
};
```

Questo è l'elenco di suggerimenti che verranno forniti in un elenco a discesa quando l'utente digita nel[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget.

Eseguire l'applicazione. Durante la digitazione, dovrebbe essere visualizzata una schermata simile alla seguente:

[![Schermata di completamento automatico di esempio che elenca i nomi che contengono "CA"](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)



## <a name="more-information"></a>Altre informazioni

Si noti che l'uso di una matrice di stringhe hardcoded non è una procedura di progettazione consigliata, perché il codice dell'applicazione deve concentrarsi sul comportamento, non sul contenuto. Il contenuto dell'applicazione, ad esempio le stringhe, deve essere esternalizzato dal codice per apportare modifiche al contenuto e semplificare la localizzazione del contenuto. Le stringhe hardcoded vengono usate in questa esercitazione solo per semplificare e concentrarsi sui[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget. L'applicazione deve invece dichiarare tali matrici di stringhe in un file XML. Questa operazione può essere eseguita con `<string-array>` una risorsa nel file `res/values/strings.xml` di progetto. Ad esempio:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="countries_array">
        <item>Bahrain</item>
        <item>Bangladesh</item>
        <item>Barbados</item>
        <item>Belarus</item>
        <item>Belgium</item>
        <item>Belize</item>
        <item>Benin</item>
    </string-array>
</resources>
```

Per usare queste stringhe di risorse per [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter), sostituire l'originale[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
linea del costruttore con quanto segue:

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```


### <a name="references"></a>Riferimenti

- [Ricetta AutoCompleteTextView](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input) &ndash; Progetto di esempio Novell. Android per`AutoCompleteTextView`
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](http://creativecommons.org/licenses/by/2.5/). Questa esercitazione è basata sull' [esercitazione di completamento automatico di Android *](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)._
