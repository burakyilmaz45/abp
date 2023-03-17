##  ABP Uygulama Başlatma

[ ABP CLI ](CLI.md)'nin `abp new` komutunu önceden oluşturulmuş [ başlangıç ​​çözümü şablonlarından ](Başlangıç ​​Şablonları/Dizin ) biriyle [ başlamak ] (Getting-Started.md) için kullanırsınız .md). Bunu yaptığınızda, genellikle ABP Çerçevesinin uygulamanızla nasıl entegre edildiğine veya nasıl yapılandırılıp başlatıldığına ilişkin ayrıntıları bilmeniz gerekmez. Başlangıç ​​şablonu ayrıca temel ABP paketleri ile birlikte gelir ve [ uygulama modülleri ](Modüller/Dizin) sizin için önceden yüklenmiş ve yapılandırılmıştır.

> Her zaman [bir başlangıç ​​şablonuyla çalışmaya başlamanız](Getting-Started.md) ve gereksinimlerinize göre değiştirmeniz önerilir. Bu belgeyi yalnızca ayrıntıları anlamak istiyorsanız veya ABP Çerçevesinin nasıl başladığını değiştirmeniz gerekiyorsa okuyun.
ABP Çerçevesi birçok özelliğe ve entegrasyona sahipken, hafif ve modüler bir çerçeve olarak oluşturulmuştur. [ Yüzlerce NuGet ve NPM paketinden ](https://abp.io/packages) oluşur , böylece yalnızca ihtiyacınız olan özellikleri kullanabilirsiniz. [ Getting Started with an Empty ASP.NET Core MVC / Razor Pages Application ](Getting-Started-AspNetCore-Application.md) belgesini takip ederseniz , ABP Çerçevesini boş bir ASP'ye kurmanın ne kadar kolay olduğunu göreceksiniz. .NET Core projesi sıfırdan. Tek bir NuGet paketi kurmanız ve birkaç küçük değişiklik yapmanız yeterlidir.

Bu belge, ABP Çerçevesinin başlangıçta nasıl başlatıldığını ve yapılandırıldığını daha iyi anlamak isteyenler içindir.

##  Bir Konsol Uygulamasına Yükleme

.NET Konsol uygulaması, minimalist .NET uygulamasıdır. Bu nedenle, ABP Çerçevesinin bir konsol uygulamasına yüklenmesini minimalist bir örnek olarak göstermek en iyisidir.

[ Visual Studio ile yeni bir konsol uygulaması oluşturursanız ] (https://learn.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio) (.NET 7.0 veya üstü için), aşağıdaki çözüm yapısına bakın (çözümü `MyConsoleDemo` olarak adlandırdım ):

![ app-startup-console-initial ](images/app-startup-console-initial.png)

Bu örnek [ üst düzey ifadeleri ](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/top-level-statements) kullanır, dolayısıyla yalnızca tek bir satırdan oluşur. kod.

İlk adım , ABP çerçevesinin en temel NuGet paketi olan [ Volo.Abp.Core ](https://www.nuget.org/packages/Volo.Abp.Core) NuGet paketini kurmaktır . Visual Studio'da [ Paket Yönetici Konsolu ](https://learn.microsoft.com/en-us/nuget/consume-packages/install-use-packages-powershell) kullanarak kurabilirsiniz :

````bash
Yükleme Paketi Volo.Abp.Core
````

Alternatif olarak, projenin kök klasöründe ( bu örnek için 'MyConsoleDemo.csproj' dosyasını içeren klasör) bir komut satırı terminali kullanabilirsiniz:

````bash
dotnet eklenti paketi Volo.Abp.Core
````

NuGet paketini ekledikten sonra uygulamamız için bir root [ module class ](Module-Development-Basics.md) oluşturmalıyız . Projede aşağıdaki sınıfı oluşturabiliriz:

````csharp
 Volo'yu kullanarak . Abp . modülerlik ;
ad alanı  MyConsoleDemo
{
    genel  sınıf  MyConsoleDemoModule : AbpModule
    {
    }
}
````

Bu, "AbpModule" sınıfından türetilen boş bir sınıftır . Uygulamanızın bağımlılıklarını kontrol edeceğiniz, yapılandırma ve başlatma/kapatma mantığınızı uygulayacağınız ana sınıftır. Daha fazla bilgi için lütfen [ Modülerlik ](Module-Development-Basics.md) belgesini kontrol edin.

İkinci ve son adım olarak `Program.cs` dosyasını aşağıdaki kod bloğunda gösterildiği gibi değiştirin:

````csharp
 MyConsoleDemo'yu kullanarak ;
 Volo'yu kullanarak . Abp ;
// 1: ABP uygulama kapsayıcısını oluşturun
 var  application  =  wait  AbpApplicationFactory kullanarak . CreateAsync < MyConsoleDemoModule >();
// 2: ABP Çerçevesini (ve tüm modülleri) başlatın/başlatın
 uygulama bekliyoruz . Async'i Başlat ();
konsol . WriteLine ( " ABP Çerçevesi başlatıldı... " );
// 3: ABP Çerçevesini (ve tüm modülleri) durdurun
 uygulama bekliyoruz . KapatmaAsync ();
````

Bu kadar. Artık ABP Çerçevesi uygulamanıza yüklendi, entegre edildi, başlatıldı ve durduruldu. Şu andan itibaren [ ABP paketlerini ](https://abp.io/packages) ihtiyacınız olduğunda uygulamanıza yükleyebilirsiniz .

##  Bir Çerçeve Paketi Yükleme

.NET uygulamanızdan e-posta göndermek istiyorsanız, .NET'in standart [ SmtpClient sınıfını ](https://learn.microsoft.com/en-us/dotnet/api/system.net.mail.smtpclient) kullanabilirsiniz. ABP ayrıca [ e-posta göndermeyi ](Emailing.md) ve e-posta ayarlarını merkezi bir yerde yapılandırmayı basitleştiren bir "IEmailSender" hizmeti sağlar. Kullanmak istiyorsanız, projenize [ Volo.Abp.Emailing ](https://www.nuget.org/packages/Volo.Abp.Emailing) NuGet paketini kurmalısınız:

````bash
dotnet eklenti paketi Volo.Abp.Emailing
````

Yeni bir ABP paketi/modülü eklediğinizde, modül sınıfınızdan modül bağımlılığını da belirtmeniz gerekir. Bu nedenle, `MyConsoleDemoModule` sınıfını aşağıda gösterildiği gibi değiştirin :

````csharp
 Volo'yu kullanarak . Abp . E-posta ;
 Volo'yu kullanarak . Abp . modülerlik ;
ad alanı  MyConsoleDemo
{
    [ DependsOn ( typeof ( AbpEmailingModule ))] // Modül bağımlılığı eklendi
    genel  sınıf  MyConsoleDemoModule : AbpModule
    {
    }
}
````

ABP E-posta Modülünü ( `AbpEmailingModule` ) kullanmak istediğimi beyan etmek için bir `[DependsOn]` özniteliği ekledim . Artık "Program.cs" dosyamda "IEmailSender" hizmetini kullanabilirim :

````csharp
 Microsoft'u kullanarak . Uzantılar _ Bağımlılık Enjeksiyonu ;
 MyConsoleDemo'yu kullanarak ;
 Volo'yu kullanarak . Abp ;
 Volo'yu kullanarak . Abp . E-posta ;
 var  application  =  wait  AbpApplicationFactory kullanarak . CreateAsync < MyConsoleDemoModule >();
 uygulama bekliyoruz . Async'i Başlat ();
// IEmailSender hizmetini kullanarak e-posta gönderme
var  emailsender  =  uygulama . Hizmet Sağlayıcı GetRequiredService < IEmailSender >();
 e-posta göndericisi bekleniyor . Async Gönder (
    kime : " info@acme.com " ,
    konu : " Merhaba Dünya " ,
    body : " Mesajımın gövdesi... "
);
 uygulama bekliyoruz . KapatmaAsync ();
````

> Bu uygulamayı çalıştırırsanız, e-posta gönderme ayarlarının henüz yapılmadığını belirten bir çalışma zamanı hatası alırsınız. Nasıl yapılandırılacağını öğrenmek için [Email Gönderme belgesini](Emailing.md) kontrol edebilirsiniz.
Bu kadar. Bir ABP NuGet paketi yükleyin, modül bağımlılığını ekleyin ( "[DependsOn]" özniteliğini kullanarak) ve NuGet paketi içindeki herhangi bir hizmeti kullanın.

[ ABP CLI ](CLI.md), bir ABP NuGet'in eklenmesini gerçekleştirmek ve ayrıca tek bir komutla sizin için modül sınıfınıza "[DependsOn]" özniteliğini eklemek için zaten özel bir komuta sahiptir:

````bash
abp eklenti paketi Volo.Abp.Emailing
````

Manuel olarak yapmak yerine `abp add-package` komutunu kullanmanızı öneririz .

##  AbpApplicationFactory

"AbpApplicationFactory", bir ABP uygulama kapsayıcısı oluşturan ana sınıftır. Birden çok aşırı yükleme ile tek bir statik 'CreateAsync' (ve eşzamansız programlamayı kullanamıyorsanız 'Create' ) yöntemi sağlar . Bunları nerede kullanabileceğinizi anlamak için bu aşırı yüklemeleri inceleyelim.

İlk aşırı yükleme, bu belgede daha önce kullandığımız gibi genel bir modül sınıfı parametresi alır:

````csharp
AbpApplicationFactory . CreateAsync < MyConsoleDemoModule >();
````

Genel sınıf parametresi, uygulamanızın kök modül sınıfı olmalıdır. Diğer tüm modüller, o modülün bağımlılıkları olarak çözümlenir.

İkinci aşırı yükleme, modül sınıfını genel parametre yerine bir "Tür" parametresi olarak alır. Böylece, önceki kod bloğu aşağıda gösterildiği gibi yeniden yazılabilir:

````csharp
AbpApplicationFactory . CreateAsync ( typeof ( MyConsoleDemoModule ));
````

Her iki aşırı yük de tamamen aynı şekilde çalışır. Bu nedenle, geliştirme zamanında modül sınıfı türünü bilmiyorsanız ve (bir şekilde) çalışma zamanında hesaplarsanız ikincisini kullanabilirsiniz.    

Yukarıdaki yöntemlerden birini oluşturursanız ABP, [ bağımlılık enjeksiyonu ](Dependency-Injection.md) sistemini dahili olarak kurmak için bir dahili hizmet koleksiyonu ( 'IServiceCollection' ) ve bir dahili hizmet sağlayıcısı ( 'IServiceProvider' ) oluşturur . Bağımlılık enjeksiyon sisteminden 'IEmailSender' hizmetini çözmek için *Bir Çerçeve Paketi Kurma* bölümündeki 'application.ServiceProvider' özelliğini kullandığımıza dikkat edin .

Bir sonraki aşırı yükleme, bağımlılık enjeksiyon sistemini kendiniz kurmanıza veya bağımlılık enjeksiyon sistemini dahili olarak kuran başka bir çerçeveye (ASP.NET Core gibi) entegre etmenize izin vermek için sizden bir " IServiceCollection" parametresi alır.

Bağımlılık enjeksiyon kurulumunu harici olarak yönetmek için "Program.cs" yi aşağıda gösterildiği gibi değiştirebiliriz :

````csharp
 Microsoft'u kullanarak . Uzantılar _ Bağımlılık Enjeksiyonu ;
 MyConsoleDemo'yu kullanarak ;
 Volo'yu kullanarak . Abp ;
// 1: IServiceCollection'ı manuel olarak oluşturdu
IServiceCollection  hizmetleri  =  yeni  ServiceCollection ();
// 2: IServiceCollection'ı harici olarak ABP Çerçevesine geçirin
 var  uygulama  kullanarak =  AbpApplicationFactory'yi bekliyoruz 
    . CreateAsync < MyConsoleDemoModule >( hizmetler );
// 3: IServiceProvider nesnesini el ile oluşturdu
IServiceProvider  serviceProvider  =  hizmetler . BuildServiceProvider ();
// 4: IServiceProvider'ı harici olarak ABP Çerçevesine geçirin
 uygulama bekliyoruz . InitializeAsync ( hizmet Sağlayıcı );
konsol . WriteLine ( " ABP Çerçevesi başlatıldı... " );
 uygulama bekliyoruz . KapatmaAsync ();
````

Bu örnekte, .NET'in standart bağımlılık enjeksiyon kapsayıcısını kullandık. " services.BuildServiceProvider ()" çağrısı standart kapsayıcıyı oluşturur. Ancak ABP, başka bir bağımlılık enjeksiyon kapsayıcısı kullanıyor olsanız bile düzgün çalışan alternatif bir uzatma yöntemi olan 'BuildServiceProviderFromFactory()' sağlar:

````csharp
IServiceProvider  serviceProvider  =  hizmetler . BuildServiceProviderFromFactory ();
````

> [Autofac](https://autofac.org/) bağımlılık enjeksiyon kapsayıcısını ABP Çerçevesi ile nasıl entegre edebileceğinizi öğrenmek istiyorsanız [Autofac Integration](Autofac-Integration.md) belgesini kontrol edebilirsiniz.
Son olarak, "CreateAsync" yöntemi, modül sınıfı adını "Type" parametresi ve "IServiceCollection" nesnesi olarak alan son bir aşırı yüklemeye sahiptir. Böylece, son `CreateAsync` method kullanımını aşağıdaki kod bloğundaki gibi yeniden yazabiliriz :

````csharp
 var  uygulama  kullanarak =  AbpApplicationFactory'yi bekliyoruz 
    . CreateAsync ( typeof ( MyConsoleDemoModule ), hizmetler );
````

> Tüm "CreateAsync" yöntemi aşırı yüklemelerinin "Create" karşılıkları vardır. Uygulama türünüz eşzamansız programlama kullanamıyorsa (bu, "await" anahtar kelimesini kullanamayacağınız anlamına gelir), "CreateAsync" yöntemi yerine "Create" yöntemini kullanabilirsiniz.
###  AbpApplicationCreationOptions

Tüm "CreateAsync" aşırı yüklemeleri , uygulama oluşturmada kullanılan seçenekleri yapılandırmak için isteğe bağlı bir "Action<AbpApplicationCreationOptions>" parametresi alabilir . Aşağıdaki örneğe bakın:

````csharp
 var  uygulama  kullanarak =  AbpApplicationFactory'yi bekliyoruz 
    . CreateAsync < MyConsoleDemoModule >( seçenekler  = >
    {
        seçenekler . UygulamaAdı  = " Uygulamam ";
    });
````

"ApplicationName" seçeneğini yapılandırmak için bir lambda yöntemini geçtik . İşte tüm standart seçeneklerin bir listesi:

*  `ApplicationName` : Uygulama için insanlar tarafından okunabilen bir ad. Bir uygulama için benzersiz bir değerdir.
*  `Yapılandırma` : Barındırma sistemi tarafından sağlanmadığında [ uygulama yapılandırmasını ](Configuration.md) ayarlamak için kullanılabilir . ASP.NET Core ve diğer .NET tarafından barındırılan uygulamalar için gerekli değildir. Ancak, dahili bir hizmet sağlayıcıyla "AbpApplicationFactory" kullandıysanız , uygulama yapılandırmasının nasıl oluşturulacağını yapılandırmak için bu seçeneği kullanabilirsiniz.
*  `Environment` : Uygulama için ortam adı.
*  `PlugInSources` : Eklenti kaynaklarının listesi. Eklentilerle nasıl çalışılacağını öğrenmek için [ Eklenti Modülleri belgelerine ](PlugIn-Modules) bakın.
*  `Services` : Hizmet bağımlılıklarını kaydetmek için kullanılabilecek `IServiceCollection` nesnesi. Hizmetlerinizi [ modül sınıfı ](Module-Development-Basics.md) içinde yapılandırdığınız için genellikle buna ihtiyacınız olmaz. Ancak, AbpApplicationCreationOptions sınıfı için uzantı yöntemleri yazılırken kullanılabilir .

####  UygulamaAdı seçeneği

Yukarıda tanımlandığı gibi, 'UygulamaAdı' seçeneği, uygulama için insanlar tarafından okunabilen bir addır. Bir uygulama için benzersiz bir değerdir.

"ApplicationName", uygulamayı ayırt etmek için ABP Çerçevesi tarafından birkaç yerde kullanılır. Örneğin, [ denetim günlüğü ](Audit-Logging.md) sistemi, ilgili uygulama tarafından yazılan her denetim günlüğü kaydında "UygulamaAdı" nı kaydeder , böylece denetim günlüğü girişini hangi uygulamanın oluşturduğunu anlayabilirsiniz. Bu nedenle, sisteminiz denetim günlüklerini tek bir noktaya kaydeden birden fazla uygulamadan (bir mikro hizmet çözümü gibi) oluşuyorsa, her uygulamanın farklı bir `ApplicationName` olduğundan emin olmalısınız .

"ApplicationName" özelliğinin değeri, varsayılan olarak **giriş derlemesinin adından** (genellikle bir .NET çözümündeki proje adı) otomatik olarak ayarlanır ; bu, çoğu durumda uygundur, çünkü her uygulamanın tipik olarak benzersiz bir giriş derlemesi adı vardır .

Uygulama adını farklı bir değere ayarlamanın iki yolu vardır. Bu ilk yaklaşımda, uygulamanızın [ yapılandırma ](Configuration.md) içinde "ApplicationName" özelliğini ayarlayabilirsiniz . En kolay yol, "appsettings.json" dosyanıza bir "ApplicationName" alanı eklemektir :

````json
{
    "ApplicationName" : " Services.Ordering "
}
````

Alternatif olarak, ABP uygulamasını oluştururken `AbpApplicationCreationOptions.ApplicationName` öğesini ayarlayabilirsiniz . Çözümünüzde (genellikle "Program.cs" dosyasında) "AddApplication" veya "AddApplicationAsync" çağrısını bulabilir ve "ApplicationName" seçeneğini aşağıda gösterildiği gibi ayarlayabilirsiniz:

````csharp
 inşaatçıyı bekliyor . AddApplicationAsync < OrderingServiceHttpApiHostModule >( seçenekler  =>
{
    seçenekler . ApplicationName  =  " Services.Ordering " ;
});
````

####  IApplicationInfoAccessor

Çözümünüzde daha sonra "ApplicationName" öğesine erişmeniz gerekirse , "IApplicationInfoAccessor" hizmetini enjekte edebilir ve değeri onun "ApplicationName" özelliğinden alabilirsiniz.

"IApplicationInfoAccessor" , uygulamanız başladığında oluşturulan rastgele bir GUID değeri olan "InstanceId" değerini de sağlar . Uygulama örneklerini birbirinden ayırmak için bu değeri kullanabilirsiniz.

##  IAbpUygulaması

"AbpApplicationFactory", "CreateAsync" (veya "Create" ) yönteminden bir "IAbpApplication" nesnesi döndürür . "IAbpApplication", bir ABP uygulaması için ana kapsayıcıdır. Aynı zamanda [ Dependency Injection ](Dependency-Injection.md) sistemine kayıtlı olduğundan , özelliklerini ve yöntemlerini kullanmak için hizmetlerinize 'IAbpApplication' enjekte edebilirsiniz .

İşte bilmek isteyebileceğiniz "IAbpApplication" özelliklerinin bir listesi:

*  `StartupModuleType` : Uygulama kapsayıcısı oluşturulurken kullanılan uygulamanın kök modülünü alır ( `AbpApplicationFactory.CreateAsync` yönteminde).
*  "Hizmetler" : Tüm hizmet kayıtlarının listesi ( "IServiceCollection" nesnesi). Uygulama başlatıldıktan sonra bu koleksiyona yeni hizmetler ekleyemezsiniz (aslında ekleyebilirsiniz, ancak bunun herhangi bir etkisi olmaz).
*  `ServiceProvider` : Uygulama tarafından kullanılan kök servis sağlayıcıya bir referans. Bu, uygulama başlatılmadan önce kullanılamaz. Tekil olmayan hizmetleri bu "IServiceProvider" nesnesinden çözmeniz gerekirse , her zaman yeni bir hizmet kapsamı oluşturun ve kullanımdan sonra atın. Aksi takdirde, uygulamanızda bellek sızıntısı sorunları olacaktır. Hizmet kapsamları hakkında daha fazla bilgi için [ bağımlılık enjeksiyonu ](Dependency-Injection.md) belgesinin *Hizmetleri Serbest Bırakma/Elden Çıkarma* bölümüne bakın .
*  `Modüller` : Geçerli uygulamaya yüklenen tüm modüllerin salt okunur listesi. Alternatif olarak, uygulama kodunuzdaki modül listesine erişmeniz gerekiyorsa `IModuleContainer` hizmetini enjekte edebilirsiniz .

"IAbpApplication" arabirimi , "IApplicationInfoAccessor" arabirimini genişletir , böylece "ApplicationName" ve "InstanceId" değerlerini buradan alabilirsiniz . Ancak, yalnızca bu özelliklere erişmeniz gerekiyorsa, bunun yerine 'IApplicationInfoAccessor' hizmetini enjekte edin ve kullanın.

`IAbpApplication` tek kullanımlıktır. Uygulamanızdan çıkmadan önce daima atın.

##  IAbpHostEnvironment

Bazen bir uygulama oluştururken mevcut barındırma ortamını almamız ve ona göre hareket etmemiz gerekir. Bu gibi durumlarda [ IWebHostEnvironment ](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.hosting.iwebhostenvironment?view=aspnetcore-7.0) veya [ IWebAssemblyHostEnvironment gibi bazı hizmetleri kullanabiliriz. ](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.webassembly.hosting.iwebassemblyhostenvironment) son uygulamada .NET tarafından sağlanmıştır.

Ancak, bu hizmetleri son uygulama tarafından kullanılan bir sınıf kitaplığında kullanamayız. ABP Çerçevesi , istediğiniz zaman geçerli ortam adını almanızı sağlayan "IAbpHostEnvironment" hizmetini sağlar. "IAbpHostEnvironment", ortam tarafından belirli eylemleri gerçekleştirmek için ABP Çerçevesi tarafından çeşitli yerlerde kullanılır. Örneğin ABP Çerçevesi, bazı hizmetler için **Geliştirme** ortamındaki önbellek süresini azaltır .

"IAbpHostEnvironment", geçerli ortam adını aşağıdaki sırayla alır:

1. `AbpApplicationCreationOptions` içinde belirtilmişse, ortam adını alır ve ayarlar .
2. Ortam adı "AbpApplicationCreationOptions" içinde belirtilmemişse, ASP.NET Core & Blazor WASM uygulamaları için "IWebHostEnvironment " veya "IWebAssemblyHostEnvironment" hizmetlerinden ortam adını almaya çalışır .
3. Ortam adı belirtilmemişse veya hizmetlerden alınamıyorsa, ortam adını **Üretim** olarak ayarlar .

ABP uygulamasını oluştururken "AbpApplicationCreationOptions" [ options class ](Options.md) öğesini yapılandırabilir ve "Environment" özelliğine bir ortam adı atayabilirsiniz. Çözümünüzde (genellikle "Program.cs" dosyasında) "AddApplication" veya "AddApplicationAsync" çağrısını bulabilir ve "Ortam" seçeneğini aşağıda gösterildiği gibi ayarlayabilirsiniz:

```csharp
 inşaatçıyı bekliyor . AddApplicationAsync < OrderingServiceHttpApiHostModule >( seçenekler  =>
{
    seçenekler . Ortam  =  Ortamlar . sahneleme ; // veya doğrudan "Hazırlama" olarak ayarlayın
});
```

Ardından, mevcut ortam adını almanız veya ortamı kontrol etmeniz gerektiğinde, "IAbpHostEnvironment" arayüzünü kullanabilirsiniz :

```csharp
genel  sınıf  MyDemoService
{
    özel  salt okunur  IAbpHostEnvironment  _abpHostEnvironment ;
    
    genel  MyDemoService ( IAbpHostEnvironment  abpHostEnvironment )
    {
        _abpHostEnvironment  =  abpHostEnvironment ;
    }
    genel  geçersiz  MyMethod ()
    {
        var  ortamAdı  =  _abpHostEnvironment . OrtamAdı ;
        if ( _abpHostEnvironment . IsDevelopment ()) { /* ... */ }
        if ( _abpHostEnvironment . IsStaging ()) { /* ... */ }
        if ( _abpHostEnvironment . IsProduction ()) { /* ... */ }
        if ( _abpHostEnvironment . IsEnvironment ( " özel ortam " )) { /* ... */ }
    }
}
```

##  .NET Genel Ana Bilgisayar ve ASP.NET Çekirdek Entegrasyonları

`AbpApplicationFactory`, herhangi bir dış bağımlılık olmadan bağımsız bir ABP uygulama kapsayıcısı oluşturabilir. Ancak çoğu durumda [ .NET'in genel ana bilgisayarı ](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host) veya ASP.NET Core ile entegre etmek isteyeceksiniz . Bu tür kullanımlar için ABP, bu sistemlere iyi entegre edilmiş bir ABP uygulama kapsayıcısını kolayca oluşturmak için yerleşik genişletme yöntemleri sağlar.

[ Getting Started with an Empty ASP.NET Core MVC / Razor Pages Application ](Getting-Started-AspNetCore-Application.md) belgesi, bir ASP.NET Core uygulamasında nasıl bir ABP uygulama kapsayıcısı oluşturabileceğinizi açık bir şekilde açıklar.

Ayrıca .NET Generic Host ile nasıl entegre edildiğini görmek için [ bir konsol uygulaması oluşturabilirsiniz ](Başlangıç ​​Şablonları/Konsol).

> Çoğu zaman, ABP CLI'nin "yeni" komutunu kullanarak doğrudan ABP uygulamaları oluşturacaksınız. Bu nedenle, bu entegrasyon ayrıntılarını önemsemenize gerek yok.
##  Ayrıca Bakın

* [ Bağımlılık enjeksiyonu ](Dependency-Injection.md)
* [ Modülerlik ](Module-Development-Basics.md)