Account SignUp
https://developer.salesforce.com/signup

Logic and Process Automation
============================
https://trailhead.salesforce.com/en/content/learn/modules/business_process_automation/process_whichtool
https://trailhead.salesforce.com/en/content/learn/modules/point_click_business_logic

Work Flow
Create a work flow to update a field on Account object
Send an email

Process Builder
Create a Process builder to update current record
Create a Process builder to update related records

Lightning Flows


Apex Data types, control flow statements, interfaces
https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/langCon_apex_datatypes_variables_intro.htm

Step 1:
-------
Execute the below code in Anonymous window

Integer i = 5;
Date dt = System.today();
DateTime dtt = System.now();

system.debug(' date : ' + dt);
system.debug(' date time : ' + dtt);





Apex Classes:
=============
https://trailhead.salesforce.com/content/learn/projects/quickstart-apex
https://trailhead.salesforce.com/content/learn/modules/apex_testing

Trigger:
========
https://trailhead.salesforce.com/en/content/learn/modules/apex_triggers/apex_triggers_intro

What is a trigger ?
Why do we use a trigger ? can Point and click tools be used instead ?
-- Process Builders cannot handle before DML It executes after a record has been created or updated. Whereas Apex triggers can handle both before and after DML operations.
-- Process Builder cannot handle delete and undelete DML. Whereas Apex triggers can handle all DML operations.
-- Validation: Processes do not trigger validation rules and can, therefore, invalidate data.
       An error reported in Process Builder is more generic which makes it difficult to find the origin of the error. With Apex triggers, exception handling can be made more specific.
-- It is all or none in case of Process Builder failure. But with Apex triggers partial success is possible.


When will a trigger be invoked ?

Step 1:
-------
trigger HelloAccountTrigger on Account (before insert) {
    //Print a message in the console
    System.debug('Hello World from Account Trigger');
}

Step 2:
-------
Create an Account and check the logs for the above message


Step 3:
-------
trigger HelloAccountTrigger on Account (before insert, after insert) {
    //Print a message in the console
    System.debug('Hello World from Account Trigger');
}

step 4:
-------
Create an Account and check the logs for the above message, Out put is shown twice

Trigger Context Variables :

Step 5:
-------
trigger HelloAccountTrigger on Account (before insert, after insert) {
    if(Trigger.isBefore){
		//Print a message in the console
		System.debug('Hello World from Account Before Trigger');
	}
}

step 6:
-------
Create an Account and check the logs for the above message, Out put is shown once


step 7:
-------
trigger HelloAccountTrigger on Account (before insert, after insert) {
    if(Trigger.isBefore){
    	//Print a message in the console
    	System.debug('Hello World from Account Before Trigger');
    }
    
    if(Trigger.isAfter){
    	//Print a message in the console
    	System.debug('Hello World from Account After Trigger');
    }
}

step 8:
------
Create an Account and check the logs for the above message, Out put is shown once for each operation

step 9:
-------
Update an account..... 
The custom print statements are not shown in the o/p, since the operation update will not invoke the trigger
We can add before update, after update and can implement our logic

step 10:
--------
Create Record or Import data (using Data Import Wizard) and check the data coming. 
Check the field values 

trigger HelloAccountTrigger on Account (before insert, after insert) {
    if(Trigger.isBefore){       
        List<Account> acc = Trigger.new;
        system.debug('No of records in trigger : ' + acc.size());
        
        for(Account accountObj : acc){
        	system.debug('Name of the account : ' + accountObj.name);
            system.debug('Account Rating : ' + accountObj.rating);
        }
    }
    
    if(Trigger.isAfter){
    	//Print a message in the console
    	System.debug('Hello World from Account After Trigger');
    }
}


step 11:
--------
Use of before trigger.

trigger HelloAccountTrigger on Account (before insert, after insert) {
    if(Trigger.isBefore){       
        List<Account> acc = Trigger.new;
        system.debug('No of records in trigger : ' + acc.size());
        
        for(Account accountObj : acc){
        	system.debug('Name of the account : ' + accountObj.name);
            system.debug('Account Rating : ' + accountObj.rating);
            if(accountObj.rating == null){
                accountObj.rating = 'cold';
            }
        }
    }
    
    if(Trigger.isAfter){
    	//Print a message in the console
    	System.debug('Hello World from Account After Trigger');
    }
}

step 12:
--------
Create an Account and leave the Rating field as blank. Save the record and check the value of Rating field
Create an Account and set the Rating field value as warm, Save the record and check the value of Rating field.


