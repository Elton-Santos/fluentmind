---
layout: default
title: "Padrões de Desenvolvimento"
---

# Padrões de Desenvolvimento - FluentMind

## 1. Padrões de Código Apex

### Nomenclatura
```apex
// Classes: PascalCase
public class ChannelManager {
    
    // Constantes: UPPER_SNAKE_CASE
    public static final String DEFAULT_TIMEOUT = 'DEFAULT_TIMEOUT';
    
    // Variáveis: camelCase
    private String channelType;
    private Map<String, Object> apiConfiguration;
    
    // Métodos: camelCase
    public void processIncomingMessage(String message) {
        // Implementation
    }
}
```

### Estrutura de Classes
```apex
/**
 * @description Manages communication channels for FluentMind
 * @author FluentMind Team
 * @since 1.0
 */
public with sharing class ChannelManager {
    
    // Constants first
    private static final String LOG_PREFIX = '[ChannelManager]';
    
    // Instance variables
    private List<Channel__c> activeChannels;
    
    // Constructors
    public ChannelManager() {
        this.activeChannels = new List<Channel__c>();
    }
    
    // Public methods
    public void processMessage(String message) {
        // Implementation
    }
    
    // Private methods
    private void validateMessage(String message) {
        // Implementation
    }
}
```

### Error Handling
```apex
public class AIService {
    
    public AIResponse processMessage(String message) {
        try {
            return callGeminiAPI(message);
        } catch (CalloutException e) {
            Logger.error(LOG_PREFIX + 'API callout failed', e);
            throw new AIServiceException('Failed to process message', e);
        } catch (Exception e) {
            Logger.error(LOG_PREFIX + 'Unexpected error', e);
            throw new AIServiceException('Unexpected error occurred', e);
        }
    }
}
```

## 2. Padrões Lightning Web Components

### Estrutura de Arquivos
```
fluentMindChat/
├── fluentMindChat.html
├── fluentMindChat.js
├── fluentMindChat.css
└── fluentMindChat.js-meta.xml
```

### JavaScript Patterns
```javascript
// fluentMindChat.js
import { LightningElement, api, track } from 'lwc';
import { ShowToastEvent } from 'lightning/platformShowToastEvent';

export default class FluentMindChat extends LightningElement {
    
    // Public properties
    @api channelId;
    
    // Private reactive properties  
    @track messages = [];
    @track isLoading = false;
    
    // Lifecycle hooks
    connectedCallback() {
        this.initializeChat();
    }
    
    // Event handlers
    handleSendMessage(event) {
        const message = event.target.value;
        this.sendMessage(message);
    }
    
    // Private methods
    async sendMessage(message) {
        this.isLoading = true;
        try {
            const response = await this.callApexMethod({ message });
            this.processResponse(response);
        } catch (error) {
            this.handleError(error);
        } finally {
            this.isLoading = false;
        }
    }
}
```

### CSS Standards
```css
/* fluentMindChat.css */
:host {
    display: block;
}

.chat-container {
    max-width: 600px;
    margin: 0 auto;
    border: 1px solid var(--lwc-colorBorder);
    border-radius: var(--lwc-borderRadiusMedium);
}

.message-item {
    padding: var(--lwc-spacingSmall);
    border-bottom: 1px solid var(--lwc-colorBorderLight);
}

.message-item:last-child {
    border-bottom: none;
}
```

## 3. Testing Standards

### Apex Unit Tests
```apex
@IsTest
private class ChannelManagerTest {
    
    @TestSetup
    static void setupTestData() {
        Channel__c testChannel = new Channel__c(
            Name = 'Test Channel',
            Channel_Type__c = 'WhatsApp',
            Status__c = 'Active'
        );
        insert testChannel;
    }
    
    @IsTest
    static void testProcessMessage_Success() {
        // Given
        ChannelManager manager = new ChannelManager();
        String testMessage = 'Hello, World!';
        
        // When
        Test.startTest();
        AIResponse response = manager.processMessage(testMessage);
        Test.stopTest();
        
        // Then
        Assert.isNotNull(response);
        Assert.areEqual('processed', response.status);
    }
    
    @IsTest
    static void testProcessMessage_InvalidInput() {
        // Given
        ChannelManager manager = new ChannelManager();
        
        // When & Then
        Test.startTest();
        try {
            manager.processMessage(null);
            Assert.fail('Expected exception not thrown');
        } catch (IllegalArgumentException e) {
            Assert.isTrue(e.getMessage().contains('Message cannot be null'));
        }
        Test.stopTest();
    }
}
```

