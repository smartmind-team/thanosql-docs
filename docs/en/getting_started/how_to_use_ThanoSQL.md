---
title: How to use ThanoSQL Web
---

# **How to use the ThanoSQL Website**

## Preface

- 3 min read
- Updated Date : {{ git_revision_date_localized }}

## **1. Access the ThanoSQL Web**

- After going to [ThanoSQL](https://www.thanosql.ai/en), you can get to the login screen by clicking the Login button.
[![IMAGE](/en/img/getting_started/img0.png)](/en/img/getting_started/img0.png)

!!! note ""
      ThanoSQL users must sign up first to use the console.

## **2. Create Account**

- Non-members must proceed with their account creation by clicking "Register as a member" on the ThanoSQL login screen.

[![IMAGE](/en/img/getting_started/img1.png){: style="width:450px"}](/en/img/getting_started/img1.png)

- To proceed, you must authenticate your email, set a new password, and agree to the terms and conditions.

[![IMAGE](/en/img/getting_started/img2.png){: style="width:450px"}](/en/img/getting_started/img2.png)

- After authenticating your email, click "Create an account" to complete your registration.

[![IMAGE](/en/img/getting_started/signup_complete.png){: style="width:450px"}](/en/img/getting_started/signup_complete.png)

## **3. Using the ThanoSQL Console**

- After completing your registration, return to the login page and enter your email and password to log in. If you are logged in for the first time, you can use the service by creating a workspace.

[![IMAGE](/en/img/getting_started/img3.png){: style="width:450px"}](/en/img/getting_started/img3.png)

!!! warning "Caution when creating workspace"
      When creating a new workspace, your workspace name must be unique and should start with a letter and can only be composed of lowercase letters and numbers.

## **4. Create Workspace**

- Once you have created the workspace, you can see a workspace with the name you entered.

[![IMAGE](/en/img/getting_started/img7.png)](/en/img/getting_started/img7.png)

- You can enter the ThanoSQL Console by clicking the Open button on the workspace page.

[![IMAGE](/img/getting_started/img4.png)](/img/getting_started/img4.png)

## **5. ThanoSQL Workspace**

- To use the ThanoSQL service, you must use the API token. Please note that API tokens can always be newly issued, but when they are newly issued, previous tokens can no longer be used.

- Pressing the GET API_TOKEN button in the upper right corner of your workspace automatically generates an API token and copies it to your clipboard. You can use the service normally using the query below.

- A more detailed workspace usage guide can be found in [How to Use ThanoSQL Workspace](/en/getting_started/hello_ThanoSQL/)

```sql
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

ex)

```sql
%load_ext thanosql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```

[![IMAGE](/img/getting_started/img6.png)](/img/getting_started/img6.png)

!!! danger  
      To run ThanoSQL queries in the ThanoSQL workspace, you must always include the above query at the top of the file.
