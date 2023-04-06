# **Creating a document project with the ABP docs module**

First in CMD

```command
dotnet tool install -g Volo.Abp.Cli
```
We are installing the CLI using the command. After that

```command
abp new Projeİsmi -dbms PostgreSQL –csf
```

We can create an MVC project using PostgreSQL database with code.

* * *

In appsettings.json in DbMigrator and Web layers, we change ConnectionStrings according to the information of our own database.

```JSON
{
  
  "ConnectionStrings": {
    "Default": "Server=localhost;Port=5432;Database=DocsPostgreSql;User ID=postgres;Password=123456;"
  }
}
```

* * *

We open CMD in the project folder. We include VoloDocs packages in the project with “abp add-module Volo.Docs”. If you want, you can add this process manually by searching Volo.Docs from NuGet packages.

```command
abp add-module Volo.Docs
```

* * *

We add the DocsModule requirements to the Module class in the Domain, EntityFrameworkCore, Application and Web layers.

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

In the web layer, we add the “@abp/docs”: “^7.0.3” dependency into package.json.

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

We perform the installation process by opening the command line terminal in the web layer and typing “abp install-libs”.

```command
abp install-libs
```

* * *

We add the Docs configuration to the DbContext class in the EntityFramework layer.

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

ConfigureDocs allows adding two different tables in our database. These tables are DocsDocuments and DocsDocumentContributors. The DocsDocuments table holds the information and contents of the documents kept in the GitHub repository. DocsDocumentContributors, on the other hand, keeps the information of the person committing these documents.

* * *

Open the Package manager console and add the migration. After completing the migration process, you can update the database.
```command
add-migration "Initial"
```

* * *

```command
update-database
```
