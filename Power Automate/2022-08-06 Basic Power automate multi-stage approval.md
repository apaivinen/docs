## Tunnuksia

Display name: Pizza Chef
Username: Chef@leikkikentta.net
Password: 

Display name: Delivery Driver
Username: Driver@leikkikentta.net
Password: 

# The post

So, I wanted to figure out how I could create approval process with power automate which is triggered from a list item. In this post I will be creating a demo for a simple pizza order process.  
I will be creating this demo as simple as possible even though this lacks so many users input checks or functionalities which would be required by real order process.  

I know there's a lot of tutorials about this topic. Even Microsoft seem to have their own on [docs.microsoft.com](https://docs.microsoft.com/en-us/power-automate/sequential-modern-approvals) site. I just wanted to dig a bit deeper by myself but if you want to know more about approval flows then you should check that docs site  

### The logic

When customer orders a pizza by adding a line to pizza order list the order needs to be approved by the customer so, the order is verified.  
After the customer approves the order, it goes to chef to be approved.  
And when the chef has approved the order goes to delivery driver who needs to approve the order to indicate that pizza was delivered safe and sound.  

Order status/stage can be monitored from the Pizza order list.

## Requirements

- Basic knowledge about managing SharePoint Online lists and Power automate flows
- SharePoint Online site, created basic team site called Approval.
- 1-3 accounts with license to use SharePoint & Power Automate. For power automate free is enough. I used three accounts which represents customer, chef and the delivery drive. Keep in mind just one account is enough and my three account testing is a bit overdone.

## Sharepoint list

First of all, I need to create a List which is used for approval process. My list is called Pizza orders and I need to have in total of three approvers for my pizza to be ordered, cooked and delivered.  

My list contains the following columns
- Title (default column) - used for pizza name (Field type: Single line of text)
-  Customer - Used for first approval as approve the order (Field type: Person)
-  Chef - Approval user for can do/ can't do the pizza (Field type: Person)
-  Delivery driver - Approval user for driver to tell if pizza was delivered (Field type: Person)
-  Approval stage - Field type: Choice column with following choices
	-  Ordered (set this as a default value)
	-  Customer approved
	-  Chef approved
	-  Delivered
-  Restart approval - (Field type: yes/no)
	-  Restart approval is not used on this flow at all. There will be a next post about how to restart the approval process by using this column.

for simplicity sake this is all the list columns I will create for this demo even though this would need so much more information like topping selection or few comment columns for extra piece of information.  

## Power automate flow, when item is created

I want to start the process automatically when a new line is added so the flow starts with SharePoint triggers When an item is created. I just selected the site and the list. Nothing else to do there.  

|![1 when item is created.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/1-when-item-is-created.png)|
|:--:|
|Picture 1. when item is created|

Next, I used Start and wait for an approval action.  
Since my goal is to have just one approver I'll use "Approve/Reject - First to respond" as approval type.  
I added some kind of descriptive title and details.  
Item link is leading to list item in question and its title is the same as item title.  
And lastly, I disabled the reassignment since I have designated assigned to in the list itself.  

|![2 start and wait for an approval - customer confirmation.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/2-start-and-wait-for-an-approval-customer-confirmation.png)|
|:--:|
|Picture 2. Start and wait for an approval|

One approval request done. Next step is to catch the answer and deal with it accordingly. I used condition to compare if Responses approver response property is `Approved`  

|![3 responses approver response.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/3-responses-approver-response.png)|
|:--:|
|Picture 3. Responses approver response|

Now when I added Responses Approver response the flow automatically created a Apply to each loop. At the first I didn't mind about this but after a while I realized that since I need three approvers I really want to shrink nested levels on this flow to make it less confusing and "lightweight".  


|![4 condition with loop.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/4-condition-with-loop.png)|
|:--:|
|Picture 4. condition with loop|

I ran the flow once to see what kind of body it returns and I noticed that Approvers response is inside an array named Responses. Here's a sanitized results of responses content  
```json
 {
"responses": [
      {
        "responder": {
          "id": "ID",
          "displayName": "Anssi",
          "email": "anssi",
          "tenantId": "ID",
          "userPrincipalName": "anssi"
        },
        "requestDate": "2022-08-10T10:22:54Z",
        "responseDate": "2022-08-10T10:23:14Z",
        "approverResponse": "Approve"
      }
	]
}
```

