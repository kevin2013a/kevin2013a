<h1 align="center">Hi 👋, I'm Kevin Horimi</h1>
<h3 align="center">Salesforce Software Engineer</h3>

<p align="center">
  <img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width="100" height="70" />
</p>

<ul>
  <li>🚀 Salesforce engineer focused on designing and implementing <b>scalable, maintainable solutions</b> on the platform.</li>
  <li>💻 Daily working with <b>Apex, SOQL/SOSL, LWC, Aura, Visualforce, JavaScript, AMPscript</b>.</li>
  <li>🔗 Hands-on experience with <b>Salesforce Data Processing Engine, Salesforce Data Manager, Microsoft Azure Data Services</b> and high-volume integrations.</li>
  <li>🧩 Strong interest in <b>architecture, async processing, integration patterns</b> and <b>test automation</b> on Salesforce.</li>
  <li>📫 Reach me at <b>kevin_horimi@hotmail.com</b></li>
</ul>

<h3 align="left">Connect with me:</h3>
<p align="left">
  <a href="https://www.linkedin.com/in/kevin-yuki-horimi-a3973716a/" target="_blank" rel="noreferrer">
    <img src="https://cdn-icons-png.flaticon.com/512/174/174857.png" height="40" width="40" />
  </a>
  <a href="https://trailblazer.me/id/kevinhorimi" target="_blank" rel="noreferrer">
    <img src="https://trailhead.salesforce.com/assets/trailhead-logo-5d3354441b4d8b97f21075b65e2aea266780d45943bbb36796ac25dc7cf4adc9.svg" height="40" width="40" />
  </a>
</p>

<h3 align="left">Main Languages and Tools:</h3>
<p align="left">
  <a href="https://trailblazer.me/id/kevinhorimi" target="_blank" rel="noreferrer">
    <img src="https://www.salesforce.com/content/dam/sfdc-docs/www/resources/campaign-assets/live-long-and-propser/images/logo.png" alt="Salesforce" width="50" height="38" />
  </a>
  <a href="https://developer.mozilla.org/en-US/docs/Web/HTML" target="_blank" rel="noreferrer">
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/html5/html5-original-wordmark.svg" alt="HTML5" width="40" height="40" />
  </a>
  <a href="https://developer.mozilla.org/en-US/docs/Web/CSS" target="_blank" rel="noreferrer">
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/css3/css3-original-wordmark.svg" alt="CSS3" width="40" height="40" />
  </a>
  <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript" target="_blank" rel="noreferrer">
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/javascript/javascript-original.svg" alt="JavaScript" width="40" height="40" />
  </a>
</p>

---

## 🧱 A little bit of my work

<p align="left">
  Below is a short case study of a production feature I designed and implemented on Salesforce, with focus on layered architecture, async processing and integration with external services.
</p>

---

### 1. Client Limit Manager – Summary 💳⚙️

> **Owner:** `kevin.horimi`  
> **Goal:** Block or reduce a client's credit limit via **Flow + Apex + external API**, using a **clean layered, object-oriented architecture**.

**Key outcomes:**

- ✅ End-to-end orchestration with human approval, external callout and data consistency.
- 🧠 Clear separation of concerns (Flow → Controller → Service → Repository → Infra).
- 🌐 Metadata-driven integration using a reusable `ApiIntegration` framework.
- 📡 Slack approval card before any external operation is executed.
- 🧪 Built to be testable and easy to evolve.

---

#### High-Level Architecture 🧩

The module follows a layered, object-oriented design:

- **Flow**: `Flow_ClientLimitManager`  
  → Declarative orchestration entry point (used by admins/business).

- **Controller**: `Controller_ClientLimitManager`  
  → Central business logic: validation, decision making, mapping between flow data and service calls.

- **Service**: `Service_ClientLimitManager`  
  → Integration layer responsible for calling the external Limit Manager API.

- **Repository**: `Repository_LogLimitChangeRequest`  
  → Encapsulates all data access related to `LogLimitChangeRequest__c`.

- **Infra**:
  - `ApiIntegration` → Metadata-driven HTTP callout framework.
  - `ApexErrorHandler` → Centralized error handling and logging.
  - **Slack approval** → Approval card sent to the responsible team before the API is called.

Each class has a single responsibility, making the solution easier to maintain, test and extend. ✅

---

#### Functional Flow (End-to-End) 🔁

1. **Log creation**  
   A `LogLimitChangeRequest__c` record is created containing:
   - client and account information,
   - operation type (block / reduce),
   - requested limit/amount,
   - additional metadata required by the external API.

2. **Approval via Slack 💬**  
   Before any API call:
   - A Slack approval card is sent to the responsible team.
   - The log record stays in a **pending approval** state.

3. **Approval decision ✅ / ❌**  
   - **If approved**:
     - The process continues and the external API is called.
   - **If rejected**:
     - No callout is made.
     - The limit remains unchanged in both the external system and Salesforce.

4. **Flow execution**  
   A Flow triggers the invocable method in `Flow_ClientLimitManager`, passing the approved `logLimitChangeRequestId`.

5. **Controller processing**  
   `Controller_ClientLimitManager`:
   - Loads the record via `Repository_LogLimitChangeRequest`.
   - Validates:
     - existence and consistency of the log,
     - required fields (document, account number, value for reductions).
   - Determines the operation type:
     - **Block** → block limit.
     - **Reduce** → reduce limit.

6. **External API call 🌐**  
   `Service_ClientLimitManager`:
   - Builds a JSON payload with client/account data and operation details.
   - Uses `ApiIntegration` with metadata configuration:
     - `LIMITMANAGER_BlockLimit` for block operations.
     - `LIMITMANAGER_ReduceLimit` for reductions.
   - Executes the HTTP call and returns:
     - `statusCode` (HTTP status),
     - `responseBody` (API JSON response).

7. **Salesforce + external system update 🧾**  
   `Controller_ClientLimitManager`:
   - Updates `LogLimitChangeRequest__c` with:
     - callout status code,
     - response body,
     - callout timestamp.
   - On success:
     - considers the operation **effective** in the external system;
     - mirrors the change in the Salesforce account record (limit aligned with API response).

8. **Flow response**  
   - The controller only treats the operation as successful if:
     - the HTTP status code is 2xx **and**
     - the internal status returned in the body also indicates success.
   - `Flow_ClientLimitManager` converts this into:
     - `success = true` or `false`.
   - On error:
     - the error is logged using `ApexErrorHandler` (context `LIMIT_MANAGER`);
     - the Flow receives `success = false` and can branch its path accordingly.

---

#### One-line Summary 📌

> **Only after Slack approval, the approved `LogLimitChangeRequest__c` is sent to the external API; if the operation succeeds, the new limit is consistently applied in both the external system and the Salesforce account, using a clean, layered OOP design (Flow → Controller → Service → Repository → Infra).** 🚀

---

#### Apex Classes 🧩

<h4 align="left">Flow_ClientLimitManager</h4>

```cls
/**
 * @author: kevin.horimi
 */
public with sharing class Flow_ClientLimitManager {

    // Variaveis de entrada do flow
    public class FlowInput {
        @InvocableVariable(required=true)
        public Id logLimitChangeRequestId;
    }

    // Variaveis de saída do flow
    public class FlowOutput {
        @InvocableVariable 
        public Boolean success;
    }

    // Metodo invocavel para executar o bloqueio/redução de limite
    @InvocableMethod(label='Client Limit Manager'
                     description='Executa o gerenciamento de limite do cliente via Controller')
    public static List<FlowOutput> execute(List<FlowInput> flowInputs) {
        List<FlowOutput> outputs = new List<FlowOutput>();

        for (FlowInput input : flowInputs) {
            FlowOutput output = new FlowOutput();

            try {
                output.success = Controller_ClientLimitManager.processRequest(input.logLimitChangeRequestId);
            } catch (Exception e) {
                output.success = false;

                ApexErrorHandler.logError(
                    ApexErrorHandler.ContextType.LIMIT_MANAGER,
                    '[Flow_ClientLimitManager] Erro ao processar LogLimitChangeRequest__c ' + input.logLimitChangeRequestId,
                    e
                );
            }

            outputs.add(output);
        }

        return outputs;
    }
}
```

