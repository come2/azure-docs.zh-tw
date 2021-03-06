您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立資料庫和集合。 

1. 按一下 [資料總管] > [新增集合]。 
    
    [新增集合] 區域會顯示在最右邊，您可能需要向右捲動才能看到它。

    ![Azure 入口網站資料總管，新增集合刀鋒視窗](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. 在 [新增集合] 頁面上，輸入新集合的設定。

    設定|建議的值|說明
    ---|---|---
    資料庫識別碼|工作|輸入 *Tasks* 作為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 /、\\、#、? 或尾端空格。
    集合識別碼|項目|輸入 *Items* 作為新集合的名稱。 集合識別碼與資料庫名稱具有相同的字元需求。
    儲存體容量| 固定 (10 GB)|將值變更為 [固定 (10 GB)]。 此值是資料庫的儲存體容量。
    輸送量|400 RU|將輸送量變更為每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以相應增加輸送量。
    資料分割索引鍵|/類別|可將資料平均分散到每個資料分割的資料分割索引鍵。 選取正確的資料分割索引鍵對於建立高效能集合來說很重要。 若要進一步了解，請參閱[設計資料分割](../articles/cosmos-db/partition-data.md#designing-for-partitioning)。

    按一下 [確定] 。

    [資料總管] 會顯示新的資料庫和集合。

    ![Azure 入口網站 [資料總管]，顯示新的資料庫和集合](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)