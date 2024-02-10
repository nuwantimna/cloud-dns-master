# cloud-dns-master
dgtek-cloud-dns-master
# Description

We use terraform to manager reverse DNS zones, that are hosted on [Google Cloud DNS](https://console.cloud.google.com/net-services/dns/zones?authuser=1&project=dns1-287706&dnsManagedZonessize=50)

|Project id | dns1-287706 |
|-----------|---|
| zones     | 134.75.103.in-addr.arpa. |
|           | 135.75.103.in-addr.arpa. |
|           | 94.59.148.in-addr.arpa.  |

# Pre-requisits

1. Install [Terraform 12+](https://www.terraform.io/downloads.html)
2. [gcloud cli](https://cloud.google.com/sdk/gcloud) and configured access

```
gcloud auth application-default login
gcloud config set project 'dns1-287706'
```

# Usage

## Short summary
1. Change directory to the corresponding zone directory
2. run `terraform init` if you never terraform before
3. change json file with the zone
4. run `terraform plan` check the changes
5. run `terraform apply` to apply them

## Detailed example

1. To update the zone you need to change a corresponding file in a corresponding
to the zone folder:

```
❯ ls -1
134.75.103.in-addr.arpa
135.75.103.in-addr.arpa
94.59.148.in-addr.arpa
```

2. Let's say we need to change a reverse DNS record in 103.75.134.0/24 network, we need to edit the following file:
```
134.75.103.in-addr.arpa/dgtek_dns_103.75.134.0.json
```

3. Find the record you'd like to change, e.g. 103.75.134.100:

```
  {
    "name": "100.134.75.103.in-addr.arpa",
    "type": "PTR",
    "ttl": "300",
    "reverse": "100.134.75.103.in-addr.arpa",
    "records": [
      "ip-103.75.134.100.dgtek.net."
    ]
  },
  ...
```

4. Update `records` section with the required domain, e.g. `mail.example.com.`. Do
not forget about the trailing dot.

```
  {
    "name": "100.134.75.103.in-addr.arpa",
    "type": "PTR",
    "ttl": "300",
    "reverse": "100.134.75.103.in-addr.arpa",
    "records": [
      "mail.example.com."
    ]
  },
  ...
```
5. Initialise terraform if you run terraform for the first time type

change directory to `./134.75.103.in-addr.arpa`

```
terraform init
```

5. From the root of the repository run the command:

```
terraform plan
```

make sure that the changes you are making are intended

6. Then apply the changes

```
terraform apply
```

7. Validate that the record updated, e.g.

```
dig -x 103.75.134.100 @ns-cloud-c2.googledomains.com +short
mail.example.com.
```

## Adding new zone

1. Create a new zone file directory with the name (see #1)
```
zone-number.in-addr.arpa
```
2. add `main.tf` file, you can copy it from existing directory
3. change variables to match the zone
4. add json file with the description of the zone in the following format
```
[
  {
    "name": "100.134.75.103.in-addr.arpa",
    "type": "PTR",
    "ttl": "300",
    "reverse": "100.134.75.103.in-addr.arpa",
    "records": [
      "mail.example.com."
    ]
  },
  ...
]
```
5. run `terraform init`
6. `terraform plan`
7. `terraform apply`
8. commit changes and create a pull resquet
