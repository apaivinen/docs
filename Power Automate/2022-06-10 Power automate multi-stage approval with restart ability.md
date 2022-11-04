## Tunnuksia

Display name: Pizza Chef
Username: Chef@leikkikentta.net
Password: 

Display name: Delivery Driver
Username: Driver@leikkikentta.net
Password: 

# The post

In the last post [Basic Power automate multi-stage approval](/posts/Basic-Power-automate-multi-stage-approval/) I created a Power automate flow which have 3 approvals. Now I'm going learn how to create an alternative solution which utilizes the same list but the approval can be restarted depending which stage it was left previously.  

The structure and core actions will be a bit different compared to the previous flow. Last flow I created three nested conditions which were handling the initial approval. Now to minimize actions and make the structure less confusing I'll be using different technique.  

## Power automate flow, when item is created or updated

I created this "Restart approval" column to the list which contains yes/no checkbox. Yes means I want to restart the approval process and start the flow. No or empty value does nothing.  

I am using SharePoints When item is created or updated trigger with this but I want to limit starting actions so only restart approval value starts the approval process.  
To limit starting actions, I need to configure Trigger actions to the trigger.   

|![1-trigger-settings.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/1-trigger-settings.png)|
|:--:|
|Picture 1. Trigger settings|


|![1-trigger-conditons.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/2-trigger-condition.png)|
|:--:|
|Picture 2. Trigger conditions|

Here's the code for trigger condition

```
@equals(triggerBody()?['Restartapproval'],true)
```

So, in trigger condition I am using `equals` expression which requires two parameters. First parameter is `Restartapproval` value from trigger body. And the second value is `true`.  Equals-expression requires both values to be the same to allow the trigger to start. Since `Restartapproval`(internal name of column Restart approval) returns only `true` or `false` the trigger starts only when the value is changed to `true`

|![3-helper-variables.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/3-helper-variables.png)|
|:--:|
|Picture 3. Helper variables|



Next I need to create two variables to assist the flow.
1. flowStopped (boolean with a default value of `false`)
2. ApprovalStage (string with default value of triggers `Approval Stage Value`)

flowStopped is needed for Do until loop to be stopped at will. This variable is updated on every case(including the default case)  
ApprovalStage is used to store current approval stage value. This Variable is updated every time when item is updated (Except the last item update)  

Since I need to check and deal dynamically the approval stages depending on their value I am using Do until loop and switch instead of condition.

|![4-do-until-and-switch.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/4-do-until-and-switch.png)|
|:--:|
|Picture 4. Do until and switch|

on Do until loop I'm setting flowStopped variable to be checked as is equal to true.  
When flowStopped variable is updated to true flow stops.   

on Switch I'm checking ApprovalStage value.
For each approval I made their own case
- Ordered (customer approval)
- Chef approval
- Driver approval

Let's start with Default since it's quite important. If flow is started when its status is Delivered or new status values are added but the flow isn't updated we want to quit this restart flow to avoid "infite" loops. By default do until runs 60 iterations until it cancels itself. You can configure this from Do until "Change limits"-link.  

I'll set flowStopped variable to true for Default action. That's all.

|![5-default.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/5-default.png)|
|:--:|
|Picture 5. Default case|

Case which processes items with Ordered status starts with Start and wait for an approval action.  
I'm using the same values as I used on previous post.  
Somewhat meaningful title & details.  
Assigned to the customer email, item link is the item title, and it leads to the item itself.  
And reassignment is disabled.  

|![6-case-ordered.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/6-case-ordered.png)|
|:--:|
|Picture 6. Case, ordered|

I'll be using same expression to extract Approvers response value as I used in previous post which is:
```
outputs('Start_and_wait_for_an_approval_-_Customer_confirmation')?['body/responses'][0]['approverResponse']
```

If `approverResponse` is not Approve the flow just updated flowStopped variable to true to end the flow.  
If `approverResponse` is Approve the flow updates list item Approval Stage Value to Customer approved and Restart approval to No  

Next, we take Approval Stage Value from Update item and save it to ApprovalStage variable.  

|![7-condition-customer-approved.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/7-condition-customer-approved.png)|
|:--:|
|Picture 7. Condition, Customer approved|

That's it for Customer approval. This needs to be copied for the chef and driver approval.

Remember to update following values accordingly in chefs and drivers' approval
1. Start and wait for an approval action assigned to value
2. Conditions expression to extract chefs' approval answer
3. Update item Approval stage

Here's outline of a chefs' approval case

|![8-case-chef.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/8-case-chef.png)|
|:--:|
|Picture 8. Case, chef|

Now there's a slight difference in Driver's approval since it's the last approval.  
We are not updating ApprovalStage variable since we are not needing it anymore.  
Also flowStopped is moved from If no action to end of the case so no matter what flowStopped variable is set to true and do until loop ends.  

Here's outline of a driver approval case

|![9-case-driver.png](http://192.168.10.50:4000/assets/img/2022-06-10-Power-automate-multi-stage-approval-with-restart-ability/9-case-driver.png)|
|:--:|
|Picture 9. Case, driver|

Now the flow is done.  

Now if there isn't any errors flow should start when list item restart approval value is set to Yes in the list.  
Flow should pick up the latest Approve stage and process the rest of the stages.  


## Closing words

Now this is just one example how it would be possible to create an approval flow which can be restarted from latest approved stage. This should be simple to develop further for example collecting comments and sending tailored notification emails.  

Switch and expressions are used for making flow to be less confusing and limit nested levels.  
The core of this flow uses only three nested levels inside switch cases so there's room for 5 more if you really need more data/action handling.  
