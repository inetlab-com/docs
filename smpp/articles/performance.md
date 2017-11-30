Performance
===========

Production
----------

The speed ultimately is determined by 
  * how fast you can prepare the messages
  * network bandwidth
  * performance on the remote side (SMPP Server)
  * how fast you can process responses

On good tuned production systems you can reach 500 messages/seconds.

Tuning
-------

You can try to play with following optimization parameters:

Change number of threads that process received messages. Dafault is 3
  
    client.WorkerThreads = 10;

Change receive or send buffer size for client socket

    client.ReceiveBufferSize = 32 * 1024 * 1024;
    client.SendBufferSize = 32 * 1024 * 1024; 

You can also monitor queue state for the client:

    client.Queue.ReceiveCount;
    client.Queue.SendCount;

Local Test
----------

Inetlab.SMPP performance on local machine with disabled logging shows following results

    Sync performance: 8764 m/s
    Async performance: 13252 m/s 

Following code demonstrate this


    class Program
    {
        static void Main(string[] args)
        {
            Program p = new Program();

           Console.WriteLine("Sync performance: "+p.RunSyncTest() +" m/s");
           Console.WriteLine("Async performance: " + p.RunAsyncTest() + " m/s");
        }

        readonly SmppServer _server = new SmppServer();
        readonly SmppClient _client = new SmppClient();

        public int RunSyncTest()
        {
            ManualResetEvent start = new ManualResetEvent(false);
            _server.evServerStarted += (sender, args) => start.Set();
            _server.StartAsync(5656);

            start.WaitOne(2000);

            _client.Connect("localhost", 5656);
            _client.Bind("1", "2");
            Stopwatch watch = new Stopwatch();
            watch.Start();

            const int messagesNumber = 20000;
            for (int i = 0; i < messagesNumber; i++)
            {
                var resp = _client.Submit(SMS.ForSubmit().From("111").To("222").Text("test"));
            }

            int result = Convert.ToInt32(messagesNumber / watch.Elapsed.TotalSeconds);

            _client.UnBind();
            _client.Disconnect();
            _server.Stop();

            return result;
        }


        public int RunAsyncTest()
        {
            ManualResetEvent start = new ManualResetEvent(false);
            _server.evServerStarted += (sender, args) => start.Set();
            _server.StartAsync(5656);

            start.WaitOne(2000);

            _client.Connect("localhost", 5656);
            _client.Bind("1", "2");
            _client.evSubmitComplete += (sender, data) => { };




            const int messagesNumber = 20000;


            object sync = new object();
            ManualResetEvent completed = new ManualResetEvent(false);
            long transfered = 0;

            _client.evSubmitComplete += delegate(object sender, SubmitSmResp data)
            {
                lock (sync)
                {
                    transfered++;
                    if (transfered == messagesNumber)
                    {
                        completed.Set();
                    }
                }

            };

            Stopwatch watch = new Stopwatch();
            watch.Start();

            for (int i = 0; i < messagesNumber; i++)
            {
                _client.SubmitAsync(SMS.ForSubmit().Text("test " + i).From("1111").To("22222"));
            }


            completed.WaitOne(120000);

            int result = Convert.ToInt32(messagesNumber / watch.Elapsed.TotalSeconds);

            _client.UnBind();
            _client.Disconnect();
            _server.Stop();

            return result;
        }
    }
