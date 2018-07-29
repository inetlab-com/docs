Analyze SMPP Protocol between client and server
===============================================

Wireshark
---------

Most reliable way to analyze SMPP Protocol is to capture network trafic with **Wireshark** tool http://www.wireshark.org/.

Internal Logger
---------------

Inetlab.SMPP library also provides build-in logging feature. All code of the library uses @Inetlab.SMPP.Logging.ILog interface.
You can implement this interface with any kind of logging framework for your solution.

  - [NLog](http://nlog-project.org)
  - [Log4Net](http://logging.apache.org/log4net/release/features.html)

Library also provides @Inetlab.SMPP.Logging.ConsoleLogger and @Inetlab.SMPP.Logging.FileLogger classes.
 
When application starts you need to register global logger factory for the library.

    LogManager.SetLoggerFactory(delegate(string loggerName)
                                           {
                                               return new ConsoleLogger(loggerName, TraceLevel.Verbose);
                                           });

or you can set @Inetlab.SMPP.SmppClient.Logger property when you create instances of @Inetlab.SMPP.SmppClient, @Inetlab.SMPP.Common.SmppServerClient or @Inetlab.SMPP.SmppServer


    client.Logger = new ConsoleLogger("MyClient", TraceLevel.Verbose);
                                          

When you need to trace network stream of SMPP protocol your logger class should implement method 
@Inetlab.SMPP.Logging.ILog.Trace(System.Object)



Example of @Inetlab.SMPP.Logging.ILog interface implmentation:

    public class ConsoleLogger : ILog
    {
        private readonly string _loggerName;
        private readonly TraceLevel _level;

        public ConsoleLogger(string loggerName, TraceLevel level)
        {
            _loggerName = loggerName;
            _level = level;
        }

        public string Name
        {
            get { return _loggerName; }
        }

        public void Error(object message, Exception ex)
        {
            if (_level < TraceLevel.Error) return;
            
            Console.WriteLine("ERROR:{0,3}: ({1}) {2}{3}", Thread.CurrentThread.ManagedThreadId, _loggerName, message,
                              ex != null ? ", Exception: " + ex : "");

        }

        public void Error(object message)
        {
            Error(message, null);
        }

        public void Warn(object message, Exception ex)
        {
            if (_level < TraceLevel.Warning) return;

            Console.WriteLine("WARN :{0,3}: ({1}) {2}", Thread.CurrentThread.ManagedThreadId, _loggerName, message);
            if (ex != null)
            {
                Console.WriteLine("Exception: " + ex);
            }
        }

        public void Warn(object message)
        {
            Warn(message, null);
        }

        public void Info(object message)
        {
            if (_level < TraceLevel.Info) return;

            Console.WriteLine("INFO :{0,3}: ({1}) {2}", Thread.CurrentThread.ManagedThreadId, _loggerName, message);
        }

        public void Debug(object message)
        {
            if (_level < TraceLevel.Info) return;

            Console.WriteLine("DEBUG:{0,3}: ({1}) {2}", Thread.CurrentThread.ManagedThreadId, _loggerName, message);
        }

        public void Trace(object message)
        {
            if (_level < TraceLevel.Verbose) return;

            Console.WriteLine("TRACE:{0,3}: ({1}) {2}", Thread.CurrentThread.ManagedThreadId, _loggerName, message);
        }

        public void Trace(object message, byte[] data)
        {

            if (_level < TraceLevel.Verbose) return;

            Console.WriteLine("TRACE:{0,3}: ({1}) {2}{3}", Thread.CurrentThread.ManagedThreadId, _loggerName, message, ToHexString(data));
        }

        private string ToHexString(byte[] buf)
        {
            if (buf == null) return "";

            System.Text.StringBuilder sb = new System.Text.StringBuilder(buf.Length * 2 + 2);
            for (int i = 0; i < buf.Length; i++)
            {
                sb.Append(buf[i].ToString("x2"));
            }
            return sb.ToString();
        }
    }


Special events
--------------

You can also use special events in base class @Inetlab.SMPP.Common.SmppClientBase for tracking PDUs:
  * with event @Inetlab.SMPP.Common.SmppClientBase.evPduReceiving you can monitor all incoming  PDUs including @Inetlab.SMPP.PDU.UnknownPacket.
  * event @Inetlab.SMPP.Common.SmppClientBase.evPduSending is invoked before sending PDU to endpoint.
  * with @Inetlab.SMPP.Common.SmppClientBase.evPduRejected you can find out which PDU didn't reach remote side.  
