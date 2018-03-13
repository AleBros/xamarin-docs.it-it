---
title: Hello, usura
description: "Creare la prima app Android con accenti ed eseguirlo in un dispositivo o un emulatore usura. Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di un piccolo progetto Android accenti che gestisce i clic del pulsante e visualizza un contatore di fare clic sul dispositivo usura. Viene spiegato come eseguire il debug dell'app usando un emulatore usura o un dispositivo usura che è connesso tramite Bluetooth su un telefono Android. Fornisce inoltre un set di comandi di debug per Android con accenti."
ms.topic: article
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8eed2d6b825a6e6dd7e956bf901246b9a630081a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="hello-wear"></a>Hello, usura

_Creare la prima app Android con accenti ed eseguirlo in un dispositivo o un emulatore usura. Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di un piccolo progetto Android accenti che gestisce i clic del pulsante e visualizza un contatore di fare clic sul dispositivo usura. Viene spiegato come eseguire il debug dell'app usando un emulatore usura o un dispositivo usura che è connesso tramite Bluetooth su un telefono Android. Fornisce inoltre un set di comandi di debug per Android con accenti._

![Schermata dell'app usura per completare questa esercitazione](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>La prima app usura

Seguire questi passaggi per creare la prima app xamarin usura:

### <a name="1-create-a-new-android-project"></a>1. Creare un nuovo progetto Android

Creare un nuovo **applicazione Android accenti**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Creazione di una nuova applicazione Android di usare nella finestra di dialogo Nuovo progetto](hello-wear-images/vs/new-solution-sml.png)](hello-wear-images/vs/new-solution.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Creazione di una nuova applicazione Android di usare nella finestra di dialogo nuova soluzione](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Questo modello include automaticamente il **libreria indossabile Android di Xamarin** NuGet (e le dipendenze) in modo sarà possibile accedere a widget usura specifiche. Se non viene visualizzato il modello usura, esaminare il [installazione e configurazione](~/android/wear/get-started/installation.md) Guida verificare di aver installato un SDK Android supportato. 

### <a name="2-choose-the-correct-target-framework"></a>2. Scegliere il corretto **Framework di destinazione**

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Verificare che **minimo Android di destinazione** è impostato su **Android 5.0 (simbolo)** o versione successiva: 

[![L'impostazione del Framework di destinazione su Android 5.0 in Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Verificare che il framework di destinazione è impostato su **Android 5.0 (simbolo)** o versione successiva:

[![Impostare il Framework di destinazione Android 5.0 in Visual Studio per Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Per ulteriori informazioni sull'impostazione del framework di destinazione, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Modificare il **axml** layout

Configurare il layout per contenere un `TextView` e `Button` per il codice di esempio: 

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

### <a name="4-edit-the-mainactivitycs-source"></a>4. Modificare il **Mainactivity** origine

Aggiungere il codice per incrementare un contatore e la visualizza ogni volta che si fa clic sul pulsante: 

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

### <a name="5-setup-an-emulator-or-device"></a>5. Programma di installazione di un emulatore o dispositivo

Il passaggio successivo viene configurato un emulatore o dispositivo per distribuire ed eseguire l'app. Se non sono ancora familiarità con il processo di distribuzione ed esecuzione di App xamarin in generale, vedere il [Hello, Android Quickstart](~/android/get-started/hello-android/hello-android-quickstart.md).

Se non si dispone di un dispositivo Android, usare, ad esempio un Smartwatch accenti Android, è possibile eseguire l'app in un emulatore. Per informazioni sul debug usura App in un emulatore, vedere [Debug Android accenti in un emulatore](~/android/wear/deploy-test/debug-on-emulator.md).

Se si dispone di un dispositivo Android, usare, ad esempio un Smartwatch accenti Android, è possibile eseguire l'app sul dispositivo anziché utilizzare un emulatore. Per ulteriori informazioni sul debug in un dispositivo usura, vedere [eseguire il Debug su un dispositivo con accenti](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Eseguire l'app Android usura

Il dispositivo Android con accenti compariranno nel menu a discesa dispositivo. Assicurarsi di scegliere il dispositivo Android con accenti corretto o AVD prima di iniziare il debug. Dopo aver selezionato il dispositivo, fare clic sul pulsante Riproduci per distribuire l'app sull'emulatore o dispositivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Scelta di un AVD accenti nel menu dispositivo di Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Scelta di un AVD accenti in Visual Studio per menu dispositivo Mac](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

È possibile visualizzare un **pochi minuti...**  messaggio (o alcune altre schermate intermedi) inizialmente: 

![Guardare l'emulatore consente di visualizzare solo un minuto...](hello-wear-images/please-wait.png)

Se si utilizza un emulatore di espressioni di controllo, può richiedere qualche minuto per avviare l'app. Quando si utilizza Bluetooth, richiede più tempo per distribuire l'app rispetto a quanto avviene tramite USB. (Ad esempio, accetta circa 5 minuti per distribuire l'app a cui è connesso a Bluetooth su un telefono 5 Nexus Watch G LG).

Dopo l'app viene distribuito correttamente, la schermata del dispositivo usura deve visualizzare una schermata simile alla seguente:

[![Schermata iniziale dell'app usura](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Toccare il **CLICK ME!** pulsante sul dispositivo usura e vedere l'incremento di conteggio con ogni scelta:

[![Schermata di usare app dopo 3 fa clic su](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

Estrarre il [accenti esempi](https://developer.xamarin.com/samples/android/Android%20Wear/) incluse, usare Android le app con App Phone complementare.

Quando si è pronti a distribuire l'app, vedere [utilizzo di creazione del pacchetto](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Collegamenti correlati

- [Fare clic su Me App (esempio)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
