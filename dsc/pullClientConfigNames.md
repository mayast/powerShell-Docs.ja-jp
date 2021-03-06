# 構成名を使用したプル クライアントのセットアップ

> 適用先: Windows PowerShell 5.0

各ターゲット ノードに対し、プル モードを使用するように指示し、プル サーバーに接続して構成を取得するための URL を指定する必要があります。 これを行うには、必要な情報を備えるようにローカル構成マネージャー (LCM) を構成する必要があります。LCM を構成するには、**DSCLocalConfigurationManager** 属性で修飾された特別な種類の構成を作成します。 LCM の構成の詳細については、「[ローカル構成マネージャーの構成](metaConfig.md)」を参照してください。

> **注**: このトピックは、PowerShell 5.0 に適用されます。 PowerShell 4.0 でのプル クライアントのセットアップについては、「[PowerShell 4.0 での構成 ID を使用したプル クライアントのセットアップ](pullClientConfigID4.md)」を参照してください。

次のスクリプトは、"CONTOSO-PullSrv" という名前のサーバーから構成をプルするように LCM を構成します。

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }
        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
            ConfigurationNames = @('ClientConfig')
            
        }      
    }
}
PullClientConfigID
```

このスクリプトでは、**ConfigurationRepositoryWeb** ブロックにプル サーバーを定義しています。 **ServerURL** プロパティには、プル サーバー用のエンドポイントを指定します。

**RegistrationKey** プロパティは、プル サーバーのすべてのクライアント ノードとそのプル サーバーとの間で共有されるキーです。 同じ値がプル サーバー上のファイルに格納されます。 **ConfigurationNames** プロパティには、クライアント ノード用の構成の名前を指定します。 プル サーバーでは、このクライアント ノードの構成 MOF ファイルに *ConfigurationNames*.mof という名前を指定する必要があります。ここで、*ConfigurationNames* はこのメタ構成に設定した **ConfigurationNames** プロパティの値と一致します。

このスクリプトを実行すると、**PullClientConfigID** という名前の新しい出力フォルダーが作成され、そこにメタ構成 MOF ファイルが格納されます。 この場合、メタ構成 MOF ファイルの名前は `localhost.meta.mof` になります。

構成を適用するには、**Path** をメタ構成 MOF ファイルの場所に設定して **Set-DscLocalConfigurationManager** コマンドレットを呼び出します。 たとえば次のようになります。`Set-DSCLocalConfigurationManager localhost –Path .\PullClientConfigID –Verbose.`

> **注**: 登録キーは、Web プル サーバーでのみ動作します。 SMB プル サーバーでは、引き続き **ConfigurationID** を使用する必要があります。 **ConfigurationID** を使用したプル サーバーの構成については、「[構成 ID を使用したプル クライアントのセットアップ](pullClientConfigID.md)」を参照してください。

## リソースおよびレポート サーバー

既定では、クライアント ノードは構成プル サーバーから必要なリソースを取得し、構成プル サーバーに状態をレポートします。 ただし、リソース用とレポート用にそれぞれ異なるプル サーバーを指定できます。
リソース サーバーを指定するには、**ResourceRepositoryWeb** (Web プル サーバーの場合) または **ResourceRepositoryShare** ブロック (SMB プル サーバーの場合) を使用します。
レポート サーバーを指定するには、**ReportRepositoryWeb** ブロックを使用します。 レポート サーバーを SMB サーバーにすることはできません。
次のメタ構成は、**CONTOSO-PullSrv** から構成を取得し、**CONTOSO-ResourceSrv** からリソースを取得し、**CONTOSO-ReportSrv** に状態レポートを送信するように、プル クライアントを構成します。

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }

        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = 'fbc6ef09-ad98-4aad-a062-92b0e0327562'
        }
        
        ResourceRepositoryWeb CONTOSO-ResourceSrv
        {
            ServerURL = 'https://CONTOSO-REsourceSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '30ef9bd8-9acf-4e01-8374-4dc35710fc90'
        }

        ReportServerWeb CONTOSO-ReportSrv
        {
            ServerURL = 'https://CONTOSO-REsourceSrv:8080/PSDSCPullServer.svc'
        }
    }
}
PullClientConfigID
```

## 参照

* [構成 ID を使用したプル クライアントのセットアップ](pullClientConfigID.md)
* [DSC Web プル サーバーのセットアップ](pullServer.md)
<!--HONumber=Feb16_HO4-->
