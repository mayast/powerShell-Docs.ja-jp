# DSC グループ リソース

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

PowerShell Desired State Configuration (DSC) の Group リソースは、ターゲット ノード上でローカル グループを管理するためのメカニズムを備えています。

##構文##
```
Group [string] #ResourceName
{
    GroupName = [string]
    [ Credential = [PSCredential] ]
    [ Description = [string[]] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ Members = [string[]] ]
    [ MembersToExclude = [string[]] ]
    [ MembersToInclude = [string[]] ]
    [ DependsOn = [string[]] ]
}
```

## プロパティ

|  プロパティ  |  説明   | 
|---|---| 
| GroupName| 特定の状態を保証するグループの名前を示します。| 
| Credential| リモート リソースにアクセスするために必要な資格情報を示します。 **注**: このアカウントには、ローカル以外のすべてのアカウントをグループに追加する適切な Active Directory アクセス許可が必要です。このアクセス許可がない場合、エラーが発生します。
| 説明| グループの説明を示します。| 
| Ensure| グループが存在するかどうかを示します。 グループが存在しないことを保証するには、このプロパティを "Absent" に設定します。 グループが存在することを保証するには、"Present" (既定値) に設定します。| 
| [メンバー]| グループからこれらのメンバーを保証することを示します。| 
| MembersToExclude| グループのメンバーでないことを保証するユーザーを示します。| 
| MembersToInclude| グループのメンバーであることを保証するユーザーを示します。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの ID が __ResourceName__ で、そのタイプが __ResourceType__ である場合、このプロパティを使用する構文は DependsOn = "[ResourceType]ResourceName" になります。| 

## 例

次の例では、TestGroup という名前のグループが存在しないことを保証する方法を示します。 

```powershell
Group GroupExample
{
    # This will remove TestGroup, if present
    # To create a new group, set Ensure to "Present“
    Ensure = "Absent"
    GroupName = "TestGroup"
}
```
<!--HONumber=Feb16_HO4-->
