<h1 align="center">Hi 👋, I'm Kevin Yuki Horimi</h1>
<h3 align="center">Salesforce Developer</h3>
<img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width=100px height=70px/>

- 🌱 I’m currently learning **APEX, SOQL, LWC, AURA, Visual Force, html, CSS e JS**

- 💬 Ask me about **APEX, SOQL, html, CSS, JS**

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
        System.debug('O primeiro número é o maior de todos!');
        } else if (n2 > n1 && n2 > n3){
        System.debug('O segundo número é o maior de todos!');
    } else {
        System.debug('O terceiro número é o maior de todos!');
    }


// Example of If and Else with concatenation and use of conditional AND;
String produto = 'Camiseta';
Integer quantidade;
Decimal preco = 49.99;

if(preco < 30){
    quantidade = 4;
     System.debug('Compra de '+quantidade+' '+produto+'s efetuada com sucesso!');
}else if(preco > 30 && preco <= 50){
    quantidade = 2;
    System.debug('Compra de '+quantidade+' '+produto+'s efetuada com sucesso!');
} else if (preco > 50 && preco <= 80){
    quantidade = 1;
    System.debug('Compra de '+quantidade+' '+produto+' efetuada com sucesso!');
} else {
    System.debug('Ir embora!');
}

```
## ARRAYS
```cls
/* Types of ARRAYS:
SET - Does not accept repeated value (Finds by value. Does not have index);
LIST - Accepts repeated values ​​(It has index and locates by position);
MAP - Stores more information, is in alphabetical order and does not store duplicate keys; */

// Simple example of set;
Set <String> musicStyle = new Set <String>{'Country', 'Rock'};
system.debug(musicStyle);


// Simple example of list;
String [] listaMercado = new String[]{'Batata', 'Cenoura', 'Tomate'};


// Simple example manipulating dates with list;
list <date> datas = new date[5]; 
datas[0] = date.newInstance(2001, 1, 1); 
datas[1] = date.newInstance(2002, 2, 3);
datas[2] = date.newInstance(2003, 3, 3);
datas[3] = date.newInstance(2004, 4, 4);
datas[4] = date.newInstance(2005, 5, 5);

System.debug(datas);

datas.remove(4); 
datas.remove(3);

System.debug(datas);


// Simple map example with string key and decimal return;
Map <String, Decimal> produtos2 = new Map <String, Decimal>();
produtos2.put('Prato', 29.90);
produtos2.put('Copo', 9.99);
produtos2.put('Panela', 99.90);

System.debug(produtos2);
System.debug(produtos2.get('Prato')); // Retorna o valor do produto;


// Simple examples of manipulating a list;
list <string> melhoresAlbuns = new String[5];// Definindo o tamanho da lista em 5 posições
melhoresAlbuns.add(0,'Black Ice'); // Adicionando valor a uma posição específica CRIANDO UMA NOVA POSIÇÃO NA LIST
melhoresAlbuns.set(1, 'Acustico MTV'); // Atribui valor a uma posição já existente
melhoresAlbuns[2] = 'Clara Nunes'; // Outra forma de atribuir valor a uma posição já existente

```
## METHODS /  FUNCTIONS
```cls
// Example of a simple function using Static to allow invoking the function without having to instantiate the class;
Public Class Calculadora {
    
    Public Static Void Calculo (Decimal a, String operacao, Decimal b){
        if(operacao == '+'){
            Decimal resultado = a+b;
            System.debug('O resultado é: '+resultado.setScale(2));
        } else if(operacao == '-'){
            Decimal resultado = a-b;
            System.debug('O resultado é: '+resultado.setScale(2));
        } else if(operacao == '/'){
            Decimal resultado = a/b;
            System.debug('O resultado é: '+resultado.setScale(2));
        } else if(operacao == '*'){
            Decimal resultado = a*b;
            System.debug('O resultado é: '+resultado.setScale(2));
        } else{
            System.debug('Operadores inválidos!');
        }
    }
}

// Invoking the function (Without needing to instantiate);
Calculadora.calculo(5,'*',5);
```
## SOQL
```cls
// Simple Query example;
SELECT Id, Name
FROM Account
WHERE Name Like '%Corporation%' // Contém Corporation;


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
FROM Projetos__r)
FROM Account
```
## APEX
```cls
// Delete all records that were created between July 1st and August 2nd with IF;
List <Projeto__c> lstProjeto = [SELECT Id, numeroProjeto__c, CreatedDate FROM Projeto__c];
List <Projeto__c> projetoDelete = new List <Projeto__c>();