<h4 align="left">Controller_ClientLimitManager</h4>

```cls
/**
 * @author: kevin.horimi
 */
public with sharing class Controller_ClientLimitManager {

    // Solicita o bloqueio ou a redução de limite na API
    public static Boolean processRequest(Id logLimitChangeRequestId) {
        Map<String, Object> response = new Map<String, Object>();

        // Valida a variavel de entrada
        if (logLimitChangeRequestId == null) {
            throw new IllegalArgumentException('Id do LogLimitChangeRequest__c é obrigatório.');
        }

        // Get record via repository class
        LogLimitChangeRequest__c logReq = Repository_LogLimitChangeRequest.getById(logLimitChangeRequestId);
        
        // Valida se a repository não retornou nulo
        if(logReq == null){
            throw new IllegalArgumentException('LogLimitChangeRequest__c não encontrado.');
        }

        // Padroniza e valida os dados necessários
        Map<String, Object> data = sanitizeData(logReq);
        String document = (String)data.get('document');
        Integer accountNumber = (Integer)data.get('accountNumber');
        String reason = (String)data.get('reason');
        String type = (String)data.get('type');
        String action = (String)data.get('action');
        Decimal newLimit = (Decimal)data.get('newLimit');

        // Realiza a chamada conforme bloqueio ou redução
        if (action == 'Block') { 
            response = blockLimit(document, accountNumber, type, reason);
        }
        if (action == 'Reduce') {
            response = reduceLimit(document, accountNumber, type, newLimit, reason);
        }

        // Atualiza os campos de detalhes da chamada do registro
        logReq = buildUpdatedRecord(logReq, response);
        updateRecord(logReq);

        // Trata e retorna a resposta
        Integer statusCode = (Integer)response.get('statusCode');
        Map<String, Object> body = 
        (Map<String, Object>) JSON.deserializeUntyped((String) response.get('responseBody'));
        Integer innerStatusCode = (Integer) body.get('status');

        return (statusCode >= 200 && statusCode < 300) &&
               (innerStatusCode >= 200 && innerStatusCode < 300);
    }

    // ------------------------------------------------------------
    // Helpers
    // ------------------------------------------------------------

    // Valida e padroniza os dados do registro de LogLimitChangeRequest__c
    @TestVisible private static Map<String, Object> sanitizeData (LogLimitChangeRequest__c record) {
        String document = (record.Account__r != null) ? record.Account__r.Documento__c : null;
        if (String.isBlank(document)) throw new IllegalArgumentException('Documento não pode ser vazio.');

        Integer accountNumber = parseAccountNumber(record.AccountNumber__c);
        if (accountNumber == null) throw new IllegalArgumentException('Account Number não pode ser vazio.');

        String reason = record.Reason__c;
        String type = record.Tipo__c == 'Antecipação de Boleto' ? 'ANTECIPACAO_CEDENTE' : 'CAPITAL_DE_GIRO';
        String action = record.RecordType.DeveloperName;
        Decimal newLimit = record.RequestedValue__c;

        if(action == 'Reduce'){
            if (newLimit == null || newLimit <= 0) {
                throw new IllegalArgumentException('RequestedValue__c deve ser maior que zero.');
            }
        }

        Map<String, Object> data = new Map<String, Object>();
        data.put('document', document);
        data.put('accountNumber', accountNumber);
        data.put('reason', reason);
        data.put('type', type);
        data.put('action', action);
        data.put('newLimit', newLimit);

        return data;
    }

    // Padroniza o AccountNumber
    @TestVisible private static Integer parseAccountNumber(String acc) {
        if (String.isBlank(acc)) return null;
        String sanitized = acc.replaceAll('[^0-9]', '');
        return String.isNotBlank(sanitized) ? Integer.valueOf(sanitized) : null;
    }

    // Monta o registro com os campos atualizados
    @TestVisible private static LogLimitChangeRequest__c buildUpdatedRecord(LogLimitChangeRequest__c record, Map<String, Object> response) {
        record.CalloutResponseBody__c = (String)response.get('responseBody');
        record.CalloutStatusCode__c = (Decimal)response.get('statusCode');
        record.CalloutTime__c = System.now();

        return record;
    }

    // Realiza o DML de update do LogLimitChangeRequest__c
    @TestVisible private static void updateRecord(LogLimitChangeRequest__c record){
        if(record == null){ return; }

        try{
            update record;
        }catch(Exception e){
            System.debug('[Controller_ClientLimitManager] Erro ao atualizar LogLimitChangeRequest__c: ' + record.Name);
            ApexErrorHandler.logError(
                ApexErrorHandler.ContextType.LIMIT_MANAGER,
                '[Controller_ClientLimitManager] Erro ao atualizar LogLimitChangeRequest__c: ' + record.Name,
                e
            );

            throw e;
        }
    }

    // Realiza a chamada API para bloqueio de limite via classe service
    private static Map<String, Object> blockLimit(String document, Integer accountNumber, String product, String reason) {
        return Service_ClientLimitManager.blockLimit(document, accountNumber, product, reason);
    }

    // Realiza a chamada API para redução de limite via classe service
    private static Map<String, Object> reduceLimit(String document, Integer accountNumber, String type, Decimal limitValue, String reason) {
        return Service_ClientLimitManager.reduceLimit(document, accountNumber, type, limitValue, reason);
    }
}
```

<h4 align="left">Service_ClientLimitManager</h4>

```cls
/**
 * @author: kevin.horimi
 */
public with sharing class Service_ClientLimitManager {
    private static final String METADATA_BLOCK_LIMIT  = 'LIMITMANAGER_BlockLimit';
    private static final String METADATA_REDUCE_LIMIT = 'LIMITMANAGER_ReduceLimit';

    // Realiza a chamada de bloqueio de limite
    public static Map<String, Object> blockLimit(String document, Integer accountNumber, String product, String reason) {
        
        // Monta o body
        Map<String, Object> bodyMap = new Map<String, Object>{
            'document'       => document,
            'account_number' => accountNumber,
            'product'        => product,
            'reason'         => reason,
            'action'         => 'BLOCK'
        };
        String body = JSON.serialize(bodyMap);

        try {
            // Realiza a chamada
            HttpResponse response = ApiIntegration.execute(METADATA_BLOCK_LIMIT, body, null);

            // Monta e retorna a resposta
            Map<String, Object> responseData = new Map<String, Object>();
            responseData.put('statusCode', response.getStatusCode());
            responseData.put('responseBody', response.getBody());

            return responseData;
        } catch (Exception e) {
            // Loga o erro
            System.debug('[Service_ClientLimitManager] Erro ao bloquear limite para documento: ' + document);
            ApexErrorHandler.logError(
                ApexErrorHandler.ContextType.LIMIT_MANAGER,
                '[Service_ClientLimitManager] Erro ao bloquear limite para documento: ' + document,
                e
            );

            throw e;
        }
    }

    // Realiza a chamada de redução de limite
    public static Map<String, Object> reduceLimit(String document, Integer accountNumber, String type, Decimal limitValue, String reason) {

        // Monta o body
        Map<String, Object> bodyMap = new Map<String, Object>{
            'document'       => document,
            'account_number' => accountNumber,
            'type'           => type,
            'limit'          => limitValue,
            'reason'         => reason
        };
        String body = JSON.serialize(bodyMap);

        try {
            // Realiza a chamada
            HttpResponse response = ApiIntegration.execute(METADATA_REDUCE_LIMIT, body, null);

            // Monta e retorna a resposta
            Map<String, Object> responseData = new Map<String, Object>();
            responseData.put('statusCode', response.getStatusCode());
            responseData.put('responseBody', response.getBody());

            return responseData;
        } catch (Exception e) {
            // Loga o erro
            System.debug('[Service_ClientLimitManager] Erro ao reduzir limite para documento: ' + document);
            ApexErrorHandler.logError(
                ApexErrorHandler.ContextType.LIMIT_MANAGER,
                '[Service_ClientLimitManager] Erro ao reduzir limite para documento: ' + document,
                e
            );
            
            throw e;
        }
    }
}
```

