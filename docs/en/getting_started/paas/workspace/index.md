# __Expolore the Workspace__

The Workspace is a PaaS (Platform as a Service) product that enables data processing, AI/ML modeling, and application development with ThanoSQL. You can leverage the power of SQL and the versatility of Jupyter notebooks to query and analyze your data efficiently.

The workspace is composed of two pages, the **Main Page** and the **Service Page**.

- **Main Page** : Create, view, and manage workspaces.
- **Service Page** : Access various services such as Query Manager and Lab.

## __Main Page__

You can view and manage workspaces collectively. If you don't have an workspace yet, you can create a new one by clicking the **Start button**. Please refer to the [Create Workspace](#create-a-workspace) guide for instructions.

[![IMAGE](/en/img/getting_started/img6.png){: style="max-height:none"}](/en/img/getting_started/img6.png)

① Create New Workspace

- You can go to the workspace creation page to create an additional workspace.

② Workspace

- 2-1. You can check the name of the corresponding workspace.
- 2-2. You can check the operating status of the corresponding workspace.
- 2-3. You can configure the corresponding workspace.
- 2-4. You can check the Plan (name, class), End Date, and Role of the corresponding workspace.
- 2-5. You can access the Service page of the corresponding workspace.

## __Service Page__

On the workspace list of the main page, you can navigate to the service page of the desired workspace by clicking the **Open button** for that workspace.

Here are the available features on the Service page:

- **Query Manager**: A tool that allows you to execute and track ThanoSQL queries. It provides interfaces such as Query Editor, Query Log, and Data Viewer. To try Query Manager in Workspace, see the [Query Manager Manual](./query_manager.md).

- **Lab**: Provides a versatile environment for data exploration, AI/ML modeling, and application development based on Jupyter Notebook. To try Lab in Workspace, see the [Lab Manual](./lab.md).

[![IMAGE](/en/img/getting_started/paas/workspace/img0.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img0.png)

A side navigation(①), a common component of service pages, provides navigation to each feature and provides a shortcut to the [workspace settings page](#workspace-settings-page). Additionally, the drop-down menu(②) in the top right corner allows you to navigate to the service page of other workspaces.

## __Create a Workspace__

- Click the Create New Workspace button on the main page.
- If there is **no** existing workspace, click the **Start button** on the initial main page to navigate to the workspace creation page.

[![IMAGE](/en/img/getting_started/img3.png){: style="max-height:none"}](/en/img/getting_started/img3.png)

### __1. Plan Settings__

[![IMAGE](/en/img/getting_started/img4.png){: style="max-height:none"}](/en/img/getting_started/img4.png)

① **Plan**

- Choose the desired plan.
- Click on the "Start" button to proceed to the next step.

② **Promotion Code**

- Enter the promotion code.

!!! warning
    The promotion code is a one-time use only and cannot be reused

- After entering the code, click on the "Start" button to proceed to the next step.

### __2. Application Form__

[![IMAGE](/en/img/getting_started/img5.png){: style="max-height:none"}](/en/img/getting_started/img5.png)

① Enter the **Workspace Name**.

- The workspace name cannot be duplicated.
- Workspace names can only be written in English (lowercase, first letter) and numbers (last letter). ex)myworkspace123

② Selected Plan

1. Selected Plan
      - Confirm the selected Plan Tier, Plan Name, and Price.
2. Invoice E-mail
      - Please input the email address that will recieve your invoice.
      - **This field is mandatory.**
3. Applicant Name
      - Please input the customer's full name.
4. Phone Number
      - Please input your mobile phone number in case we cannot reach you via email.
5. Inquiry Context
      - Please input any inquiries you may have.
!!! tip ""
      workspace end-date changes, contact information change, etc...

③ Complete Request

- Click on the **Complete Button** to request / complete creating a new workspace.

## __Workspace Settings Page__

You can access general workspace information, usage logs, and data required for development, such as API tokens. You can navigate to these options by clicking on the settings icon in the workspace list on the Main page or the settings icon at the bottom left of the Service page.

### __General__

[![IMAGE](/en/img/getting_started/img10.png){: style="max-height:none"}](/en/img/getting_started/img10.png)

① Workspace Start / Stop

- You can start/stop the workspace and check its status.

② Workspace Name

- You can view the workspace name, but you cannot modify it.

③ Plan

- You can check the Plan Tier and Plan Name of the workspace.
- It is possible to apply for an upgrade of the plan through the Upgrade button.  

④ Effective Date

- This section shows the remaining subscription period of the workspace. After the subscription period expires, you can request to update the subscription period by using the Upgrade button in step ③.

⑤ Workspace Delete

- Delete your Workspace.

!!! warning
    Please contact us in case your Workspace needs to be deleted. Your Workspace data will be permanently deleted once your request is processed. Be aware that this action cannot be reversed.

### __Log__

[![IMAGE](/en/img/getting_started/img11.png){: style="max-height:none"}](/en/img/getting_started/img11.png)

① Workspace Usage

- View your Workspace Usage in a graph.

② Workspace Log

- View the most recent logs of your Workspace. Click on the **View All Button** to view all logs.

### __Developer__

[![IMAGE](/en/img/getting_started/img12.png){: style="max-height:none"}](/en/img/getting_started/img12.png)

① Copy API Token

- You must use the given API token to use our services. Clicking on the API Token's copy button will save it onto your clipboard.

② Refresh API Token

- Be aware that refreshing your API Token will automatically expire your previous tokens.

③ SSH Key Download

- Download your SSH Key as a key.pem file.
