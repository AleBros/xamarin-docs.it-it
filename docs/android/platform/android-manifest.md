---
title: Utilizzo di manifesto Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 655f988cc54cf54e346e68109271775dee2918a9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111252"
---
# <a name="working-with-the-android-manifest"></a>Utilizzo di manifesto Android


## <a name="overview"></a>Panoramica

**Androidmanifest. XML** è un file potenti nella piattaforma Android che consente di descrivere le funzionalità e requisiti dell'applicazione per Android. Tuttavia, le operazioni desiderate non sono facile. Xamarin. Android consente di ridurre al minimo questa difficoltà, consentendo di aggiungere attributi personalizzati per le classi, che verranno quindi usate per generare automaticamente il manifesto per l'utente. Il nostro obiettivo consiste nel fatto che il 99% dei nostri utenti mai necessario modificare manualmente **androidmanifest. XML**. 

**Androidmanifest. XML** viene generato come parte del processo di compilazione e il codice XML trovato all'interno **Properties/AndroidManifest.xml** è unito a dati XML generati dagli attributi personalizzati. Risultante unite **androidmanifest. XML** si trova nel **obj** sottodirectory; ad esempio, si trova nella **obj/Debug/android/AndroidManifest.xml** per le compilazioni di Debug . Il processo di unione è piuttosto semplice: Usa gli attributi personalizzati all'interno del codice per generare gli elementi XML, e *inserisce* tali elementi in **androidmanifest. XML**. 



## <a name="the-basics"></a>Nozioni di base

