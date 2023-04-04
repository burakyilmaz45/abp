## STRING ENCRYPTION V2

First of all, since the StringEncryption module in the Volo.Abp.Security package is faulty, we take the codes of this module from the ABP GitHup repository and create it ourselves.

For this, we create three classes named **AbpStringEncryptionOptions, IStringEncryptionService** and **StringEncryptionService** in the Domain layer.

### AbpStringEncryptionOptions

* * *

```csharp
using System;
using System.Collections.Generic;
using System.Text;

namespace Serender.Definitions.CustomerMasters
{
    public class AbpStringEncryptionOptions
    {
        public int Keysize { get; set; }
        public string DefaultPassPhrase { get; set; }
        public byte[] InitVectorBytes { get; set; }
        public byte[] DefaultSalt { get; set; }

        public AbpStringEncryptionOptions()
        {
            Keysize = 256;
            DefaultPassPhrase = "gsKnGZ041HLL4IM8";
            InitVectorBytes = Encoding.ASCII.GetBytes("jkE49230Tf093b42");
            DefaultSalt = Encoding.ASCII.GetBytes("hgt!16kl");
        }
    }
}
```

* * *

### IStringEncryptionService

* * *

```csharp
using JetBrains.Annotations;
using System;
using System.Collections.Generic;
using System.Text;

namespace Serender.Definitions.CustomerMasters
{
    public interface IStringEncryptionService
    {
        [CanBeNull]
        string Encrypt([CanBeNull] string plainText, string passPhrase = null, byte[] salt = null);

        [CanBeNull]
        string Decrypt([CanBeNull] string cipherText, string passPhrase = null, byte[] salt = null);
    }
}
```

* * *

### StringEncryptionService

* * *

```csharp
using Microsoft.Extensions.Options;
using System;
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography;
using System.Text;
using Volo.Abp.DependencyInjection;

namespace Serender.Definitions.CustomerMasters
{
    public class StringEncryptionService : IStringEncryptionService, ITransientDependency
    {
        protected AbpStringEncryptionOptions Options { get; }

        public StringEncryptionService(IOptions<AbpStringEncryptionOptions> options)
        {
            Options = options.Value;
        }

        public virtual string Encrypt(string plainText, string passPhrase = null, byte[] salt = null)
        {
            if (plainText == null)
            {
                return null;
            }

            if (passPhrase == null)
            {
                passPhrase = Options.DefaultPassPhrase;
            }

            if (salt == null)
            {
                salt = Options.DefaultSalt;
            }

            var plainTextBytes = Encoding.UTF8.GetBytes(plainText);
            using (var password = new Rfc2898DeriveBytes(passPhrase, salt))
            {
                var keyBytes = password.GetBytes(Options.Keysize / 8);
                using (var symmetricKey = Aes.Create())
                {
                    symmetricKey.Mode = CipherMode.CBC;
               using (var encryptor = symmetricKey.CreateEncryptor(keyBytes, Options.InitVectorBytes))
                    {
                        using (var memoryStream = new MemoryStream())
                        {
          using (var cryptoStream = new CryptoStream(memoryStream, encryptor, CryptoStreamMode.Write))
                            {
                                cryptoStream.Write(plainTextBytes, 0, plainTextBytes.Length);
                                cryptoStream.FlushFinalBlock();
                                var cipherTextBytes = memoryStream.ToArray();
                                return Convert.ToBase64String(cipherTextBytes);
                            }
                        }
                    }
                }
            }
        }


        public virtual string Decrypt(string cipherText, string passPhrase = null, byte[] salt = null)
        {
            if (string.IsNullOrEmpty(cipherText))
            {
                return null;
            }

            if (passPhrase == null)
            {
                passPhrase = Options.DefaultPassPhrase;
            }

            if (salt == null)
            {
                salt = Options.DefaultSalt;
            }

            var cipherTextBytes = Convert.FromBase64String(cipherText);
            using (var password = new Rfc2898DeriveBytes(passPhrase, salt))
            {
                var keyBytes = password.GetBytes(Options.Keysize / 8);
                using (var symmetricKey = Aes.Create())
                {
                    symmetricKey.Mode = CipherMode.CBC;
               using (var decryptor = symmetricKey.CreateDecryptor(keyBytes, Options.InitVectorBytes))
                    {
                        using (var memoryStream = new MemoryStream(cipherTextBytes))
                        {
           using (var cryptoStream = new CryptoStream(memoryStream, decryptor, CryptoStreamMode.Read))
                            {
                                var plainTextBytes = new byte[cipherTextBytes.Length];
                                var totalReadCount = 0;
                                while (totalReadCount < cipherTextBytes.Length)
                                {
                                    var buffer = new byte[cipherTextBytes.Length];
                                    var readCount = cryptoStream.Read(buffer, 0, buffer.Length);
                                    if (readCount == 0)
                                    {
                                        break;
                                    }

                                    for (var i = 0; i < readCount; i++)
                                    {
                                        plainTextBytes[i + totalReadCount] = buffer[i];
                                    }

                                    totalReadCount += readCount;
                                }

                                return Encoding.UTF8.GetString(plainTextBytes, 0, totalReadCount);
                            }
                        }
                    }
                }
            }
        }
    }
}
```
* * *

