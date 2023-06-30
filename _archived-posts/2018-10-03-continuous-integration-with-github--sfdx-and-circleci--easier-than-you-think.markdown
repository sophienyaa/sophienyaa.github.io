---
layout: post
title: Continuous Integration with GitHub, SFDX and CircleCI… Easier than you think!
date: 2018-10-03

---
This post is a follow up/companion to the [talk](https://success.salesforce.com/sessions?eventId=a1Q3A00001XoCSUUA3#/session/a2q3A000001WWXdQAO) I did at [Dreamforce 2018](https://www.salesforce.com/dreamforce/). If you didn’t get to see it in person, you can check out the slides [here](https://drive.google.com/file/d/1H3nmdgw1Jjzz1YqPakkPakWPuECv9zKW/view), and I will update this post when the recording becomes available, but for now, read on.

In the salesforce ecosystem, the traditional way of moving code from development environments (sandboxes, etc) to production has been either change sets, or the force.com migration tool (ANT). Neither method is perfect, but until recently they were all we had. Change sets are an easy, but time intensive process. They can be created in the user interface and are well within the reach of most people. The force.com migration tool was arguable more powerful (CLI based tool, able to be scripted, etc) but a lot more difficult to use. Neither tool was particularly well suited to an agile environment that required continuous integration or delivery.

So what do I mean when I say continuous integration? I am referring to both the development practice and the tooling required to facilitate it.

A good explanation of CI, taken from Microsoft’s Azure [docs](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-continuous-integration) is as follows: “Continuous Integration (CI) is the process of automating the build and testing of code every time a team member commits changes to version control. CI encourages developers to share their code and unit tests by merging their changes into a shared version control repository after every small task completion. Committing code triggers an automated build system to grab the latest code from the shared repository and to build, test, and validate the full master branch (also known as the trunk or main).”

This has traditionally been difficult with salesforce, because we lacked the tooling to do it effectively. In the old world, salesforce development took an ‘org-centric’ view of the world, with your production org serving as the ‘source of truth’ and sandboxes containing work in progress. This ‘org-centric’ model has a number of problems (e.g prod can be changed by anyone, developing dependent features in separate environments, merge conflicts, etc).

Since the advent of Salesforce DX (SFDX) we have been handed the tools to move towards a ‘source-centric’ world, with our source/version control system (e.g Git) becoming the source of truth, and our scratch org’s essentially becoming ‘runtimes’ rather than org’s in their own right. Scratch orgs are ephemeral things that can be created and destroyed at will, only needing to live as long as the development cycle for whatever feature you are working on, or as long as it takes for code to be pushed to them and tested (if used in a CI pipeline)

Because of SFDX, we now get access to all of the power of modern source/version control systems, such as Git. Git providers like GitHub give us powerful user interfaces, the ability to perform code reviews (pull requests) with ease, the ability to track every single change with information like who changed the file, what was changed and when. As code is versioned we also now gain the ability to revert to previous versions of the source making it much easier to recover if something goes wrong. Alongside this, we know that our code is stored safely outside of salesforce and we can set up access control to prevent code being overwritten by unauthorised users.

Another huge advantage we gain from source/version control is the ability to create branches of our source code. For example; you may have your ‘production’ ready application in the 'master' branch, with the version you are currently working on in the 'develop' branch (think of your UAT/pre-prod environment). Whenever you work on a new feature, you can make a copy of your 'master' branch in to its own new branch (e.g 'feature/new-feature') and do the work there. Once you are happy with it, a pull request can be made to merge it in to develop for testing. Once this has been completed and all of the code in 'develop' is ready for release, this can then be merged in to 'master' for your release.

Source/version control is only half of the equation. It is all well and good to have your code in git, and this itself is valuable, but the real power comes from automation and continuous integration. When we have CI setup, every time we make a commit to our feature branch ('feature/new-feature') we are pulling it from git, pushing it to a scratch org and running all of our tests. This lets us know very quickly if a) our code even deploys and b) if we've broken any tests. We also use a scratch org 'locally' for running and testing our code on our local machine (of-course the scratch org actually runs in the cloud) in a similar way we would have used a developer sandbox in the past.

Once our code is ready for the next environment (e.g UAT) we can have our CI setup automatically push our code to our UAT sandbox whenever a commit is made to develop, finally, once we are happy and ready to move to our production org, we can then have our CI deploy to production upon a merge into the 'master' branch.

![](/img/0*Ox-9PCPMD24Uquas.png)_Example of CI Development Process_  
  
