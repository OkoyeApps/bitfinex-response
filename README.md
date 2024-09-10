### :point_right: [Distributed Exchange main Repository can be found here for review](https://github.com/OkoyeApps/distributed-exchange) :point_left:

### Explanations for my distributed exchange engine

I will outline the reasons for incorporating a worker in my implementation, explain its necessity, and demonstrate that its inclusion does not compromise RESTful principles or create client dependencies.

### Links to resources used for implementation

- [Bitfinex loves Grenache](https://blog.bitfinex.com/tutorial/bitfinex-loves-microservices-grenache/)
- [Grenache node link](https://www.npmjs.com/package/grenache-nodejs-link)
- [grenache-grape](https://github.com/bitfinexcom/grenache?tab=readme-ov-file)
- [grenache nodejs http](https://github.com/bitfinexcom/grenache-nodejs-http) (with this internally the DHT is asked for the IP of the server and then the request is done as Peer-to-Peer request via Http.)

### Issues:

The feedback from my review stated, `Unfortunately solution is not P2P, and also relies on a server called (named worker)`

### Questions and answers regarding the roles of a worker and a client in the context of Grape and Grenache.

- **What is a worker :**

  - **Ans:** A worker is an implementation that adds a node to the Grenache network. This means a worker is always required to join a Grenache network, and you can have multiple workers within the network. you create a worker by calling

  ```js
  const service = peer.transport("server");
  ```

  - **Note**: Having a worker does not imply that it is an HTTP server.

- **What then is a client:**

  - **Ans:**A client is an implementation that, when connected to any available worker, forms a peer. This is achieved using the Grenache Node.js HTTP package. This means that a client does not own a worker but instead connects to any available worker to form a peer network.

- **What happens when a client joins the network with its own worker:**

  - **Ans**: The client’s worker is registered on the network, but this does not mean the client will connect exclusively through its own worker. For example, if a client registers its worker at `127.0.0.1:3001` and another worker is registered at `127.0.0.1:3002`, the Grenache network might route the client’s traffic through the `3002` worker, even though the client’s worker is registered at `3001`.

- **Does this mean more than one client can connect to a worker:**

  - **Ans:** Yes.

- **Is this still peer-to-peer:**

  - **Ans** The answer for this depends on the requirement constraints but with Grenache and it's libraries the answer is yes

- **When a client provides its own worker, does it claim sole ownership of that worker:**

  - **Ans:** No it doesn't as when a client worker joins the Grenache network, there is no provided implementation of an exclusive lock over that worker

- **Is there a way to remove a worker from the Grenache network programmatically:**

  - **Ans** In my research although there is a `link.stop() link.stopAnnouncing()` method, this still doesn't immediately remove the worker from the Grenache network