- · After completing the StringEncryption module, we create a folder called Attributes in the Domain.Shared layer.
- · We create a class named **EncryptAttribute** in the Attributes folder.

### EncryptAttribute

* * *
```csharp
using System;
using System.Collections.Generic;
using System.Text;

namespace Serender.Definitions.Attributes
{
    public class EncryptAttribute : System.Attribute
    {

        public EncryptAttribute()
        {

        }
    }
}
```
* * *

- We use this attribute for the field that we want to encrypt in the CustomerMaster class.

* * *
```csharp
[Encrypt]
public string Adress { get; set; }
```
* * *

- · For automatic encryption, we add a new DTO named **CustomerMasterEncryptDto** to the CustomerMaster folder in the Application.Contracts layer.
    
- · As in CustomerMaster.cs in the domain layer, we use the EncryptAttribute for the field we want to encrypt.
    
* * *
```csharp
using Serender.Definitions.Attributes;
using System;
using System.Collections.Generic;
using System.Text;
using Volo.Abp.Application.Dtos;

namespace Serender.Definitions.CustomerMasters
{
    public class CustomerMasterEncryptDto : EntityDto<Guid>
    {
        public Guid CustomerTypeId { get; set; }
        public string Code { get; set; }
        public string Name { get; set; }
        public Guid CustomerGroupId { get; set; }
        [Encrypt]
        public string Adress { get; set; }
        public string PostCode { get; set; }
        public string Telephone { get; set; }
        public string Mail { get; set; }
        public string CommercialName { get; set; }
        public string TaxOffice { get; set; }
        public string TaxidNumber { get; set; }
        public Guid ChartofAccountsId { get; set; }
    }
}
```
* * *

- · We create a new Mapper class named **CustomerMasterEnctyptDtoMapper** in order to use the automatic encryption process in the Create method.
- · In order to use automatic decryption in Get and GetList methods, we create a new Mapper class named **CustomCustomerMasterMapper**.
- · We create a new Mapper class named **CustomerMasterUpdateDtoMapper** in order to use automatic encryption in the Update method.
- · In these Mapper classes, we take the properties using the System.Reflection library and move the values from the source to the destination.

### CustomerMasterEnctyptDtoMapper

* * *
```csharp
using Serender.Definitions.Attributes;
using Serender.Definitions.CustomerMasters;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Volo.Abp.DependencyInjection;
using Volo.Abp.ObjectMapping;

namespace Serender.Definitions
{
    public class CustomerMasterEnctyptDtoMapper : IObjectMapper<CreateCustomerMasterDto, CustomerMasterEncryptDto>, ITransientDependency
    {
        protected IStringEncryptionService _stringEncryptionService { get; }

        public CustomerMasterEnctyptDtoMapper(IStringEncryptionService stringEncryptionService)
        {
            _stringEncryptionService = stringEncryptionService;
        }
        public CustomerMasterEncryptDto Map(CreateCustomerMasterDto source)
        {
            var deneme = Map(source, new CustomerMasterEncryptDto());
            return deneme;
        }

        public CustomerMasterEncryptDto Map(CreateCustomerMasterDto source, CustomerMasterEncryptDto destination)
        {

            destination.GetType().GetProperties().ToList().ForEach(dp =>
            {
                if (source.GetType().GetProperties().Any(x => x.Name == dp.Name))
                {
                    if (destination.GetType().GetProperty(dp.Name).GetCustomAttributes(true).Where(sp => sp.GetType() == typeof(EncryptAttribute)).Any())
                    {
                        try
                        {
                            dp.SetValue(destination, _stringEncryptionService.Encrypt(source.GetType().GetProperty(dp.Name).GetValue(source).ToString()));
                        }
                        catch
                        {
                            dp.SetValue(destination, source.GetType().GetProperty(dp.Name).GetValue(source));
                        }
                    }
                    else
                    {
                        dp.SetValue(destination, source.GetType().GetProperty(dp.Name).GetValue(source));
                    }
                }
            }
            );
            return destination;
        }
    }
}
```
* * *

### CustomCustomerMasterMapper

