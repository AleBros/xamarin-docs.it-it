---
title: "Procedura dettagliata: creazione di un'interfaccia a schede con TabHost"
description: In questo articolo verrà illustrata la creazione di un'interfaccia a schede in tramite l'API TabHost xamarin.
ms.prod: xamarin
ms.assetid: AD6E2173-974E-477C-940F-0CAB5E53326D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: ca9a3f3d31707205cdcd4e0d8e74fa303ccba047
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---creating-a-tabbed-ui-with-tabhost"></a>Procedura dettagliata: creazione di un'interfaccia a schede con TabHost

_In questo articolo verrà illustrata la creazione di un'interfaccia a schede in tramite l'API TabHost xamarin._

> [!NOTE]
> `TabHost` è un'API precedente che è stata deprecata da Google. Gli sviluppatori sono invitati a compilare le applicazioni a schede utilizzando il [ActionBar](~/android/user-interface/controls/action-bar.md). Il `ActionBar` è disponibile in tutte le versioni di Android. È stata introdotta in Android 3.0 (livello API 11) e nuovamente è stato trasferito per Android 2.2 (livello API 8) e Android 2.3 (livello API 10) nei [V7 delle applicazioni libreria](http://developer.android.com/tools/support-library/features.html#v7-appcompat), che è disponibile per xamarin tramite il [Xamarin Libreria di supporto Android - V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto.

In questo articolo verrà illustrata la creazione di un'interfaccia a schede in xamarin utilizzando il `TabHost` API. Si tratta di un'API precedente è disponibile in tutte le versioni di Android. In questo esempio verrà creata un'applicazione con tre schede, con la logica per ogni scheda incapsulata in un'attività.
Nella schermata riportata di seguito è riportato un esempio dell'applicazione che verrà creato:

