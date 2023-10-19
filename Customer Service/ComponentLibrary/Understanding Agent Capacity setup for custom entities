# Understanding Agent Capacity setup for custom entities
## Author: Joseph Thomas
## Contributors: Shreyansh Jain, Aparajita Rai
## Summary
In Dynamics 365 Customer Service in order to leverage Capacity Based Routing for any entity other than the case (incident) entity some additional configuration is necessary. We will outline two options to achieve this, a no-code option and a low-code option. 
This and many other resources for your implementation are available here ([Dynamics 365 Guidance](https://learn.microsoft.com/en-us/dynamics365/guidance/#implementation-optimization-resources)). More specifically, Microsoft has published a sample library to aid you in your implementation and setup here ([Dynamics 365 Customer Service implementation optimization resources](https://learn.microsoft.com/en-us/dynamics365/guidance/resources/cs-index)). There is a wealth of details available born out of real customer deployments designed to significantly reduce your time to value.
Agent Capacity Recap
Let’s review the Unified Routing and how capacity is setup and released for the native Case entity. Capacity is managed in two places, at the User level and at the Workstream level. The Workstream for Cases is setup with ‘Unit based’ capacity and the ‘cost’ of each incoming Case is set at a numerical value. Here I’ve created a new Workstream for Record routing for the Case table. I set the Capacity to Unit based and the value of each case = 25.
 
![](Images/RC1.png)

I’ll then go to User management then to Enhanced user management, open my User record and navigate to the Omnichannel Tab. Here I’ll set the Capacity = 100.

![](Images/RC2.png)
 
Therefore, if I have a Case Workstream and a Chat Workstream and the Unit based capacity for the Chat Workstream = 30, my Agents can have any combination of Cases and Chats as long as the total never exceeds 100.
Now that this is setup, records will begin routing to our Agents. The Case record comes in and gets routed to our Agent. The mechanism that handles the routing is the Queue Item entity. This record has a relationship to our routed record (Object) and to the Agent to whom it was routed to (Worked by). Once our Agent completes the Case, the ‘Worked by’ Agent relationship value is removed and the record is deactivated thereby clearing the 25 capacity points from our Agent.

## No-Code solution to release Capacity non-Case records
The advantage of the no-code solution is, that, well, it does not require you to be a developer and know C#! The example Flows you will see below took us about an hour or so to put together and test. Let’s walk through the Workstream setup for the entity along with the capacity. Let’s create a custom activity entity and complete the setup. I am using Unit based capacity and the value of my custom entity = 25.

![](Images/RC3.png)
 
## Route non-case records using PowerAutomate
We then have to automate the creation of our associated Queue Item record. This can be done in PowerAutomate by inserting a ‘Perform an unbound action’ step with the Action Name = msdyn_ApplyRoutingRuleEntityRecord. 

![](Images/RC4.png)
 
## Close live work items using PowerAutomate
Next, we have to account for removing the Agent from the ‘Worked by’ lookup on the Queue Item record thereby releasing the capacity of our custom entity (25 points) from our Agent. I too can utilize PowerAutomate for this. 

Note: Your business requirements may differ from the example below and therefore your Flow will likely be different but the goals of your Flow will be the same. 

In short, when our record is updated and if the Status = Completed, list the related Queue Item, put a ‘-1’ in the Worked by field and change the Status and Status Reason fields to Inactive. 

![](Images/RC5.png)
 
## Low-Code solution to release Capacity non-Case entity
The advantages of the ‘Low-Code’ solution is that it is a synchronous job and your business processes may require a solution that is a bit closer to real-time than you get with the ‘No-Code’ solution. The setup for the Workstream and capacity will be the same as the previous example.

## Route non-case records using a plug-in
The first step here is also similar to the previous example in that we have to route the non-case record which also creates the Queue Item record we see natively when a Case is routed. The good news is, the steps and sample code to perform this action are available here ([Route non-case records using a plug-in](https://learn.microsoft.com/en-us/dynamics365/customer-service/trigger-routing-non-case-records)). You’ll notice the plugin triggers the same msdyn_ApplyRoutingRuleEntityRecord as does our PowerAutomate solution!

## Close live work items or deactivate queue items
There is more good news here as well! There is sample code available here ([Close live work items or deactivate queue items](https://learn.microsoft.com/en-us/dynamics365/customer-service/deactivate-queue-items)) to achieve the second series of actions to close our Queue Item records accordingly. 

## Defining your business processes
A key thing to keep in mind is the business process that drives these updates within your organization. It’s critical that time is spent defining and documenting what each step in the lifecycle of your non-case record will be. Both of our solution example listed here assume the following basic process;
1.	The non-case record is created and Active
a.	This invokes the first step in our automation to create the Queue Item record
2.	It is then assigned to an Agent
3.	The Agent performs whatever interaction or updates your business process dictates
4.	The Agent deactivates the non-case record when they have completed the business process
a.	This invokes our automation that removes the Agent from the ‘Worked by’ lookup and deactivates the Queue Item record thereby releasing the capacity that was being consumed by this item.
Step #3 above is where your business process specifics can add complexity to the Flow or the Plug-in. Let’s expand the above process a bit:
1.	The non-case record is created and Active
a.	This invokes the first step in our automation to create the Queue Item record
2.	It is then assigned to an Agent
3.	The Agent makes a phone call to the customer to collect two pieces of information. After the Agent speaks to the customer, they update picklist A and picklist B with the information collected from the customer and save the record.
a.	This could invoke this record to be routed to the Queue A
4.	Another Agent picks the record up from Queue A
a.	This invokes another Queue Item record to be created
5.	This Agent performs some additional actions to complete the process and the record
a.	This invokes the Queue Item record to be closed releasing the capacity.
Your business process will dictate the triggers for assignment and closure and should be accounted for in either the Flow or the plug-in code to accommodate your requirements.
