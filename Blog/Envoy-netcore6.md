## Building an API Gateway using Evvoy and .Net Core 6.0 ##

### Introduction - What is API gateway ? ###

An API Gateway is basically a service that acts as the single entry point into an application from the outside world. It's resposible for routing requests, API composition, authentication and some other functions as well.
It encapsulates the internal application architecture and services, and exposes and API to its clients.

[draw.io chart goes here]

Main features and benifits of using an API gateway:  

* Less coupling - When using an API gateway, the clients (mobile applications, web clients, etc..) does not need to know anything about the internal architecture of the backend application, not any of its internal routes or services. All the clients requests are recieved and routed internall by the API gateway, hence, the ciient only needs to know the API gateway adddress.
* Less requests and roundtrips - For example, nd API gateway can retrieve data from multiple services based on a single http request.
* Better security - Having the application internal architecture and services hidden from the outside world, reduces the potentional "attack" targets of an application.
* Simplifies communication for the clients - as it can translate from a standard public API protocol to any other protocol used internally by the application services.
* And much more !

Draw backs of using API gateway:

* Increases application complexity - As it is another service that needs to built, maintained,properly setup and configured.
* May incease increase response time, as it also introduces another network hop.
* May become a bottle neck, if not scaled properly.
* Creates an additional potential point of failure for the application.

### Implementation - Setting up the API gatewau using Envoy ###

We will create two simple web API projects that we will expose via the API gateway, and use Envoy as the API gateway do all the routing work for us.  

We'll start by creating the two web API's, each in a seperate folder:
First API - 'Users' service  

1. We'll start by creating a new solution:
   `dotnet new sln --name apigateway`  
2. `dotnet new webapi --name users -o ./users-service`
3. `dotnet new webapi --name orders -o ./orders-service`
4. Next, we need to add the new projects to the solution:  
   `dotnet sln add .\users-service`  
   `dotnet sln add .\orders-service`
5. Create a new controller in `users-service` as follows:

   ```csharp
    [ApiController]
    [Route("[controller]")]
    public class UsersController : ControllerBase {

        [HttpGet]
        public ActionResult<string> Get()
        {
            return "Hello from users service";
        }
    }  
    ```

6. Create a new controller in `orders-service` as follows:  

    ```csharp
    [ApiController]
    [Route("[controller]")]
    public class InventoryController : ControllerBase {

        [HttpGet]
        public IActionResult Get()
        {
            return Ok("Hello from Orders service");
        }
    }
    ```

Next, we will create and assign certificates for our Web API projects.

1. Creating a certificate for `users-service`

    1. Use `dotnet` CLI tools to create a new certificate:  
       `dotnet dev-certs https -ep $env:USERPROFILE\.aspnet\https\Clients.API.pfx -p pa55w0rd!`
    2. Next we need to trust the newly created certificate:  
       `dotnet dev-certs https --trust`
    3. Verify a certificate is created under `USERPROFILE`
    4. Open relevant `.csproj` file
    5. Generate a GUID
    6. Add the GUID to the csproj as follows:  
    `<UserSecretsId>clientsapi-6FBC2E53-9DE4-4E1D-B05E-32CB0BF4BC40</UserSecretsId>`
    7. We need to create user secret. Run the following command from the web api project folder:  
   `dotnet user-secrets set "Kestrel:Certificates:Development:Password" "pa55w0rd!"`  

5. Create a docker file for each of the web api's:  
  5.1 users-service  
    [DOCKER-FILE]
  5.2 orders-service  
    [DOCKER-FILE]

Once that is done, we need to create a `docker-compose` file, to spin up both services and the `Envoy` proxy as well.

[DOCKER-COMPOSE goes here]

Next, we need to configure Envoy, and tell it how to route the incoming http requests, to the matching services.

5. create `envoy.yaml` file for the envoy  
   5.1 Configure listeners and routes per listener
   5.1 Configure clusters and redirection address per listener

### Enabling HTTPS support in Envoy   
8. To enable HTTPS with Envoy, we need to create certificates for Envoy to use
9. Create certificates using WSL or GitBash using OpenSSL tool
10. Open WSL
11. Open `explorer.exe .`
12. Create a new text file called https.config and open it in VSCode
13. Use from web template to write the contents of the https.config file
14. Open WSL command line again and use the following command to generate the key.pem file (csr.pem)  
    `openssl req -config https.config -new -out csr.pem`
15. Now run the following command to generate the certificate based on the key we just created :  
   `openssl x509 -req -days 365 -extfile https.config -extensions v3_req -in csr.pem -signkey key.pem -out https.crt`
16. copy the `https.crt` and `key.pem` files to a direct location (i.e to the Envoy location of the our solution)
17. update the `docker-compose.yaml` file Envoy section, to inlude mounted volumns than contains the certifacates:  

     ```yaml
     - ./Envoy/Certs/https.crt:/etc/ssl/certs/https.crt
     - ./Envoy/Certs/key.pem:/etc/ssl/certs/key.pem
     ```

18. In addition, in the `docker-compose.yaml`, add a new port to be used for secured traffice, i.e https:  
    `- "10001:10001"`
19. Next, we need to injest/inject the newly created certificates to be used by Envoy, in the `envoy.yaml` file.
    1. Add a `tls_context` section to the `envoy.yaml` file.  
    Under the `name` sub-section of `filters` section of the listener

    ```yaml
    transport_socket:
        name: envoy.transport_sockets.tls
        typed_config:
          "@type": type.googleapis.com/envoy.extensions.transport_sockets.tls.v3.DownstreamTlsContext
          common_tls_context:
            tls_certificates:
              - certificate_chain: 
                  filename: "/etc/ssl/certs/https.crt"
                private_key:
                  filename: "/etc/ssl/certs/key.pem"
    ```

20. Install the certificate into out machine into the trusted root certification authorities

### Testing our setu[]