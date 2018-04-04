---
title: Utilizzo con il manifesto Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 18817063900437baa625d8572f0ae28fec77be1e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-the-android-manifest"></a>Utilizzo con il manifesto Android


## <a name="overview"></a>Panoramica

**AndroidManifest.xml** è un file potente nella piattaforma Android che consente di descrivere le funzionalità e requisiti dell'applicazione per Android. Tuttavia, in uso non è facile. Xamarin consente di ridurre al minimo questa difficoltà, consentendo di aggiungere attributi personalizzati per le classi, che verranno quindi usate per generare automaticamente il manifesto per l'utente. L'obiettivo è che il 99% dei nostri utenti non deve mai essere necessario modificare manualmente **AndroidManifest.xml**. 

**AndroidManifest.xml** viene generato come parte del processo di compilazione e il codice XML trovato all'interno di **Properties/AndroidManifest.xml** viene unito con il XML generato dagli attributi personalizzati. Risultante unito **AndroidManifest.xml** si trova nel **obj** sottodirectory; ad esempio si trova in **obj/Debug/android/AndroidManifest.xml** per le compilazioni di Debug . Il processo di unione è semplice: attributi personalizzati all'interno del codice utilizzato per generare gli elementi XML, e *inserisce* gli elementi in **AndroidManifest.xml**. 



## <a name="the-basics"></a>Nozioni di base

