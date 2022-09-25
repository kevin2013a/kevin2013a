<h1 align="center">Hi 👋, I'm Kevin Yuki Horimi</h1>
<h3 align="center">Salesforce Developer</h3>
<img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width=100px height=70px/>

- 🌱 I’m currently learning **APEX, SOQL, LWC, AURA, HTML, CSS e JS**

- 💬 Ask me about **APEX, SOQL, LWC, JS**

- 📫 How to reach me **kevin_horimi@hotmail.com**

<h3 align="left">Connect with me:</h3>

<a href="https://www.linkedin.com/in/kevin-yuki-horimi-a3973716a/" target="_blank" rel="noreferrer"> <img src="https://cdn-icons-png.flaticon.com/512/174/174857.png" height="40" width="40"/></a>  <a href="https://trailblazer.me/id/kevinhorimi" target="_blank" rel="noreferrer"> <img src="https://trailhead.salesforce.com/assets/trailhead-logo-5d3354441b4d8b97f21075b65e2aea266780d45943bbb36796ac25dc7cf4adc9.svg" height="40" width="40"/></a> 



</p>

<h3 align="left">Languages and Tools:</h3>
<p align="left"> 
<a href="https://trailblazer.me/id/kevinhorimi" target="_blank" rel="noreferrer"> <img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width="50" height="38"/> <a/> <a href="https://developer.mozilla.org/pt-BR/docs/Web/HTML" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/html5/html5-original-wordmark.svg" alt="html5" width="40" height="40"/> </a> <a href="https://developer.mozilla.org/pt-BR/docs/Web/CSS" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/css3/css3-original-wordmark.svg" alt="css3" width="40" height="40"/> </a> <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/javascript/javascript-original.svg" alt="javascript" width="40" height="40"/></a> </p>
<br/><br/>


# Learning Apex:

## IF AND ELSE
```cls
// Example of If and Else comparing numbers;
Integer n1 = 10;
Integer n2 = 9;
Integer n3 = 8;

if(n1 > n2 && n1 > n3){
        System.debug('The first number is the biggest of all!');
        } else if (n2 > n1 && n2 > n3){
        System.debug('The second number is the biggest of all!');
    } else {
        System.debug('The third number is the biggest of all!');
    }


// Example of If and Else with concatenation and use of conditional AND;
String product = 'T-shirt';
Integer quantity;
Decimal price = 49.99;

if(price < 30){
    quantity = 4;
     System.debug('Purchase of '+quantity+' '+product+'s done successfully!');
}else if(price > 30 && price <= 50){
    quantity = 2;
    System.debug('Purchase of '+quantity+' '+product+'s done successfully!');
} else if (price > 50 && price <= 80){
    quantity = 1;
    System.debug('Purchase of '+quantity+' '+product+' done successfully!');
} else {
    System.debug('Go away!');
}

```
                                     
## ARRAYS
```cls
/* Types of ARRAYS:
SET - Does not accept repeated value (Finds by value. Does not have index);
LIST - Accepts repeated values (It has index and locates by position);
MAP - Stores more information, is in alphabetical order and does not store duplicate keys; */

// Simple example of set;
Set <String> musicStyle = new Set <String>{'Country', 'Rock'};
system.debug(musicStyle);


// Simple example of list;
String [] listMarket = new String[]{'Potato', 'Carrot', 'Tomato'};


// Simple example manipulating dates with list;
list <date> dates = new date[5]; 
dates[0] = date.newInstance(2001, 1, 1); 
dates[1] = date.newInstance(2002, 2, 3);
dates[2] = date.newInstance(2003, 3, 3);
dates[3] = date.newInstance(2004, 4, 4);
dates[4] = date.newInstance(2005, 5, 5);

System.debug(datas);

dates.remove(4); 
dates.remove(3); // Removing two last dates;

System.debug(datas);


// Simple map example with string key and decimal return;
Map <String, Decimal> products = new Map <String, Decimal>();
products.put('Dish', 9.90);
products.put('Glass', 4.99);
products.put('Pan', 39.90);

System.debug(products);
System.debug(products.get('Dish')); // Retorn the product price;


// Simple examples of manipulating a list;
list <string> bestAlbuns = new String[5]; // Setting the list size to 5 positions;
bestAlbuns.add(0,'Black Ice'); // Adding value to a specific position CREATING A NEW POSITION IN THE LIST;
bestAlbuns.set(1, 'Acustico MTV'); // Assign value to an existing position;
bestAlbuns[2] = 'Clara Nunes'; // Another way to assign value to an existing position;

```
        
