
### Sreenshots & Troubleshooting

**Create Project & Assign IAM Roles to Other User**
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/9de5bc91-99c8-48ab-9ff4-21e5657cde06" />

**Enable Service API & Compute Engine API**

Before enabling we should have link a billing account. Otherwise it won't able to enable APIs

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/e32e5fbb-83ff-4a7a-9368-170cf36beaf4" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/bff7394a-45d9-45d5-bf30-6537cc30d682" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/4bd7e7d3-d7df-47cf-9b9f-93b7807ae90d" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/a6a1b808-7e18-485d-a0ba-d5cec2e77dfe" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/8e7ab0a5-92f2-4201-a907-837d9659481c" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/b75fa17f-7bf4-445d-8738-5992b8b04bfd" />

<img width="641" height="521" alt="image" src="https://github.com/user-attachments/assets/3a9c5810-288b-4a88-8a35-742f7c196b21" />

Then enable Compute engine API

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/fc5c5dc0-d6f9-41cf-ab39-1dfa01f82d1d" />

Now Enabled

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/5cccd223-733b-4e84-ab2c-cdec1562fbde" />

Now go to other User, and create VM

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/1af77132-c9aa-4122-860d-059c47501090" />

**Creation of VM**

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/42b38240-947c-4bb5-99aa-aefdc7cdcc43" />

Select Which core 

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/fa40e5f2-9b78-4353-9c73-4a71357ca3b4" />

Select OS

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/1f73fcbc-4300-4c08-8806-5a6d56bd4429" />

Select Backup

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/d686f05b-059c-4834-bbc5-857fc82c3f07" />

Allow Port HTTP traffic, because i am going to install nginx and see on browser

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/b4d72868-a293-4b29-a36d-7b7fc429ff67" />

selected Spot instance, because it is for poc , it can be destroy any time

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/5aaca572-09a6-4137-a199-c53abd6c967e" />

Click on create button -->

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/b36c3a8c-57ff-473a-96c9-f14f7c89c086" />

Here i have got an error like below

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/5c6843cf-e631-4b7e-8cf9-167aeecef352" />

**Searched for solution** **`This is the solution I found useful`**

1. Grant from Console

2. Go to:
https://console.cloud.google.com/iam-admin/serviceaccounts

3. Find the service account:
177824574300-compute@developer.gserviceaccount.com

Click on it → Permissions tab → Grant Access

Add:

- New Principal: ##email-address that permission to be granted##

- Role: Service Account User

- Click Save

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/b39eca29-2519-435a-832f-10501caf182d" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/ce19653e-04f9-4ca9-bfe0-23341b1fd8fc" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/369e392c-ff3d-4c9b-a659-427b4267ae1b" />

Now with this created again with same steps as above for creating VM instance

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/0009b619-ae85-4022-ac44-a54f2343836a" />

login with cloud shell

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/a0ae61ca-68a2-4632-9d58-48f2f7a70261" />

Here i got error as i have given wrong vm name. Here i confused a bit for project name and vm name

**`I have given project name instead of VM name`**

Again changed name and run again, this time it worked for me 

Check VM name, Zone correctly

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/df389f05-6698-4658-b850-120fd1758c1e" />

Now i am going to install nginx with all the steps , as this is ubuntu machine, I followed these steps

```bash
sudo apt update
sudo apt install nginx -y 

sudo systemctl enable nginx
sudo systemctl start nginx
```

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/256d5cbe-e7cf-446f-8708-92e3cf796ba9" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/2a6a7089-9e70-4258-ba15-64607499fd16" />

Now checked with public IP on Browser, BOOM 💥
I got nginx page

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/76d4d86e-09bf-4a3d-9920-6ebc44013101" />

##























