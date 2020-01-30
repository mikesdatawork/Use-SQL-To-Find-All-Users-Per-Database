![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Find All Users Per Database
**Post Date: March 23, 2018**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process


![Get SQL Users Per Database]( https://mikesdatawork.files.wordpress.com/2018/03/image002.png "Find SQL Users Connections Per Database")
 
<p>Often times it's a good idea to get a list of the users and databases. Traditionally; an easy method for this is use sp_helplogins, but porting this to a table a bit of a mess, and who wants to do it one database at a time? Here's an equivalent bit of SQL logic that helps get you all the databases and users assigned.</p>

## SQL-Logic
```SQL
use master;
set nocount on
 
if object_id('tempdb..#users_per_database') is not null
drop table  #users_per_database
create table    #users_per_database ([database] varchar(255), [name] varchar(255), [desc] varchar(255), [role] varchar(255))
 
declare @gather_users   varchar(max)
set @gather_users   = ''
select  @gather_users   = @gather_users +
'use [' + [name] + '];' + char(10) +
'select 
    db_name()
,   sdp.[name]
,   sdp.type_desc
,   user_name(sdrm.role_principal_id)
from 
    [' + [name] + '].sys.database_principals sdp left outer join 
    [' + [name] + '].sys.database_role_members sdrm 
    on sdp.principal_id = sdrm.member_principal_id 
where 
    sdp.sid not in (0x01,0x00) 
    and sdp.sid is not null 
    and sdp.type not in (''c'')
order by 
    name' + char(10)
from sys.databases 
where [name] not in ('tempdb') and [state_desc] = 'online'
insert into #users_per_database
exec (@gather_users)
 
select
    [database]
,   [name]
from
    #users_per_database
where
    [desc] not in
    (   'DATABASE_ROLE'
    ,   'SQL_USER'
    ,   'WINDOWS_GROUP'
    )
    and [name] not in
    (   'dbo'
    ,   'NT AUTHORITY\SYSTEM'
    ,   'NT AUTHORITY\LOCAL SERVICE'
    ,   'NT AUTHORITY\NETWORK SERVICE'
    )
    and [name] not like '%$%'
```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)


## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

