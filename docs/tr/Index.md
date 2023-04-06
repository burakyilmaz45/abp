# **ABP docs module ile bir doküman projesi oluşturma**

İlk olarak CMD’ de

```command
dotnet tool install -g Volo.Abp.Cli
```

komutunu kullanarak CLI kurulumu yapıyoruz. Sonrasında

```command
abp new Projeİsmi -dbms PostgreSQL –csf
```

koduyla PostgreSQL veri tabanı kullanan bir MVC projesi oluşturabiliriz.

* * *

DbMigrator ve Web katmanlarında bulunan appsettings.json’ da ConnectionStrings’ i kendi veritabanımızın bilgilerine göre değiştiriyoruz.

```JSON
{
  
  "ConnectionStrings": {
    "Default": "Server=localhost;Port=5432;Database=DocsPostgreSql;User ID=postgres;Password=123456;"
  }
}
```

* * *

Proje klasörü içinde CMD’ yi açıyoruz. “abp add-module Volo.Docs”’ ile VoloDocs paketlerini projeye dahil ediyoruz. Eğer isterseniz bu işlemi NuGet paketlerinden Volo.Docs olarak aratıp manuel olarak ekleyebilirsiniz.

```command
abp add-module Volo.Docs
```

* * *

Domain, EntityFrameworkCore, Application ve Web katmalarında bulunan Module sınıfına DocsModule gerekliliklerini ekliyoruz.

```C#
[DependsOn(
    typeof(DocsDomainModule),// eklenen gereklilik
    typeof(VoloDocsPostgreDomainSharedModule),
    typeof(AbpAuditLoggingDomainModule),
    typeof(AbpBackgroundJobsDomainModule),
    typeof(AbpFeatureManagementDomainModule),
    typeof(AbpIdentityDomainModule),
    typeof(AbpOpenIddictDomainModule),
    typeof(AbpPermissionManagementDomainOpenIddictModule),
    typeof(AbpPermissionManagementDomainIdentityModule),
    typeof(AbpSettingManagementDomainModule),
    typeof(AbpTenantManagementDomainModule),
    typeof(AbpEmailingModule)
)]
    public class VoloDocsPostgreDomainModule : AbpModule
{

}
```

* * *

```C#
[DependsOn(
    typeof(DocsEntityFrameworkCoreModule),//eklenen gereklilik
    typeof(VoloDocsPostgreDomainModule),
    typeof(AbpIdentityEntityFrameworkCoreModule),
    typeof(AbpOpenIddictEntityFrameworkCoreModule),
    typeof(AbpPermissionManagementEntityFrameworkCoreModule),
    typeof(AbpSettingManagementEntityFrameworkCoreModule),
    typeof(AbpEntityFrameworkCorePostgreSqlModule),
    typeof(AbpBackgroundJobsEntityFrameworkCoreModule),
    typeof(AbpAuditLoggingEntityFrameworkCoreModule),
    typeof(AbpTenantManagementEntityFrameworkCoreModule),
    typeof(AbpFeatureManagementEntityFrameworkCoreModule)
    )]

    public class VoloDocsPostgreEntityFrameworkCoreModule : AbpModule
{

}
```

* * *

```C#
[DependsOn(
    typeof(DocsApplicationModule),//eklenen gereklilik
    typeof(VoloDocsPostgreDomainModule),
    typeof(AbpAccountApplicationModule),
    typeof(VoloDocsPostgreApplicationContractsModule),
    typeof(AbpIdentityApplicationModule),
    typeof(AbpPermissionManagementApplicationModule),
    typeof(AbpTenantManagementApplicationModule),
    typeof(AbpFeatureManagementApplicationModule),
    typeof(AbpSettingManagementApplicationModule)
    )]

    public class VoloDocsPostgreApplicationModule : AbpModule
{

}
```

* * *

```C#
[DependsOn(
    typeof(DocsWebModule),//eklenen gereklilik
    typeof(VoloDocsPostgreHttpApiModule),
    typeof(VoloDocsPostgreApplicationModule),
    typeof(VoloDocsPostgreEntityFrameworkCoreModule),
    typeof(AbpAutofacModule),
    typeof(AbpIdentityWebModule),
    typeof(AbpSettingManagementWebModule),
    typeof(AbpAccountWebOpenIddictModule),
    typeof(AbpAspNetCoreMvcUiLeptonXLiteThemeModule),
    typeof(AbpTenantManagementWebModule),
    typeof(AbpAspNetCoreSerilogModule),
    typeof(AbpSwashbuckleModule)
    )]

    public class VoloDocsPostgreWebModule : AbpModule
{

}
```

* * *

Web katmanında package.json içine “@abp/docs": "^7.0.3” bağımlılığının eklemesini yapıyoruz.

```JSON
{
  "version": "1.0.0",
  "name": "my-app",
  "private": true,
  "dependencies": {
    "@abp/aspnetcore.mvc.ui.theme.leptonxlite": "~2.0.3",
    "@abp/docs": "^7.0.3"
  }


}
```

* * *

Web katmanında komut satırı terminalini açıp, “abp install-libs” yazarak yükleme işlemini gerçekleştiriyoruz.

```command
abp install-libs
```

* * *

EntitiyFramework katmanında DbContext sınıfına Docs yapılandırmasını ekliyoruz.

```C#
protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        /* Include modules to your migration db context */

        builder.ConfigurePermissionManagement();
        builder.ConfigureSettingManagement();
        builder.ConfigureBackgroundJobs();
        builder.ConfigureAuditLogging();
        builder.ConfigureIdentity();
        builder.ConfigureOpenIddict();
        builder.ConfigureFeatureManagement();
        builder.ConfigureTenantManagement();
        builder.ConfigureDocs();//Eklenen yapılandırma


    }
```

ConfigureDocs veri tabanımızda iki farklı tablo eklenmesini sağlar. Bu tablolar DocsDocuments ve DocsDocumentContributors. DocsDocuments tablosu GitHub reposunda tutulan dokümanların bilgilerini ve içeriklerini tutuyor. DocsDocumentContributors ise bu dokümanlara commit işlemi yapan kişinin bilgilerini tutuyor.

* * *

Package manager console’ u açın ve migration ekleme işlemini gerçekleştirin. Migration ekleme işlemini tamamladıktan sonra veri tabanı güncelleme işlemini yapabilirsiniz.

```command
add-migration "Initial"
```

* * *

```command
update-database
```
