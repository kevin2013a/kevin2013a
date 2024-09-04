<h1 align="center">Hi 👋, I'm Kevin Horimi</h1>
<h3 align="center">Salesforce Developer</h3>
<img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width=100px height=70px/>

- 🌱 I'm always improving in **APEX, SOQL, SOSL, LWC, AURA, VisualForce, JS, AMPscript**

- 🌱 I also worked with **Salesforce Data Processing Engine, Salesforce Data Manager, Microsoft Azure Data Service**

- 💬 Ask me about **APEX, SOQL, LWC, JS**

- 📫 How to reach me **kevin_horimi@hotmail.com**

<h3 align="left">Connect with me:</h3>

<a href="https://www.linkedin.com/in/kevin-yuki-horimi-a3973716a/" target="_blank" rel="noreferrer"> <img src="https://cdn-icons-png.flaticon.com/512/174/174857.png" height="40" width="40"/></a>  <a href="https://trailblazer.me/id/kevinhorimi" target="_blank" rel="noreferrer"> <img src="https://trailhead.salesforce.com/assets/trailhead-logo-5d3354441b4d8b97f21075b65e2aea266780d45943bbb36796ac25dc7cf4adc9.svg" height="40" width="40"/></a> 



</p>

<h3 align="left">Main Languages and Tools:</h3>
<p align="left"> 
<a href="https://trailblazer.me/id/kevinhorimi" target="_blank" rel="noreferrer"> <img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width="50" height="38"/> <a/> <a href="https://developer.mozilla.org/pt-BR/docs/Web/HTML" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/html5/html5-original-wordmark.svg" alt="html5" width="40" height="40"/> </a> <a href="https://developer.mozilla.org/pt-BR/docs/Web/CSS" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/css3/css3-original-wordmark.svg" alt="css3" width="40" height="40"/> </a> <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/javascript/javascript-original.svg" alt="javascript" width="40" height="40"/></a> </p>
<br/><br/>

<h3 align="left">A little bit of my work:</h3>
<h4 align="left">FlowDefinitionCleaner - Delete old flow versions by API Name</h4>
<br/>

```cls
/**
 * @autor: kevin.horimi
 */
public class FlowDefinitionCleaner {
    
    public static void deleteOldFlowVersions(String flowApiName, Integer numberOfOldVersionsToKeep) {
        // Find flows versions by API name
        List<FlowVersionView> flowVersions = [SELECT DurableId, FlowDefinitionView.ApiName, VersionNumber, Status 
                                              FROM FlowVersionView 
                                              WHERE FlowDefinitionView.ApiName = :flowApiName
                                              AND Status != 'Active'
                                              ORDER BY VersionNumber DESC];
        
        if (flowVersions.size() <= numberOfOldVersionsToKeep) {
            System.debug('No old version to delete!');
            return;
        }

        // Prepare the list of IDs to delete
        List<Id> idsToDelete = new List<Id>();

        for (Integer i = numberOfOldVersionsToKeep; i < flowVersions.size(); i++) {
            idsToDelete.add(flowVersions[i].DurableId);
        }

        // Submit delete requests using the Tooling API
        deleteFlowVersionsUsingToolingAPI(idsToDelete);
    }

    // Method for Submitting Delete Requests Using the Tooling API
    private static void deleteFlowVersionsUsingToolingAPI(List<Id> idsToDelete) {
        String url = URL.getOrgDomainUrl().toExternalForm() + '/services/data/v60.0/tooling/composite';

        List<Map<String, Object>> toolingRequests = new List<Map<String, Object>>();

        for (Integer i = 0; i < idsToDelete.size(); i++) {
            toolingRequests.add(new Map<String, Object>{
                'method' => 'DELETE',
                'url' => '/services/data/v60.0/tooling/sobjects/Flow/' + idsToDelete[i],
                'referenceId' => 'ref' + i
            });
        }

        HttpRequest req = new HttpRequest();
        req.setEndpoint(url);
        req.setMethod('POST');
        req.setHeader('Content-Type', 'application/json');
        req.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
        req.setTimeout(60000);

        // Build payload
        Map<String, Object> payload = new Map<String, Object>{
            'compositeRequest' => toolingRequests
        };

        req.setBody(JSON.serialize(payload));

        Http http = new Http();
        HttpResponse res = http.send(req);
        
        System.debug('res.getBody(): ' + res.getBody());
        System.debug('res.getStatusCode(): ' + res.getStatusCode());

        // Process the response
        if (res.getStatusCode() >= 200 && res.getStatusCode() < 300) {
            System.debug('Successfully deleted flow versions! Status code: ' + res.getStatusCode());
        } else {
            System.debug('Failed to delete flow versions Status code: ' + res.getStatusCode() + ', Response: ' + res.getBody());
        }
    }
}
```