for(Projeto__c Count: lstProjeto){
    Date DataInicio = Date.newInstance(2022, 07, 01);
    Date DataFim = Date.newInstance(2022, 08, 03);
    if(Count.CreatedDate >= DataInicio && Count.CreatedDate < DataFim){
        projetoDelete.add(Count);
    }
}
if(projetoDelete != NULL){
    DELETE projetoDelete;
}
                                                                       

// Simple example creating record with more than one RecorType via Apex;
Despesa__c newDespesa = new Despesa__c();
                                                                       
newDespesa.RecordTypeId = '0128Z000000dDp8QAE';// Assigning a Record Type by Record Type Id;
INSERT newDespesa;

 
// Simple example updating all records without despesas to 'Sem Descrição':
List <Despesa__c> lstDesp = [SELECT Id, Name, TipoDespesa__c FROM Despesa__c];
List <Despesa__c> despUpdate = new List <Despesa__c>();

for(Despesa__c Counter: lstDesp){
    if(Counter.TipoDespesa__c == null){
        Counter.TipoDespesa__c = 'Sem Descrição';
        despUpdate.add(Counter);
    }
}
if(despUpdate != NULL){
    UPDATE despUpdate;
}
```
## TRIGGER
```cls
// Simple example of Trigger structure organized by time and action;
Trigger LeadTrigger on Lead (before INSERT, before UPDATE, after INSERT, after UPDATE){
    
    if(Trigger.isBefore && Trigger.isInsert){
        LeadBO.tamanhoEmpresa(Trigger.new);
        
    }else if(Trigger.isBefore && Trigger.isUpdate){
        LeadBO.tamanhoEmpresa(Trigger.new);
        
    }else if(Trigger.isAfter && Trigger.isInsert){
        LeadBO.verificarCadastro(trigger.new);
        
    }else if(Trigger.isAfter && Trigger.isUpdate){
   
    }
}
```
## BO / HANDLER
```cls
// Simple example of a BO class that houses the methods called by the Trigger;
public class LeadBO {
    
    // Creates a task whenever a Lead with AnnualRevenue over 500,000.00 is created;
    public static void verificarCadastro (List<Lead> lstLead){
        List <Task> lstTask = new List<Task>();
        
        for(Lead Counter: lstLead){
            if(Counter.AnnualRevenue > 500000){
                task newTask = new Task();
                newTask.Subject = 'Verificar se o cadastro está completo';
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
    public static void tamanhoEmpresa(list <Lead> lstLead){ 
        for(Lead Counter2: lstLead){
            if(Counter2.NumberOfEmployees<1000){
                Counter2.TamanhoEmpresa__c = 'Pequena';
            }
        }  
    } 
}
```
## TEST CLASS
```cls
// Simple test class example: testing all calculator possibilities;
@IsTest
public class calculadoraTest {
    
    @IsTest
    public static decimal testarMetodoSoma(){
        Decimal resultSoma = Calculadora.Calculo(10,'+',5);
        System.assert(resultSoma == 15, 'Resultado da soma não é o esperado!');
        return resultSoma;
    }

    @IsTest
    public static decimal testarMetodoSubtracao(){
        Decimal resultSubtracao = Calculadora.Calculo(10,'-',5);
        System.assert(resultSubtracao == 5, 'Resultado da subtração não é o esperado!');
        return resultSubtracao;
    }
    
    @IsTest
    public static decimal testarMetodoDivisao(){
        Decimal resultDivisao = Calculadora.Calculo(10,'/',5);
        System.assert(resultDivisao == 2, 'Resultado da divisão não é o esperado!');
        return resultDivisao;
    }
    
    @IsTest
    public static decimal testarMetodoMultiplicacao(){
        Decimal resultMultiplicacao = Calculadora.Calculo(10,'*',5);
        System.assert(resultMultiplicacao == 50, 'Resultado da divisão não é o esperado!');
        return resultMultiplicacao;
    }
    
    @IsTest
    public static decimal testarError(){
        Decimal error = Calculadora.Calculo(10,'&',5);
        System.assert(error == 0, 'Resultado da divisão não é o esperado!');
        return error;
    }
}


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
        Case casoCriado = [SELECT Id, Type FROM Case WHERE Id =: newCase.id];
        System.assert (newCase.Type == 'Other', 'O caso não esta igual a Other');
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
        System.assert (newCase.Type == 'Other', 'O caso não esta igual a Other');
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

