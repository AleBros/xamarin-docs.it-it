---
title: Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026933"
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

Scaricare **android\_m2repository. zip** da Google usando l'URL restituito da **Ildasm**. In alternativa, è possibile verificare la versione del _repository del supporto Android_ attualmente installata nell'Android SDK Manager:

!["Android SDK Manager che mostra il repository del supporto Android versione 32 installata"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponde a quella necessaria per il pacchetto NuGet, non è necessario scaricare nulla di nuovo. È invece possibile eseguire nuovamente il zip della directory **m2repository** esistente che si trova in **extra\\Android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Android SDK Manager).

Calcolare l'hash MD5 dell'URL restituito da **Ildasm**. Formattare la stringa risultante per utilizzare tutte le lettere maiuscole e nessun spazio. Modificare ad esempio la variabile `$url` in base alle esigenze e quindi eseguire le due righe seguenti (in base [al C# codice originale di Novell. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) in PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Output di esempio:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copiare **android\_m2repository. zip** nella cartella **% LocalAppData%\\novell\\zip\\** . Rinominare il file in modo da usare l'hash MD5 dal passaggio di calcolo hash MD5 precedente. Esempio:

**% LOCALAPPDATA%\\Novell\\zip\\F16A3455987DBAE5783F058F19F7FCDF. zip**

Opzionale Decomprimere il file in **% LocalAppData%\\novell\\Novell. Android. support. v4\\23.4.0.0\\content\\** (creando un **contenuto\\** sottodirectory m2repository). Se si ignora questo passaggio, la prima compilazione che usa la libreria richiederà un po' più tempo perché sarà necessario completare questo passaggio.
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

Fare doppio clic sull'assembly _Novell. Android. support. v4_ nella sezione _riferimenti_ del progetto Android in Visual Studio per Mac per aprire l'assembly nel browser assembly. Verificare che l'elenco a discesa _lingua_ sia impostato su _C#_ e selezionare l'assembly _Novell. Android. support. v4_ di primo livello dall'albero di navigazione del browser assembly. Individuare la proprietà `SourceUrl` sotto uno degli attributi `IncludeAndroidResourcesFrom` o `JavaLibraryReference`:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Scaricare **android\_m2repository. zip** da Google usando i `SourceUrl` restituiti da **Ildasm**. In alternativa, è possibile verificare la versione del _repository del supporto Android_ attualmente installata nell'Android SDK Manager:

!["Android SDK Manager che mostra il repository del supporto Android versione 32 installata"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponde a quella necessaria per il pacchetto NuGet, non è necessario scaricare nulla di nuovo. È invece possibile eseguire nuovamente il zip della directory **m2repository** esistente che si trova in **Extras/Android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Android SDK Manager).

Calcolare l'hash MD5 dell'URL restituito da **Ildasm**. Formattare la stringa risultante per utilizzare tutte le lettere maiuscole e nessun spazio. Ad esempio, modificare la stringa dell'URL in base alle esigenze e quindi eseguire il comando seguente in un prompt dei comandi di **Terminal. app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Un'altra opzione consiste nell'usare l'interprete `csharp` per eseguire [lo C# stesso codice usato da Novell. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
A tale scopo, modificare la variabile `url` in base alle esigenze, quindi eseguire il comando seguente in un prompt dei comandi di **Terminal. app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Output di esempio:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copiare **android\_m2repository. zip** nella cartella **$Home/.local/share/Xamarin/Zips/** . Rinominare il file in modo da usare l'hash MD5 dal passaggio di calcolo hash MD5 precedente. Esempio:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

Opzionale Decomprimere il file in: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(creazione di una sottodirectory **Content/m2repository** ). Se si ignora questo passaggio, la prima compilazione che usa la libreria richiederà un po' più tempo perché sarà necessario completare questo passaggio.

Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non corrisponde alla versione del pacchetto NuGet. Come nel passaggio precedente di **Ildasm** , è possibile usare il browser Assembly in Visual Studio per Mac per trovare il numero di versione corretto. Cercare la proprietà `Version` sotto uno degli attributi `IncludeAndroidResourcesFrom` o `JavaLibraryReference`:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – non accurato "download non riuscito. Scaricare {0} e inserirlo nella directory {1} ". e "installare il pacchetto:'{0}' disponibile nel programma di installazione dell'SDK" messaggi di errore correlati a Novell. Android. Support Packages

### <a name="next-steps"></a>Passaggi successivi

In questo documento viene illustrato il comportamento corrente a partire dal 2016 agosto. La tecnica descritta in questo documento non fa parte del gruppo di test stabili per Novell, quindi potrebbe interrompersi in futuro.

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario .
