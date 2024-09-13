# API SAM Template
SAM Template for REST API using AWS API Gateway

This guide focuses primarily on the following:
- setting up a template for a REST API hosted on API Gateway
- looking at the different types of authentication policies for the API
- noting the various elements to keep an eye on

This guide is specifically designed for developers who create separate lambdas that they wish to maintain independently and subsequently link them to an API. This documentation is exclusively for API management and definition. No instructions on the development of a CI/CD pipeline are provided. 

## Repo structure

The are are four folders, each of a specific authorization type.

- `api_key`: this is when you define an API with an authorization key. 
- `cognito_auth`: this is when you define a Cognito User Pool for authorization. It essentially handles the token authentication.  
- `lambda_auth`: this is when you define an API with an authorization key. 


The `template.yml` file contains the definition of the API. 
The `src` folder contains the Open API definition of the api. 


## The SAM Template

The same template defines the API resource. It contains a variety of properties, including the name, description, etc. Some properties are optional, including logging. 

One of te most important part to keep an eye on are: 
- `EndpointConfiguration`: setting EDGE tells API Gateway to use Cloudfront distribution to distribute the API across the AWS regions. 
- `Domain`: sets the domain preferences for your API. You will have to create the certificate in ACM separately. **Make sure to create it inside the eu-east-1 region.** Additionally, the HostedZoneId needs to be public for public APIs and is specific to your domain hosted zone. It requires that you've registered your domain inside Route53. 
- `DefinitionBody`: It simply identifies the path to the API definition. 
- `Cors`: You may want to restrict the types of methods used  by your API, the origin and the headers. **Keep an eye on the headers you use in your API. Any non-standard headers will result in errors, you need to add them here. Custom headers also have to be lower case to avoid any issues.**
- `Auth`: this is the authentication definition for your API. There are three types of authentication. Specifying no `Auth` property results in an unsecured API. 

The properties of the template to edit to your needs are marked with comments. 

### Authentication

**API KEYS**

When using the API Key definition, API Gateway will automatically generate an API. It will not be updated unless you update a core property of the template. 

**COGNITO AUTHENTICATION**

When using Cognito authentication, you will need to create a user pool first. The integration happens natively between API Gateway and Cognito IDP, insuring that all the users inside your user pool have access to the API. 

**LAMBDA AUTHENTICATORS**

When using lambda authenticators, you will create a separate lambda and role (you can even define it inside the template) that will act as the authorizer. In the example provided, it uses JWT tokens that are sent directly to the lambda via API Gateway for authentication. 


## The API structure 

The structure of the `api.yml` file follows the standard swagger definition format. I've included an example for reference. 

The important part is the `x-amazon-apigateway-integration` property which links the specific method and path to an api. It is essential to follow that format for it to work. 

It requires that you create a separate lambda and separate role **with access from API Gateway** (you can use the same role with access to multiple lambdas to save time). 