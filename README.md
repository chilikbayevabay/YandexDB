# YandexDB

Example:


``` C#
            var cred = ChannelCredentials.Create(new SslCredentials(), CallCredentials.FromInterceptor(Interceptor));
            var channel = new Channel("ydb.serverless.yandexcloud.net", 2135, cred, new ChannelOption[] { });
            var header = new Metadata();
            header.Add("x-ydb-auth-ticket", "t1.9euelZrPnp......  --- tokeen.  (yc iam create-token)  ---  ");
            header.Add("x-ydb-database", "/ru-central1/b1g5k..../etn00.... (from console)");
            var client = new Ydb.Cms.V1.CmsService.CmsServiceClient(channel);

            var tableService = new TableService.TableServiceClient(channel);
            var session = tableService.CreateSession(new CreateSessionRequest(), header);
            var creaetSessionResult = session.Operation.Result.Unpack<CreateSessionResult>();
            var sessionId = creaetSessionResult.SessionId;

            var txControl = new TransactionControl() { CommitTx = true, BeginTx = new TransactionSettings() { StaleReadOnly = new StaleModeSettings(), SerializableReadWrite = new SerializableModeSettings(), OnlineReadOnly = new OnlineModeSettings() { AllowInconsistentReads = true } } };
            var res = tableService.ExecuteDataQuery(new ExecuteDataQueryRequest() {
                SessionId = sessionId,
                TxControl = txControl,
                Query = new Query() {
                    YqlText = @"SELECT id, Name
                                FROM `Table1`
                                ORDER BY id
                                LIMIT 10;" 
                }
            }, header);
            var result = res.Operation.Result.Unpack<ExecuteQueryResult>();


```