---
title: Tema Material
description: Come eseguire il tema dell'app Xamarin.Android con il tema materiale
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 5e83e34dab407c5be84fc5b4c3c0c445d56907e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028753"
---
# <a name="material-theme"></a>Tema Material

Il *tema Material* è uno stile dell'interfaccia utente che determina l'aspetto delle visualizzazioni e delle attività a partire da Android 5,0 (Lollipop). Il tema materiale è integrato in Android 5,0, quindi viene usato dall'interfaccia utente del sistema e dalle applicazioni. Il tema del materiale non è un "tema" nel senso di un'opzione di aspetto a livello di sistema che un utente può scegliere dinamicamente da un menu impostazioni. Il tema del materiale può invece essere considerato come un set di stili di base incorporati correlati che è possibile usare per personalizzare l'aspetto dell'app.

Android offre tre tipi di tema di materiale:

- `Theme.Material` &ndash; versione scura del tema materiale; Questa è la versione predefinita in Android 5,0.

- `Theme.Material.Light` &ndash; versione Light del tema del materiale.

- `Theme.Material.Light.DarkActionBar` &ndash; versione Light del tema Material, ma con una barra di azione scura.

Di seguito sono riportati alcuni esempi di queste varianti dei temi materiali:

[![screenshot di esempio del tema scuro, tema chiaro e tema scuro Barra delle azioni](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

È possibile derivare da tema materiale per creare il proprio tema, eseguendo l'override di alcuni o di tutti gli attributi di colore. Ad esempio, è possibile creare un tema che deriva da `Theme.Material.Light`, ma esegue l'override del colore della barra dell'app in modo che corrisponda al colore del marchio. È anche possibile applicare stili a visualizzazioni singole; ad esempio, è possibile creare uno stile per [CardView](~/android/user-interface/controls/card-view.md) con angoli più arrotondati e che utilizza un colore di sfondo più scuro.

È possibile usare un singolo tema per un'intera app oppure è possibile usare temi diversi per schermate diverse (attività) in un'app. Nelle schermate precedenti, ad esempio, una singola app usa un tema diverso per ogni attività per dimostrare le combinazioni di colori predefinite. I pulsanti di opzione passano l'app a diverse attività e, di conseguenza, visualizzano temi diversi.

Poiché tema materiale è supportato solo in Android 5,0 e versioni successive, non è possibile usarlo (o un tema personalizzato derivato da tema materiale) per eseguire il tema dell'app per l'esecuzione in versioni precedenti di Android. Tuttavia, è possibile configurare l'app per l'uso del tema materiale nei dispositivi Android 5,0 e per eseguire normalmente il fallback su un tema precedente quando viene eseguito in versioni precedenti di Android. per informazioni dettagliate, vedere la sezione [compatibilità](#compatibility) di questo articolo.

## <a name="requirements"></a>Requisiti

Gli elementi seguenti sono necessari per usare le nuove funzionalità del tema materiale Android 5,0 nelle app basate su Xamarin:

- **Xamarin.android** &ndash; Xamarin.Android 4,20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac. 

- **Android SDK** &ndash; Android 5,0 (API 21) o versione successiva deve essere installato tramite il Android SDK Manager.

- **Java jdk 1,8** &ndash; JDK 1,7 può essere usato se si destinazione in modo specifico il livello API 23 e versioni precedenti. JDK 1,8 è disponibile da [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Per informazioni su come configurare un progetto di app Android 5,0, vedere [configurazione di un progetto android 5,0](~/android/platform/lollipop.md).

## <a name="using-the-built-in-themes"></a>Uso dei temi predefiniti

Il modo più semplice per usare il tema Material consiste nel configurare l'app per l'uso di un tema incorporato senza personalizzazione. Se non si vuole configurare in modo esplicito un tema, l'App utilizzerà per impostazione predefinita il `Theme.Material` (tema scuro). Se l'app ha una sola attività, è possibile configurare un tema a livello di attività. Se l'app ha più attività, è possibile configurare un tema a livello di applicazione in modo che usi lo stesso tema in tutte le attività oppure è possibile assegnare temi diversi a diverse attività. Le sezioni seguenti illustrano come configurare i temi a livello di applicazione e a livello di attività.

### <a name="theming-an-application"></a>Temi di un'applicazione

Per configurare un'intera applicazione per l'utilizzo di una versione del tema Material, impostare l'attributo `android:theme` del nodo applicazione in **file AndroidManifest. XML** su uno dei seguenti elementi:

- `@android:style/Theme.Material` &ndash; tema scuro.

- `@android:style/Theme.Material.Light` &ndash; tema chiaro.

- `@android:style/Theme.Material.Light.DarkActionBar` &ndash; tema chiaro con la barra di azione scura.

Nell'esempio seguente l'applicazione *MyApp* viene configurata per l'utilizzo del tema chiaro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

In alternativa, è possibile impostare l'attributo `Theme` dell'applicazione in **AssemblyInfo.cs** (o **Properties.cs**). Esempio:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Quando il tema dell'applicazione è impostato su `@android:style/Theme.Material.Light`, ogni attività in *MyApp* verrà visualizzata con `Theme.Material.Light`.

### <a name="theming-an-activity"></a>Temi di un'attività

Per sottoporre a tema un'attività, è necessario aggiungere un'impostazione di `Theme` all'attributo `[Activity]` sopra la dichiarazione di attività e assegnare `Theme` alla versione del tema Material che si desidera utilizzare. Nell'esempio seguente viene tema un'attività con `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Altre attività in questa app useranno la combinazione di colori predefinita `Theme.Material` scuro (o, se configurata, l'impostazione del tema dell'applicazione).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Uso di temi personalizzati

È possibile migliorare il marchio creando un tema personalizzato che stili l'app con i colori del marchio&rsquo;. Per creare un tema personalizzato, è necessario definire un nuovo stile che deriva da una versione predefinita del tema Material, sostituendo gli attributi di colore che si desidera modificare. Ad esempio, è possibile definire un tema personalizzato che deriva da `Theme.Material.Light.DarkActionBar` e imposta il colore di sfondo della schermata su beige invece che su bianco.

Il tema Material espone gli attributi di layout seguenti per la personalizzazione:

- `colorPrimary` &ndash; colore della barra dell'app.

- `colorPrimaryDark` &ndash; il colore della barra di stato e delle barre dell'app contestuale; si tratta in genere di una versione scura di `colorPrimary`.

- `colorAccent` &ndash; il colore dei controlli dell'interfaccia utente, ad esempio caselle di controllo, pulsanti di opzione e caselle di testo di modifica.

- `windowBackground` &ndash; il colore dello sfondo dello schermo.

- `textColorPrimary` &ndash; il colore del testo dell'interfaccia utente nella barra dell'app.

- `statusBarColor` &ndash; colore della barra di stato.

- `navigationBarColor` &ndash; colore della barra di spostamento.

Queste aree dello schermo sono etichettate nel diagramma seguente:

[![diagramma degli attributi e delle aree dello schermo associate](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Per impostazione predefinita, `statusBarColor` è impostato sul valore di `colorPrimaryDark`. È possibile impostare `statusBarColor` su un colore a tinta unita oppure è possibile impostarlo su `@android:color/transparent` per rendere trasparente la barra di stato. È inoltre possibile rendere trasparente la barra di spostamento impostando `navigationBarColor` su `@android:color/transparent`.

### <a name="creating-a-custom-app-theme"></a>Creazione di un tema personalizzato per l'app

È possibile creare un tema personalizzato dell'app creando e modificando i file nella cartella **risorse** del progetto dell'app. Per applicare uno stile all'app con un tema personalizzato, seguire questa procedura:

- Creare un file **Colors. XML** in **Resources/values** &mdash; usare questo file per definire i colori personalizzati del tema. Ad esempio, è possibile incollare il codice seguente in **Colors. XML** per iniziare:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

- Modificare questo file di esempio per definire i nomi e i codici colore per le risorse di colore che verrà usato nel tema personalizzato.

- Creare una cartella **Resources/values-V21** . In questa cartella creare un file **Styles. XML** :

    [![percorso di Styles. XML nella cartella Resources/values-21. XML](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Si noti che **Resources/values-V21** è specifico di android 5,0 &ndash; versioni precedenti di Android non leggerà i file in questa cartella.

- Aggiungere un nodo `resources` a **Styles. XML** e definire un nodo `style` con il nome del tema personalizzato. Ad esempio, di seguito è riportato un file **Styles. XML** che definisce *MyCustomTheme* (derivato dallo stile del tema `Theme.Material.Light` incorporato):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

- A questo punto, un'app che usa *MyCustomTheme* visualizzerà il tema Stock `Theme.Material.Light` senza personalizzazioni:

    [![aspetto del tema personalizzato prima delle personalizzazioni](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

- Aggiungere le personalizzazioni dei colori a **Styles. XML** definendo i colori degli attributi di layout che si desidera modificare. Ad esempio, per modificare il colore della barra dell'app in modo che `my_blue` e modificare il colore dei controlli dell'interfaccia utente in `my_purple`, aggiungere gli override dei colori a **Styles. XML** che fanno riferimento a risorse colore configurate in **Colors. XML**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Dopo aver apportato queste modifiche, un'app che usa *MyCustomTheme* visualizzerà il colore della barra dell'app in `my_blue` e i controlli dell'interfaccia utente in `my_purple`, ma utilizzerà la combinazione di colori `Theme.Material.Light` in qualsiasi altra posizione:

[![aspetto del tema personalizzato dopo le personalizzazioni](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

In questo esempio, *MyCustomTheme* prende in prestito i colori da `Theme.Material.Light` per il colore di sfondo, la barra di stato e i colori del testo, ma modifica il colore della barra dell'app in `my_blue` e imposta il colore del pulsante di opzione su `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Creazione di uno stile di visualizzazione personalizzato

Android 5,0 rende inoltre possibile lo stile di una singola visualizzazione. Dopo aver creato **Colors. XML** e **Styles. XML** , come descritto nella sezione precedente, è possibile aggiungere uno stile di visualizzazione a **Styles. XML**.
Per applicare uno stile a una singola visualizzazione, attenersi alla procedura seguente:

- Modificare **Resources/values-V21/Styles. XML** e aggiungere un nodo `style` con il nome dello stile di visualizzazione personalizzato. Impostare gli attributi di colore personalizzati per la visualizzazione all'interno di questo nodo `style`. Ad esempio, per creare uno stile [CardView](~/android/user-interface/controls/card-view.md) personalizzato con angoli più arrotondati e che usa `my_blue` come colore di sfondo della scheda, aggiungere un nodo `style` a **Styles. XML** (all'interno del nodo `resources`) e configurare il colore di sfondo e il raggio dell'angolo:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

- Nel layout impostare l'attributo `style` per la visualizzazione in modo che corrisponda al nome dello stile personalizzato scelto nel passaggio precedente. Esempio:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Lo screenshot seguente illustra un esempio del `CardView` predefinito (visualizzato a sinistra) rispetto a una `CardView` con stile con il tema `CardView.MyBlue` personalizzato (visualizzato a destra):

[![esempi di CardView predefiniti e CardView personalizzati](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

In questo esempio, la `CardView` personalizzata viene visualizzata con il colore di sfondo `my_blue` e un raggio dell'angolo 18DP.

## <a name="compatibility"></a>Compatibilità

Per applicare uno stile all'app in modo che usi il tema materiale in Android 5,0 ma ripristina automaticamente uno stile compatibile verso il basso nelle versioni precedenti di Android, seguire questa procedura:

- Definire un tema personalizzato in **Resources/values-V21/Styles. XML** che deriva da uno stile del tema del materiale. Esempio:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- Definire un tema personalizzato in **Resources/values/styles. XML** che deriva da un tema precedente, ma usa lo stesso nome di tema indicato in precedenza. Esempio:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- In **file AndroidManifest. XML**configurare l'app con il nome del tema personalizzato. 
    Esempio:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

- In alternativa, è possibile applicare uno stile a un'attività specifica usando il tema personalizzato:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Se il tema usa i colori definiti in un file **Colors. XML** , assicurarsi di inserire questo file in **Resources/values** (anziché **Resources/values-V21**) in modo che entrambe le versioni del tema personalizzato possano accedere alle definizioni dei colori.

Quando l'app viene eseguita in un dispositivo Android 5,0, userà la definizione del tema specificata in **Resources/values-V21/Styles. XML**. Quando questa app viene eseguita in dispositivi Android meno recenti, viene automaticamente eseguito il fallback alla definizione del tema specificata in **Resources/values/styles. XML**.

Per altre informazioni sulla compatibilità dei temi con le versioni precedenti di Android, vedere [risorse alternative](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto il nuovo stile dell'interfaccia utente del tema materiale incluso in Android 5,0 (Lollipop). Sono state descritte le tre versioni di tema Material predefinite che è possibile usare per applicare uno stile all'app, ma è stato illustrato come creare un tema personalizzato per la personalizzazione dell'app e viene fornito un esempio di come tema una singola visualizzazione. Infine, questo articolo ha illustrato come usare il tema materiale nell'app mantenendo la compatibilità verso il basso con le versioni precedenti di Android.

## <a name="related-links"></a>Collegamenti correlati

- [ThemeSwitcher (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [Introduzione a lollipop](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Risorse alternative](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Lollipop Android](https://developer.android.com/about/versions/lollipop)
- [Android Pie Developer](https://developer.android.com/about/versions/pie/)
- [Progettazione materiale](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Principi di progettazione del materiale](https://material.io/design/)
- [Gestione della compatibilità](https://developer.android.com/training/backward-compatible-ui/)
