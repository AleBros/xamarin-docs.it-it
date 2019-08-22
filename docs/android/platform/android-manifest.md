---
title: Uso del manifesto Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 1aaacae8cebed2396661a28c189af44c25238e7b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887833"
---
# <a name="working-with-the-android-manifest"></a>Uso del manifesto Android

**File AndroidManifest. XML** è un file potente della piattaforma Android che consente di descrivere le funzionalità e i requisiti dell'applicazione per Android. Tuttavia, l'utilizzo di tale servizio non è semplice. Novell. Android consente di ridurre al minimo questa difficoltà consentendo di aggiungere attributi personalizzati alle classi, che verranno quindi usate per generare automaticamente il manifesto. Il nostro obiettivo è che il 99% degli utenti non debba mai modificare manualmente **file AndroidManifest. XML**. 

**File AndroidManifest. XML** viene generato come parte del processo di compilazione e il codice XML trovato all'interno di **Properties/file AndroidManifest. XML** viene unito a XML generato da attributi personalizzati. Il **file file AndroidManifest. XML** risultante Unito si trova nella sottodirectory **obj** ; ad esempio, si trova in **obj/debug/Android/file AndroidManifest. XML** per le compilazioni di debug. Il processo di Unione è semplice: USA gli attributi personalizzati all'interno del codice per generare elementi XML e *inserisce* tali elementi in **file AndroidManifest. XML**. 



## <a name="the-basics"></a>Nozioni di base

In fase di compilazione, gli assembly vengono analizzati`abstract` per le classi non che derivano dall' [`[Activity]`](xref:Android.App.ActivityAttribute) [attività](xref:Android.App.Activity) e in cui è stato dichiarato l'attributo. USA quindi tali classi e attributi per compilare il manifesto. Si consideri il codice di esempio seguente: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

In questo modo non viene generato alcun risultato in **file AndroidManifest. XML**. Se si desidera che `<activity/>` venga generato un elemento, è necessario utilizzare il[`[Activity]`](xref:Android.App.Activity) 
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

In questo esempio il frammento XML seguente viene aggiunto a **file AndroidManifest. XML**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

L' `[Activity]` attributo non ha alcun effetto `abstract` sui tipi; `abstract` i tipi vengono ignorati.



### <a name="activity-name"></a>Nome attività

A partire da Novell. Android 5,1, il nome del tipo di un'attività è basato sull'MD5SUM del nome qualificato dall'assembly del tipo esportato. In questo modo è possibile fornire lo stesso nome completo da due assembly diversi e non ottenere un errore di creazione del pacchetto. (Prima di Novell. Android 5,1, il nome di tipo predefinito dell'attività è stato creato dallo spazio dei nomi minuscolo e dal nome della classe). 

Se si desidera eseguire l'override di questa impostazione predefinita e specificare in modo esplicito il nome dell' [`Name`](xref:Android.App.ActivityAttribute.Name) attività, utilizzare la proprietà: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

```xml
<activity android:name="awesome.demo.activity" />
```

*Nota*: è consigliabile usare la `Name` proprietà solo per motivi di compatibilità con le versioni precedenti, in quanto tale ridenominazione può rallentare la ricerca dei tipi in fase di esecuzione. Se si dispone di codice legacy che prevede che il nome del tipo predefinito dell'attività sia basato sullo spazio dei nomi in minuscolo e sul nome della classe, vedere la pagina relativa alla [denominazione di Android Callable Wrapper](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) per suggerimenti sulla gestione della compatibilità. 


### <a name="activity-title-bar"></a>Barra del titolo attività

Per impostazione predefinita, Android assegna all'applicazione una barra del titolo quando viene eseguita. Il valore utilizzato per questo oggetto [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)è. Nella maggior parte dei casi, questo valore è diverso dal nome della classe. Per specificare l'etichetta dell'app sulla barra del titolo, usare la [`Label`](xref:Android.App.ActivityAttribute.Label) proprietà.
Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Avviabile da selezione applicazione

Per impostazione predefinita, l'attività non viene visualizzata nella schermata di avvio dell'applicazione Android. Ciò è dovuto al fatto che nell'applicazione saranno presenti molte attività e che non si desidera un'icona per ciascuna di esse. Per specificare quale deve essere avviabile dall'utilità di avvio dell'applicazione, utilizzare [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) la proprietà. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

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

Per impostazione predefinita, all'attività verrà assegnata l'icona di avvio predefinita fornita dal sistema. Per usare un'icona personalizzata, aggiungere prima il **. png** alle **risorse/disegnatore**, impostare l'azione di compilazione su **AndroidResource**, quindi usare [`Icon`](xref:Android.App.ActivityAttribute.Icon) la proprietà per specificare l'icona da usare. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

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

Quando si aggiungono le autorizzazioni per il manifesto Android, come descritto in [aggiungere autorizzazioni al manifesto Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest), queste autorizzazioni vengono registrate in **Properties/file AndroidManifest. XML**. Se, ad esempio, si imposta `INTERNET` l'autorizzazione, l'elemento seguente viene aggiunto a **Properties/file AndroidManifest. XML**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Le compilazioni di debug impostano automaticamente alcune autorizzazioni per `INTERNET` semplificare `READ_EXTERNAL_STORAGE`il &ndash; debug (ad esempio e). queste impostazioni vengono impostate solo nell'oggetto **obj/debug/Android/file AndroidManifest. XML** generato e non vengono visualizzate come abilitate nella Impostazioni **delle autorizzazioni necessarie** . 

