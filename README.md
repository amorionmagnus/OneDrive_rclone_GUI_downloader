# OneDrive_rclone_GUI_downloader

OneDrive rclone GUI Downloader

This release includes 3 files:
• the main Python application: rclone_gui_downloader.py
• the macOS setup script: MacOS_setup.sh
• the double-click launcher for the main application: run_rclone_gui_downloader.command

Main features
•	macOS-friendly graphical interface for downloading OneDrive content via rclone.
•	Uses an existing rclone OneDrive remote or helps configure it through Microsoft OAuth.
•	Automatically opens the Microsoft OAuth flow in the browser when OneDrive is not configured.
•	Does not store OAuth tokens inside the application files; authentication remains managed by rclone.
•	Loads OneDrive files and folders into an expandable file-tree view.
•	Shows both files and folders, not only directories.
•	Supports expanding and collapsing folders to arbitrary depth.
•	Loads the tree up to a default depth and can map deeper folders on demand.
•	Sequentially expands all subfolders when requested, avoiding overlapping rclone tree requests.
•	Hides expand indicators for folders known to be empty.
•	Supports adding items from OneDrive paths or own-drive links where resolvable.
•	For public/shared links, guides the user to add a shortcut to “My files” before downloading.
•	Three-state file-tree selection:
•	empty: not selected,
•	full: selected for queue/download,
•	partial: nested children selected, but not the whole folder.
•	Selecting a folder selects only not-yet-downloaded children by default.
•	Already downloaded green items are not selected automatically when selecting a parent folder.
•	Already downloaded items can still be manually selected if the user wants to re-download them.
•	Parent folder selection updates automatically based on child states.
•	If all children are selected, the parent becomes fully selected.
•	If some children are selected, the parent becomes partially selected.
•	If all children are unselected, the parent becomes unselected.
•	Queue-based download workflow:
•	Add to queue adds selected file-tree items to the queue,
•	selected file-tree checkboxes are cleared after queueing,
•	Remove from queue removes checked queue items,
•	queue items have their own selection checkbox.
•	Download controls:
•	Start begins queue processing,
•	Pause temporarily suspends active rclone processes,
•	Resume continues suspended transfers,
•	Stop terminates active transfers and clears the queue.
•	Adjustable simultaneous transfer count with a Transfers 1–5 slider.
•	Default transfer count is 3.
•	Transfer-count changes are applied dynamically:
•	increasing starts additional queued jobs,
•	decreasing does not interrupt active transfers; it simply prevents new ones from starting until the active count drops.
•	Queue and file-tree color/status system:
•	blue: queued,
•	yellow: active or partially completed/in progress,
•	green: downloaded successfully,
•	red: error,
•	orange: local .partial file exists,
•	neutral: not queued and not downloaded.
•	Folder colors are aggregated from children:
•	green only when all known children are downloaded,
•	yellow when anything inside is active or partially completed,
•	blue when all known children are queued,
•	red when any child has an error,
•	neutral when only some children are queued or no relevant status applies.
•	Destination folder picker for choosing the local download directory.
•	Detects already downloaded local files during Load data and colors the file tree accordingly.
•	Preserves the original folder hierarchy in the destination directory.
•	Creates selected empty folders locally.
•	Downloads individual files with temporary .partial suffix first.
•	Finalizes successful file downloads by renaming filename.ext.partial to filename.ext.
•	Interrupted or unfinished downloads remain as .partial files.
•	Retry logic preserves existing .partial files and attempts to continue/retry without deleting them.
•	Conflict handling before downloads:
•	replace existing files/folders,
•	skip existing conflicts,
•	keep both by renaming old local items with _old.
•	Live Download progress table with status, transferred size, total size, percent, speed, ETA, elapsed time, and file count.
•	Displays total current speed.
•	Displays average speed.
•	Displays queue ETA based on known remaining size and recent average speed.
•	Dedicated transfer error panel with timestamps.
•	Retry button activates only after a real download error occurs.
•	Retry files with errors removes failed final outputs when appropriate and re-adds failed items to the queue.
•	Download log records meaningful events only:
•	item queued,
•	transfer started,
•	file completed,
•	item completed,
•	error occurred,
•	pause/resume/stop,
•	final queue summary.
•	Progress chunks and ordinary Transferred: stats are not treated as errors.
•	Completion log reports whether all queued downloads finished with or without errors.
•	Close-window confirmation is always shown.
•	If downloads are running, the close dialog includes a prominent warning.
•	UI uses loading cursor and temporarily disables buttons while waiting for file-tree or server responses.
•	Buttons remain usable during downloads except when a blocking tree/server operation is running.
•	Includes macOS helper scripts for setup and .command permissions.
•	Setup script can check/install Homebrew, Python, Tkinter support, and rclone.
•	Project files avoid embedding private OAuth tokens, refresh tokens, tenant IDs, personal paths, or hardcoded user data.
What changed from v0.1.6 to v0.2.9
Selection and file-tree behavior
• Added/expanded three-state selection logic for file-tree items:
• empty,
• full,
• partial.
• Folder selection now propagates to children more intelligently.
• Newly loaded children inherit selection only when appropriate.
• Parent folder state is recalculated from child states.
• If all children are selected, the parent becomes fully selected.
• If only some children are selected, the parent becomes partial.
• If all children are unselected, the parent becomes empty.
• Fixed the case where manually unselecting all children did not fully unselect the parent.
• Removed visible stale Loading... placeholder rows from the tree.
• Improved Expand all subfolders so expansion requests are queued sequentially instead of firing inconsistently.
• Added final log message after the full tree has been mapped and expanded.
• Empty folders no longer show an unnecessary expand marker.
• Selecting a parent folder no longer automatically selects green, already-downloaded children.
• When some children are already downloaded and skipped during selection, the parent becomes partial instead of full.
• Green items can still be manually selected for re-download.
• Fixed the issue where clicking the selection box on a folder could still select green subfolders.

