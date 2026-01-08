# Scheduler Contract

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

The **Scheduler Contract in Zetrix Blockchain** is designed to support a standard interface for communication with other smart contracts. It enables seamless interaction between different contracts and facilitates task scheduling and execution based on a predefined cron job timing. Here's a breakdown of its functionality:

1. **Standard Interface:** The Scheduler Contract provides a standardized interface that other smart contracts can use to communicate with it. This interface likely includes functions for registering and deregistering tasks.
2. **Task Registration:** Smart contracts that require scheduled execution of specific tasks can communicate with the Scheduler Contract to register those tasks. During registration, the smart contract specifies the details of the task, such as the function to be executed, the timing for execution, and any required parameters.
3. **Cron Job Timing:** The Scheduler Contract utilizes a cron job timing mechanism to automatically execute the registered tasks based on the specified schedule. Cron job timing allows for the precise configuration of task execution intervals, such as daily, weekly, monthly, or custom-defined intervals.
4. **Automatic Execution:** Once a task is registered with the Scheduler Contract, it will be automatically executed according to the specified cron job timing. This eliminates the need for manual intervention or triggering of task execution by external parties.

By leveraging the Scheduler Contract's standard interface, smart contracts within the Zetrix Blockchain ecosystem can seamlessly interact with the Scheduler Contract, register their tasks, and rely on the automatic execution based on the defined schedule. This functionality simplifies the management of scheduled tasks within the blockchain network, streamlines communication between contracts, and enhances the efficiency of decentralized applications built on Zetrix Blockchain.

## Design Architecture

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

In the Zetrix Blockchain ecosystem, users can connect to the Scheduler Contract using a standard protocol from their Smart Contract or SDK. This allows for seamless integration and interaction between various components. Here's an explanation of the process:

1. **User Integration:** Users can integrate their Smart Contracts or SDKs with the Scheduler Contract using a standard protocol. This protocol provides a set of functions and methods that enable communication and interaction with the Scheduler Contract.
2. **Periodic List Check**: A common scheduler, which is a component outside the Scheduler Contract, periodically checks the running list stored within the Scheduler Contract. This check occurs every 40 seconds or after the maximum block validation time.
3. **Task Owner's Balance Check:** Before executing a task, the common scheduler verifies the remaining balance of the task owner. This check ensures that the owner has sufficient funds or resources to cover the execution of the task.
4. **Callback Trigger:** Upon reaching the set cron timing, the common scheduler triggers the callback function in the Scheduler Contract. The cron timing specifies when a task should be executed based on a predetermined schedule.
5. **Delegation to Callback Address:** The Scheduler Contract delegates the task execution by making a call to the callback address specified during task registration. This ensures that the desired function or operation defined in the callback address is executed as per the task's requirements.

By connecting their Smart Contracts or SDKs to the Scheduler Contract using the standard protocol, users can effectively schedule and execute tasks based on the defined cron timing.

## Operation Flow

<figure><img src="../../.gitbook/assets/image (17) (1) (1).png" alt=""><figcaption><p>Operation Flow</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption><p>Operation flow</p></figcaption></figure>

## Communication With Scheduler Contract

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Protocol For Registration

| Item            | Description                                                                    |
| --------------- | ------------------------------------------------------------------------------ |
| taskName        | Example: ”My Task”                                                             |
| cronTime        | Example: 0 0/1 \* \* \* \*                                                     |
| callbackAddress | User Contract Address (ZTX….)                                                  |
| callbackMethod  | Predefined Method in User Contract                                             |
| callbackParams  | <p>Key Value json form</p><p>example:</p><p>{</p><p>   “id”: “xxx”</p><p>}</p> |

## Example (Register Task)

Scheduler Contract : [https://test-explorer.zetrix.com/account/ZTX3Kfprq7zvFb8KXNEAbWZSZScZfVuFJCtuA](https://test-explorer.zetrix.com/account/ZTX3Kfprq7zvFb8KXNEAbWZSZScZfVuFJCtuA)

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Example (Running Task)

Scheduler Contract : [https://test-explorer.zetrix.com/account/ZTX3Kfprq7zvFb8KXNEAbWZSZScZfVuFJCtuA](https://test-explorer.zetrix.com/account/ZTX3Kfprq7zvFb8KXNEAbWZSZScZfVuFJCtuA)

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>