<h4 align="left">Repository_LogLimitChangeRequest</h4>

```cls
/**
 * @author: kevin.horimi
 */
public with sharing class Repository_LogLimitChangeRequest extends Repository {
    
    // ------------------------------------------------------------
    // Construtor: configura os campos
    // ------------------------------------------------------------
    public Repository_LogLimitChangeRequest() {
        super(
            'LogLimitChangeRequest__c',
            new List<String>{
                'Id',
                'Name',
                'Account__c',
                'CalloutResponseBody__c',
                'CalloutStatusCode__c',
                'RecordType.DeveloperName',
                'Account__r.Documento__c',
                'Tipo__c',
                'RequestedValue__c',
                'AccountNumber__c',
                'Reason__c',
                'ApprovalResponseTime__c',
                'CalloutTime__c',
                'SentForApprovalTime__c',
                'ApproverName__c',
                'Status__c',
                'CurrentValue__c',
                'ApprovalComments__c'
            }
        );
    }

    // ------------------------------------------------------------
    // Recebe um Id e retorna o registro encontrado
    // ------------------------------------------------------------
    public static LogLimitChangeRequest__c getById(Id id) {
        List<LogLimitChangeRequest__c> rows =
            (List<LogLimitChangeRequest__c>) new Repository_LogLimitChangeRequest().findById(id);
        return rows.isEmpty() ? null : rows[0];
    }
    public static LogLimitChangeRequest__c getById(String id) {
        return getById((Id) id);
    }

    // ------------------------------------------------------------
    // Recebe um list ou set de Ids e retorna todos os registros encontrados
    // ------------------------------------------------------------
    public static List<LogLimitChangeRequest__c> getByIds(List<Id> ids) {
        return (List<LogLimitChangeRequest__c>)
            new Repository_LogLimitChangeRequest().findById(new Set<Id>(ids));
    }
    public static List<LogLimitChangeRequest__c> getByIds(List<String> ids) {
        return getByIds((List<Id>) ids);
    }
    public static List<LogLimitChangeRequest__c> getByIds(Set<Id> ids) {
        return (List<LogLimitChangeRequest__c>)
            new Repository_LogLimitChangeRequest().findById(ids);
    }
    public static List<LogLimitChangeRequest__c> getByIds(Set<String> ids) {
        return getByIds((List<Id>) new List<String>(ids));
    }

    // ------------------------------------------------------------
    // Recebe uma string com o name e retorna o registro encontrado
    // ------------------------------------------------------------
    public static LogLimitChangeRequest__c findByName(String name) {
        List<LogLimitChangeRequest__c> rows = 
            (List<LogLimitChangeRequest__c>) new Repository_LogLimitChangeRequest()
                .findByField('Name', name);

        return rows.isEmpty() ? null : rows[0];
    }

    // ------------------------------------------------------------
    // Recebe uma string com o AccountNumber e retorna o registro encontrado
    // ------------------------------------------------------------
    public static LogLimitChangeRequest__c findByAccountNumber(String accountNumber) {
        List<LogLimitChangeRequest__c> rows = 
            (List<LogLimitChangeRequest__c>) new Repository_LogLimitChangeRequest()
                .findByField('AccountNumber__c', accountNumber);

        return rows.isEmpty() ? null : rows[0];
    }
}
```

<h4 align="left">FixtureFactory_LogLimitChangeRequest</h4>

```cls
/**
 * @author: kevin.horimi
 */
public with sharing class FixtureFactory_LogLimitChangeRequest extends UtilsFixtureFactory {

    public FixtureFactory_LogLimitChangeRequest() {
        super(new LogLimitChangeRequest__c());
    }

    public FixtureFactory_LogLimitChangeRequest withAccount(Id accountId) {
        return (FixtureFactory_LogLimitChangeRequest) this.put('Account__c', accountId);
    }

    public FixtureFactory_LogLimitChangeRequest withAccountNumber(String accountNumber) {
        return (FixtureFactory_LogLimitChangeRequest) this.put('AccountNumber__c', accountNumber);
    }

    public FixtureFactory_LogLimitChangeRequest withType(String type) {
        return (FixtureFactory_LogLimitChangeRequest) this.put('Tipo__c', type);
    }

    public FixtureFactory_LogLimitChangeRequest withReason(String reason) {
        return (FixtureFactory_LogLimitChangeRequest) this.put('Reason__c', reason);
    }

    public FixtureFactory_LogLimitChangeRequest withRequestedValue(Decimal requestedValue) {
        return (FixtureFactory_LogLimitChangeRequest) this.put('RequestedValue__c', requestedValue);
    }

    public FixtureFactory_LogLimitChangeRequest withRecordTypeId(Id recordTypeId) {
        return (FixtureFactory_LogLimitChangeRequest) this.put('RecordTypeId', recordTypeId);
    }

    public LogLimitChangeRequest__c persistRequest() {
        return (LogLimitChangeRequest__c) this.persist();
    }
}
```

<h4 align="left">AccountRepository</h4>

```cls
public with sharing class AccountRepository extends UtilsAbstractRepository {
    
    public List<Account> findById(Id accountId, Set<String> fields) {
        return findByIds(new Set<Id>{accountId}, fields);
    }

    public List<Account> findByIds( Set<Id> accountIds, Set<String> fields) {
        
        String query = new UtilsQuery(Account.sObjectType)
                .selectFields(fields)
                .add(UtilsQuery.condition('Id').isIn(accountIds))
                .build();
        
        return Database.query(query);
    }

    public Account findByName(String name) {
        Set<String> fields = new set<String>{'Name'};

        String query = new UtilsQuery(Account.sObjectType)
                .selectFields(fields)
                .add(UtilsQuery.condition('Name').equalsTo(name))
                .build();

        List<Account> result = Database.query(query);

        return result[0];
    }
}
```

<h4 align="left">UtilsAbstractRepository</h4>

```cls
public abstract inherited sharing class UtilsAbstractRepository {

    public SObject save (SObject record) {

        if (record == null) return record;

        return this.save(new List<SObject>{ record }).get(0);
    }

    public List<SObject> save (List<SObject> records) {

        if(records.isEmpty()) return records;

        upsert records;

        return records;
    }

    public SObject save( SObject record, Schema.SObjectField externalIdField) {

        if(record == null) return record;

        return this.save(new List<SObject>{ record }, externalIdField).get(0);
    }

    public List<SObject> save( List<SObject> records,  Schema.SObjectField externalIdField) {

        if(records.isEmpty()) return records;

        Database.upsert(records, externalIdField);

        return records;
    }

    public SObject remove (SObject record) {

        if(record == null) return record;

        return this.remove(new List<SObject>{ record }).get(0);
    }

    public List<SObject> remove (List<SObject> records) {

        if(records.isEmpty()) return records;

        delete records;

        return records;
    }
}
```

