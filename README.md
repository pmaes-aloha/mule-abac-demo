# Mule ABAC Demo
This repository contains an example of how MuleSoft Anypoint can be used to implement Attribute Based Access Control (ABAC).

## Introduction


The overall logical components involved in ABAC control are defined as follows:

![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/4f1d864a-4f61-43f8-a920-fac80c9c21c0)



A principal (person, application) accesses a resource through a policy enforcement point (PEP). In order to do so, the PEP passes the request a Policy Decision Point (PDP).
The PDP allows or denies the request based upon:
* the policies it finds in the PAP (Policy Administration Point) in order to fetch the relevant policies for this request. 
* potentially additional information not found in the request but required to make an access decision for the required policy. For instance geolocation details. This information is retreived from the Policy Information Point (PIP)

## Solution Design

At this point, a rudimentary version of the PEP and PDP have been implemented using MuleSoft custom policy and a Mule app.
Also an example of a protected resource is available (Mule app).

![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/53fd4842-dd08-4c41-aea0-c65f001df914)


## Request Flow
The PEP custom policy will POST the entire incoming request (JSON serialized) to the PDP. The PDP will evaluate the payload for a header named 'pdp-statuscode'.
If the statuscode = 200 , the PDP will approve the request.  Any other status code will get the request rejected (default).

The PDP response to the PEP will be either 
*  `{"continue": "true"}`
*  `{"continue": "false"}`

In the former case, the PEP custom policy will allow the request to pass to the backend setting the payload as the original full request message.
For the latter, the PEP custom policy will not allow the request to reach the backend service and replies with 
`{
"code" : 403,
"description" : "Not allowed"
}` 
to the requester.

## Assets
This repository contains the following assets:
* MS_CP_ABAC_PEP : the custom policy (Mule 4)
* ms_app_backend: Anypoint Studio project with a sample backend application
* ms_app_pdp: Anypoint Studio project for the PDP application
* ms_cp_abac_pep_local: the mule app version of the policy

## Setup
* MS_CP_ABAC_PEP : download into Anypoint Studio and deploy to your exchange (change groupid in the pom.xml and configure maven according to Mule 4 documentation )
* mss_app_abac_backend : if you would like to use the sample backend, download into Anypoint Studio and then deploy into to Cloudhub or Cloudhub 2.0
* ms_app_abac_pdp : download into Anypoint Studion and then deploy into Cloudhub or Cloudhub 2.O
* ms_cp_abac_pep_local: do not deploy, is there as a visual aid to inspect to custom policy logic

## Configuration

## Prerequisites
* PDP and Backend application are in deployed  
* Custom Policy is available in Exchange
   ![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/e84c1b83-85a0-4b0d-aa23-ac22653cbc57)

## Configure the Mule Proxy
* Add a new Mule Gateway API via API Manager
  * Proxy Type can be either using Autodiscovery or Deploy a proxy application
  * Mule version must be Mule4
  ![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/9da7f385-c268-49f5-bc11-8c60d13bacf6)
* complete the configuration of the API
* once the API is active, add the Policy through the API Administration
  ![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/8ce89fbf-733e-40a3-9959-a3c4cdbb99e9)
* configure the policy (attention protocol must be 'HTTP' in capitals)
  ![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/d5d0ab83-b839-411c-8986-9d97be8ceac5)


## Testing
The setup can be tested be invoking the proxy endpoint, for instance with Postman
* ABAC success, add the header pdp-statuscode and set it to 200
  ![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/a81cbf8d-b5f6-4a77-931a-b6ac112480c6)

* ABAC, failure set the pdp-statuscode to anything else
  ![image](https://github.com/pmaes-aloha/mule-abac-demo/assets/98394409/079e5965-57c7-409a-99c1-565ca092d2da)

  

  