## METHODS /  FUNCTIONS
```cls
// Example of a simple function using Static to allow calling the function without having to instantiate the class;
Public Class Calculator {
    
    Public Static Void Calculation (Decimal a, String operation, Decimal b){
        if(operation == '+'){
            Decimal result = a+b;
            System.debug('The result is: '+result.setScale(2));
        } else if(operation == '-'){
            Decimal result = a-b;
            System.debug('The result is: '+result.setScale(2));
        } else if(operation == '/'){
            Decimal result = a/b;
            System.debug('The result is: '+result.setScale(2));
        } else if(operation == '*'){
            Decimal result = a*b;
            System.debug('The result is: '+result.setScale(2));
        } else{
            System.debug('Invalid Operator!');
        }
    }
}

// Calling the function (Without needing to instantiate);
Calculator.Calculation(5,'*',5);
```
        
## SOQL
```cls
// Simple Query example;
SELECT Id, Name
FROM Account
WHERE Name Like '%Corporation%' // Contains Corporation;


// Example sorting the Query;
SELECT Id, Name
FROM Account
WHERE Name Like '%Corporation'
ORDER BY Name DESC NULLS LAST
LIMIT 10


// Example grouping the Query by name;
SELECT Name, Count(Id)
FROM Opportunity
GROUP BY Name
HAVING Count(Id)>1


// SubQuery example:
SELECT Id, Name,
(SELECT Id, Name, Amount
FROM Opportunities)
FROM Account 


// Using several SubQuery:
SELECT Id, Name,
( SELECT Id, Name
FROM Contacts),
( SELECT Id, Name
FROM Opportunities),
( SELECT Id, Name
FROM Despesas__r),
( SELECT Id, Name
FROM Projects__r)
FROM Account
```
        
## APEX
```cls
// Delete all records that were created between July 1st and August 2nd with IF;
List <Project__> lstProject = [SELECT Id, ProjectNumber__c, CreatedDate FROM Project__c];
List <Project__c> projectDelete = new List <Project__c>();

for(Project__c Count: lstProject){
    Date initialDate = Date.newInstance(2022, 07, 01);
    Date endDate = Date.newInstance(2022, 08, 03);
    if(Count.CreatedDate >= initialDate && Count.CreatedDate < endDate){
        projectDelete.add(Count);
    }
}
if(projectDelete != NULL){
    DELETE projectDelete;
}
                                                                       

// Simple example creating record with more than one RecorType via Apex;
Expense__c newExpense = new Expense__c();
                                                                       
newExpense.RecordTypeId = '0128Z000000dDp8QAE';// Assigning a Record Type by Record Type Id;
INSERT newExpense;

 
// Simple example updating all records without expenses type to 'Without description':
List <Expense__c> lstExp = [SELECT Id, Name, ExpenseType__c FROM Expense__c];
List <Expense__c> expUpdate = new List <Despesa__c>();

for(Despesa__c Counter: lstDesp){
    if(Counter.ExpenseType__c == null){
        Counter.ExpenseType__c = 'Without description';
        expUpdate.add(Counter);
    }
}
if(ExpUpdate != NULL){
    UPDATE expUpdate;
}
```
        
## TRIGGER
```cls
// Simple example of Trigger structure organized by time and action;
Trigger LeadTrigger on Lead (before INSERT, before UPDATE, after INSERT, after UPDATE){
    
    if(Trigger.isBefore && Trigger.isInsert){
        LeadBO.companySize(Trigger.new);
        
    }else if(Trigger.isBefore && Trigger.isUpdate){
        LeadBO.companySize(Trigger.new);
        
    }else if(Trigger.isAfter && Trigger.isInsert){
        LeadBO.checkRegistration(trigger.new);
        
    }else if(Trigger.isAfter && Trigger.isUpdate){
   
    }
}
```
        