* * *
```csharp
using Serender.Definitions.Attributes;
using Serender.Definitions.CustomerMasters;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Volo.Abp.DependencyInjection;
using Volo.Abp.ObjectMapping;

namespace Serender.Definitions
{
    public class CustomCustomerMasterMapper : IObjectMapper<CustomerMaster, CustomerMasterDto>, ITransientDependency
    {
        protected IStringEncryptionService _stringEncryptionService { get; }

        public CustomCustomerMasterMapper(IStringEncryptionService stringEncryptionService)
        {

            _stringEncryptionService = stringEncryptionService;
        }


        public CustomerMasterDto Map(CustomerMaster source)
        {
            var deneme = Map(source, new CustomerMasterDto());
            return deneme;
        }

        public CustomerMasterDto Map(CustomerMaster source, CustomerMasterDto destination)
        {
            destination.GetType().GetProperties().ToList().ForEach(dp =>
            {
                if (source.GetType().GetProperty(dp.Name).GetCustomAttributes(true).Where(sp => sp.GetType() == typeof(EncryptAttribute)).Count() > 0)
                {
                    try
                    {
                        dp.SetValue(destination, _stringEncryptionService.Decrypt(source.GetType().GetProperty(dp.Name).GetValue(source).ToString()));
                    }
                    catch
                    {
                        dp.SetValue(destination, source.GetType().GetProperty(dp.Name).GetValue(source));
                    }
                }
                else
                {
                    dp.SetValue(destination, source.GetType().GetProperty(dp.Name).GetValue(source));
                }
            }
            );

            return destination;

        }
    }
}
```
* * *

### CustomerMasterUpdateDtoMapper

* * *
```csharp
using AutoMapper;
using Serender.Definitions.CustomerMasters;
using Volo.Abp.DependencyInjection;
using Volo.Abp.ObjectMapping;
using System.Linq;
using System.Reflection;
using System;
using System.Collections.Generic;
using Serender.Definitions.Attributes;

namespace Serender.Definitions
{
    public class CustomerMasterUpdateDtoMapper : IObjectMapper<UpdateCustomerMasterDto, CustomerMasterEncryptDto>, ITransientDependency
    {
        protected IStringEncryptionService _stringEncryptionService { get; }

        public CustomerMasterUpdateDtoMapper(IStringEncryptionService stringEncryptionService)
        {
            _stringEncryptionService = stringEncryptionService;
        }
        public CustomerMasterEncryptDto Map(UpdateCustomerMasterDto source)
        {
            var deneme = Map(source, new CustomerMasterEncryptDto());
            return deneme;
        }

        public CustomerMasterEncryptDto Map(UpdateCustomerMasterDto source, CustomerMasterEncryptDto destination)
        {

            destination.GetType().GetProperties().ToList().ForEach(dp =>
            {
                if (source.GetType().GetProperties().Any(x => x.Name == dp.Name))
                {
                    if (destination.GetType().GetProperty(dp.Name).GetCustomAttributes(true).Where(sp => sp.GetType() == typeof(EncryptAttribute)).Any())
                    {
                        try
                        {
                            dp.SetValue(destination, _stringEncryptionService.Encrypt(source.GetType().GetProperty(dp.Name).GetValue(source).ToString()));
                        }
                        catch
                        {
                            dp.SetValue(destination, source.GetType().GetProperty(dp.Name).GetValue(source));
                        }
                    }
                    else
                    {
                        dp.SetValue(destination, source.GetType().GetProperty(dp.Name).GetValue(source));
                    }
                }
            }
            );
            return destination;
        }
    }
}
```
* * *

### CHANGES THAT MUST BE MADE IN THE CUSTOMER MASTER CLASS IN THE APPLICATION LAYER

- First, we add the StringEncryption and ObjectMapper interfaces to the CustomerMasterAppService class in the Application layer.

* * *
```csharp
public class CustomerMasterAppService : DefinitionsAppService, ICustomerMasterAppService
    { 
  //…
  //…
        private readonly IObjectMapper<DefinitionsApplicationModule> _objectMapper;
        protected IStringEncryptionService _stringEncryptionService { get; }
    public CustomerMasterAppService(
            IStringEncryptionService stringEncryptionService,
            IObjectMapper<DefinitionsApplicationModule> objectMapper)
        {
             //…
//…
            _stringEncryptionService = stringEncryptionService;
            _objectMapper = objectMapper;
        }
}
```
* * *

### CreateAsync Method

· We send the input values from the user to the Mapper class we have created.

· We assign the values returned from Mapper to a value called EnctyptDto.

· We draw the input values required for CreateAsync from the EnctyptDto variable.

**GetAsync and GetListAsync method**

