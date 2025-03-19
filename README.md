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
DECLARE @ColumnName NVARCHAR(128) = NULL; -- Kolon adını yaz veya NULL bırak.

-- Kolonları tablo değişkenine aktar
DECLARE @Columns TABLE (ColumnName NVARCHAR(128));
INSERT INTO @Columns (ColumnName)
SELECT name FROM sys.columns 
WHERE object_id = OBJECT_ID(QUOTENAME(@SchemaName) + '.' + QUOTENAME(@TableName));

-- Tabloyu kullanan tüm SP ve View’leri al
;WITH ReferencingObjects AS
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

SELECT 
    r.SchemaName AS ReferencingSchema,
    r.ObjectName AS ReferencingObject,
    r.ObjectType,
    CASE WHEN @ColumnName IS NOT NULL THEN @ColumnName ELSE '-' END AS CheckedColumn,
    CASE WHEN @ColumnName IS NOT NULL THEN
        CASE WHEN r.ObjectDefinition LIKE '%' + @ColumnName + '%' THEN 'Var' ELSE 'Yok' END
        ELSE '-' END AS ColumnExists,
    CASE WHEN r.ObjectDefinition LIKE '%SELECT *%' THEN 'SELECT * kullanılmış'
         ELSE '-' END AS SelectStarUsed,
    -- Kullanılan tüm kolonları yan yana yaz
    CASE WHEN @ColumnName IS NULL THEN
        STUFF((SELECT ', ' + col.ColumnName
               FROM @Columns col
               WHERE r.ObjectDefinition LIKE '%' + col.ColumnName + '%'
               FOR XML PATH(''), TYPE).value('.', 'NVARCHAR(MAX)'), 1, 2, '')
         ELSE '-' END AS AllUsedColumns,
    -- Kullanılan / Kullanılmayan kolon sayısı
    (SELECT COUNT(*) FROM @Columns col WHERE r.ObjectDefinition LIKE '%' + col.ColumnName + '%') AS UsedColumnCount,
    (SELECT COUNT(*) FROM @Columns col WHERE r.ObjectDefinition NOT LIKE '%' + col.ColumnName + '%') AS UnusedColumnCount
FROM ReferencingObjects r
ORDER BY r.ObjectType, r.ObjectName;

-- Toplam var/yok sayısını da verelim:
IF(@ColumnName IS NOT NULL)
BEGIN
    SELECT 
        SUM(CASE WHEN ObjectDefinition LIKE '%' + @ColumnName + '%' THEN 1 ELSE 0 END) AS TotalExistsCount,
        SUM(CASE WHEN ObjectDefinition NOT LIKE '%' + @ColumnName + '%' THEN 1 ELSE 0 END) AS TotalNotExistsCount
    FROM ReferencingObjects;
END



