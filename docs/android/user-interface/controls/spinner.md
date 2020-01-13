---
title: Casella di selezione Xamarin.Android
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: ba4a83eb997b879e8a2398f9857e2fd80221f8ef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029156"
---
# <a name="xamarinandroid-spinner"></a>Casella di selezione Xamarin.Android

[`Spinner`](xref:Android.Widget.Spinner) è un widget che presenta un elenco a discesa per la selezione di elementi. Questa guida illustra come creare una semplice app che visualizza un elenco di opzioni in una casella di selezione, seguito da modifiche che visualizzano altri valori associati alla scelta selezionata.

## <a name="basic-spinner"></a>Casella di selezione di base

Nella prima parte di questa esercitazione verrà creato un semplice widget Spinner che visualizza un elenco di pianeti. Quando viene selezionato un pianeta, un messaggio di avviso popup Visualizza l'elemento selezionato:

[schermate di esempio ![dell'app HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Avviare un nuovo progetto denominato **HelloSpinner**.

Aprire **risorse/layout/Main. aXML** e inserire il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

Si noti che l'attributo `android:text` del [`TextView`](xref:Android.Widget.TextView)e l'attributo `android:prompt` del [`Spinner`](xref:Android.Widget.Spinner)fanno riferimento alla stessa risorsa di stringa. Questo testo si comporta come un titolo per il widget. Quando viene applicato al [`Spinner`](xref:Android.Widget.Spinner), il testo del titolo verrà visualizzato nella finestra di dialogo di selezione visualizzata quando si seleziona il widget.

Modificare **Resources/values/Strings. XML** e modificare il file in modo che abbia un aspetto simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

Il secondo elemento `<string>` definisce la stringa del titolo a cui fa riferimento il [`TextView`](xref:Android.Widget.TextView) e [`Spinner`](xref:Android.Widget.Spinner) nel layout precedente.
L'elemento `<string-array>` definisce l'elenco di stringhe che verranno visualizzate come elenco nel widget [`Spinner`](xref:Android.Widget.Spinner) .

A questo punto, aprire **MainActivity.cs** e aggiungere l'istruzione `using` seguente:

```csharp
using System;
```

Inserire quindi il codice seguente per il metodo [`OnCreate()`](xref:Android.App.Activity.OnCreate*)):

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

Una volta impostato il layout `Main.axml` come visualizzazione contenuto, il widget [`Spinner`](xref:Android.Widget.Spinner) viene acquisito dal layout con [`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*).
[`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
viene quindi creato un nuovo [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter), che associa ogni elemento nella matrice di stringhe all'aspetto iniziale per l' [`Spinner`](xref:Android.Widget.Spinner) (ovvero la modalità di visualizzazione di ogni elemento nella casella di selezione se selezionata). L'ID `Resource.Array.planets_array` fa riferimento al `string-array` definito in precedenza e l'ID `Android.Resource.Layout.SimpleSpinnerItem` fa riferimento a un layout per l'aspetto della casella di selezione standard, definito dalla piattaforma.
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
viene chiamato per definire l'aspetto per ogni elemento quando viene aperto il widget. Infine, il [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) viene impostato per associare tutti gli elementi al [`Spinner`](xref:Android.Widget.Spinner) impostando la proprietà [`Adapter`](xref:Android.Widget.ArrayAdapter) .

Fornire ora un metodo di callback che invia una notifica all'applicazione quando è stato selezionato un elemento dalla [`Spinner`](xref:Android.Widget.Spinner). Questo metodo dovrebbe avere un aspetto simile al seguente:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Quando si seleziona un elemento, viene eseguito il cast del mittente a un [`Spinner`](xref:Android.Widget.Spinner) in modo che sia possibile accedere agli elementi. Utilizzando la proprietà `Position` sul `ItemEventArgs`, è possibile individuare il testo dell'oggetto selezionato e utilizzarlo per visualizzare un [`Toast`](xref:Android.Widget.Toast).

Eseguire l'applicazione; dovrebbe essere simile al seguente:

[![screenshot esempio di Spinner con Mars selezionato come pianeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Casella di selezione con coppie chiave/valore

Spesso è necessario usare `Spinner` per visualizzare i valori di chiave associati a un tipo di dati usato dall'app. Poiché `Spinner` non funziona direttamente con le coppie chiave/valore, è necessario archiviare separatamente la coppia chiave/valore, popolare il `Spinner` con i valori di chiave, quindi utilizzare la posizione della chiave selezionata nella casella di selezione per cercare il valore dei dati associati. 

Nei passaggi seguenti l'app **HelloSpinner** viene modificata per visualizzare la temperatura media per il pianeta selezionato:

Aggiungere la seguente istruzione `using` a **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Aggiungere la variabile di istanza seguente alla classe `MainActivity`.
Questo elenco conterrà le coppie chiave/valore per i pianeti e le relative temperature medie:

```csharp
private List<KeyValuePair<string, string>> planets;
```

Nel metodo `OnCreate` aggiungere il codice seguente prima di dichiarare `adapter`:

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

Questo codice consente di creare un archivio semplice per i pianeti e le temperature medie associate. In un'app reale, un database viene in genere usato per archiviare le chiavi e i dati associati.

Subito dopo il codice precedente, aggiungere le righe seguenti per estrarre le chiavi e inserirle in un elenco (in ordine):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Passare questo elenco al costruttore `ArrayAdapter` (anziché alla risorsa `planets_array`):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modificare `spinner_ItemSelected` in modo che la posizione selezionata venga usata per cercare il valore (la temperatura) associato al pianeta selezionato:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Eseguire l'applicazione; il toast dovrebbe essere simile al seguente:

[![esempio di selezione della temperatura del pianeta](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>Risorse

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base ai termini descritti nella* licenza
[*Creative Commons 2,5 Attribution*](https://creativecommons.org/licenses/by/2.5/).
