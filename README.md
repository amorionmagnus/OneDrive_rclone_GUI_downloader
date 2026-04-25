# OneDrive_rclone_GUI_downloader

OneDrive rclone GUI Downloader

This is a Tkinter GUI wrapper for rclone.

Main features:
- Loads files and folders from an rclone remote, default: onedrive:
- Shows a tree view with expandable folders and files.
- Loads subfolders lazily when expanded.
- Lets you select files or folders using the ✓ column.
- Downloads selected items sequentially, not in parallel.
- Preserves folder hierarchy under the chosen destination folder.
- Uses rclone copy for folders and rclone copyto for individual files.
- Shows transfer progress: transferred size, total size, percent, speed, ETA, elapsed time and file count.
- Shows a live rclone error panel with timestamps.
- Marks items with detected file-level errors in red and shows the error next to the item.
- Enables “Retry files with errors” after a failed download attempt.
- If the selected remote is missing, it can start rclone configuration and ask only for the Microsoft Client ID.
- If the selected remote already exists, “Configure rclone” first tests the remote. If it works, the app asks whether to keep the existing configuration or delete it and configure again.

Changes in v7:
- Removed config.ini entirely.
- Removed the “Open config” button.
- The app now starts with built-in defaults: remote = onedrive, destination = ~/Downloads/OneDriveShared.
- Changing the remote name or destination folder affects the current session only.

How to run:
1. Put these files in one folder, for example:
   /Users/user/Downloads/OneDriveShared/_prog
2. Make sure rclone is installed:
   brew install rclone
3. Run:
   python3 rclone_gui_downloader.py
   or double-click run_onedrive_rclone_downloader.command (before first use run enable_command_permissions.sh in the same folder).

Safety note:
The app uses rclone copy/copyto, not sync, so it should not delete local files.





How to create a Client ID in Azure / Microsoft Entra

1. Go to the Azure Portal:

   https://portal.azure.com/

2. Sign in with your Microsoft account.

3. If prompted to choose a plan, select Free Trial.

   You do not need paid Azure resources just to register an application for OAuth / Microsoft Graph access.

4. Open:

   Azure Portal -> App registrations -> New registration

5. Enter an application name, for example:

   OneDrive Downloader

6. Under Supported account types, select:

   Accounts in any organizational directory and personal Microsoft accounts

   This is important to properely work with both:
   - business / school Microsoft accounts
   - private Microsoft accounts, such as Outlook.com / personal OneDrive

7. Under Redirect URI, choose the following platform / authentication type:

   Mobile and desktop applications

   Then add localhost as the redirect URI, for example:

   http://localhost

   or, depending on your MSAL / rclone setup:

   http://localhost:53682/

   This is used by MSAL authentication / local OAuth login flow.

8. Click Register.

9. After registration, go to the app's Overview page.

10. Copy:

   Application (client) ID

   This is your Client ID.

11. You usually do not need to create a Client Secret for this kind of app.

   For a local desktop/public-client app, authentication is normally done using MSAL / OAuth login in the browser. Public clients cannot securely store secrets.

12. Go to:

   Authentication

13. In the public client / advanced settings section, enable:

   Allow public client flows

   This is needed for native/public-client OAuth flows used by tools such as rclone or a local desktop app.

14. Go to:

   API permissions -> Add a permission -> Microsoft Graph -> Delegated permissions

15. Add the following delegated Microsoft Graph permissions:

   Files.Read.All
   offline_access
   User.Read
   openid
   profile

   These permissions allow the app to read files through Microsoft Graph, identify the signed-in user, and keep access via refresh tokens.

16. Save the permissions.

17. In some tenants, you may need to click:

   Grant admin consent

   For a private Microsoft account this may not appear in the same way, but for business/school tenants it can be required, especially for broader permissions such as Files.Read.All.

18. You now have the required configuration:

   Client ID: Application (client) ID from Overview
   Redirect URI: localhost
   Account type: business + personal Microsoft accounts
   Public client flows: enabled
   Microsoft Graph permissions:
     - Files.Read.All
     - offline_access
     - User.Read
     - openid
     - profile

Using the Client ID

You can use the created Client ID in one of two ways:

1. Manual rclone OneDrive configuration

   You can run rclone configuration manually, choose OneDrive, and provide your own Client ID when asked. This gives full control over the OAuth setup.

2. Simplified configuration through the application

   Alternatively, you can use the simplified setup inside the app. In that case, the app can guide the user through Microsoft login using MSAL authentication and the configured Client ID, without requiring the user to manually configure every rclone option.
