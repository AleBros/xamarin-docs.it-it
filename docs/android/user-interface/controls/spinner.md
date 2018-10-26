---
title: Spinner
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 90b4755cdb4b8248c2b731d070d720076d4dda40
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102997"
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) è un widget che visualizza un elenco di riepilogo a discesa per selezionare gli elementi. Questa guida illustra come creare una semplice app che visualizza un elenco di scelte in una casella di selezione, seguita dalle modifiche che consentono di visualizzare gli altri valori associati con l'opzione selezionata.

## <a name="basic-spinner"></a>Selezione di base

Nella prima parte di questa esercitazione, si creerà un widget di casella di selezione semplice che visualizza un elenco di pianeti. Quando viene selezionato un pianeta, l'elemento selezionato viene visualizzato un messaggio di tipo avviso popup:

[![Screenshot di esempio di app HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Avviare un nuovo progetto denominato **HelloSpinner**.

Aprire **Resources/Layout/Main.axml** e inserire il codice XML seguente:

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

Si noti che il [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)del `android:text` attributo e il [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)del `android:prompt` attributo sia un riferimento alla stessa risorsa di stringa. Questo testo si comporta come un titolo per il widget. Quando applicato al [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/), verrà visualizzato il testo del titolo nella finestra di dialogo di selezione che viene visualizzato dopo aver selezionato il widget.

Modificare **Resources/Values/Strings.xml** e modificare il file deve essere come segue:

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

La seconda `<string>` elemento definisce la stringa del titolo fa riferimento il [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) e [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) nel layout precedente.
Il `<string-array>` elemento definisce l'elenco di stringhe che verrà visualizzato come elenco di [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget.

A questo punto aprire **MainActivity.cs** e aggiungere quanto segue `using` istruzione:

```csharp
using System;
```

Successivamente, inserire il codice seguente per il [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
metodo:

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

Dopo il `Main.axml` layout viene impostato come la visualizzazione contenuto, il [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget viene acquisito dal layout con [ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
Il [`CreateFromResource()`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/)
Crea quindi una nuova [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), che associa ogni elemento nella matrice di stringhe all'aspetto iniziale per il [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) (ovvero modo in cui ogni elemento verrà visualizzato nella casella di selezione, quando selezionato) . Il `Resource.Array.planets_array` riferimenti a ID il `string-array` definita in precedenza e `Android.Resource.Layout.SimpleSpinnerItem` ID fa riferimento a un layout per l'aspetto della casella di selezione standard, definito dalla piattaforma.
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/)
viene chiamato per definire l'aspetto di ogni elemento quando viene aperto il widget. Infine, il [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) è impostata per associare tutti i relativi elementi con il [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) impostando il [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter) proprietà.

Forniscono ora un metodo di callback che notifys l'applicazione quando è stato selezionato un elemento dal [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/). Ecco come dovrebbe risultare questo metodo:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Quando viene selezionato un elemento, il mittente viene eseguito il cast a un [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) in modo che gli elementi accessibili. Usando il `Position` proprietà di `ItemEventArgs`, è possibile trovare il testo dell'oggetto selezionato e utilizzarlo per visualizzare un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/).

Eseguire l'applicazione. dovrebbe essere simile al seguente:

[![Screenshot di esempio di casella di selezione con Mars selezionato come con la copertura globale](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Casella di selezione con coppie chiave/valore

Spesso è necessario usare `Spinner` per visualizzare valori di chiave che sono associati a un tipo di dati usati dall'app. In quanto `Spinner` non funziona direttamente con coppie chiave/valore, è necessario archiviare separatamente la coppia chiave/valore, popolare la `Spinner` con valori di chiave, quindi usare la posizione della chiave selezionata nella casella di selezione per cercare il valore di dati associato. 

Nei passaggi seguenti, il **HelloSpinner** app viene modificato per visualizzare la temperatura media per il pianeta selezionato:

Aggiungere il codice seguente `using` dell'istruzione **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Aggiungere la seguente variabile di istanza per il `MainActivity` classe.
Questo elenco conterrà coppie chiave/valore per il pianeti e le temperature mean:

```csharp
private List<KeyValuePair<string, string>> planets;
```

Nel `OnCreate` metodo, aggiungere il codice seguente prima `adapter` viene dichiarato:

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

Questo codice crea un semplice negozio per i pianeti e le temperature mean associate. (In un'app reale, un database è in genere usato per archiviare le chiavi e i relativi dati associati.)

Immediatamente dopo il codice sopra riportato, aggiungere le righe seguenti per estrarre le chiavi e inserirle in un elenco (in ordine):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Passare questo elenco per il `ArrayAdapter` costruttore (anziché il `planets_array` risorse):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modificare `spinner_ItemSelected` in modo che la posizione selezionata viene utilizzata per cercare il valore (la temperatura) associato al mondo selezionato:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Eseguire l'applicazione. il tipo avviso popup dovrebbe essere simile al seguente:

[![Esempio di selezione con copertura globale la visualizzazione della temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>Risorse

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
