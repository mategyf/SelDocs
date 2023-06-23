# Új tábla és form készítése

## 1. SQL

### 1.1 Új tábla létrehozása az adatbázisban

Például:

```sql
CREATE TABLE [dbo].[CURRENCIES](
 [ID]                   [int]           NOT NULL,
 [TransactID]           [int]           NULL,
 [Shortname]            [nvarchar](15)  NULL,
 [InternalCode]         [nvarchar](5)   NULL,
 [Description]          [nvarchar](30)  NULL,
 [RatesUnit]            [int]           NULL,
 [LinkCode_1]           [nvarchar](20)  NULL,
 [LinkCode_2]           [nvarchar](20)  NULL,
 [Rounding]             [int]           NULL,
 [NotInUse]             [bit]           NOT NULL,
 [CurrRatesWEBManage]   [int]           NOT NULL,
 [ParentCurrenciesID]   [int]           NOT NULL
)
```

### 1.2 Új UPSERT és DELETE tárolt eljárások létrehozása

Korábbi hasonló tárolt eljárás alapján érdemes csinálni.

### 1.3 Felhasználói jogok

```sql
GRANT EXECUTE ON OBJECT::CURRENCIES_UPSERT TO xxx_USER;
GRANT EXECUTE ON OBJECT::CURRENCIES_DELETE TO xxx_USER;

GRANT SELECT ON OBJECT::CURRENCIES TO xxx_USER;
```

### 1.4 SYS_GRIDS frissítése

A SYS_GRIDS-be a megfelelő paraméterekkel kell egy rekordot tenni.

```sql
INSERT INTO [dbo].[SYS_GRIDS]
           ([Code]
           ,[SQL_SELECT]
           ,[SQL_TOP]
           ,[SQL_FROM]
           ,[SQL_WHEREQUERY]
           ,[SQL_FIX_WHERE]
           ,[SQL_GROUP_BY]
           ,[STOREDPROC_UPSERT]
           ,[STOREDPROC_DELETE])
     VALUES
           ('CURRENCIES'
           ,'Shortname, InternalCode, [Description], RatesUnit, LinkCode_1, LinkCode_2, Rounding, NotInUse, CurrRatesWEBManage, ParentCurrenciesID'
           ,0
           ,'CURRENCIES'
           ,''
           ,'_deleted=0'
           ,''
           ,'CURRENCIES_UPSERT'
           ,'CURRENCIES_DELETE')
```

## 2. Frontend