### Jest Tests para LWC
```javascript
// __tests__/fluentMindChat.test.js
import { createElement } from 'lwc';
import FluentMindChat from 'c/fluentMindChat';

describe('c-fluent-mind-chat', () => {
    afterEach(() => {
        while (document.body.firstChild) {
            document.body.removeChild(document.body.firstChild);
        }
    });

    it('renders chat interface correctly', () => {
        // Given
        const element = createElement('c-fluent-mind-chat', {
            is: FluentMindChat
        });

        // When
        document.body.appendChild(element);

        // Then
        const chatContainer = element.shadowRoot.querySelector('.chat-container');
        expect(chatContainer).toBeTruthy();
    });

    it('sends message when button clicked', async () => {
        // Given
        const element = createElement('c-fluent-mind-chat', {
            is: FluentMindChat
        });
        document.body.appendChild(element);

        // When
        const input = element.shadowRoot.querySelector('lightning-input');
        input.value = 'Test message';
        input.dispatchEvent(new CustomEvent('change'));

        const button = element.shadowRoot.querySelector('lightning-button');
        button.click();

        // Then
        await Promise.resolve();
        expect(element.messages.length).toBeGreaterThan(0);
    });
});
```

## 4. Documentation Standards

### Class Documentation
```apex
/**
 * @description Service class responsible for managing AI interactions with Google Gemini
 * @author FluentMind Development Team
 * @since 1.0.0
 * @group AI Services
 * @example
 * AIService service = new AIService();
 * AIResponse response = service.processMessage('Hello, how can you help?');
 */
public with sharing class AIService {
    
    /**
     * @description Processes a customer message using Google Gemini AI
     * @param message The customer message to process
     * @param sessionContext Current session context for maintaining conversation flow
     * @return AIResponse containing the AI-generated response and metadata
     * @throws AIServiceException when API call fails or response is invalid
     * @example
     * String customerMessage = 'I need help with my order';
     * SessionContext context = new SessionContext(sessionId);
     * AIResponse response = processMessage(customerMessage, context);
     */
    public AIResponse processMessage(String message, SessionContext sessionContext) {
        // Implementation
    }
}
```

### Method Documentation
```apex
/**
 * @description Validates channel configuration before activation
 * @param channel The channel record to validate
 * @return ValidationResult containing validation status and error messages
 * @throws ValidationException when required fields are missing
 * @see ChannelConfigValidator for detailed validation rules
 * @since 1.1.0
 */
private ValidationResult validateChannelConfiguration(Channel__c channel) {
    // Implementation
}
```

## 5. Security Standards

### CRUD/FLS Enforcement
```apex
public with sharing class SecureChannelManager {
    
    public List<Channel__c> getChannels() {
        // Check object permission
        if (!Schema.sObjectType.Channel__c.isAccessible()) {
            throw new SecurityException('Insufficient permissions to access channels');
        }
        
        // Use Security.stripInaccessible for field-level security
        List<Channel__c> channels = [
            SELECT Id, Name, Channel_Type__c, Status__c 
            FROM Channel__c 
            WHERE Status__c = 'Active'
        ];
        
        SObjectAccessDecision decision = Security.stripInaccessible(
            AccessType.READABLE, 
            channels
        );
        
        return decision.getRecords();
    }
}
```

### Input Sanitization
```apex
public class MessageProcessor {
    
    public void processMessage(String message) {
        // Validate input
        if (String.isBlank(message)) {
            throw new IllegalArgumentException('Message cannot be null or empty');
        }
        
        // Sanitize HTML
        String sanitizedMessage = message.escapeHtml4();
        
        // Validate length
        if (sanitizedMessage.length() > MAX_MESSAGE_LENGTH) {
            throw new IllegalArgumentException('Message too long');
        }
        
        // Process sanitized message
        processValidatedMessage(sanitizedMessage);
    }
}
```

## 6. Performance Standards

