# vpc-ha-pa-iks-app-
# Single VPC with HA Palo Alto Firewall with an IKS application container
The purpose of this pattern to deploy an auto-scale simple web application, deploy an HA Palo Alto Firewall supported by NLB/ALB in a single VPC environment.
Solution components are:
1. Single VPC
2. A public facing ALB or NLB
3. A pair of Palo Alto appliance in a HA configuration
4. A private IKS cluster
5. Sample httpbin application
6. Optional: CIS for internet GLB and WAF


## Deployment procedure
1. Download a copy of terraforms:
```
git clone a copy of terraform
```
git clone https://github.com/bkoohi/vpc-ha-pa-iks-app.git
2. Change dir to downloaded directory
```
cd vpc-ha-pa-iks-app
```
4. update variable.tf file with the following variables:
```
vi variable.tf
```
   - vpc_name : VPC name used for deployment 
   - basename : Prefix used for network subnets and VSIs names.
   - region   : Region to use for deployment of the environment
   - resource_group_name : Default is standard default resource group.
   - ssh_keyname : ssh key used for accessing Web and Palo Alto VSIs 
      - Follow IBM Cloud procedure for creating new ssh key, if required: https://cloud.ibm.com/docs/ssh-keys?topic=ssh-keys-adding-an-ssh-key
   - ibmcloud_api_key ued for environment deployment. 
      - Follow IBM Cloud procedure for creating new API key, if required: https://cloud.ibm.com/docs/account?topic=account-userapikey&interface=ui
   


5. Initialize terrform
```
terraform init
```
7. Apply terraform
```
terraform apply -auto-approve

```
9. Review list of VSIs in VPC and identify Palo Alto VSI ( pa-ha-instanca1 & pa-ha-instanca2 ). Record FIPs for two Palo Alto VSIs.
10. Review list Load Balancers in VPC and identify Public Load Balancer ( ie. auto-scale-vpc-vnf-alb ) deployed for Palo Alto VSIs and Private Load Balanncer ( ie. auto-scale-vpc-web-alb ) deployed for auto-scale Web app VSIs. Record hostname of Private Load Balancer ( ie. 3bdeefaa-us-south.lb.appdomain.cloud )
11. Use Palo Alto configuration script provided in scripts directory to configure each Palo Alto instance
```
cd scripts
./remote-vnf-setup.sh 52.116.129.163 admin P@rtal123 3bdeefaa-us-south.lb.appdomain.cloud ( an example )
```
11. Try step 9 for configuring 2nd Palo Alto instance
```
./remote-vnf-setup.sh 150.240.66.11 admin P@rtal123 3bdeefaa-us-south.lb.appdomain.cloud ( an example )
```
13. Apply Palo Alto licenses to both appliances. Login into Devices as admin, Devices --> Licenses --> Active feature using authentication code
14. Test Web application: 
```
curl -v hostname_public_alb 
```
