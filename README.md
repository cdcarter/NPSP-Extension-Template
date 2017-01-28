# NPSP Extention Template
Want to extend the functionality of the [Nonprofit Success Pack](https://github.com/SalesforceFoundation/Cumulus)?  This template provides a basic project skeleton so you can hit the ground running.  The main goal is to get you up and running with [CumulusCI](https://github.com/SalesforceFoundation/CumulusCI), the  process built and used by the Salesforce.org's development team to manage the development and release process for the Nonprofit Success Pack.

Our hope is that by making it easier to share your contribution to the Nonprofit Success Pack community, you can spend your time focusing on the business logic that adds value to the community.  This template should make it easy to automate much of the time consuming part of building and releasing an managed package which extends the Nonprofit Success Pack.

# Quick Start
This section assumes you are somewhat familiar working with metadata in Salesforce from your local computer and have some basic familiarity with Git and GitHub.  The examples below show git command line commands but you can accomplish the same functionality through most GUI git tools as well.

If you need a primer on the command line and git, check out:
* [Learn Enough Command Line to be Dangerous](https://www.learnenough.com/command-line-tutorial)
* [Learn Enough Git to be Dangerous](https://www.learnenough.com/git-tutorial)


## Step 1: Setup
The first step is to set up version control and test the build scripts against a fresh Developer Edition org.  This section should take about 30 minutes to complete.

* Create a Repository for your project in GitHub
* Clone the GitHub repository to your local computer
* Unzip the NPSP-Extension-Template.zip file into your cloned local repository
* Edit cumulusci.yml to set the Project Name, Package Name, and namespace (if known)
* [Create a Developer Edition](https://developer.salesforce.com/signup) org to use as your personal dev org for the project
    * Log into your Developer Edition org.
    * Make sure you have Chatter and Chatter Publisher Actions enabled in the org
* Now, we need to set up our CumulusCI Org Connection:
	* run `cci org connect dev` to OAuth to your Developer Edition. This will encrypt an access and refresh token, and not store your password.
	* `cci` will fail if you are not in a local git repository.
* Now, run the build: `cci flow run dev_org --org dev` and watch as your new org is set up for development
* When the build completes, check the Installed Packages in the org
* Go to Setup -> Create -> Packages and you should see your package, ready for development
* If all goes well, add all the files, commit them, and push to GitHub:

    git add .gitignore
    git add --all
    git commit
    git push

At this point, anyone can check out your repository, create an org, and set up a free development environment to contribute to your package!

## Step 2: Develop
Now that we have our development environment ready, it's time to start writing the great new idea that inspired you to go through that setup in the first place :)

If you develop locally:

* Create a new branch in your local git repository.  The name should start with `feature/` and you can put whatever you want after that (i.e. `feature/aura-controller`).  The CumulusCI process isolates all your changes into branches.  It's easy to do and invaluable to helping multiple people work on the same project or a single person to work on multiple features without them interfering with each other.
    * `git checkout -b feature/aura-controller`
* Develop your changes to the metadata under the src directory, using your tool of choice.
* If you added any metadata, you should run `cci task run update_package_xml` to regenerate `src/package.xml`
* Run `cci flow run ci_feature` again to run the entire build against your Developer Edition org to ensure all your tests are passing
* Commit and push your changes to GitHub
* Submit a Pull Request to merge your branch into master when the feature is ready
* Merge the Pull Request
* Repeat...

If you develop primarily in the Developer Console or another remote IDE:
* Create a new branch in your local git repository.  The name should start with `feature/` and you can put whatever you want after that (i.e. `feature/aura-controller`).  The CumulusCI process isolates all your changes into branches.  It's easy to do and invaluable to helping multiple people work on the same project or a single person to work on multiple features without them interfering with each other.
    * `git checkout -b feature/aura-controller`
* Develop your changes in the Developer Console.
* Ensure *any* new metadata components you create get added to the unmanaged package that CumulusCI automatically created in your Dev Edition.
* Run `cci task run retrieve_src` to pull the changes to your local environment.
* Run `cci flow run ci_feature` again to run the entire build against your Developer Edition org to ensure all your tests are passing
* Commit and push your changes to GitHub
* Submit a Pull Request to merge your branch into master when the feature is ready
* Merge the Pull Request
* Repeat...

## Step 3: Package
After you've merged your first feature branch, it's time to think about building a beta managed package.  This will allow you to ensure that your metadata will work in a managed package and since it's a beta there is no risk of locking you into anything like a release version would.

This should take about 30 minutes to get your first beta managed package

* Create a new Developer Edition org to serve as your packaging org
	* Configure the new org by running `cci org connect packaging`
* Create the empty package to manage: `cci task run create_package --org packaging`
* Log into the packaging org and go to Setup -> Create -> Packages.  Edit the configuration to assign the org a namespace and then assign your package as the managed package.
* Edit `cumulusci.yml` and ensure that `project__package__namespace` is correct
* Run `cci flow run ci_master --org packaging`

Now let's quickly configure Github.

* First, generate a Personal Access Token with the repo scope. Don't close the window once it's created!
* Run `cci project connect_github` to configure your Github account. Instead of entering your github password, use the person access token you created as your password.

This step requires the managed package api!
* Run `cci flow run release_beta --org packaging` to upload a beta and create a release on Github.

If you don't have that API yet:
* Upload your beta managed package manually through the api w/ version number 1.0
* Run `cci task run github_release -o version 1.0 --org packaging` to create a github release for the current version.
* Run `cci task run github_release_notes -o publish True -o tag release/1.0 --org dev` to update the release notes for the beta.

* Create a new Developer Edition org to test beta packages

## Step 4: Keep going!

### Automate

### Extend