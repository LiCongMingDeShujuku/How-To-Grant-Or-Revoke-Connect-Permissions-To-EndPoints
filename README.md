![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# POST TITLE
#### SECONDARY TITLE
**TIME STAMP**

## Contents

- [中文](#中文)
- [English](#English)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
以下是如何授予或撤消对Endpoints的权限。第一; 我们将从创建两个SQL帐户开始。 一个将拥有Endpoints（通过创建它），另一个将由应用程序用于连接到Endpoint。

这里有一个快速提示。只要CONNATION权限授予任何帐户Endpoint，它就会显示为授予Endpoint的“owner”。因此，如果你查看视图  sys.server_permissions和sys.server_principals，你会看到sys.server_permissions视图下的grantor_principal_id列将映射回sys.server_principals视图下的principal_id列。 这是GRANTOR。

Endpoint所有者帐户确实没有什么特别之处。我称它为“EndpointOwner”我是知道它的目的。这可以是Windows帐户，SQL帐户，

```SQL
甚至可能是一个组，但我可以简单地直接通过SQL登录。

使用password ='MyComplexPassword'创建登录名[EndpointOwner]，default_database = [master]，default_language = [us_english]，check_expiration = off，check_policy = off; alter server role [sysadmin] add member [EndpointOwner]
```


## English
Here’s how you can GRANT or REVOKE permissions to Endpoints. First; we’ll start off by creating two SQL accounts. One which will own the endpoint ( by creating it ), and the other which will be used by the applications to connect to the Endoint.

Here’s a quick tip. Whenever the CONNECT permission is granted to an Endpoint on any account it will appear as the ‘owner’ of the Endpoint granted it. So if you were looking at the catalog views sys.server_permissions, and the sys.server_principals you’ll see the grantor_principal_id column under the sys.server_permissions view will map back to the principal_id column under the sys.server_principals view. This is the GRANTOR.

There really isn’t anything special about the Endpoint owner account. I just called it “EndpointOwner” so I know it’s purpose. 

This can be a Windows account, SQL account, or perhaps even a group, but I’m keeping it simple with a straight-up SQL login.

```SQL
create login [EndpointOwner] with password=’MyComplexPassword’, default_database=[master], default_language=[us_english], check_expiration=off, check_policy=off; alter server role [sysadmin] add member [EndpointOwner]
```


![#](images/01-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
We are also going to create an SQL account that the application will use to connect using the Endpoint. We are calling it the EndpointUser. This is the same process as above.
create login [EndpointUser] with password=’MyComplexPassword’, default_database=[master], default_language=[us_english], check_expiration=off, check_policy=off;
我们还将创建一个供应用程序与Endpoint进行连接的SQL帐户。我们称之为EndpointUser。这与上述过程相同。
使用password ='MyComplexPassword'创建登录名[EndpointUser]，default_database = [master]，default_language = [us_english]，check_expiration = off，check_policy = off;
You’ll then login to Management Studio using the EndpointOwner account, and create (or drop and recreate) your Endpoint if it already exists. This will of course set the Endpoint Owner to your new EndpointOwner SQL login. You could of course run the statement GRANT TAKE OWNERSHIP ON ENDPOINT and simply specify your new owner account name. I could show you the statement to create the Endpoint, but this is more about managing the permissions with GRANT or REVOKE rather than creating the Endpoint it’s self.
use master; grant take ownership on endpoint::MyEndpoint to MyEndpointOwnerAccount with grant option;
然后，你将使用EndpointOwner帐户登录Management Studio，并创建（或删除并重新创建）你的Endpoint（如果已存在）。这当然会将Endpoint Owner设置为新的EndpointOwner SQL登录。你当然也可以在ENDPO上运行语句GRANT TAKE OWNERSHIP，只需指定新的所有者帐户名称即可。我可以向你展示创建Endpoint的语句，但这更多的是使用GRANT或REVOKE管理权限，而不是创建Endpoint本身。

![#](images/02-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#") 

Next we’ll be granting the CONNECT permission to the Endpoint. In this example we’ll be using the Endpoint ‘ConfigMgrEndpoint’. By the way; you’ll need the CONTROL permission on the endpoint or ALTER ANY ENDPOINT permission just to run the GRANT statement against an Endpoint. If you’re the DBA; you’ll usually have the rights to perform this action, but if you don’t, this is just something to keep in mind in case you run into trouble running grants against other accounts specifically for Endpoints.
接下来，我们将授予Endpoint的CONNECT权限。在这个例子中，我们将使用Endpoint'ConfigMgrEndpoint'。另外，你需要Endpoint上的CONTROL权限或ALTER ANY ENDPOINT权限才能对Endpoint运行GRANT语句。如果你是DBA，通常有权执行此操作，但如果你不这样做，那么在遇到专门针对Endpoint的其他帐户运行授权时遇到问题时，请注意这一点。
use [master]; grant connect on endpoint::[ConfigMgrEndpoint] to [EndpointUser];

![#](images/03-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
Alternatively; you could use the GUI to GRANT CONNECTION permissions to the Endpoint on a particular user, but the process as you can see below is riddled with different windows, and prompts, and makes for a complicated process. In the example below we are using the EndpointOwner as the user that the applications will use to connect to SQL Server through the Endpoint.
另外，你可以使用GUI对特定用户的Endpoint授予CONNECTION权限，但是你可以看到下面的过程充满了不同的窗口和提示，并使其变得很复杂的过程。在下面的示例中，我们使用EndpointOwner作为应用程序将通过Endpoint连接到SQL Server的用户。
1. Go to your database instance.
2. Expand Security
3. Expand Logins
1.转到数据库实例。
2.展开安全性
3.展开登录

![#](images/04-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
4. Double click the user your application will use to connect to the Endpoint. Again in our example we are using the EndpointOwner account.
4.双击应用程序将用于连接到Endpoint的用户。在我们的示例中，我们再次使用EndpointOwner帐户。

![#](images/05-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
5. Select ‘Securables’ on the left, and ‘Search’ on the right.
5.选择左侧的“Securables”和右侧的“Search”。

![#](images/06-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
6. Select ‘Specific Objects’, and click ‘OK’.
6.选择“Specific Objects”，然后单击“OK’”。

![#](images/07-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
7. Select ‘Object Types’.
7. 选择“Object Types”。
![#](images/08-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")

 
8. Check ‘Endpoints’ and click ‘OK’.
8. 查询“Endpoints”并单击“OK”。
![#](images/09-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
9. Click ‘Browse’.
9.单击“Browse”

![#](images/10-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
10. Select the Endpoint you are granting the CONNECT too, and click ‘OK’. In this example we are using Endpoint [ConfigMgrEndpoint].
10.选择你正在授予CONNECT的端点，然后单击“OK”。在此示例中，我们使用Endpoint [ConfigMgrEndpoint]。

![#](images/11-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
11. Click ‘OK’.
11.单击“OK’”。

![#](images/12-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
12. Select the Endpoint form the list, and check ‘Grant’, and click ‘OK’.
12.从列表中选择Endpoint，然后选中“Grant”，然后单击“OK”。

![#](images/13-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
Suppose you want to REVOKE the CONNECT permissions to the ENDPOINT… Here’s a quick statement to do that instead of using the GUI.
如果你想REVOKE ENDPOINT的CONNECT权限，这是一个不使用GUI的快速状态，

![#](images/14-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
Removing CONNECT permissions is much easier than adding cause you don’t have to search the different objects. You just need to double click the account in SQL Server, go to Securables, and simply uncheck the GRANT. Here is what you will see when using the GUI to remove the CONNECT permission.
删除CONNECT权限比添加更容易，因为你不必搜索不同的对象。你只需双击SQL Server中的帐户，转到Securables，然后只需取消选中GRANT即可。以下是使用GUI删除CONNECT权限时将看到的内容。

![#](images/15-How-To-Grant-Or-Revoke-Connect-Permissions-To-EndPoints.pngraw=true "#")
 
Hope this helpful.
希望这个对你有所帮助。




[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

