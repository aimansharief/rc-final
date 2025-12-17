# 🛠️ Environment Setup Guide

This document provides step-by-step instructions to initialize the environment, configure Keycloak, and update the registry setup.

---

## 🧩 Step 1: Initialize Docker Compose

Run the following command to set up and start the initial containers:

```bash
make compose-init
```

---

## 🗄️ Step 2: Disable SSL Requirement in Keycloak Database

1. **SSH into the PostgreSQL container:**

   ```bash
   docker exec -it <postgres-container-name> sh
   ```
   ```bash
   psql -U <username> -d <database-name>
   ```

2. **Execute the following SQL commands to disable SSL for the required realms:**

   ```sql
   UPDATE REALM SET ssl_required = 'NONE' WHERE id = 'master';
   UPDATE REALM SET ssl_required = 'NONE' WHERE id = 'sunbird-rc';
   ```

3. **Exit the PostgreSQL session and restart the Keycloak container:**

   ```bash
   docker restart <keycloak-container-name>
   ```

---

## ⚙️ Step 3: Update Keycloak Configuration

1. **Log in to the Keycloak Admin Console.**

2. **Navigate to the target realm** (for example, `sunbird-rc`).

3. **Update the Frontend URL** to match your deployment environment.
`Ex: http://<ip>:8080/auth`
4. **(New) Create an Admin User**
   1. From the left menu, navigate to Users → Add User.
   2. Enter the Username: admin.
   3. Click Create.
   4. Open the Credentials tab.
   5. Set the Password to abcd@123.
   6. Turn off the Temporary option.
   7. Click Set Password and confirm.
   8. Go to the Role Mappings tab.
   9. Under Available Roles, select admin and click Add Selected.

---

## 🔑 Step 4: Generate and Configure a Secret

1. **From the Keycloak UI, generate a new client secret** for your client.

2. **Update the `.env` file** with the generated secret:

   ```env
   KEYCLOAK_SECRET=<your-new-secret>
   ```

3. **Restart the Registry container** to apply the updated configuration:

   ```bash
   docker-compose up -d --force-recreate --no-deps registry
   ```

---
## 🚀 Step 5: Trigger APIs with Postman

1. **Import the Postman Collection:**
   - Open Postman.
   - Click on `Import` and select the `postmancollection.json` file.

2. **Configure Postman Environment:**
   - Create a new Postman environment.

3. **Execute API Requests:**
   - Select the configured environment.
   - Run the requests within the imported collection to test the APIs.

## ✅ Verification

After completing all steps, verify that:
- All containers are running properly
- Keycloak is accessible without SSL errors
- The registry can authenticate with Keycloak using the new secret

## 🔧 Troubleshooting

If you encounter issues:
- Check container logs using `docker logs <container-name>`
- Verify environment variables are correctly set
- Ensure all containers are on the same Docker network