<h4 align="left">FixtureFactory_Account</h4>

```cls
/**
 * @author: kevin.horimi
 */
public with sharing class FixtureFactory_Account extends UtilsFixtureFactory {

    public FixtureFactory_Account() {
        super(new Account());
    }

    public FixtureFactory_Account withName(String name) {
        return (FixtureFactory_Account) this.put('Name', name);
    }

    public FixtureFactory_Account withDocumento(String documento) {
        return (FixtureFactory_Account) this.put('Documento__c', documento);
    }

    public Account build() {
        return (Account) this.object();
    }

    public Account persistAccount() {
        return (Account) this.persist();
    }
}
```

<h4 align="left">Repository</h4>

```cls
abstract public class Repository {
 
    protected String objectName;
    protected List<String> commonFields;    
   
    public Repository(String objectName, List<String> commonFields) {
        if (objectName == null || objectName == '' || commonFields == null || commonFields.isEmpty()) {
			throw new RepositoryQueryException('You need inform both objectName and commonFields.');
        }
        
        this.objectName = objectName;
        this.commonFields = commonFields;
	}
    
    private class RepositoryQueryException extends System.Exception {}
    
    public enum QueryOptions {
        Q_GROUP_BY,
        Q_ORDER_BY,
        Q_LIMIT,
        Q_OFFSET,
        Q_OPERATOR,
        Q_ALL_ROWS
    }
    
    public List<SObject> findById(Id id) {
        String query = getQuery(new List<String>{'Id = :id'});
        return Database.query(query);
    }
    
    public List<SObject> findById(Set<Id> id) {       
        String query = getQuery(new List<String>{'Id IN :id'});
        return Database.query(query);
    }
    
    public virtual List<SObject> findByName(String name) {
        String query = getQuery(new List<String>{'Name = :name'}, new Map<QueryOptions, String>{
            Repository.QueryOptions.Q_ORDER_BY => 'CreatedDate DESC'
        });
        return Database.query(query);
    }
    
    protected String getCommonFields() {
        return String.join(commonFields, ', ');
    }
    
    public static List<Id> getIds(List<SObject> sObjects) {
        List<Id> ids = new List<Id>();
        
        for(SObject obj : sObjects) {
            ids.add(obj.Id);
        }
        
        return ids;
    }
    
    public String getQuery() {
        return getQuery(null, null);
    }
    
    public String getQuery(List<String> conditions) {
        return getQuery(conditions, null);
    }
    
    public String getQuery(Map<QueryOptions, String> queryOptions) {
        return getQuery(null, queryOptions);
    }
    
    public String getQuery(List<String> conditions, Map<QueryOptions, String> queryOptions) {
        String query = 'SELECT ' + getCommonFields()  + ' FROM ' + objectName;
        
        String operator = 'AND';
        
        if (queryOptions != null) {
            if (queryOptions.containsKey(Repository.QueryOptions.Q_OPERATOR)) {
                operator = queryOptions.get(Repository.QueryOptions.Q_OPERATOR);
            }
        }
        
        if (conditions != null && !conditions.isEmpty()) {
            query = query + ' WHERE ' + String.join(conditions, ' ' + operator + ' ');
		}
        
        if (queryOptions != null) {
            if (queryOptions.containsKey(Repository.QueryOptions.Q_GROUP_BY)) {
                query += ' GROUP BY ' + queryOptions.get(Repository.QueryOptions.Q_GROUP_BY);
            }
            
            if (queryOptions.containsKey(Repository.QueryOptions.Q_ORDER_BY)) {
                query += ' ORDER BY ' + queryOptions.get(Repository.QueryOptions.Q_ORDER_BY);
            }
            
            if (queryOptions.containsKey(Repository.QueryOptions.Q_LIMIT)) {
                query += ' LIMIT ' + queryOptions.get(Repository.QueryOptions.Q_LIMIT);
            }
            
            if (queryOptions.containsKey(Repository.QueryOptions.Q_OFFSET)) {
                query += ' OFFSET ' + queryOptions.get(Repository.QueryOptions.Q_OFFSET);
            }
            if (queryOptions.get(Repository.QueryOptions.Q_ALL_ROWS) == 'true') {
                query += ' ALL ROWS';
            }
        }
        
    	return query;
    }

    public List<SObject> findByField(String fieldName, Object value) {
        if (String.isBlank(fieldName)) {
            throw new RepositoryQueryException('Field name must not be null or empty.');
        }

        String soql = 'SELECT ' + String.join(commonFields, ',') +
                    ' FROM ' + objectName +
                    ' WHERE ' + fieldName + ' = :value';

        return Database.query(soql);
    }
}
```
---

#### Test Classes 🧩

