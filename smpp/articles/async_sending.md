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
public static async Task<IList<SubmitSmResp>> TrySubmitAsync(this SmppClient client, IList<SubmitSm> pduList)
{
    var respList = new List<SubmitSmResp>();
    foreach (var submitSm in pduList)
    {
        var resp = await client.TrySubmitAsync(submitSm);
        respList.Add(resp);
    }

    return respList;
}
```
