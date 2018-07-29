Asynchronous Programming with .NET 4.5 
======================================

For developers who want to use advantages of .NET 4.5 in writing asynchronous code, can use following SmppClientExtension class that helps to write async methods.


    public static class SmppClientExtension
    {

        public static Task<SubmitSmResp> TrySubmitAsync(this SmppClient client, SubmitSm pdu)
        {
            var tcs = new TaskCompletionSource<SubmitSmResp>();

            SubmitSmRespEventHandler handler = null;

            handler = (sender, data) =>
            {

                if (pdu.Sequence == data.Sequence)
                {

                    client.evSubmitComplete -= handler;
                    tcs.SetResult(data);

                }
            };

            client.evSubmitComplete += handler;

            try
            {
                client.SubmitAsync(pdu);
            }
            catch (Exception ex)
            {
                tcs.TrySetException(ex);
            }
            return tcs.Task;
        }


    }


Usage example:

```csharp

    public static async Task<IEnumerable<SubmitSmResp>> SendBatch(this SmppClient client, IEnumerable<SubmitSm> list)
    {
         List<Task> tasks = new List<Task>();

         foreach (SubmitSm sm in list)
         {
             tasks.Add(client.TrySubmitAsync(sm));
         }

         await Task.WhenAll(tasks).ConfigureAwait(false);

         return tasks.OfType<Task<SubmitSmResp>>().Select(x => x.Result);
    }

```
