---

_That's what it should be.  I am using it in a private project, so it needs to be tidied up before I can show it anyone._

---

# bdta - C library to use the Bitcoin.de Trading API
This is a C equivalent of the PHP SDK provided by [Bitcoin.de](https://www.bitcoin.de) for their Trading API, so a cite from their website (https://www.bitcoin.de/de/api/tapi/v1/sdk) describes it best:

> This SDK is built and provided to ease the request/response-handling. It already includes the basic routines for generating the nonce and signature in the right manner so there is no need to build this by yourself.

Since I only porting, all the honor belongs there and me the bugs.

## Requirements and dependencies
Requests are done with [libcurl](https://curl.haxx.se/libcurl/), HMAC signatures with [openssl](https://www.openssl.org).
In my private project I am using the Websocket API (with [libwebsockets](https://libwebsockets.org)), too, so an future release will access it.


## The following code demonstrates some simple requests using the library:
The code simply includes the library and initializes it for requesting afterwards some concrete methods of the API (showAccountInfo and showMyOrders).
Each call of `do_request` returns an data structure, which provides some general (i.e. response-header) and specific (result regarding the API-method) information. 

```c
#include <stdio.h>
#include <stdlib.h>

#include "btda.h"

int
main (void)
{
    struct bdta_response *response;
    struct bdta_parameter parameters[] = {
        { "type", ORDER_TYPE_SELL },
        { NULL, NULL }
    };

    const char *api_key = "MY_API_KEY";
    const char *api_secret = "MY_API_SECRET";


    bdta_init (api_key, api_secret);


    // Retrieve my account information
    response = do_request (METHOD_SHOW_ACCOUNT_INFO, NULL);
    printf ("showAccountInfo: %s\n", response->body);
    
    // Retrieve my pending sell orders
    response = do_request (METHOD_SHOW_MY_ORDERS, &parameters);
    printf ("showMyOrders (type = sell): %s\n", response->body);


    free (response);
    bdta_cleanup ();

    return EXIT_SUCCESS;
}
```

The Memory for the response is obtained with `malloc`, and can be freed with `free`.
After you are done using the library, you should call the `bdta_cleanup` function.