Responses is an array due to possibility of multiple approvers so they each get their own set of properties. Now since I have only one approver I could use expressions to extract just the first Responses Approver response which is in the JSON marked as `approverResponse` property  

For helping to create the expression I created a compose action where I selected Responses property from Start and wait for an approval action  

|![5 compose.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/5-compose.png)|
|:--:|
|Picture 5. Helper compose action|

I went and clicked on inputs field and selected all text + copied the content to a text editor. I got this result:  
`@{outputs('Start_and_wait_for_an_approval_-_Customer_Confirmation')?['body/responses']}`  

I need to modify this a bit so I could use it in a condition.  

First I removed `@{` and `}` from start and end  

Then I added `[0]` to the end to indicate to take first value from array  

After that I added `['approverResponse']` to end of the expression so I can extract the Responses Approver response  

So the complete expression is  
```expression
outputs('Start_and_wait_for_an_approval_-_Customer_Confirmation')?['body/responses'][0]['approverResponse']
```
This expression can be copied and pasted to the condition as an expression and use equal condition to check if the value is Approve  

|![6 condition.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/6-condition.png)|
|:--:|
|Picture 6. Condition|

Now if the value isn't Approve the condition goes to No which is empty so the flow ends at there. There's a possibility to add more processing if approver rejected but for this demo, I'll keep it empty.  
If Approver approves, then I'll just update the list item. I'm using SharePoints Update Item action where I enter site and list details. I'll use Id and Title from When an item is created action as update item Id and Title.  
The only change the item needs is changing Approval Stage Value from ordered to Customer approved.  

|![7 update item.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/7-update-item.png)|
|:--:|
|Picture 7. Update item|

Now I have one approval done and I must create following actions for Chef approval inside Customers confirmation Yes condition.  
1. Start and wait for an approval
2. Condition with updated expression to reflect chefs response
3. Update item with chefs answer

So the chefs approval outline should look like this  

|![8 chefs response.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/8-chefs-response.png)|
|:--:|
|Picture 8. Chefs approval|

It's the same deal as customer approval. If aoorivak us rejected do nothing. If Approved then update the list item.  
And by now you could guess already that the drivers approval needs to be created inside chefs if yes section.  

Here's the drivers approval outline  

|![9 driver response.png](http://192.168.10.50:4000/assets/img/2022-08-06-Basic-Power-automate-multi-stage-approval/9-driver-response.png)|
|:--:|
|Picture 9. Drivers approval|

and now I have a three-stage approval process done. As you can see there's no actions after Update Item - Driver approved since this the end of flow.  

## Testing the flow

Now when you create a new item to the list with Title, customer, chef and driver the flow should start. Keep in mind in testing there's no checks if all the necessary values are empty so don't forget to fill them.  
Now there's at least three different places where users can approve/reject their requests.  
One of the most common places to approve/reject the request is from email since you'll get an email about every request.  
The second common place to react the request is Teams since you'll get a notification about it. The request is in Approvals app where you can choose your answer.  
The third place is in Power automate portal under "Action items" menu.  

No matter where you give your answer it updates to everywhere so you can't answer multiple times for one request.  

Now this is the out-of-the-box answer functionality. You could also create a custom-made teams message or email if you wanted to but that's a topic for another time.  

## Closing words

Now keep in mind even though approvals wait for an answer there's a timeout in place. Flows can be run maximum of 30 days and after that they get cancelled. For more information about power automate limits check this link  
https://docs.microsoft.com/en-gb/power-automate/limits-and-config/  

As the limits and config page says there's also limit about nested levels. I used expressions to remove For each loops for each response. If I wouldn't use the expression it would add 3 more nested levels to this flow which would make it in total of 6 nested levels which means you would had only 2 more levels to work with if you needed to do some modifications on your own. Unless ofcourse you are able to create child flows to have a workaround for nested levels limit.

In the next post [Power automate multi-stage approval with restart ability](/posts/Power-automate-multi-stage-approval-with-restart-ability/) I will be creating a new Power automate flow which is used to restart this flow by changing Restart approval value from no to yes. This can be modified to achieve the same functionality as this flow.  
