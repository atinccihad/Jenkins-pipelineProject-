Jenkins derlemesinden sürekli dağıtma
Makale
27.09.2022
Okumak için 3 dakika
11 katılımcı
Azure DevOps Services | Azure DevOps Server 2022 - Azure DevOps Server 2019 | TFS 2018

Azure Pipelines, Sürekli Tümleştirme (CI) için Jenkins'i kullanırken Azure'a dağıtan bir Azure Pipelines yayın işlem hattından çeşitli DevOps avantajları elde edebilmeniz için Jenkins ile tümleştirmeyi destekler:

Jenkins derleme işlerinde mevcut yatırımlarınızı yeniden kullanma
İş öğelerini ve ilgili kod değişikliklerini izleme
CI/CD iş akışınız için uçtan uca izlenebilirlik elde edin
Bir dizi bulut hizmeti için tutarlı bir şekilde dağıtma
Dağıtımları gating ile derleme kalitesini zorlama
El ile onay işlemleri ve CI tetikleyicileri gibi iş akışlarını tanımlama
Her Jenkins işiyle ilgili sorunları göstermek için Jenkins'i JIRA ve Azure Pipelines ile tümleştirme
ServiceNow gibi diğer hizmet yönetimi araçlarıyla tümleştirme
Tipik bir yaklaşım, Jenkins'i kullanarak GitHub gibi bir Git deposunda barındırılan kaynak koddan bir uygulama oluşturmak ve azure pipelines kullanarak azure'a dağıtmaktır.

GitHub ve Jenkins'ten Azure'a dağıtım şeması

Başlamadan önce
Uygulamanızın GitHub, Azure Repos, GitHub Enterprise Server, Bitbucket Cloud gibi bir depoda veya Jenkins'in etkileşim kurabileceği başka bir kaynak denetim sağlayıcısında barındırılan kaynak koduna ihtiyacınız vardır.
CI derlemelerinizi çalıştırdığınız bir Jenkins sunucusu gerekir. Azure'da hızlı bir şekilde Jenkins sunucusu ayarlayabilirsiniz.
Uygulamanızı derleyen bir Jenkins projesine ihtiyacınız olacaktır. Örneğin, Jenkins üzerinde Maven ile bir Java uygulaması oluşturabilirsiniz .
Jenkins'i Azure Pipelines ile bağlama
Proje ayarları sayfasının Hizmet bağlantıları bölümünden bir Jenkins hizmet bağlantısı oluşturun.

Daha fazla bilgi için bkz. Jenkins hizmet bağlantısı. Bu bölümdeki genel kavramları tanımıyorsanız bkz. Proje ayarlarınıza erişme ve Hizmet bağlantısı oluşturma ve kullanma.

Jenkins yapıtı ekleme
Yeni bir yayın işlem hattı oluşturun ve bu işlem hattına bir Jenkins yapıtı ekleyin. Jenkins hizmet bağlantısını seçtikten sonra, dağıtmak üzere mevcut bir Jenkins işini seçebilirsiniz.

Bir Jenkins derlemesinin çıkışını Azure blob depolamada depolamak mümkündür. Bunu Jenkins projenizde yapılandırdıysanız Azure depolamadan yapıtları indir'i seçin ve varsayılan sürümü ve kaynak diğer adını seçin.

Daha fazla bilgi için bkz . Jenkins yapıtları. Bu bölümdeki genel kavramlar hakkında bilgi sahibi değilseniz bkz. Yayın işlem hattı oluşturma ve Yayın yapıtları ve yapıt kaynakları.

Dağıtım adımlarını tanımlama
Uygulamanızı seçtiğiniz hedefe dağıtmak için gereken görevleri, yayın işlem hattınızın Görevler sayfasındaki Aracı işi bölümüne ekleyin. Örneğin, bir web uygulamasını dağıtmak için Azure App Service Dağıtma görevini ekleyin.

YAML
Klasik
Azure App Service Dağıtma görevi YAML kodunu deponun kökündeki .yml dosyadaki bir işe ekleyin.

YAML

Kopyala

...
jobs:
- job: DeployMyApp
  pool:
    name: Default
  steps:
  - task: AzureRmWebAppDeployment@4
    inputs:
      connectionType: 'AzureRM'
      azureSubscription: your-subscription-name
      appType: webAppLinux
      webAppName: 'MyApp'
      deployToSlotOrASE: false
      packageForLinux: '$(System.DefaultWorkingDirectory)/**/*.zip'
      takeAppOfflineFlag: true
...
Azure yayın işlem hattınızı her tetiklediğinizde Jenkins CI işi tarafından yayımlanan yapıtlar indirilir ve dağıtımınız için kullanılabilir hale getirilir. Her işle ilişkili işlemeler de dahil olmak üzere iş akışınızın tam izlenebilirliğini elde edersiniz.

Azure App Service Dağıtma görevinin diğer ayrıntılarına bakın Bu bölümdeki genel kavramlar hakkında bilginiz yoksa bkz. Derleme ve yayın işleri veDerlemelerde ve sürümlerde görevleri kullanma.

Sürekli dağıtımı etkinleştirme
Jenkins sunucunuz Azure'da barındırılıyorsa veya Azure DevOps kuruluşunuz Jenkins sunucunuza doğrudan görünürlük sağlıyorsa, yayın işlem hattınızda bir yayının oluşturulmasına ve kaynak yapıt her güncelleştirildiğinde dağıtımın başlatılmasına neden olan sürekli dağıtım (CD) tetikleyicisini kolayca etkinleştirebilirsiniz.

Azure'da barındırılan veya doğrudan görünür bir Jenkins sunucusu için sürekli dağıtımı etkinleştirmek için:

Yayın işlem hattınızın İşlem Hatları sayfasından sürekli dağıtım tetikleyicisi bölmesini açın.

Ayarı Etkin olarak değiştirin.

Ekle'yi seçin ve tetikleyiciyi oluşturmak istediğiniz dalı seçin. İsterseniz varsayılan dalı da seçebilirsiniz.

Ancak, şirket içi bir Jenkins sunucunuz varsa veya Azure DevOps kuruluşunuz Jenkins Sunucunuz için doğrudan görünürlüğe sahip değilse , aşağıdaki adımları kullanarak jenkins projesinden bir Azure işlem hattı yayınını tetikleyebilirsiniz:

Azure DevOps veya TFS kuruluşunuzda Kişisel Erişim Belirteci (PAT) oluşturun. Jenkins bu bilgilerin kuruluşunuza erişmesini gerektirir. Bu bölümdeki yaklaşan adımlar için belirteç bilgilerinin bir kopyasını tuttuğunuzdan emin olun.

Team Foundation Server eklentisini Jenkins sunucunuza yükleyin.

Jenkins projenizde , TFS/Team Services'da Tetikleyici sürümü adlı yeni bir derleme sonrası eylemi bulacaksınız. Bu eylemi projenize ekleyin.

Azure DevOps kuruluşunuzun veya TFS sunucunuzun koleksiyon URL'sini şu şekilde girin: https://<accountname>.visualstudio.com/DefaultCollection/

Kullanıcı adını boş bırakın ve parola olarak PAT'nizi girin.

Tetikleme için Azure DevOps projesini ve yayın tanımını seçin.

Artık Jenkins CI işiniz her tamamlandığında yeni bir CD sürümü tetiklenir.
