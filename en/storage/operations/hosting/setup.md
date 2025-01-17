# Hosting setup

{{ objstorage-name }} lets you configure a bucket:

* For [static website hosting](#hosting).
* To [redirect all requests](#redirects).
* For [conditionally redirecting requests](#redirects-on-conditions).

## Static website hosting {#hosting}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), go to the bucket you want to configure hosting for.
   1. Make sure public access is allowed to the bucket. If not, follow the instructions in [{#T}](../buckets/bucket-availability.md).
   1. In the left panel, select **Website**.
   1. Under **Hosting**, specify:
      * The website homepage.
      * The page to be displayed to the user in the event of 4xx errors. Optional.
   1. Click **Save**.

- {{ TF }}

   If you do not have {{ TF }} yet, [install it and configure the {{ yandex-cloud }} provider](../../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).

   Before you start, retrieve the [static access keys](../../../iam/operations/sa/create-access-key.md): a secret key and a key ID used for authentication in {{ objstorage-short-name }}.

   1. In the configuration file, describe the parameters of resources that you want to create:

      
      ```
      provider "yandex" {
        token     = "<OAuth>"
        cloud_id  = "<cloud ID>"
        folder_id = "<folder ID>"
        zone      = "{{ region-id }}-a"
      }

      resource "yandex_storage_bucket" "test" {
        access_key = "<static key ID>"
        secret_key = "<secret key>"
        bucket     = "<bucket name>"
        acl        = "public-read"
        website {
          index_document = "index.html"
          error_document = "error.html"
        }

      }
      ```



      Where:

      * `access_key`: The ID of the static access key.
      * `secret_key`: The value of the secret access key.
      * `bucket`: Bucket name.
      * `acl`: Parameters for [ACL](../../concepts/acl.md#predefined-acls).
      * `website`: Website parameters:
         * `index_document`: Absolute path to the file of the website's homepage. Required parameter.
         * `error_document`: Absolute path to the file to be displayed to the user in the event of `4xx` errors. Optional.

   1. Make sure that the configuration files are valid.

      1. In the command line, go to the directory where you created the configuration file.
      1. Run the check using the command:

         ```
         terraform plan
         ```

      If the configuration is described correctly, the terminal displays a list of created resources and their parameters. If the configuration contains errors, {{ TF }} will point them out.

   1. Deploy the cloud resources.

      1. If the configuration doesn't contain any errors, run the command:

      ```
      terraform apply
      ```

      1. Confirm that you want to create the resources.

      Afterwards, all the necessary resources are created in the specified folder. You can check that the resources are there with the correct settings using the [management console]({{ link-console-main }}).

{% endlist %}

## Redirect all requests {#redirects}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), go to the bucket you wish to configure redirection for.
   1. Make sure public access is allowed to the bucket. If not, follow the instructions in [{#T}](../buckets/bucket-availability.md).
   1. In the left panel, select **Website**.
   1. Under **Redirect**, specify:
      * The domain name of the host to act as the redirect target for all requests to the current bucket.
      * The protocol if the specified host accepts requests strictly over a specific protocol.

- {{ TF }}

   {% include [terraform-definition](../../../_tutorials/terraform-definition.md) %}

   
   For more information about the {{ TF }}, [see the documentation](../../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).


   To set up a redirect for all requests:

   1. Open the {{ TF }} configuration file and add the `redirect_all_requests_to` parameter to the `yandex_storage_bucket` resource description.

      ```hcl
      ...
      resource "yandex_storage_bucket" "test" {
        access_key = "<static_key_ID>"
        secret_key = "<secret_key>"
        bucket     = "<bucket_name>"
        acl        = "public-read"
        website {
          index_document = "<absolute_path_to_website_homepage_file>"
          error_document = "<absolute_path_to_error_file>"
      	 redirect_all_requests_to = "<host_name>"
        }
      }
      ...
      ```

      Where:
      * `access_key`: The ID of the static access key.
      * `secret_key`: The value of the secret access key.
      * `bucket`: Bucket name.
      * `acl`: Parameters for [ACL](../../concepts/acl.md#predefined-acls).
      * `website`: Website parameters:
         * `index_document`: Absolute path to the file of the website's homepage. Required parameter.
         * `error_document`: Absolute path to the file to be displayed to the user in the event of `4xx` errors. Optional.
         * `redirect_all_requests_to`: The domain name of the host to act as the redirect target for all requests to the current bucket. You can set a protocol prefix (`http://` or `https://`). By default, the original request's protocol is used.

      For more information about the `yandex_storage_bucket` resource parameters in {{ TF }}, see the [provider documentation]({{ tf-provider-link }}//storage_bucket#static-website-hosting).

   1. Check the configuration using the command:

      ```bash
      terraform validate
      ```

      If the configuration is correct, the following message is returned:

      ```bash
      Success! The configuration is valid.
      ```

   1. Run the command:

      ```bash
      terraform plan
      ```

      The terminal will display a list of resources with parameters. No changes are made at this step. If the configuration contains errors, {{ TF }} will point them out.

   1. Apply the configuration changes:

      ```bash
      terraform apply
      ```

   1. Confirm the changes: type `yes` into the terminal and press **Enter**.

      You can use the [management console]({{ link-console-main }}) to check the request redirect settings.

{% endlist %}

## Conditional request redirection {#redirects-on-conditions}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), go to the bucket you wish to configure conditional request redirects for.
   1. Make sure public access is allowed to the bucket. If not, follow the instructions in [{#T}](../buckets/bucket-availability.md).
   1. In the left panel, select **Website**.
   1. Under **Hosting**, add a redirect rule with the redirect condition and the new address.
      * Condition. For example, you can do a redirect when you receive a specified response code or if the beginning of the object key in a request matches the specified key.
      * Redirection:
         * The domain name of the host where requests that satisfy the condition should redirect.
         * The protocol to use to send redirected requests.
         * Response code to determine the redirect type.
         * Replace the entire key specified in the condition or its initial sequence only.

- {{ TF }}

   {% include [terraform-definition](../../../_tutorials/terraform-definition.md) %}

   
   For more information about the {{ TF }}, [see the documentation](../../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).


   To set up a conditional redirect of requests:

   1. Open the {{ TF }} configuration file and add the `routing_rules` parameter to the bucket description:

      ```hcl
      ...
      resource "yandex_storage_bucket" "test" {
        access_key = "<static_key_ID>"
        secret_key = "<secret_key>"
        bucket     = "<bucket_name>"
        acl        = "public-read"
        website {
          index_document = "<absolute_path_to_website_homepage_file>"
          error_document = "<absolute_path_to_error_file>"
      	 routing_rules            = <<EOF
          [
            {
              "Condition": {
                "KeyPrefixEquals": "<object_key_prefix>",
                "HttpErrorCodeReturnedEquals": "<HTTP_response_code>"
              },
              "Redirect": {
                "Protocol": "<new_schema>",
                "HostName": "<new_domain_name>",
                "ReplaceKeyPrefixWith": "<new_object_key_prefix>",
                "ReplaceKeyWith": "<new_object_key>",
                "HttpRedirectCode": "<new_HTTP_response_code>"
              }
            },
          ...
          ]
          EOF
        }
      }
      ...
      ```

      Where:
      * `access_key`: The ID of the static access key.
      * `secret_key`: The value of the secret access key.
      * `bucket`: Bucket name.
      * `acl`: Parameters for [ACL](../../concepts/acl.md#predefined-acls).
      * `website`: Website parameters:
         * `index_document`: Absolute path to the file of the website's homepage. Required parameter.
         * `error_document`: Absolute path to the file to be displayed to the user in the event of `4xx` errors. Optional.
         * `routing_rules`: Rules for redirecting requests in JSON format. Each rule's `Condition` and `Redirect` fields must contain at least one <q>key-value</q> pair. For more information about the supported fields, see the [data schema](../../s3/api-ref/hosting/upload.md#request-scheme) of the respective API method (the **For conditionally redirecting requests** tab).

      For more information about the `yandex_storage_bucket` resource parameters in {{ TF }}, see the [provider documentation]({{ tf-provider-link }}//storage_bucket#static-website-hosting).

   1. Check the configuration using the command:

      ```bash
      terraform validate
      ```

      If the configuration is correct, the following message is returned:

      ```bash
      Success! The configuration is valid.
      ```

   1. Run the command:

      ```bash
      terraform plan
      ```

      The terminal will display a list of resources with parameters. No changes are made at this step. If the configuration contains errors, {{ TF }} will point them out.

   1. Apply the configuration changes:

      ```bash
      terraform apply
      ```

   1. Confirm the changes: type `yes` into the terminal and press **Enter**.

      You can use the [management console]({{ link-console-main }}) to check the settings for conditionally redirecting requests.

{% endlist %}