step 13:
--------
Use of After Trigger.

trigger HelloAccountTrigger on Account (before insert, after insert) {
    if(Trigger.isBefore){       
        List<Account> acc = Trigger.new;
        system.debug('No of records in trigger : ' + acc.size());
        
        for(Account accountObj : acc){
            system.debug('Name of the account : ' + accountObj.name);
            system.debug('Account Rating : ' + accountObj.rating);
            if(accountObj.rating == null){
                accountObj.rating = 'cold';
            }
        }
    }
    
    if(Trigger.isAfter){
        List<Opportunity> oppList = new List<Opportunity>();
        
        // Get the related opportunities for the accounts in this trigger
        Map<Id,Account> acctsWithOpps = new Map<Id,Account>(
            [SELECT Id,(SELECT Id FROM Opportunities) FROM Account WHERE Id IN :Trigger.New]);
        
        // Add an opportunity for each account if it doesn't already have one.
        // Iterate through each account.
        for(Account a : Trigger.New) {
            System.debug('acctsWithOpps.get(a.Id).Opportunities.size()=' + acctsWithOpps.get(a.Id).Opportunities.size());
            // Check if the account already has a related opportunity.
            if (acctsWithOpps.get(a.Id).Opportunities.size() == 0) {
                // If it doesn't, add a default opportunity
                oppList.add(new Opportunity(Name=a.Name + ' Opportunity', StageName='Prospecting',
                                            CloseDate=System.today().addMonths(1), AccountId=a.Id));
            }           
        }
        if (oppList.size() > 0) {
            insert oppList;
        }
    }
}

step 14:
--------
Create an Account. Notice that the after trigger automatically creates ann Opportunity and associates it with the Account


step 15:
--------
Using trigger Exceptions

trigger HelloAccountTrigger on Account (before insert, after insert, before delete) {
    if(Trigger.isBefore && Trigger.isInsert){       
        List<Account> acc = Trigger.new;
        system.debug('No of records in trigger : ' + acc.size());
        
        for(Account accountObj : acc){
            system.debug('Name of the account : ' + accountObj.name);
            system.debug('Account Rating : ' + accountObj.rating);
            if(accountObj.rating == null){
                accountObj.rating = 'cold';
            }
        }
    }
    
    if(Trigger.isBefore && Trigger.isDelete){       
        // Prevent the deletion of accounts if they have related opportunities.
        for (Account a : [SELECT Id FROM Account
                          WHERE Id IN (SELECT AccountId FROM Opportunity) AND
                          Id IN :Trigger.old]) {
                              Trigger.oldMap.get(a.Id).addError(
                                  'Cannot delete account with related opportunities.');
                          }
    }
    
    if(Trigger.isAfter){
        List<Opportunity> oppList = new List<Opportunity>();
        
        // Get the related opportunities for the accounts in this trigger
        Map<Id,Account> acctsWithOpps = new Map<Id,Account>(
            [SELECT Id,(SELECT Id FROM Opportunities) FROM Account WHERE Id IN :Trigger.New]);
        
        // Add an opportunity for each account if it doesn't already have one.
        // Iterate through each account.
        for(Account a : Trigger.New) {
            System.debug('acctsWithOpps.get(a.Id).Opportunities.size()=' + acctsWithOpps.get(a.Id).Opportunities.size());
            // Check if the account already has a related opportunity.
            if (acctsWithOpps.get(a.Id).Opportunities.size() == 0) {
                // If it doesn't, add a default opportunity
                oppList.add(new Opportunity(Name=a.Name + ' Opportunity', StageName='Prospecting',
                                            CloseDate=System.today().addMonths(1), AccountId=a.Id));
            }           
        }
        if (oppList.size() > 0) {
            insert oppList;
        }
    }
}

step 16:
--------
Create an Account. Notice that the after trigger automatically creates ann Opportunity and associates it with the Account
Try to delete the Account. An error message is shown.




Batch classes and how to set them up -- Raghu
SOQL, SOSL and DML 
https://trailhead.salesforce.com/en/content/learn/modules/database_basics_dotnet/sql_to_soql
https://trailhead.salesforce.com/content/learn/modules/apex_database/apex_database_intro


Trigger patterns

Recursion
http://www.sfdcpoint.com/salesforce/avoid-recursive-trigger-salesforce/


Exception Handling
https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_exception_trycatch_example.htm

Apex governor limits:
https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_gov_limits.htm

Order of execution
https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_order_of_execution.htm



