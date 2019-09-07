---
title: Creazione di pacchetti di app Wear
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: fa35f6fe2388484875180594f18041947963ef7a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763970"
---
# <a name="packaging-wear-apps"></a>Creazione di pacchetti di app Wear

Le app Android Wear sono assemblate con un'app Android completa per la distribuzione in Google Play. 

## <a name="automatic-packaging"></a>Creazione automatica dei pacchetti

A partire da Novell Android 5,0, l'app Wear viene automaticamente assemblata come risorsa nell'app palmare quando si crea un riferimento al progetto dal progetto palmare al progetto Wear. Per creare questa associazione, è possibile usare i passaggi seguenti: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Se l'app Wear non fa già parte della soluzione Handheld, fare clic con il pulsante destro del mouse sul nodo della soluzione e scegliere **aggiungi > Aggiungi progetto esistente**.

2. Passare al file con **estensione csproj** dell'app Wear, selezionarlo e fare clic su **Apri**. Il progetto di app Wear dovrebbe essere ora visibile nella soluzione Handheld.

3. Fare clic con il pulsante destro del mouse sul nodo **riferimenti** e selezionare **Aggiungi riferimento**.

4. Nella finestra di dialogo **Gestione riferimenti** abilitare il progetto Wear (fare clic per aggiungere un segno di spunta), quindi fare clic su **OK**.

5. Modificare il nome del pacchetto per il progetto Wear in modo che corrisponda al nome del pacchetto del progetto Handheld (il nome del pacchetto può essere modificato in **proprietà > manifesto Android**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Se l'app Wear non fa già parte della soluzione Handheld, fare clic con il pulsante destro del mouse sul nodo della soluzione e scegliere **aggiungi > Aggiungi progetto esistente**.

2. Passare al file con **estensione csproj** dell'app Wear, selezionarlo e fare clic su **Apri**. Il progetto di app Wear dovrebbe essere ora visibile nella soluzione Handheld.

3. Fare clic con il pulsante destro del mouse sul nodo del progetto Handheld nella soluzione e scegliere **modifica riferimenti.**

4. Nella finestra di dialogo **modifica riferimenti** abilitare il progetto Wear (fare clic per aggiungere un segno di spunta), quindi fare clic su **OK**.

5. Modificare il nome del pacchetto per il progetto Wear in modo che corrisponda al nome del pacchetto del progetto Handheld (il nome del pacchetto può essere modificato in **Opzioni progetto > applicazione Android**).

-----

Si noti che si riceverà un errore **XA5211** se il nome del pacchetto dell'app Wear non corrisponde al nome del pacchetto dell'app Handheld. Ad esempio:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Per correggere l'errore, modificare il nome del pacchetto dell'app Wear in modo che corrisponda al nome del pacchetto dell'app Handheld.

Quando si fa clic su **compila > compila tutto**, questa associazione attiva il packaging automatico del progetto Wear nel progetto principale (telefono) del palmare. L'app Wear viene automaticamente compilata e inclusa come risorsa nell'app Handheld.

L'assembly generato dal progetto di app Wear non viene usato come riferimento a un assembly nel progetto Handheld (Phone). Al contrario, il processo di compilazione esegue le operazioni seguenti:

- Verifica che i nomi dei pacchetti corrispondano. 

- Genera il codice XML e lo aggiunge al progetto Handheld per associarlo all'app Wear. Ad esempio: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- Aggiunge l'app Wear come risorsa non **elaborata** al progetto Handheld. 

## <a name="manual-packaging"></a>Creazione manuale del pacchetto

È possibile scrivere app Android Wear in Novell. Android prima della versione 5,0, ma è necessario seguire le istruzioni per la creazione manuale del pacchetto per distribuire l'app: 

1. Assicurarsi che i progetti di progetto e palmare (telefono) indossabili abbiano lo stesso numero di versione e il nome del pacchetto.

2. Compilare manualmente il progetto indossabile come build di **rilascio** .

3. Aggiungere manualmente la versione **. APK** dal passaggio (2) nella directory **Resources/RAW** del progetto Handheld (Phone).

4. Aggiungere manualmente una nuova risorsa XML Resource **/XML/wearable_app_desc. XML** nel progetto Handheld, che fa riferimento a **apk** indossabile dal passaggio (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Aggiungere manualmente un `<meta-data />` elemento all'elemento **file AndroidManifest. XML** `<application>` del progetto Handheld che fa riferimento alla nuova risorsa XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Vedere anche le [istruzioni packging manuali](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)del sito per sviluppatori Android.