- The change we need to make in these two methods is to use the mapper we created ourselves in the return operation.
* * *
```csharp
public async Task<CustomerMasterDto> GetAsync(Guid id)
        {
            var queryable = await _customerMasterRepository.GetQueryableAsync();
            var query = from customerMaster in queryable
                        join customerType in await _customerTypeRepository.GetQueryableAsync() on customerMaster.CustomerTypeId equals customerType.Id
                        join customerGroup in await _customerGroupRepository.GetQueryableAsync() on customerMaster.CustomerGroupId equals customerGroup.Id
                        join accountChart in await _accountChartRepository.GetQueryableAsync() on customerMaster.ChartofAccountsId equals accountChart.Id
                        where customerMaster.Id == id
                        select new { customerMaster, customerType, customerGroup, accountChart };
            var queryResult = await AsyncExecuter.FirstOrDefaultAsync(query);
            if (queryResult == null)
            {
                throw new EntityNotFoundException(typeof(CustomerMaster), id);
            }
       // return ObjectMapper.Map<CustomerMaster, CustomerMasterDto>(queryResult.customerMaster);

            return _objectMapper.Map<CustomerMaster, CustomerMasterDto>(queryResult.customerMaster); 
        }
public async Task<PagedResultDto<CustomerMasterDto>> GetListAsync(GetCustomerMasterListDto input)
        {
            var queryable = await _customerMasterRepository.GetQueryableAsync();

            var query = from customerMaster in queryable
                        join customerType in await _customerTypeRepository.GetQueryableAsync() on customerMaster.CustomerTypeId equals customerType.Id
                        join customerGroup in await _customerGroupRepository.GetQueryableAsync() on customerMaster.CustomerGroupId equals customerGroup.Id
                        join accountChart in await _accountChartRepository.GetQueryableAsync() on customerMaster.ChartofAccountsId equals accountChart.Id
                        select new { customerMaster, customerType, customerGroup, accountChart };

            query = query
                .OrderBy(NormalizeSorting(input.Sorting))
                .Skip(input.SkipCount)
                .Take(input.MaxResultCount);

            var queryResult = await AsyncExecuter.ToListAsync(query);

            var customerMasterDtos = queryResult.Select(x =>
            {
        // return ObjectMapper.Map<CustomerMaster, CustomerMasterDto>(x.customerMaster);
             return _objectMapper.Map<CustomerMaster, CustomerMasterDto>(x.customerMaster);
            }).ToList();

            var totalCount = await _customerMasterRepository.GetCountAsync();

            return new PagedResultDto<CustomerMasterDto>(
                totalCount,
                customerMasterDtos
            );
        }
```
* * *

### UpdateAsync method

· Since we need a return, use the **UpdateAsync** method to Task&lt;TResult&gt; transforming it into structure.

· We perform the same operation in the ICustomerMasterAppService class in the Application.Contracts layer and in the CustomerMasterController class in the HttpApi layer.

· As we did in the CreateAsync method, we send the data from the user to the Mapper we created and assign it to a variable.

· We perform the data required for the update process with the values returned from the mapper process instead of the input from the user.

* * *
```csharp
public async Task <CustomerMasterDto> UpdateAsync(Guid id, UpdateCustomerMasterDto input)
        {
            var customerMaster = await _customerMasterRepository.GetAsync(id);
            var EnctyptDto = _objectMapper.Map<UpdateCustomerMasterDto, CustomerMasterEncryptDto>(input);
            if (customerMaster.Code != EnctyptDto.Code || customerMaster.Name != EnctyptDto.Name || customerMaster.CustomerTypeId != EnctyptDto.CustomerTypeId
                || customerMaster.CustomerGroupId != EnctyptDto.CustomerGroupId || customerMaster.Adress != EnctyptDto.Adress || customerMaster.PostCode != EnctyptDto.PostCode
                || customerMaster.Telephone != EnctyptDto.Telephone || customerMaster.Mail != EnctyptDto.Mail || customerMaster.CommercialName != EnctyptDto.CommercialName
                || customerMaster.TaxOffice != EnctyptDto.TaxOffice || customerMaster.TaxidNumber != EnctyptDto.TaxidNumber 
                || customerMaster.ChartofAccountsId != EnctyptDto.ChartofAccountsId)
            {
                await _customerMasterManager.ChangeCodeAsync(customerMaster, EnctyptDto.Code, EnctyptDto.Name, EnctyptDto.CustomerTypeId, EnctyptDto.CustomerGroupId,
                    EnctyptDto.Adress, EnctyptDto.PostCode, EnctyptDto.Telephone, EnctyptDto.Mail, EnctyptDto.CommercialName, EnctyptDto.TaxOffice, EnctyptDto.TaxidNumber,
                    EnctyptDto.ChartofAccountsId);
            }

            await _customerMasterRepository.UpdateAsync(customerMaster);
            return _objectMapper.Map<CustomerMaster, CustomerMasterDto>(customerMaster);
        }
```
* * *
