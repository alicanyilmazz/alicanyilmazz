<h3 align="left">👩‍💻  About Me</h3>

###

<h1 align="center"></h1>

###

<h4 align="left">- 🔭 I'm working on as software engineer.<br>- 📚  Domain Experience : #Payment Systems #Debit And Prepaid Card<br>- ⚡Software Engineer at Intertech.<br>-  ⚡Canada,Toronto</h4>

###

<h3 align="left">🛠 Language and tools</h3>

###

<div align="left">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/dotnetcore/dotnetcore-original.svg" height="40" alt="dotnetcore logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/dot-net/dot-net-plain-wordmark.svg" height="40" alt="dot-net logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/microsoftsqlserver/microsoftsqlserver-plain-wordmark.svg" height="40" alt="microsoftsqlserver logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/azure/azure-original.svg" height="40" alt="azure logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/sqlite/sqlite-original.svg" height="40" alt="sqlite logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/kubernetes/kubernetes-plain.svg" height="40" alt="kubernetes logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/docker/docker-plain-wordmark.svg" height="40" alt="docker logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/firebase/firebase-plain-wordmark.svg" height="40" alt="firebase logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/html5/html5-original.svg" height="40" alt="html5 logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/css3/css3-original.svg" height="40" alt="css3 logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg" height="40" alt="javascript logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/swift/swift-original.svg" height="40" alt="swift logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/jira/jira-original.svg" height="40" alt="jira logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/git/git-original.svg" height="40" alt="git logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/github/github-original.svg" height="40" alt="github logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/subversion/subversion-original.svg" height="40" alt="subversion logo"  />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg" height="40" alt="react logo"  />
</div>

###


###

<div align="center">
</div>

###

<div align="center">
  <a href="https://www.linkedin.com/in/alicanylmz/" target="_blank">
    <img src="https://raw.githubusercontent.com/maurodesouza/profile-readme-generator/master/src/assets/icons/social/linkedin/default.svg" width="37" height="25" alt="linkedin logo"  />
  </a>
  <a href="https://medium.com/@alicanyilmaz101" target="_blank">
    <img src="https://raw.githubusercontent.com/maurodesouza/profile-readme-generator/master/src/assets/icons/social/medium/default.svg" width="37" height="25" alt="medium logo"  />
  </a>
  <img src="https://raw.githubusercontent.com/maurodesouza/profile-readme-generator/master/src/assets/icons/social/youtube/default.svg" width="37" height="25" alt="youtube logo"  />
  <img src="https://raw.githubusercontent.com/maurodesouza/profile-readme-generator/master/src/assets/icons/social/twitter/default.svg" width="37" height="25" alt="twitter logo"  />
</div>

###

USE [DatabaseAdin];

DECLARE @SchemaName NVARCHAR(128) = 'dbo';
DECLARE @TableName NVARCHAR(128) = 'TM';
DECLARE @ColumnName NVARCHAR(128) = NULL;  -- Buraya kolon adı yazabilirsin (ör: 'CARD_NUMBER'). Eğer NULL ise tüm kolonları arar.

-- Kolonları tablo değişkenine alalım
DECLARE @Columns TABLE (ColumnName NVARCHAR(128));

IF @ColumnName IS NULL
BEGIN
    -- Tüm kolonlar alınır
    INSERT INTO @Columns (ColumnName)
    SELECT name FROM sys.columns 
    WHERE object_id = OBJECT_ID(QUOTENAME(@SchemaName) + '.' + QUOTENAME(@TableName));
END
ELSE
BEGIN
    -- Tek kolon alınır
    INSERT INTO @Columns (ColumnName)
    SELECT name FROM sys.columns 
    WHERE object_id = OBJECT_ID(QUOTENAME(@SchemaName) + '.' + QUOTENAME(@TableName))
      AND name = @ColumnName;
END

-- Tablonun kullanıldığı SP ve View'leri bulalım
WITH ReferencingObjects AS
(
    SELECT DISTINCT 
        o.object_id,
        OBJECT_SCHEMA_NAME(o.object_id) AS SchemaName,
        OBJECT_NAME(o.object_id) AS ObjectName,
        o.type_desc AS ObjectType,
        m.definition AS ObjectDefinition
    FROM sys.sql_expression_dependencies d
    INNER JOIN sys.objects o ON d.referencing_id = o.object_id
    INNER JOIN sys.sql_modules m ON o.object_id = m.object_id
    WHERE d.referenced_id = OBJECT_ID(@SchemaName + '.' + @TableName)
      AND o.type IN ('P', 'V')
)

-- Kolon kullanım durumlarını gösterelim
SELECT 
    r.SchemaName AS ReferencingSchema,
    r.ObjectName AS ReferencingObject,
    r.ObjectType,
    c.ColumnName,
    CASE 
        WHEN r.ObjectDefinition LIKE '%' + c.ColumnName + '%' THEN '✅ Var'
        ELSE '❌ Yok'
    END AS ColumnUsage,
    CASE 
        WHEN r.ObjectDefinition LIKE '%SELECT *%' THEN '⚠️ SELECT * Kullanılmış'
        ELSE ''
    END AS Warning
FROM ReferencingObjects r
CROSS JOIN @Columns c
ORDER BY r.ObjectType, r.ObjectName, c.ColumnName;







