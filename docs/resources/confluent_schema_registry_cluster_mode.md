---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "confluent_schema_registry_cluster_mode Resource - terraform-provider-confluent"
subcategory: ""
description: |-
  
---

# confluent_schema_registry_cluster_mode Resource

[![General Availability](https://img.shields.io/badge/Lifecycle%20Stage-General%20Availability-%2345c6e8)](https://docs.confluent.io/cloud/current/api.html#section/Versioning/API-Lifecycle-Policy)

`confluent_schema_registry_cluster_mode` provides a Mode resource that enables updating Mode on a Schema Registry cluster on Confluent Cloud.

-> **Note:** It is recommended to set `lifecycle { prevent_destroy = true }` on production instances to prevent accidental mode deletion. This setting rejects plans that would destroy or recreate the mode, such as attempting to change uneditable attributes. Read more about it in the [Terraform docs](https://www.terraform.io/language/meta-arguments/lifecycle#prevent_destroy).

## Example Usage

### Option #1: Manage multiple Schema Registry clusters in the same Terraform workspace

```terraform
provider "confluent" {
  cloud_api_key    = var.confluent_cloud_api_key    # optionally use CONFLUENT_CLOUD_API_KEY env var
  cloud_api_secret = var.confluent_cloud_api_secret # optionally use CONFLUENT_CLOUD_API_SECRET env var
}

resource "confluent_schema_registry_cluster_mode" "example" {
  schema_registry_cluster {
    id = confluent_schema_registry_cluster.essentials.id
  }
  rest_endpoint = confluent_schema_registry_cluster.essentials.rest_endpoint
  mode          = "READONLY"
  credentials {
    key    = "<Schema Registry API Key for confluent_schema_registry_cluster.essentials>"
    secret = "<Schema Registry API Secret for confluent_schema_registry_cluster.essentials>"
  }

  lifecycle {
    prevent_destroy = true
  }
}
```

### Option #2: Manage a single Schema Registry cluster in the same Terraform workspace

```terraform
provider "confluent" {
  schema_registry_id            = var.schema_registry_id            # optionally use SCHEMA_REGISTRY_ID env var
  schema_registry_rest_endpoint = var.schema_registry_rest_endpoint # optionally use SCHEMA_REGISTRY_REST_ENDPOINT env var
  schema_registry_api_key       = var.schema_registry_api_key       # optionally use SCHEMA_REGISTRY_API_KEY env var
  schema_registry_api_secret    = var.schema_registry_api_secret    # optionally use SCHEMA_REGISTRY_API_SECRET env var
}

resource "confluent_schema_registry_cluster_mode" "example" {
  mode          = "READONLY"

  lifecycle {
    prevent_destroy = true
  }
}
```

<!-- schema generated by tfplugindocs -->
## Argument Reference

The following arguments are supported:

- `schema_registry_cluster` - (Optional Configuration Block) supports the following:
    - `id` - (Required String) The ID of the Schema Registry cluster, for example, `lsrc-abc123`.
- `rest_endpoint` - (Optional String) The REST endpoint of the Schema Registry cluster, for example, `https://psrc-00000.us-central1.gcp.confluent.cloud:443`).
- `credentials` (Optional Configuration Block) supports the following:
    - `key` - (Required String) The Schema Registry API Key.
    - `secret` - (Required String, Sensitive) The Schema Registry API Secret.

-> **Note:** A Schema Registry API key consists of a key and a secret. Schema Registry API keys are required to interact with Schema Registry clusters in Confluent Cloud. Each Schema Registry API key is valid for one specific Schema Registry cluster.

-> **Note:** Use Option #2 to simplify the key rotation process. When using Option #1, to rotate a Schema Registry API key, create a new Schema Registry API key, update the `credentials` block in all configuration files to use the new Schema Registry API key, run `terraform apply -target="confluent_schema_registry_cluster_mode.orders"`, and remove the old Schema Registry API key. Alternatively, in case the old Schema Registry API Key was deleted already, you might need to run `terraform plan -refresh=false -target="confluent_schema_registry_cluster_mode.orders" -out=rotate-schema-registry-api-key` and `terraform apply rotate-schema-registry-api-key` instead.

!> **Warning:** Use Option #2 to avoid exposing sensitive `credentials` value in a state file. When using Option #1, Terraform doesn't encrypt the sensitive `credentials` value of the `confluent_kafka_cluster_mode` resource, so you must keep your state file secure to avoid exposing it. Refer to the [Terraform documentation](https://www.terraform.io/docs/language/state/sensitive-data.html) to learn more about securing your state file.

- `mode` - (Optional String) The global Schema Registry mode. Accepted values are: `READWRITE`, `READONLY`, `READONLY_OVERRIDE`, and `IMPORT`.

## Attributes Reference

In addition to the preceding arguments, the following attributes are exported:

- `id` - (Required String) The ID of the Mode, in the format `<Schema Registry cluster ID>`, for example, `lsrc-abc123`.

## Import

You can import a Mode by using the Schema Registry cluster ID, Subject name in the format `<Schema Registry cluster ID>`, for example:

```shell
$ export IMPORT_SCHEMA_REGISTRY_API_KEY="<schema_registry_api_key>"
$ export IMPORT_SCHEMA_REGISTRY_API_SECRET="<schema_registry_api_secret>"
$ export IMPORT_SCHEMA_REGISTRY_REST_ENDPOINT="<schema_registry_rest_endpoint>"
$ terraform import confluent_schema_registry_cluster_mode.example lsrc-abc123
```

!> **Warning:** Do not forget to delete terminal command history afterwards for security purposes.