So let's talk about how we actually achieve this with salesforce. In this example I will be using [GitHub](https://github.com/) as my source/version control system (other alternatives are [BitBucket](https://bitbucket.org/), [GitLab](https://about.gitlab.com/)) and [CircleCI](https://circleci.com/) as my CI automation tool (other alternatives are [TravisCI](https://travis-ci.org/), [Jenkins](https://jenkins.io/)). The ‘glue’ that ties all of this together is [SFDX](https://developer.salesforce.com/platform/dx).

I’ve created a GitHub repository with everything you need to get started, I would suggest you clone it from [here](https://github.com/mickwheelz/Dreamforce2018) as instructions below reference scripts from it. If you want to find out what the scripts are doing, simply open them in a text editor. These instructions require a *nix like environment (e.g macOS, Linux, Bash on windows) and have been tested on macOS only.

**NOTE:** The first time this build runs it will deploy whatever is in your force-app/ folder to production. As with anything you do, be sure to try this in a developer edition org, or a sandbox first before using this with your production org!

In this example there is only a simple ‘Hello World’ apex class, test and lightning component. You should remove these before you begin and replace with your own source code. Salesforce provide steps here on how to migrate your existing code to SFDX here, After you’ve cloned the repository to your machine you should follow the instructions [here](https://developer.salesforce.com/blogs/developer-relations/2017/07/migrating-existing-projects-salesforce-dx.html) (inside the folder you clone to).

Now that we have our code ready to use with CI, let's get going.

We will tackle authentication first. To authenticate to our production org and to create scratch orgs we are using [JWT](https://help.salesforce.com/articleView?id=remoteaccess_oauth_jwt_flow.htm&type=5) to do this.

1. You first need to create a certificate and key to authenticate with. To do this you can run the script in build/generate-keys.sh

* Follow the prompts when creating the certificate files
* Take note of the Base64 output (big long chunk of text), as you will need this to set up CircleCI later
  ![](/img/0*4jJrhsB_e-qOGQlT.png)Output from the key generation script1. You will need to create a connected app in your production (and any sandboxes you wish to use CI with)
* First, from Setup, enter App in the Quick Find box, then select App Manager. Click New Connected App.
* Give your application a name such as ‘CircleCI’
* Make sure you check Enable OAuth Settings in the connected app
* Set the OAuth callback to [http://localhost:1717/OauthRedirect](http://localhost:1717/OauthRedirect)
* Check Use Digital Signatures and add your certificate file (server.crt), this will be in the build/ folder. Once you have done this delete this file
* Select the required OAuth scopes
* Make sure that refresh is enabled - otherwise you'll get this error: user hasn't approved this consumer
* Ensure that Admin approved users are pre-authorized under Permitted Users is selected
* Ensure that you allow the System Administrator profile is selected under the Profiles related list
* Take note of the Consumer Key as you will need it for to setup CircleCI
  ![](/img/0*1SYY0AQ40gjZQEmS.png)Connected App SettingsNow that we have authentication setup, we can configure CircleCI. I have provided a basic config.yml file, this is already within the .circleci/ directory along with some shell scripts for circle to use for deployment and validation. Circle has extensive documentation on these config files [here](https://circleci.com/docs/2.0/config-intro/#section=configuration) and stay tuned for a future post covering this area in more detail.

1. You now can set up your CircleCI build

* Ensure you have connected your GitHub account to CircleCI, to do this go [https://www.circleci.com,](https://www.circleci.com,) click 'Signup' and then'Signup with GitHub'
  ![](/img/0*XugeXJBuqX_VqffW.png)Adding a project in CircleCI* Once logged in, click on Add Projects choose your GitHub to use repository and click Set Up Project then click Start building there is an example config.yml in this repository already. You can edit this to suit your needs.
* Cancel the first build, as it will fail without any environment variables set
* Click the gear icon next to the repository name on the left hand side of the screen
* In the settings screen, choose Environment Variables you will need to add three variables by clicking Add Variable
* SFDC_SERVER_KEY is the Base64 output generated in Step 1
* SFDC_PROD_CLIENTID is the Consumer Key from Step 2
* SFDC_PROD_USER is the username to use with CircleCI (This should be an Integration user, with the System Administrator profile)
  ![](/img/0*wx8p8Nhos4lAYZiS.png)Setting Environment Variables in CircleCI* You can now re-run the first build.
  Once you have all of this configured and working, you can use the CI build process from here on our, and hopefully never have to worry about a damn change-set again!

Now, every time you push to any branch other than 'master' a scratch org will be created, your code deployed to it and all tests run. If you then merge in to 'master' a production build will be run, validating and deploying your code.

Remember, this is just an introduction. In future posts I will explain in further detail the scripts and config files used in this repository, so you can customise them to suit your exact requirements.