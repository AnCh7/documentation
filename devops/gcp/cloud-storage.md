# Cloud Storage

> References:
>
> https://cloud.google.com/storage/docs/concepts

## Concepts





## How-to guides

### Overview of access control

- **Uniform**: [Uniform bucket-level access](https://cloud.google.com/storage/docs/uniform-bucket-level-access) allows you to use [Identity and Access Management (IAM)](https://cloud.google.com/storage/docs/access-control/iam) alone to manage permissions. IAM applies permissions to all the objects contained inside the bucket or groups of objects with common name prefixes. IAM also allows you to use features that are not available when working with ACLs, such as [IAM Conditions](https://cloud.google.com/iam/docs/conditions-overview) and Cloud Audit Logs.
- **Fine-grained**: The fine-grained option enables you to use IAM and [Access Control Lists (ACLs)](https://cloud.google.com/storage/docs/access-control/lists) together to manage permissions. ACLs are a legacy access control system for Cloud Storage designed for interoperability with Amazon S3. You can specify access and apply permissions at both the bucket level and per individual object.

In addition to IAM and ACLs, the following tools are available to help you control access to your resources:

- Use [signed URLs](https://cloud.google.com/storage/docs/access-control/signed-urls) to give time-limited read or write access to an object through a URL you generate.
- Use [signed policy documents](https://cloud.google.com/storage/docs/authentication/signatures#policy-document) to specify what can be uploaded to a bucket.
- Use [Firebase Security Rules](https://firebase.google.com/docs/storage/security) to provide granular, attribute-based access control to mobile and web apps.
- Use [Credential Access Boundaries](https://cloud.google.com/iam/docs/downscoping-short-lived-credentials) to downscope the permissions that are available to an OAuth 2.0 access token.

##### Identity and Access Management

IAM allows you to control who has access to the *resources* in your Google Cloud project.

The set of access rules you apply to a resource is called an IAM *policy*. An IAM policy applied to your **project** defines the actions that users can take on all objects or buckets within your project. An IAM policy applied to a **single bucket** defines the actions that users can take on that specific bucket and objects within it.

*Members* are the "who" of IAM. Members can be individual users, groups, domains, or even the public as a whole. 

Members are assigned *roles*, which grant members the ability to perform actions in Cloud Storage as well as Google Cloud more generally. Roles are a bundle of one or more [permissions](https://cloud.google.com/storage/docs/access-control/iam#permissions).

Each role is a collection of one or more *permissions*. Permissions are the basic units of IAM: each permission allows you to perform a certain action.

Granting roles at the bucket level does not affect any existing roles that you granted at the project level, and vice versa.

Some roles can be used at both the project level and the bucket level. When used at the project level, the permissions they contain apply to all buckets and objects in the project. Some roles can only be applied at one level.

*Legacy Bucket* IAM roles work in tandem with [bucket ACLs](https://cloud.google.com/storage/docs/access-control/lists#permissions): when you add or remove a Legacy Bucket role, the ACLs associated with the bucket reflect your changes. Similarly, changing a bucket-specific ACL updates the corresponding Legacy Bucket IAM role for the bucket.

Cloud Storage supports *convenience values*, which are a special set of members that can be applied specifically to your IAM bucket policies.

[IAM Conditions](https://cloud.google.com/iam/docs/conditions-overview) allows you to set conditions that control how permissions are granted to members:

- **[`resource.name`](https://cloud.google.com/iam/docs/conditions-attribute-reference#resourcename_attribute):** Grant access to buckets and objects based on the bucket or object name.
- **[Date/time](https://cloud.google.com/iam/docs/conditions-attribute-reference#date-time):** Set an expiration date for the permission.

Best practices:

- Use the principle of least privilege when granting access.
- Avoid granting roles with `setIamPolicy` permission to people you do not know.
- Be careful how you grant permissions for anonymous users.
- Avoid setting permissions that result in inaccessible buckets and objects.
- Be sure you delegate administrative control of your buckets.

Examples:

- Adding a member to a bucket-level policy

  ```json
  {
    "bindings":[
      {
        "role": "roles/storage.objectViewer",
        "members":[
          "jane@gmail.com"
        ]
      }
    ]
  }
  ```

- Adding a member to a project-level policy

  ```json
  {
    "policy": {
      "version": "0",
      "bindings": {
        "role": "roles/storage.objectViewer",
        "members": "jane@gmail.com"
      },
    }
  }
  ```


##### Uniform bucket-level access

Allows you to uniformly control access to your Cloud Storage resources. When you enable uniform bucket-level access on a bucket, ACLs are disabled, and only bucket-level IAM permissions grant access to that bucket and the objects it contains. You revoke all access granted by object ACLs and the ability to administrate permissions using bucket ACLs.

##### Access control lists (ACLs)

ACL is a mechanism you can use to define who has access to your buckets and objects, as well as what level of access they have. In Cloud Storage, you apply ACLs to individual buckets and objects. Each ACL consists of one or more *entries*. An entry gives a specific user (or group) the ability to perform specific actions. Each entry consists of two pieces of information:

- A **permission**, which defines *what* actions can be performed (for example, read or write).
- A **scope** (sometimes referred to as a *grantee*), which defines *who* can perform the specified actions (for example, a specific user or group of users).

*Permissions* describe *what* can be done to a given object or bucket (READER, WRITER, etc.)

*Scopes* specify *who* it is that has a given permission: Google account email address, Google group email address, etc.

A **predefined** or "canned" ACL is an alias for a set of specific ACL entries that you can use to quickly apply many ACL entries at once to a bucket or object: private, bucketOwnerRead, bucketOwnerFullControl etc.

When buckets are created or objects are uploaded, if you do not explicitly assign an ACL to them, they are given the **default ACL.**

Best practices:

- Use the principle of least privilege when granting access to your buckets and objects.
- Avoid granting OWNER permission to people you do not know.
- Be careful how you grant permissions for anonymous users.
- Avoid setting ACLs that result in inaccessible objects.
- Be sure you delegate administrative control of your buckets.
- Be aware of Cloud Storage's interoperable behavior.
- You cannot apply an ACL that specifies a different bucket or object owner.
- The bucket or object owner always has OWNER permission of the bucket or object.

##### Cross-origin resource sharing (CORS)

The [same-origin policy](https://www.w3.org/Security/wiki/Same_Origin_Policy) is a security policy enforced on client-side web applications (like web browsers) to prevent interactions between resources from different origins.

There are two types of CORS requests: simple and preflighted. A [simple request](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Simple_requests) can be initiated directly. A [preflighted request](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Preflighted_requests) must send a preliminary, "preflight" request to the server to get permission before the primary request can proceed.

Cloud Storage allows you to set a CORS configuration at the bucket level only. 