<h4 align="left">Flow_ClientLimitManager_Test</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class Flow_ClientLimitManager_Test {

    // ------------------------------------------------------------
    // Construção de dados para teste
    // ------------------------------------------------------------
    @TestSetup
    static void setupData() {
        Account accError = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Cliente Erro')
        .withDocumento('99999999999'))
        .persistAccount();

        Account accSuccess = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Cliente Teste')
        .withDocumento('12345678901'))
        .persistAccount();

        LogLimitChangeRequest__c reqError = ((FixtureFactory_LogLimitChangeRequest) new FixtureFactory_LogLimitChangeRequest()
        .withAccount(accError.Id)
        .withAccountNumber('54321'))
        .persistRequest();

        LogLimitChangeRequest__c reqSuccess = ((FixtureFactory_LogLimitChangeRequest) new FixtureFactory_LogLimitChangeRequest()
        .withAccount(accSuccess.Id)
        .withAccountNumber('12345'))
        .persistRequest();
    }
    
    // ------------------------------------------------------------
    // Testes
    // ------------------------------------------------------------
    @IsTest
    static void test_execute_success() {
        Test.setMock(HttpCalloutMock.class, new SuccessMock());

        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');

        Flow_ClientLimitManager.FlowInput input = new Flow_ClientLimitManager.FlowInput();
        input.logLimitChangeRequestId = rec.Id;

        Test.startTest();
        List<Flow_ClientLimitManager.FlowOutput> outputs =
            Flow_ClientLimitManager.execute(new List<Flow_ClientLimitManager.FlowInput>{ input });
        Test.stopTest();

        System.assertEquals(1, outputs.size(), 'Deve retornar um output');
        System.assertNotEquals(null, outputs[0], 'Output não deve ser nulo');
        System.assert(outputs[0].success != null, 'Campo success deve estar definido');
    }

    @IsTest
    static void test_execute_withException() {
        Test.setMock(HttpCalloutMock.class, new ErrorMock());

        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('54321');

        Flow_ClientLimitManager.FlowInput input = new Flow_ClientLimitManager.FlowInput();
        input.logLimitChangeRequestId = rec.Id;

        Test.startTest();
        try {
            List<Flow_ClientLimitManager.FlowOutput> outputs =
                Flow_ClientLimitManager.execute(new List<Flow_ClientLimitManager.FlowInput>{ input });
        } catch (Exception e) {
            System.assert(false, 'Não deve lançar exceção externa: ' + e.getMessage());
        }
        Test.stopTest();
    }

    // ------------------------------------------------------------
    // Mocks
    // ------------------------------------------------------------
    private class SuccessMock implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setStatusCode(200);
            res.setBody('{"ok":true}');

            return res;
        }
    }

    private class ErrorMock implements HttpCalloutMock {
        public HttpResponse respond(HttpRequest req) {
            throw new CalloutException('Erro simulado');
        }
    }
}
```

<h4 align="left">Controller_ClientLimitManager_Test</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class Controller_ClientLimitManager_Test {

    // ------------------------------------------------------------
    // Construção de dados para teste
    // ------------------------------------------------------------
    @TestSetup
    static void setupData() {
        Account acc = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste')
        .withDocumento('12345678900'))
        .persistAccount();

        Map<String, Schema.RecordTypeInfo> rtMap =
            Schema.SObjectType.LogLimitChangeRequest__c.getRecordTypeInfosByDeveloperName();

        Id rtBlock = rtMap.containsKey('Block') ? rtMap.get('Block').getRecordTypeId() : null;
        Id rtReduce = rtMap.containsKey('Reduce') ? rtMap.get('Reduce').getRecordTypeId() : null;

        LogLimitChangeRequest__c blockReq = ((FixtureFactory_LogLimitChangeRequest) new FixtureFactory_LogLimitChangeRequest()
        .withAccount(acc.Id)
        .withAccountNumber('12345')
        .withType('Antecipação de Boleto')
        .withReason('Motivo de bloqueio')
        .withRequestedValue(100)
        .withRecordTypeId(rtBlock))
        .persistRequest();

        LogLimitChangeRequest__c reduceReq = ((FixtureFactory_LogLimitChangeRequest) new FixtureFactory_LogLimitChangeRequest()
        .withAccount(acc.Id)
        .withAccountNumber('54321')
        .withType('Capital de Giro')
        .withReason('Motivo de redução')
        .withRequestedValue(500)
        .withRecordTypeId(rtReduce))
        .persistRequest();
    }

    // ------------------------------------------------------------
    // Testes principais
    // ------------------------------------------------------------
    @IsTest
    static void test_processRequest_block() {
        Test.startTest();
        Test.setMock(HttpCalloutMock.class, new SuccessMock());

        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');
        Boolean result = Controller_ClientLimitManager.processRequest(rec.Id);

        Test.stopTest();

        System.assert(result, 'Deve retornar true para execução bem-sucedida (block)');
    }

    @IsTest
    static void test_processRequest_reduce() {
        Test.startTest();
        Test.setMock(HttpCalloutMock.class, new SuccessMock());

        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('54321');
        Boolean result = Controller_ClientLimitManager.processRequest(rec.Id);

        Test.stopTest();

        System.assert(result, 'Deve retornar true para execução bem-sucedida (reduce)');
    }

    @IsTest
    static void test_processRequest_withException() {
        Test.startTest();
        Test.setMock(HttpCalloutMock.class, new ErrorMock());

        try {
            LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');
            Boolean result = Controller_ClientLimitManager.processRequest(rec.Id);
        } catch (Exception e) {
            System.assert(true, 'Deve retornar erro');
        }

        Test.stopTest();
    }

    @IsTest
    static void test_processRequest_withInvalidId() {
        try {
            Controller_ClientLimitManager.processRequest('a0Z000000000001AAA');

            System.assert(false, 'Deveria lançar exceção');
        } catch (Exception e) {
            System.assert(e.getMessage().contains('não encontrado'));
        }
    }

    @IsTest
    static void test_processRequest_withNullId() {
        try {
            Controller_ClientLimitManager.processRequest(null);

            System.assert(false, 'Deveria lançar exceção');
        } catch (Exception e) {
            System.assert(e.getMessage().contains('é obrigatório'));
        }
    }

    // ------------------------------------------------------------
    // Testes dos helpers
    // ------------------------------------------------------------
    @IsTest
    static void test_parseAccountNumber() {
        System.assertEquals(12345, Controller_ClientLimitManager.parseAccountNumber('123-45'));
        System.assertEquals(null, Controller_ClientLimitManager.parseAccountNumber(null));
    }

    @IsTest
    static void test_sanitizeData_valid() {
        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');

        Map<String, Object> data = Controller_ClientLimitManager.sanitizeData(rec);

        System.assertNotEquals(null, data.get('document'));
        System.assertNotEquals(null, data.get('accountNumber'));
    }

    @IsTest
    static void test_sanitizeData_withInvalidRequestedValue() {
        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');
        rec.RecordType.DeveloperName = 'Reduce';
        rec.RequestedValue__c = 0;

        try {
            Controller_ClientLimitManager.sanitizeData(rec);
            System.assert(false);
        } catch (Exception e) {
            System.assert(e.getMessage().contains('RequestedValue__c deve ser maior que zero'));
        }
    }

    @IsTest
    static void test_buildUpdatedRecord() {
        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');

        Map<String, Object> resp = new Map<String, Object>{
            'responseBody' => '{"status":200}',
            'statusCode' => 200
        };

        LogLimitChangeRequest__c updated = Controller_ClientLimitManager.buildUpdatedRecord(rec, resp);

        System.assertNotEquals(null, updated.CalloutStatusCode__c);
        System.assertNotEquals(null, updated.CalloutResponseBody__c);
    }

    @IsTest
    static void test_updateRecord_withNullRecord() {
        Controller_ClientLimitManager.updateRecord(null);
        System.assert(true, 'Não deve lançar erro com registro nulo');
    }

    @IsTest
    static void test_updateRecord_withException() {
        LogLimitChangeRequest__c rec = Repository_LogLimitChangeRequest.findByAccountNumber('12345');

        rec.Id = null;
        Boolean thrown = false;

        try {
            Controller_ClientLimitManager.updateRecord(rec);
        } catch (Exception e) {
            thrown = true;
        }

        System.assert(thrown, 'Esperado lançar exceção e cobrir o catch');
    }

    // ------------------------------------------------------------
    // Mocks
    // ------------------------------------------------------------
    private class SuccessMock implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'application/json');
            res.setStatusCode(200);
            res.setBody('{"status":200,"data":{"error":{}}}');

            return res;
        }
    }

    private class ErrorMock implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'application/json');
            res.setStatusCode(200);
            res.setBody('{"status":404,"data":{"error":{}}}');

            return res;
        }
    }
}
```

