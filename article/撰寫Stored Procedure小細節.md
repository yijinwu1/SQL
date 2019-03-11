撰寫Stored Procedure小細節

*    不要忘記 set nocount on
    
    SQL Server 會針對每個 Select 和 DML 回傳訊息給用戶端，<br>
    當有設定 nocount on 時就可以關閉 SQL Server 回傳訊息的行為，<br>
    降低網路的傳輸量。
    
*    查詢陳述句太過複雜，請使用 SP

    如果商業邏輯複雜導致查詢陳述句過長又龐大，<br>
    因為用戶端只會傳 SP Name 給 SQL Server(而不是一長串的TSQL)，<br>
    降低網路的傳輸量。
    
*    使用兩節式命名

    相關的物件請都使用兩節式(schema name + object name)命名，<br>
    可以直接且明確找到該物件和編譯過的執行計畫，<br>
    省下搜尋其他 schema底下可能的 object 所浪費的資源和時間。
    
    --兩節式命名
    select * from dbo.test
    exec dbo.myproc
    --避免
    select * from test
    exec myproc
    
*    stored procedure 命名勿使用 sp_ 開頭

    如果 stored procedure 使用 sp_ 開頭，<br>
    那 SQL Server 會先搜尋 master database 完後，<br>
    再搜尋現階段連線的 database，<br>
    這不僅讓費時間和資源，<br>
    也增加出錯的機率(如果 master database 有相同的 stroed procedure 名稱)
    
    --usp 開頭<br>
    create proc dbo.ups_xxx<br>
    --避免<br>
    create proc dbo.sp_xxx
    
*    執行字串，請使用 sp_executesql 取代 Execute(Exec) 陳述式

    執行字串，請使用 sp_executesql 預存程序，<br>
    而不使用 Execute 陳述式。<br>
    因為此預存程序支援參數替代，<br>
    所以 sp_executesql 會比 Execute 更具有多變性，<br>
    同時， sp_executesql 所產生的執行計畫更能讓 SQL Server 重複使用，<br>
    因此 sp_executesql 也會比 Execute 更有效率。
    
    --語法
    EXEC SP_EXECUTESQL 執行語法, 帶入參數型態, 帶入參數;
    例：
    DECLARE @query NVARCHAR(MAX)=N'
      SELECT *
      FROM [Study4TW].[dbo].[Activity]
      WHERE Id=@id;'
    EXEC SP_EXECUTESQL @query, N'@id int', @id=1;
    
*    盡量避免使用 Cursor
    
    使用 Cursor 會造成耗時的工作、伺服器收到大量 TDS 封包，Lock 也伴隨而來。<br>
    若需使用，請減少傳回的資料列。請檢視是否可使用 TSQL 或其他方法取代。<br>
    例：<br>
    先將所需處理資料塞到 Temp Table，<br>
    然後開啟 Cursor 並針對 Temp Table處理(不要針對原Table)，<br>
    Server Side Cursor 請使用 FORWARD_ONLY / READ_ONLY 選項以優化效能，<br>
    Cursor 類型勿選用 Staic 和 Keyset，此類型容易讓 Temp Table 暴增，影響效能。<br>
    FAST_FORWARD 類型可結賞網路往返次數。<br>
    
    
