<h1><b>Primary Key、Clustered Index、NONClustered Index</b></h1>

每個資料表只能有一個 Clustered Index，<br/>
若需建立一個 Clustered Index，<br/>
當主索引鍵(Primary key)存在時，<br/>
需檢查此主索引鍵是否為非叢集(NONClustered)，否則無法建立。<br/>

若建立索引時未指令為叢集或非叢集時，預設為建立非叢集(NONClustered)索引。<br/>

若資料表具有主索引鍵(Primary key)，則建立主索引鍵預設將被設定為叢集(Clustered)<br/>
且唯一的(Unique)， 故此資料表無法建立一個叢集索引(Clustered index)，<br/>
除非將主索引鍵(Primary key)改為非叢集(NONClustered)， 否則無法建立叢集索引(Clustered index)，<br/>
但是可以建立多個非叢集索引(NONClustered index)。<br/>

建立唯一的(UNIQUE)索引時，應該要將欄位設定為非空值(NOT NULL)。<br/>
因為在建立資料時，重複的空值(NULL)會被視為重複資料。<br/>

將Clustered Primary Key 改為NONClustered Primary Key語法
<pre>
  BEGIN TRANSACTION--啟動交易
  GO

  ALTER TABLE 資料表 DROP CONSTRAINT 主索引鍵名稱  --先刪除原來的索引
  GO

  ALTER TABLE 資料表 ADD CONSTRAINT PK_tbl001 PRIMARY KEY NONCLUSTERED --接著建立NONCLUSTERED Primary Key的主鍵
  (
     欄位
  ) WITH( STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
  GO

  COMMIT--確認交易
  GO
</pre>