In fase di compilazione, gli assembly vengono analizzati per non -`abstract` classi che derivano da [attività](https://developer.xamarin.com/api/type/Android.App.Activity/) e hanno il [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) attributo dichiarato su di essi. Utilizza quindi tali classi e attributi per il manifesto di compilazione. Si consideri il codice di esempio seguente: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Il risultato è nothing generati nel **androidmanifest. XML**. Se si desidera che un `<activity/>` elemento deve essere generato, è necessario usare il [`[Activity]`](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) 
attributo personalizzato: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

L'esempio seguente genera il seguente frammento xml da aggiungere alla **androidmanifest. XML**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

Il `[Activity]` attributo non ha alcun effetto sul `abstract` tipi; `abstract` tipi vengono ignorati.



### <a name="activity-name"></a>Nome attività

A partire da xamarin. Android 5.1, il nome del tipo di un'attività si basa su MD5SUM del nome qualificato dall'assembly del tipo in fase di esportazione. In questo modo lo stesso nome completo per essere forniti dalle due assembly diversi e non visualizzato un errore di creazione dei pacchetti. (Prima di xamarin. Android 5.1, il nome del tipo predefinito dell'attività è stato creato dallo spazio dei nomi in minuscolo e il nome della classe). 

Se si vuole eseguire l'override di questa impostazione predefinita e specificare in modo esplicito il nome dell'attività, usare il [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) proprietà: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento xml seguente:

```xml
<activity android:name="awesome.demo.activity" />
```

*Nota*: è consigliabile usare il `Name` proprietà solo per motivi di compatibilità, di conseguenza la ridenominazione può rallentare la ricerca del tipo in fase di esecuzione. Se si dispone di codice legacy che prevede che il nome del tipo predefinito dell'attività per lo spazio dei nomi in minuscolo in base e il nome della classe, vedere [Android Callable Wrapper denominazione](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) per suggerimenti sulla compatibilità di gestione. 


### <a name="activity-title-bar"></a>Barra del titolo attività

Per impostazione predefinita, Android offre all'applicazione una barra del titolo quando viene eseguito. Il valore usato per l'oggetto è [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). Nella maggior parte dei casi, questo valore sarà diverso dal nome classe. Per specificare l'etichetta della tua app sulla barra del titolo, usare il [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) proprietà.
Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento xml seguente:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Dal selettore dell'applicazione

Per impostazione predefinita, l'attività non verrà riportata nella schermata di utilità di avvio dell'applicazione di Android. Infatti, vi saranno probabilmente molte attività nell'applicazione, e non si desidera un'icona per ognuno. Per specificare quale dovrebbe essere avviabili dall'applicazione di avvio, usare il [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) proprietà. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento xml seguente:

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

Per impostazione predefinita, l'attività verrà fornita l'icona dell'utilità di avvio predefinita fornita dal sistema. Per usare un'icona personalizzata, aggiungere prima la **. PNG** a **risorse/drawable**, impostare la proprietà azione compilazione su **AndroidResource**, quindi usare il [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) proprietà per specificare l'icona da utilizzare. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento xml seguente:

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

Quando si aggiungono le autorizzazioni per il manifesto Android (come descritto in [aggiungere le autorizzazioni del manifesto Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), queste autorizzazioni vengono registrate nella **Properties/AndroidManifest.xml**. Ad esempio, se si imposta la `INTERNET` l'autorizzazione, l'elemento seguente viene aggiunto a **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Build di debug impostato automaticamente alcune autorizzazioni per semplificare l'esecuzione del debug (ad esempio `INTERNET` e `READ_EXTERNAL_STORAGE`) &ndash; queste impostazioni sono configurate solo in generato **obj/Debug/android/AndroidManifest.xml** e non sono visualizzata come abilitata nel **autorizzazioni necessarie** impostazioni. 

Ad esempio, se si esamina il file manifesto generato in **obj/Debug/android/AndroidManifest.xml**, è probabile che sono stati aggiunti elementi delle autorizzazioni seguenti: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Nella versione di versione del manifesto di compilazione (in **obj/Debug/android/AndroidManifest.xml**), queste autorizzazioni sono *non* configurata automaticamente. Se si ritiene che il passaggio a una build di rilascio comporta la perdita di un'autorizzazione che era disponibile nella build di Debug nell'app, verificare di aver impostato questa autorizzazione in modo esplicito **autorizzazioni necessarie** impostazioni per l'app (vedere  **Compilazione > applicazione Android** in Visual Studio per Mac, vedere **proprietà > manifesto Android** in Visual Studio). 




## <a name="advanced-features"></a>Funzionalità avanzate


### <a name="intent-actions-and-features"></a>Funzioni e azioni di tipo intent

Manifesto Android fornisce un modo per descrivere le funzionalità dell'attività. Questa operazione viene eseguita tramite [Intent](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) e [`[IntentFilter]`](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) 
attributo personalizzato. È possibile specificare quali azioni siano appropriate per l'attività con il [`IntentFilter`](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) 
costruttore e quali categorie sono appropriate con il [`Categories`](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) 
. È necessario fornire almeno un'attività (per questo motivo le attività vengono fornite nel costruttore). `[IntentFilter]` è possibile offrire più volte e ogni uso genera un oggetto separato `<intent-filter/>` elemento all'interno di `<activity/>`. Ad esempio:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento xml seguente:

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


### <a name="application-element"></a>Elemento Application

Manifesto Android offre inoltre un modo per la dichiarazione delle proprietà per l'intera applicazione. Questa operazione viene eseguita tramite il `<application>` elemento e la controparte, il [applicazione](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) attributo personalizzato. Si noti che queste sono le impostazioni (a livello di assembly) a livello di applicazione anziché le impostazioni per ogni attività. In genere, dichiara `<application>` le proprietà per l'intera applicazione e quindi eseguire l'override queste impostazioni (se necessario) in base a una per ogni attività. 

Il seguente, ad esempio, `Application` attributo viene aggiunto alla **AssemblyInfo.cs** per indicare che l'applicazione può essere sottoposto a debug, che è il nome leggibile dall'utente **My App**, e che utilizza il `Theme.Light` stile come il tema predefinito per tutte le attività: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Questa dichiarazione fa sì che il frammento XML seguente deve essere generato **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
In questo esempio, tutte le attività nell'app predefinita saranno il `Theme.Light` stile. Se si imposta il tema dell'attività su `Theme.Dialog`, solo che attività useranno il `Theme.Dialog` stile mentre tutte le altre attività nell'app predefinita sarà il `Theme.Light` stile come impostato nel `<application>` elemento. 

Il `Application` elemento non è l'unico modo per configurare `<application>` attributi. In alternativa, è possibile inserire attributi direttamente nel `<application>` dell'elemento **Properties/AndroidManifest.xml**. Queste impostazioni vengono unite in finale `<application>` elemento che si trova in **obj/Debug/android/AndroidManifest.xml**. Si noti che il contenuto del **Properties/AndroidManifest.xml** sempre eseguire l'override dei dati forniti dagli attributi personalizzati. 

Esistono molti attributi a livello di applicazione che è possibile configurare nel `<application>` elemento; per altre informazioni su queste impostazioni, vedere la [le proprietà pubbliche](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) sezione del [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Elenco di attributi personalizzati

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : genera una [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) frammento XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : genera una [/manifesto/applicazione](http://developer.android.com/guide/topics/manifest/application-element.html) frammento XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : genera una [manifesto/strumentazione](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) frammento XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : genera una [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) frammento XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : genera una [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) frammento XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : genera una [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) frammento XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : genera una [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) frammento XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : genera una [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) frammento XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : genera una [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) frammento XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : genera una [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) frammento XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : genera una [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) frammento XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : genera una [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) frammento XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : genera una [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) frammento XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : genera una [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) frammento XML