<br/>
<h4 align="left">FlowDefinitionCleanerTest</h4>

```cls
/**
 * @author: kevin.horimi
 */
@isTest
public class FlowDefinitionCleanerTest {
    
    // Test successful return when there are versions to delete
    @isTest
    static void testSuccessDeleteOldFlowVersions() {
        Test.setMock(HttpCalloutMock.class, new MockHttpSuccessResponseGenerator());

        Test.startTest();
        FlowDefinitionCleaner.deleteOldFlowVersions('someFlowApiName', 1);
        Test.stopTest();
    }

    // Test returns with failure when there are versions to delete
    @isTest
    static void testFailDeleteOldFlowVersions() {
        Test.setMock(HttpCalloutMock.class, new MockHttpFailResponseGenerator());

        Test.startTest();
        FlowDefinitionCleaner.deleteOldFlowVersions('someFlowApiName', 1);
        Test.stopTest();
    }

    // Tests return when there are no versions to delete
    @isTest
    static void testNoOldFlowVersions() {
        Test.setMock(HttpCalloutMock.class, new MockHttpSuccessResponseGenerator());

        Test.startTest();
        FlowDefinitionCleaner.deleteOldFlowVersions('someFlowApiName', 100);
        Test.stopTest();
    }

    // Mock class for successful HTTP response
    public class MockHttpSuccessResponseGenerator implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'application/json');
            res.setBody('{"compositeResponse":[{"httpStatusCode":204,"referenceId":"ref0"},{"httpStatusCode":204,"referenceId":"ref1"}]}');
            res.setStatusCode(200);
            return res;
        }
    }

    // Mock class for failed HTTP response
    public class MockHttpFailResponseGenerator implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'application/json');
            res.setBody('{"compositeResponse":[{"httpStatusCode":404,"referenceId":"ref0"},{"httpStatusCode":404,"referenceId":"ref1"}]}');
            res.setStatusCode(404);
            return res;
        }
    }
}
```

<br/>
<h3 align="left">Another exemple:</h3>
<h4 align="left">DeleteRecordsUsingComposite - Delete records via Composite based on the record IDs</h4>

