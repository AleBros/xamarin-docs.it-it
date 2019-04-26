---
title: App Wear creazione dei pacchetti
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 585c276b327a9092bdd13fa633307477017558c5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276795"
---
# <a name="packaging-wear-apps"></a>App Wear creazione dei pacchetti

Sono inclusi le app Android Wear con un'app di Android completo per la distribuzione in Google Play. 

## <a name="automatic-packaging"></a>Creazione automatica di pacchetti

A partire da Xamarin Android 5.0, l'app Wear viene automaticamente fornito come una risorsa nell'app palmari quando si crea un riferimento al progetto dal progetto palmare al progetto Wear. È possibile utilizzare la procedura seguente per creare questa associazione: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Se l'app Wear non è già parte della soluzione palmare, fare doppio clic sul nodo della soluzione e selezionare **Aggiungi > Aggiungi progetto esistente...** .

2. Passare al **file con estensione csproj** file dell'app per Wear, selezionarlo e fare clic su **Open**. Il progetto dell'app Wear dovrebbe essere ora visibile nella soluzione palmare.

3. Fare doppio clic il **riferimenti** nodo e selezionare **Aggiungi riferimento**.

4. Nel **gestione riferimenti** finestra di dialogo, usura del progetto (fare clic per aggiungere un segno di spunta), quindi fare clic su enable **OK**.

5. Modificare il nome del pacchetto per il progetto Wear in modo che corrisponda il nome del pacchetto del progetto palmare (il nome del pacchetto può essere modificato in **proprietà > manifesto Android**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Se l'app Wear non è già parte della soluzione palmare, fare doppio clic sul nodo della soluzione e selezionare **Aggiungi > Aggiungi progetto esistente...** .

2. Passare al **file con estensione csproj** file dell'app per Wear, selezionarlo e fare clic su **Open**. Il progetto dell'app Wear dovrebbe essere ora visibile nella soluzione palmare.

3. Fare clic sul nodo del progetto palmari nella soluzione e fare clic su **Modifica riferimenti...** .

4. Nel **Modifica riferimenti** finestra di dialogo, usura del progetto (fare clic per aggiungere un segno di spunta), quindi fare clic su enable **OK**.

5. Modificare il nome del pacchetto per il progetto Wear in modo che corrisponda il nome del pacchetto del progetto palmare (il nome del pacchetto può essere modificato in **opzioni progetto > applicazione Android**).

-----


Si noti che si otterrà un' **XA5211** errore se il nome del pacchetto dell'app Wear corrisponde il nome del pacchetto dell'app palmari. Ad esempio:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Per correggere questo errore, modificare il nome del pacchetto dell'app Wear in modo che corrisponda il nome del pacchetto dell'app palmari.

Quando fa clic su **compilazione > compila tutto**, questa associazione attiva automatiche dei pacchetti del progetto Wear nel progetto principale palmare (Phone). L'app Wear viene automaticamente creata e inclusa come risorsa nell'app palmari.

L'assembly che genera il progetto dell'app Wear non viene usato come un riferimento all'assembly nel progetto palmare (Phone). Al contrario, il processo di compilazione esegue le operazioni seguenti:

-   Verifica che il pacchetto nomi corrispondano. 

-   Genera codice XML e lo aggiunge al progetto portatile per associarlo all'app Wear. Ad esempio: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   Aggiunge l'app per Wear come un **raw** risorse al progetto portatile. 


## <a name="manual-packaging"></a>Creazione di pacchetti manuale

È possibile scrivere le app Android Wear in xamarin. Android precedenti alla versione 5.0, ma è necessario seguire queste istruzioni di creazione di pacchetti manuale per distribuire l'app: 

1. Verificare che il progetto indossabile e progetti palmare (Phone) abbiano lo stesso nome del pacchetto e del numero di versione.

2. Creare manualmente il progetto indossabile come un **rilascio** di compilazione.

3. Aggiungere manualmente la versione **. File APK** dal passaggio (2) il **risorse/raw** directory del progetto palmare (Phone).

4. Aggiungere manualmente una nuova risorsa XML **Resources/xml/wearable_app_desc.xml** nel progetto palmare che fa riferimento a indossabile **APK** ottenuto nel passaggio (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Aggiungere manualmente un `<meta-data />` elemento del progetto palmari **androidmanifest. XML** `<application>` elemento che fa riferimento alla nuova risorsa XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Vedere anche il sito di Android Developer [packging manuale istruzioni](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually).

