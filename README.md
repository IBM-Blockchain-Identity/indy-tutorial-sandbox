# Abstract

A turnkey, Docker-based sandbox that enables quick and easy exploration of Hyperledger Indy concepts. This devops repo can be used to gather hands-on experience of Indy basics using the scenarios outlined in the [Sovrin's Getting Started Guide](https://github.com/hyperledger/indy-node/blob/stable/getting-started.md).

## Quick Summary commands

With just four command lines executed you have the Indy Demo ready to use.

```
$ git clone https://github.com/IBM-Blockchain-Identity/indy-tutorial-sandbox.git
$ cd indy-tutorial-sandbox
$ make indy-base
$ make local run-demo
```

# Indy Docker

A Docker file is provided that creates and configures Indy nodes and clients. The resulting Docker image can be used to instantiate the particants in the **Alice Demo** that are described in the [Sovrin's Getting Started Guide](https://github.com/hyperledger/indy-node/blob/stable/getting-started.md).

## Dependencies

While the Docker image that will be created below may run on many different versions of Docker, it was initially tested and verified on Docker v17.10.0-ce.  To see what version of Docker is currently installed on your system, run:

```
$ docker --version
```

Information on downloading and installing Docker for various platforms can be found [here](https://www.docker.com/get-docker).

## Step 1: Create the Indy Docker Image

Clone the **indy-tutorial-sandbox** repository.

```
$ git clone https://github.com/IBM-Blockchain-Identity/indy-tutorial-sandbox.git
```
   
Change to the cloned directory and use the **Makefile** target **indy-base** to create the **indy-base** Docker image.

```
$ make indy-base
```

Now, you should have a **indy-base** Docker image available to run.

```
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
indy-base               latest              0e5fe43800da        43 hours ago        1.09GB
```

## Step 2: Run the Alice Demo

You can set up and run the **Alice Demo** using the **indy-base** Docker image from Step 1.  In the cloned directory there is a **Makefile** that can be used to start and stop all of the Docker containers used for the demo.  

The **run-demo** target starts a four-node pool (Node1-Node4), sets up and runs the Faber, Acme and Thrift agents, and starts an Indy CLI.

```
$ make run-demo
```

The **Makefile** has a number of targets that perform many tasks. An attempt is made to determine the local IP address.  It can be checked using the **local** target.  If you want to use a different IP address, you can edit the Makefile and set the LOCAL variable.  

To see what your local address is you can run the command with just the **local** target.

```
$ make local 
```

After executing the **run-demo** target, you should have 8 Docker containers running.

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                              NAMES    
e26633e1d1f9        indy-base           "/bin/bash -c '   ..."   10 seconds ago      Up 11 seconds                                          Indy
41e9fcc0733f        indy-base           "/bin/bash -c 'gen..."   11 seconds ago      Up 12 seconds       0.0.0.0:7777->7777/tcp             Thrift
287accdc16a2        indy-base           "/bin/bash -c 'gen..."   12 seconds ago      Up 12 seconds       0.0.0.0:6666->6666/tcp             Acme
5d13e6af5836        indy-base           "/bin/bash -c 'gen..."   13 seconds ago      Up 13 seconds       0.0.0.0:5555->5555/tcp             Faber
70126d9120f0        indy-base           "/bin/bash -c 'ini..."   13 seconds ago      Up 14 seconds       0.0.0.0:9707-9708->9707-9708/tcp   Node4
5305fcb69354        indy-base           "/bin/bash -c 'ini..."   14 seconds ago      Up 15 seconds       0.0.0.0:9705-9706->9705-9706/tcp   Node3
63932d40357e        indy-base           "/bin/bash -c 'ini..."   15 seconds ago      Up 15 seconds       0.0.0.0:9703-9704->9703-9704/tcp   Node2
7e9f2f93f41e        indy-base           "/bin/bash -c 'ini..."   15 seconds ago      Up 16 seconds       0.0.0.0:9701-9702->9701-9702/tcp   Node1
```

When the Indy container starts, it runs several Indy commands that set up the agents.  Once the agents are operational, you are at the **indy>** prompt and the demo environment is ready for use.  You can now follow the **Alice Demo** scenario.  

The following commands are from the demo script and can be used to test that the demo environment is working correctly.

```
indy@sandbox> prompt ALICE
ALICE@sandbox> new wallet Alice
ALICE@sandbox> show sample/faber-request.indy
ALICE@sandbox> load sample/faber-request.indy
ALICE@sandbox> show connection "Faber College"
ALICE@sandbox> accept request from "Faber College"
ALICE@sandbox> show claim Transcript
ALICE@sandbox> request claim Transcript
ALICE@sandbox> show claim Transcript
```

The entire **Alice Demo** can be run using the **run-alice** target.  This does everything that the **run-demo** target does, plus executes the remaining Indy commands to run the entire demo.  

You will be left at the **indy>** prompt, allowing you to explore additional commands.  To get a list of all Indy commands, enter **help**.  

The **exit** command will exit the Indy command prompt and leave you at the bash shell command line.  You can explore the file system or run the Indy command prompt again by typing **indy**.

There are several directories under **~/.indy-cli** that might be interesting to explore.  The network configuration is in the **~/.indy-cli/networks/sandbox** directory, and the wallets are in the **~/.indy-cli/wallets/sandbox** directory.


## Makefile Targets

The following **Makefile** targets can be used to start and stop the Docker containers and set up the demo environment used for the **Alice Demo**.

**indy-base**

* Create the Docker image that is used for both Indy nodes and clients.

**local**

* Find the local host IP address.

**run-demo**

* Start all Indy node, Indy agents and Indy CLI used for the **Alice Demo**.  This also automatically executes several Indy commands that set up the agents before leaving you at the **indy>** prompt.

**run-alice**

* Start all Indy node, Indy agents and Indy CLI used for the **Alice Demo**.  This also automatically executes all of the Indy commands that run the entire Alice demo before leaving you at the **indy>** prompt.

**indy-cli**

* Start a new Indy CLI client leaving you at the **indy>** prompt.

**stop**

* Stop all Docker containers used for the **Alice Demo**.

**start**

* Start all stopped Docker containers used for the **Alice Demo** that were stopped using the **stop** target.

**clean**

* Stop and remove all Docker containers used for the **Alice Demo**.

## Troubleshooting

Some failures running through the demo can be due to failure to contact the various service endpoints.  Verify the IP addresses that the makefile is using and edit the Makefile LOCAL variable as necessary.



## Using the Docker Image

The **indy-base** Docker image is used for both Indy nodes and clients.  

You can run the Docker image and interact with it using a bash shell.

```
$ docker run -it --rm indy-base /bin/bash
```

To start the Docker image as an Indy client:

```
$ docker run -it --rm indy-base /bin/bash
# indy
Loading module /usr/local/lib/python3.5/dist-packages/config/config-crypto-example1.py
Module loaded.

Indy-CLI (c) 2017 Evernym, Inc.
Type 'help' for more information.
Running Indy 1.2

indy> 
```

To start the docker image as an Indy node:

```
$ docker run -it --rm indy-base /bin/bash
# init_indy_keys --name Alpha
# start_indy_node Alpha 9701 9702
```

You can connect to an existing node:

```
$ docker exec -it Node1 /bin/bash
```

## Cleanup

To stop and remove the created Docker containers from your system:

```
$ make clean
```

To remove the Docker image from your system:

```
$ docker rmi indy-base
```

## Links

* [Getting Started with Indy](https://github.com/hyperledger/indy-node/blob/stable/getting-started.md)
* [Indy Node](https://github.com/hyperledger/indy-node)
* [Indy – Running the Getting Started tutorial locally](https://github.com/hyperledger/indy-node/blob/master/docs/indy-running-locally.md)
* [Create a Network and Start Nodes](https://github.com/hyperledger/indy-node/blob/master/docs/start-nodes.md)

<br>
<hr>

# Appendix

## Log of run-alice

```
$ make run-alice
```

```
BLS Public key is 4N8aUNHSgjQVgkpm8nhNEfDf6txHznoYREg9kirmJrkivgL4oSEimFF6nsQ6M41QvhM2Z33nves5vfSn9n1UwNFJBYtWVnHYMATn76vLuL3zU88KyeAYcHfsih3He6UHcXDxcaecHVz6jhCYz1P2UZn2bDVruL5wXpehgBfBaLKm3Ba
BLS Public key is 37rAPpXVoxzKhz7d9gkUe52XuXryuLXoM6P6LbWDB7LSbG62Lsb33sfG7zqS8TK1MXwuCHj1FKNzVpsnafmqLG1vXN88rt38mNFs9TENzm4QHdBzsvCuoBnPH7rpYYDo9DZNJePaDvRvqJKByCabubJz3XXKbEeshzpz4Ma5QYpJqjk
BLS Public key is 3WFpdbg7C5cnLYZwFZevJqhubkFALBfCBBok15GdrKMUhUjGsk3jV6QKj6MZgEubF7oqCafxNdkm7eswgA4sdKTRc82tLGzZBd6vNqU8dupzup6uYUf32KTHTPQbuUM8Yk4QFXjEf2Usu2TJcNkdgpyeUSX42u5LqdDDpNSWUK5deC5
BLS Public key is 2zN3bHM1m4rLz54MJHYSwvqzPchYp8jkHswveCLAEJVcX6Mm1wHQD1SkPYMzUDTZvWvhuE6VNAkK3KxVeEmsanSmvjVkReDeBEMxeDaayjcZjFGPydyey1qxBHmTvAnBKoPydvuTAqx5f7YNNRAdeLmUi99gERUU7TD8KfAa6MpQ9bw
Loading module /usr/local/lib/python3.5/dist-packages/config/config-crypto-example1.py
Module loaded.

Indy-CLI (c) 2017 Evernym, Inc.
Type 'help' for more information.
Running Indy 1.3.410
```

Running command: 'connect sandbox'...

```
New wallet Default created
Active wallet set to "Default"
Active wallet set to "Default"

indy063764 updated its pool parameters: f 1, totalNodes 4,minNodesToConnect 2, quorums {'ledger_status_last_3PC': Quorum(2), 'checkpoint': Quorum(2), 'consistency_proof': Quorum(2), 'election': Quorum(3), 'bls_signatures': Quorum(3), 'propagate_primary': Quorum(2), 'observer_data': Quorum(2), 'reply': Quorum(2), 'view_change_done': Quorum(3), 'propagate': Quorum(2), 'timestamp': Quorum(2), 'commit': Quorum(3), 'f': 1, 'ledger_status': Quorum(2), 'same_consistency_proof': Quorum(2), 'prepare': Quorum(2), 'view_change': Quorum(3)}

Client indy063764 initialized with the following node registry:
    Node1C listens at 192.168.1.64 on port 9702
    Node2C listens at 192.168.1.64 on port 9704
    Node3C listens at 192.168.1.64 on port 9706
    Node4C listens at 192.168.1.64 on port 9708

Active client set to indy063764

CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi listening for other nodes at 0.0.0.0:6001
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi looking for Node1C at 192.168.1.64:9702
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi looking for Node2C at 192.168.1.64:9704
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi looking for Node3C at 192.168.1.64:9706
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi looking for Node4C at 192.168.1.64:9708
Connecting to sandbox...
9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi's connections changed from set() to {'Node1C'}
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi now connected to Node1C
9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi's connections changed from {'Node1C'} to {'Node1C', 'Node2C'}
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi now connected to Node2C
9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi's connections changed from {'Node1C', 'Node2C'} to {'Node1C', 'Node2C', 'Node3C', 'Node4C'}
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi now connected to Node3C
CONNECTION: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi now connected to Node4C
CATCH-UP: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi completed catching up ledger 0, caught up 0 in total
CATCH-UP: 9Hn7wpAYkbK4dmnSb2dvLJqS2Np8kKyeocr7bzVWXtoi completed catching up ledger 0, caught up 0 in total
Connected to sandbox.
```

Running command: 'new key with seed 000000000000000000000000Steward1'...

```
Key created in wallet Default
DID for key is Th7MpTaRZVRYnPiabds81Y
Verification key is ~7TYfekw4GUagBnBVCqPjiC
Current DID set to Th7MpTaRZVRYnPiabds81Y
```

Running command: 'send NYM dest=ULtgFQJe6bjiFbs7ke3NJD role=TRUST_ANCHOR verkey=~5kh3FB4H3NKq7tUDqeqHc1'...

```
Adding nym ULtgFQJe6bjiFbs7ke3NJD
Nym ULtgFQJe6bjiFbs7ke3NJD added
```

Running command: 'send NYM dest=CzkavE58zgX7rUMrzSinLr role=TRUST_ANCHOR verkey=~WjXEvZ9xj4Tz9sLtzf7HVP'...

```
Adding nym CzkavE58zgX7rUMrzSinLr
Nym CzkavE58zgX7rUMrzSinLr added
```

Running command: 'send NYM dest=H2aKRiDeq8aLZSydQMDbtf role=TRUST_ANCHOR verkey=~3sphzTb2itL2mwSeJ1Ji28'...

```
Adding nym H2aKRiDeq8aLZSydQMDbtf
Nym H2aKRiDeq8aLZSydQMDbtf added
```

Running command: 'new key with seed Faber000000000000000000000000000'...

```
Key created in wallet Default
DID for key is ULtgFQJe6bjiFbs7ke3NJD
Verification key is ~5kh3FB4H3NKq7tUDqeqHc1
Current DID set to ULtgFQJe6bjiFbs7ke3NJD
```

Running command: 'send ATTRIB dest=ULtgFQJe6bjiFbs7ke3NJD raw={"endpoint": {"ha": "192.168.1.64:5555", "pubkey": "5hmMA64DDQz5NzGJNVtRzNwpkZxktNQds21q3Wxxa62z"}}'...

```
Adding attributes {"endpoint": {"ha": "192.168.1.64:5555", "pubkey": "5hmMA64DDQz5NzGJNVtRzNwpkZxktNQds21q3Wxxa62z"}} for ULtgFQJe6bjiFbs7ke3NJD
Attribute added for nym ULtgFQJe6bjiFbs7ke3NJD
```

Running command: 'new key with seed Acme0000000000000000000000000000'...

```
Key created in wallet Default
DID for key is CzkavE58zgX7rUMrzSinLr
Verification key is ~WjXEvZ9xj4Tz9sLtzf7HVP
Current DID set to CzkavE58zgX7rUMrzSinLr
```

Running command: 'send ATTRIB dest=CzkavE58zgX7rUMrzSinLr raw={"endpoint": {"ha": "192.168.1.64:6666", "pubkey": "C5eqjU7NMVMGGfGfx2ubvX5H9X346bQt5qeziVAo3naQ"}}'...

```
Adding attributes {"endpoint": {"ha": "192.168.1.64:6666", "pubkey": "C5eqjU7NMVMGGfGfx2ubvX5H9X346bQt5qeziVAo3naQ"}} for CzkavE58zgX7rUMrzSinLr
Attribute added for nym CzkavE58zgX7rUMrzSinLr
```

Running command: 'new key with seed Thrift00000000000000000000000000'...

```
Key created in wallet Default
DID for key is H2aKRiDeq8aLZSydQMDbtf
Verification key is ~3sphzTb2itL2mwSeJ1Ji28
Current DID set to H2aKRiDeq8aLZSydQMDbtf
```

Running command: 'send ATTRIB dest=H2aKRiDeq8aLZSydQMDbtf raw={"endpoint": {"ha": "192.168.1.64:7777", "pubkey": "AGBjYvyM3SFnoiDGAEzkSLHvqyzVkXeMZfKDvdpEsC2x"}}'...

```
Adding attributes {"endpoint": {"ha": "192.168.1.64:7777", "pubkey": "AGBjYvyM3SFnoiDGAEzkSLHvqyzVkXeMZfKDvdpEsC2x"}} for H2aKRiDeq8aLZSydQMDbtf
Attribute added for nym H2aKRiDeq8aLZSydQMDbtf
```

Running command: 'save wallet'...

```
Active wallet "Default" saved (/root/.indy-cli/wallets/sandbox/default.wallet)
```

Running command: 'prompt ALICE'...

Running command: 'new wallet Alice'...

```
Active wallet "Default" saved (/root/.indy-cli/wallets/sandbox/default.wallet)
New wallet Alice created
Active wallet set to "Alice"
```

Running command: 'load sample/faber-request.indy'...

```
indy063764 is already started, so start has no effect
1 connection request found for Faber College.
Creating connection for Faber College.

Try Next:
    show connection "Faber College"
    accept request from "Faber College"
```

Running command: 'show connection Faber'...

```
Expanding Faber to "Faber College"
Connection (not yet accepted)
    Name: Faber College
    DID: not yet assigned
    Trust anchor: Faber College (not yet written to Indy)
    Verification key: <empty>
    Signing key: <hidden>
    Remote: ULtgFQJe6bjiFbs7ke3NJD
    Remote Verification key: <unknown, waiting for sync>
    Remote endpoint: <unknown, waiting for sync>
    Request nonce: b1134a647eb818069c089e7694f63e6d
    Request status: not verified, remote verkey unknown
    Last synced: <this connection has not yet been synchronized>

Try Next:
    sync "Faber College"
    accept request from "Faber College"
```

Running command: 'accept request from Faber'...

```
Expanding Faber to "Faber College"
Request not yet verified.
Connection not yet synchronized.
Attempting to sync...
No key present in wallet for making request on Indy, so adding one
Key created in wallet Alice
DID for key is T46mX2vcg4Y2a46yUebpUZ
Verification key is ~Xfb8idWcB4LpyfUyUs6VAJ
Current DID set to T46mX2vcg4Y2a46yUebpUZ

Synchronizing...
    Connection Faber College synced
    Connection Faber College synced
Accepting request with nonce b1134a647eb818069c089e7694f63e6d from id ULtgFQJe6bjiFbs7ke3NJD
CONNECTION: 744760 looking for Faber College at 192.168.1.64:5555

Signature accepted.

Response from Faber College (12.89 ms):
    Trust established.
    DID created in Indy.
    Available Claim(s): Transcript

Synchronizing...
    Confirmed DID written to Indy.

Try Next:
    show claim "Transcript"
    request claim "Transcript"
```

Running command: 'show claim Transcript'...

```
Found claim Transcript in connection Faber College
Status: available (not yet issued)
Name: Transcript
Version: 1.2
Attributes:
    student_name
    ssn
    degree
    year
    status

Try Next:
    request claim "Transcript"
```

Running command: 'request claim Transcript'...

```
Found claim Transcript in connection Faber College
Requesting claim Transcript from Faber College...

Signature accepted.

Response from Faber College (16.85 ms):
    Received claim "Transcript".
```

Running command: 'show claim Transcript'...

```
Found claim Transcript in connection Faber College
Status: 2018-05-11 11:37:07.572193
Name: Transcript
Version: 1.2
Attributes:
    student_name: Alice Garcia
    ssn: 123-45-6789
    degree: Bachelor of Science, Marketing
    year: 2015
    status: graduated
```

Running command: 'save wallet'...

```
Active wallet "Alice" saved (/root/.indy-cli/wallets/sandbox/alice.wallet)
```

Running command: 'load sample/acme-job-application.indy'...

```
1 connection request found for Acme Corp.
Creating connection for Acme Corp.

Try Next:
    show connection "Acme Corp"
    accept request from "Acme Corp"
```

Running command: 'accept request from Acme'...

```
Expanding Acme to "Acme Corp"
Request not yet verified.
Connection not yet synchronized.
Attempting to sync...

Synchronizing...
    Connection Acme Corp synced
    Connection Acme Corp synced
Accepting request with nonce 57fbf9dc8c8e6acde33de98c6d747b28c from id CzkavE58zgX7rUMrzSinLr
CONNECTION: 744760 looking for Acme Corp at 192.168.1.64:6666

Signature accepted.

Response from Acme Corp (4.94 ms):
    Trust established.
    DID created in Indy.

Synchronizing...
    Confirmed DID written to Indy.

Try Next:
    show proof request "Job-Application"
    send proof "Job-Application" to "Acme Corp"
```

Running command: 'show proof request Job-Application'...

```
Found proof request "Job-Application" in connection "Acme Corp"
Status: Requested
Name: Job-Application
Version: 0.2
Attributes:
    first_name: string
    last_name: string
    phone_number: string
    degree (V): Bachelor of Science, Marketing
    status (V): graduated
    ssn (V): 123-45-6789


The Proof is constructed from the following claims:

    Claim (Transcript v1.2 from Faber College)
          student_name: Alice Garcia
        * ssn: 123-45-6789
        * degree: Bachelor of Science, Marketing
          year: 2015
        * status: graduated


Try Next:
    set <attr-name> to <attr-value>
    send proof "Job-Application" to "Acme Corp"
```

Running command: 'set first_name to Alice'...


Running command: 'set last_name to Garcia'...


Running command: 'set phone_number to 123-456-7890'...


Running command: 'show proof request Job-Application'...

```
Found proof request "Job-Application" in connection "Acme Corp"
Status: Requested
Name: Job-Application
Version: 0.2
Attributes:
    first_name: Alice
    last_name: Garcia
    phone_number: 123-456-7890
    degree (V): Bachelor of Science, Marketing
    status (V): graduated
    ssn (V): 123-45-6789


The Proof is constructed from the following claims:

    Claim (Transcript v1.2 from Faber College)
          student_name: Alice Garcia
        * ssn: 123-45-6789
        * degree: Bachelor of Science, Marketing
          year: 2015
        * status: graduated


Try Next:
    set <attr-name> to <attr-value>
    send proof "Job-Application" to "Acme Corp"
```

Running command: 'send proof Job-Application to Acme'...

```
Signature accepted.
```

Running command: 'show connection Acme'...

```
Expanding Acme to "Acme Corp"
Connection
    Name: Acme Corp
    DID: NACAzr6T7sRGqauSE2Sbif
    Trust anchor: Acme Corp (confirmed)
    Verification key: ~BA8PGdm88sLmniLLk8aMaG
    Signing key: <hidden>
    Remote: CzkavE58zgX7rUMrzSinLr
    Remote Verification key: ~WjXEvZ9xj4Tz9sLtzf7HVP
    Remote endpoint: 192.168.1.64:6666
    Request nonce: 57fbf9dc8c8e6acde33de98c6d747b28c
    Request status: Accepted
    Proof Request(s): Job-Application
    Last synced: 20 seconds ago

Try Next:
    show proof request "Job-Application"
    send proof "Job-Application" to "Acme Corp"

Response from Acme Corp (3.0 ms):
    Your Proof Job-Application 0.2 was received and verified


Response from Acme Corp (7.5 ms):
    Available Claim(s): Job-Certificate
```

Running command: 'show claim Job-Certificate'...

```
Found claim Job-Certificate in connection Acme Corp
Status: available (not yet issued)
Name: Job-Certificate
Version: 0.2
Attributes:
    first_name
    last_name
    employee_status
    experience
    salary_bracket

Try Next:
    request claim "Job-Certificate"
```

Running command: 'request claim Job-Certificate'...

```
Found claim Job-Certificate in connection Acme Corp
Requesting claim Job-Certificate from Acme Corp...

Signature accepted.

Response from Acme Corp (22.63 ms):
    Received claim "Job-Certificate".
```

Running command: 'show claim Job-Certificate'...

```
Found claim Job-Certificate in connection Acme Corp
Status: 2018-05-11 11:37:46.638892
Name: Job-Certificate
Version: 0.2
Attributes:
    first_name: Alice
    last_name: Garcia
    employee_status: Permanent
    experience: 3 years
    salary_bracket: between $50,000 to $100,000
```

Running command: 'load sample/thrift-loan-application.indy'...

```
1 connection request found for Thrift Bank.
Creating connection for Thrift Bank.

Try Next:
    show connection "Thrift Bank"
    accept request from "Thrift Bank"
```

Running command: 'accept request from Thrift'...

```
Expanding Thrift to "Thrift Bank"
Request not yet verified.
Connection not yet synchronized.
Attempting to sync...

Synchronizing...
    Connection Thrift Bank synced
    Connection Thrift Bank synced
Accepting request with nonce 77fbf9dc8c8e6acde33de98c6d747b28c from id H2aKRiDeq8aLZSydQMDbtf
CONNECTION: 744760 looking for Thrift Bank at 192.168.1.64:7777

Signature accepted.

Response from Thrift Bank (3.13 ms):
    Trust established.
    DID created in Indy.

Synchronizing...
    Confirmed DID written to Indy.

Try Next:
    show proof request "Loan-Application-Basic"
    send proof "Loan-Application-Basic" to "Thrift Bank"
    show proof request "Loan-Application-KYC"
    send proof "Loan-Application-KYC" to "Thrift Bank"
    show proof request "Name-Proof"
    send proof "Name-Proof" to "Thrift Bank"
```

Running command: 'show proof request Loan-Application-Basic'...

```
Found proof request "Loan-Application-Basic" in connection "Thrift Bank"
Status: Requested
Name: Loan-Application-Basic
Version: 0.1
Attributes:
    salary_bracket (V): between $50,000 to $100,000
    employee_status (V): Permanent


The Proof is constructed from the following claims:

    Claim (Job-Certificate v0.2 from Acme Corp)
          first_name: Alice
          last_name: Garcia
        * employee_status: Permanent
          experience: 3 years
        * salary_bracket: between $50,000 to $100,000


Try Next:
    set <attr-name> to <attr-value>
    send proof "Loan-Application-Basic" to "Thrift Bank"
```

Running command: 'send proof Loan-Application-Basic to Thrift'...

```
Signature accepted.

Running command: 'show proof request Loan-Application-KYC'...

Found proof request "Loan-Application-KYC" in connection "Thrift Bank"
Status: Requested
Name: Loan-Application-KYC
Version: 0.1
Attributes:
    [2] first_name (V): Alice
    [2] last_name (V): Garcia
    [1] ssn (V): 123-45-6789


The Proof is constructed from the following claims:

    Claim [1] (Transcript v1.2 from Faber College)
          student_name: Alice Garcia
        * ssn: 123-45-6789
          degree: Bachelor of Science, Marketing
          year: 2015
          status: graduated

    Claim [2] (Job-Certificate v0.2 from Acme Corp)
        * first_name: Alice
        * last_name: Garcia
          employee_status: Permanent
          experience: 3 years
          salary_bracket: between $50,000 to $100,000


Try Next:
    set <attr-name> to <attr-value>
    send proof "Loan-Application-KYC" to "Thrift Bank"


Response from Thrift Bank (3.02 ms):
    Your Proof Loan-Application-Basic 0.1 was received and verified

    Loan eligibility criteria satisfied, please send another claim 'Loan-Application-KYC'
```

Running command: 'send proof Loan-Application-KYC to Thrift'...

```
Signature accepted.

Running command: 'save wallet'...

Active wallet "Alice" saved (/root/.indy-cli/wallets/sandbox/alice.wallet)

Response from Thrift Bank (2.63 ms):
    Your Proof Loan-Application-KYC 0.1 was received and verified
```

```
ALICE@sandbox> 
```
