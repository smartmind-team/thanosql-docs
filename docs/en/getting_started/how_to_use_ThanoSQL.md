---
title: How to use ThanoSQL
---

# **How to use the ThanoSQL Web Service**

## Preface

- 3 min read
- Updated Date : {{ git_revision_date_localized }}

## **1. Access the ThanoSQL Web**

- After accessing [ThanoSQL](https://www.thanosql.ai), you can access the login screen by clicking the Login button.
  <a href = "/img/getting_started/img0.png">
  <img src = "/img/getting_started/img0.png"></img>
  </a>

!!! note ""
ThanoSQL is available free of charge to everyone during the promotion period from June 1st to August 31st.

ThanoSQL users must sign up to use the console.

## **2. Create Account**

- Non-members proceed with their membership by clicking "Register Membership" on the ThanoSQL login screen.

<a href = "/img/getting_started/img1.png">
      <img src = "/img/getting_started/img1.png" width = 350px></img>
</a>

- To proceed with membership, you must enter your email authentication and password and confirm the terms and conditions.

<a href = "/img/getting_started/img2.png">
      <img src = "/img/getting_started/img2.png" width=350px></img>
</a>

- After authenticating your email, click "Create an account" to complete your membership.

<a href = "/img/getting_started/signup_complete.png">
      <img src = "/img/getting_started/signup_complete.png" width=350px></img>
</a>

## **3. Using the ThanoSQL Console**

- After completing your membership, return to the login page and enter your email and password to log in. If you are logged in for the first time, you can use the service by creating a workspace.

!!! note ""
During the promotion period, you are limited to one workspace per account.

<a href = "/img/getting_started/img3.png">
      <img src = "/img/getting_started/img3.png" width = 400px></img>
</a>

!!! warning "Caution when creating workspace"

When creating a workspace, the name can be used in combination with English lowercase letters and numbers of up to 20 characters, and it is not generated if the name overlaps with another person's workspace. The first letter of the workspace name must begin in lowercase English.

## **4. Create Workspace**

- Once you have created the workspace, you can check the workspace with the ID you entered as below.

<a href = "/img/getting_started/img7.png">
      <img src = "/img/getting_started/img7.png" width = 400px></img>
</a>

- After creation, you can enter the ThanoSQL Console through the Open button within the workspace page.

<a href = "/img/getting_started/img4.png">
      <img src = "/img/getting_started/img4.png"></img>
</a>

## **5. ThanoSQL Workspace**

- To use the ThanoSQL service, you must use the API token. Please note that API tokens can be newly issued, but if they are newly issued, previously issued tokens can no longer be used.

- Pressing the GET API_TOKEN button in the upper right corner of your workspace automatically generates an API token and copies it to the clipboard. You can use the service normally through the query below.

- The overall workspace usage can be found in [How to Use ThanoSQL Workspace](/getting_started/hello_ThanoSQL/)

```sql
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

ex)

```sql
%load_ext thanosql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```

<a href = "/img/getting_started/img6.png">
      <img src = "/img/getting_started/img6.png"></img>
</a>

!!! danger  
To use ThanoSQL grammar on the ThanoSQL workspace, you must always run the above query at the top of the file.
