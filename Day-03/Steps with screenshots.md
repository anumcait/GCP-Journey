## Step by Step Screenshots

### Through Console

****Create a project & Create IAM Role & Assign role to the User****

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/d4411f96-3a09-48cb-ac1c-ba0aad606518" />

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/46b47f49-705c-4bf2-b1ef-7d4c4749a66d" />


**Groups**

If Organization is there we can assign Groups, For now there is no organization, so we cannot assign Groups

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/a89a0672-9bc7-4454-b439-652891f71480" />


**Roles**

We can create custom roles --> Select Roles --> Custom Role --> Fill Details --> Select required roles --> then Add Role

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/aadd5c41-f832-45c9-8909-709ce0948d03" />


Then lists all roles 


<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/1f83d177-cfac-45a1-8e18-0ffd61cb864a" />



Then Assign role to the user


<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/148ec284-b70c-4c5e-888e-977fbf59f2cb" />


Now Custom Roles Assigned to the User 


<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/286e67b5-8546-44b8-bab9-5b4323911bfc" />


### Thourgh Cloud Shell

First Click on Shell Icon --> Authorize the Cloud Shell

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/5a91753f-b562-41f4-b3a7-17f9adb2363a" />

Then Create Project through cloud shell

Assign a Project ID : **export PROJECT_ID = "<Project_id_name>"**

create project : **gcloud projects create $PROJECT_ID**

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/9a34c7ce-488b-420b-8479-dc55605f9fc7" />

Now Created Project -> Check with reload, You can see the new project in the list (Click on All button if not seen)

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/ed56de52-3c8c-4fc9-ba49-f0a64ece3a6d" />

Before there is no IAM user, role 

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/d51440a5-28f1-47e2-8c22-f631d20f62dd" />

**Now create IAM user, role **

First configure to set the project : gcloud config set project $PROJECT_ID

This will change the project name to the selected project

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/581e6a01-c86e-4fd2-86f3-1f3fde429a49" />

Now create and assign role : gcloud projects add-iam-policy-binding $PROJECT_ID \
--member="user:<username>" \
--role="roles/viewer"

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/82f86ee8-892c-49e0-b1bb-6f01af9c3446" />

Now the user with role we can see

<img width="786" height="401" alt="image" src="https://github.com/user-attachments/assets/090b4171-d217-4bc4-a7db-ed02e5800ce2" />



















