In fase di compilazione, gli assembly vengono analizzati per non`abstract` classi che derivano da [attività](https://developer.xamarin.com/api/type/Android.App.Activity/) e avere la [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) attributo dichiarato su di essi. Queste classi e attributi viene quindi utilizzato per compilare il manifesto. Si consideri il codice di esempio seguente: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Di conseguenza, non viene generato in **AndroidManifest.xml**. Se si desidera un `<activity/>` elemento deve essere generato, è necessario utilizzare il [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) attributo personalizzato: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

L'esempio seguente genera il seguente frammento xml da aggiungere alla **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

Il `[Activity]` attributo non ha alcun effetto `abstract` tipi; `abstract` tipi vengono ignorati.



### <a name="activity-name"></a>Nome attività

A partire da xamarin 5.1, il nome del tipo di un'attività dipende MD5SUM del nome completo dell'assembly del tipo da esportare. In questo modo lo stesso nome completo essere fornito da due assembly diversi e non ottenere un errore del pacchetto. (Prima 5.1 xamarin, il nome del tipo predefinito dell'attività è stato creato dallo spazio dei nomi lowercased e il nome della classe). 

Se si desidera eseguire l'override di questa impostazione predefinita e specificare in modo esplicito il nome dell'attività, utilizzare il [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) proprietà: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il seguente frammento xml:

```xml
<activity android:name="awesome.demo.activity" />
```

*Nota*: si consiglia di utilizzare il `Name` proprietà solo per motivi di compatibilità con le versioni precedenti, pertanto la ridenominazione può rallentare la ricerca del tipo in fase di esecuzione. Se si dispone di codice legacy che prevede che il nome del tipo predefinito dell'attività sia basata sullo spazio dei nomi lowercased e il nome della classe, vedere [Android Callable Wrapper denominazione](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) per suggerimenti sulla compatibilità di gestione. 


### <a name="activity-title-bar"></a>Sulla barra del titolo di attività

Per impostazione predefinita, Android fornisce all'applicazione una barra del titolo per l'esecuzione. Il valore utilizzato per l'oggetto è [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). Nella maggior parte dei casi, questo valore sarà diverso dal nome classe. Per specificare l'etichetta dell'applicazione sulla barra del titolo, utilizzare il [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) proprietà.
Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il seguente frammento xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Avviabili da selezione applicazione

Per impostazione predefinita, l'attività non appariranno nella schermata di avvio dell'applicazione di Android. In questo modo verrà probabilmente esistere molte attività nell'applicazione e non si desidera un'icona per ogni componente. Per specificare quale deve essere avviabili dall'applicazione di avvio, utilizzare il [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) proprietà. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il seguente frammento xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>Icona attività

Per impostazione predefinita, l'attività verrà assegnata l'icona di avvio predefinito fornito dal sistema. Per utilizzare un'icona personalizzata, aggiungere il **PNG** a **risorse/drawable**, impostare l'azione di compilazione **AndroidResource**, quindi utilizzare il [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) proprietà per specificare l'icona da utilizzare. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il seguente frammento xml:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>Autorizzazioni

Quando si aggiungono le autorizzazioni per il file manifesto Android (come descritto in [aggiungere autorizzazioni al manifesto Android](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest/)), queste autorizzazioni vengono registrate nella **Properties/AndroidManifest.xml**. Ad esempio, se si imposta la `INTERNET` autorizzazione, l'elemento seguente viene aggiunta a **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Alcune autorizzazioni per rendere più semplice eseguire il debug vengono impostate automaticamente le compilazioni di debug (ad esempio `INTERNET` e `READ_EXTERNAL_STORAGE`) &ndash; queste impostazioni vengono impostate solo in generato **obj/Debug/android/AndroidManifest.xml** e non sono visualizzato come abilitato nel **delle autorizzazioni necessarie** impostazioni. 

Ad esempio, se si esamina il file manifesto generato in **obj/Debug/android/AndroidManifest.xml**, è possibile visualizzare gli elementi di autorizzazione aggiunto il seguente: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Nella versione di versione del manifesto di compilazione (in **obj/Debug/android/AndroidManifest.xml**), queste autorizzazioni sono *non* configurata automaticamente. Se si ritiene che il passaggio a una build di rilascio comporta perdere un'autorizzazione che era disponibile nella build di Debug dell'app, verificare di aver impostato questa autorizzazione in modo esplicito **delle autorizzazioni necessarie** le impostazioni per l'app (vedere  **Compilazione > applicazione Android** in Visual Studio per Mac; vedere **proprietà > manifesto Android** in Visual Studio). 




## <a name="advanced-features"></a>Funzionalità avanzate


### <a name="intent-actions-and-features"></a>Funzionalità e le azioni preventivo

Il manifesto Android fornisce un modo descrivere le funzionalità dell'attività. Questa operazione viene eseguita tramite [intenti](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) e [ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) attributo personalizzato. È possibile specificare quali sono le azioni appropriate per l'attività con il [ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) costruttore e le categorie sono appropriate con il [ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) proprietà. È necessario fornire almeno un'attività (ovvero il motivo per cui le attività vengono fornite nel costruttore). `[IntentFilter]` è possibile specificare più volte e risultati di ogni utilizzo in un apposito `<intent-filter/>` elemento all'interno di `<activity/>`. Ad esempio:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Questo esempio produce il seguente frammento xml:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>Elemento dell'applicazione

Il manifesto Android fornisce inoltre un modo dichiarare le proprietà per l'intera applicazione. Questa operazione viene eseguita tramite il `<application>` elemento e la relativa controparte, il [applicazione](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) attributo personalizzato. Si noti che queste sono le impostazioni a livello applicazione (a livello di assembly) anziché le impostazioni per ogni attività. In genere, si dichiara `<application>` le proprietà per l'intera applicazione e quindi eseguire l'override queste impostazioni (in base alle esigenze) su una base per ogni attività. 

Ad esempio, `Application` attributo viene aggiunto al **AssemblyInfo.cs** per indicare che l'applicazione può essere sottoposto a debug, che è il nome leggibile dall'utente **My App**, e che utilizza il `Theme.Light` stile come il tema predefinito per tutte le attività: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Questa dichiarazione genera il seguente frammento XML deve essere generato **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
In questo esempio, tutte le attività nell'app utilizzerà il `Theme.Light` stile. Se si imposta il tema di un'attività su `Theme.Dialog`, solo che verrà utilizzato l'attività di `Theme.Dialog` stile mentre tutte le altre attività nell'app utilizzerà per impostazione predefinita il `Theme.Light` stile come impostato nel `<application>` elemento. 

Il `Application` elemento non è l'unico modo per configurare `<application>` attributi. In alternativa, è possibile inserire attributi direttamente nel `<application>` elemento **Properties/AndroidManifest.xml**. Queste impostazioni vengono unite in finale `<application>` elemento che si trova in **obj/Debug/android/AndroidManifest.xml**. Si noti che il contenuto di **Properties/AndroidManifest.xml** sempre eseguire l'override dei dati forniti dagli attributi personalizzati. 

Esistono numerosi attributi a livello di applicazione che è possibile configurare nel `<application>` elemento; per ulteriori informazioni su queste impostazioni, vedere il [proprietà pubbliche](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) sezione [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Elenco di attributi personalizzati

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : genera un [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) frammento XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : genera un [/manifesto dell'applicazione](http://developer.android.com/guide/topics/manifest/application-element.html) frammento XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : genera un [/manifesto/strumentazione](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) frammento XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : genera un [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) frammento XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : genera un [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) frammento XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : genera un [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) frammento XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : genera un [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) frammento XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : genera un [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) frammento XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : genera un [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) frammento XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : genera un [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) frammento XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : genera un [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) frammento XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : genera un [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) frammento XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : genera un [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) frammento XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : genera un [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) frammento XML

