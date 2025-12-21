## Setup Instructions
### Prerequisites

Before proceeding, ensure that you're already familiar with how [foss.crave.io](https://foss.crave.io) works and the original [workflow](https://github.com/sounddrill31/crave_aosp_builder).

### Steps

1. **Fork this repository.**

2. **Configure Repository Secrets:**
   - Go to `Settings -> Security -> Secrets and Variables -> Actions`.
   - Get your Crave's username and authentication token from `crave.conf` on foss.crave.io.
   - Create the following Repository Secrets:
     - `CRAVE_USERNAME`: Your crave username from `crave.conf`.
     - `CRAVE_TOKEN`: Your authentication token from `crave.conf`.

3. **Set Workflow Permissions:**
   - Go to `Settings -> Code and Automation -> Actions -> General`.
   - Set "Workflow Permissions" to **Read and Write Permissions** and save.
     - If this option is greyed out and you're working within a GitHub Organization:
       - Navigate to `Organization settings -> Code, planning, and automation -> Actions -> General -> Workflow permissions` and set it to **Read and Write Permissions**.

## Setting Up the Runner (If Not Already Done)

1. **Access the Runner Setup:**
   - In the repository's top menu bar, click on `Actions`.
   - Select `Self-Hosted Runner -> New Runner`.
   - Scroll down and copy the Runner Token.

2. **Create a Self-Hosted Runner:**
   - Go back to `Actions`, and select "Create Self-Hosted Runner".
   - Run the workflow and enter your Runner Token.
     - Ensure there are no extra spaces before or after the token.

3. **Start the Workflow:**
   - If the runner is still offline, trigger the "Start/Restart Self-Hosted Runner" workflow to force-restart the runner.

## Starting a Build

1. **Run the Workflow:**
   - In the repository's top menu bar, click on `Actions`.
   - On the left side, select "Build in foss.crave.io".
   - On the right side, select "Run workflow".
     - Choose the base project.
     - If your ROM isn't listed, just `repo init` the ROM manifest within your build script (refer to existing [scripts](https://github.com/aosp-realm/android_build_scripts)).
     - Enter your device codename and the build script to use.
     
> [!CAUTION]
> **DO NOT** use the standard URL for build script. You **MUST** use the Raw URL, otherwise the build will fail because it will download an HTML instead of your build commands.

2. **Execute the Workflow:**
   - Run the workflow.

## Setting Up Signing Keys (Optional)

If you wish to build a signed ROM, you must provide your keys in a specific format to bypass GitHub's secret size limits and ensure the workflow stays automated.

1. **Prepare your keys locally:**
   - Navigate to your signing keys folder on your Linux machine and run:
     ```bash
     cd path/to/your/keys
     tar -czpf keys.tar.gz *
     base64 -w0 keys.tar.gz > keys.txt
     ```

2. **Host the keys:**
   - Create a Secret Gist on GitHub ([gist.github.com](https://gist.github.com)).
   - Paste the content of `keys.txt` into the Gist and save.
   - Click the "Raw" button on the top right of the file view.
   - Copy the resulting URL. It must look like: `https://gist.githubusercontent.com/.../raw/...`

3. **Add the Secret:**
   - In your repository, go to `Settings -> Secrets and Variables -> Actions`.
   - Create a new secret named `KEYS_BASE64`.
   - Paste the Raw Gist URL as the value.

> [!CAUTION]
> **DO NOT** use the standard Gist UI URL. You **MUST** use the Raw URL, otherwise the build will fail because the runner will download HTML instead of the base64 of your keys.

## Important Informations

- **Crave pull in devspace:** `crave_pull.yml` What is this for? Occasionally, the GitHub runner's connection to devspace fails due to heavy load. This workflow is used after a successful build to automatically pull the compiled objects into your devspace.

- **GitHub Release:** `github_release_upload.yml` This is executed by default if your github action stay alive until your build finished or else execute it manually

- **Gdrive Upload:** `gdrive_upload.yml` This workflows requires a setup that is out of the scope of this repository, refer to: https://github.com/adityak74/google-drive-upload-git-action

- **Dirty Builds:** If you wish to perform a dirty build after a successful one, **DO NOT CHANGE THE BASE PROJECT**! Ensure you're using the same project where the initial successful build is.
  
- **Release Limits:** Due to GitHub's 2GB per file limit on releases, if your compiled ROM zip is below 2GB, it will be uploaded to this repository's release page. If it exceeds 2GB, it will remain in your devspace for you to upload elsewhere. (Go to foss.crave.io to log in to your devspace)

- **Compiled Objects:** **DO NOT START ANOTHER BUILD WITHOUT SECURING THE COMPILED OBJECTS FROM THE PREVIOUS BUILD**! (Upload or move it out of the build directory) Compiled objects will be pulled to devspace but deleted upon starting another build to avoid clogging devspace storage.
  - Check the `Display files information` of the workflow for these files
  
## Must read
https://fosson.top/crave/
