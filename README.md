This is a small local backend to host user avatars for the Axion app.

Endpoints added (do not remove any existing backend endpoints in your real backend):

- POST /api/upload-avatar
  - form-data: avatar (file), address (string)
  - returns JSON: { address, url }

- GET /api/avatar/:address
  - returns JSON: { address, url } or 404

- Static files served at /uploads/*

Run locally:

1) cd backend
2) npm install
3) npm start

This is optional — your main production backend can continue to operate. The app is written to try the production BACKEND_URL first; you can also run this local backend and point the app to it for local testing by changing `app/(tabs)/api.ts` BACKEND_URL.

Deploying to GitHub + Vercel
--------------------------------

This repository can be deployed on Vercel. There are two important notes:

- Vercel runs Node files as serverless functions. The repo now exports the
  Express `app` when Vercel is detected so the same code can be used both
  locally and on Vercel.
- File uploads stored on the server filesystem are not persistent on Vercel.
  If you need users to upload avatars and persist them, use an external
  object store (S3, DigitalOcean Spaces, Cloudinary, etc.) and modify the
  upload handler to save files there. The current implementation will work
  for serving committed files in `uploads/`, but runtime uploads won't persist.

Quick steps to publish and deploy

1) Create a GitHub repository (public or private) and copy the repo HTTPS URL.

2) From this project directory run (PowerShell):

```powershell
git init
git add .
git commit -m "Initial commit for Vercel deployment"
git remote add origin <YOUR_GITHUB_HTTPS_URL>
git branch -M main
git push -u origin main
```

3) Go to https://vercel.com, sign in, choose "Import Project", select the
   Git provider and pick the repository you just pushed. Select the project
   settings (Framework Preset: Other / Node) and deploy.

4) After deploy you'll get a Vercel URL. The API endpoints will be available
   at:

   - POST /api/upload-avatar
   - GET /api/avatar/:address

   Keep in mind POST /api/upload-avatar will accept uploads but they will not
   persist between function invocations on Vercel. For production use, use
   an external file store and update the handler accordingly.

If you'd like, I can:

- Add an example S3/Cloudinary integration for uploads (you'll need to provide
  credentials or I can add environment-variable wiring and docs), or
- Create a minimal `api/` serverless-only implementation that stores incoming
  files to an external provider and returns stable URLs.