## BO / HANDLER
```cls
// Simple example of a BO class that houses the methods called by the Trigger;
public class LeadBO {
    
    // Creates a task whenever a Lead with AnnualRevenue over 500,000.00 is created;
    public static void checkRegistration (List<Lead> lstLead){
        List <Task> lstTask = new List<Task>();
        
        for(Lead Counter: lstLead){
            if(Counter.AnnualRevenue > 500000){
                task newTask = new Task();
                newTask.Subject = 'Check if the registration is complete';
                newTask.Status = 'Not Started';
                newTask.Priority = 'Normal';
                newTask.WhoId = Counter.Id;
                lstTask.add(newTask); 
            }
            if(lstLead != NULL){
            INSERT lstTask;
            }
        }    
    }
    
    
   // Assign the company size as Small when the number of employees is less than 1000;
    public static void companySize(list <Lead> lstLead){ 
        for(Lead Counter: lstLead){
            if(Counter.NumberOfEmployees<1000){
                Counter.companySize__c = 'Small';
            }
        }  
    } 
}
```
                                                 
## TEST CLASS
```cls
// Simple test class example: testing all calculator possibilities;
@IsTest
public class calculatorTest {
    
    @IsTest
    public static decimal testSumMethod(){
        Decimal resultSum = Calculator.Calculation(10,'+',5);
        System.assert(resultSum == 15, 'Sum result is not as expected!');
        return resultSum;
    }

    @IsTest
    public static decimal testSubtractionMethod(){
        Decimal resultSubtraction = Calculator.Calculation(10,'-',5);
        System.assert(resultSubtraction == 5, 'Subtraction result is not as expected!');
        return resultSubtraction;
    }
    
    @IsTest
    public static decimal testDivisionMethod(){
        Decimal resultDivision = Calculator.Calculation(10,'/',5);
        System.assert(resultDivision == 2, 'Division result is not as expected!');
        return resultDivision;
    }
    
    @IsTest
    public static decimal testMultiplicationMethod(){
        Decimal resultMultiplication = Calculator.Calculation(10,'*',5);
        System.assert(resultMultiplication == 50, 'Multiplication result is not as expected!');
        return resultMultiplication;
    }
    
    @IsTest
    public static decimal testError(){
        Decimal error = Calculator.Calculation(10,'&',5);
        System.assert(error == 0, 'Result is not as expected!');
        return error;
    }
}

```
```cls
// Simple test class example for records;
@IsTest
Public Class CaseTest {

    // Testing the validation when inserting the record
    @IsTest
    public static void ifIsCreated(){
        Case newCase   = new Case(); 
        newCase.Status = 'New';
        newCase.Origin = 'Web';
        INSERT newCase;
        Case createdCase = [SELECT Id, Type FROM Case WHERE Id =: newCase.id];
        System.assert (newCase.Type == 'Other', 'The case type is not the same as Other');
    }
    
    // Testing the validation when updating the record;
    @IsTest
    public static void ifIsUpdate(){
        
        Case newCase   = new Case(); 
        newCase.Status = 'New';
        newCase.Origin = 'Web';
        newCase.Type = 'Electronic';
        INSERT newCase;
        newCase.Type = NULL;
        UPDATE newCase;
        System.assert (newCase.Type == 'Other', 'The case type is not the same as Other');
    }
}
```
                                                 
