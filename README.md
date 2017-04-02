# jenkins-github-webhook-build-trigger-plugin

Trigger Jenkins Jobs via GitHub Webhooks and provide Webhook Payload Information as Environment Variables inside your Job.

-----

&nbsp;

### Is this for me?

If you can agree with all statements, then this is for you.

 * [Bash](http://tiswww.case.edu/php/chet/bash/bashtop.html) is love. Bash is live.
 * Using the [git commandline client](https://git-scm.com/book/en/v2/Getting-Started-The-Command-Line) in Jenkins Jobs:
   * gives me the control I want and need.
   * replaces all other mostly defunct and/or bloated Git Plugins.
 * I only use GitHub.com for my repositories.
 * I only 'git clone' via `https://` URLs.
 * I work with git branches and tags.
 * I want my Jenkins jobs triggered at every push (branch or tag).
 * I want my Jenkins jobs triggered exclusively via GitHub Webhook Push Events.
 * I use Linux to run Jenkins.
 * I want my Jenkins jobs triggered automatically by convention of configuration job naming.

Ok. Still here?! Then this might be for you :bowtie:

-----

&nbsp;

### How it works in three sentences and one picture

  * Plugin REST Endpoint parses the actual GitHub Webhook JSON Payload and extracts its information.
  * It then triggers all Jenkins jobs matching `{repositoryOwner}---{repositoryName}.*`
  * Lastly it injects Environment Variables into the job run for you to determine what branch and revision is to clone.

![](https://codeclou.github.io/jenkins-github-webhook-build-trigger-plugin/img/webhook-payload---with-overlays.png?v2)

-----

&nbsp;

### GitHub Webhook Configuration

This is how you need to configure the GitHub Webhook in your repository 'Settings'.

 * **Payload URL**
   * `https://jenkins/github-webhook-build-trigger/receive`
   * Note: 
     * The endpoint can be called without authentication.
     * When using matrix-based security 'Anonymous' needs 'Job' → `build,discover,read` permissions.
 * **Content type**
   * `application/json`
 * **Secret**
   * Choose a good secret, at best a random sha512 hash. Use that secret for all webhooks of all your repositories.
 * **Which events ...**
   * Just the `push` event

<p align="center"><img src="https://codeclou.github.io/jenkins-github-webhook-build-trigger-plugin/img/github-webhook-settings---with-overlays.png?v4" width="80%"></p>


### Jenkins Job Configuration


<p align="center"><img src="https://codeclou.github.io/jenkins-github-webhook-build-trigger-plugin/img/jenkins-source-code-management.png" width="80%"></p>

<p align="center"><img src="https://codeclou.github.io/jenkins-github-webhook-build-trigger-plugin/img/jenkins-build-trigger.png" width="80%"></p>
	

### Example Trigger

We can see in the Response Tab of a GitHub Webhook Delivery that we notify the Git-Plugin with a specific sha1 commit id.

<p align="center"><img src="https://codeclou.github.io/jenkins-github-webhook-build-trigger-plugin/img/webhook-specific-commit-id.png" width="80%"></p>

And that triggers this exact id for the exact branch/tag that has been sent by the Webhook as JSON payload.

<p align="center"><img src="https://codeclou.github.io/jenkins-github-webhook-build-trigger-plugin/img/jenkins-build-by-commit-id.png" width="80%"></p>




-----

&nbsp;

### Appendix

**Build Plugin**

Jave Oracle Java 8 and Apache Maven 3 installed. And then build like this:

```bash
git clone https://github.com/codeclou/jenkins-github-webhook-build-trigger-plugin.git
cd jenkins-github-webhook-build-trigger-plugin
mvn clean
mvn compile
mvn hpi:hpi
```

Now you should have a file called `./target/github-webhook-notifier-plugin.hpi` which
you can upload manually to Jenkins under 'Manage Plugins' → 'Advanced' → 'Upload Plugin'.


&nbsp;

&nbsp;

**What's the story behind it?**

I needed something that forcefully triggers my Jenkins Jobs by passing the actual git revision and branch or tag information.

The default behaviour of existing plugins is to receive the GitHub Webhook Payload, but 
only using the `after` commit id and "deciding if it needs to rebuild the job". 

Example: You are on your `master` Branch and you create a tag of off the `master` branch
and called `1.0.0`. When pushing `1.0.0` tag, the jenkins job will not trigger an actual build.
What happens? It will do some strange `git fetch` requests and comes to the result, that the revision
was already built with the previous push done by `master` branch. And partly he is right. 
Until further commits happen, the `master` branch has the same revision as the `1.0.0` tag.
But **I want tag pushes to trigger a build anyway**. And since I hate 'API-wrappers' of stuff,
I decided to create a single purpose tool that just passes the information of the webhook payload
through to the job. And it is the jobs logic that can now decide what to do.


&nbsp;


-----

&nbsp;

### License

[MIT](./LICENSE) © [Bernhard Grünewaldt](https://github.com/clouless)