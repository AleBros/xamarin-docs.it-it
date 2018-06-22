---
title: Creazione di pacchetti usura App
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: af96c0f8cf862b7a208beb5b91ecbb30598b09d9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767713"
---
# <a name="packaging-wear-apps"></a>Creazione di pacchetti usura App

Le app Android usura sono inclusi con un'app Android completa per la distribuzione su Google Play. 

## <a name="automatic-packaging"></a>Creazione automatica di pacchetti

A partire da Xamarin Android 5.0, l'app usura viene automaticamente compresso come risorsa nell'app palmari quando si crea un riferimento al progetto dal progetto palmari al progetto usura. Per creare questa associazione, è possibile utilizzare la procedura seguente: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Se l'app usura non è già parte della soluzione palmare, fare doppio clic sul nodo della soluzione e selezionare **Aggiungi > Aggiungi progetto esistente...** .

2. Passare il **csproj** file dell'app usura, selezionarla e fare clic su **aprire**. Il progetto di app usura dovrebbe ora essere visibile nella soluzione palmari.

3. Fare doppio clic su di **riferimenti** nodo e selezionare **Aggiungi riferimento**.

4. Nel **gestione riferimenti** finestra di dialogo, usura progetto (fare clic per aggiungere un segno di spunta), quindi fare clic su Abilita **OK**.

5. Modificare il nome del pacchetto per il progetto usura in modo che corrisponda al nome del pacchetto del progetto palmare (il nome del pacchetto può essere modificato in **proprietà > manifesto Android**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Se l'app usura non è già parte della soluzione palmare, fare doppio clic sul nodo della soluzione e selezionare **Aggiungi > Aggiungi progetto esistente...** .

2. Passare il **csproj** file dell'app usura, selezionarla e fare clic su **aprire**. Il progetto di app usura dovrebbe ora essere visibile nella soluzione palmari.

3. Il nodo del progetto palmari nella soluzione e fare clic destro **Modifica riferimenti...** .

4. Nel **Modifica riferimenti** finestra di dialogo, usura progetto (fare clic per aggiungere un segno di spunta), quindi fare clic su Abilita **OK**.

5. Modificare il nome del pacchetto per il progetto usura in modo che corrisponda al nome del pacchetto del progetto palmare (il nome del pacchetto può essere modificato in **opzioni progetto > applicazione Android**).

-----


Si noti che verrà visualizzato un **XA5211** errore se il nome del pacchetto dell'app usura non corrisponde al nome di pacchetto dell'app palmari. Ad esempio:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Per correggere l'errore, modificare il nome del pacchetto dell'app usura in modo che corrisponda al nome del pacchetto dell'app palmari.

Quando fa clic su **compilazione > Build All**, questa associazione è attiva automatiche dei pacchetti del progetto usura nel progetto principale palmare (Phone). L'applicazione di usura viene automaticamente compilato e inclusa come risorsa nell'app palmari.

L'assembly che genera il progetto di app usura non viene usato come riferimento a un assembly nel progetto palmare (Phone). Al contrario, il processo di compilazione esegue le operazioni seguenti:

-   Verifica che il pacchetto nomi corrispondano. 

-   Genera codice XML e lo aggiunge al progetto per associarlo con l'app usura palmari. Ad esempio: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   Aggiunge l'app usura come un **raw** risorse al progetto palmari. 


## <a name="manual-packaging"></a>Creazione manuale di pacchetti

È possibile scrivere App, usare Android di xamarin prima versione 5.0, ma è necessario seguire queste istruzioni di creazione manuale di pacchetti per distribuire l'app: 

1. Verificare che il progetto indossabile e progetti palmare (Phone) abbiano lo stesso nome del pacchetto e del numero di versione.

2. Creare manualmente il progetto indossabile come un **versione** di compilazione.

3. Aggiungere manualmente la versione **. APK** dal passaggio (2) il **risorse/raw** directory del progetto palmare (Phone).

4. Aggiungere manualmente una nuova risorsa XML **Resources/xml/wearable_app_desc.xml** nel progetto di palmari che fa riferimento a indossabile **APK** dal passaggio (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Aggiungere manualmente un `<meta-data />` elemento al progetto palmari **AndroidManifest.xml** `<application>` elemento che fa riferimento alla nuova risorsa XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Vedere anche il sito di Android Developer [packging manuale istruzioni](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually).

