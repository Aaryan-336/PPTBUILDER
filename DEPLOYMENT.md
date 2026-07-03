# PPTBUILDER Deployment Guide (Render & Vercel)

This guide walks you through deploying the AI PowerPoint Builder presentation generator stack. 

- **Backend (FastAPI & PostgreSQL)** is deployed on **Render**.
- **Frontend (Next.js & React)** is deployed on **Vercel**.

---

## What We Configured For Production
1. **Dynamic Backend API**: Configured the React app in [page.tsx](file:///Users/aaryankhanna/Downloads/PPTBUILDER/frontend/app/page.tsx#L87) to read from `process.env.NEXT_PUBLIC_API_URL` instead of hardcoding `localhost:8000`.
2. **CORS credentials fix**: Updated [main.py](file:///Users/aaryankhanna/Downloads/PPTBUILDER/backend/main.py#L31-L38) to disable credentials since the backend is stateless. This allows wildcards (`*`) to work safely without triggering browser CORS blocks.
3. **Database URL Normalizer**: Added a fallback in [config.py](file:///Users/aaryankhanna/Downloads/PPTBUILDER/backend/config.py#L17-L19) to translate Render's legacy `postgres://` connection strings into `postgresql://` to prevent SQLAlchemy startup crashes.
4. **Render Blueprint Configuration**: Created [render.yaml](file:///Users/aaryankhanna/Downloads/PPTBUILDER/render.yaml) in the workspace root to define the database and backend services as a single stack.

---

## Step 1: Deploy Backend to Render

1. Log into your [Render Dashboard](https://dashboard.render.com/).
2. Click **New +** at the top right and select **Blueprint**.
3. Connect your GitHub repository containing this codebase.
4. Render will automatically parse the [render.yaml](file:///Users/aaryankhanna/Downloads/PPTBUILDER/render.yaml) file:
   - It will provision a **PostgreSQL Database** named `pptbuilder-db`.
   - It will configure a **Web Service** named `pptbuilder-backend` built from the `/backend` folder.
5. In the **Blueprint Configuration** inputs, you will see a request for the following environment variables:
   - `GROQ_API_KEY`: Provide your Groq Cloud API Key here.
6. Click **Deploy**.
7. Once deployed, copy your **Web Service URL** (e.g. `https://pptbuilder-backend.onrender.com`).

---

## Step 2: Deploy Frontend to Vercel

1. Log into your [Vercel Dashboard](https://vercel.com/).
2. Click **Add New** and select **Project**.
3. Connect your GitHub repository.
4. In the Project configuration:
   - Set the **Root Directory** to `frontend`.
   - Leave the **Framework Preset** as **Next.js**.
   - Expand the **Environment Variables** section and add:
     - **Name**: `NEXT_PUBLIC_API_URL`
     - **Value**: The Render Web Service URL you copied in Step 1 (e.g. `https://pptbuilder-backend.onrender.com`). Ensure there is **no trailing slash** at the end.
5. Click **Deploy**.
6. Once deployed, open your Vercel website link. The application is now live and will work seamlessly from any computer!
