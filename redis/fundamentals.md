# Redis fundamentals

Redis stands for **REmote DIctionary Server**

- Redis stores data in in-memory (RAM) instead of disks.
- We can compare redis to a nosql db, but it's not completely a nosql, which means it stores data in key-value pairs.
- Redis is fast as it stores data in ram, however the con is also the same.
- As it stores data in RAM, if we use redis as our DB (we can), all of a sudden if our system got crashed, all data in redis will be gone as it store only in RAM.
- That's why redis is not primarily used as a DB.
- However we can use it for temporary storage and it will be super-fast
- Redis can store data in strings,list,sets,Hashes (like JSON objects), Sorted Sets

  
| Application                 | Usage                                              |
| --------------------------- | -------------------------------------------------- |
| ✅ **Login Systems**         | Store temporary sessions/tokens                    |
| 🛒 **E-commerce**           | Cache product data to reduce DB load               |
| 📈 **Analytics dashboards** | Count users, clicks, events instantly              |
| 💬 **Chat Apps**            | Send and receive real-time messages                |
| 🎮 **Games**                | Store real-time player state/scores                |
| 🧵 **Job Queues**           | Like BullMQ, Celery, etc. use Redis to queue tasks |

## Redis as Message Broker

A Message Broker is a software that helps different services talk to each other asynchronously — by sending and receiving messages.

> Think of it like a post office between services.

### Why is it needed?
In real-world apps, you often have:

- Frontend

- Backend

- Payment service

- Email service

- Notification service

- Logging service ...and more.

Instead of all of them talking directly (which can become a mess), a message broker sits in between and manages all communication.

### How it works:

Producer sends a message (like “order placed”)

Message broker receives and stores it

Consumer(s) (like email service or payment service) pick up that message and act on it

They don’t need to be online at the same time. This is called asynchronous communication.

### Example: Food Delivery App

| Component                   | What it does                                          |
| --------------------------- | ----------------------------------------------------- |
| Frontend                    | Sends `order_placed` message                          |
| Backend (Producer)          | Pushes message to Redis                               |
| Email Service (Consumer)    | Listens for `order_placed` → sends confirmation email |
| Delivery Service (Consumer) | Starts delivery workflow                              |

Redis supports Pub/Sub (Publish/Subscribe) and Queues.

<ol>
  <li> Pub/Sub (One-to-Many)</li>
  <ul>
    <li> publisher sends message to a channel</li>
    <li>All subscribers listening to that channel will receive it</li>
    
    ```
      Publisher ----> Redis Channel ----> [Subscriber 1, Subscriber 2]
    ```
    
  </ul>
  <li>Queues (One-to-One)</li>
  Use Redis Lists as queues:
  <ul>
     <li> LPUSH to add message</li>
     <li>RPOP to process message</li>

    ```
    Producer --(LPUSH)--> Redis List (Queue) --(RPOP)--> Consumer
    ```
    
  </ul>
</ol>

###  Simple Example with Redis Pub/Sub (JavaScript)

```
// Publisher
const pub = new Redis();
pub.publish("order", "New Order from Saran");

// Subscriber
const sub = new Redis();
sub.subscribe("order", () => {
  sub.on("message", (channel, message) => {
    console.log(`Received ${message} from ${channel}`);
  });
});

```

Output:

```
Received New Order from order
```
