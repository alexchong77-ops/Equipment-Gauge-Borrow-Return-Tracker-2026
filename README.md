# Equipment & Gauge Borrow/Return Tracker

Responsive pin gauge and calibrated-tool borrow/return web app seeded from the supplied workbook (1,028 inventory records). Runs on Node.js without package installation.

## Start and sign in

1. Install Node.js 18 or newer on the server computer.
2. In this folder, run `node server.js`.
3. Open `http://0.0.0.0:10000`.
4. Initial superuser: username `Admin`, password `superadmin123`. On first sign-in, the app requires a new password of at least 12 characters.
5. Sign in as Admin and register each staff member with a name, unique username, and password. Staff can then sign in and use the tracker. Only Admin can manage accounts.
6. In **Gauge inventory**, add each gauge’s certificate number and calibration due date before issuing it.

Passwords are stored as salted scrypt hashes. Audit events record the signed-in account, not a name entered into a form. Each borrow requires staff, job order, drawing number, expected return, a current calibration record, a phone camera photo of the size marking, and size verification. Each return requires a camera photo and condition check. Monitoring search covers staff, job order, drawing, gauge ID, and size.

## Phone and shared access

By default, the server listens only on this computer (`127.0.0.1`). For shop devices on a trusted local network, run in PowerShell with `$env:HOST='0.0.0.0'; node server.js`, then visit `http://<server-computer-LAN-IP>:4317`. Use the photo input on a borrow or return form to open the phone camera/photo picker; the app saves a compressed image on the server.

This starter app has no roles beyond Admin/staff and no protection against an untrusted device on the same network. Keep it on an isolated trusted shop network. Before exposing it beyond that network, use approved authentication controls and HTTPS. In-app overdue and due-soon flags update automatically. Browser alerts work while the app is open and permitted by the browser; it does not send email or notify devices while closed.

## Data

The server saves records and salted password hashes to `data/state.json` and images to `data/photos/`. Back up both folders together. **Export backup** downloads records as JSON but not image files. Audit events are appended by the app for borrows, returns, staff account changes, password changes, and calibration updates. The server owner can still edit local files, so keep backups for formal retention.

The workbook contained no staff names or calibration certificate details, so add those in the app before issue. The workbook is unchanged.

## Public deployment (Render)

This repository includes `render.yaml` for a single-instance Render web service in Singapore. It uses a persistent disk for `state.json` and camera evidence; do not remove the disk or scale the service to multiple instances while this file-based storage is in use. Render's persistent disks require a paid web service. During initial setup, Render asks for `ADMIN_INITIAL_PASSWORD`; use a unique password of at least 12 characters and change it again at first sign-in. Do not commit live `data/` files or passwords to source control. After the service deploys, Render provides its shareable HTTPS `onrender.com` URL.

To deploy, push this project to a private GitHub repository (the existing `.gitignore` excludes live `data/` and `node_modules/`), then in Render choose **New > Blueprint**, connect that repository, review the paid persistent disk and service plan, enter the initial Admin password, and apply. The deployed app starts with an empty state unless a deliberate, protected migration of local records and photo files is performed. Preserve the local data and photos as the source backup during rollout.