```cls
/**
 * @author: kevin.horimi
 */
public class DeleteRecordsUsingComposite {
    
    public static void deleteRecords(List<Id> idsToDelete) {
        // Prepare the composite request payload
        List<Map<String, Object>> compositeRequest = new List<Map<String, Object>>();
        Integer batchSize = 0;

        for (Integer i = 0; i < idsToDelete.size(); i++) {
            compositeRequest.add(new Map<String, Object>{
                'method' => 'DELETE',
                'url' => '/services/data/v60.0/composite/sobjects?ids=' + idsToDelete[i]
            });

            batchSize++;

            // Send batch request when reaching Salesforce's batch limit (25 in this case) or at the end of the list
            if (batchSize == 25 || i == idsToDelete.size() - 1) {
                sendCompositeRequest(compositeRequest);
                compositeRequest.clear();
                batchSize = 0;
            }
        }
    }

    // Method to send composite request to Salesforce
    private static void sendCompositeRequest(List<Map<String, Object>> compositeRequest) {
        String url = URL.getOrgDomainUrl().toExternalForm() + '/services/data/v60.0/composite';
        
        List<Map<String, Object>> requestsWithReferenceIds = new List<Map<String, Object>>();

        // Add a unique referenceId to each request
        for (Integer i = 0; i < compositeRequest.size(); i++) {
            Map<String, Object> request = compositeRequest[i];
            request.put('referenceId', 'ref' + i);
            requestsWithReferenceIds.add(request);
        }

        HttpRequest req = new HttpRequest();
        req.setEndpoint(url);
        req.setMethod('POST');
        req.setHeader('Content-Type', 'application/json');
        req.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());

        // Build the payload
        Map<String, Object> payload = new Map<String, Object>{
            'compositeRequest' => requestsWithReferenceIds
        };

        req.setBody(JSON.serialize(payload));

        Http http = new Http();
        HttpResponse res = http.send(req);
        
        System.debug('res.getBody(): ' + res.getBody());
        System.debug('res.getStatusCode(): ' + res.getStatusCode());

        // Process the response
        if (res.getStatusCode() >= 200 && res.getStatusCode() < 300) {
            System.debug('Records deleted successfully! Status code: ' + res.getStatusCode());
        } else {
            System.debug('Failed to delete records. Status code: ' + res.getStatusCode() + ', Response: ' + res.getBody());
        }
    }
}

/* 
References: 
https://salesforce.stackexchange.com/questions/233934/deleting-records-in-sales-cloud-using-rest-api#:~:text=You%20only%20have%20the%20options%20for%20GET%20and,up%20to%2025%20records%20at%20once%20this%20way.
https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_sobjects_collections_delete.htm
*/
```

<br/>
<h4 align="left">DeleteRecordsUsingCompositeTest</h4>

```cls
/**
 * @author: kevin.horimi
 */
@isTest
public with sharing class DeleteRecordsUsingCompositeTest {
    
    // Create data
    @testSetup
    static void setup() {
        List<Account> accounts = new List<Account>();
        
        for(Integer i = 0; i < 5; i++){
            Account account = new account(Name = 'Bob Test '+i
                                          // Other required fields
                                         );
            accounts.add(account);
        }
        
        insert accounts;
    }
    
    // Successful return test
    @isTest
    static void testSuccessdeleteRecords() {
        List<Id> ids = new List<Id>();
        
        List<Account> accounts = [SELECT Id FROM Account];
        
        for(Account account :accounts){
            ids.add(account.Id);   
        }
        
        Test.setMock(HttpCalloutMock.class, new MockHttpSuccessResponseGenerator());
        
        Test.startTest();
        DeleteRecordsUsingComposite.deleteRecords(ids);
        Test.stopTest();
    }

    // Test failed return
    @isTest
    static void testFaildeleteRecords() {
        List<Id> ids = new List<Id>();
        
        List<Account> accounts = [SELECT Id FROM Account];
        
        for(Account account :accounts){
            ids.add(account.Id);   
        }
            
        Test.setMock(HttpCalloutMock.class, new MockHttpFailResponseGenerator());

        Test.startTest();
        DeleteRecordsUsingComposite.deleteRecords(ids);
        Test.stopTest();
    }

    // Mock class for successful HTTP response
    public class MockHttpSuccessResponseGenerator implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'application/json');
            res.setBody('{"compositeResponse":[{"httpStatusCode":204,"referenceId":"ref0"},{"httpStatusCode":204,"referenceId":"ref1"}]}');
            res.setStatusCode(200);
            return res;
        }
    }

    // Mock class for failed HTTP response
    public class MockHttpFailResponseGenerator implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'application/json');
            res.setBody('{"compositeResponse":[{"httpStatusCode":404,"referenceId":"ref0"},{"httpStatusCode":404,"referenceId":"ref1"}]}');
            res.setStatusCode(404);
            return res;
        }
    }
}
```

<br/><br/>
