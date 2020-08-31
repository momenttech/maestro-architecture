---
title: Messaging
slideshow:
   logoUrl: ../images/mmtt.png
---
# Messaging details

## Intra Pod Messaging

Communication beetween elements of a pod is done via zeromq lib from [Messaging package](http://gitlab.mmtt.fr/AI/Messaging) version **3**.

### Installation

    pip3 install git+http://gitlab.mmtt.fr/AI/Messaging.git@3.0

### The client

The client implement the [Lazy Pirate pattern](http://zguide.zeromq.org/page:chapter4), a client-side reliability algorithme.  
Serialization and deserialization is done by the library and support numpy arrays.

**Parameters:**
* server: remote address, should be *127.0.0.1*
* port: remote port
* protocol: Optional, default is *tcp*

**Simple exemple:**  
```python
from Messaging import zeromq
import numpy as np

client = zeromq.LazyClient('127.0.0.1', 65000)
data = {'request': 'inverse array', 
        'content': np.array([[1, 2, 3],
                             [4, 5, 6]])
       }

res = client.send(data)
print res

>>> {'request': 'inverse array', 
     'content': array([[-1, -2, -3], 
                       [-4, -5, -6]])
    }
```

### The Worker

The worker is multi-threaded, default is 5 threads.

**Parameters:**
* callback: call function of the worker
* server: address to bind, should be *127.0.0.1*
* port: port to bind
* protocol: Optional, default is *tcp*
* nb_workers: Optional, number of threads to use. Default is 5.

**Simple exemple:**
```python
from time import sleep
from Messaging import zeromq
import numpy as np

def inverse(data):
    data['array'] = -data['array']
    return data

workers = zeromq.ThreadedWorker(inverse, "127.0.0.1", 65000, nb_workers=2)
workers.start()
## Stop workers after 120 seconds
sleep(120)
workers.stop()