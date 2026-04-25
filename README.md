# OneDrive_rclone_GUI_downloader

OneDrive rclone GUI Downloader v0.16

This is a Tkinter GUI wrapper for rclone.

Main features:

	•	Graphical macOS-friendly interface for downloading OneDrive content through rclone.
	•	Loads the OneDrive remote file tree directly from the configured rclone remote.
	•	Displays both files and folders in an expandable tree view.
	•	Supports recursive folder browsing with expand/collapse controls.
	•	Loads the file tree up to a default depth of 5 during Load data.
	•	Allows deeper folders to be loaded on demand.
	•	Supports Expand all subfolders with sequential queued requests to avoid overlapping rclone calls.
	•	Hides expand indicators for folders known to be empty.
	•	Supports selecting individual files, whole folders, or nested items for download.
	•	Uses a three-state download marker:
	•	empty: nothing selected,
	•	full: item selected for download,
	•	partial: only nested child items are selected.
	•	Automatically updates parent folders to partial state when deeper files or folders are selected.
	•	Unselecting a parent folder clears selected child items.
	•	Downloads selected items sequentially, not in parallel.
	•	Preserves the original folder hierarchy in the destination directory.
	•	Creates empty selected folders locally, even when rclone copy would otherwise skip them.
	•	Uses rclone copy for folders and rclone copyto for individual files.
	•	Provides a destination folder picker.
	•	Detects local name conflicts before downloading.
	•	Lets the user choose how to handle conflicts:
	•	replace existing files/folders,
	•	skip conflicting items,
	•	keep both by renaming the old local item with _old.
	•	Shows transfer progress, including downloaded size, total size, percentage, speed, ETA, elapsed time, and file count when available from rclone.
	•	Includes a live rclone log panel.
	•	Includes a dedicated error panel with timestamps.
	•	Marks files with detected errors in red.
	•	Displays file-specific error messages next to affected items.
	•	Enables retrying failed file downloads after the transfer finishes.
	•	Supports stopping an active download.
	•	Changes the Download button into Stop while downloading.
	•	Disables unrelated buttons during long-running operations.
	•	Changes the cursor to a loading cursor while waiting for rclone or Graph responses.
	•	Confirms application close when the user clicks the window close button.
	•	Shows an additional red warning if downloads are still running during close.
	•	Logs a final completion summary after all downloads finish, with or without errors.
	•	Includes Add from link support:
	•	resolves own-drive OneDrive links when possible,
	•	expands the tree to the linked file/folder,
	•	selects the linked item for download,
	•	opens public/shared links in the browser when they need to be added manually as a shortcut.
	•	Supports automatic rclone configuration if the onedrive: remote does not exist.
	•	Asks only for the Microsoft Client ID during automatic setup.
	•	Opens the Microsoft OAuth flow in the browser.
	•	Stores OAuth tokens through rclone, not inside the application files.
	•	Provides a Configure rclone button:
	•	tests existing onedrive: configuration,
	•	confirms when it works,
	•	offers to remove and recreate it if needed.
	•	Does not require a project config.ini.
	•	Includes macOS helper scripts for setup and .command permissions.
	•	Avoids storing private data such as OAuth tokens, refresh tokens, tenant IDs, or user-specific paths in the project files.



How to run:
1. Put these files in one folder, for example:
   /Users/user/Downloads/OneDriveShared/_prog
2. Make sure rclone is installed:
   brew install rclone
3. Run:
   python3 rclone_gui_downloader.py
   or double-click run_onedrive_rclone_downloader.command (before first use run enable_command_permissions.sh in the same folder).

Fast install on MacOS:
1. Put these files in one folder, for example:
   /Users/user/Downloads/OneDriveShared/_prog
2. Run MacOS_setup.sh with Admin rights in Terminal

cd /Users/user/Downloads/OneDriveShared/_prog
chmod +x MacOS_setup.sh
./MacOS_setup.sh

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