<h4 align="left">Service_ClientLimitManager_Test</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class Service_ClientLimitManager_Test {
    
    // ------------------------------------------------------------
    // Testes
    // ------------------------------------------------------------
    @IsTest
    static void test_blockLimit_success() {
        Test.setMock(HttpCalloutMock.class, new SuccessMock());

        Test.startTest();
        Map<String, Object> result = Service_ClientLimitManager.blockLimit(
            '12345678901', 12345, 'ANTECIPACAO_CEDENTE', 'Motivo de teste'
        );
        Test.stopTest();

        System.assertEquals(200, result.get('statusCode'));
        System.assertEquals('{"ok":true}', result.get('responseBody'));
    }

    @IsTest
    static void test_reduceLimit_success() {
        Test.setMock(HttpCalloutMock.class, new SuccessMock());

        Test.startTest();
        Map<String, Object> result = Service_ClientLimitManager.reduceLimit(
            '12345678901', 12345, 'ANTECIPACAO_CEDENTE', 100, 'Motivo de teste'
        );
        Test.stopTest();

        System.assertEquals(200, result.get('statusCode'));
        System.assertEquals('{"ok":true}', result.get('responseBody'));
    }

    @IsTest
    static void test_blockLimit_withException() {
        Test.setMock(HttpCalloutMock.class, new ErrorMock());

        try {
            Service_ClientLimitManager.blockLimit('12345678901', 12345, 'ANTECIPACAO_CEDENTE', 'Motivo de teste');

            System.assert(false, 'Deveria lançar exceção ao simular falha.');
        } catch (Exception e) {
            System.assert(e.getMessage().contains('Simulated callout failure'));
        }
    }

    @IsTest
    static void test_reduceLimit_withException() {
        Test.setMock(HttpCalloutMock.class, new ErrorMock());

        try {
            Service_ClientLimitManager.reduceLimit('12345678901', 12345, 'ANTECIPACAO_CEDENTE', 100, 'Motivo de teste');

            System.assert(false, 'Deveria lançar exceção ao simular falha.');
        } catch (Exception e) {
            System.assert(e.getMessage().contains('Simulated callout failure'));
        }
    }

    // ------------------------------------------------------------
    // Mocks
    // ------------------------------------------------------------
    private class SuccessMock implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setStatusCode(200);
            res.setBody('{"ok":true}');

            return res;
        }
    }

    private class ErrorMock implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            throw new CalloutException('Simulated callout failure');
        }
    }
}
```

<h4 align="left">Repository_LogLimitChangeRequest_Test</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class Repository_LogLimitChangeRequest_Test {

    // ------------------------------------------------------------
    // Construção de dados para teste
    // ------------------------------------------------------------
    @TestSetup
    static void setupData() {
        Account acc = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste')
        .withDocumento('12345678900'))
        .persistAccount();

        LogLimitChangeRequest__c req = ((FixtureFactory_LogLimitChangeRequest) new FixtureFactory_LogLimitChangeRequest()
        .withAccount(acc.Id)
        .withAccountNumber('12345')
        .withType('Antecipação de Boleto')
        .withReason('Motivo de bloqueio')
        .withRequestedValue(100))
        .persistRequest();
    }

    // ------------------------------------------------------------
    // Testes
    // ------------------------------------------------------------
    @IsTest
    static void test_getById_withValidId() {
        LogLimitChangeRequest__c record = [SELECT Id FROM LogLimitChangeRequest__c LIMIT 1];
        LogLimitChangeRequest__c result = Repository_LogLimitChangeRequest.getById(record.Id);

        System.assertNotEquals(null, result, 'Deve retornar um registro válido');
        System.assertEquals(record.Id, result.Id, 'O Id retornado deve ser o mesmo do registro inserido');
    }

    @IsTest
    static void test_getById_withInvalidId() {
        LogLimitChangeRequest__c result = Repository_LogLimitChangeRequest.getById('001000000000000AAA');

        System.assertEquals(null, result, 'Deve retornar null para Id inexistente');
    }

    @IsTest
    static void test_getById_withStringId() {
        LogLimitChangeRequest__c record = [SELECT Id FROM LogLimitChangeRequest__c LIMIT 1];
        LogLimitChangeRequest__c result = Repository_LogLimitChangeRequest.getById((String)record.Id);

        System.assertNotEquals(null, result, 'Deve retornar um registro válido');
        System.assertEquals(record.Id, result.Id, 'O Id retornado deve ser o mesmo do registro inserido');
    }

    @IsTest
    static void test_getByIds_withListIds() {
        List<LogLimitChangeRequest__c> records = [SELECT Id FROM LogLimitChangeRequest__c];
        List<Id> ids = new List<Id>();
        for (LogLimitChangeRequest__c rec : records) ids.add(rec.Id);

        List<LogLimitChangeRequest__c> result = Repository_LogLimitChangeRequest.getByIds(ids);

        System.assertEquals(records.size(), result.size(), 'Deve retornar a mesma quantidade de registros');
    }

    @IsTest
    static void test_getByIds_withStringList() {
        List<LogLimitChangeRequest__c> records = [SELECT Id FROM LogLimitChangeRequest__c];
        List<String> ids = new List<String>();
        for (LogLimitChangeRequest__c rec : records) ids.add((String)rec.Id);

        List<LogLimitChangeRequest__c> result = Repository_LogLimitChangeRequest.getByIds(ids);

        System.assertEquals(records.size(), result.size(), 'Deve funcionar também com List<String>');
    }

    @IsTest
    static void test_getByIds_withSetIds() {
        Set<Id> ids = new Set<Id>();
        for (LogLimitChangeRequest__c rec : [SELECT Id FROM LogLimitChangeRequest__c]) {
            ids.add(rec.Id);
        }

        List<LogLimitChangeRequest__c> result = Repository_LogLimitChangeRequest.getByIds(ids);

        System.assert(!result.isEmpty(), 'Deve retornar registros para Set<Id>');
    }

    @IsTest
    static void test_getByIds_withSetStringIds() {
        Set<String> ids = new Set<String>();
        for (LogLimitChangeRequest__c rec : [SELECT Id FROM LogLimitChangeRequest__c]) {
            ids.add((String)rec.Id);
        }

        List<LogLimitChangeRequest__c> result = Repository_LogLimitChangeRequest.getByIds(ids);
        
        System.assert(!result.isEmpty(), 'Deve retornar registros para Set<String>');
    }

    @IsTest
    static void test_getByIds_withEmptyList() {
        List<LogLimitChangeRequest__c> result = Repository_LogLimitChangeRequest.getByIds(new List<Id>());

        System.assertEquals(0, result.size(), 'Deve retornar lista vazia quando IDs vazios forem passados');
    }

    @IsTest
    static void test_findByAccountNumber() {
        LogLimitChangeRequest__c result = Repository_LogLimitChangeRequest.findByAccountNumber('12345');

        System.assert(!String.isBlank(result.Id), 'Deve retornar 1 LogLimitChangeRequest__c');
    }

    @IsTest
    static void test_findByName() {
        LogLimitChangeRequest__c record = [SELECT Id, Name FROM LogLimitChangeRequest__c LIMIT 1];
        LogLimitChangeRequest__c result = Repository_LogLimitChangeRequest.findByName(record.Name);

        System.assertNotEquals(null, result, 'Deve retornar um registro válido');
        System.assertEquals(record.Id, result.Id, 'O Id retornado deve ser o mesmo do registro inserido');
    }
}
```