Queue model and download controls
• Replaced direct Download behavior with a queue-based workflow.
• Added Add to queue.
• Added Remove from queue.
• Added selectable queue rows in Download progress.
• Added Start, Pause/Resume, and Stop controls.
• Stop now terminates active transfers, clears the queue, and logs that the operation was stopped.
• Pause now changes to Resume.
• Pause temporarily suspends active rclone processes rather than clearing the queue.
• Resume continues suspended transfers.
• Active downloads are no longer stopped just because transfer count is reduced.
• Transfer slider now controls how many jobs may run concurrently.
• Default transfer count set to 3.
• Transfer slider supports 1–5 simultaneous jobs.
• Increasing transfers can start additional queued jobs.
• Decreasing transfers prevents new jobs from starting until active transfers naturally drop to the new limit.

Progress and speed display
• Improved Download progress so transfer stats are shown there instead of in the error panel.
• Fixed incorrect Total = 1 B display caused by misinterpreting rclone counters.
• Percent display now includes downloaded size and total size, for example:
42.1% (123.4 MB / 293.0 MB).
• Added total current speed.
• Renamed “10-min average speed” to Average speed.
• Added queue ETA based on recent average speed and known remaining queue size.
• Added per-item status in the progress table.
• Added elapsed time and file-count information where available.
• Reduced noisy log spam from repeated transfer chunks.

Logging and error handling
• Ordinary rclone progress lines such as Transferred:, Elapsed time, and Errors: 0 are no longer treated as errors.
• Error panel now focuses on actual transfer errors.
• Download log now records high-level events rather than every progress chunk.
• After all queue work finishes, the log reports whether downloads completed with or without errors.
• For completed files, the log now includes file size and duration.
• Retry button now stays disabled until a real download error appears.
• Retry files with errors now re-adds failed items to the queue instead of behaving like a general action.
• Error status is reflected in both the file tree and download progress table.
• File/tree status updates are logged where relevant.

Status colors
• Added or refined status colors across file tree and queue:
• blue for queued,
• yellow for active/partial/in-progress,
• green for completed,
• red for error,
• orange for .partial,
• neutral for no active status.
• Folder coloring now depends on children, not just local folder existence.
• A folder becomes green only when all known children are downloaded.
• If only some children are downloaded, the folder becomes yellow, not green.
• If all known children are queued, the folder becomes blue.
• If only some children are queued, the folder remains neutral/partial rather than fully blue.
• If any child has an error, the folder reflects error state.
• File tree is analyzed during Load data against the destination path to mark already downloaded items.

Partial downloads
• Removed unsupported rclone --partial flag after it caused unknown flag: --partial.
• Implemented application-level .partial handling.
• Individual files now download first to filename.ext.partial.
• After successful completion, .partial is renamed to the final filename.
• Interrupted downloads leave .partial files in place.
• Existing .partial files are marked orange in the file tree.
• Retry no longer deletes existing .partial files by default.
• Retry attempts to continue/retry using the existing partial output where possible.

Add-from-link and deep-path handling
• Improved Add from link for own-drive links.
• If the linked item is already loaded in memory, the app expands the path and selects it.
• If the linked item is deeper than the loaded tree, the app requests additional parent/child levels as needed.
• Added matching improvements using path and OneDrive/rclone item identifiers where possible.
• Public/shared links still require manual “Add shortcut to My files” when they cannot be directly resolved by rclone/Graph.

Conflict handling and local filesystem behavior
• Added conflict detection before downloading.
• Added user choices for conflicts:
• replace old with new,
• keep old and skip,
• keep both by renaming old local item with _old.
• Selected empty folders are created locally.
• Existing downloaded items are detected on load and colored accordingly.
• Folder hierarchy is preserved under the chosen destination.

UI behavior
• Loading cursor appears while waiting for rclone/Graph/tree responses.
• Buttons are blocked only during tree/server response operations, not during normal downloading.
• Close-window behavior asks for confirmation.
• If downloads are running, the close warning is more explicit.
• Renamed status terminology so the UI uses Status, not “X color”.
• Removed unnecessary config-file workflow and related UI from earlier versions.

Setup and packaging
• Added/merged macOS setup scripts.
• Setup script checks for Homebrew, Python, Tkinter support, and rclone.
• Setup script sets executable permissions for the .command launcher.
• Setup script removes macOS quarantine flag when needed.
• Project no longer requires config.ini for fixed constants.
• Added safer project hygiene by avoiding embedded private data such as user paths, Client IDs, tenant IDs, and OAuth tokens.





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
