# Relay client deployment via Terraform 

## GCP

### Prerequisites

Before we begin, ensure that you have these tools ready:

* Terraform - [download from here](https://www.terraform.io/downloads.html)
* gcloud - [download from here](https://cloud.google.com/sdk/gcloud/)
* git - [download from here](https://git-scm.com/downloads)
* Webhook Relay account - [register here](https://my.webhookrelay.com/)

### Clone repository

Clone this repository:

```
git clone https://github.com/webhookrelay/relay-tf
cd relay-tf
```

### Initialize Terraform modules & authentication

```
cd gcp
```

Initialize terraform modules:

```
terraform init
```

Authenticate to your GCP account:

```
gcloud auth application-default login
```

> Instructions how to use service account can be found here: https://www.terraform.io/docs/providers/google/guides/getting_started.html#adding-credentials.

### Set variables

Let's first create a bucket called 'terraform-test' here https://my.webhookrelay.com/buckets. You can also create an output, just make sure 'internal' is chosen to route webhooks through our GCP agent:

![Bucket config](https://raw.githubusercontent.com/webhookrelay/relay-tf/master/static/terraform-bucket.png)

Then, go to the access token page here https://my.webhookrelay.com/tokens and click "Create Token". You will need these details for the terraform inputs file.

Create a new `inputs.tfvars` using your favorite text editor, and put the following in it:

```
project                = "<your google project id>"
relay_key              = "<key from https://my.webhookrelay.com/tokens>"
relay_secret           = "<secret from https://my.webhookrelay.com/tokens>"
relay_buckets          = "<comma separated bucket names>
```

### Terraform it

Now, to deploy relay agent:

```
terraform apply -var-file inputs.tfvars
```

It will print out ssh command (that you can use to get into the instance) and external IP:

```
Outputs:

external_ip_address = 34.74.183.24
gcloud_ssh = gcloud beta compute ssh --zone us-east1-b relay-agent-vm-84b8179561401df3 --project webhookrelay
```

### Using it

Now, if you send webhooks to your bucket's input (https://my.webhookrelay.com/v1/webhooks/...), they will be dispatched to the destination through the GCP server and therefore have "34.74.183.24" IP. This way you can whitelist webhook source or just ensure that you deploy Webhook Relay agent with terraform into an existing private network.