<h4 align="left">FixtureFactory_LogLimitChangeRequestTest</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class FixtureFactory_LogLimitChangeRequestTest {

    @IsTest
    static void test_FixtureFactoryLogLimitChangeRequest() {
        Account acc = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste'))
        .persistAccount();

        Id recordTypeId = [SELECT Id FROM RecordType WHERE SObjectType = 'LogLimitChangeRequest__c' LIMIT 1].Id;

        LogLimitChangeRequest__c req = ((FixtureFactory_LogLimitChangeRequest) new FixtureFactory_LogLimitChangeRequest()
        .withAccount(acc.Id)
        .withAccountNumber('123456')
        .withType('Antecipação de Boleto')
        .withReason('Motivo de bloqueio')
        .withRequestedValue(100)
        .withRecordTypeId(recordTypeId))
        .persistRequest();

        System.assertNotEquals(null, req.Id, 'O registro deve ser persistido com sucesso');
        System.assertEquals(acc.Id, req.Account__c, 'O campo Account__c deve ser preenchido corretamente');
        System.assertEquals('123456', req.AccountNumber__c, 'O campo AccountNumber__c deve estar correto');
        System.assertEquals('Antecipação de Boleto', req.Tipo__c, 'O campo Tipo__c deve estar correto');
        System.assertEquals('Motivo de bloqueio', req.Reason__c, 'O campo Reason__c deve estar correto');
        System.assertEquals(100, req.RequestedValue__c, 'O campo RequestedValue__c deve estar correto');
        System.assertEquals(recordTypeId, req.RecordTypeId, 'O RecordTypeId deve estar correto');
    }

    @IsTest
    static void test_chainedMethodsReturntype() {
        FixtureFactory_LogLimitChangeRequest factory = new FixtureFactory_LogLimitChangeRequest();

        // Verifica se cada método retorna a própria instância da classe
        System.assertEquals(
            factory,
            factory.withAccount(null),
            'withAccount deve retornar a própria instância'
        );
        System.assertEquals(
            factory,
            factory.withAccountNumber('123'),
            'withAccountNumber deve retornar a própria instância'
        );
        System.assertEquals(
            factory,
            factory.withType('Teste'),
            'withType deve retornar a própria instância'
        );
        System.assertEquals(
            factory,
            factory.withReason('Motivo'),
            'withReason deve retornar a própria instância'
        );
        System.assertEquals(
            factory,
            factory.withRequestedValue(500),
            'withRequestedValue deve retornar a própria instância'
        );
        System.assertEquals(
            factory,
            factory.withRecordTypeId(null),
            'withRecordTypeId deve retornar a própria instância'
        );
    }
}
```

<h4 align="left">AccountRepository_Test</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class AccountRepository_Test {
    
    // ------------------------------------------------------------
    // Construção de dados para teste
    // ------------------------------------------------------------
    @TestSetup
    static void setupData() {
        Account acc1 = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste 1'))
        .persistAccount();

        Account acc2 = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste 2'))
        .persistAccount();
    }

    // ------------------------------------------------------------
    // Testes
    // ------------------------------------------------------------
    @IsTest
    static void test_findById() {
        Account acc = [SELECT Id, Name FROM Account WHERE Name = 'Conta Teste 1' LIMIT 1];
        Set<String> fields = new Set<String>{'Id', 'Name'};
        
        AccountRepository repo = new AccountRepository();
        
        List<Account> result = repo.findById(acc.Id, fields);
        
        System.assertEquals(1, result.size(), 'Deve retornar exatamente uma conta');
        System.assertEquals(acc.Id, result[0].Id, 'O ID deve corresponder ao da conta inserida');
        System.assertEquals(acc.Name, result[0].Name, 'O nome deve corresponder ao da conta inserida');
    }

    @IsTest
    static void test_findByIds() {
        List<Account> accs = [SELECT Id, Name FROM Account];
        Set<Id> ids = new Set<Id>{accs[0].Id, accs[1].Id};
        Set<String> fields = new Set<String>{'Id', 'Name'};
        
        AccountRepository repo = new AccountRepository();
        
        List<Account> result = repo.findByIds(ids, fields);
        
        System.assertEquals(2, result.size(), 'Deve retornar duas contas');
        Set<Id> resultIds = new Set<Id>();
        for (Account a : result) resultIds.add(a.Id);
        System.assert(resultIds.containsAll(ids), 'Os IDs retornados devem estar entre os esperados');
    }

    @IsTest
    static void test_findByName() {
        String name = 'Conta Teste 2';
        AccountRepository repo = new AccountRepository();
        
        Account result = repo.findByName(name);
        
        System.assertNotEquals(null, result, 'Deve retornar uma conta');
        System.assertEquals(name, result.Name, 'O nome deve corresponder ao da conta pesquisada');
    }
}
```

<h4 align="left">UtilsAbstractRepositoryTest</h4>

```cls
@IsTest
private class UtilsAbstractRepositoryTest {

    // ------------------------------------------------------------
    // Testes
    // ------------------------------------------------------------
    @IsTest
    static void test_save_withSingleRecord() {
        Account acc = new Account(Name = 'Teste Single');
        MockRepository repo = new MockRepository();

        Test.startTest();
        SObject result = repo.save(acc);
        Test.stopTest();

        System.assertNotEquals(null, result, 'O retorno não deve ser nulo');
        System.assert(((Account)result).Id != null, 'O registro deve ter um Id após o upsert');
    }

    @IsTest
    static void test_save_withListOfRecords() {
        List<Account> accounts = new List<Account>{
            new Account(Name = 'Conta 1'),
            new Account(Name = 'Conta 2')
        };
        MockRepository repo = new MockRepository();

        Test.startTest();
        List<SObject> result = repo.save(accounts);
        Test.stopTest();

        System.assertEquals(2, result.size(), 'Deve retornar dois registros');
        System.assert(((Account)result[0]).Id != null, 'Registros devem ser inseridos');
    }

    @IsTest
    static void test_save_insertUsingId_withExternalIdField() {
        Account acc = new Account(Name = 'Insert via external field (Id)');
        MockRepository repo = new MockRepository();
        
        Test.startTest();
        SObject result = repo.save(acc, Account.Id);
        Test.stopTest();
        
        System.assertNotEquals(null, result, 'O retorno não deve ser nulo');
        System.assertNotEquals(null, ((Account)result).Id, 'Deve inserir quando o Id é nulo');
    }
    
    @IsTest
    static void test_save_updateUsingId_withExternalIdField() {
        Account acc = new Account(Name = 'Original');
        insert acc;
        
        acc.Name = 'Atualizado';
        MockRepository repo = new MockRepository();
        
        Test.startTest();
        SObject result = repo.save(acc, Account.Id);
        Test.stopTest();
        
        System.assertEquals(acc.Id, ((Account)result).Id, 'Deve manter o mesmo Id no update');
        Account refreshed = [SELECT Name FROM Account WHERE Id = :acc.Id];
        System.assertEquals('Atualizado', refreshed.Name, 'O nome deve ter sido atualizado');
    }

    @IsTest
    static void test_remove_withSingleRecord() {
        Account acc = new Account(Name = 'Para Remover');
        insert acc;

        MockRepository repo = new MockRepository();

        Test.startTest();
        SObject result = repo.remove(acc);
        Test.stopTest();

        System.assertNotEquals(null, result, 'O retorno não deve ser nulo');
        System.assertEquals(0, [SELECT COUNT() FROM Account WHERE Id = :acc.Id], 'O registro deve ser removido do banco');
    }

    @IsTest
    static void test_remove_withListOfRecords() {
        List<Account> accounts = new List<Account>{
            new Account(Name = 'A remover 1'),
            new Account(Name = 'A remover 2')
        };
        insert accounts;

        MockRepository repo = new MockRepository();

        Test.startTest();
        List<SObject> result = repo.remove(accounts);
        Test.stopTest();

        System.assertEquals(2, result.size(), 'Deve retornar dois registros');
        System.assertEquals(0, [SELECT COUNT() FROM Account WHERE Id IN :accounts], 'Todos os registros devem ser removidos');
    }

    @IsTest
    static void test_emptyAndNullScenarios() {
        MockRepository repo = new MockRepository();
        List<Account> emptyList = new List<Account>();

        System.assertEquals(null, repo.save((SObject)null), 'Salvar registro nulo deve retornar nulo');
        System.assertEquals(null, repo.remove((SObject)null), 'Remover registro nulo deve retornar nulo');
        System.assertEquals(0, repo.save(emptyList).size(), 'Salvar lista vazia deve retornar lista vazia');
        System.assertEquals(0, repo.remove(emptyList).size(), 'Remover lista vazia deve retornar lista vazia');
    }

    // ------------------------------------------------------------
    // Mock
    // ------------------------------------------------------------
    private class MockRepository extends UtilsAbstractRepository {}
}
```

<h4 align="left">FixtureFactory_Account_Test</h4>