![Schermata di esempio dell'app con più schede](creating-a-tabbed-ui-images/image02.png)


## <a name="creating-the-application"></a>Creare l'applicazione

Scaricare e decomprimere [TabHostWalkthrough](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/).
Il progetto viene utilizzato come punto di partenza per l'applicazione e contiene alcune immagini. Se si esamina il progetto, si noterà che le risorse per le icone scheda drawable abbiamo già creato.

Primo consente di aggiornare il file di layout **Resources/Layout/Main.axml** che ospiterà le schede. Modifica **Resources/Layout/Main.axml** e inserire il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
         android:id="@android:id/tabhost"
         android:layout_width="fill_parent"
         android:layout_height="fill_parent">
    <LinearLayout
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:padding="5dp">
        <TabWidget
                android:id="@android:id/tabs"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" />
        <FrameLayout
                android:id="@android:id/tabcontent"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp" />
    </LinearLayout>
</TabHost>
```

Nella schermata seguente viene mostrato il layout nella finestra di progettazione di Xamarin:

[![Schermata del layout TabHost nella finestra di progettazione di Xamarin](creating-a-tabbed-ui-images/image04-sml.png)](creating-a-tabbed-ui-images/image04.png#lightbox)

Il TabHost deve avere due visualizzazioni figlio all'interno: un `TabWidget` e `FrameLayout`. Posizione il `TabWidget` e `FrameLayout` verticalmente all'interno di `TabHost`, un `LinearLayout` viene utilizzato. Il FrameLayout è in cui il contenuto per ogni scheda viene, che è vuota perché il `TabHost` incorporeranno automaticamente ogni attività in fase di esecuzione. Sono disponibili diverse regole che devono essere osservate quando si tratta di creazione del layout per le interfacce utente a schede:

-   Il `TabHost` deve avere l'id `@android:id/tabhost`.

-   Il `TabWidget` deve avere l'id `@android:id/tabs`.

-   Il `FrameLayout` deve avere l'id `@android:id/tabcontent`.

-   `TabHost` richiede di qualsiasi attività che gestisce da cui ereditare `TabActivity`. Pertanto, è importante sottoclasse `TabActivity` qui &ndash; una regolare attività non funzionerà.

Modificare il file **Mainactivity** in modo che la classe `MainActivity` sottoclassi `TabActivity` come illustrato nel frammento di codice seguente:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/ic_launcher")]
public class MainActivity : TabActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
    }
}
```

Creare quattro classi di attività separate nel progetto: `MyScheduleActivity`, `SessionsActivity`, `SpeakersActivity`, e `WhatsOnActivity`. Ogni attività costituiranno l'interfaccia utente di una scheda. Per ora queste attività sarà uno stub che consente di visualizzare un `TextView` con un semplice messaggio. Modificare il codice in ogni attività per contenere i seguenti elementi `OnCreate` implementazione:

```csharp
[Activity]
public class MyScheduleActivity : Activity
{
    protected override void OnCreate (Bundle savedInstanceState)
    {
        base.OnCreate (savedInstanceState);
        TextView textview = new TextView (this);
        textview.Text = "This is the My Schedule tab";
        SetContentView (textview);
    }
}
```

Si noti che il codice sopra riportato non utilizza un file di layout. Crea solo un `TextView` con testo e lo imposta `TextView` come la visualizzazione contenuto. Duplicare questo per ognuna delle tre attività rimanenti.

Si assegnerà quindi le icone per ogni scheda. Ogni scheda richiede due icone &ndash; uno per lo stato selezionato e uno per lo stato non selezionato. Un esempio di queste due icone diverse può essere visti nelle due immagini riportate (le icone necessarie per questa applicazione sono già stati aggiunti al progetto di esempio):

![Schermata di stato selezionato e le icone di stato non selezionato](creating-a-tabbed-ui-images/tab-icons.png)


Si assegnerà le risorse drawable alle schede icona definendo un *Drawablestato-List*. Elenco stato drawables sono risorse drawable speciali sono definiti in XML e consentono di specificare diverse immagini che sono specifiche di è stato tale elemento. In questo esempio è un'immagine che viene utilizzata quando viene selezionata una scheda e un altro che viene utilizzato quando non è selezionata la scheda. Per risparmiare tempo, drawables l'elenco stato necessari sono stati aggiunti al progetto. Nell'elenco seguente vengono mostrati i file e il XML contiene:

-   `ic_tab_my_schedule.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

-   `ic_tab_sessions.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_sessions_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_sessions_unselected"/>
    </selector>
    ```

-   `ic_tab_speakers.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_speakers_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_speakers_unselected"/>
    </selector>
    ```

-   `ic_tab_whats_on.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

Vengono aggiunte le schede del `TabHost` a livello di codice, ovvero un'attività molto ripetitiva. A tale scopo, aggiungere il metodo seguente alla classe `MainActivity`:

```csharp
private void CreateTab(Type activityType, string tag, string label, int drawableId )
{
    var intent = new Intent(this, activityType);
    intent.AddFlags(ActivityFlags.NewTask);

    var spec = TabHost.NewTabSpec(tag);
    var drawableIcon = Resources.GetDrawable(drawableId);
    spec.SetIndicator(label, drawableIcon);
    spec.SetContent(intent);

    TabHost.AddTab(spec);
}
```

In ogni scheda di `TabHost` è rappresentato da un'istanza del del `TabHost.TabSpec` classe. Questa istanza conterrà i metadati necessari per il rendering di scheda, in particolare:

-   **Testo e l'icona** &ndash; da visualizzare nel `TabWidget`.

-   **Scheda contenuto** &ndash; potrebbe trattarsi un `Activity` o `View` e viene visualizzato quando si seleziona la scheda.

-   **Tag univoco** &ndash; ogni scheda deve avere un tag univoco assegnato a esso.

È necessario aggiungere un `TabHost.TabSpec` istanza per ogni scheda nell'applicazione. Consente di eseguire questa operazione nel passaggio successivo.

Aggiornare il metodo `OnCreate` in `MainActivity` in modo che risulti simile nel codice seguente:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateTab(typeof(WhatsOnActivity), "whats_on", "What's On", Resource.Drawable.ic_tab_whats_on);
    CreateTab(typeof(SpeakersActivity), "speakers", "Speakers", Resource.Drawable.ic_tab_speakers);
    CreateTab(typeof(SessionsActivity), "sessions", "Sessions", Resource.Drawable.ic_tab_sessions);
    CreateTab(typeof(MyScheduleActivity), "my_schedule", "My Schedule", Resource.Drawable.ic_tab_my_schedule);
}
```

Eseguire l'applicazione. L'applicazione dovrebbe essere simile al seguente nella schermata riportata all'inizio di questa procedura dettagliata.

La procedura è terminata. È stata creata un'applicazione a schede che consente all'utente di passare in modo semplice a diverse parti di un'applicazione.



## <a name="summary"></a>Riepilogo

In questo capitolo discussi layout a schede e interattiva di processo di creazione di un'applicazione a schede. La procedura dettagliata viene illustrato come utilizzare un `TabActivity` ingrandimento un layout di file che ospita un `TabHost` e `TabWidget`. Il `TabHost` è stata popolata successivamente con un insieme di `TabHost.TabSpec` gli oggetti che verrebbero usati dal `TabHost` in fase di esecuzione per creare un'istanza di attività utilizzato in ogni scheda.



## <a name="related-links"></a>Collegamenti correlati

- [TabHostWalkthrough (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacchetto NuGet di supporto Android libreria v7 delle applicazioni](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria delle applicazioni V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
