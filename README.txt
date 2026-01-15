UDP Chat Application

A lightweight command-line chat app built on UDP sockets. It lets multiple clients communicate in real time with minimal overhead.

Features
- Simple UDP-based messaging
- No long-lived (TCP-style) persistent connection required
- Supports multiple clients
- Low-overhead, fast message delivery (best effort)

Requirements
- Python 3.8+
- Runs on macOS / Linux / Windows

How to Run

1) Start the server

python3 ChatApp.py -s <server_port>

2) Start a client (use multiple terminals to simulate multiple users)

python3 ChatApp.py -c <username> <server_ip> <server_port> <client_port>

Example:

python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000

Usage

- Enter a supported command at the prompt and press Enter
- Use Ctrl + C to quit the program

Notes

- UDP does not guarantee delivery or ordering of packets
- This project is best for practicing networking fundamentals and low-latency communication concepts


EXAMPLES

Starting the server/client

Start server:
python3 ChatApp.py -s 5000

The port can be any valid port in range. If it starts successfully, you should see:
>>> [Server is online...]

Start client:
python3 ChatApp.py -c userA localhost 5000 6000

You may choose any username (e.g., userA/userB/userC/userD/userE...). The server IP can be localhost, and the client port can be any valid port in range. On success, the client will print the welcome message, a “client table updated” message, and the current table, for example:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}


Commands

send: send a private message
send userD hii

- The first token must be "send"
- The second token is the recipient username
- Everything after that is treated as the message

If userD is online and the send succeeds, you will see:
>>> [Message sent!]
>>> [Message received by userD.]

On userD’s side, the message appears as:
>>> userA: hii


send_all: send a message to everyone (group chat)
send_all hii

For group chat you do not specify a username. For example, if userA runs "send_all hii" while userB and userD are online, both userB and userD will receive:
>>> Group Chat userA: hii

Meanwhile, the server prints:
>>> [Client userA sent group message: hii ]


dereg: deregister (go Offline)
dereg userA

This sets the user status to Offline. The client stays running, but it will not receive messages while Offline. If deregistration succeeds, the client shows:
>>> [You are Offline. Bye.]

The server updates and broadcasts the table to all users. For example:
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userD': ['127.0.0.1', 6003, 'Online']}

If userD sends a private message to userA while userA is Offline, userD will see something like:
>>> [Offline Message sent at 2023-10-21 02:14:41 received by the server and saved.]

The message is stored in the server’s memory for later delivery.


reg: re-register (go Online again)
reg userA

When a user registers again, they become Online and will receive any saved offline messages. If successful, the client prints the updated table, then the offline messages list, including sender and timestamp. Example:
>>> [Client table updated.]
>>> [You have offline messages:]
>>> userD: 02:14:41 hii

The original sender (userD) also receives a delivery notification:
>>> [Offline Message sent at 02:14:41 received by userA]

This offline-message behavior also applies to group chat. For example, if userA is Offline, userB sends a group message "hi", and userD sends a private message "hello", then after "reg userA", userA will see:
>>> [Client table updated.]
>>> [You have offline messages:]
>>> Group_Chat:userD: 02:22:40 hi
>>> userB: 02:22:49 hello


Test case 1:
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
python3 ChatApp.py -c userC localhost 5000 6002
userA:send userB hi
userB:send userC hi
userC:send userA hi
userA:dereg userA
userB:send userA hello
userC:send userA hello
userA:reg userA
userA,userB,userC:exit

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send userB hi
>>> [Message sent!]
>>> [Message received by userB.]
>>> userC: hi 
dereg userA
>>> [You are Offline. Bye.]
reg userA
>>> [Client table updated.]
>>> [You have offline messages:]
>>> userB: 02:40:58 hello 
>>> userC: 02:41:03 hello 
>>> ^C