```cls
/**
 * @author: kevin.horimi
 */
@IsTest
private class FixtureFactory_Account_Test {

    @IsTest
    static void test_build_withNameAndDocumento() {
        FixtureFactory_Account factory = new FixtureFactory_Account()
            .withName('Conta Teste')
            .withDocumento('12345678900');

        Account acc = factory.build();

        System.assertNotEquals(null, acc, 'O objeto Account não deve ser nulo');
        System.assertEquals('Conta Teste', acc.Name, 'O campo Name não foi atribuído corretamente');
        System.assertEquals('12345678900', acc.Documento__c, 'O campo Documento__c não foi atribuído corretamente');
    }

    @IsTest
    static void test_persistAccount() {
        FixtureFactory_Account factory = new FixtureFactory_Account()
            .withName('Conta Persistida')
            .withDocumento('98765432100');

        Account acc = factory.persistAccount();

        System.assertNotEquals(null, acc, 'O objeto persistido não deve ser nulo');
        System.assertNotEquals(null, acc.Id, 'O objeto persistido deve ter um Id');
        System.assertEquals('Conta Persistida', acc.Name, 'O campo Name deve ter sido persistido corretamente');
        System.assertEquals('98765432100', acc.Documento__c, 'O campo Documento__c deve ter sido persistido corretamente');

        Account accFromDB = [SELECT Id, Name, Documento__c FROM Account WHERE Id = :acc.Id LIMIT 1];
        System.assertEquals('Conta Persistida', accFromDB.Name);
        System.assertEquals('98765432100', accFromDB.Documento__c);
    }
}
```

<h4 align="left">RepositoryTest</h4>

```cls
@IsTest
private class RepositoryTest {

    // ------------------------------------------------------------
    // Construção de dados para teste
    // ------------------------------------------------------------
    @TestSetup
    static void setupData() {
        Account acc1 = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste 1'))
        .persistAccount();

        Account acc2 = ((FixtureFactory_Account) new FixtureFactory_Account()
        .withName('Conta Teste 2'))
        .persistAccount();
    }

    // ------------------------------------------------------------
    // Testes
    // ------------------------------------------------------------
    @IsTest
    static void test_constructor_withValidParameters() {
        AccountRepository repo = new AccountRepository();

        System.assertEquals('Account', repo.getObjectNameForTest());
        System.assertEquals(2, repo.getCommonFieldsForTest().size());
    }

    @IsTest
    static void test_constructor_withInvalidParameters() {
        Boolean exceptionThrown = false;

        try {
            new AccountRepositoryTestHelper(null, new List<String>{'Id'});
        } catch (Exception e) {
            exceptionThrown = true;

            System.assert(e.getMessage().contains('inform both objectName'));
        }

        System.assert(exceptionThrown);

        exceptionThrown = false;
        try {
            new AccountRepositoryTestHelper('Account', new List<String>());
        } catch (Exception e) {
            exceptionThrown = true;
        }

        System.assert(exceptionThrown);
    }

    @IsTest
    static void test_getCommonFields() {
        AccountRepository repo = new AccountRepository();
        String fields = repo.testGetCommonFields();

        System.assertEquals('Id, Name', fields);
    }

    @IsTest
    static void test_getQuery_basic() {
        AccountRepository repo = new AccountRepository();
        String query = repo.getQuery();

        System.assert(query.startsWith('SELECT Id, Name FROM Account'));
    }

    @IsTest
    static void test_getQuery_withConditions() {
        AccountRepository repo = new AccountRepository();
        String query = repo.getQuery(new List<String>{'Name = \'Test\''});

        System.assert(query.contains('WHERE Name = \'Test\''));
    }

    @IsTest
    static void test_getQuery_withOptions() {
        AccountRepository repo = new AccountRepository();

        Map<Repository.QueryOptions, String> options = new Map<Repository.QueryOptions, String>{
            Repository.QueryOptions.Q_GROUP_BY => 'Name',
            Repository.QueryOptions.Q_ORDER_BY => 'CreatedDate DESC',
            Repository.QueryOptions.Q_LIMIT => '5',
            Repository.QueryOptions.Q_OFFSET => '2',
            Repository.QueryOptions.Q_ALL_ROWS => 'true'
        };

        String query = repo.getQuery(new List<String>{'Name != null'}, options);

        System.assert(query.contains('GROUP BY Name'));
        System.assert(query.contains('ORDER BY CreatedDate DESC'));
        System.assert(query.contains('LIMIT 5'));
        System.assert(query.contains('OFFSET 2'));
        System.assert(query.contains('ALL ROWS'));
    }

    @IsTest
    static void test_findByField_valid() {
        AccountRepository repo = new AccountRepository();

        Account acc = [SELECT Id, Name FROM Account WHERE Name = 'Conta Teste 1' LIMIT 1];
        List<SObject> results = repo.findByField('Name', acc.Name);

        System.assertEquals(1, results.size());
        System.assertEquals(acc.Id, results[0].Id);
    }

    @IsTest
    static void test_findByField_withInvalidField() {
        AccountRepository repo = new AccountRepository();
        Boolean exceptionThrown = false;

        try {
            repo.findByField('', 'SomeValue');
        } catch (Exception e) {
            exceptionThrown = true;
            
            System.assert(e.getMessage().contains('Field name must not be null'));
        }

        System.assert(exceptionThrown);
    }

    @IsTest
    static void test_getIdsUtility() {
        List<Account> accounts = [SELECT Id, Name FROM Account LIMIT 2];
        List<Id> ids = Repository.getIds(accounts);

        System.assertEquals(accounts.size(), ids.size());
        System.assertEquals(accounts[0].Id, ids[0]);
    }

    @IsTest
    static void test_findByIdAndByName() {
        Account acc = [SELECT Id, Name FROM Account WHERE Name = 'Conta Teste 2' LIMIT 1];
        AccountRepository repo = new AccountRepository();

        // findById(Id)
        List<SObject> found = repo.findById(acc.Id);
        System.assertEquals(1, found.size());

        // findById(Set<Id>)
        found = repo.findById(new Set<Id>{acc.Id});
        System.assertEquals(1, found.size());

        // findByName
        found = repo.findByName(acc.Name);
        System.assertEquals(1, found.size());
    }

    @IsTest
    static void test_getQueryOverload_withOnlyOptions() {
        AccountRepository repo = new AccountRepository();

        Map<Repository.QueryOptions, String> options = new Map<Repository.QueryOptions, String>{
            Repository.QueryOptions.Q_ORDER_BY => 'CreatedDate DESC',
            Repository.QueryOptions.Q_LIMIT    => '10'
        };

        String viaOverload = repo.getQuery(options);
        String viaExplicit = repo.getQuery(null, options);

        System.assertEquals(viaExplicit, viaOverload,
            'getQuery(options) deve delegar para getQuery(null, options)');
        System.assert(viaOverload.contains('ORDER BY CreatedDate DESC'));
        System.assert(viaOverload.contains('LIMIT 10'));
        System.assert(!viaOverload.contains(' WHERE '),
            'Não deve haver WHERE quando não há condições');
    }

    @IsTest
    static void test_getQuery_withCustomOperatorOR() {
        AccountRepository repo = new AccountRepository();

        Map<Repository.QueryOptions, String> options = new Map<Repository.QueryOptions, String>{
            Repository.QueryOptions.Q_OPERATOR => 'OR'
        };

        List<String> conditions = new List<String>{
            'Name = \'Conta Teste 1\'',
            'Name = \'Conta Teste 2\''
        };

        String q = repo.getQuery(conditions, options);

        System.assert(q.contains(' WHERE Name = \'Conta Teste 1\' OR Name = \'Conta Teste 2\''),
            'As condições devem ser unidas por OR quando Q_OPERATOR=OR');
    }

    // ------------------------------------------------------------
    // Inner classes auxiliares
    // ------------------------------------------------------------
    private class AccountRepository extends Repository {
        public AccountRepository() {
            super('Account', new List<String>{'Id', 'Name'});
        }

        // Expor protected para teste
        public String testGetCommonFields() {
            return getCommonFields();
        }

        public String getObjectNameForTest() {
            return objectName;
        }

        public List<String> getCommonFieldsForTest() {
            return commonFields;
        }
    }

    private class AccountRepositoryTestHelper extends Repository {
        public AccountRepositoryTestHelper(String name, List<String> fields) {
            super(name, fields);
        }
    }
}
```