### Bulk Operations
```apex
public class BulkMessageProcessor {
    
    public void processMessages(List<String> messages) {
        List<Interaction__c> interactions = new List<Interaction__c>();
        
        for (String message : messages) {
            interactions.add(new Interaction__c(
                Customer_Message__c = message,
                Timestamp__c = DateTime.now()
            ));
        }
        
        // Bulk insert instead of individual inserts
        if (!interactions.isEmpty()) {
            insert interactions;
        }
    }
}
```

### Query Optimization
```apex
public class OptimizedDataAccess {
    
    public List<Session__c> getActiveSessions(Set<Id> channelIds) {
        // Use selective queries with indexed fields
        return [
            SELECT Id, Channel__c, Customer_Phone__c, Session_Status__c,
                   (SELECT Id, Customer_Message__c, AI_Response__c 
                    FROM Interactions__r 
                    ORDER BY Sequence_Number__c DESC 
                    LIMIT 10)
            FROM Session__c 
            WHERE Channel__c IN :channelIds 
            AND Session_Status__c = 'Active'
            AND Started_At__c >= :Date.today().addDays(-1)
            ORDER BY Started_At__c DESC
        ];
    }
}
```

## 7. Integration Patterns

### HTTP Callout Pattern
```apex
public class GeminiAPIConnector {
    
    private static final String BASE_URL = 'https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent';
    
    public GeminiResponse generateContent(GeminiRequest request) {
        HttpRequest req = new HttpRequest();
        req.setEndpoint(BASE_URL);
        req.setMethod('POST');
        req.setHeader('Content-Type', 'application/json');
        req.setHeader('Authorization', 'Bearer ' + getAccessToken());
        req.setBody(JSON.serialize(request));
        req.setTimeout(30000); // 30 seconds
        
        Http http = new Http();
        HttpResponse res = http.send(req);
        
        if (res.getStatusCode() == 200) {
            return (GeminiResponse) JSON.deserialize(res.getBody(), GeminiResponse.class);
        } else {
            throw new CalloutException('API call failed: ' + res.getStatus());
        }
    }
    
    private String getAccessToken() {
        // Implementation to get access token
        return 'token';
    }
}
```

### Async Processing
```apex
public class AsyncMessageProcessor implements Queueable, Database.AllowsCallouts {
    
    private List<String> messages;
    
    public AsyncMessageProcessor(List<String> messages) {
        this.messages = messages;
    }
    
    public void execute(QueueableContext context) {
        processMessages();
        
        // Chain additional jobs if needed
        if (hasMoreWork()) {
            System.enqueueJob(new AsyncMessageProcessor(getNextBatch()));
        }
    }
    
    private void processMessages() {
        for (String message : messages) {
            // Process each message
            processIndividualMessage(message);
        }
    }
}
```

## 8. Logging and Monitoring

### Structured Logging
```apex
public class Logger {
    
    public static void info(String component, String message, Map<String, Object> context) {
        LogEntry__c entry = new LogEntry__c(
            Level__c = 'INFO',
            Component__c = component,
            Message__c = message,
            Context__c = JSON.serialize(context),
            Timestamp__c = DateTime.now()
        );
        
        Database.insert(entry, false);
    }
    
    public static void error(String component, String message, Exception ex) {
        LogEntry__c entry = new LogEntry__c(
            Level__c = 'ERROR',
            Component__c = component,
            Message__c = message,
            Exception_Type__c = ex.getTypeName(),
            Stack_Trace__c = ex.getStackTraceString(),
            Timestamp__c = DateTime.now()
        );
        
        Database.insert(entry, false);
    }
}
```

### Performance Monitoring
```apex
public class PerformanceMonitor {
    
    private Long startTime;
    private String operationName;
    
    public PerformanceMonitor(String operationName) {
        this.operationName = operationName;
        this.startTime = System.currentTimeMillis();
    }
    
    public void finish() {
        Long duration = System.currentTimeMillis() - startTime;
        
        if (duration > 5000) { // Log if operation takes more than 5 seconds
            Logger.warn('Performance', 
                'Slow operation detected: ' + operationName, 
                new Map<String, Object>{'duration' => duration}
            );
        }
    }
}
```

---

[← Voltar: Git Workflow](./git-workflow.md) | [Voltar ao Índice](./index.md)