## AURA COMPONENTS
```cls
// Simple exemple of an Aura Component that calculate the BMI;
// BmiCaclculator.cmp
<aura:component implements="force:appHostable,flexipage:availableForAllPageTypes,flexipage:availableForRecordHome,force:hasRecordId" access="global" >
    
    <aura:attribute name="result" type="decimal"/>
    <aura:attribute name="classification" type="string"/>
    
    <div class="slds-box slds-theme_default"> 
        <h1>BMI Calculator</h1><br/>
        <lightning:input type="decimal" aura:id="height" label="Isert your height"/>
        <lightning:input type="decimal" aura:id="mass" label="Isert your mass"/>
        <br/>
        
        <span class="result"> Result: {!v.result} </span> <br/><br/>
        <span class="classification"> Classification: {!v.classification} </span> <br/><br/>
        <lightning:button variant="brand" label="Calculate" title="Calculate" onclick="{! c.handleClick }" />
        
    </div>
    
</aura:component>

// BmiCalculatorController.js
({
    handleClick : function(component, event, helper) {
        helper.BmiCalculator(component);
    },
})

// BmiCalculatorHelper.js
({
    BmiCalculator : function(component) {
        var height = component.find("height").get("v.value");
        var mass = component.find("mass").get("v.value");
        
        var heightUpdated = parseFloat(height.replace (",", "."));
        var massUpdated = parseFloat(mass.replace (",", "."));
        
        var result = massUpdated / (heightUpdated*heightUpdated);
        component.set("v.result", result.toFixed(2));
        
        if(result < 18.5){
            component.set("v.classification", "Less than ideal weight")
        }else if(result >= 18.5 && result < 25){
            component.set("v.classification", "Normal weight")
        }else if(result >= 25 && result < 30){
            component.set("v.classification", "Overweight")
        }else if(result >= 30){
            component.set("v.classification", "Obesity")
        }else{
            component.set("v.classification", "ERROR - Invalid data!")
        }
    }
})
```
        
## LWC (LIGHTNING WEB COMPONENTS)
```cls
// Simple exemple of an LWC Component that allows create a new Lead;
// insertLeadLWC.js-meta.xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>55.0</apiVersion>
    <isExposed>true</isExposed> // In the meta file we expose the component;
    <targets>
        <target>lightning__RecordPage</target> // And make available for use on the pages;
        <target>lightning__AppPage</target>
        <target>lightning__HomePage</target>
    </targets>
</LightningComponentBundle>
        
// insertLeadLWC.html
<template>
    <lightning-card title="Criar Lead" icon-name="action:new_lead">

            <div class="slds-m-around_medium">
                <lightning-record-edit-form object-api-name="Lead" onsuccess={handleSuccess}> // Calling the method on success insert new Lead;

                    <p> <lightning-input-field field-name="Name"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="Company"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="Phone" type="phone"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="MobilePhone" type="phone"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="Status"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="AnnualRevenue"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="Rating"></lightning-input-field> </p>  <br/>
             
                    <p>
                        <lightning-button label="Clear" onclick={handleReset}></lightning-button> // The Clear button calls the handleReset method;
                        <lightning-button label="Criate Lead" type="submit" variant="brand"></lightning-button>
                    </p>

                </lightning-record-edit-form>
            </div>
            
    </lightning-card>  
</template>
        
// insertLeadLWC.js
import { LightningElement } from 'lwc';
import { ShowToastEvent } from 'lightning/platformShowToastEvent'; // Importing Toast library to exibe the succefull message;
import { NavigationMixin } from 'lightning/navigation'; // Importing Navigation library to redirect the user to the new record page;

 export default class InsertLeadLWC extends NavigationMixin (LightningElement) {
    recordId;

    handleSuccess(event){

        const toastEvent = new ShowToastEvent({
            title: 'Congratulations',
            message: 'The Lead ' + event.detail.id + ' was created successfully!',
            variant: 'success'
        });

        this.dispatchEvent(toastEvent);

        this[NavigationMixin.Navigate]({
            type: 'standard__recordPage',
            attributes: {
                recordId: event.detail.id,
                objectApiName: 'Lead',
                actionName: 'view'
            }
        });
        
    }

    handleReset(){
        const inputFields = this.template.querySelectorAll( // Getting all input fields;
            'lightning-input-field'
        );

        if(inputFields) { // If input fields aren't null, reset them;
            inputFields.forEach(field => {
                field.reset();
            });
        }
       
    }
}
```
        
