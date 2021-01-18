### Cognito

- authentication, authorization and user management for web and mobile apps
- also third party authentication (facebook, google)

1. user pools
- user directories for sign up and sign in for app users
2. identity pools
- aws credentials to grant access to users

Steps
1. authenticate and get token from user pool
2. exchange token for aws credentials from identity pool
3. use aws credentials for resource access

authentication providers
- facebook
- google
- amazon
- SAML
- openId

### KMS

CMKs
- aws-managed
- aws-owned
- customer-managed

Customer managed
- key sources
  - KMS
  - external - upload
  - cloudHSM (custom key store)
  
  ### Cloud HSM
  
  - for contractual, regulatory requirements
  - HSM - stores cryptographic keys
  - initialize HSM
  - upload signed certificates
  
 x | classic | current
  --|--|--
  device | third-party safeNET LUNA| AWS proprietary
  pricing | $5k | pay per hour
  HA | add 2nd device | clustered
  FIPS 140-2 | level 2|level 3
  
  ### WAF and Shield
  
  **Shield**
  - DDOS protection
  - standard, advanced
  - enabled by default
  
  General Best Practices
  1. scale
  2. minimize attack surface area/exposed resources
  3. alert what is not normal
  4. resilience
  
  **WAF** 
  - web application firewall
  - rule
    - condition
      - acl (allow/deny)
  - rules
  - web acls
  - conditions
    - geofiltering
    - cross-site scripting
    - size constraints
    - sql injection
    - regex matching
    
 apply web acl to cloudfront, etc
 
 ### Organizations

orgs
- org units
  - org units
  - accounts
 
 SCPs and Tag Policies
 - attach to organizational units
 
 SCP
 - service control policy
 - services available for use
 
 Tag policies
 - rules on tagging
 
 *most restrictive policy applies
 
 ### Identity Federation (IAM)
 - alternative to cognito
 - IAM to Active Directory / LDAP with SAML 2.0
 - google,facebook, amazon (OpenID Connect - OIDC)
 
 - create identity provider
 - openid connect
   - provider url
   - audience
 - SAML
   - provider name
   - metadata document
      
SSO
- SSO identity store by default or integrate with Active Directory (AD) and Azure AD
- built-in SSO integrations, salesforce, box, etc
- also access to multiple AWS accounts

### AWS Directory Service

- manAGED MICROSOFT AD
- simple ad - based on samba
  - no mfa
  - kerberos sso
  - small - <= 500 users/2000 objects
  - large - <= 5000 users/20000 objects
  - does not support trust relationships with other ads
- ad connector
  - self managed ad to aws services
  - mfa
  - ec2 join ad domain


### Resource Access Manager

- share resources accross accounts (external accounts or within org)
