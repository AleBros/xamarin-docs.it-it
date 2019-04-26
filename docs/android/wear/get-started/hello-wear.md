---
title: Prima app per Wear
description: Creare la prima app Android Wear ed eseguirlo in un dispositivo o emulatore Wear. Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di un progetto Android Wear piccolo che gestisce i clic sui pulsanti e visualizza un contatore di fare clic sul dispositivo Wear. Viene spiegato come eseguire il debug dell'app usando un emulatore Wear o un dispositivo Wear connessa tramite Bluetooth per un telefono Android. Consente inoltre una serie di suggerimenti per il debug di Android Wear.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: a8e27063040ff91f72a1cbf932b1b277a5dee63d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277394"
---
# <a name="hello-wear"></a>Prima app per Wear

_Creare la prima app Android Wear ed eseguirlo in un dispositivo o emulatore Wear. Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di un progetto Android Wear piccolo che gestisce i clic sui pulsanti e visualizza un contatore di fare clic sul dispositivo Wear. Viene spiegato come eseguire il debug dell'app usando un emulatore Wear o un dispositivo Wear connessa tramite Bluetooth per un telefono Android. Consente inoltre una serie di suggerimenti per il debug di Android Wear._

![Screenshot dell'app Wear per essere completati in questa esercitazione](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>La prima app Wear

Seguire questi passaggi per creare la prima app xamarin. Android Wear:

### <a name="1-create-a-new-android-project"></a>1. Creare un nuovo progetto Android

Creare una nuova **applicazione Android Wear**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Creazione di una nuova applicazione Android Wear nella finestra di dialogo Nuovo progetto](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Creazione di una nuova applicazione Android Wear nella finestra di dialogo nuova soluzione](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Questo modello include automaticamente il **libreria indossabile di Xamarin Android** NuGet (e le dipendenze) ed è pertanto necessario l'accesso ai widget Wear specifici. Se non viene visualizzato il modello Wear, consultare il [installazione e configurazione](~/android/wear/get-started/installation.md) Guida verificare di avere installato una versione di Android SDK supportato. 

### <a name="2-choose-the-correct-target-framework"></a>2. Scegliere i valori corretti **Framework di destinazione**

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Assicurarsi che **minima di Android di destinazione** è impostata su **Android 5.0 (Lollipop)** o versione successiva: 

[![Impostazione del Framework di destinazione in Android 5.0 in Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Verificare che il framework di destinazione è impostato su **Android 5.0 (Lollipop)** o versione successiva:

[![Impostazione del Framework di destinazione di Android 5.0 in Visual Studio per Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Per altre informazioni sull'impostazione del framework di destinazione, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Modificare il **Main. axml** layout

Configurare il layout per contenere una `TextView` e un `Button` dell'esempio: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. Modificare il **MainActivity.cs** origine

Aggiungere il codice per incrementare un contatore e lo visualizza ogni volta che viene scelto il pulsante: 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. Configurare un emulatore o dispositivo

Il passaggio successivo è configurato un emulatore o il dispositivo da distribuire ed eseguire l'app. Se non si ha ancora familiarità con il processo di distribuzione ed esecuzione delle App xamarin. Android in generale, vedere la [Hello, Android: Guida introduttiva](~/android/get-started/hello-android/hello-android-quickstart.md).

Se non hai un dispositivo Android Wear, ad esempio un Smartwatch Android Wear, è possibile eseguire l'app in un emulatore. Per informazioni sul debug delle App Wear in un emulatore, vedere [il Debug di Android Wear in un emulatore](~/android/wear/deploy-test/debug-on-emulator.md).

Se si dispone di un dispositivo Android Wear, ad esempio un Smartwatch Android Wear, è possibile eseguire l'app sul dispositivo invece di usare un emulatore. Per altre informazioni sul debug in un dispositivo Wear, vedere [eseguire il Debug in un dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Eseguire l'app Android Wear

Il dispositivo Android Wear dovrebbe essere nel menu a discesa dispositivo. Assicurarsi di scegliere il dispositivo Android Wear corretto oppure AVD prima di avviare il debug. Dopo aver selezionato il dispositivo, fare clic sul pulsante Riproduci per distribuire l'app all'emulatore o dispositivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Scelta di un AVD Wear nel menu di Visual Studio dispositivo](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Scelta di un AVD Wear in Visual Studio per il menu di dispositivi Mac](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Potrebbe essere visualizzato un **pochi minuti...**  messaggio (o alcuni altri schermo intero intermedio) all'inizio: 

![Guarda l'emulatore Visualizza solo un minuto...](hello-wear-images/please-wait.png)

Se si usa un emulatore di espressioni di controllo, può richiedere un po' di tempo per l'avvio dell'app. Quando si usa Bluetooth, richiede più tempo per distribuire l'app rispetto a quanto avviene tramite USB. (Ad esempio, sono necessari circa 5 minuti per distribuire l'app a Watch G LG che è connesso a Bluetooth su un telefono Nexus 5.)

Dopo che l'app distribuisce correttamente, la schermata del dispositivo Wear dovrebbe visualizzare una schermata simile alla seguente:

[![Schermata iniziale dell'app per Wear](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Toccare il **CLICK ME!** pulsante sul dispositivo Wear e vedere l'incremento di conteggio con ogni tocco:

[![Schermata di Wear app dopo il 3 clic](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

Consultare il [Wear esempi](https://developer.xamarin.com/samples/android/Android%20Wear/) , incluse le app Android Wear con le app Phone complementare.

Quando si è pronti per distribuire l'app, vedere [utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Collegamenti correlati

- [Fare clic su Me App (esempio)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