## LWC WITH APEX
```cls
// Simple exemple of task list component mixing LWC and Apex Class;
// TaskControllerLWC.cls
public class TaskControllerLWC { // First we need to create an Apex class to get the list;
    
    @AuraEnabled(cacheable=true)
    public static List<Task> getListTask(){
        return [Select Id, WhoId, AccountId, Account.Name, Subject, CreatedDate, Status, Priority, ActivityDate FROM Task WHERE OwnerId =: UserInfo.getUserId() AND Status != 'Completed']; // Getting just the tasks related with the user Id;
    }
}

// listTaskLWC.html
<template>
	<lightning-card title="Your Tasks" icon-name="standard:task">
		<div>
			<lightning-datatable key-field="id" data={tasks} columns={columns} hide-checkbox-column>
			</lightning-datatable>
		</div>
	</lightning-card>
</template>
        
listTaskLWC.js
import { LightningElement, wire } from 'lwc';
import  getListTask  from '@salesforce/apex/TaskControllerLWC.getListTask'; // Importing the method from the Apex class;

export default class ListTaskLWC extends LightningElement {
    tasks;
    error;
 
     columns =[
         {label: 'Subject', fieldName: 'Subject'},
         {label: 'Status', fieldName: 'Status'},
         {label: 'Priority', fieldName: 'Priority'},
         {label: 'Activity Date', fieldName: 'ActivityDate'},
         {label: 'Related ID', fieldName: 'WhoId'}
     ]
 
     @wire(getListTask)wiredTasks({error, data}){
         if(data){
             this.tasks = data;
             this.error = undefined;
             console.log(this.tasks);
         } else if(error){
             this.error = error;
             this.tasks = undefined;
         }
     }
}
        
```       
```cls
// Simple exemple of Internal purchases list component with an add item card and a items not purchased yet list card;
// PurchasesControllerLWC.cls
public with sharing class PurchasesControllerLWC {

    @AuraEnabled(cacheable=true)
        public static List<Purchases__c> getPurchasesList(){
            return[SELECT Id, Name, Quantity__c, UnitPrice__c, PurchaseDate__c, Bought__c, TotalPrice__c, CreatedDate FROM Purchases__c WHERE Bought__c = false ORDER BY CreatedDate desc];
        }
}
	
// PurchasesList.html
<template>
    
    <lightning-card title="Add an item to the list" icon-name="standard:record">
            <div class="slds-m-around_medium">
                <lightning-record-edit-form object-api-name="Purchases__c" onsuccess={handleSuccess}>

                    <p> <lightning-input-field field-name="Name"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="PurchaseDate__c"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="Quantity__c" type="phone"></lightning-input-field> </p>
                    <p> <lightning-input-field field-name="UnitPrice__c" type="phone"></lightning-input-field> </p>
             
                    <br/>
                    <p>
                        <lightning-button label="Clear" onclick={handleReset}></lightning-button>
                        <lightning-button label="Add Item" type="submit" variant="brand"></lightning-button>
                    </p>

                </lightning-record-edit-form>
            </div>
            
    </lightning-card>  

    <lightning-card title="Internal purchases list" icon-name="standard:record">
        <div class="slds-m-around_medium">    
                  
                        <lightning-datatable key-field="id" data={purchases} columns={columns} hide-checkbox-column>
                        </lightning-datatable>

        </div>
   </lightning-card>

</template>
	
//PurchasesList.js
import { LightningElement, wire } from 'lwc';
import { ShowToastEvent } from 'lightning/platformShowToastEvent';
import  getPurchasesList  from '@salesforce/apex/PurchasesControllerLWC.getPurchasesList';

export default class PurchasesList extends LightningElement {
    recordId;
    purchases;
    error;
 
     columns =[
         {label:'Name', fieldName: 'Name'},
         {label: 'Quantity', fieldName: 'Quantity__c'},
         {label: 'Unit Price', fieldName: 'UnitPrice__c'},
         {label: 'Total Price', fieldName: 'TotalPrice__c'},
         {label: 'Purchase Date', fieldName: 'PurchaseDate__c'},
     ];
 
     @wire(getPurchasesList)wiredPurchases({error, data}){
         if(data){
             this.purchases = data;
             this.error = undefined;
             console.log(this.purchases);
         } else if(error){
             this.error = error;
             this.purchases = undefined;
         }
     }

    handleSuccess(event){
        
        this.recordId = event.detail.id;

        const toastEvent = new ShowToastEvent({
            title: 'Congratulations!',
            message: 'Item added successfully!',
            variant: 'success'
        });

        this.dispatchEvent(toastEvent);

        window.location.reload();
    }

    handleReset(){
        const inputFields = this.template.querySelectorAll(
            'lightning-input-field'
        );

        if(inputFields) {
            inputFields.forEach(field => {
                field.value=null;
            });
        }
    }
}
	
```  
	