Ad esempio, se si esamina il file manifesto generato in **obj/debug/Android/file AndroidManifest. XML**, è possibile che vengano visualizzati gli elementi di autorizzazione aggiunti seguenti: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Nella versione build di rilascio del manifesto (in **obj/debug/Android/file AndroidManifest. XML**) queste autorizzazioni *non* vengono configurate automaticamente. Se si ritiene che il cambio a una build di rilascio provochi la perdita di un'autorizzazione disponibile nella build di debug da parte dell'app, verificare di aver impostato in modo esplicito questa autorizzazione nelle impostazioni delle **autorizzazioni necessarie** per l'app (vedere **compilare > Android Applicazione** in Visual Studio per Mac; vedere **proprietà > manifesto Android** in Visual Studio). 




## <a name="advanced-features"></a>Funzionalità avanzate


### <a name="intent-actions-and-features"></a>Azioni e funzionalità per finalità

Il manifesto Android fornisce un modo per descrivere le funzionalità dell'attività. Questa operazione viene eseguita tramite [Intent](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) e [`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
attributo personalizzato. È possibile specificare quali azioni sono appropriate per l'attività con[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
e quali categorie sono appropriate con il[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
. È necessario fornire almeno un'attività (motivo per cui le attività vengono fornite nel costruttore). `[IntentFilter]`può essere specificato più volte e ogni utilizzo produce un elemento distinto `<intent-filter/>` `<activity/>`all'interno di. Ad esempio:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

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

Il manifesto Android fornisce anche un modo per dichiarare le proprietà per l'intera applicazione. Questa operazione viene eseguita tramite `<application>` l'elemento e la relativa controparte, l'attributo personalizzato dell' [applicazione](xref:Android.App.ApplicationAttribute) . Si noti che si tratta di impostazioni a livello di applicazione (a livello di assembly) anziché di impostazioni per ogni attività. In genere, si `<application>` dichiarano le proprietà per l'intera applicazione e quindi si esegue l'override di queste impostazioni (in base alle esigenze) per ogni attività. 

Ad esempio, l'attributo `Application` seguente viene aggiunto a **AssemblyInfo.cs** per indicare che l'applicazione può essere sottoposta a debug, che il nome leggibile dall'utente è l' **app**e che usa `Theme.Light` lo stile come tema predefinito per tutti attività 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Questa dichiarazione causa la generazione del frammento XML seguente in **obj/debug/Android/file AndroidManifest. XML**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

In questo esempio, tutte le attività nell'app utilizzeranno per impostazione `Theme.Light` predefinita lo stile. Se si imposta il tema di un'attività `Theme.Dialog`su, solo quell'attività userà `Theme.Dialog` lo stile mentre tutte le altre attività nell' `Theme.Light` app utilizzeranno per impostazione predefinita lo stile impostato `<application>` nell'elemento. 

L' `Application` elemento non è l'unico modo per configurare `<application>` gli attributi. In alternativa, è possibile inserire gli attributi direttamente nell' `<application>` elemento di **Properties/file AndroidManifest. XML**. Queste impostazioni vengono unite nell'elemento `<application>` finale che risiede in **obj/debug/Android/file AndroidManifest. XML**. Si noti che il contenuto di **Properties/file AndroidManifest. XML** esegue sempre l'override dei dati forniti dagli attributi personalizzati. 

Sono disponibili molti attributi a livello di applicazione che è possibile configurare nell' `<application>` elemento. per altre informazioni su queste impostazioni, vedere la sezione [proprietà pubbliche](xref:Android.App.ApplicationAttribute) di [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 



## <a name="list-of-custom-attributes"></a>Elenco di attributi personalizzati

- [Android. app. ActivityAttribute](xref:Android.App.ActivityAttribute) : Genera un frammento XML [/manifest/Application/Activity](https://developer.android.com/guide/topics/manifest/activity-element.html) 
- [Android. app. ApplicationAttribute](xref:Android.App.ApplicationAttribute) : Genera un frammento XML [/manifest/Application](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android. app. InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : Genera un frammento XML [/manifest/Instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
- [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : Genera un frammento XML [//Intent-Filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android. app. MetaDataAttribute](xref:Android.App.MetaDataAttribute) : Genera un frammento XML [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android. app. PermissionAttribute](xref:Android.App.PermissionAttribute) : Genera un frammento XML [//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android.App.PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : Genera un frammento XML [//Permission-Group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android.App.PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : Genera un frammento XML [//Permission-Tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute) : Genera un frammento XML [/manifest/Application/Service](https://developer.android.com/guide/topics/manifest/service-element.html) 
- [Android. app. UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : Genera un frammento XML [/manifest/application/uses-Library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
- [Android. app. UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : Genera un frammento XML [/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android.Content.BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : Genera un frammento XML [/manifest/Application/Receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
- [Android. Content. ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : Genera un frammento XML [/manifest/Application/Provider](https://developer.android.com/guide/topics/manifest/provider-element.html) 
- [Android. Content. GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : Genera un frammento XML [/manifest/Application/Provider/Grant-URI-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)