userB output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> userA: hi 
send userC hi
>>> [Message sent!]
>>> [Message received by userC.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send userA hello
>>> [Offline Message sent at 2023-10-21 02:40:58 received by the server and saved.]
>>> [Offline Message sent at 02:40:58 received by userA]
^C

userC output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> userB: hi 
send userA hi
>>> [Message sent!]
>>> [Message received by userA.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send userA hello
>>> [Offline Message sent at 2023-10-21 02:41:03 received by the server and saved.]
>>> [Offline Message sent at 02:41:03 received by userA]
^C


Test case 2:
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
python3 ChatApp.py -c userC localhost 5000 6002
server exit
userB: dereg userB
userA: send userC hi

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send userC hi
>>> [Message sent!]
>>> [Message received by userC.]
>>> 

userB output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
dereg userB
>>> [Message sent! 1 time(s) retrying...]
>>> [Message sent! 2 time(s) retrying...]
>>> [Message sent! 3 time(s) retrying...]
>>> [Message sent! 4 time(s) retrying...]
>>> [Message sent! 5 time(s) retrying...]
>>> [Server not responding.]
>>> [Exiting...]

userC output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> userA: hi 


Test case 3:
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
python3 ChatApp.py -c userC localhost 5000 6002
python3 ChatApp.py -c userD localhost 5000 6003
userD:dereg userD
userA:send_all hi
userB:send userD hello
userD:reg userD

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client userA sent group message: hi ]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Offline']}
send_all hi
>>> [Group Message received by Server.]
>>> [No ACK from userD, message not delivered.]
>>> [Offline Message sent at 02:55:38 received by the server and saved.]
>>> [Offline Message sent at 02:55:38 received by userD]

userB output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Offline']}
>>> Group Chat userA: hi 
send userD hello
>>> [Offline Message sent at 2023-10-21 02:55:50 received by the server and saved.]
>>> [Offline Message sent at 02:55:50 received by userD]

userC output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Offline']}
>>> Group Chat userA: hi 

userD output:
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online'], 'userD': ['127.0.0.1', 6003, 'Online']}
dereg userD
>>> [You are Offline. Bye.]
>>> reg userD
>>> [Client table updated.]
>>> [You have offline messages:]
>>> Group_Chat:userA: 02:55:38 hi 
>>> userB: 02:55:50 hello 
>>> 


Test case 4
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
userB: exit (no dereg)
userA: send_all hi
userA: send userA hi
userA: send userA hi hi
server: exit
userA: send userA hi hi

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client userA sent group message: hi ]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
send_all hi
>>> [Group Message received by Server.]
>>> [No ACK from userB, message not delivered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Offline']}
send userA hi
>>> [Message sent!]
>>> userA: hi 
>>> [Message received by userA.]
>>> send userA hi hi
>>> [Message sent!]
>>> userA: hi hi 
>>> [Message received by userA.]
>>> send userA hi hi
>>> [Message sent!]
>>> userA: hi hi 
>>> [Message received by userA.]
>>> send userA hi hi
>>> [Message sent!]
>>> userA: hi hi 
>>> [Message received by userA.]

userB output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}


Test case 5
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
userB: dereg userA
userB: reg userB
userA: dereg userA
userB: reg userA
userB: send userA hi hi
userA: reg userA

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
dereg userA
>>> [You are Offline. Bye.]
>>> reg userA
>>> [Client table updated.]
>>> [You have offline messages:]
>>> userB: 23:48:57 hi hi 

userB output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
dereg userA
>>> [Incorrect username.]
>>> reg userB
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online']}
reg userA
>>> [Incorrect username.]
>>> send userA hi hi
>>> [Offline Message sent at 2023-11-08 23:48:57 received by the server and saved.]
>>> >>> [Offline Message sent at 23:48:57 received by userA]


Test case 6
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
python3 ChatApp.py -c userB localhost 5000 6001(duplicate reg)
python3 ChatApp.py -c userC localhost 5000 6002
userA: send_all hello
userA: dereg userA
userC: send userA hi
server: exit
userC: send_all hellooo
userA: reg userA

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Duplicate login detected.]
>>> [Client table updated.]
>>> [Client userA sent group message: hello ]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send_all hello
>>> [Group Message received by Server.]
>>> dereg userA
>>> [You are Offline. Bye.]
>>> reg userA
>>> 

userB output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Welcome, You are registered.]
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> Group Chat userA: hello 
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}

userC output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> Group Chat userA: hello 
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send userA hi
>>> [Offline Message sent at 2023-11-09 01:17:25 received by the server and saved.]
>>> send_all hellooo
>>> [Message sent! 1 time(s) retrying...]
>>> [Message sent! 2 time(s) retrying...]
>>> [Message sent! 3 time(s) retrying...]
>>> [Message sent! 4 time(s) retrying...]
>>> [Message sent! 5 time(s) retrying...]
>>> [Server not responding.]
>>> [Exiting...]


Test case 7
python3 ChatApp.py -s 5000
python3 ChatApp.py -c userA localhost 5000 6000
python3 ChatApp.py -c userB localhost 5000 6001
python3 ChatApp.py -c userC localhost 5000 6002
userA: dereg userA
userB: send userA hii
userB: exit
userC: send_all hello
userC: send userA hi hi
userA: reg userA

server output:
>>> [Server is online...]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client table updated.]
>>> [Client userC sent group message: hello ]
>>> [Client table updated.]

userA output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
dereg userA
>>> [You are Offline. Bye.]
>>> reg userA
>>> [Client table updated.]
>>> [You have offline messages:]
>>> userB: 03:31:44 hii 
>>> Group_Chat:userC: 03:31:59 hello 
>>> userC: 03:32:05 hi hi 
>>> 

userB output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send userA hii
>>> [Offline Message sent at 2023-11-09 03:31:44 received by the server and saved.]

userC output:
>>> [Welcome, You are registered.]
>>> >>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Online'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
>>> [Client table updated.]
>>> {'userA': ['127.0.0.1', 6000, 'Offline'], 'userB': ['127.0.0.1', 6001, 'Online'], 'userC': ['127.0.0.1', 6002, 'Online']}
send_all hello
>>> [Group Message received by Server.]
>>> [No ACK from userA, message not delivered.]
>>> [Offline Message sent at 03:31:59 received by the server and saved.]
>>> send userA hi hi
>>> [Offline Message sent at 2023-11-09 03:32:05 received by the server and saved.]
>>> >>> [Offline Message sent at 03:31:59 received by userA]
>>> [Offline Message sent at 03:32:05 received by userA]
