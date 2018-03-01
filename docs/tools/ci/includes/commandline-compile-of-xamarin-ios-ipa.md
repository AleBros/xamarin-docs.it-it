
La seguente riga di comando per specificare una build di rilascio della soluzione **SOLUTION_FILE.sln** per iPhone. Il percorso del IPA può essere impostato specificando il la `IpaPackageDir` proprietà nella riga di comando:

 - Nel Mac, utilizzando **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

Il **xbuild** comando si trova in genere nella directory **/Library/Frameworks/Mono.framework/Commands**.

 - In Windows, tramite **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** non espanderà automaticamente `$( )` le espressioni passate dalla riga di comando. Per questo motivo è consigliabile utilizzare un percorso completo durante l'impostazione di `IpaPackageDir` nella riga di comando.


Vedere il [note sulla versione di iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location) per ulteriori informazioni sul `IpaPackageDir` proprietà.
