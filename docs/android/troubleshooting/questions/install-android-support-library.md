---
title: Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 107fcd39a64ef1d7758d211ab47a07e1ece52f4e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757233"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Passaggi di esempio per Novell. Android. support. v4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Scaricare il pacchetto NuGet Novell. Android. support desiderato, ad esempio installarlo con gestione pacchetti NuGet.

Usare `ildasm` per verificare la versione di **android_m2repository. zip** necessaria per il pacchetto NuGet:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Output di esempio:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Scaricare **Android\_m2repository. zip** da Google usando l'URL restituito da **Ildasm**. In alternativa, è possibile verificare la versione del _repository del supporto Android_ attualmente installata nell'Android SDK Manager:

!["Android SDK Manager che mostra il repository del supporto Android versione 32 installata"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponde a quella necessaria per il pacchetto NuGet, non è necessario scaricare nulla di nuovo. È invece possibile eseguire nuovamente il zip della directory **m2repository** esistente che si trova **in\\extra Android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Android SDK Manager).

Calcolare l'hash MD5 dell'URL restituito da **Ildasm**. Formattare la stringa risultante per utilizzare tutte le lettere maiuscole e nessun spazio. Ad esempio, modificare la `$url` variabile in base alle esigenze e quindi eseguire le due righe seguenti (in base [al codice originale C# di Novell. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) in PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Output di esempio:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copiare **Android\_m2repository. zip** nella cartella **% LocalAppData%\\Novell\\Zip\\**  . Rinominare il file in modo da usare l'hash MD5 dal passaggio di calcolo hash MD5 precedente. Ad esempio:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

Opzionale Decomprimere il file in **%\\LocalAppData\\% Novell Novell. Android. support\\.\\V4\\ 23.4.0.0 Content** (creazione di un **contenuto\\m2repository** sottodirectory). Se si ignora questo passaggio, la prima compilazione che usa la libreria richiederà un po' più tempo perché sarà necessario completare questo passaggio.
Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non corrisponde alla versione del pacchetto NuGet. È possibile usare `ildasm` per trovare il numero di versione corretto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Output di esempio:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Scaricare il pacchetto NuGet Novell. Android. support desiderato, ad esempio installarlo con gestione pacchetti NuGet.

Fare doppio clic sull'assembly _Novell. Android. support. v4_ nella sezione _riferimenti_ del progetto Android in Visual Studio per Mac per aprire l'assembly nel browser assembly. Verificare che l'elenco a discesa _lingua_ sia impostato su _C#_ e selezionare l'assembly _Novell. Android. support. v4_ di primo livello dall'albero di navigazione del browser assembly. Individuare la `SourceUrl` proprietà sotto uno `IncludeAndroidResourcesFrom` degli attributi o `JavaLibraryReference` :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Scaricare **Android\_m2repository. zip** da Google usando il `SourceUrl` restituito da **Ildasm**. In alternativa, è possibile verificare la versione del _repository del supporto Android_ attualmente installata nell'Android SDK Manager:

!["Android SDK Manager che mostra il repository del supporto Android versione 32 installata"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponde a quella necessaria per il pacchetto NuGet, non è necessario scaricare nulla di nuovo. È invece possibile eseguire nuovamente il zip della directory **m2repository** esistente che si trova in **Extras/Android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Android SDK Manager).

Calcolare l'hash MD5 dell'URL restituito da **Ildasm**. Formattare la stringa risultante per utilizzare tutte le lettere maiuscole e nessun spazio. Ad esempio, modificare la stringa dell'URL in base alle esigenze e quindi eseguire il comando seguente in un prompt dei comandi di **Terminal. app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Un'altra opzione consiste nell'usare `csharp` l'interprete per eseguire [lo C# stesso codice usato da Novell. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
A tale scopo, modificare la `url` variabile in base alle esigenze e quindi eseguire il comando seguente in un prompt dei comandi di **Terminal. app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Output di esempio:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copiare **Android\_m2repository. zip** nella cartella **$Home/.local/share/Xamarin/Zips/** . Rinominare il file in modo da usare l'hash MD5 dal passaggio di calcolo hash MD5 precedente. Ad esempio:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

Opzionale Decomprimere il file in: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(creazione di una sottodirectory **Content/m2repository** ). Se si ignora questo passaggio, la prima compilazione che usa la libreria richiederà un po' più tempo perché sarà necessario completare questo passaggio.

Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non corrisponde alla versione del pacchetto NuGet. Come nel passaggio precedente di **Ildasm** , è possibile usare il browser Assembly in Visual Studio per Mac per trovare il numero di versione corretto. Cercare la `Version` proprietà sotto uno `IncludeAndroidResourcesFrom` degli attributi o `JavaLibraryReference` :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Altri riferimenti

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – non accurato "download non riuscito. Scaricarlo {0} e inserirlo {1} nella directory ". e "installare il pacchetto:'{0}' disponibile nel programma di installazione dell'SDK" messaggi di errore correlati a Novell. Android. Support Packages

### <a name="next-steps"></a>Fasi successive

In questo documento viene illustrato il comportamento corrente a partire dal 2016 agosto. La tecnica descritta in questo documento non fa parte del gruppo di test stabili per Novell, quindi potrebbe interrompersi in futuro.

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario .
