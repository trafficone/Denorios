# Denorios
ActivityPub but serverless. A pre-requisite for Massless Mastodon.  
Current design goals 
- SAM/Lambda
- Probably Python
- Easy to customize and deploy

I'm not sure if this is a good idea or not, but I figured I'd start a GitHub Repo for this.
## References
[ActivityStreams Documentation](https://www.w3.org/ns/activitystreams)
[ActivityPub Documentation](https://www.w3.org/TR/activitypub/)
[Oauth2 Documentation](https://oauth.net/2/)
[Verifiable Credential Data Integrity](https://w3c.github.io/vc-data-integrity/) for Linked-Data Signatures
[Mastodon Source](https://github.com/mastodon/mastodon/tree/main)

## Serverless Functions
Each action in a serverless environment is handled by a single function. 

### Endpoint Functions
When a Fediverse app interacts with your "server" they need the following:
- User (Accept: application/activity+json)
-- Inbox 
-- Outbox
-- Followers
-- Following
-- Liked
-- Posts
- Explore
- Public
-- Local

### Moderation Functions
- Admin
-- User
--- Silence *
--- Block *
--- Elevate
--- Mark Sensitive *
--- Freeze
--- Limit
--- Suspend
-- Post
--- Flag *
--- Mark Sensitive *
--- Delete
-- Server
--- Reputation
--- Reject Media
--- Silence All Accounts
--- Defederate

* Publicly available moderation actions

### Federating Functions
When interacting with other servers, you will handle the following:
 - Deliver - initiated internally
 - Receive - Verify server + sender on receipt
 -- fetch server publickey 
 -- fetch remote publickey 
 - Forward - forward messages from inbox (deliver triggerd by reply+undeliverable)

## Serverless Infra
Currently designed for AWS. GCP and Azure TBA
- CloudFormation/SAM
-- Serverless application build/deployment
- Route53 
-- DNS Management
- Lambda 
-- Handle functions internal and external
-- Handle Auth and Key Verification
-- Handle Cache Management
- API Gateway
-- Handle HTTP requests
-- Route to Lambda/S3
-- Handle invalid/unauthorized Requests
- DynamoDB
-- Store User Data
-- Store Inbox/Outbox data (with favorites, shares attached)
-- Store Federated Data (cached users, federated public posts)
- S3
-- Store and Share Static Content (SPA, icons)
-- Store and Share media content (images, videos, etc.)
- Cognito
-- Manage User/authentication
- SQS 
-- Manage Inbox/Outbox queue management 
### Possible Additions
- Rekognition for automoderation of media
- Comprehend for flagging potentially sensitive/inflamatory posts

## Security
### Authentication and Validation
Signing objects is required to validate they were sent by the person claiming to send them.
Some federation platforms, like Mastodon, manage the ActivityPub pub/priv key generation. 
This relies on a secondary authentication to guarantee users registered with the server to validate who they are.

- User Registration Keygen
- Signing objects
- Signature Validation
